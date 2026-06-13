# Project Name

> Brief one-sentence description of what this project demonstrates

## 🎯 Learning Objectives

What you will learn by building this project:
- Specific concept 1 (e.g., JWT token generation and validation)
- Specific concept 2 (e.g., Bcrypt password hashing)
- Specific concept 3 (e.g., Middleware chain execution)
- Go-specific feature (e.g., Context usage in HTTP handlers)

## 📋 Features

- [ ] Feature 1 (e.g., User registration with email validation)
- [ ] Feature 2 (e.g., Login with JWT token generation)
- [ ] Feature 3 (e.g., Protected routes with auth middleware)
- [ ] Feature 4 (e.g., Token refresh endpoint)

## 🛠 Tech Stack

- **Language**: Go latest stable (Go 1.26+ as of June 2026)
- **Framework**: Fiber v3 by default, or Gin for comparison projects
- **Database**: PostgreSQL 18+ recommended (15+ acceptable for compatibility labs)
- **ORM**: GORM v2, with awareness of the Generics API
- **Cache**: Redis 8+ recommended (if applicable)
- **Additional**: List any specific libraries used

## 🏗 Project Structure

```
project-name/
├── cmd/
│   └── main.go              # Application entry point
├── internal/
│   ├── handlers/            # HTTP handlers
│   │   └── auth_handler.go
│   ├── services/            # Business logic
│   │   └── auth_service.go
│   ├── repository/          # Data access
│   │   └── user_repository.go
│   ├── models/              # Database models
│   │   └── user.go
│   ├── dto/                 # Request/response objects
│   │   └── auth_dto.go
│   └── middleware/          # Custom middleware
│       └── auth_middleware.go
├── pkg/
│   ├── config/              # Configuration
│   ├── database/            # DB setup
│   └── logger/              # Logging
├── migrations/              # SQL migrations
│   └── 001_create_users.sql
├── docker-compose.yml
├── Dockerfile
├── .env.example
├── go.mod
└── README.md
```

## 🚀 Getting Started

### Prerequisites

- Go latest stable (Go 1.26+ as of June 2026)
- Docker and Docker Compose
- Make (optional)

### Installation & Running

#### Option 1: Docker (Recommended)

```bash
# 1. Clone the repository
cd projects/phase-X-category/project-name

# 2. Copy environment variables
cp .env.example .env

# 3. Start all services (app + database + redis)
docker-compose up

# App will be available at http://localhost:3000
```

#### Option 2: Local Development

```bash
# 1. Install dependencies
go mod download

# 2. Setup environment
cp .env.example .env
# Edit .env with your local database credentials

# 3. Start PostgreSQL (and Redis if needed)
docker-compose up -d postgres redis

# 4. Run migrations
go run cmd/migrate/main.go

# 5. Run the application
go run cmd/main.go
```

### Environment Variables

```bash
# Server
PORT=3000
ENV=development

# Database
DB_HOST=localhost
DB_PORT=5432
DB_USER=postgres
DB_PASSWORD=postgres
DB_NAME=project_db
DB_SSLMODE=disable

# JWT (change in production!)
JWT_SECRET=your-super-secret-key-change-in-production
JWT_ACCESS_TTL=15m
JWT_REFRESH_TTL=168h

# Redis (if applicable)
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_PASSWORD=
```

## 📡 API Endpoints

### Authentication

#### Register User
```http
POST /api/auth/register
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "SecurePass123",
  "name": "John Doe"
}
```

**Response (201 Created):**
```json
{
  "id": 1,
  "email": "user@example.com",
  "name": "John Doe",
  "created_at": "2024-01-15T10:30:00Z"
}
```

#### Login
```http
POST /api/auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "SecurePass123"
}
```

**Response (200 OK):**
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expires_in": 900
}
```

#### Get Profile (Protected)
```http
GET /api/users/me
Authorization: Bearer <access_token>
```

**Response (200 OK):**
```json
{
  "id": 1,
  "email": "user@example.com",
  "name": "John Doe",
  "role": "user",
  "created_at": "2024-01-15T10:30:00Z"
}
```

### Additional endpoints...

## 🧪 Testing

### Using cURL

```bash
# Register a user
curl -X POST http://localhost:3000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"Test123!","name":"Test User"}'

# Login
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"Test123!"}'

# Access protected route (use token from login response)
curl http://localhost:3000/api/users/me \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```

### Using Postman/Insomnia

Import the `postman_collection.json` file from the project root (if provided).

### Running Tests

```bash
# Run all tests
go test ./...

# Run with coverage
go test -cover ./...

# Run specific test
go test ./internal/services -v
```

## 🔑 Key Implementation Details

### 1. Password Hashing

```go
// Using bcrypt with default cost (10)
hashedPassword, err := bcrypt.GenerateFromPassword([]byte(password), bcrypt.DefaultCost)
```

**Why bcrypt?**
- Specifically designed for passwords
- Slow by design (prevents brute force)
- Built-in salt generation
- Future-proof (can increase cost over time)

### 2. JWT Token Generation

```go
type AccessClaims struct {
    UserID uint   `json:"user_id"`
    Email  string `json:"email"`
    jwt.RegisteredClaims
}

claims := AccessClaims{
    UserID: user.ID,
    Email:  user.Email,
    RegisteredClaims: jwt.RegisteredClaims{
        Subject:   strconv.FormatUint(uint64(user.ID), 10),
        ExpiresAt: jwt.NewNumericDate(time.Now().Add(15 * time.Minute)),
        IssuedAt:  jwt.NewNumericDate(time.Now()),
    },
}

token := jwt.NewWithClaims(jwt.SigningMethodHS256, claims)
```

**Why JWT?**
- Stateless authentication
- Can be verified without database lookup
- Contains user claims
- Expires automatically

### 3. Middleware Chain

```go
// Order matters!
app.Use(logger.New())              // 1. Log all requests
app.Use(cors.New())                // 2. Handle CORS
app.Use(recover.New())             // 3. Recover from panics

protected := app.Group("/api/protected")
protected.Use(AuthMiddleware())    // 4. Verify authentication
```

**Execution order:**
Request → Logger → CORS → Recover → Auth → Handler → Auth → Recover → CORS → Logger → Response

## 🎓 What You'll Learn

### Go Concepts
- [x] Struct tags for JSON and validation
- [x] Error handling with custom error types
- [x] Interface usage for dependency injection
- [x] Context usage for request-scoped data
- [x] Goroutines for async operations (if applicable)

### Backend Patterns
- [x] Layered architecture (handler/service/repository)
- [x] DTO pattern for API contracts
- [x] Repository pattern for data access
- [x] Middleware for cross-cutting concerns
- [x] Dependency injection via constructors

### Security
- [x] Password hashing with bcrypt
- [x] JWT authentication
- [x] Token expiration and refresh
- [x] Input validation
- [x] SQL injection prevention (via GORM)

### Fiber Framework
- [x] Request parsing and validation
- [x] Route groups
- [x] Middleware usage
- [x] Context locals for data sharing
- [x] Error handling

### GORM
- [x] Model definition and migrations
- [x] CRUD operations
- [x] Query building
- [x] Context-aware queries and transaction blocks
- [x] Awareness of the GORM Generics API (if useful for the project)
- [x] Associations (if applicable)
- [x] Transaction handling (if applicable)

## 🐛 Common Issues & Solutions

### Issue 1: "Connection refused" when starting app

**Cause:** Database not ready yet

**Solution:**
```bash
# Wait for database to be fully ready
docker-compose up -d postgres
sleep 5
go run cmd/main.go
```

Or use healthchecks in docker-compose.yml

### Issue 2: "jwt: token is expired"

**Cause:** Access token expired (default: 15 minutes)

**Solution:** Use the refresh token endpoint to get a new access token

### Issue 3: "duplicate key value violates unique constraint"

**Cause:** Email already exists in database

**Solution:** Use a different email or check with login first

## 🔄 Next Steps & Extensions

Once you've completed this project, consider:

- [ ] Add email verification with OTP
- [ ] Implement password reset flow
- [ ] Add OAuth2 providers (Google, GitHub)
- [ ] Implement rate limiting on auth endpoints
- [ ] Add user profile updates
- [ ] Implement role-based access control (RBAC)
- [ ] Add refresh token rotation
- [ ] Implement token blacklisting for logout
- [ ] Add two-factor authentication (2FA)

## 🔗 Related Modules

This project uses/creates the following reusable modules:

- `modules/auth-jwt` - JWT authentication (if extracted)
- Related to: Project 03 (RBAC), Project 04 (Email OTP)

## 📚 Resources & References

- [Fiber Documentation](https://docs.gofiber.io/)
- [GORM Guides](https://gorm.io/docs/)
- [JWT Best Practices](https://tools.ietf.org/html/rfc8725)
- [OWASP Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
- [Go Context Package](https://pkg.go.dev/context)

## 🤝 Contributing

Found an issue or want to improve this project? Feel free to open an issue or PR.

## 📝 License

MIT

---

## ✍️ LEARNINGS.md

After completing this project, document your experience in `LEARNINGS.md`:

- What worked well?
- What was confusing?
- What mistakes did you make?
- What would you do differently?
- What "aha moments" did you have?
- What Go idioms did you learn?
- What surprised you?

This reflection is key to solidifying your learning!
