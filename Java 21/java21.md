# Java 21

**Difficulty:** ⭐ Must-Know → Expert
**Interview Relevance:** ⭐⭐⭐⭐⭐
**Category:** Java Core / JVM / Concurrency / Modern Java / Backend

> **Important:** Java 21 is an **LTS (Long-Term Support)** release. For interviews, focus not only on syntax but on **why these features were introduced, their runtime behavior, concurrency model, and production trade-offs**.

---

# 0. Interview Relevance & Question Mapping

### ⭐ Priority

| Topic                                 |              Priority |
| ------------------------------------- | --------------------: |
| Virtual Threads                       |                 ⭐⭐⭐⭐⭐ |
| Records                               |                 ⭐⭐⭐⭐⭐ |
| Sealed Classes                        |                 ⭐⭐⭐⭐⭐ |
| Pattern Matching                      |                 ⭐⭐⭐⭐⭐ |
| Switch Expressions / Pattern Matching |                 ⭐⭐⭐⭐⭐ |
| Text Blocks                           |                   ⭐⭐⭐ |
| Record Patterns                       |                  ⭐⭐⭐⭐ |
| Sequenced Collections                 |                  ⭐⭐⭐⭐ |
| String Templates                      | ⚠️ Preview in Java 21 |
| Structured Concurrency                | ⚠️ Preview in Java 21 |
| Generational ZGC                      |                  ⭐⭐⭐⭐ |
| Foreign Function & Memory API         |                   ⭐⭐⭐ |
| JVM Performance                       |                  ⭐⭐⭐⭐ |

### ⭐ Frequently Asked

* What are the major features of Java 21?
* Why are virtual threads important?
* Platform thread vs virtual thread?
* How do virtual threads work?
* Are virtual threads faster than platform threads?
* What is a record?
* Record vs normal class?
* Are records immutable?
* What are sealed classes?
* What problem does pattern matching solve?
* Pattern matching for `switch`?
* What are record patterns?
* What are sequenced collections?
* What changed in the JVM in Java 21?
* What is Generational ZGC?
* Structured concurrency?
* Which Java 21 features are preview features?

---

# 1. Precise Definition

**Java 21** is an LTS Java release that introduced and finalized several major language, library, JVM, and concurrency features, most notably **virtual threads, pattern matching, record patterns, sequenced collections, and finalized improvements around modern Java programming**.

### Interview-ready answer

> **Java 21 is an LTS release that significantly modernizes Java through lightweight virtual threads, finalized pattern matching features, record patterns, sealed-class integration, sequenced collections, and important JVM improvements, making Java particularly stronger for scalable concurrent backend applications.**

---

# 2. Why Does Java 21 Exist?

Modern backend systems increasingly require:

```text
Millions of requests
       ↓
High concurrency
       ↓
Many I/O operations
       ↓
Efficient resource usage
```

Traditional platform-thread-per-request models can become expensive at very high concurrency.

Java 21 introduces:

```text
Virtual Threads
       ↓
Millions of lightweight concurrent tasks
       ↓
Simpler blocking-style code
       ↓
Better scalability for I/O-heavy workloads
```

At the language level:

```text
Old Java
 ↓
verbose conditionals
 ↓
manual type checks
```

Modern Java:

```text
Pattern Matching
 ↓
less boilerplate
 ↓
more expressive type handling
```

---

# 3. Major Java 21 Features

```text
Java 21
│
├── Virtual Threads ⭐⭐⭐⭐⭐
│
├── Pattern Matching for switch ⭐⭐⭐⭐⭐
│
├── Record Patterns ⭐⭐⭐⭐⭐
│
├── Record Classes ⭐⭐⭐⭐⭐
│
├── Sealed Classes ⭐⭐⭐⭐⭐
│
├── Sequenced Collections ⭐⭐⭐⭐
│
├── String Templates ⚠️ Preview
│
├── Structured Concurrency ⚠️ Preview
│
├── Generational ZGC ⭐⭐⭐⭐
│
├── Foreign Function & Memory API ⭐⭐⭐
│
└── Other JVM / library improvements
```

**Important interview distinction:** not every feature associated with Java 21 is finalized. Some were **preview features** in Java 21.

---

# 4. Virtual Threads

# ⭐⭐⭐⭐⭐ Most Important Java 21 Topic

Virtual threads are lightweight threads managed by the JVM rather than being directly mapped one-to-one to operating-system threads.

Example:

```java
Thread.startVirtualThread(() -> {
    System.out.println("Running");
});
```

Or:

```java
Thread.startVirtualThread(() -> {
    processRequest();
});
```

Conceptually:

```text
Traditional Platform Threads

Java Thread
     ↓
OS Thread
     ↓
Expensive


Virtual Threads

Virtual Thread
     ↓
JVM Scheduler
     ↓
Carrier Platform Thread
     ↓
OS Thread
```

---

# 5. Why Virtual Threads?

Suppose a server receives:

```text
100,000 requests
```

and many requests spend time waiting for:

* Database
* HTTP API
* File I/O
* Network

With platform threads:

```text
Request
   ↓
Platform Thread
   ↓
WAITING
   ↓
OS resource consumed
```

Virtual threads make blocking-style I/O much cheaper from the application's concurrency perspective.

```text
100,000 tasks
      ↓
100,000 Virtual Threads
      ↓
Small number of Carrier Threads
      ↓
CPU
```

This allows much higher concurrency for suitable workloads.

---

# 6. Platform Thread vs Virtual Thread

| Platform Thread           | Virtual Thread                              |
| ------------------------- | ------------------------------------------- |
| Maps closely to OS thread | JVM-managed                                 |
| Relatively expensive      | Very lightweight                            |
| Limited practical count   | Very high count possible                    |
| Good for CPU-bound work   | Excellent for many I/O-bound tasks          |
| OS scheduling involved    | JVM schedules virtual threads onto carriers |
| Larger memory footprint   | Much smaller per thread                     |

### Critical point

> **Virtual threads are not simply "faster threads."**

Their major advantage is **scalable concurrency**, particularly for workloads that spend substantial time waiting.

---

# 7. Creating Virtual Threads

### Directly

```java
Thread thread =
        Thread.startVirtualThread(() -> {
            System.out.println("Hello");
        });
```

### Builder API

```java
Thread thread =
        Thread.ofVirtual()
              .start(() -> {
                  System.out.println("Hello");
              });
```

### Executor

```java
try (ExecutorService executor =
         Executors.newVirtualThreadPerTaskExecutor()) {

    executor.submit(() -> processRequest());
}
```

The executor creates a new virtual thread per submitted task.

---

# 8. Virtual Thread Architecture

Conceptually:

```text
                    JVM
                     │
              Virtual Thread
              Virtual Thread
              Virtual Thread
              Virtual Thread
                     │
             JVM Scheduler
                     │
          ┌──────────┼──────────┐
          ↓          ↓          ↓
      Carrier 1   Carrier 2   Carrier 3
          ↓          ↓          ↓
        OS Thread  OS Thread  OS Thread
```

The carrier threads execute virtual threads.

When a virtual thread performs certain blocking operations, the JVM can suspend/unmount it and use the carrier for another runnable virtual thread.

---

# 9. Mounting and Unmounting

⭐ **Advanced**

Suppose:

```java
Thread.startVirtualThread(() -> {
    databaseCall();
});
```

Conceptually:

```text
Virtual Thread
      ↓
Mounted on Carrier
      ↓
Database I/O
      ↓
Virtual Thread waits
      ↓
Unmounted
      ↓
Carrier can execute another task
```

When the operation completes:

```text
I/O complete
     ↓
Virtual thread becomes runnable
     ↓
Mounted on available carrier
     ↓
Execution continues
```

This is a major reason virtual threads improve concurrency for blocking workloads.

---

# 10. Virtual Threads Are Not for CPU Speed

⭐ **Interview Trap**

Suppose you have:

```java
while (true) {
    calculateSomething();
}
```

This is CPU-bound.

Virtual threads do not magically provide more CPU cores.

```text
CPU-bound
   ↓
Limited by CPU
   ↓
Virtual threads don't create CPU capacity
```

Virtual threads are most valuable when:

```text
Concurrency
   +
Blocking I/O
```

---

# 11. Virtual Threads vs Reactive Programming

Traditional reactive architecture:

```text
Request
 ↓
Non-blocking API
 ↓
Callback / Future
 ↓
Continuation
```

Virtual threads allow:

```text
Request
 ↓
Normal-looking blocking code
 ↓
DB call
 ↓
HTTP call
 ↓
Response
```

while still supporting very high concurrency.

This can significantly simplify application code.

### Important

Virtual threads do **not** make blocking operations universally non-blocking.

They make the **threading cost of waiting** much lower for operations that integrate appropriately with the virtual-thread model.

---

# 12. Virtual Threads and Spring Boot

Modern Spring applications can take advantage of virtual threads when configured appropriately.

Conceptually:

```text
HTTP Request
      ↓
Virtual Thread
      ↓
Controller
      ↓
Service
      ↓
Database
      ↓
Wait
      ↓
Virtual Thread yields
      ↓
Response
```

But there is a critical constraint:

> Your entire stack must be evaluated for virtual-thread compatibility and bottlenecks.

For example, even if millions of virtual threads are possible, a database connection pool may still only allow:

```text
100 connections
```

Therefore:

```text
Virtual threads ≠ unlimited database concurrency
```

---

# 13. Virtual Thread Interview Trap

Question:

> If I create 1 million virtual threads, will my application execute 1 million tasks simultaneously?

**No.**

Runnable tasks still compete for CPU and underlying resources.

You may have:

```text
1,000,000 virtual threads
        ↓
limited CPU cores
        ↓
limited carrier threads
        ↓
limited DB connections
        ↓
limited network capacity
```

Virtual threads primarily reduce the **cost of maintaining large numbers of concurrent tasks**.

---

# 14. Records

⭐ **Must-Know**

Records are concise classes designed primarily for modeling immutable data carriers.

Example:

```java
public record User(
        Long id,
        String name,
        String email
) {}
```

This automatically provides important members such as:

* Accessor methods
* Constructor
* `equals()`
* `hashCode()`
* `toString()`

For a record:

```java
User user = new User(
        1L,
        "Divyansh",
        "user@example.com"
);
```

Access:

```java
user.name();
user.email();
```

---

# 15. Record vs Normal Class

| Record                               | Normal Class                 |
| ------------------------------------ | ---------------------------- |
| Concise data carrier syntax          | More boilerplate             |
| Components declared in header        | Fields declared separately   |
| Accessors generated                  | Usually written manually     |
| `equals/hashCode/toString` generated | Usually implemented manually |
| Implicitly extends `Record`          | Can extend a class           |
| Cannot extend another class          | Can extend a class           |
| Components are final fields          | Fields can be mutable        |
| Good for value-like data             | General-purpose modeling     |

---

# 16. Are Records Immutable?

⭐ **Major Interview Trap**

Records are **shallowly immutable**, not automatically deeply immutable.

Example:

```java
record User(List<String> roles) {}
```

The record component reference cannot be reassigned, but the list itself may still be mutable.

```java
user.roles().add("ADMIN");
```

may modify the underlying list.

Therefore:

> **A record does not automatically make referenced objects immutable.**

---

# 17. Record Constructor

You can validate data using a compact constructor:

```java
record User(String name, int age) {

    public User {
        if (age < 0) {
            throw new IllegalArgumentException(
                    "Age cannot be negative"
            );
        }
    }
}
```

The compact constructor avoids repeating the parameter list.

---

# 18. Records and DTOs

Records are excellent for DTO-style objects:

```java
public record UserResponse(
        Long id,
        String name,
        String email
) {}
```

Spring Boot API:

```text
Database Entity
      ↓
Service
      ↓
UserResponse Record
      ↓
JSON
```

This reduces DTO boilerplate.

---

# 19. Sealed Classes

⭐ **Must-Know**

Sealed classes restrict which classes can extend or implement a type.

```java
public sealed interface Payment
        permits CardPayment,
                UPIPayment,
                CashPayment {
}
```

Implementations:

```java
final class CardPayment
        implements Payment {
}
```

```java
final class UPIPayment
        implements Payment {
}
```

---

# 20. Why Sealed Classes?

Without sealed classes:

```text
Payment
 ├── Card
 ├── UPI
 ├── Cash
 ├── Unknown
 ├── SomethingElse
 └── ...
```

With sealed classes:

```text
Payment
 ├── CardPayment
 ├── UPIPayment
 └── CashPayment
```

The hierarchy becomes explicitly controlled.

Useful for:

* Domain modeling
* State machines
* API results
* Compiler exhaustiveness analysis
* Controlled inheritance

---

# 21. Sealed Class Hierarchy

A permitted subclass must declare one of:

```text
final
sealed
non-sealed
```

Example:

```java
sealed class Vehicle
        permits Car, Bike {
}
```

```java
final class Car extends Vehicle {
}
```

```java
non-sealed class Bike extends Vehicle {
}
```

`non-sealed` reopens inheritance beyond that branch.

---

# 22. Pattern Matching for `instanceof`

Modern Java reduces boilerplate.

Old:

```java
if (obj instanceof String) {
    String s = (String) obj;
    System.out.println(s.length());
}
```

Modern:

```java
if (obj instanceof String s) {
    System.out.println(s.length());
}
```

The variable `s` is introduced as part of the type test.

---

# 23. Pattern Matching for `switch`

⭐ **Very Important**

Modern Java can use type patterns in `switch`.

Conceptually:

```java
static String describe(Object obj) {

    return switch (obj) {
        case Integer i -> "Integer: " + i;
        case String s -> "String: " + s;
        case null -> "null";
        default -> "Other";
    };
}
```

This is much more expressive than:

```text
instanceof
+
casts
+
if/else
```

---

# 24. Why Pattern Matching Matters

Traditional:

```text
Check type
 ↓
Cast
 ↓
Use
```

Modern:

```text
Pattern
 ↓
Match + bind
 ↓
Use
```

This reduces:

* Boilerplate
* Casting
* Error-prone code
* Complex conditional logic

---

# 25. Record Patterns

⭐ **Advanced**

Record patterns allow destructuring of record components.

Suppose:

```java
record Point(int x, int y) {}
```

Instead of:

```java
if (obj instanceof Point p) {
    int x = p.x();
    int y = p.y();
}
```

you can use a record pattern:

```java
if (obj instanceof Point(int x, int y)) {
    System.out.println(x);
    System.out.println(y);
}
```

Conceptually:

```text
Point
 ├── x
 └── y

Pattern
 ↓
(x, y)
```

---

# 26. Record Patterns + Sealed Classes

This combination is powerful.

```java
sealed interface Shape
        permits Circle, Rectangle {
}

record Circle(double radius)
        implements Shape {
}

record Rectangle(double width, double height)
        implements Shape {
}
```

Now pattern matching can express domain logic concisely.

```java
static double area(Shape shape) {

    return switch (shape) {
        case Circle(double r) ->
                Math.PI * r * r;

        case Rectangle(double w, double h) ->
                w * h;
    };
}
```

This gives you:

```text
Sealed hierarchy
       +
Pattern matching
       +
Record patterns
       ↓
Strong domain modeling
```

---

# 27. Exhaustiveness

With a sealed hierarchy:

```java
sealed interface Result
        permits Success, Failure {
}
```

A switch can reason about the known permitted subclasses.

```java
switch (result) {
    case Success s -> ...
    case Failure f -> ...
}
```

The compiler can detect missing cases in appropriate switch expressions/statements.

This is particularly valuable when domain states are deliberately finite.

---

# 28. Switch Expressions

Modern Java allows:

```java
int result = switch (day) {

    case MONDAY, FRIDAY -> 6;

    case TUESDAY -> 7;

    default -> 8;
};
```

No need for:

```java
int result;

switch(day) {
    ...
}
```

and manual assignment in every branch.

---

# 29. Sequenced Collections

⭐ **Java 21 Library Feature**

Java 21 introduced interfaces for collections with a defined encounter order.

Important concepts:

```text
SequencedCollection
SequencedSet
SequencedMap
```

They provide consistent operations for working with first and last elements.

Conceptually:

```text
Collection
   ↓
SequencedCollection
   ├── first
   ├── last
   ├── reversed
   └── addFirst/addLast
```

This addresses inconsistencies in older collection APIs around accessing the beginning/end/reversed view of ordered collections.

---

# 30. Example: Sequenced Collection

Conceptually:

```java
SequencedCollection<String> names =
        new ArrayList<>();

names.addLast("A");
names.addLast("B");

String first = names.getFirst();
String last = names.getLast();

SequencedCollection<String> reversed =
        names.reversed();
```

This provides a common abstraction for sequence-aware collections.

---

# 31. String Templates

⚠️ **Preview Feature in Java 21**

String Templates were introduced as a preview feature, not a finalized Java 21 feature.

Conceptually:

```text
Template
+
Expressions
↓
String
```

This is important in interviews because you should distinguish:

```text
Final feature
vs
Preview feature
```

Do not describe every Java 21 preview feature as part of stable Java syntax.

---

# 32. Structured Concurrency

⚠️ **Preview Feature in Java 21**

Structured concurrency was introduced as a preview API.

The goal is to treat related concurrent tasks as a single structured unit.

Conceptually:

```text
Request
   │
   ├── Task A
   ├── Task B
   └── Task C
        ↓
   Combined result
```

Instead of launching unrelated background tasks whose lifetimes become difficult to track.

This is especially relevant alongside virtual threads.

---

# 33. Structured Concurrency Mental Model

Without structured concurrency:

```text
Request
 ├── Thread A ────────────────?
 ├── Thread B ─────────?
 └── Thread C ───────────────────?
```

Task lifetime can become difficult to reason about.

Structured approach:

```text
Request Scope
 ├── Task A
 ├── Task B
 └── Task C
      ↓
All complete/cancel
      ↓
Scope exits
```

The objective is **clear ownership and lifecycle of concurrent tasks**.

---

# 34. Generational ZGC

⭐ **Advanced JVM Topic**

Java 21 introduced **Generational ZGC**.

ZGC is a low-latency garbage collector.

Generational ZGC adds generational collection concepts:

```text
Young Generation
Old Generation
```

The motivation comes from the common observation that many objects are short-lived.

```text
Objects
 │
 ├── Short-lived → Young
 │
 └── Long-lived → Old
```

This can improve efficiency for suitable workloads.

---

# 35. Foreign Function & Memory API

Java 21 finalized the **Foreign Function & Memory API**.

It provides a modern way for Java programs to:

* Access foreign memory
* Interoperate with native code

This is especially relevant for:

```text
Native libraries
High-performance systems
Systems programming integration
Off-heap memory
```

It provides a safer and more structured alternative to some older native-interoperability approaches.

---

# 36. Java 21 and JVM

Java 21 is not only a language update.

Think in layers:

```text
Java 21
│
├── Language
│   ├── Pattern matching
│   ├── Record patterns
│   └── Sealed classes
│
├── Libraries
│   ├── Sequenced collections
│   └── Concurrency APIs
│
├── Concurrency
│   ├── Virtual threads
│   └── Structured concurrency
│
└── JVM
    └── Generational ZGC
```

---

# 37. Java 21 vs Java 8

⭐ **Very Important for Experienced Java Interviews**

| Java 8                   | Java 21               |
| ------------------------ | --------------------- |
| Lambdas                  | Pattern matching      |
| Streams                  | Record patterns       |
| Optional                 | Sealed classes        |
| Default methods          | Virtual threads       |
| `java.time`              | Sequenced collections |
| CompletableFuture        | Modern concurrency    |
| Traditional thread model | Virtual-thread model  |
| Verbose DTO classes      | Records               |
| Traditional type checks  | Pattern matching      |

### Evolution

```text
Java 8
 ↓
Functional programming
 ↓
Java 11
 ↓
Java 17 LTS
 ↓
Records + Sealed Classes
 ↓
Java 21 LTS
 ↓
Virtual Threads
 + Pattern Matching
 + Record Patterns
 + Modern Collections
```

---

# 38. Java 21 in Spring Boot

For modern backend systems:

```text
Client
  ↓
Spring Boot
  ↓
Virtual Threads
  ↓
Controller
  ↓
Service
  ↓
Database
```

Records:

```java
public record UserResponse(
        Long id,
        String name
) {}
```

Pattern matching:

```java
switch (result) {
    case Success s -> ...
    case Failure f -> ...
}
```

Virtual threads:

```text
HTTP requests
     ↓
Virtual threads
     ↓
Blocking-style service code
     ↓
Database / external APIs
```

This can simplify highly concurrent I/O-heavy backend applications.

---

# 39. Production Trade-Off: Virtual Threads

### Good fit

```text
REST APIs
Database calls
HTTP clients
File/network I/O
High concurrent request counts
```

### Less useful

```text
Heavy CPU computation
GPU workloads
Tasks already limited by CPU
```

Remember:

```text
Virtual threads solve
        ↓
Thread scalability

They do NOT solve
        ↓
CPU capacity
Database capacity
Network bandwidth
```

---

# 40. Production Trade-Off: Records

Good:

```text
DTOs
API responses
Value-like data
Events
Configuration snapshots
```

Potentially less appropriate:

```text
Entities with complex mutable lifecycle
ORM models requiring framework-specific behavior
Objects whose identity/lifecycle semantics don't fit value-like modeling
```

Do not blindly replace every POJO with a record.

---

# 41. Production Trade-Off: Sealed Classes

Excellent when the domain is closed:

```text
Payment
 ├── Card
 ├── UPI
 └── Cash
```

Less appropriate when third parties must freely extend the hierarchy.

---

# 42. Bad vs Good Design

### ❌ Bad

Using virtual threads everywhere because:

> "Java 21 is faster."

This misunderstands the feature.

### Better

Analyze:

```text
Workload
 ↓
I/O vs CPU
 ↓
Concurrency level
 ↓
Blocking behavior
 ↓
Resource limits
 ↓
Virtual thread suitability
```

---

# 43. Common Interview Traps

### Are virtual threads faster than platform threads?

Not inherently.

They primarily make **high concurrency cheaper**.

---

### Do virtual threads eliminate thread pools?

Not necessarily.

The appropriate concurrency/resource-management strategy depends on the workload. Virtual threads change the economics of task-per-thread execution but don't remove the need to bound scarce external resources.

---

### Can virtual threads execute CPU-bound work faster?

❌ Not inherently.

CPU remains the limiting resource.

---

### Are records completely immutable?

❌ No.

They provide final component fields and value-oriented semantics, but referenced objects can remain mutable.

---

### Are sealed classes immutable?

❌ No.

Sealing controls inheritance, not object mutability.

---

### Are String Templates a finalized Java 21 feature?

❌ No.

They were a preview feature in Java 21.

---

### Is Structured Concurrency finalized in Java 21?

❌ No.

It was a preview feature in Java 21.

---

### Is `parallelStream()` replaced by virtual threads?

❌ No.

They solve different problems.

```text
parallelStream
→ parallel data processing

virtual threads
→ scalable concurrent tasks
```

---

# 44. Interviewer Follow-Up Chain

```text
What are the major Java 21 features?
        ↓
Why are virtual threads important?
        ↓
Platform thread vs virtual thread?
        ↓
How does JVM schedule virtual threads?
        ↓
What happens when a virtual thread blocks?
        ↓
Why are virtual threads good for I/O?
        ↓
Why don't they improve CPU-bound performance?
        ↓
How would you use them in Spring Boot?
        ↓
What is a record?
        ↓
Are records immutable?
        ↓
What are sealed classes?
        ↓
Why use sealed classes?
        ↓
What is pattern matching?
        ↓
What are record patterns?
        ↓
What are sequenced collections?
        ↓
What is Generational ZGC?
        ↓
Which Java 21 features are preview features?
        ↓
Java 8 vs Java 21?
        ↓
Would you migrate a production Java 8 application directly to Java 21?
        ↓
What compatibility risks would you investigate?
```

---

# 45. Common Candidate Mistakes

### ❌ Weak

> Virtual threads are lightweight threads that make Java multithreaded applications faster.

### Better

> Virtual threads are JVM-managed lightweight threads designed to support very high concurrency efficiently, particularly for tasks that spend significant time blocked on I/O. They don't increase CPU capacity and aren't inherently faster for CPU-bound computation.

---

### ❌ Weak

> Records are immutable classes.

### Better

> Records are concise, final, value-oriented data carriers with final component fields, but their referenced objects are not automatically deeply immutable.

---

### ❌ Weak

> Sealed classes prevent inheritance.

Wrong.

They **restrict which types may directly extend or implement the sealed type**.

---

# 46. 30-Second Revision

```text
JAVA 21 LTS
│
├── Virtual Threads ⭐⭐⭐⭐⭐
│      ↓
│   Massive concurrency
│   I/O-heavy workloads
│
├── Pattern Matching ⭐⭐⭐⭐⭐
│      ↓
│   Less casting/boilerplate
│
├── Record Patterns ⭐⭐⭐⭐⭐
│      ↓
│   Destructure records
│
├── Sealed Classes ⭐⭐⭐⭐⭐
│      ↓
│   Controlled inheritance
│
├── Sequenced Collections ⭐⭐⭐⭐
│      ↓
│   First / Last / Reversed
│
├── Generational ZGC ⭐⭐⭐⭐
│      ↓
│   Low-latency GC
│
├── FFM API ⭐⭐⭐
│      ↓
│   Native interoperability
│
├── Structured Concurrency ⚠️
│      ↓
│   Preview in Java 21
│
└── String Templates ⚠️
       ↓
    Preview in Java 21
```

### The most important mental model

```text
Java 8
→ Functional Java

Java 17
→ Records + Sealed Classes

Java 21
→ Scalable Concurrency + Pattern Matching
```

---

# 47. Master Interview Test

Answer without looking back:

1. What are the most important features introduced/finalized around Java 21?
2. What problem do virtual threads solve?
3. Platform threads vs virtual threads?
4. **Explain the relationship between a virtual thread, carrier thread, JVM scheduler, and OS thread.**
5. **Why are virtual threads particularly useful for I/O-bound applications but not a solution to CPU-bound scalability?**
6. What is a Java record?
7. **Are records immutable? Explain shallow vs deep immutability.**
8. What problem do sealed classes solve?
9. Explain pattern matching for `switch` and record patterns.
10. **You have a Spring Boot application handling 100,000 concurrent HTTP requests, each making database and external HTTP calls. Design how you would use Java 21 virtual threads, connection pools, timeouts, retries, caching, and observability. Explain where virtual threads help, where they do not, and what resource bottlenecks still limit system scalability.**
