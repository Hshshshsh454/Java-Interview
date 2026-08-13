# Microservices Architecture

**Difficulty:** ⭐ Advanced → Expert
**Interview Relevance:** ⭐⭐⭐⭐⭐
**Category:** System Design / Distributed Systems / Backend / Spring Boot

> **Core idea:** Microservices is not simply "breaking a monolith into many APIs." It is an architectural approach where a system is decomposed into **independently deployable services aligned around business capabilities**, with explicit boundaries and communication between them.

---

# 0. Interview Relevance & Question Mapping

| Topic                           | Priority |
| ------------------------------- | -------: |
| Microservices fundamentals      |    ⭐⭐⭐⭐⭐ |
| Monolith vs Microservices       |    ⭐⭐⭐⭐⭐ |
| Service boundaries              |    ⭐⭐⭐⭐⭐ |
| Database per service            |    ⭐⭐⭐⭐⭐ |
| API communication               |    ⭐⭐⭐⭐⭐ |
| REST vs messaging               |    ⭐⭐⭐⭐⭐ |
| Service discovery               |     ⭐⭐⭐⭐ |
| API Gateway                     |    ⭐⭐⭐⭐⭐ |
| Load balancing                  |    ⭐⭐⭐⭐⭐ |
| Fault tolerance                 |    ⭐⭐⭐⭐⭐ |
| Circuit breaker                 |    ⭐⭐⭐⭐⭐ |
| Distributed transactions / Saga |    ⭐⭐⭐⭐⭐ |
| Event-driven architecture       |    ⭐⭐⭐⭐⭐ |
| Kafka                           |     ⭐⭐⭐⭐ |
| Idempotency                     |    ⭐⭐⭐⭐⭐ |
| Observability                   |    ⭐⭐⭐⭐⭐ |
| Security                        |    ⭐⭐⭐⭐⭐ |
| Deployment / containers         |     ⭐⭐⭐⭐ |
| Kubernetes                      |     ⭐⭐⭐⭐ |
| CQRS / Event Sourcing           |     ⭐⭐⭐⭐ |

### ⭐ Frequently Asked

* What are microservices?
* Monolith vs microservices?
* How do you identify service boundaries?
* Why database per service?
* How do microservices communicate?
* REST vs Kafka?
* What is API Gateway?
* What is service discovery?
* What happens if one service goes down?
* How do you handle distributed transactions?
* What is Saga?
* What is Circuit Breaker?
* What is the Outbox Pattern?
* How do you maintain consistency?
* How do you secure microservices?
* How do you monitor microservices?
* How do you prevent cascading failures?

---

# 1. Precise Definition

### Interview-ready answer

> **Microservices architecture is an architectural style in which an application is decomposed into small, independently deployable services organized around business capabilities, where each service owns its logic and typically its data, and services communicate through well-defined APIs or asynchronous messaging.**

The important words are:

```text
Business capability
Independent deployment
Service ownership
Loose coupling
Explicit communication
```

---

# 2. Why Do Microservices Exist?

Consider a large monolith:

```text
                 E-Commerce Monolith
┌─────────────────────────────────────────────┐
│                                             │
│ User │ Product │ Order │ Payment │ Inventory│
│                                             │
└─────────────────────────────────────────────┘
                      ↓
                  Database
```

As the application grows:

```text
Problems
│
├── Large codebase
├── Difficult deployments
├── Tight coupling
├── Independent scaling is difficult
├── One failure can affect large areas
├── Team ownership becomes difficult
└── Long release cycles
```

Microservices split the system along meaningful business boundaries:

```text
User Service
Product Service
Order Service
Payment Service
Inventory Service
Notification Service
```

---

# 3. Microservices Architecture

A typical architecture:

```text
                         Clients
                            │
                            ↓
                     API Gateway
                            │
          ┌─────────────────┼─────────────────┐
          ↓                 ↓                 ↓
     User Service      Order Service     Product Service
          │                 │                 │
          ↓                 ↓                 ↓
      User DB           Order DB         Product DB
                            │
                    ┌───────┴────────┐
                    ↓                ↓
               Payment Service   Inventory Service
                    │                │
                    ↓                ↓
               Payment DB       Inventory DB
```

For asynchronous communication:

```text
Order Service
      ↓
    Kafka
   ↙  ↓  ↘
Payment Inventory Notification
```

---

# 4. Monolith vs Microservices

## Monolith

```text
             Application
┌──────────────────────────────┐
│ User                         │
│ Product                      │
│ Order                        │
│ Payment                      │
│ Inventory                    │
└──────────────────────────────┘
             ↓
          Database
```

## Microservices

```text
User ─────→ User DB

Order ────→ Order DB

Payment ──→ Payment DB

Inventory → Inventory DB
```

### Comparison

| Monolith                      | Microservices                    |
| ----------------------------- | -------------------------------- |
| Single deployable unit        | Multiple deployable units        |
| Simple initially              | Operationally complex            |
| Local calls                   | Network calls                    |
| Easier transactions           | Distributed transactions         |
| Easier debugging              | Distributed debugging            |
| Shared database often common  | Data ownership usually separated |
| Scale whole application       | Scale services independently     |
| Lower infrastructure overhead | Higher infrastructure overhead   |

### Important

> **Microservices are not automatically better than a monolith.**

For a small application, a modular monolith can be a much better architecture.

---

# 5. Service Boundary

⭐ **One of the most important interview topics**

The biggest design question is:

> **Where should one service end and another begin?**

Do not split based only on technical layers.

Bad:

```text
UserController Service
UserDatabase Service
UserValidation Service
```

Better:

```text
Order Service
Payment Service
Inventory Service
```

because these represent business capabilities.

---

# 6. Domain-Driven Design

Microservice boundaries are often informed by **Domain-Driven Design (DDD)**.

Example e-commerce domain:

```text
E-Commerce
│
├── Catalog
├── Ordering
├── Payment
├── Inventory
├── Shipping
└── Customer
```

Potential bounded contexts:

```text
Catalog Context
Order Context
Payment Context
Inventory Context
Shipping Context
```

A bounded context defines a meaningful domain boundary where terminology and business rules are internally consistent.

---

# 7. High Cohesion + Low Coupling

Good service:

```text
Order Service
│
├── Order logic
├── Order state
├── Order validation
└── Order persistence
```

It should not require direct knowledge of every internal detail of:

```text
Payment
Inventory
Shipping
```

Instead:

```text
Order
 ↓ API/Event
Payment
```

### Goal

```text
High Cohesion
      +
Low Coupling
```

---

# 8. Database per Service

⭐ **Frequently Asked**

A common microservices principle is:

> **Each service owns its data and controls access to its database/schema.**

Example:

```text
Order Service
      ↓
   Order DB

Payment Service
      ↓
  Payment DB

Inventory Service
      ↓
 Inventory DB
```

Avoid:

```text
                 Shared DB
              /     |      \
          Order   Payment  Inventory
```

because services become tightly coupled through database tables.

---

# 9. Why Database per Service?

Suppose Payment Service directly reads:

```sql
SELECT *
FROM orders;
```

Now Payment depends on:

```text
Order schema
Order table
Order DB
```

Changing Order's schema could break Payment.

Instead:

```text
Payment
   ↓
Order API/Event
```

This preserves ownership.

---

# 10. Does Every Microservice Need a Separate Physical Database?

**Not necessarily.**

The architectural principle is primarily **data ownership**, not "one physical database server per service."

For example:

```text
Same PostgreSQL cluster
├── order schema
├── payment schema
└── inventory schema
```

can be a transitional or practical architecture if access boundaries are enforced.

But:

```text
Payment Service
directly queries
Order tables
```

still violates service ownership.

---

# 11. Communication Between Microservices

Two major approaches:

```text
Synchronous
Asynchronous
```

### Synchronous

```text
Order
  ↓ HTTP
Payment
  ↓
Response
```

Technologies:

```text
REST
gRPC
```

### Asynchronous

```text
Order
  ↓
Kafka
  ↓
Payment
```

Technologies:

```text
Kafka
RabbitMQ
SQS
```

---

# 12. REST vs Messaging

| REST/gRPC                      | Messaging                  |
| ------------------------------ | -------------------------- |
| Immediate response             | Asynchronous               |
| Strong request/response model  | Event-driven               |
| Easier initially               | More complex               |
| Caller depends on availability | Consumer can process later |
| Good for queries               | Good for events/workflows  |
| Can increase coupling          | Decouples timing           |

Example:

### REST

```text
Order → Payment
"Authorize payment"
```

### Event

```text
Order
 ↓
OrderCreated
 ↓
Kafka
 ↓
Payment
```

---

# 13. Synchronous Communication Problem

Suppose:

```text
Order
 ↓
Payment
 ↓
Fraud
 ↓
Bank
```

If every call is synchronous:

```text
Order
  ↓
Payment
  ↓
Fraud
  ↓
Bank
```

Latency accumulates.

If Bank becomes slow:

```text
Bank slow
   ↓
Fraud waits
   ↓
Payment waits
   ↓
Order waits
   ↓
User waits
```

This can create **cascading latency and failures**.

---

# 14. Asynchronous Communication

Instead:

```text
Order
 ↓
Event
 ↓
Kafka
 ↓
Payment
```

The Order service can acknowledge the request while downstream processing continues, if the business workflow allows eventual completion.

This provides:

* Decoupling
* Buffering
* Better resilience
* Independent scaling

But introduces:

* Eventual consistency
* Duplicate events
* Ordering challenges
* Debugging complexity

---

# 15. API Gateway

⭐ **Must-Know**

An API Gateway is a common entry point for clients.

```text
                    Client
                       ↓
                 API Gateway
                 /     |     \
                ↓      ↓      ↓
             User    Order   Product
```

Responsibilities may include:

* Routing
* Authentication
* Authorization
* Rate limiting
* TLS termination
* Request aggregation
* API versioning
* Observability

---

# 16. API Gateway vs Load Balancer

These are not identical.

### Load Balancer

Primarily distributes traffic:

```text
Load Balancer
 ├── Server 1
 ├── Server 2
 └── Server 3
```

### API Gateway

Provides API-level capabilities:

```text
Gateway
 ├── Authentication
 ├── Routing
 ├── Rate limiting
 ├── Transformation
 └── Aggregation
```

They can coexist:

```text
Client
 ↓
Load Balancer
 ↓
API Gateway
 ↓
Services
```

---

# 17. Service Discovery

In dynamic environments, service instances can change.

Example:

```text
Order Service
 ├── 10.0.0.5
 ├── 10.0.0.6
 └── 10.0.0.7
```

How does Payment know where Order is?

A service discovery mechanism can maintain:

```text
Service Name
      ↓
Available Instances
```

Example:

```text
order-service
      ↓
10.0.0.5
10.0.0.6
10.0.0.7
```

In Kubernetes, service discovery is commonly provided through Kubernetes Services and DNS.

---

# 18. Load Balancing

Suppose:

```text
Order Service
├── Instance 1
├── Instance 2
└── Instance 3
```

Requests can be distributed:

```text
Request
 ↓
Load Balancer
 ├── Instance 1
 ├── Instance 2
 └── Instance 3
```

Common strategies:

```text
Round Robin
Least Connections
Weighted
Hash-based
```

---

# 19. Fault Tolerance

Microservices introduce network failure.

Assume:

```text
Order
 ↓
Payment
```

Payment is down.

A robust system should not allow Order to hang indefinitely.

Use:

```text
Timeout
Retry
Circuit Breaker
Fallback
Bulkhead
```

---

# 20. Timeout

Always establish bounded waiting for remote calls.

```text
Order
 ↓
Payment
 ↓
No response
 ↓
Timeout
```

Without timeout:

```text
Request
 ↓
Waiting
 ↓
Waiting
 ↓
Waiting
 ↓
Thread/resource exhaustion
```

---

# 21. Retry

Transient failure:

```text
Order
 ↓
Payment
 ↓
Network failure
```

Retry:

```text
Attempt 1
 ↓
Failure
 ↓
Backoff
 ↓
Attempt 2
 ↓
Success
```

Use:

```text
Exponential Backoff
+
Jitter
```

to avoid synchronized retry storms.

---

# 22. Circuit Breaker

⭐ **Frequently Asked**

Circuit breaker protects a service from repeatedly calling a failing dependency.

```text
CLOSED
   ↓ repeated failures
OPEN
   ↓ wait
HALF_OPEN
   ↓ successful test
CLOSED
```

### Closed

Requests flow normally.

### Open

Requests fail fast or use fallback.

### Half-open

Limited requests test recovery.

---

# 23. Bulkhead Pattern

Bulkheads isolate resources so one dependency cannot consume everything.

Example:

```text
Application
│
├── Payment Thread Pool
│
├── Notification Thread Pool
│
└── Search Thread Pool
```

If Notification becomes slow:

```text
Notification failure
      ↓
Notification resources exhausted
      ↓
Payment remains operational
```

This is analogous to compartments in a ship.

---

# 24. Distributed Transactions

⭐ **Major Interview Topic**

Suppose placing an order requires:

```text
Order
 ↓
Payment
 ↓
Inventory
 ↓
Shipping
```

These services may have separate databases.

You cannot casually rely on one local database transaction across all of them.

Problem:

```text
Order → SUCCESS
Payment → SUCCESS
Inventory → FAILURE
```

Now what?

---

# 25. Saga Pattern

The Saga pattern breaks a distributed business transaction into local transactions.

Example:

```text
Create Order
     ↓
Reserve Payment
     ↓
Reserve Inventory
     ↓
Create Shipment
```

If Inventory fails:

```text
Inventory failed
      ↓
Compensate Payment
      ↓
Cancel Order
```

Conceptually:

```text
T1 → T2 → T3
          ↓ failure
C2 ← C1
```

where `C` represents compensating actions.

---

# 26. Saga: Choreography

Services react to events.

```text
Order
 ↓ OrderCreated
Kafka
 ↓
Payment
 ↓ PaymentCompleted
Kafka
 ↓
Inventory
```

There is no central coordinator.

### Advantages

* Loose coupling
* Natural event-driven architecture

### Problems

* Complex event flows
* Difficult debugging
* Business workflow can become difficult to understand

---

# 27. Saga: Orchestration

A central orchestrator coordinates the workflow.

```text
             Saga Orchestrator
              /      |       \
             ↓       ↓        ↓
          Order   Payment   Inventory
```

The orchestrator decides:

```text
1. Charge payment
2. Reserve inventory
3. Create shipment
```

If something fails:

```text
Run compensation
```

### Trade-off

More centralized control, but the orchestrator becomes an important component whose design must avoid becoming a monolithic workflow engine.

---

# 28. Outbox Pattern

⭐ **Must-Know**

Suppose Order Service needs to:

```text
1. Save order
2. Publish OrderCreated
```

Potential failure:

```text
DB save
 ↓ SUCCESS

Kafka publish
 ↓ FAILURE
```

Now:

```text
Database = order exists
Kafka = event missing
```

The **Outbox Pattern** addresses this by storing the event in the same local transaction:

```text
Transaction
├── Insert Order
└── Insert Outbox Event
        ↓
      COMMIT
        ↓
Outbox Publisher
        ↓
Kafka
```

This improves reliable event publication.

---

# 29. Idempotency

Microservices often involve retries and duplicate messages.

Example:

```text
Payment Request
 ↓
Processed
 ↓
Response lost
 ↓
Retry
 ↓
Payment again ❌
```

Use:

```text
Idempotency Key
```

Example:

```text
payment-request-id = abc123
```

Server:

```text
abc123 exists?
 ├── Yes → return previous result
 └── No → process
```

---

# 30. Event-Driven Architecture

Instead of:

```text
Order → Payment
```

use:

```text
Order
 ↓
OrderCreated
 ↓
Event Broker
 ├── Payment
 ├── Inventory
 ├── Notification
 └── Analytics
```

Advantages:

* Loose temporal coupling
* Multiple consumers
* Async processing
* Scalability

Challenges:

* Event ordering
* Duplicate delivery
* Schema evolution
* Eventual consistency
* Debugging

---

# 31. Kafka in Microservices

Typical architecture:

```text
Producer
   ↓
Kafka Topic
 ├── Partition 0
 ├── Partition 1
 └── Partition 2
      ↓
Consumer Group
 ├── Consumer 1
 └── Consumer 2
```

Kafka can be used for:

```text
Domain Events
Audit Events
Analytics
Async Workflows
Integration
Data Pipelines
```

---

# 32. Authentication vs Authorization

Microservices should distinguish:

### Authentication

```text
Who are you?
```

### Authorization

```text
What can you access?
```

Typical architecture:

```text
Client
 ↓
Identity Provider
 ↓
Access Token
 ↓
API Gateway
 ↓
Services
```

Services should still enforce authorization where necessary rather than blindly trusting every gateway decision.

---

# 33. JWT in Microservices

A common flow:

```text
Client
 ↓
Login
 ↓
Auth Service / Identity Provider
 ↓
JWT
 ↓
API Gateway
 ↓
Service
```

JWT may contain claims such as:

```json
{
  "sub": "123",
  "roles": ["USER"]
}
```

The system must still validate:

* Signature
* Expiration
* Issuer
* Audience
* Relevant claims

---

# 34. Observability

Microservices make debugging harder because one request can cross many services.

```text
Client
 ↓
Gateway
 ↓
Order
 ↓
Payment
 ↓
Inventory
 ↓
Notification
```

You need:

```text
Logs
Metrics
Distributed Tracing
```

---

# 35. Distributed Tracing

A request should carry correlation/trace context:

```text
Trace ID: ABC123
```

Then:

```text
Gateway
  ↓ ABC123
Order
  ↓ ABC123
Payment
  ↓ ABC123
Inventory
```

You can reconstruct the request path.

Tools commonly used include:

```text
OpenTelemetry
Jaeger
Zipkin
```

---

# 36. Centralized Logging

Without centralized logging:

```text
Order Server 1 → logs
Payment Server 2 → logs
Inventory Server 4 → logs
```

Debugging becomes painful.

Centralized logging:

```text
Services
  ↓
Log Collector
  ↓
Central Log Platform
  ↓
Search / Dashboard
```

---

# 37. Health Checks

Services should expose health information.

Conceptually:

```text
/health
```

A health system may distinguish:

```text
Liveness
Readiness
```

### Liveness

> Is the process alive?

### Readiness

> Can this instance currently receive traffic?

This distinction is especially important in container orchestration.

---

# 38. Deployment

A common modern deployment:

```text
Git
 ↓
CI/CD
 ↓
Build
 ↓
Docker Image
 ↓
Container Registry
 ↓
Kubernetes
 ↓
Microservices
```

Each service can be deployed independently.

Example:

```text
Order v1
 ↓
Order v2
```

while Payment remains:

```text
Payment v1
```

---

# 39. Independent Scaling

Suppose:

```text
Product Service
100 QPS

Order Service
10,000 QPS
```

Microservices can scale independently:

```text
Product
2 instances

Order
20 instances
```

Instead of scaling the entire application.

---

# 40. API Versioning

When an API changes:

```text
/api/v1/orders
/api/v2/orders
```

or through headers/content negotiation.

Important when different clients or services cannot migrate simultaneously.

---

# 41. Distributed Configuration

Services may require:

```text
Database URLs
API keys
Feature flags
Timeouts
Service endpoints
```

Avoid hardcoding environment-specific configuration.

Conceptually:

```text
Configuration Service
        ↓
Microservices
```

Secrets should be managed separately and securely.

---

# 42. Microservices and SOLID

### Single Responsibility

Each service should have a coherent business responsibility.

### Dependency Inversion

Services communicate through stable contracts rather than internal implementation details.

### Open/Closed

Stable APIs/events should allow evolution without breaking consumers.

But:

> SOLID applies primarily to code/module design; microservices operate at a larger architectural boundary.

---

# 43. Microservices and DDD

A useful relationship:

```text
DDD
 ↓
Bounded Context
 ↓
Business Boundary
 ↓
Potential Microservice
```

But:

> **Bounded Context ≠ automatically one microservice.**

A bounded context can initially be implemented as a module inside a monolith and extracted later if justified.

---

# 44. Microservices vs Modular Monolith

⭐ **Advanced Interview Topic**

A modular monolith:

```text
                Application
┌──────────────────────────────┐
│ User Module                  │
│ Order Module                 │
│ Payment Module               │
│ Inventory Module             │
└──────────────────────────────┘
```

has strong internal boundaries but one deployment unit.

This can provide:

* Simpler deployment
* Local function calls
* Easier transactions
* Lower operational complexity

while preserving the possibility of later service extraction.

### Strong architectural principle

> **Start with strong modular boundaries; introduce distributed systems complexity only when the benefits justify it.**

---

# 45. Microservices Failure Model

Assume:

```text
Service A
   ↓
Service B
   ↓
Service C
```

Failures can occur at every boundary:

```text
Network failure
Timeout
Duplicate request
Duplicate event
Out-of-order event
Service crash
Database failure
Message broker failure
Partial deployment
Schema incompatibility
```

Therefore microservices require explicit failure handling.

---

# 46. Production Architecture

A realistic architecture:

```text
                         Internet
                            │
                            ↓
                      CDN / WAF
                            │
                            ↓
                     Load Balancer
                            │
                            ↓
                       API Gateway
                            │
       ┌────────────────────┼────────────────────┐
       ↓                    ↓                    ↓
   User Service        Order Service       Product Service
       │                    │                    │
    User DB              Order DB           Product DB
                            │
                            ↓
                         Kafka
                  ┌─────────┼─────────┐
                  ↓         ↓         ↓
              Payment   Inventory  Notification
                 │          │
             Payment DB  Inventory DB

       ┌────────────────────────────────────────┐
       │ Observability: Logs + Metrics + Traces │
       └────────────────────────────────────────┘
```

---

# 47. Bad Architecture

❌ Avoid:

```text
                 API Gateway
                      ↓
             ┌────────┴────────┐
             ↓                 ↓
        Service A          Service B
             │                 │
             └────────┬────────┘
                      ↓
                Shared Database
```

where every service freely reads and writes every table.

This creates:

```text
Tight coupling
 ↓
Schema coupling
 ↓
Deployment coupling
 ↓
Microservices become distributed monolith
```

---

# 48. Distributed Monolith

⭐ **Expert Interview Topic**

A distributed monolith looks like microservices:

```text
A → B → C → D
```

but behaves like a monolith because:

* Services must all deploy together
* Shared database
* Excessive synchronous calls
* Tight contracts
* Strong runtime coupling
* One service cannot operate independently

### Key insight

> **Having many services does not mean you have a good microservices architecture.**

---

# 49. Microservices Trade-Offs

### Advantages

```text
Independent deployment
Independent scaling
Team autonomy
Fault isolation
Technology flexibility
Business-aligned ownership
```

### Disadvantages

```text
Network complexity
Distributed transactions
Eventual consistency
Operational overhead
Observability complexity
Security complexity
Deployment complexity
Higher infrastructure cost
```

---

# 50. Common Interview Traps

### ❌ "Each microservice must have its own database server."

Not necessarily.

The important concept is **data ownership and isolation**, not necessarily one physical DB server.

---

### ❌ "Microservices eliminate coupling."

No.

They **move coupling from in-process code dependencies to explicit network/API/event contracts**.

---

### ❌ "Kafka guarantees exactly-once business processing."

Not automatically.

Even when infrastructure provides delivery/processing guarantees, application-level idempotency and transactional semantics still matter.

---

### ❌ "Microservices always improve scalability."

No.

Poorly designed services can actually reduce performance because of excessive network calls.

---

### ❌ "Every service should communicate through REST."

No.

Use:

```text
REST/gRPC
+
Messaging/events
```

based on communication requirements.

---

# 51. Interviewer Follow-Up Chain

```text
What are microservices?
       ↓
Why use microservices?
       ↓
Monolith vs microservices?
       ↓
How do you define service boundaries?
       ↓
What is DDD?
       ↓
What is bounded context?
       ↓
Should every service have a separate database?
       ↓
How do services communicate?
       ↓
REST vs Kafka?
       ↓
What is API Gateway?
       ↓
What is service discovery?
       ↓
What happens when Payment Service fails?
       ↓
Timeout?
       ↓
Retry?
       ↓
Circuit Breaker?
       ↓
How do you handle distributed transactions?
       ↓
Saga?
       ↓
Choreography vs orchestration?
       ↓
Outbox Pattern?
       ↓
Idempotency?
       ↓
How do you maintain consistency?
       ↓
How do you monitor the system?
       ↓
How do you secure it?
       ↓
How do you prevent cascading failures?
       ↓
Can your architecture become a distributed monolith?
       ↓
Would you actually choose microservices?
       ↓
Why not a modular monolith?
```

---

# 52. Common Candidate Mistakes

### Weak

> "Microservices are small services."

### Strong

> "Microservices are independently deployable services organized around cohesive business capabilities, with explicit communication and ownership boundaries."

---

### Weak

> "Use Kafka for communication."

### Strong

> "I would use synchronous communication where the caller requires an immediate response and asynchronous messaging where temporal decoupling, buffering, or event-driven processing is beneficial."

---

### Weak

> "Use Saga because microservices don't support transactions."

### Strong

> "A local database transaction cannot automatically provide atomicity across independently owned service databases. Saga coordinates local transactions and compensating actions to achieve business-level consistency."

---

# 53. 30-Second Revision

```text
MICROSERVICES
│
├── Business Boundaries
│       ↓
│   Bounded Contexts
│
├── Independent Deployment
│
├── Service Ownership
│
├── Database Ownership
│
├── Communication
│   ├── REST
│   ├── gRPC
│   └── Kafka
│
├── Resilience
│   ├── Timeout
│   ├── Retry
│   ├── Circuit Breaker
│   └── Bulkhead
│
├── Distributed Consistency
│   ├── Saga
│   ├── Outbox
│   └── Idempotency
│
└── Operations
    ├── Logs
    ├── Metrics
    ├── Tracing
    └── CI/CD
```

### Golden mental model

```text
Business Capability
       ↓
Service Boundary
       ↓
Data Ownership
       ↓
API / Event Contract
       ↓
Independent Deployment
       ↓
Independent Scaling
       ↓
Failure Isolation
```

---

# 54. Master Interview Test

Answer without looking back:

1. What are microservices?
2. Why would you choose microservices over a monolith?
3. Microservices vs modular monolith?
4. How do you identify service boundaries?
5. What is a bounded context?
6. Why is database-per-service commonly recommended?
7. REST vs gRPC vs Kafka?
8. **What happens when a synchronous dependency fails, and how would you prevent cascading failures?**
9. **Design an order-processing system using Order, Payment, Inventory, and Notification services. Explain service boundaries, database ownership, REST vs Kafka communication, Saga, Outbox, idempotency, retries, circuit breakers, observability, and consistency.**
10. **Your microservices system has 20 services but requires 15 synchronous calls to complete one user request and all services share one PostgreSQL database. Diagnose why this is effectively a distributed monolith and redesign the architecture.**
