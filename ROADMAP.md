# Learning Roadmap & Progress Tracker

Track your journey through the Go backend patterns repository.

**Started:** [Your Start Date]
**Current Phase:** Phase 0 - Go Backend Fundamentals
**Current Project:** Not Started
**Completion:** 0/33 projects (0%)

---

## 📊 Overall Progress

```
Phase 0: Fundamentals        ░░░░░░░░░░ 0/5  (0%)
Phase 1: Foundation          ░░░░░░░░░░ 0/7  (0%)
Phase 2: Scaling             ░░░░░░░░░░ 0/10 (0%)
Phase 3: Reliability         ░░░░░░░░░░ 0/11 (0%)
───────────────────────────────────────────
Total Progress               ░░░░░░░░░░ 0/33 (0%)
```

---

## 🧱 Phase 0: Go Backend Fundamentals (Weeks 0-2)

Build small complete labs that make Go backend basics automatic before adding framework and distributed-systems complexity.

| #   | Project                         | Framework | Status         | Started | Completed | Notes                                                     |
| --- | ------------------------------- | --------- | -------------- | ------- | --------- | --------------------------------------------------------- |
| 00a | `net-http-json-api`             | net/http  | 🔲 Not Started | -       | -         | Routing, JSON, status codes, request lifecycle            |
| 00b | `context-errors-config`         | net/http  | 🔲 Not Started | -       | -         | Context cancellation, error wrapping, env config          |
| 00c | `sql-migrations-transactions`   | net/http  | 🔲 Not Started | -       | -         | `database/sql`, PostgreSQL, migrations, transactions      |
| 00d | `testing-benchmarking`          | net/http  | 🔲 Not Started | -       | -         | Table tests, handler tests, integration tests, benchmarks |
| 00e | `docker-observability-shutdown` | net/http  | 🔲 Not Started | -       | -         | Docker Compose, logs, health checks, graceful shutdown    |

**Phase 0 Checkpoint:**

- [ ] Can build a minimal Go HTTP API without a framework
- [ ] Understand request lifecycle, status codes, JSON encoding/decoding
- [ ] Can use `context.Context` for cancellation and timeouts
- [ ] Can connect to PostgreSQL with `database/sql`
- [ ] Can write unit, integration, and handler tests
- [ ] Can run services locally with Docker Compose
- [ ] Can shut down gracefully and expose basic health checks

---

## 🎯 Phase 1: Foundation Modules (Weeks 3-6)

Building standalone, reusable components.

### Week 3

| #   | Project             | Framework | Status         | Started | Completed | Notes                           |
| --- | ------------------- | --------- | -------------- | ------- | --------- | ------------------------------- |
| 01  | `auth-jwt-basics`   | Fiber     | 🔲 Not Started | -       | -         | Registration, login, JWT tokens |
| 02  | `auth-jwt-hardened` | Fiber     | 🔲 Not Started | -       | -         | Token rotation, blacklisting    |

### Week 4

| #   | Project                 | Framework | Status         | Started | Completed | Notes                              |
| --- | ----------------------- | --------- | -------------- | ------- | --------- | ---------------------------------- |
| 03  | `rbac-permissions`      | Fiber     | 🔲 Not Started | -       | -         | Roles, permissions, access control |
| 04  | `smtp-otp-verification` | Fiber     | 🔲 Not Started | -       | -         | Email sending, OTP validation      |

### Week 5-6

| #   | Project               | Framework | Status         | Started | Completed | Notes                                                    |
| --- | --------------------- | --------- | -------------- | ------- | --------- | -------------------------------------------------------- |
| 05  | `file-upload-storage` | Fiber     | 🔲 Not Started | -       | -         | Multipart uploads, S3 integration                        |
| 06  | `api-integration-ai`  | Fiber     | 🔲 Not Started | -       | -         | External APIs, streaming responses                       |
| 06b | `auth-jwt-basics-gin` | **Gin**   | 🔲 Not Started | -       | -         | **Port project 01 to Gin - Learn framework portability** |

**Phase 1 Checkpoint:**

- [ ] Can build REST API from scratch
- [ ] Understand layered architecture
- [ ] Comfortable with GORM and Fiber v3
- [ ] Can implement auth without looking up basics
- [ ] Have 3-4 reusable modules
- [ ] **Understand framework-agnostic patterns (built same project in 2 frameworks)**

---

## 🚀 Phase 2: Scaling Patterns (Weeks 7-12)

Applying modules while learning distributed systems.

### Read Scaling (Weeks 7-8)

| #   | Project                | Status         | Started | Completed | Notes                             |
| --- | ---------------------- | -------------- | ------- | --------- | --------------------------------- |
| 07  | `caching-strategies`   | 🔲 Not Started | -       | -         | Redis, cache-aside, invalidation  |
| 08  | `database-replication` | 🔲 Not Started | -       | -         | Primary-replica replication, read replicas |
| 09  | `indexing-performance` | 🔲 Not Started | -       | -         | Query optimization, explain plans |

### Write Scaling (Weeks 9-10)

| #   | Project              | Status         | Started | Completed | Notes                           |
| --- | -------------------- | -------------- | ------- | --------- | ------------------------------- |
| 10  | `async-writes-queue` | 🔲 Not Started | -       | -         | Message queues, background jobs |
| 11  | `batch-processing`   | 🔲 Not Started | -       | -         | Bulk operations, batching       |
| 12  | `database-sharding`  | 🔲 Not Started | -       | -         | Horizontal partitioning         |

### Real-Time Communication (Weeks 11-12)

| #   | Project                   | Framework | Status         | Started | Completed | Notes                                                   |
| --- | ------------------------- | --------- | -------------- | ------- | --------- | ------------------------------------------------------- |
| 13  | `websockets-realtime`     | Fiber     | 🔲 Not Started | -       | -         | WebSocket connections, pub/sub                          |
| 14  | `sse-notifications`       | Fiber     | 🔲 Not Started | -       | -         | Server-Sent Events                                      |
| 15  | `long-polling`            | Fiber     | 🔲 Not Started | -       | -         | Long polling implementation                             |
| 15b | `websockets-realtime-gin` | **Gin**   | 🔲 Not Started | -       | -         | **Port project 13 to Gin - Compare real-time handling** |

**Phase 2 Checkpoint:**

- [ ] Understand CAP theorem through implementation
- [ ] Can design for read-heavy workloads
- [ ] Can design for write-heavy workloads
- [ ] Understand caching tradeoffs
- [ ] Implemented real-time features
- [ ] **See how WebSockets work across frameworks**

---

## 🛡 Phase 3: Reliability & Advanced Patterns (Weeks 13-18)

### Resilience Patterns (Weeks 13-14)

| #   | Project           | Status         | Started | Completed | Notes                               |
| --- | ----------------- | -------------- | ------- | --------- | ----------------------------------- |
| 16  | `retry-patterns`  | 🔲 Not Started | -       | -         | Exponential backoff, jitter         |
| 17  | `circuit-breaker` | 🔲 Not Started | -       | -         | Failure detection, fallbacks        |
| 18  | `idempotency`     | 🔲 Not Started | -       | -         | Idempotent endpoints                |
| 19  | `self-healing`    | 🔲 Not Started | -       | -         | Health checks, graceful degradation |

### Long-Running Processes (Weeks 15-16)

| #   | Project           | Status         | Started | Completed | Notes                          |
| --- | ----------------- | -------------- | ------- | --------- | ------------------------------ |
| 20  | `message-queues`  | 🔲 Not Started | -       | -         | RabbitMQ/NATS patterns         |
| 21  | `worker-pools`    | 🔲 Not Started | -       | -         | Goroutines, concurrency        |
| 22  | `workflow-engine` | 🔲 Not Started | -       | -         | Temporal-style durable workflows, saga pattern |

### Advanced Architecture (Weeks 17-18)

| #   | Project                | Framework | Status         | Started | Completed | Notes                                             |
| --- | ---------------------- | --------- | -------------- | ------- | --------- | ------------------------------------------------- |
| 23  | `cqrs-pattern`         | Fiber     | 🔲 Not Started | -       | -         | Command/Query separation                          |
| 24  | `microservices-basic`  | Mixed     | 🔲 Not Started | -       | -         | Service communication                             |
| 25  | `production-api-fiber` | **Fiber** | 🔲 Not Started | -       | -         | **CAPSTONE (Fiber)**: Everything combined         |
| 25b | `production-api-gin`   | **Gin**   | 🔲 Not Started | -       | -         | **CAPSTONE (Gin)**: Same app, different framework |

**Phase 3 Checkpoint:**

- [ ] Can design resilient systems
- [ ] Understand distributed systems patterns
- [ ] Can identify failure modes
- [ ] Know when to use advanced patterns
- [ ] Built full production-ready system
- [ ] **Demonstrated framework mastery with dual capstones**

---

## 🎓 Skills Tracker

### Go Language

- [ ] Basic syntax and types
- [ ] Error handling patterns
- [ ] Error wrapping and sentinel/domain errors
- [ ] Interfaces and composition
- [ ] Goroutines and channels
- [ ] Context usage
- [ ] Testing and benchmarking
- [ ] Advanced concurrency patterns

### Web Development

- [ ] `net/http` request lifecycle
- [ ] REST API design
- [ ] HTTP handlers and routing
- [ ] Middleware implementation
- [ ] Request/response handling
- [ ] Authentication & authorization
- [ ] WebSockets
- [ ] Server-Sent Events

### Database

- [ ] `database/sql` basics
- [ ] SQL and PostgreSQL
- [ ] GORM ORM
- [ ] GORM context usage, transactions, and Generics API awareness
- [ ] Migrations
- [ ] Indexing and optimization
- [ ] Transactions
- [ ] Replication
- [ ] Sharding

### System Design

- [ ] Caching strategies
- [ ] Scalability patterns
- [ ] CAP theorem
- [ ] Consistency models
- [ ] Resilience patterns
- [ ] Message queues
- [ ] Event-driven architecture
- [ ] CQRS and Event Sourcing

### DevOps

- [ ] Docker and containerization
- [ ] Docker Compose
- [ ] Environment configuration
- [ ] Logging and monitoring
- [ ] Health checks and graceful shutdown
- [ ] CI/CD basics
- [ ] Kubernetes basics (if applicable)

---

## 📝 Weekly Log

### Week [Number]: [Date Range]

**Projects Started:**

- Project name

**Projects Completed:**

- Project name

**Key Learnings:**

- What did you learn this week?
- What challenges did you face?
- What clicked for you?

**Time Spent:** [hours]

**Next Week Goals:**

- Goal 1
- Goal 2

---

## 🏆 Milestones

- [ ] **Week 1:** First API running
- [ ] **Week 2:** Phase 0 complete - Can build and test a small Go API without a framework
- [ ] **Week 3:** First reusable module started
- [ ] **Week 6:** Phase 1 complete - Have solid foundation modules
- [ ] **Week 8:** Implemented caching and saw performance gains
- [ ] **Week 10:** Built async system with message queues
- [ ] **Week 12:** Phase 2 complete - Understand scaling
- [ ] **Week 14:** Implemented circuit breaker and saw it work
- [ ] **Week 16:** Built complex workflow system
- [ ] **Week 18:** Phase 3 complete - Production-ready skills
- [ ] **Week 19:** Capstone project deployed

---

## 📊 Statistics

**Total Time Invested:** 0 hours
**Projects Completed:** 0 / 33
**Modules Created:** 0
**Lines of Go Code Written:** 0
**Frameworks Mastered:** 0 / 2 (Fiber, Gin)
**Issues Encountered & Solved:** 0
**GitHub Stars (if public):** 0

---

## 🎯 Monthly Goals

### Month 1 (Weeks 0-4)

- [ ] Complete Phase 0 (Go backend fundamentals)
- [ ] Start Phase 1 foundation modules
- [ ] Comfortable with Go syntax, `net/http`, context, and testing
- [ ] Can build REST API independently without a framework

### Month 2 (Weeks 5-8)

- [ ] Complete Phase 1 (Foundation)
- [ ] Create 3-4 reusable modules
- [ ] Learn caching patterns
- [ ] Implement database replication
- [ ] Start understanding distributed systems through implementation

### Month 3 (Weeks 9-12)

- [ ] Build async processing system
- [ ] Implement real-time features
- [ ] Complete Phase 2 (Scaling)
- [ ] Compare at least one feature across Fiber and Gin

### Month 4 (Weeks 13-16)

- [ ] Add resilience patterns
- [ ] Handle failures gracefully
- [ ] Build message queue systems
- [ ] Build workflow/worker systems

### Month 5 (Weeks 17-19)

- [ ] Complete all projects
- [ ] Build capstone project
- [ ] Solidify system design knowledge
- [ ] Portfolio ready for interviews

---

## 🔄 Retrospective Template

After each phase, reflect on:

### What Went Well?

- Successes
- Smooth implementations
- Quick understanding

### What Was Challenging?

- Difficult concepts
- Time-consuming tasks
- Confusing patterns

### What Would You Do Differently?

- Alternative approaches
- Better strategies
- Time management

### Key Takeaways

- Most important lessons
- Unexpected insights
- Skills gained

---

## 📈 Progress Updates

### Update [Date]

**Current Status:**

- Phase: X
- Project: Y
- Completion: Z%

**This Week:**

- Completed: [projects]
- Learning highlights: [key lessons]
- Challenges faced: [obstacles]

**Next Week:**

- Starting: [projects]
- Focus areas: [topics]

---

## 🎓 Interview Readiness

As you progress, track your ability to:

- [ ] Explain your code and decisions confidently
- [ ] Discuss tradeoffs of different approaches
- [ ] Draw system architecture diagrams
- [ ] Talk about real challenges you solved
- [ ] Demonstrate deep understanding (not just surface knowledge)
- [ ] Show production-ready thinking
- [ ] Explain Go idioms and why they exist
- [ ] Discuss distributed systems concepts with examples

---

## 💡 Tips for Success

1. **One project at a time** - Don't rush
2. **Document learnings** - Write LEARNINGS.md after each project
3. **Break things** - Learn by experimentation
4. **Ask why** - Understand, don't just copy
5. **Review regularly** - Look back at earlier projects
6. **Share your progress** - Twitter, blog, GitHub
7. **Take breaks** - Avoid burnout
8. **Celebrate wins** - Acknowledge progress

---

**Remember:** This is a marathon, not a sprint. Quality over speed. Understanding over completion.

Update this file regularly to track your amazing progress! 🚀
