# Java Concurrency

**Difficulty:** ⭐ Advanced → Expert
**Interview Relevance:** ⭐⭐⭐⭐⭐
**Category:** Java Core / JVM / Multithreading / Java Memory Model / Backend / System Design

> **Core idea:** Java concurrency is about **multiple tasks making progress concurrently while safely sharing or coordinating access to resources**. Advanced interviews focus on the **Java Memory Model (JMM), happens-before, atomicity, visibility, synchronization, locks, executors, concurrent collections, CompletableFuture, ForkJoinPool, and Java 21 virtual threads**.

---

# 0. Interview Relevance & Question Mapping

| Topic                      | Priority |
| -------------------------- | -------: |
| Process vs Thread          |    ⭐⭐⭐⭐⭐ |
| Concurrency vs Parallelism |    ⭐⭐⭐⭐⭐ |
| Thread Lifecycle           |    ⭐⭐⭐⭐⭐ |
| `Thread` API               |     ⭐⭐⭐⭐ |
| `Runnable` vs `Callable`   |    ⭐⭐⭐⭐⭐ |
| Race Conditions            |    ⭐⭐⭐⭐⭐ |
| Critical Section           |    ⭐⭐⭐⭐⭐ |
| `synchronized`             |    ⭐⭐⭐⭐⭐ |
| `volatile`                 |    ⭐⭐⭐⭐⭐ |
| Atomic Classes             |    ⭐⭐⭐⭐⭐ |
| Java Memory Model          |    ⭐⭐⭐⭐⭐ |
| Happens-Before             |    ⭐⭐⭐⭐⭐ |
| Lock / ReentrantLock       |    ⭐⭐⭐⭐⭐ |
| ReadWriteLock              |     ⭐⭐⭐⭐ |
| StampedLock                |      ⭐⭐⭐ |
| ExecutorService            |    ⭐⭐⭐⭐⭐ |
| ThreadPoolExecutor         |    ⭐⭐⭐⭐⭐ |
| CompletableFuture          |    ⭐⭐⭐⭐⭐ |
| ConcurrentHashMap          |    ⭐⭐⭐⭐⭐ |
| BlockingQueue              |    ⭐⭐⭐⭐⭐ |
| Deadlock                   |    ⭐⭐⭐⭐⭐ |
| Livelock / Starvation      |     ⭐⭐⭐⭐ |
| ForkJoinPool               |     ⭐⭐⭐⭐ |
| Virtual Threads            |    ⭐⭐⭐⭐⭐ |
| Structured Concurrency     |     ⭐⭐⭐⭐ |

---

# 1. Precise Definition

### Interview-ready answer

> **Concurrency is the design and execution of multiple tasks whose execution periods overlap, requiring coordination to ensure correctness when tasks share resources. Java provides concurrency primitives and abstractions including threads, locks, atomic variables, executors, concurrent collections, asynchronous APIs, and virtual threads.**

---

# 2. Concurrency vs Parallelism

⭐ **Classic Interview Question**

### Concurrency

Multiple tasks can make progress during overlapping periods.

```text id="b7f4dz"
Time →

Task A: ███░░███░
Task B: ░███░░███
```

### Parallelism

Multiple tasks execute **simultaneously** on different CPU cores.

```text id="h6qg1v"
Core 1 → Task A ███████
Core 2 → Task B ███████
```

### Mental model

> **Concurrency = dealing with multiple tasks.**
> **Parallelism = executing multiple tasks simultaneously.**

---

# 3. Process vs Thread

| Process                           | Thread                        |
| --------------------------------- | ----------------------------- |
| Independent execution environment | Execution unit inside process |
| Separate address space            | Shares process memory         |
| Higher creation overhead          | Lower overhead                |
| Stronger isolation                | Easier data sharing           |
| IPC required between processes    | Shared memory communication   |

Conceptually:

```text id="y2m49v"
Process
│
├── Thread 1
├── Thread 2
└── Thread 3

Shared:
├── Heap
├── Code
└── Resources
```

Each thread has its own execution state, including its own stack.

---

# 4. Why Concurrency Exists

A server may receive:

```text id="z5n7un"
Request 1
Request 2
Request 3
...
Request 10000
```

Processing sequentially:

```text id="e7jq6x"
R1 → R2 → R3 → R4
```

limits throughput.

Concurrency allows:

```text id="3tyh3k"
R1 ──────────
R2 ────────
R3 ───────────
R4 ──────
```

This is especially useful when tasks spend time waiting for:

* Database
* Network
* Files
* External APIs

---

# 5. Thread Creation

Basic:

```java id="2j4yqj"
Thread thread = new Thread(() -> {
    System.out.println("Running");
});

thread.start();
```

### Critical

Use:

```java id="i7l3tu"
thread.start();
```

not:

```java id="xqjj6s"
thread.run();
```

`start()` creates/schedules a new thread of execution.

Calling `run()` directly is simply a normal method invocation on the current thread.

---

# 6. Thread Lifecycle

Conceptually:

```text id="a5r6yr"
NEW
 ↓
RUNNABLE
 ↓
RUNNING
 ↓
BLOCKED / WAITING / TIMED_WAITING
 ↓
RUNNABLE
 ↓
TERMINATED
```

Java's `Thread.State` includes:

```text id="8f4wte"
NEW
RUNNABLE
BLOCKED
WAITING
TIMED_WAITING
TERMINATED
```

### Important

Java's `RUNNABLE` state encompasses both ready-to-run and actually running states from the JVM's perspective.

---

# 7. `sleep()` vs `wait()`

⭐ **Frequently Asked**

### `Thread.sleep()`

```java id="2h8w8c"
Thread.sleep(1000);
```

* Pauses the current thread for a period.
* Does **not** release monitors it already owns.

### `Object.wait()`

```java id="xq2m5g"
synchronized (lock) {
    lock.wait();
}
```

* Releases the monitor associated with that object.
* Waits until notified/interrupted or otherwise awakened.
* Must be called while owning the object's monitor.

---

# 8. `wait()`, `notify()`, `notifyAll()`

Classic monitor coordination:

```java id="x0mmf3"
synchronized (lock) {

    while (!condition) {
        lock.wait();
    }

    // use shared state
}
```

Producer:

```java id="w6ybpq"
synchronized (lock) {
    updateState();
    lock.notifyAll();
}
```

### Why `while`, not `if`?

Because a thread must re-check the condition after waking.

```text id="c1dr8j"
wait
 ↓
wake up
 ↓
re-check condition
 ↓
continue only if condition is true
```

---

# 9. Runnable vs Callable

### Runnable

```java id="3n8ghw"
Runnable task = () -> {
    process();
};
```

Conceptually:

```text id="2r5hvi"
input → task → no return value
```

### Callable

```java id="8xj3f2"
Callable<Integer> task =
        () -> calculate();
```

Conceptually:

```text id="ddv6yz"
task → result / exception
```

`Callable` works naturally with `Future`.

---

# 10. Race Condition

⭐ **Must-Know**

Suppose:

```java id="q7f8dr"
count++;
```

It looks like one operation but conceptually involves:

```text id="15z2ja"
read count
   ↓
add 1
   ↓
write count
```

Two threads:

```text id="k0ax8k"
Thread A       Thread B

read 0         read 0
+1             +1
write 1        write 1
```

Expected:

```text id="b4t4s2"
2
```

Actual:

```text id="9t6c7f"
1
```

This is a **lost update** caused by a race condition.

---

# 11. Critical Section

A critical section is code that accesses shared mutable state and must be protected from unsafe concurrent execution.

```java id="yp2zrr"
synchronized void increment() {
    count++;
}
```

Conceptually:

```text id="43yqzh"
Thread A
   ↓
LOCK
   ↓
count++
   ↓
UNLOCK

Thread B
   ↓
wait
```

---

# 12. `synchronized`

⭐ **Must-Know**

`synchronized` provides:

```text id="x8d1p3"
Mutual exclusion
+
Memory visibility
+
Happens-before guarantees
```

Example:

```java id="i0g4vh"
public synchronized void increment() {
    count++;
}
```

Only one thread at a time can execute synchronized instance methods protected by the same object monitor.

---

# 13. Synchronized Method vs Block

### Method

```java id="z8d3hl"
public synchronized void update() {
    // ...
}
```

Locks:

```text id="7c0w6r"
this
```

for an instance method.

### Block

```java id="75rc1b"
synchronized (lock) {
    // critical section
}
```

Locks the specific object referenced by `lock`.

### Why block is often preferable

It allows a smaller critical section:

```text id="3j0e7q"
Non-critical work
      ↓
LOCK
      ↓
Critical work
      ↓
UNLOCK
      ↓
Non-critical work
```

This can reduce contention.

---

# 14. Static Synchronized Method

```java id="m0v0m2"
static synchronized void update() {
}
```

This synchronizes on the `Class` object associated with the class:

```text id="w7e9j8"
MyClass.class
```

This differs from:

```java id="q8q0y6"
synchronized void update()
```

which synchronizes on the instance.

---

# 15. `volatile`

⭐ **Very Frequently Asked**

Example:

```java id="w6jz1y"
private volatile boolean running = true;
```

`volatile` provides visibility and ordering guarantees under the Java Memory Model.

Conceptually:

```text id="s3n2w9"
Thread A
running = false
      ↓
Memory visibility
      ↓
Thread B sees false
```

---

# 16. Why `volatile` Doesn't Solve `count++`

```java id="74pl9f"
volatile int count;

count++;
```

Still not atomic.

Because:

```text id="2zy6y7"
READ
 ↓
ADD
 ↓
WRITE
```

Multiple threads can interleave these operations.

Use:

```java id="h2v3z4"
AtomicInteger
```

or synchronization/locking.

---

# 17. Atomic Classes

Package:

```java id="5q8jtl"
java.util.concurrent.atomic
```

Examples:

```text id="6r9s5w"
AtomicInteger
AtomicLong
AtomicBoolean
AtomicReference
```

Example:

```java id="j5kj6f"
AtomicInteger count =
        new AtomicInteger(0);

count.incrementAndGet();
```

The operation is atomic.

---

# 18. CAS

⭐ **Advanced**

Many atomic classes use **Compare-And-Set (CAS)**.

Conceptually:

```text id="a4b0ck"
Current value = 10

Expected = 10
New value = 11

CAS:
if current == expected
    current = new
```

If another thread changed the value:

```text id="a5hy1g"
CAS fails
 ↓
Retry
```

This supports lock-free algorithms for suitable operations.

---

# 19. Atomic vs `synchronized`

| Atomic                                | `synchronized`                        |
| ------------------------------------- | ------------------------------------- |
| Often uses CAS                        | Monitor-based mutual exclusion        |
| Excellent for simple atomic state     | Better for compound critical sections |
| Can avoid blocking in some operations | Threads may block                     |
| Great for counters/state              | Great for multi-variable invariants   |

Do not assume atomic classes are always faster. The right choice depends on contention and workload.

---

# 20. Java Memory Model

⭐⭐⭐⭐⭐ **Expert-level topic**

The JMM defines how threads interact with memory and establishes rules for:

```text id="n8e5rq"
Visibility
Ordering
Atomicity
Happens-before
```

Without a memory model:

```text id="4t7v9k"
Thread A
write

Thread B
read
```

could behave unpredictably due to:

* CPU caches
* Compiler optimizations
* JIT transformations
* CPU memory ordering

---

# 21. Happens-Before

⭐ **Extremely Important**

If operation A **happens-before** operation B, then the Java Memory Model guarantees the required visibility and ordering relationship between them.

Important examples:

```text id="l2j2r7"
Unlock
 ↓
subsequent lock on same monitor
```

```text id="kqv7v8"
volatile write
 ↓
subsequent volatile read
```

```text id="3f4f7z"
Thread.start()
 ↓
actions in started thread
```

```text id="cb3xgk"
Actions in thread
 ↓
successful join()
```

---

# 22. Lock

Java provides explicit locks through:

```java id="3p5qz4"
java.util.concurrent.locks.Lock
```

Example:

```java id="q6r0a2"
Lock lock = new ReentrantLock();

lock.lock();

try {
    count++;
} finally {
    lock.unlock();
}
```

### Important

Always release the lock in `finally`.

---

# 23. `ReentrantLock`

A `ReentrantLock` allows the same thread to acquire the same lock multiple times.

```text id="p0xv9r"
Thread A
 ↓
lock()
 ↓
lock()
 ↓
unlock()
 ↓
unlock()
```

The lock maintains reentrant acquisition state.

---

# 24. `synchronized` vs `ReentrantLock`

| `synchronized`                 | `ReentrantLock`                                       |
| ------------------------------ | ----------------------------------------------------- |
| Simpler                        | More flexible                                         |
| Automatic unlock               | Manual unlock                                         |
| No explicit lock object needed | Explicit Lock                                         |
| Less error-prone               | More advanced features                                |
| Basic monitor semantics        | `tryLock`, interruptible lock acquisition, conditions |

Use `synchronized` when its semantics are sufficient.

Use `ReentrantLock` when you genuinely need advanced lock capabilities.

---

# 25. `tryLock()`

Instead of waiting indefinitely:

```java id="b6f6u4"
if (lock.tryLock()) {
    try {
        // work
    } finally {
        lock.unlock();
    }
}
```

The thread can continue if the lock isn't immediately available.

Timed version:

```java id="d2a4jp"
lock.tryLock(1, TimeUnit.SECONDS);
```

---

# 26. ReadWriteLock

Useful when:

```text id="5j9x0z"
Many readers
Few writers
```

```java id="w8n8y6"
ReadWriteLock lock =
        new ReentrantReadWriteLock();
```

Conceptually:

```text id="qf8w7v"
Readers
 R1 ─┐
 R2 ─┼──→ Read Lock
 R3 ─┘

Writer
 W1 ───→ Write Lock
```

Multiple readers can often proceed concurrently, while writers require exclusive access.

---

# 27. StampedLock

`StampedLock` provides:

```text id="q7w9mt"
Read lock
Write lock
Optimistic read
```

Optimistic reading can reduce locking overhead for certain read-heavy workloads, but correctness requires validation.

It is more complex than `ReentrantReadWriteLock`.

---

# 28. ExecutorService

⭐ **Must-Know**

Instead of manually creating threads:

```java id="u1n3f5"
ExecutorService executor =
        Executors.newFixedThreadPool(4);
```

Submit tasks:

```java id="ypg8q0"
executor.submit(() -> process());
```

Shutdown:

```java id="s4e7kg"
executor.shutdown();
```

Mental model:

```text id="0ezw0f"
Tasks
 ↓
Executor
 ↓
Worker Threads
 ↓
Execution
```

---

# 29. Why Use Thread Pools?

Creating a thread for every task can be expensive.

Thread pools provide:

```text id="b5e1v8"
Thread reuse
Concurrency control
Task queueing
Lifecycle management
```

Instead of:

```text id="11n9u8"
1000 tasks
 ↓
1000 threads
```

you can use:

```text id="b2myqy"
1000 tasks
 ↓
Thread Pool
 ↓
Fixed number of workers
```

---

# 30. ThreadPoolExecutor

⭐ **Advanced**

A `ThreadPoolExecutor` manages:

```text id="3y4w7z"
Core threads
Maximum threads
Keep-alive time
Work queue
Thread factory
Rejected execution handler
```

Conceptually:

```text id="f5f5nm"
Incoming Tasks
      ↓
   Work Queue
      ↓
Worker Threads
      ↓
Execution
```

---

# 31. Thread Pool Sizing

### CPU-bound

Often start around:

```text id="v2p4yk"
number of CPU cores
```

and benchmark.

### I/O-bound

Can use more concurrency because threads spend time waiting, but the correct size depends on:

```text id="uw7vhm"
I/O latency
CPU
Memory
Downstream limits
Throughput
```

There is no universal magic number.

---

# 32. Future

A `Future` represents the result of an asynchronous computation.

```java id="u0l8l2"
Future<Integer> future =
        executor.submit(() -> calculate());
```

Retrieve:

```java id="yq6m4n"
Integer result = future.get();
```

Potential problem:

```text id="5r5l1h"
get()
 ↓
blocks
```

---

# 33. CompletableFuture

⭐ **Must-Know**

Provides composable asynchronous computation.

```java id="jv6m9s"
CompletableFuture
    .supplyAsync(() -> fetchUser())
    .thenApply(User::getName)
    .thenAccept(System.out::println);
```

Pipeline:

```text id="9v5lbn"
Async task
   ↓
Transform
   ↓
Consume
```

---

# 34. `thenApply()` vs `thenCompose()`

### `thenApply`

```java id="s0j9qf"
future.thenApply(user ->
    user.getName()
);
```

Transforms:

```text id="0dy3y7"
Future<A>
 ↓
A → B
 ↓
Future<B>
```

### `thenCompose`

Used when the next function itself returns a future:

```text id="f4v3p6"
Future<A>
 ↓
A → Future<B>
 ↓
Future<B>
```

It prevents nested:

```text id="7j0zjp"
Future<Future<B>>
```

---

# 35. `thenCombine()`

Combine independent futures:

```java id="4q0m2k"
userFuture.thenCombine(
    orderFuture,
    (user, orders) ->
        new UserOrders(user, orders)
);
```

Conceptually:

```text id="o2f6t4"
User Future ──────┐
                  ├──→ Combined Result
Order Future ─────┘
```

---

# 36. BlockingQueue

⭐ **Important**

A `BlockingQueue` supports producer-consumer coordination.

```text id="7b0v5n"
Producer
   ↓
BlockingQueue
   ↓
Consumer
```

Example:

```java id="a9o2j4"
BlockingQueue<Task> queue =
        new LinkedBlockingQueue<>();
```

Producer:

```java id="c2y0yq"
queue.put(task);
```

Consumer:

```java id="7u8l9m"
Task task = queue.take();
```

If the queue is empty:

```text id="grn4qh"
take()
 ↓
wait
```

---

# 37. Producer-Consumer Pattern

```text id="p9x0af"
Producer 1 ─┐
Producer 2 ─┼──→ Queue ──→ Consumer 1
Producer 3 ─┘             Consumer 2
```

Advantages:

```text id="6h8t6c"
Decoupling
Backpressure
Controlled concurrency
```

---

# 38. ConcurrentHashMap

For concurrent key-value access:

```java id="q0u2z8"
ConcurrentHashMap<String, Integer> map =
        new ConcurrentHashMap<>();
```

Useful methods:

```java id="d3v4ah"
putIfAbsent()
compute()
computeIfAbsent()
merge()
```

These methods help implement atomic map-level operations.

---

# 39. Deadlock

⭐ **Very Frequently Asked**

Deadlock occurs when threads wait indefinitely for resources held by one another.

Example:

```text id="d7v8z1"
Thread A
  holds Lock 1
  waits for Lock 2

Thread B
  holds Lock 2
  waits for Lock 1
```

```text id="z0t1vy"
       Lock 1
      ↙      ↘
    A          B
     ↘        ↙
       Lock 2
```

Neither can proceed.

---

# 40. Four Coffman Conditions

Deadlock requires the classic conditions:

```text id="9zq2y3"
1. Mutual exclusion
2. Hold and wait
3. No preemption
4. Circular wait
```

Break one condition to prevent deadlock.

---

# 41. Deadlock Prevention

One strong technique:

> **Always acquire multiple locks in a globally consistent order.**

Bad:

```text id="d4u8w0"
Thread A:
Lock A → Lock B

Thread B:
Lock B → Lock A
```

Better:

```text id="j1f4tk"
Both:
Lock A → Lock B
```

Then circular wait is avoided.

---

# 42. Livelock

Threads are active but make no useful progress.

Example:

```text id="7pl3gk"
Thread A
 "I'll move"

Thread B
 "I'll move"

Thread A
 "No, you move"

Thread B
 "No, you move"
```

Unlike deadlock:

```text id="b8x2n6"
Deadlock → blocked
Livelock → active but ineffective
```

---

# 43. Starvation

A thread waits indefinitely because other threads repeatedly get access to required resources.

Example:

```text id="s7x2q5"
High-priority/frequently scheduled tasks
        ↓
Resource
        ↓
Low-priority task
        ↓
Never gets enough opportunity
```

Possible causes:

* Unfair locking
* Poor scheduling
* Long critical sections

---

# 44. Atomicity vs Visibility vs Ordering

⭐ **Expert Interview Topic**

These are different properties.

### Atomicity

Operation appears indivisible.

```text id="a6j9fr"
count++
```

is not automatically atomic.

### Visibility

One thread sees another thread's update.

### Ordering

Operations appear in an allowed ordering under the memory model.

```text id="l4q8r2"
Concurrency correctness
├── Atomicity
├── Visibility
└── Ordering
```

---

# 45. Thread Safety

A component is thread-safe if it maintains its correctness under the intended concurrent usage.

Example:

```java id="8m1wz0"
AtomicInteger counter =
        new AtomicInteger();

counter.incrementAndGet();
```

Thread safety does not necessarily mean:

```text id="1u5b1c"
"no locks"
```

or:

```text id="b7x0w9"
"immutable"
```

There are many ways to achieve thread safety.

---

# 46. Immutability

One of the simplest concurrency strategies is immutable state.

Example:

```java id="l6j2s8"
public record User(
    String name,
    int age
) {}
```

If the referenced state is also appropriately immutable:

```text id="q5k7b3"
No shared mutation
       ↓
Less synchronization
       ↓
Simpler concurrency
```

---

# 47. Java 21 Virtual Threads

⭐⭐⭐⭐⭐ **Critical Modern Java Topic**

Java 21 provides virtual threads:

```java id="1v9g9u"
Thread.startVirtualThread(() -> {
    processRequest();
});
```

Or:

```java id="q1x7pa"
try (ExecutorService executor =
         Executors.newVirtualThreadPerTaskExecutor()) {

    executor.submit(() -> processRequest());
}
```

Architecture:

```text id="8d2x3y"
Many Virtual Threads
       ↓
JVM Scheduler
       ↓
Carrier Platform Threads
       ↓
CPU
```

---

# 48. Why Virtual Threads Matter

Traditional:

```text id="x6g4u7"
100,000 requests
      ↓
100,000 platform threads
      ↓
Expensive
```

Virtual threads:

```text id="q7s9x4"
100,000 concurrent tasks
      ↓
100,000 virtual threads
      ↓
Small carrier-thread pool
```

Especially useful for:

```text id="4t9v5w"
I/O-bound workloads
HTTP services
Database calls
Network operations
```

---

# 49. Virtual Threads Do Not Increase CPU

⭐ **Interview Trap**

If your workload is:

```java id="z2y7m4"
while (true) {
    performHeavyCalculation();
}
```

virtual threads do not create additional CPU cores.

```text id="9v4q6f"
CPU-bound
   ↓
CPU capacity is bottleneck
```

Virtual threads primarily improve **concurrency scalability**, especially when tasks spend substantial time waiting.

---

# 50. Concurrency Architecture

A modern Java backend might look like:

```text id="7m4q2a"
             Client Requests
                    │
                    ↓
             Spring Boot
                    │
            Virtual Threads
                    │
        ┌───────────┼───────────┐
        ↓           ↓           ↓
     Service A   Service B   Service C
        │           │           │
        ↓           ↓           ↓
      DB          Redis        Kafka
```

Concurrency mechanisms are selected according to the workload:

```text id="8f1s5d"
CPU-heavy
→ parallelism / ForkJoinPool

I/O-heavy
→ virtual threads / async I/O

Shared state
→ locks / atomics / concurrent collections

Pipeline
→ CompletableFuture / queues
```

---

# 51. Common Interview Traps

### Does `start()` execute the thread immediately?

It starts a new thread of execution and makes it eligible for scheduling; exact execution timing is scheduler-dependent.

---

### Does `run()` create a new thread?

❌ No.

It is a normal method call.

---

### Does `volatile` guarantee atomicity?

❌ No.

---

### Does `synchronized` only provide mutual exclusion?

❌ No.

It also provides memory visibility and happens-before guarantees.

---

### Is `ConcurrentHashMap` completely lock-free?

❌ Don't describe it that way.

Its implementation combines different concurrency mechanisms; the API is designed for safe concurrent access.

---

### Are virtual threads faster than platform threads?

Not inherently.

Their key benefit is **cheap high concurrency**.

---

### Does concurrency always improve performance?

❌ No.

Concurrency can introduce:

```text
Lock contention
Context switching
Coordination overhead
Cache contention
Complexity
```

---

# 52. Interviewer Follow-Up Chain

```text id="5l6c0p"
What is concurrency?
      ↓
Concurrency vs parallelism?
      ↓
Process vs thread?
      ↓
Thread lifecycle?
      ↓
start() vs run()?
      ↓
Race condition?
      ↓
Critical section?
      ↓
synchronized?
      ↓
volatile?
      ↓
Why isn't volatile count++ atomic?
      ↓
AtomicInteger?
      ↓
CAS?
      ↓
Java Memory Model?
      ↓
Happens-before?
      ↓
ReentrantLock?
      ↓
synchronized vs Lock?
      ↓
ExecutorService?
      ↓
ThreadPoolExecutor?
      ↓
Future vs CompletableFuture?
      ↓
BlockingQueue?
      ↓
ConcurrentHashMap?
      ↓
Deadlock?
      ↓
Livelock?
      ↓
Starvation?
      ↓
Virtual threads?
      ↓
When should virtual threads NOT be used?
```

---

# 53. Common Candidate Mistakes

### ❌ Weak

> Concurrency means multiple threads running simultaneously.

### Better

> Concurrency means multiple tasks can make progress during overlapping periods; actual simultaneous execution is parallelism.

---

### ❌ Weak

> `volatile` makes a variable thread-safe.

### Better

> `volatile` provides visibility and ordering guarantees, but it does not make compound operations such as `count++` atomic.

---

### ❌ Weak

> `synchronized` makes the whole application thread-safe.

### Better

> `synchronized` protects the specific critical sections guarded by the same monitor; overall thread safety still depends on the complete shared-state design.

---

### ❌ Weak

> Virtual threads replace thread pools.

### Better

> Virtual threads change the cost model of task-per-thread concurrency, but executors and resource limits are still relevant. For example, `newVirtualThreadPerTaskExecutor()` provides an executor that creates a virtual thread per task.

---

# 54. 30-Second Revision

```text id="9d3g5x"
JAVA CONCURRENCY
│
├── Threads
│
├── Shared State
│      ↓
│   Race Conditions
│
├── Synchronization
│   ├── synchronized
│   ├── Lock
│   └── ReadWriteLock
│
├── Memory Model
│   ├── Visibility
│   ├── Atomicity
│   ├── Ordering
│   └── Happens-Before
│
├── Atomic Classes
│   └── CAS
│
├── Executors
│   ├── ExecutorService
│   └── ThreadPoolExecutor
│
├── Async
│   └── CompletableFuture
│
├── Concurrent Collections
│   ├── ConcurrentHashMap
│   └── BlockingQueue
│
├── Failure Modes
│   ├── Deadlock
│   ├── Livelock
│   └── Starvation
│
└── Java 21
    └── Virtual Threads
```

### Golden mental model

```text id="4j8m3z"
Multiple Tasks
      ↓
Shared Resources?
      ↓
Yes
      ↓
Need correctness
      ↓
Atomicity + Visibility + Ordering
      ↓
Choose mechanism
 ┌─────────────┬──────────────┬─────────────┐
 ↓             ↓              ↓
Atomic      Locking      Concurrent DS
 ↓             ↓              ↓
CAS        synchronized    CHM / Queue
      \          |          /
       └─────── JMM ───────┘
```

---

# 55. Master Interview Test

Answer without looking back:

1. What is concurrency?
2. Concurrency vs parallelism?
3. Process vs thread?
4. Explain the Java thread lifecycle.
5. `start()` vs `run()`?
6. What is a race condition?
7. **Why is `count++` not atomic, even when `count` is `volatile`?**
8. **Explain the Java Memory Model and happens-before relationship with a concrete example involving `volatile`, `synchronized`, or `Thread.start()`.**
9. **Design a thread-safe high-throughput counter/service using `AtomicInteger`, `ReentrantLock`, `ConcurrentHashMap`, and `ExecutorService`. Explain why you would choose each mechanism.**
10. **You are designing a Java 21 Spring Boot API receiving 100,000 concurrent requests. Each request performs database and external HTTP I/O, while some background tasks are CPU-intensive. Design the concurrency model using virtual threads, executor boundaries, connection pools, `CompletableFuture` where appropriate, synchronization mechanisms, timeouts, and backpressure. Explain why each mechanism is used and identify the likely bottlenecks.**
