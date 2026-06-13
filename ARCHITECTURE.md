# Architecture Patterns & Design Decisions

This document explains the architectural patterns used across projects, why they exist, and when to use them.

> **Important**: These patterns are **framework-agnostic where it matters most**. Services, repositories, DTOs, models, and business rules should transfer across Fiber, Gin, Echo, Chi, or `net/http`. The HTTP adapter layer still changes in real ways, especially because Fiber is `fasthttp`-based while Gin and Chi sit closer to the standard `net/http` ecosystem.

## 🏗 Project Structure Philosophy

### The Standard Structure

All projects follow this structure for consistency:

```
project-name/
├── cmd/                # Application entry points
│   └── main.go        # Main executable
├── internal/          # Private application code (not importable)
│   ├── handlers/      # HTTP layer
│   ├── services/      # Business logic layer
│   ├── repository/    # Data access layer
│   ├── models/        # Database entities
│   ├── dto/           # Data transfer objects
│   ├── middleware/    # HTTP middleware
│   └── validators/    # Input validation
├── pkg/               # Public packages (importable)
│   ├── config/        # Configuration
│   ├── database/      # DB setup
│   └── logger/        # Logging
├── migrations/        # Database migrations
└── tests/            # Integration tests
```

### Why This Structure?

**internal/ vs pkg/**

- `internal/`: Go convention for private code (can't be imported by other projects)
- `pkg/`: Public, reusable code (can be imported)
- This prevents accidental tight coupling

**Layered Architecture**

```
HTTP Request → Handlers → Services → Repository → Database
                    ↓
                  DTOs
```

**Benefits:**

- **Separation of concerns**: Each layer has one job
- **Testability**: Can test business logic without HTTP
- **Flexibility**: Can swap HTTP framework (Fiber → Gin) without changing business logic
- **Maintainability**: Know where to find things
- **Framework-agnostic**: 90% of code works with any framework

## 📊 Core Patterns Explained

### 1. Handler (Controller) Pattern

**What it is:** HTTP request/response handling layer

**Responsibilities:**

- Parse request (body, params, query)
- Validate input (basic validation)
- Call service layer
- Format response
- Handle HTTP status codes

**What it should NOT do:**

- Business logic
- Direct database access
- Complex validation rules

**Framework note:** This is the layer that changes the most between Fiber/Gin/Echo. Keep framework-specific context, binding, status-code, and middleware behavior here.

**Example (Fiber):**

```go
type UserHandler struct {
    userService services.UserService
}

func (h *UserHandler) Register(c fiber.Ctx) error {
    // 1. Parse request
    var req dto.RegisterRequest
    if err := c.Bind().Body(&req); err != nil {
        return c.Status(400).JSON(fiber.Map{"error": "invalid request"})
    }

    // 2. Validate (basic)
    if err := validator.Validate(req); err != nil {
        return c.Status(400).JSON(fiber.Map{"error": err.Error()})
    }

    // 3. Call service (business logic)
    user, err := h.userService.Register(c.Context(), req)
    if err != nil {
        // Handle different error types
        if errors.Is(err, services.ErrUserExists) {
            return c.Status(409).JSON(fiber.Map{"error": "user already exists"})
        }
        return c.Status(500).JSON(fiber.Map{"error": "internal error"})
    }

    // 4. Format response
    return c.Status(201).JSON(dto.UserResponse{
        ID:    user.ID,
        Email: user.Email,
    })
}
```

**Same handler in Gin (notice minimal changes):**

```go
func (h *UserHandler) Register(c *gin.Context) {
    var req dto.RegisterRequest
    if err := c.ShouldBindJSON(&req); err != nil {
        c.JSON(400, gin.H{"error": "invalid request"})
        return
    }

    user, err := h.userService.Register(c.Request.Context(), req)
    if err != nil {
        if errors.Is(err, services.ErrUserExists) {
            c.JSON(409, gin.H{"error": "user already exists"})
            return
        }
        c.JSON(500, gin.H{"error": "internal error"})
        return
    }

    c.JSON(201, dto.UserResponse{
        ID:    user.ID,
        Email: user.Email,
    })
}
```

**Key principle:** Keep handlers thin - they're just HTTP adapters. The same service layer works for both!

---

### 2. Service Pattern

**What it is:** Business logic layer (should be framework-agnostic)

**Responsibilities:**

- Implement business rules
- Coordinate between repositories
- Handle transactions
- Complex validation
- Business-level error handling

**What it should NOT do:**

- HTTP-specific logic (status codes, headers)
- Direct SQL queries (use repositories)

**Important:** This layer should not depend on Fiber, Gin, Echo, or pure `net/http`.

**Example:**

```go
type UserService interface {
    Register(ctx context.Context, req dto.RegisterRequest) (*models.User, error)
    Login(ctx context.Context, req dto.LoginRequest) (*dto.AuthResponse, error)
}

type userService struct {
    userRepo   repository.UserRepository
    tokenRepo  repository.TokenRepository
    emailService EmailService
}

func (s *userService) Register(ctx context.Context, req dto.RegisterRequest) (*models.User, error) {
    // 1. Business validation
    if exists, _ := s.userRepo.ExistsByEmail(ctx, req.Email); exists {
        return nil, ErrUserExists
    }

    // 2. Business logic
    hashedPassword, err := bcrypt.GenerateFromPassword([]byte(req.Password), bcrypt.DefaultCost)
    if err != nil {
        return nil, fmt.Errorf("failed to hash password: %w", err)
    }

    user := &models.User{
        Email:    req.Email,
        Password: string(hashedPassword),
        Role:     "user", // Default role
    }

    // 3. Data persistence
    if err := s.userRepo.Create(ctx, user); err != nil {
        return nil, fmt.Errorf("failed to create user: %w", err)
    }

    // 4. Side effects (async)
    go s.emailService.SendWelcomeEmail(user.Email) // Don't wait

    return user, nil
}
```

**Key principle:** Business logic lives here, not in handlers or repositories. This code works with ANY framework!

---

### 3. Repository Pattern

**What it is:** Data access abstraction layer

**Responsibilities:**

- Database operations (CRUD)
- Query building
- Transaction handling
- Data mapping

**What it should NOT do:**

- Business logic
- Validation
- HTTP handling

**Why use it:**

- Can swap databases (PostgreSQL → MySQL → MongoDB)
- Easy to mock for testing
- Keeps SQL/queries in one place

**Example:**

```go
type UserRepository interface {
    Create(ctx context.Context, user *models.User) error
    FindByID(ctx context.Context, id uint) (*models.User, error)
    FindByEmail(ctx context.Context, email string) (*models.User, error)
    ExistsByEmail(ctx context.Context, email string) (bool, error)
    Update(ctx context.Context, user *models.User) error
    Delete(ctx context.Context, id uint) error
}

type userRepository struct {
    db *gorm.DB
}

func (r *userRepository) FindByEmail(ctx context.Context, email string) (*models.User, error) {
    var user models.User
    err := r.db.WithContext(ctx).Where("email = ?", email).First(&user).Error
    if err != nil {
        if errors.Is(err, gorm.ErrRecordNotFound) {
            return nil, ErrUserNotFound
        }
        return nil, err
    }
    return &user, nil
}

func (r *userRepository) Create(ctx context.Context, user *models.User) error {
    return r.db.WithContext(ctx).Create(user).Error
}
```

**Key principle:** Repository is the ONLY place that knows about the database.

---

### 4. DTO (Data Transfer Object) Pattern

**What it is:** Objects for transferring data between layers

**Why separate from models:**

- **Security**: Don't expose internal model structure
- **Flexibility**: API can change without changing database
- **Validation**: Input validation separate from model validation
- **Documentation**: Clear API contract

**Example:**

```go
// Request DTO (what API accepts)
type RegisterRequest struct {
    Email    string `json:"email" validate:"required,email"`
    Password string `json:"password" validate:"required,min=8"`
    Name     string `json:"name" validate:"required"`
}

// Response DTO (what API returns)
type UserResponse struct {
    ID        uint      `json:"id"`
    Email     string    `json:"email"`
    Name      string    `json:"name"`
    CreatedAt time.Time `json:"created_at"`
    // NOTE: Password is never in response
}

// Internal Model (database entity)
type User struct {
    ID        uint      `gorm:"primaryKey"`
    Email     string    `gorm:"uniqueIndex;not null"`
    Password  string    `gorm:"not null"` // Hashed
    Name      string    `gorm:"not null"`
    Role      string    `gorm:"not null;default:'user'"`
    CreatedAt time.Time
    UpdatedAt time.Time
}
```

**Key principle:** Never expose internal models directly through APIs.

---

### 5. Middleware Pattern

**What it is:** Functions that run before/after handlers

**Common uses:**

- Authentication (verify tokens)
- Authorization (check permissions)
- Logging (request/response)
- Rate limiting
- CORS
- Error recovery

**Execution order:**

```
Request → Middleware 1 → Middleware 2 → Handler → Middleware 2 (after) → Middleware 1 (after) → Response
```

**Example:**

```go
// Authentication middleware
func AuthMiddleware(tokenService TokenService) fiber.Handler {
    return func(c fiber.Ctx) error {
        // 1. Extract token
        token := c.Get("Authorization")
        if token == "" {
            return c.Status(401).JSON(fiber.Map{"error": "unauthorized"})
        }

        // Remove "Bearer " prefix
        token = strings.TrimPrefix(token, "Bearer ")

        // 2. Validate token
        claims, err := tokenService.ValidateToken(token)
        if err != nil {
            return c.Status(401).JSON(fiber.Map{"error": "invalid token"})
        }

        // 3. Store user info in context for handlers
        c.Locals("userID", claims.UserID)
        c.Locals("role", claims.Role)

        // 4. Continue to next middleware/handler
        return c.Next()
    }
}

// Usage
app.Use("/api/protected", AuthMiddleware(tokenService))
```

**Authorization middleware:**

```go
func RequireRole(roles ...string) fiber.Handler {
    return func(c fiber.Ctx) error {
        userRole := c.Locals("role").(string)

        for _, role := range roles {
            if userRole == role {
                return c.Next()
            }
        }

        return c.Status(403).JSON(fiber.Map{"error": "forbidden"})
    }
}

// Usage: Only admins can access
app.Delete("/api/users/:id", RequireRole("admin"), userHandler.Delete)
```

**Key principle:** Middleware for cross-cutting concerns, not business logic.

---

### 6. Configuration Pattern

**What it is:** Centralized application configuration

**Why:**

- **Security**: No hardcoded secrets
- **Flexibility**: Different configs for dev/staging/prod
- **12-Factor App**: Configuration via environment

**Example:**

```go
type Config struct {
    Server   ServerConfig
    Database DatabaseConfig
    JWT      JWTConfig
    Redis    RedisConfig
}

type ServerConfig struct {
    Port string
    Env  string // "development" | "production"
}

type DatabaseConfig struct {
    Host     string
    Port     string
    User     string
    Password string
    DBName   string
    SSLMode  string
}

type JWTConfig struct {
    Secret           string
    AccessTokenTTL   time.Duration
    RefreshTokenTTL  time.Duration
}

func LoadConfig() (*Config, error) {
    // Load from .env file
    if err := godotenv.Load(); err != nil {
        log.Println("No .env file found")
    }

    return &Config{
        Server: ServerConfig{
            Port: getEnv("PORT", "3000"),
            Env:  getEnv("ENV", "development"),
        },
        Database: DatabaseConfig{
            Host:     getEnv("DB_HOST", "localhost"),
            Port:     getEnv("DB_PORT", "5432"),
            User:     getEnv("DB_USER", "postgres"),
            Password: getEnv("DB_PASSWORD", ""),
            DBName:   getEnv("DB_NAME", "myapp"),
            SSLMode:  getEnv("DB_SSLMODE", "disable"),
        },
        JWT: JWTConfig{
            Secret:          mustGetEnv("JWT_SECRET"), // Required
            AccessTokenTTL:  15 * time.Minute,
            RefreshTokenTTL: 7 * 24 * time.Hour,
        },
    }, nil
}

func getEnv(key, defaultValue string) string {
    if value := os.Getenv(key); value != "" {
        return value
    }
    return defaultValue
}

func mustGetEnv(key string) string {
    value := os.Getenv(key)
    if value == "" {
        panic(fmt.Sprintf("%s environment variable is required", key))
    }
    return value
}
```

**Key principle:** Configuration is injected, never hardcoded.

---

## 🔄 Data Flow Example

Let's trace a complete request through the architecture:

### Scenario: User Registration

```
1. HTTP Request arrives
   POST /api/auth/register
   Body: {"email": "user@example.com", "password": "secret123"}

2. Fiber Router
   → Matches route → Calls handler

3. Handler (handlers/auth_handler.go)
   → Parses request body into DTO
   → Validates DTO
   → Calls service.Register()

4. Service (services/user_service.go)
   → Checks if user exists (via repository)
   → Hashes password (bcrypt)
   → Creates User model
   → Calls repository.Create()
   → Sends welcome email (async)
   → Returns User

5. Repository (repository/user_repository.go)
   → Inserts into database
   → Returns created User with ID

6. Handler (back from service)
   → Converts User model to DTO.UserResponse
   → Returns JSON response with 201 status

7. HTTP Response
   {"id": 1, "email": "user@example.com", "created_at": "..."}
```

**Why this flow?**

- **Handler**: Knows nothing about business logic or database
- **Service**: Knows nothing about HTTP or SQL
- **Repository**: Knows nothing about business rules or HTTP

**Result**: Each layer can be tested independently and changed without affecting others.

---

## 🎯 When to Use Each Pattern

### Use Handler Layer When:

- Processing HTTP requests
- Parsing request bodies
- Setting HTTP status codes
- Formatting responses

### Use Service Layer When:

- Implementing business logic
- Coordinating multiple repositories
- Complex validation rules
- Handling transactions
- Making decisions based on business rules

### Use Repository Layer When:

- Querying database
- Saving/updating data
- Complex SQL queries
- Transaction management

### Use DTOs When:

- Accepting API input
- Returning API responses
- Need different validation than model
- Want to hide internal structure

### Use Middleware When:

- Cross-cutting concerns (auth, logging)
- Request preprocessing
- Response postprocessing
- Rate limiting
- CORS handling

---

## 🔄 Evolution of Patterns

### Simple CRUD App

```
Handler → Repository → Database
```

Good for: Simple APIs, prototypes

### Adding Business Logic

```
Handler → Service → Repository → Database
```

Good for: Most applications

### Complex Systems

```
Handler → Service → Multiple Repositories → Databases
              ↓
         Message Queue → Workers
              ↓
         Cache Layer (Redis)
```

Good for: Production systems, high scale

**Key insight:** Start simple, add layers as complexity grows.

---

## 📚 Pattern Tradeoffs

### Layered Architecture

**Pros:**

- Clear separation of concerns
- Easy to test each layer
- Can swap implementations
- Team members can work on different layers

**Cons:**

- More boilerplate code
- Potentially over-engineered for simple apps
- Need to pass data through layers

**When to use:** Applications with business logic beyond simple CRUD

---

### Repository Pattern

**Pros:**

- Database-agnostic
- Easy to mock for testing
- Centralized query logic
- Can optimize queries in one place

**Cons:**

- Extra layer of abstraction
- Potential performance overhead
- Can be overkill for simple queries

**When to use:** When you want flexibility to change databases or extensive testing

---

### Service Pattern

**Pros:**

- Business logic in one place
- Reusable across different interfaces (HTTP, CLI, gRPC)
- Testable without HTTP layer

**Cons:**

- Can become a "god object" if not careful
- Need to decide what belongs in service vs repository

**When to use:** Any app with business logic beyond basic CRUD

---

## 🎓 Learning Progression

### Phase 0: Learn the Backend Runtime First

- Project 00a: Build a JSON API with `net/http`
- Project 00b: Add `context.Context`, timeouts, config, and error wrapping
- Project 00c: Use PostgreSQL with `database/sql`, migrations, and transactions
- Project 00d: Add unit, handler, integration, and benchmark tests
- Project 00e: Add Docker Compose, structured logs, health checks, and graceful shutdown

The point is to understand the primitives before frameworks hide them. Fiber and Gin become easier to compare when you already know the HTTP lifecycle, cancellation, database boundaries, and test strategy.

### Phase 1: Learn Patterns in Isolation

- Project 01: Handler + Repository (no service yet)
- Project 02: Add service layer
- Project 03: Add middleware
- Project 04: Add DTOs

### Phase 2: Apply to Real Features

- Project 05-10: Use all patterns together
- See when each pattern helps
- Experience pain of NOT using patterns

### Phase 3: Advanced Patterns

- Project 11+: CQRS, Event Sourcing, Microservices
- Understand when advanced patterns are needed
- Learn tradeoffs from experience

---

## 🚀 Best Practices

### General

1. **Start simple, add complexity as needed**
2. **Each file should have one responsibility**
3. **Interfaces for testing and flexibility**
4. **Dependency injection over global variables**
5. **Error handling at every layer**

### Go-Specific

1. **Accept interfaces, return structs**
2. **Use context.Context for cancellation and timeouts**
3. **Pointers for models, values for DTOs**
4. **Explicit error handling (no silent failures)**
5. **Use struct tags for validation and serialization**

### Testing

1. **Unit test services (business logic)**
2. **Integration test repositories (database)**
3. **E2E test handlers (API contracts)**
4. **Mock external dependencies**
5. **Test error cases, not just happy path**

---

This architecture evolves as you build. Start with basics, add patterns as you need them, learn from experience.
