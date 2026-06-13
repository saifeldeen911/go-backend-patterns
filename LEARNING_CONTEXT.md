# Learning Context & Philosophy

This document explains the **why** behind this repository and serves as context for AI assistants helping with development.

## 🎯 Learning Goals

### Primary Objective

Master Go backend development and distributed systems design through **practical implementation**, not passive learning.

### What Success Looks Like

- Build production-grade, reusable Go modules
- Understand system design tradeoffs from real implementation experience
- Create a portfolio demonstrating senior-level backend engineering
- Develop muscle memory for Go idioms and patterns
- Gain hands-on experience with scalability and reliability patterns

## 🧠 Background & Context

### Student Profile

- **Background**: Computer Science graduate with software engineering experience
- **Current Knowledge**:
  - Strong programming fundamentals
  - Understanding of software architecture concepts
  - Familiar with debugging and problem-solving
  - Experience with other programming languages
- **Gap**: Go syntax, Go-specific idioms, Go ecosystem
- **NOT Learning**:
  - Basic programming (variables, loops, functions)
  - General software engineering principles
  - Algorithm fundamentals
  - How to use Git

### Learning Style

- **Preference**: Learning by building real projects
- **Anti-pattern**: Tutorial hell, hello-world examples, passive video watching
- **Strength**: Can understand complex systems when learning in context
- **Approach**: Build → Break → Understand → Refactor → Extract → Reuse

## 🏗 Repository Philosophy

### Core Principles

#### 1. Realistic Backend Work from Day One

- No toy examples or oversimplified demos
- Every project should exercise real backend concerns
- Implement proper error handling, validation, and security from the start
- Use realistic scenarios (auth systems, not "todo apps")

#### 2. Fundamentals Before Framework Dependence

- Start with `net/http`, `context`, `database/sql`, testing, Docker, and graceful shutdown
- Learn what frameworks are wrapping before depending on Fiber or Gin
- Make request lifecycle, cancellation, errors, and tests automatic
- Keep the fundamentals track short and practical, not academic

#### 3. Build Reusable Modules

- Each foundational project becomes a reusable package
- Modules should be importable in future projects
- Design for composition, not duplication
- Example: Build auth once, use it in 5+ projects

#### 4. Framework-Agnostic Patterns

- **Primary framework: Fiber v3** (Express-inspired, beginner-friendly, fast)
- **Alternative implementations: Gin** (mature, widely used, `net/http`-based)
- Focus on **patterns** (handler/service/repository) that work in ANY framework
- Learn: "This is how you structure a backend", not "This is Fiber-specific code"
- Selected projects reimplemented in Gin to demonstrate portability
- Goal: Be employable with Gin, Echo, Chi, or any Go web framework

#### 5. Progressive Complexity

- **Phase 0**: Build Go backend fundamentals without framework dependence
- **Phase 1**: Build individual modules (auth, RBAC, file upload)
- **Phase 2**: Combine modules + add scaling patterns (caching, replication)
- **Phase 3**: Add reliability patterns (circuit breakers, retries) + advanced architecture (CQRS)
- **Capstone**: Full production system using everything (in BOTH Fiber and Gin)

#### 6. Learn Advanced System Design Through Implementation

Rather than just reading about distributed systems:

- Implement caching and experience cache invalidation challenges
- Build with database replication and face consistency tradeoffs
- Create circuit breakers and understand cascading failures
- Implement CQRS and see when it's overkill vs. necessary

Advanced topics are included intentionally because practical exposure to scalability, reliability, and distributed-systems tradeoffs is what separates theory-only knowledge from strong backend engineering. The goal is not to over-engineer every early project; the goal is to turn concepts you may already know theoretically into working code, failures, measurements, and tradeoff notes.

#### 7. Documentation as Learning Tool

- Write README for each project → forces clear thinking
- Maintain LEARNINGS.md → capture mistakes and insights
- Document architectural decisions → builds interview skills
- Explain patterns in own words → deepens understanding

## 🔄 Learning Flow

### For Each Project

```
1. PLAN
   ├─ Read project README
   ├─ Understand the pattern/concept being learned
   ├─ Identify what's new vs. what's reused
   └─ Set success criteria

2. BUILD
   ├─ Use AI to explain Go syntax and idioms
   ├─ Type code manually (no copy-paste initially)
   ├─ Ask "why" for unfamiliar patterns
   ├─ Test incrementally (don't build everything then run)
   └─ Run the project and verify functionality

3. UNDERSTAND
   ├─ Break things intentionally
   ├─ Change values and see effects
   ├─ Read error messages carefully
   ├─ Ask AI to explain unexpected behavior
   └─ Debug problems before asking for solutions

4. REFACTOR
   ├─ Review code for Go idioms
   ├─ Ask AI: "Is this idiomatic Go?"
   ├─ Simplify complex sections
   ├─ Add comments for tricky parts
   └─ Improve error handling

5. EXTRACT
   ├─ Identify reusable components
   ├─ Move to modules/ directory
   ├─ Create clean public API
   ├─ Write module documentation
   └─ Add usage examples

6. DOCUMENT
   ├─ Update project LEARNINGS.md
   ├─ Note mistakes and how you fixed them
   ├─ Record "aha moments"
   ├─ Document tradeoffs observed
   └─ Update ROADMAP.md
```

## 🎓 Key Learning Objectives by Phase

### Phase 0: Go Backend Fundamentals (Weeks 0-2)

**Go HTTP Fundamentals**

- `net/http` routing and handler signatures
- Request parsing and response writing
- JSON encoding and decoding
- Status codes and API error responses
- Middleware basics without a framework

**Runtime and Reliability Basics**

- `context.Context` cancellation and deadlines
- Graceful shutdown
- Timeouts around HTTP and database work
- Error wrapping, sentinel errors, and domain errors
- Environment-based configuration

**Database and Testing Basics**

- PostgreSQL with `database/sql`
- Manual SQL migrations
- Transactions and rollback behavior
- Unit tests, table-driven tests, handler tests
- Integration tests and basic benchmarks

**Local Operations**

- Docker Compose for app dependencies
- Health checks
- Structured logging basics
- Minimal observability habits before advanced systems

### Phase 1: Foundation (Weeks 3-6)

**Go Language Mastery**

- Go syntax and idioms
- Error handling patterns (`if err != nil`)
- Struct tags for JSON/validation
- Pointers vs values
- Interfaces and composition
- Goroutines and channels (basics)
- Package management with Go modules

**Web Development with Fiber v3**

- Routing and route groups
- Middleware chains
- Request/response handling
- Context usage
- Error handling in HTTP handlers

**Database with GORM**

- Model definitions
- Migrations
- CRUD operations
- Associations (has-many, belongs-to)
- Query building
- Transactions

**Authentication & Authorization**

- JWT token generation and validation
- Password hashing with bcrypt
- Token refresh flows
- Role-based access control
- Permission systems

**Integration Patterns**

- External API calls
- SMTP integration
- File upload handling
- S3/cloud storage integration

### Phase 2: Scaling (Weeks 7-12)

**Read Scaling**

- Redis caching strategies (cache-aside, write-through)
- Cache invalidation patterns
- Database read replicas
- Connection pooling
- Query optimization and indexing
- Monitoring query performance

**Write Scaling**

- Asynchronous processing with message queues
- Background job patterns
- Batch processing
- Database sharding concepts
- Horizontal vs vertical scaling

**Real-Time Features**

- WebSocket connections
- Server-Sent Events (SSE)
- Long polling
- Pub/sub patterns
- Connection management at scale

**System Design Concepts**

- CAP theorem in practice
- Consistency vs availability tradeoffs
- Latency vs throughput
- When to cache, when not to cache
- Read-heavy vs write-heavy workloads

### Phase 3: Reliability (Weeks 13-18)

**Failure Handling**

- Retry logic with exponential backoff
- Circuit breaker pattern
- Bulkhead isolation
- Graceful degradation
- Timeout management

**Distributed Systems**

- Idempotency in APIs
- Message queue patterns (RabbitMQ, NATS)
- Worker pools and job processing
- Saga pattern for distributed transactions
- Event sourcing basics

**Advanced Architecture**

- CQRS (Command Query Responsibility Segregation)
- Event-driven architecture
- Microservices communication patterns
- Service discovery and inter-service communication; service mesh as an optional advanced layer

**Production Readiness**

- Health checks (liveness/readiness)
- Graceful shutdown
- Structured logging
- Metrics and monitoring
- Configuration management
- Secret management

## 💡 What Makes This Repository Different

### Not Just Code Examples

- **Problem**: Most repos show "how" but not "why"
- **This Repo**: Documents decisions, tradeoffs, and lessons learned

### Not Just Tutorials

- **Problem**: Tutorials hold your hand, you don't learn to think
- **This Repo**: Projects with goals, you figure out implementation (with AI help)

### Not Isolated Concepts

- **Problem**: Learning caching in isolation doesn't show when/why to use it
- **This Repo**: Each concept applied in realistic context with previous modules

### Not Static Code

- **Problem**: Clone and forget
- **This Repo**: Living documentation of learning journey, meant to evolve

## 🎯 Success Metrics

### Technical Metrics

- [ ] Can build a new Go REST API from scratch without looking up basics
- [ ] Can explain Go's error handling philosophy and why it's designed that way
- [ ] Can identify which architectural pattern fits a given problem
- [ ] Can discuss tradeoffs between different approaches (with real examples)
- [ ] Can debug Go applications effectively

### System Design Metrics

- [ ] Can design a scalable read-heavy system
- [ ] Can design a resilient write-heavy system
- [ ] Can identify single points of failure in an architecture
- [ ] Can propose solutions to scalability bottlenecks
- [ ] Can explain CAP theorem with real implementation examples

### Portfolio Metrics

- [ ] Have 20+ completed Go projects/labs, with the capstones held to production-ready standards
- [ ] Have 5+ reusable modules others can use
- [ ] Have documentation that shows deep understanding
- [ ] Can walk through any project in an interview
- [ ] Can explain specific technical decisions and their outcomes

## 🚫 Anti-Patterns to Avoid

### During Learning

- **Copy-pasting without understanding** → Type code manually, ask questions
- **Building everything at once** → Build incrementally, test often
- **Ignoring errors** → Read error messages, understand what broke
- **Tutorial dependency** → Use AI as a mentor, not a solution generator
- **Premature optimization** → Make it work, then make it better
- **Perfectionism** → Done is better than perfect, iterate

### In Code

- **Over-engineering** → Start simple, add complexity only when needed
- **Ignoring Go idioms** → Learn and apply idiomatic Go patterns
- **No error handling** → Handle errors properly from day one
- **Magic numbers** → Use constants, environment variables
- **God objects** → Separate concerns (handlers, services, repositories)
- **No tests** → Write tests for critical paths

## 🔄 Evolution of This Repository

### Current Phase: Setup & Foundation

- Establishing structure
- Creating Phase 0 fundamentals
- Building the first backend labs and foundation modules

### Near Future: Scaling & Composition

- Combining modules in complex projects
- Adding scalability patterns
- Encountering real architectural decisions

### Long Term: Advanced Patterns & Contributions

- CQRS, event sourcing, microservices, and durable workflow engines
- Contributing modules back to community
- Helping others learn from this approach

## 📚 Complementary Learning Resources

While this repo focuses on building, these resources provide theoretical context:

**System Design**

- [System Design Primer](https://github.com/donnemartin/system-design-primer)
- [Designing Data-Intensive Applications](https://dataintensive.net/)

**Go Language**

- [Effective Go](https://go.dev/doc/effective_go)
- [Go by Example](https://gobyexample.com/)

**Backend Patterns**

- [Microservices Patterns](https://microservices.io/patterns/)
- [Enterprise Integration Patterns](https://www.enterpriseintegrationpatterns.com/)

**Use these to understand "why", then implement "how" in this repo.**

## 🎤 For AI Assistants

When helping with this repository, remember:

- The student wants to **understand**, not just have working code
- Always explain **why**, not just **how**
- Encourage experimentation and breaking things
- Point out Go idioms and best practices
- Suggest improvements but don't just provide them
- Ask clarifying questions to ensure understanding
- Reference `AI_RULES.md` for detailed guidelines

---

This is a **learning journey**, not a race. Quality over speed. Understanding over completion.
