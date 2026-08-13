# System Design

**Difficulty:** ⭐ Advanced → Expert
**Interview Relevance:** ⭐⭐⭐⭐⭐
**Category:** Software Architecture / Distributed Systems / Backend Engineering

---

## 0. Interview Relevance & Question Mapping

System Design evaluates whether you can design software that remains **correct, scalable, available, maintainable, observable, and cost-effective** as load and complexity increase.

### ⭐ Must-Know Areas

| Area                   | Priority |
| ---------------------- | -------: |
| Requirements Gathering |    ⭐⭐⭐⭐⭐ |
| API Design             |    ⭐⭐⭐⭐⭐ |
| Database Design        |    ⭐⭐⭐⭐⭐ |
| Caching                |    ⭐⭐⭐⭐⭐ |
| Load Balancing         |    ⭐⭐⭐⭐⭐ |
| Scaling                |    ⭐⭐⭐⭐⭐ |
| CAP Theorem            |    ⭐⭐⭐⭐⭐ |
| Consistency            |    ⭐⭐⭐⭐⭐ |
| Availability           |    ⭐⭐⭐⭐⭐ |
| Replication            |    ⭐⭐⭐⭐⭐ |
| Partitioning/Sharding  |    ⭐⭐⭐⭐⭐ |
| Message Queues         |     ⭐⭐⭐⭐ |
| Rate Limiting          |     ⭐⭐⭐⭐ |
| Distributed Systems    |    ⭐⭐⭐⭐⭐ |
| Reliability            |    ⭐⭐⭐⭐⭐ |
| Observability          |     ⭐⭐⭐⭐ |
| Security               |     ⭐⭐⭐⭐ |
| Cost/Trade-offs        |    ⭐⭐⭐⭐⭐ |

### Common interview questions

* Design URL Shortener
* Design WhatsApp
* Design Uber
* Design YouTube
* Design Netflix
* Design Instagram
* Design Amazon
* Design a Payment System
* Design a Notification System
* Design a Rate Limiter
* Design a Distributed Cache
* Design a Job Queue

---

# 1. Precise Definition

**System Design** is the process of defining the architecture, components, interfaces, data flow, storage, communication mechanisms, scalability strategy, reliability mechanisms, and operational characteristics of a software system.

### Interview-ready answer

> **System design is the process of translating functional and non-functional requirements into an architecture consisting of services, data stores, APIs, communication mechanisms, and infrastructure while balancing scalability, availability, consistency, latency, reliability, security, maintainability, and cost.**

---

# 2. Why Does System Design Exist?

A small application may look like:

```text
Client
  ↓
Application
  ↓
Database
```

At larger scale:

```text
                 Users
                   ↓
               DNS/CDN
                   ↓
             Load Balancer
                   ↓
        ┌──────────┼──────────┐
        ↓          ↓          ↓
    Server 1    Server 2    Server 3
        │          │          │
        └──────┬───┴──────────┘
               ↓
          Cache / Queue
               ↓
        ┌──────┴──────┐
        ↓             ↓
    Database       Object Store
```

Now new problems appear:

* Millions of requests
* Database bottlenecks
* Network failures
* Duplicate requests
* Partial failures
* Data consistency
* Service failures
* Traffic spikes
* Concurrent updates
* Security threats
* Observability

System design exists to reason about these problems **before and during implementation**.

---

# 3. Requirements Gathering

⭐ **The first step of almost every system-design interview.**

Do not immediately draw microservices.

First determine:

### Functional Requirements

What must the system do?

Example: URL shortener

```text
Create short URL
Redirect short URL
Track clicks
```

### Non-Functional Requirements

How should it behave?

```text
Availability
Latency
Throughput
Scalability
Durability
Consistency
Security
```

---

# 4. Functional vs Non-Functional

| Functional     | Non-Functional         |
| -------------- | ---------------------- |
| Create account | 99.99% availability    |
| Send message   | <100 ms latency        |
| Upload image   | 10M users              |
| Place order    | Durable storage        |
| Search product | Horizontal scalability |

### Interview rule

> **Functional requirements define what the system does. Non-functional requirements define how well the system must do it.**

---

# 5. Capacity Estimation

⭐ **Extremely Important**

Before choosing architecture, estimate scale.

Suppose:

```text
100 million users
10 million daily active users
20 requests/user/day
```

Daily requests:

```text
10M × 20
= 200M requests/day
```

Average QPS:

```text
200M / 86,400
≈ 2,315 requests/sec
```

If peak traffic is 5× average:

```text
≈ 11,575 requests/sec
```

Now architecture decisions become more meaningful.

---

# 6. Capacity Estimation Checklist

Estimate:

```text
Users
DAU / MAU
Requests per second
Peak QPS
Read/write ratio
Data size
Storage growth
Bandwidth
Cache size
Replication requirements
```

Example:

```text
10M DAU
↓
200M requests/day
↓
2.3K average QPS
↓
~11.5K peak QPS
```

---

# 7. High-Level Architecture

A common scalable architecture:

```text
                         Clients
                            │
                            ↓
                     DNS / CDN / WAF
                            │
                            ↓
                     Load Balancer
                            │
             ┌──────────────┼──────────────┐
             ↓              ↓              ↓
          Server          Server          Server
             │              │              │
             └──────────────┼──────────────┘
                            ↓
                       Cache / Queue
                            │
                  ┌─────────┴─────────┐
                  ↓                   ↓
              Database          Object Storage
```

Each component should exist for a reason.

---

# 8. Client

The client could be:

* Web browser
* Mobile application
* IoT device
* Another service

Typical flow:

```text
Client
  ↓
HTTPS Request
  ↓
API
```

Security should normally use:

```text
HTTPS/TLS
Authentication
Authorization
Rate limiting
Input validation
```

---

# 9. Load Balancer

A load balancer distributes traffic across application instances.

```text
                Load Balancer
                /     |     \
               ↓      ↓      ↓
             App1    App2    App3
```

Benefits:

* Horizontal scaling
* Fault isolation
* Traffic distribution
* Health checks

Common strategies:

```text
Round Robin
Least Connections
Weighted Routing
Consistent Hashing
```

---

# 10. Horizontal vs Vertical Scaling

⭐ **Frequently Asked**

### Vertical scaling

Increase machine capacity:

```text
8 CPU
 ↓
32 CPU
```

### Horizontal scaling

Add machines:

```text
1 Server
 ↓
10 Servers
```

| Vertical             | Horizontal                  |
| -------------------- | --------------------------- |
| Bigger machine       | More machines               |
| Simpler initially    | Better large-scale strategy |
| Hardware limit       | More scalable               |
| Can require downtime | Can support rolling scaling |

Modern distributed systems generally favor horizontal scaling where appropriate.

---

# 11. Stateless Application Servers

A scalable application server should ideally be stateless.

Bad:

```text
Server 1
 └── User Session

Server 2
 └── No Session
```

Request routed to Server 2 may fail to find the session.

Better:

```text
Server 1 ─┐
Server 2 ─┼──→ Redis / Database
Server 3 ─┘
```

Or use appropriately designed stateless authentication such as signed tokens.

---

# 12. Database Selection

The database should be selected based on access patterns and consistency requirements.

### Relational

Examples:

```text
PostgreSQL
MySQL
```

Good for:

* Transactions
* Relationships
* Structured data
* Strong consistency requirements

### NoSQL

Examples:

```text
MongoDB
Cassandra
DynamoDB
```

Useful when requirements favor:

* Massive horizontal scale
* Flexible schemas
* Specific access patterns
* High write throughput

### Key-value

```text
Redis
DynamoDB
```

### Graph

```text
Neo4j
```

for relationship-heavy workloads.

---

# 13. Database Schema Design

Example e-commerce system:

```text
User
 ├── id
 ├── name
 └── email

Product
 ├── id
 ├── name
 └── price

Order
 ├── id
 ├── user_id
 └── status

OrderItem
 ├── order_id
 ├── product_id
 └── quantity
```

Relationships:

```text
User
 ↓
Orders
 ↓
OrderItems
 ↓
Products
```

---

# 14. Indexing

Indexes improve query performance.

Example:

```sql
CREATE INDEX idx_user_email
ON users(email);
```

Without an appropriate index:

```text
Query
 ↓
Scan many rows
 ↓
Slow
```

With index:

```text
Query
 ↓
Index
 ↓
Relevant rows
 ↓
Faster
```

But indexes have costs:

* Storage
* Write overhead
* Maintenance
* Memory/cache pressure

Therefore:

> **Index based on actual query patterns, not blindly on every column.**

---

# 15. Database Replication

Replication maintains multiple copies of data.

```text
              Primary
             /       \
            ↓         ↓
       Replica 1   Replica 2
```

Writes:

```text
Client
 ↓
Primary
```

Reads may be distributed:

```text
Client
 ↓
Read Replicas
```

Benefits:

* Read scalability
* Fault tolerance
* Disaster recovery

Trade-off:

```text
Replication lag
```

can create stale reads.

---

# 16. Sharding

When one database cannot handle the scale, data can be partitioned across multiple database nodes.

Example:

```text
User ID

1 - 1M       → Shard 1
1M - 2M      → Shard 2
2M - 3M      → Shard 3
```

Conceptually:

```text
                 Router
                   ↓
        ┌──────────┼──────────┐
        ↓          ↓          ↓
     Shard 1    Shard 2    Shard 3
```

Common shard keys:

```text
user_id
tenant_id
region
```

---

# 17. Good Shard Key

A good shard key should provide:

* Even distribution
* High cardinality
* Query locality
* Low hotspot risk

Bad shard key:

```text
country
```

if one country receives 80% of traffic.

Potential hotspot:

```text
Shard India
████████████████████
Shard USA
████
Shard UK
██
```

---

# 18. Caching

⭐ **Must-Know**

Cache stores frequently accessed data closer to the application.

```text
Client
 ↓
Application
 ↓
Cache
 ├── Hit → Return
 └── Miss
      ↓
   Database
      ↓
    Cache
```

Example:

```text
Redis
```

---

# 19. Cache-Aside Pattern

Common pattern:

```text
Read
 ↓
Check Cache
 ↓
Hit?
 ├── Yes → Return
 └── No
      ↓
   Database
      ↓
   Update Cache
      ↓
   Return
```

Advantages:

* Simple
* Database load reduction
* Low latency

Challenge:

> Cache invalidation.

---

# 20. Cache Invalidation

Suppose:

```text
Database:
price = 100

Cache:
price = 100
```

Update database:

```text
price = 120
```

If cache still contains:

```text
100
```

the application may return stale data.

Common strategies:

```text
TTL
Explicit invalidation
Write-through
Write-back
Cache-aside
Versioning
Event-driven invalidation
```

---

# 21. Cache Eviction

When cache capacity is limited:

```text
LRU
LFU
FIFO
TTL
```

### LRU

**Least Recently Used**

Remove data that hasn't been accessed recently.

### LFU

**Least Frequently Used**

Remove data with low access frequency.

---

# 22. Message Queue

Queues decouple producers and consumers.

```text
Producer
   ↓
 Queue
   ↓
Consumer
```

Examples:

```text
Kafka
RabbitMQ
Amazon SQS
```

Useful for:

* Async processing
* Traffic spikes
* Event-driven architecture
* Retry
* Decoupling
* Background jobs

---

# 23. Synchronous vs Asynchronous

### Synchronous

```text
Client
 ↓
Service A
 ↓
Service B
 ↓
Response
```

Service A waits.

### Asynchronous

```text
Client
 ↓
Service A
 ↓
Queue
 ↓
Response

Consumer
 ↓
Process later
```

Async processing can improve responsiveness and decouple workloads, but introduces eventual consistency and operational complexity.

---

# 24. Kafka Mental Model

A simplified Kafka architecture:

```text
Producer
   ↓
Topic
 ├── Partition 0
 ├── Partition 1
 └── Partition 2
        ↓
    Consumer Group
```

Partitions enable:

* Parallelism
* Ordering within a partition
* Horizontal consumption

Important concepts:

```text
Topic
Partition
Offset
Producer
Consumer
Consumer Group
Replication
```

---

# 25. API Design

Example:

```http
POST /users
GET /users/{id}
PUT /users/{id}
DELETE /users/{id}
```

Good API design considers:

* HTTP methods
* Status codes
* Request validation
* Pagination
* Idempotency
* Authentication
* Authorization
* Versioning
* Error format

---

# 26. Idempotency

⭐ **Extremely Important**

An operation is idempotent if repeating the same operation produces the same intended final effect.

This matters especially for payments.

Suppose:

```text
Client
 ↓
POST /payment
 ↓
Server processes payment
 ↓
Response lost
```

Client retries:

```text
POST /payment
```

Without protection:

```text
Payment 1
Payment 2 ❌
```

Use an idempotency key:

```text
Idempotency-Key: abc123
```

Server:

```text
abc123
 ↓
Already processed?
 ├── Yes → Return previous result
 └── No → Process + store result
```

---

# 27. Rate Limiting

Protects services from excessive requests.

Example:

```text
User
 ↓
Rate Limiter
 ↓
100 requests/minute?
 ├── Yes → Allow
 └── No → 429
```

Common algorithms:

```text
Token Bucket
Leaky Bucket
Fixed Window
Sliding Window
```

---

# 28. CAP Theorem

⭐ **Must-Know Distributed Systems Concept**

For a distributed data system, CAP refers to:

```text
C → Consistency
A → Availability
P → Partition Tolerance
```

During a network partition, a system cannot simultaneously guarantee both perfect consistency and availability in the CAP sense.

Therefore:

```text
Partition occurs
      ↓
Choose trade-off
   /          \
Consistency  Availability
```

### Important interview correction

Do **not** say:

> "You choose any two of C, A, P."

More precisely:

> **When a partition occurs, a distributed system must trade off consistency and availability. Partition tolerance is a practical requirement for distributed systems operating over unreliable networks.**

---

# 29. Strong vs Eventual Consistency

### Strong consistency

After a successful write:

```text
All reads
 ↓
See latest value
```

Useful for:

* Financial transactions
* Critical state

### Eventual consistency

After a write:

```text
Some replicas
 ↓
May temporarily have old data
 ↓
Eventually converge
```

Useful when:

* Availability matters
* Slight staleness is acceptable
* Massive distributed scale is required

---

# 30. Availability

Availability represents how often the system is operational and able to serve requests.

Common targets:

```text
99%
99.9%
99.99%
99.999%
```

For example:

```text
99.9%
```

allows significantly more downtime than:

```text
99.99%
```

Availability targets should be connected to business requirements and measured through appropriate SLIs/SLOs.

---

# 31. Reliability

Reliability is the ability of a system to perform correctly over time despite failures.

Techniques:

```text
Retries
Timeouts
Circuit Breakers
Replication
Failover
Backups
Idempotency
Dead Letter Queues
Health Checks
```

---

# 32. Retry

Suppose:

```text
Service A
   ↓
Service B
```

B temporarily fails.

A can retry:

```text
Attempt 1
 ↓
Failure
 ↓
Wait
 ↓
Attempt 2
 ↓
Failure
 ↓
Backoff
 ↓
Attempt 3
```

Use **exponential backoff** and usually **jitter** to reduce synchronized retry storms.

---

# 33. Circuit Breaker

If a downstream service repeatedly fails:

```text
Service A
   ↓
Service B
   ↓
Failure
```

Continuously retrying can overload B further.

Circuit breaker:

```text
Closed
  ↓ failures
Open
  ↓
Reject/fallback quickly
  ↓
Half-Open
  ↓
Test recovery
  ↓
Closed
```

States:

```text
CLOSED
OPEN
HALF_OPEN
```

---

# 34. Timeout

Never allow distributed requests to wait indefinitely.

```text
Service A
   ↓
Service B
   ↓
Timeout
```

Without timeouts:

```text
Request
 ↓
Thread waiting
 ↓
Thread waiting
 ↓
Thread waiting
 ↓
Resource exhaustion
```

Timeouts prevent resource starvation from slow dependencies.

---

# 35. Observability

Production systems need:

```text
Logs
Metrics
Traces
```

Known as the three pillars of observability.

### Logs

What happened?

### Metrics

How much/how often?

Examples:

```text
QPS
CPU
Memory
Error rate
Latency
```

### Traces

Where did the request spend time?

```text
Client
 ↓ 10ms
API
 ↓ 20ms
Service
 ↓ 100ms
Database
```

---

# 36. Monitoring Architecture

```text
Application
 ├── Logs ─────→ Logging System
 ├── Metrics ──→ Metrics System
 └── Traces ───→ Tracing System
                      ↓
                  Dashboard
                      ↓
                    Alert
```

Typical ecosystem:

```text
Prometheus
Grafana
OpenTelemetry
ELK/OpenSearch
```

---

# 37. Security

A production system should consider:

```text
Authentication
Authorization
TLS
Encryption
Secrets Management
Input Validation
Rate Limiting
Audit Logging
Network Security
```

Authentication:

```text
Who are you?
```

Authorization:

```text
What can you do?
```

---

# 38. Database Transaction

Consider transferring money:

```text
Account A
 ↓
Debit ₹100
 ↓
Account B
 ↓
Credit ₹100
```

If debit succeeds but credit fails:

```text
A = -100
B = unchanged
```

Incorrect.

A transaction aims to preserve the required atomicity:

```text
BEGIN
 ↓
Debit
 ↓
Credit
 ↓
COMMIT

or

ROLLBACK
```

---

# 39. ACID

⭐ **Frequently Asked**

```text
A → Atomicity
C → Consistency
I → Isolation
D → Durability
```

### Atomicity

All-or-nothing.

### Consistency

Transactions preserve defined database invariants.

### Isolation

Concurrent transactions are isolated according to the chosen isolation level.

### Durability

Committed data survives appropriate failures.

---

# 40. Distributed Transactions

Across multiple services:

```text
Order Service
     ↓
Payment Service
     ↓
Inventory Service
```

A single traditional DB transaction may not span all services.

Common patterns:

```text
Saga
Outbox Pattern
Event-driven architecture
Compensating transactions
```

### Saga

```text
Order
 ↓
Payment
 ↓
Inventory
 ↓
Shipping
```

If a later operation fails:

```text
Compensating action
```

may undo the business effect of earlier operations.

---

# 41. Outbox Pattern

Problem:

```text
Update DB
   +
Publish Event
```

What if:

```text
DB succeeds
Event publishing fails
```

The state and event diverge.

Outbox:

```text
Transaction
 ├── Update business data
 └── Insert event into outbox
          ↓
       Commit
          ↓
   Event Publisher
          ↓
        Kafka
```

This improves reliable event publication.

---

# 42. Microservices vs Monolith

### Monolith

```text
              Application
 ┌────────────────────────────┐
 │ User │ Order │ Payment │ DB│
 └────────────────────────────┘
```

### Microservices

```text
User Service
     ↓
Order Service
     ↓
Payment Service
     ↓
Inventory Service
```

### Microservices advantages

* Independent deployment
* Independent scaling
* Team autonomy
* Fault isolation in some dimensions

### Costs

* Network communication
* Distributed transactions
* Observability complexity
* Deployment complexity
* Data consistency challenges
* Operational overhead

### Interview rule

> **Do not choose microservices merely because the system is "large." Choose boundaries based on domain, scaling, ownership, deployment, and operational requirements.**

---

# 43. Monolith vs Microservices

| Monolith                   | Microservices                      |
| -------------------------- | ---------------------------------- |
| Simpler deployment         | More complex deployment            |
| Easier local development   | Distributed development            |
| Easier transactions        | Distributed consistency challenges |
| Easier debugging initially | Better service isolation           |
| Can scale horizontally     | Independent service scaling        |
| Lower operational overhead | Higher operational overhead        |

---

# 44. CDN

A CDN caches content closer to users.

```text
User India
   ↓
CDN Edge India
   ↓
Origin

User USA
   ↓
CDN Edge USA
   ↓
Origin
```

Useful for:

* Images
* Videos
* JavaScript
* CSS
* Static assets

Benefits:

```text
Lower latency
Lower origin traffic
Higher scalability
```

---

# 45. Object Storage

Large files should generally not be stored directly in the application server filesystem.

Architecture:

```text
Client
 ↓
Application
 ↓
Object Storage
```

Examples:

```text
Images
Videos
PDFs
Backups
```

Typical pattern:

```text
Application
 ↓
Generate pre-signed upload URL
 ↓
Client
 ↓
Object Storage
```

This reduces application-server bandwidth and storage pressure.

---

# 46. System Design Process

Use this sequence in interviews:

```text id="x8gls3"
1. Requirements
       ↓
2. Constraints
       ↓
3. Capacity Estimation
       ↓
4. API Design
       ↓
5. Data Model
       ↓
6. High-Level Architecture
       ↓
7. Deep Dive
       ↓
8. Scaling
       ↓
9. Reliability
       ↓
10. Security
       ↓
11. Observability
       ↓
12. Trade-offs
```

---

# 47. Example: URL Shortener

### Requirements

```text
Create short URL
Redirect short URL
```

### API

```http
POST /urls
GET /{shortCode}
```

### Architecture

```text
Client
 ↓
Load Balancer
 ↓
URL Service
 ├── Redis
 └── Database
```

### Redirect flow

```text
GET /abc123
     ↓
Redis?
 ├── Hit → URL
 └── Miss
      ↓
   Database
      ↓
   Redis
      ↓
   URL
```

This illustrates:

* API design
* Database
* Cache
* Horizontal scaling
* Read-heavy workload

---

# 48. Common Interview Mistakes

### ❌ Starting with technology

> "We'll use Kafka, Redis, Kubernetes, MongoDB..."

Wrong starting point.

Start with:

```text
Requirements
 ↓
Constraints
 ↓
Architecture
 ↓
Technology choice
```

---

### ❌ Adding microservices everywhere

More services do not automatically mean better architecture.

---

### ❌ Ignoring failure

A distributed system must assume:

```text
Network can fail
Server can fail
Database can fail
Messages can duplicate
Requests can retry
```

---

### ❌ Ignoring data consistency

Always ask:

> What consistency does this operation require?

---

### ❌ Ignoring cost

The theoretically scalable architecture may be unnecessarily expensive.

---

# 49. Interviewer Follow-Up Chain

```text
Design the system
      ↓
What are the requirements?
      ↓
How much traffic?
      ↓
What is peak QPS?
      ↓
How much storage?
      ↓
SQL or NoSQL?
      ↓
Why?
      ↓
How do you scale the database?
      ↓
Do you need caching?
      ↓
What happens when cache fails?
      ↓
What happens when DB fails?
      ↓
How do you handle duplicate requests?
      ↓
What happens during network partition?
      ↓
What consistency do you need?
      ↓
How do services communicate?
      ↓
How do you handle retries?
      ↓
What if a downstream service is down?
      ↓
How do you monitor it?
      ↓
How do you secure it?
      ↓
What are the trade-offs?
```

---

# 50. Common Candidate Mistakes

### Weak

> "Use Redis for performance."

### Strong

> "The workload is read-heavy and the same objects are accessed repeatedly, so a cache can reduce database load and latency. I'd use a cache-aside strategy with an appropriate TTL and explicit invalidation where stale data is unacceptable."

---

### Weak

> "Use Kafka because it is scalable."

### Strong

> "The operation is asynchronous and can tolerate eventual processing, so a durable message broker decouples producers from consumers, absorbs traffic spikes, and enables independent scaling."

---

# 51. 30-Second Revision

```text id="3pr3ls"
SYSTEM DESIGN
│
├── Requirements
├── Scale
├── APIs
├── Database
├── Cache
├── Load Balancer
├── Queue
├── Scaling
├── Consistency
├── Availability
├── Reliability
├── Security
└── Observability
```

### Core mental model

```text
Users
 ↓
CDN / WAF
 ↓
Load Balancer
 ↓
Stateless Services
 ↓
Cache / Queue
 ↓
Database / Object Storage
```

### Distributed systems

```text
Replication
Sharding
Caching
Partitioning
Consistency
Idempotency
Retries
Timeouts
Circuit Breakers
```

### Golden rule

> **Every architectural component should solve a specific requirement or bottleneck.**

---

# 52. Master Interview Test

Answer without looking back:

1. What is System Design?
2. Difference between functional and non-functional requirements?
3. Why is capacity estimation important?
4. Horizontal vs vertical scaling?
5. Why should application servers preferably be stateless?
6. SQL vs NoSQL — how would you decide?
7. What problem does caching solve?
8. **Explain cache-aside and its invalidation challenges.**
9. **Explain replication vs sharding and when you would use each.**
10. **Design a production-grade e-commerce system handling millions of users. Explain requirements, APIs, database schema, service boundaries, caching, messaging, transactions, consistency, scaling, failure handling, security, observability, and the trade-offs behind every major architectural decision.**
