# Backend Roadmap: Architecture, Structure, Communication, Protocols

A path from junior basics to mastery, built for a mentor and a junior working together.

**How to use it**

- Tick a topic only when the junior can explain it out loud without notes.
- Every phase ends with a practice task (junior builds it) and a review session (mentor asks the questions).
- "Mentor refresh" items are deeper cuts for the mentor to revisit while the junior works on the phase.
- Examples assume Python, Django, PostgreSQL, Kafka and Docker, but the ideas are stack-independent.

## Progress overview

| Phase | Theme | Rough duration | Done |
|---|---|---|---|
| 0 | Foundations | 3-4 weeks | [ ] |
| 1 | Structure inside one service | 4-6 weeks | [ ] |
| 2 | Protocols under the hood | 4 weeks | [ ] |
| 3 | Synchronous communication and API design | 5-6 weeks | [ ] |
| 4 | Asynchronous communication | 6-8 weeks | [ ] |
| 5 | Data and consistency | 6-8 weeks | [ ] |
| 6 | System-level architecture | Ongoing | [ ] |

Durations assume part-time study next to regular work.

---

## Phase 0: Foundations

**Goal:** the junior understands what happens between a client sending a request and a response coming back.

### Topics

- [ ] Client-server model, request/response cycle
- [ ] What a process, a thread and a port are
- [ ] IP addresses, DNS lookup, what `localhost` means
- [ ] HTTP basics: methods, status codes, headers, body
- [ ] JSON as a data format
- [ ] Relational basics: tables, keys, joins, indexes
- [ ] Environment variables and configuration
- [ ] Git workflow: branches, pull requests, code review
- [ ] Docker basics: image, container, volume, compose
- [ ] Reading logs and stack traces

### Practice task

- [ ] Build a small CRUD API (e.g. a notes service) with Django, PostgreSQL and docker-compose
- [ ] Call every endpoint with `curl` and explain each line of the output

### Review session

- [ ] "What happens when I type a URL and press Enter?"
- [ ] "What is the difference between 401, 403 and 404?"
- [ ] "Why do we need an index, and what does it cost?"

### Mentor refresh

- [ ] Re-read the HTTP semantics spec (RFC 9110) sections on methods and status codes

---

## Phase 1: Structure inside one service

**Goal:** the junior can organise code so that business rules do not depend on the framework or the database.

### Topics

- [ ] Separation of concerns, cohesion and coupling
- [ ] Layered architecture: presentation, application, domain, infrastructure
- [ ] Dependency direction and dependency inversion
- [ ] Hexagonal architecture (ports and adapters)
- [ ] Where business logic should live in a Django project (and where it should not)
- [ ] Repository and unit-of-work patterns
- [ ] Domain model basics: entities, value objects, aggregates
- [ ] Bounded contexts and the modular monolith
- [ ] Error handling strategy: domain errors vs. infrastructure errors
- [ ] Testing pyramid: unit, integration, end-to-end
- [ ] Configuration and secrets management (twelve-factor app)

### Practice task

- [ ] Refactor the Phase 0 service so domain logic has no Django imports
- [ ] Draw the module boundaries and enforce them with import-linter
- [ ] Write unit tests for the domain that run without a database

### Review session

- [ ] "Show me one place where a dependency points the wrong way, and fix it."
- [ ] "What would have to change if we swapped PostgreSQL for MongoDB?"
- [ ] "When is this much structure overkill?"

### Reading

- [ ] *Architecture Patterns with Python* (Percival and Gregory), parts 1 and 2

### Mentor refresh

- [ ] Strategic DDD: context mapping, anti-corruption layers
- [ ] Review one of your own production services against these rules

---

## Phase 2: Protocols under the hood

**Goal:** the junior can reason about what is on the wire and why a connection is slow, stuck or refused.

### Topics

- [ ] OSI and TCP/IP layer models (as a mental map, not memorisation)
- [ ] TCP: three-way handshake, flow control, keep-alive, connection reset
- [ ] UDP and when it is the better choice
- [ ] TLS 1.3: handshake, certificates, certificate chains
- [ ] Mutual TLS (mTLS)
- [ ] DNS in depth: record types, TTL, caching, resolution path
- [ ] HTTP/1.1: persistent connections, head-of-line blocking
- [ ] HTTP/2: multiplexing, streams, header compression
- [ ] HTTP/3 and QUIC: why it moved to UDP
- [ ] WebSocket: upgrade handshake, framing, heartbeats
- [ ] Server-Sent Events and long polling
- [ ] Serialization: JSON vs. Protobuf vs. Avro vs. MessagePack
- [ ] Schema evolution: backward and forward compatibility
- [ ] Reverse proxies and load balancers: L4 vs. L7

### Practice task

- [ ] Capture an HTTP/1.1, an HTTP/2 and a WebSocket session in Wireshark and annotate them
- [ ] Put nginx in front of the service with TLS and explain every directive used
- [ ] Encode the same payload in JSON and Protobuf and compare size and speed

### Review session

- [ ] "A request hangs for 30 seconds and then fails. List the places it could be stuck."
- [ ] "Why does HTTP/2 not fully solve head-of-line blocking?"
- [ ] "Which field changes break a Protobuf consumer, and which are safe?"

### Reading

- [ ] *High Performance Browser Networking* (Grigorik), networking and HTTP chapters

### Mentor refresh

- [ ] QUIC connection migration and 0-RTT trade-offs
- [ ] TLS session resumption and certificate rotation in your own setup

---

## Phase 3: Synchronous communication and API design

**Goal:** the junior can design an API contract others can rely on and make calls between services that fail safely.

### Topics

- [ ] REST: resources, representations, statelessness
- [ ] Correct use of methods, status codes and caching headers (ETag, Cache-Control)
- [ ] Safe and idempotent methods; idempotency keys for POST
- [ ] Pagination styles: offset, cursor, keyset
- [ ] Filtering, sorting and partial responses
- [ ] Error format conventions (e.g. Problem Details, RFC 9457)
- [ ] API versioning strategies and deprecation
- [ ] Contract-first design with OpenAPI
- [ ] gRPC: unary and streaming calls, deadlines, status codes
- [ ] GraphQL: what it solves, N+1 problem, when not to use it
- [ ] Authentication: sessions, JWT, API keys
- [ ] OAuth2 and OpenID Connect flows
- [ ] Timeouts on every outbound call
- [ ] Retries with exponential backoff and jitter
- [ ] Circuit breakers and bulkheads
- [ ] Rate limiting and backpressure
- [ ] Sync vs. async I/O in Python: WSGI vs. ASGI, the event loop

### Practice task

- [ ] Write an OpenAPI spec first, then implement the service against it
- [ ] Expose the same capability over REST and gRPC
- [ ] Add a second service that calls the first with timeouts, retries and a circuit breaker
- [ ] Kill the first service and show the second degrading gracefully

### Review session

- [ ] "Why is retrying a POST dangerous, and how do we make it safe?"
- [ ] "A client needs a field renamed. Walk me through doing it without breaking anyone."
- [ ] "When would you pick gRPC over REST, and when not?"

### Reading

- [ ] *API Design Patterns* (Geewax), selected chapters
- [ ] *Release It!* (Nygard), stability patterns

### Mentor refresh

- [ ] Token exchange and propagation across service hops
- [ ] Load-shedding strategies and adaptive concurrency limits

---

## Phase 4: Asynchronous communication

**Goal:** the junior can build message-driven flows that lose nothing and do nothing twice.

### Topics

- [ ] Why async: decoupling, buffering, load levelling
- [ ] Message queue vs. log: RabbitMQ or NATS vs. Kafka
- [ ] Commands, events and documents as message types
- [ ] Kafka core: topics, partitions, offsets, consumer groups
- [ ] Partition keys and ordering guarantees
- [ ] Consumer group rebalancing
- [ ] Delivery semantics: at-most-once, at-least-once, effectively-once
- [ ] Idempotent consumers and deduplication
- [ ] Dual-write problem and the transactional outbox
- [ ] Change data capture
- [ ] Dead-letter queues and retry topics
- [ ] Poison messages and how to handle them
- [ ] Schema registry and message versioning
- [ ] Sagas: orchestration vs. choreography
- [ ] Compensating actions
- [ ] CQRS
- [ ] Event sourcing: benefits and real costs
- [ ] Task queues for background work (e.g. Celery) vs. event streaming
- [ ] Consumer lag and how to monitor it

### Practice task

- [ ] Build a two-service flow over Kafka (e.g. order placed, then payment reserved)
- [ ] Implement the outbox pattern on the producer side
- [ ] Crash the consumer mid-message and prove no loss and no duplicate side effects
- [ ] Add a dead-letter topic and a replay procedure

### Review session

- [ ] "Why is exactly-once delivery a misleading phrase?"
- [ ] "Two events for the same entity arrive out of order. How could that happen and what do we do?"
- [ ] "When is a plain HTTP call the better choice than an event?"

### Reading

- [ ] *Enterprise Integration Patterns* (Hohpe and Woolf), messaging chapters
- [ ] *Designing Event-Driven Systems* (Stopford)

### Mentor refresh

- [ ] Kafka transactions and idempotent producer internals
- [ ] Rebalance protocols (eager vs. cooperative) and their effect on your consumers

---

## Phase 5: Data and consistency

**Goal:** the junior can choose storage and consistency guarantees deliberately instead of by habit.

### Topics

- [ ] ACID and what each letter really promises
- [ ] Isolation levels and their anomalies (dirty read, non-repeatable read, phantom, write skew)
- [ ] Optimistic vs. pessimistic locking
- [ ] Index internals: B-tree, hash, GIN; reading a query plan
- [ ] Connection pooling
- [ ] Zero-downtime schema migrations
- [ ] Replication: leader-follower, multi-leader, leaderless
- [ ] Replication lag and read-your-writes
- [ ] Partitioning and sharding strategies
- [ ] CAP and PACELC
- [ ] Consistency models: strong, causal, eventual
- [ ] Choosing a store: relational, document, key-value, object, search
- [ ] Caching patterns: cache-aside, write-through, write-behind
- [ ] Cache invalidation and stampede protection
- [ ] Distributed locks and why they are fragile
- [ ] Consensus at a conceptual level (Raft)

### Practice task

- [ ] Reproduce a write-skew anomaly in PostgreSQL, then fix it
- [ ] Add a Redis cache to a slow endpoint with a clear invalidation rule
- [ ] Run a schema change on a table under load without downtime

### Review session

- [ ] "Which isolation level does our database use by default, and what can go wrong with it?"
- [ ] "The cache and the database disagree. How did that happen?"
- [ ] "When would you reach for a document store instead of PostgreSQL?"

### Reading

- [ ] *Designing Data-Intensive Applications* (Kleppmann)

### Mentor refresh

- [ ] Serializable snapshot isolation internals
- [ ] Re-evaluate one past storage decision with what you know now

---

## Phase 6: System-level architecture

**Goal:** the junior can take part in architecture decisions, argue trade-offs and document them.

### Topics

- [ ] Monolith, modular monolith and microservices: honest trade-offs
- [ ] How to find service boundaries
- [ ] API gateway and Backend-for-Frontend
- [ ] Service discovery
- [ ] Service mesh: what it gives and what it costs
- [ ] Horizontal vs. vertical scaling, stateless services
- [ ] Load balancing algorithms and health checks
- [ ] Multi-tenancy models
- [ ] Zero-trust between services
- [ ] Secrets management and rotation
- [ ] Structured logging and correlation IDs
- [ ] Metrics: RED and USE methods
- [ ] Distributed tracing with OpenTelemetry
- [ ] SLIs, SLOs and error budgets
- [ ] Deployment strategies: rolling, blue-green, canary
- [ ] Feature flags
- [ ] Graceful shutdown and startup probes
- [ ] Capacity planning and load testing
- [ ] C4 model for diagrams
- [ ] Architecture Decision Records (ADRs)
- [ ] Evolutionary architecture and fitness functions

### Practice task

- [ ] Draw C4 context and container diagrams for a real system
- [ ] Write an ADR for a real decision, including rejected alternatives
- [ ] Add tracing across the Phase 4 services and follow one request end to end
- [ ] Define one SLO and build the dashboard for it
- [ ] Lead a design review, with the mentor as the sceptical reviewer

### Review session

- [ ] "Give me three reasons not to split this into microservices."
- [ ] "Something is slow in production. Where do you look first, second, third?"
- [ ] "What would have to be true for this decision to be wrong?"

### Reading

- [ ] *Building Microservices*, 2nd edition (Newman)
- [ ] *Software Architecture: The Hard Parts* (Ford, Richards, Sadalage, Dehghani)
- [ ] *Fundamentals of Software Architecture* (Richards and Ford)

### Mentor refresh

- [ ] Read two public post-mortems per month and discuss them together
- [ ] Hand one real architecture decision to the junior and review, not rewrite, the result

---

## Mastery signals

The roadmap is working when the junior can do these without prompting.

- [ ] Explains a trade-off instead of naming a "best practice"
- [ ] Predicts failure modes before writing code
- [ ] Debugs across service boundaries using traces and logs
- [ ] Writes a design document others can act on
- [ ] Reviews someone else's design and finds a real problem
- [ ] Teaches one of these phases to the next junior

## Session log

| Date | Phase | What was covered | Open questions |
|---|---|---|---|
| | | | |
