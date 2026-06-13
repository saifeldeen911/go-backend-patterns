# Framework Guide: Fiber vs Gin

Understanding Go web frameworks and why patterns matter more than syntax.

## 🎯 Why Learn Multiple Frameworks?

### The Reality

- **Fiber**: Express-inspired API, strong docs, high-performance design, now with v3 APIs that differ from older v2 examples
- **Gin**: Mature, widely used, `net/http`-based ecosystem, strong docs and middleware support
- **Echo, Chi, net/http**: Also used in production

### Your Strategy

1. **Learn with Fiber** (Phases 1-2) - Focus on patterns, not syntax
2. **Implement key projects in Gin** (Phase 1b, 2b, 3) - See portability
3. **Understand both** - Be employable anywhere

### The Truth

**The framework doesn't matter as much as you think.** What matters:

- Understanding layered architecture
- Knowing how middleware works
- Proper error handling
- Repository pattern
- Service layer design

These patterns transfer across frameworks, but the adapters are not identical. Fiber is built around `fasthttp`, while Gin builds on the standard `net/http` request model. That affects middleware integration, request context handling, and some third-party library compatibility.

---

## 📊 Framework Comparison

### Practical Comparison

| Feature                 | Fiber             | Gin                | Note                   |
| ----------------------- | ----------------- | ------------------ | ---------------------- |
| **Performance**         | Very fast         | Very fast          | Benchmark gaps rarely matter before architecture, DB, and IO |
| **Ecosystem fit**       | `fasthttp`-based  | `net/http`-based   | Gin usually integrates more directly with standard Go HTTP tooling |
| **Adoption**            | Growing           | More established   | Verify job-market signals locally when this matters |
| **Learning Curve**      | Easy              | Easy               | Both beginner-friendly |
| **Documentation**       | Excellent         | Excellent          | Both well-documented   |
| **API Style**           | Express.js-like   | Simple & idiomatic | Both clean             |
| **Middleware**          | Built-in rich set | Good ecosystem     | Both extensible        |

### When to Use Each

**Use Fiber when:**

- Building new projects (greenfield)
- You want an Express-inspired API and are comfortable with Fiber's `fasthttp` foundation
- You like Express.js-style API
- Working on personal/startup projects

**Use Gin when:**

- Joining existing teams that already use Gin or standard `net/http` middleware
- Maximizing compatibility with the standard Go HTTP ecosystem
- Contributing to popular projects
- Enterprise environments

**Reality:** You'll likely use what your company uses. **Knowing both makes you more employable.**

---

## 🔄 Side-by-Side Comparison

### Basic Server Setup

#### Fiber

```go
package main

import "github.com/gofiber/fiber/v3"

func main() {
    app := fiber.New()

    app.Get("/", func(c fiber.Ctx) error {
        return c.JSON(fiber.Map{
            "message": "Hello from Fiber",
        })
    })

    app.Listen(":3000")
}
```

#### Gin

```go
package main

import "github.com/gin-gonic/gin"

func main() {
    app := gin.Default()

    app.GET("/", func(c *gin.Context) {
        c.JSON(200, gin.H{
            "message": "Hello from Gin",
        })
    })

    app.Run(":3000")
}
```

**Key Differences:**

- Fiber v3 uses `c fiber.Ctx`, Gin uses `c *gin.Context`
- Fiber: `c.JSON()`, Gin: `c.JSON(statusCode, data)`
- Fiber: `app.Listen()`, Gin: `app.Run()`

> Version note: older Fiber v2 examples use `github.com/gofiber/fiber/v2`, `func(c *fiber.Ctx)`, and `c.BodyParser(&req)`. Current Fiber v3 examples use `github.com/gofiber/fiber/v3`, `func(c fiber.Ctx)`, and `c.Bind().Body(&req)`.

---

### Request Parsing

#### Fiber

```go
type RegisterRequest struct {
    Email    string `json:"email" validate:"required,email"`
    Password string `json:"password" validate:"required,min=8"`
}

func Register(c fiber.Ctx) error {
    var req RegisterRequest

    // Parse body
    if err := c.Bind().Body(&req); err != nil {
        return c.Status(400).JSON(fiber.Map{
            "error": "Invalid request body",
        })
    }

    // Access path params
    id := c.Params("id")

    // Access query params
    page := c.Query("page", "1")

    return c.JSON(fiber.Map{"success": true})
}
```

#### Gin

```go
type RegisterRequest struct {
    Email    string `json:"email" binding:"required,email"`
    Password string `json:"password" binding:"required,min=8"`
}

func Register(c *gin.Context) {
    var req RegisterRequest

    // Parse body (with validation)
    if err := c.ShouldBindJSON(&req); err != nil {
        c.JSON(400, gin.H{
            "error": "Invalid request body",
        })
        return
    }

    // Access path params
    id := c.Param("id")

    // Access query params
    page := c.DefaultQuery("page", "1")

    c.JSON(200, gin.H{"success": true})
}
```

**Key Differences:**

- Fiber: `validate` tag, Gin: `binding` tag
- Fiber v3: `c.Bind().Body()`, Gin: `c.ShouldBindJSON()`
- Fiber: `c.Params()`, Gin: `c.Param()`
- Fiber: `c.Query()`, Gin: `c.DefaultQuery()` or `c.Query()`
- Fiber returns errors, Gin uses explicit returns

---

### Middleware

#### Fiber

```go
func AuthMiddleware(c fiber.Ctx) error {
    token := c.Get("Authorization")

    if token == "" {
        return c.Status(401).JSON(fiber.Map{
            "error": "unauthorized",
        })
    }

    // Validate token...
    userID := 123 // from token

    // Store in context
    c.Locals("userID", userID)

    // Continue to next handler
    return c.Next()
}

// Usage
app.Use(AuthMiddleware)
app.Get("/protected", func(c fiber.Ctx) error {
    userID := c.Locals("userID").(int)
    return c.JSON(fiber.Map{"user_id": userID})
})
```

#### Gin

```go
func AuthMiddleware() gin.HandlerFunc {
    return func(c *gin.Context) {
        token := c.GetHeader("Authorization")

        if token == "" {
            c.JSON(401, gin.H{
                "error": "unauthorized",
            })
            c.Abort() // Stop execution
            return
        }

        // Validate token...
        userID := 123 // from token

        // Store in context
        c.Set("userID", userID)

        // Continue to next handler
        c.Next()
    }
}

// Usage
app.Use(AuthMiddleware())
app.GET("/protected", func(c *gin.Context) {
    userID, _ := c.Get("userID")
    c.JSON(200, gin.H{"user_id": userID})
})
```

**Key Differences:**

- Fiber: `c.Locals()`, Gin: `c.Set()` and `c.Get()`
- Fiber: Return error to stop, Gin: Call `c.Abort()`
- Gin middleware returns a function (closure pattern)

---

### Route Groups

#### Fiber

```go
app := fiber.New()

// API v1 group
api := app.Group("/api/v1")

// Auth routes
auth := api.Group("/auth")
auth.Post("/register", handlers.Register)
auth.Post("/login", handlers.Login)

// Protected routes
protected := api.Group("/users")
protected.Use(middleware.Auth)
protected.Get("/me", handlers.GetProfile)
protected.Put("/me", handlers.UpdateProfile)
```

#### Gin

```go
app := gin.Default()

// API v1 group
api := app.Group("/api/v1")

// Auth routes
auth := api.Group("/auth")
{
    auth.POST("/register", handlers.Register)
    auth.POST("/login", handlers.Login)
}

// Protected routes
protected := api.Group("/users")
protected.Use(middleware.Auth())
{
    protected.GET("/me", handlers.GetProfile)
    protected.PUT("/me", handlers.UpdateProfile)
}
```

**Key Differences:**

- Very similar! Gin uses `{}` blocks by convention
- Fiber: `.Use(middleware)`, Gin: `.Use(middleware())`

---

## 🏗 The SAME Patterns in Both

### Handler → Service → Repository

This architecture is **mostly reusable** regardless of framework:

```go
// handlers/user_handler.go (Framework-specific)

// FIBER VERSION
func (h *UserHandler) GetProfile(c fiber.Ctx) error {
    userID := c.Locals("userID").(uint)

    user, err := h.userService.GetByID(c.Context(), userID)
    if err != nil {
        return c.Status(500).JSON(fiber.Map{"error": err.Error()})
    }

    return c.JSON(dto.ToUserResponse(user))
}

// GIN VERSION
func (h *UserHandler) GetProfile(c *gin.Context) {
    userID, _ := c.Get("userID")

    user, err := h.userService.GetByID(c.Request.Context(), userID.(uint))
    if err != nil {
        c.JSON(500, gin.H{"error": err.Error()})
        return
    }

    c.JSON(200, dto.ToUserResponse(user))
}
```

**The service and repository layers are IDENTICAL:**

```go
// services/user_service.go (Framework-agnostic!)

type UserService interface {
    GetByID(ctx context.Context, id uint) (*models.User, error)
    // ... other methods
}

type userService struct {
    userRepo repository.UserRepository
}

func (s *userService) GetByID(ctx context.Context, id uint) (*models.User, error) {
    // This code is the SAME regardless of framework
    user, err := s.userRepo.FindByID(ctx, id)
    if err != nil {
        return nil, err
    }
    return user, nil
}

// repository/user_repository.go (Framework-agnostic!)

type UserRepository interface {
    FindByID(ctx context.Context, id uint) (*models.User, error)
    // ... other methods
}

type userRepository struct {
    db *gorm.DB
}

func (r *userRepository) FindByID(ctx context.Context, id uint) (*models.User, error) {
    // This code is the SAME regardless of framework
    var user models.User
    err := r.db.WithContext(ctx).First(&user, id).Error
    return &user, err
}
```

**See?** Most service, repository, model, and DTO code can stay framework-agnostic. The HTTP adapter layer still deserves careful framework-specific attention.

---

## 🎓 Learning Strategy

### Phase 1: Master Patterns with Fiber

**Projects 01-06: Use Fiber**

- Focus on learning patterns, not framework syntax
- Fiber's Express-like API is intuitive for beginners
- Build your foundation modules

**Project 06b: Rebuild 01 in Gin**

- After completing `auth-jwt-basics` in Fiber
- Rebuild the SAME project in Gin
- See how patterns translate
- Notice what is portable and what is framework-specific

### Phase 2: Understand Portability

**Projects 07-15: Continue with Fiber**

- You're comfortable with basics now
- Focus on scaling patterns (caching, WebSockets, etc.)

**Project 15b: Rebuild 13 in Gin**

- Implement WebSockets in Gin
- Compare real-time handling in both frameworks
- Reinforce framework-agnostic thinking

### Phase 3: Demonstrate Mastery

**Project 25: Capstone in Fiber**

- Full production API with all patterns

**Project 25b: Capstone in Gin**

- Same functionality, different framework
- Proves you understand patterns, not just syntax
- **This is extremely impressive in interviews**

---

## 💼 Career Impact

### In Interviews

**With only Fiber:**

- "I know Fiber"
- Interviewer: "We use Gin, can you adapt?"
- You: "I'd need to learn Gin"

**With both Fiber and Gin:**

- "I've built production systems in both Fiber and Gin"
- Interviewer: "Interesting, what's your take on both?"
- You: "Both are solid. The core architecture transfers - handlers, services, repositories, DTOs - but the HTTP adapter details differ. I can work with either, and I know what to check when moving between Fiber, Gin, Echo, Chi, or net/http."

**This demonstrates:**

- Deep understanding of patterns vs surface syntax
- Adaptability
- Architectural thinking
- Production experience

---

## 🔄 Conversion Cheat Sheet

Quick reference for moving between frameworks:

| Task                | Fiber                    | Gin                            |
| ------------------- | ------------------------ | ------------------------------ |
| **Context**         | `c fiber.Ctx`            | `c *gin.Context`               |
| **Parse JSON**      | `c.Bind().Body(&req)`    | `c.ShouldBindJSON(&req)`       |
| **Path param**      | `c.Params("id")`         | `c.Param("id")`                |
| **Query param**     | `c.Query("page")`        | `c.Query("page")`              |
| **Get header**      | `c.Get("Authorization")` | `c.GetHeader("Authorization")` |
| **Return JSON**     | `c.JSON(data)`           | `c.JSON(200, data)`            |
| **Status code**     | `c.Status(404)`          | (included in JSON)             |
| **Store data**      | `c.Locals("key", val)`   | `c.Set("key", val)`            |
| **Get data**        | `c.Locals("key")`        | `c.Get("key")`                 |
| **Stop chain**      | `return error`           | `c.Abort()`                    |
| **Continue**        | `c.Next()`               | `c.Next()`                     |
| **Request context** | Fiber v3 `Ctx` implements `context.Context`; verify v2 projects separately | `c.Request.Context()` |

---

## 🎯 Key Takeaway

**You're not learning "Fiber" or "Gin".**

**You're learning:**

- How to structure backend applications
- Layered architecture patterns
- Middleware chains
- Error handling strategies
- API design principles

**These skills transfer to:**

- Any Go framework (Echo, Chi, net/http)
- Any language (Node.js/Express, Python/Flask, Ruby/Rails)
- Any backend system

The framework is an adapter around your backend design. The patterns matter most, but the adapter details still matter.

---

## 📚 Resources

- [Fiber Documentation](https://docs.gofiber.io/)
- [Gin Documentation](https://gin-gonic.com/docs/)
- [Awesome Go Web Frameworks](https://github.com/avelino/awesome-go#web-frameworks)

---

## ✅ Your Action Items

- [ ] Complete Phase 0 with `net/http` first
- [ ] Build first 6 framework projects in Fiber v3 (learn patterns)
- [ ] Rebuild project 01 in Gin (see portability)
- [ ] Continue Phase 2 in Fiber (focus on scaling patterns)
- [ ] Rebuild project 13 in Gin (WebSockets comparison)
- [ ] Build both capstone projects (demonstrate mastery)
- [ ] Update resume: "Go backend projects with Fiber v3, Gin, and net/http"
- [ ] In interviews: Emphasize pattern knowledge over framework preference

**Remember: Frameworks come and go. Patterns are forever.** 🚀
