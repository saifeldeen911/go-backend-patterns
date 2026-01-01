# Go Backend Patterns & System Design

A comprehensive learning repository demonstrating production-ready Go backend patterns, distributed systems concepts, and scalability techniques through hands-on implementation.

## 🎯 Mission

Learn Go/Fiber backend development and system design by **building real-world projects** with reusable, production-grade modules. No tutorials, no hello world—just practical, composable systems.

## 🏗 Repository Structure

```
go-backend-patterns/
├── README.md                    # You are here
├── LEARNING_CONTEXT.md          # Full learning philosophy & goals
├── AI_RULES.md                  # Guidelines for AI assistance
├── FRAMEWORK_GUIDE.md           # Fiber vs Gin comparison & strategy
├── ARCHITECTURE.md              # Deep dive on patterns & decisions
├── ROADMAP.md                   # Progress tracker
│
├── modules/                     # Reusable packages (import in any project)
│   ├── auth-jwt/               # JWT authentication system
│   ├── rbac/                   # Role-based access control
│   ├── smtp-otp/               # Email verification
│   ├── file-upload/            # File handling (local/S3)
│   ├── rate-limiter/           # Redis-based rate limiting
│   └── audit-log/              # Activity tracking
│
├── projects/                    # Learning projects by phase
│   ├── phase-1-foundation/     # Weeks 1-4: Core modules
│   ├── phase-2-scaling/        # Weeks 5-10: Read/write scaling
│   └── phase-3-reliability/    # Weeks 11-16: Resilience patterns
│
└── examples/                    # Complete reference implementations
    ├── production-api-fiber/    # Full-stack using Fiber
    └── production-api-gin/      # Same app using Gin (portability demo)
```

## 📚 Learning Path

### **Phase 1: Foundation Modules** (Weeks 1-4)

Build standalone, reusable components:

| #   | Project                 | Concepts Learned                              | Status         |
| --- | ----------------------- | --------------------------------------------- | -------------- |
| 01  | `auth-jwt-basics`       | Registration, login, JWT tokens               | 🔲 Not Started |
| 02  | `auth-jwt-hardened`     | Token rotation, blacklisting, rate limiting   | 🔲 Not Started |
| 03  | `rbac-permissions`      | Roles, permissions, hierarchical access       | 🔲 Not Started |
| 04  | `smtp-otp-verification` | Email sending, OTP generation/validation      | 🔲 Not Started |
| 05  | `file-upload-storage`   | Multipart uploads, S3 integration, validation | 🔲 Not Started |
| 06  | `api-integration-ai`    | External APIs (Groq/OpenRouter), streaming    | 🔲 Not Started |

### **Phase 2: Scaling Patterns** (Weeks 5-10)

Apply modules while learning distributed systems:

#### Read Scaling

| #   | Project                | Concepts                             | Status         |
| --- | ---------------------- | ------------------------------------ | -------------- |
| 07  | `caching-strategies`   | Redis, cache-aside, invalidation     | 🔲 Not Started |
| 08  | `database-replication` | Master-slave, read replicas, pooling | 🔲 Not Started |
| 09  | `indexing-performance` | Query optimization, explain plans    | 🔲 Not Started |

#### Write Scaling

| #   | Project              | Concepts                                  | Status         |
| --- | -------------------- | ----------------------------------------- | -------------- |
| 10  | `async-writes-queue` | Message queues, background jobs           | 🔲 Not Started |
| 11  | `batch-processing`   | Bulk operations, transaction batching     | 🔲 Not Started |
| 12  | `database-sharding`  | Horizontal partitioning, tenant isolation | 🔲 Not Started |

#### Real-Time Communication

| #   | Project                   | Concepts                                         | Framework | Status         |
| --- | ------------------------- | ------------------------------------------------ | --------- | -------------- |
| 13  | `websockets-realtime`     | WebSocket connections, pub/sub                   | Fiber     | 🔲 Not Started |
| 14  | `sse-notifications`       | Server-Sent Events, event streaming              | Fiber     | 🔲 Not Started |
| 15  | `long-polling`            | Long polling vs WebSockets/SSE                   | Fiber     | 🔲 Not Started |
| 15b | `websockets-realtime-gin` | **Same as 13 but with Gin** - Compare frameworks | Gin       | 🔲 Not Started |

### **Phase 3: Reliability & Advanced Patterns** (Weeks 11-16)

#### Resilience

| #   | Project           | Concepts                                  | Status         |
| --- | ----------------- | ----------------------------------------- | -------------- |
| 16  | `retry-patterns`  | Exponential backoff, jitter, policies     | 🔲 Not Started |
| 17  | `circuit-breaker` | Failure detection, fallbacks, recovery    | 🔲 Not Started |
| 18  | `idempotency`     | Idempotent endpoints, duplicate detection | 🔲 Not Started |
| 19  | `self-healing`    | Health checks, graceful degradation       | 🔲 Not Started |

#### Long-Running Processes

| #   | Project           | Concepts                               | Status         |
| --- | ----------------- | -------------------------------------- | -------------- |
| 20  | `message-queues`  | RabbitMQ/NATS, dead letter queues      | 🔲 Not Started |
| 21  | `worker-pools`    | Goroutines, concurrency, resource mgmt | 🔲 Not Started |
| 22  | `workflow-engine` | Temporal/Cadence, saga pattern         | 🔲 Not Started |

#### Advanced Architecture

| #   | Project                | Concepts                                       | Framework | Status         |
| --- | ---------------------- | ---------------------------------------------- | --------- | -------------- |
| 23  | `cqrs-pattern`         | Command/Query separation, event sourcing       | Fiber     | 🔲 Not Started |
| 24  | `microservices-basic`  | Service mesh, inter-service communication      | Mixed     | 🔲 Not Started |
| 25  | `production-api-fiber` | **Capstone with Fiber**: All patterns combined | Fiber     | 🔲 Not Started |
| 25b | `production-api-gin`   | **Capstone with Gin**: Demonstrate portability | Gin       | 🔲 Not Started |

## 🚀 Quick Start

### Prerequisites

- Go 1.21+
- Docker & Docker Compose
- PostgreSQL (via Docker)
- Redis (via Docker)
- Git

### Running Any Project

```bash
# Navigate to project
cd projects/phase-1-foundation/01-auth-jwt-basics

# Copy environment variables
cp .env.example .env

# Run with Docker (includes all dependencies)
docker-compose up

# OR run locally
go mod download
go run cmd/main.go
```

### Using Modules in Your Own Projects

```bash
# Import a module
go get github.com/yourusername/go-backend-patterns/modules/auth-jwt

# Use in your code
import "github.com/yourusername/go-backend-patterns/modules/auth-jwt"
```

## 🎓 Learning Approach

### Not a Tutorial Repository

This repo is built on **learning by doing**:

- No step-by-step tutorials
- No copy-paste solutions
- Real-world complexity from day one
- AI-assisted learning (see `AI_RULES.md`)

### How to Learn from Each Project

1. **Read the project README** - Understand goals and concepts
2. **Examine the code structure** - See architectural decisions
3. **Run the project** - See it working end-to-end
4. **Break things intentionally** - Learn from failures
5. **Extend functionality** - Add features, refactor patterns
6. **Extract to modules** - Make reusable for future projects

### Using AI as Your Mentor

See `AI_RULES.md` for guidelines on how to use GitHub Copilot or other AI tools to maximize learning while building.

## 📖 Key Concepts Covered

### Backend Fundamentals

- RESTful API design
- Authentication & Authorization (JWT, RBAC)
- Input validation & error handling
- File uploads & storage
- Email integration (SMTP)
- External API integration
- **Framework-agnostic patterns** (works with Fiber, Gin, Echo, Chi, etc.)

### Database & Persistence

- GORM (ORM for Go)
- PostgreSQL (relational database)
- Migrations & schema management
- Indexing & query optimization
- Connection pooling
- Database replication & sharding

### Caching & Performance

- Redis caching strategies
- Cache invalidation patterns
- Read replicas
- Query optimization

### Scalability

- Horizontal vs vertical scaling
- Load balancing concepts
- Async processing
- Batch operations
- Database sharding strategies

### Real-Time Communication

- WebSockets
- Server-Sent Events (SSE)
- Long polling
- Pub/sub patterns

### Reliability & Resilience

- Retry logic with exponential backoff
- Circuit breakers
- Idempotency
- Graceful degradation
- Health checks & monitoring

### Distributed Systems

- Message queues (RabbitMQ, NATS)
- Worker pools
- Workflow engines
- CQRS (Command Query Responsibility Segregation)
- Event sourcing basics
- Saga pattern

### DevOps & Operations

- Docker containerization
- Docker Compose for local dev
- Environment configuration
- Logging & monitoring
- Graceful shutdown

## 🛠 Technology Stack

### Core

- **Language**: Go 1.21+
- **Web Framework**: Fiber v2 (primary) / Gin (alternative implementations)
- **ORM**: GORM v2
- **Database**: PostgreSQL 15+
- **Cache**: Redis 7+

> **Framework Philosophy**: We primarily use **Fiber** for learning because of its Express-like API and excellent documentation. However, the patterns you learn (handlers, services, repositories, middleware) are **framework-agnostic**. Selected projects include **Gin implementations** to demonstrate portability. The goal is understanding backend patterns, not framework lock-in.

### Additional Libraries

- JWT: `golang-jwt/jwt`
- Validation: `go-playground/validator`
- Environment: `godotenv`
- Password Hashing: `bcrypt`
- UUID: `google/uuid`
- Circuit Breaker: `sony/gobreaker`

### Infrastructure

- **Containerization**: Docker
- **Orchestration**: Docker Compose
- **Message Queue**: RabbitMQ / NATS
- **Storage**: AWS S3 (optional)

## 📂 Project Structure Convention

Each project follows this consistent structure:

```
project-name/
├── README.md              # Project-specific documentation
├── LEARNINGS.md           # What you learned, mistakes, gotchas
├── docker-compose.yml     # Full dev environment
├── Dockerfile             # Multi-stage build
├── .env.example           # Environment variables template
├── go.mod                 # Go dependencies
├── go.sum                 # Dependency checksums
│
├── cmd/
│   └── main.go           # Application entry point
│
├── internal/              # Private application code
│   ├── handlers/         # HTTP handlers (controllers)
│   ├── services/         # Business logic
│   ├── repository/       # Data access layer
│   ├── models/           # Database entities
│   ├── dto/              # Data transfer objects
│   ├── middleware/       # Custom middleware
│   └── validators/       # Input validation
│
├── pkg/                   # Public reusable packages
│   ├── config/           # Configuration management
│   ├── database/         # DB connection setup
│   ├── logger/           # Logging setup
│   └── utils/            # Helper functions
│
├── migrations/            # Database migrations
└── tests/                # Integration tests
```

## 🤝 Contributing

This is a personal learning repository, but:

- **Issues**: Bug reports and suggestions welcome
- **Discussions**: Share your learnings and improvements
- **Forks**: Feel free to fork and adapt to your learning style

## 📝 License

MIT License - Feel free to use this for your own learning

## 🔗 Resources

- [Official Go Documentation](https://go.dev/doc/)
- [Fiber Documentation](https://docs.gofiber.io/)
- [GORM Documentation](https://gorm.io/docs/)
- [Anthropic Claude Docs](https://docs.claude.com/)
- [System Design Primer](https://github.com/donnemartin/system-design-primer)

## 📊 Progress Tracking

Track your journey in `ROADMAP.md` - update status as you complete each project.

---

**Start with Project 01 and build your way up. Each project compounds on the previous ones.**

Happy learning! 🚀
