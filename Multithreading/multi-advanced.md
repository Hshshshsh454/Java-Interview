# Java Multithreading

**Difficulty:** ⭐ Advanced → Expert
**Interview Relevance:** ⭐⭐⭐⭐⭐
**Category:** Java Core / Concurrency / JVM / Operating Systems / Java Memory Model

> **Core idea:** Multithreading is the execution of multiple threads within a process. In Java, threads share the process heap while maintaining independent execution stacks. Advanced interviews focus on **thread lifecycle, scheduling, synchronization, race conditions, Java Memory Model, locks, executors, thread pools, concurrent collections, asynchronous programming, and virtual threads**.

---

# 0. Interview Relevance & Question Mapping

| Topic                           | Priority |
| ------------------------------- | -------: |
| Process vs Thread               |    ⭐⭐⭐⭐⭐ |
| Multithreading vs Concurrency   |    ⭐⭐⭐⭐⭐ |
| Concurrency vs Parallelism      |    ⭐⭐⭐⭐⭐ |
| Thread lifecycle                |    ⭐⭐⭐⭐⭐ |
| Creating threads                |    ⭐⭐⭐⭐⭐ |
| `start()` vs `run()`            |    ⭐⭐⭐⭐⭐ |
| `Runnable` vs `Callable`        |    ⭐⭐⭐⭐⭐ |
| Thread states                   |    ⭐⭐⭐⭐⭐ |
| `sleep()` / `wait()` / `join()` |    ⭐⭐⭐⭐⭐ |
| Race condition                  |    ⭐⭐⭐⭐⭐ |
| Synchronization                 |    ⭐⭐⭐⭐⭐ |
| `volatile`                      |    ⭐⭐⭐⭐⭐ |
| Atomic classes                  |    ⭐⭐⭐⭐⭐ |
| Java Memory Model               |    ⭐⭐⭐⭐⭐ |
| Happens-before                  |    ⭐⭐⭐⭐⭐ |
| ExecutorService                 |    ⭐⭐⭐⭐⭐ |
| ThreadPoolExecutor              |    ⭐⭐⭐⭐⭐ |
| CompletableFuture               |    ⭐⭐⭐⭐⭐ |
| Deadlock                        |    ⭐⭐⭐⭐⭐ |
| ConcurrentHashMap               |    ⭐⭐⭐⭐⭐ |
| ForkJoinPool                    |     ⭐⭐⭐⭐ |
| Virtual Threads                 |    ⭐⭐⭐⭐⭐ |

---

# 1. Precise Definition

### Interview-ready answer

> **Multithreading is a programming technique in which multiple threads execute within the same process, allowing independent tasks to make concurrent progress and potentially execute in parallel on multiple CPU cores.**

A Java process can contain:

```text
Process
│
├── Thread 1
├── Thread 2
├── Thread 3
└── Thread 4
```

Threads generally share:

```text
Heap
Static/class data
Process resources
```

but each thread has its own:

```text
Stack
Program counter
Execution state
```

---

# 2. Why Does Multithreading Exist?

Consider a server processing requests:

```text
Without concurrency:

Request 1
   ↓
Request 2
   ↓
Request 3
   ↓
Request 4
```

A slow request can delay everything behind it.

With multiple threads:

```text
Request 1 ───────────
Request 2 ───────
Request 3 ─────────────
Request 4 ──────
```

This can improve:

* Responsiveness
* Throughput
* Resource utilization
* I/O concurrency
* CPU utilization for parallel workloads

---

# 3. Multithreading vs Concurrency vs Parallelism

⭐ **Frequently Asked**

### Multithreading

Multiple threads exist within a process.

### Concurrency

Multiple tasks can make progress during overlapping periods.

### Parallelism

Multiple tasks execute simultaneously.

```text
Concurrency:

CPU
 ↓
A → B → A → C → B


Parallelism:

Core 1 → A A A A
Core 2 → B B B B
```

### Mental model

> **Multithreading is a mechanism; concurrency is a property of execution; parallelism is simultaneous execution.**

---

# 4. Process vs Thread

| Process                           | Thread                        |
| --------------------------------- | ----------------------------- |
| Independent execution environment | Execution unit inside process |
| Separate address space            | Shares process memory         |
| Higher overhead                   | Lower overhead                |
| Strong isolation                  | Easier communication          |
| IPC needed for communication      | Shared-memory communication   |

Architecture:

```text
Process
│
├── Heap
├── Code
├── Resources
│
├── Thread A → Stack A
├── Thread B → Stack B
└── Thread C → Stack C
```

---

# 5. Thread Memory Model

A simplified representation:

```text
Process
│
├── Heap
│   ├── Object A
│   ├── Object B
│   └── Object C
│
├── Class Metadata
│
├── Thread 1
│   └── Stack 1
│
├── Thread 2
│   └── Stack 2
│
└── Thread 3
    └── Stack 3
```

### Important

Threads share heap objects.

Therefore:

```text
Shared mutable heap state
        ↓
Potential race condition
```

---

# 6. Creating a Thread

## Method 1 — Extend `Thread`

```java
class MyThread extends Thread {

    @Override
    public void run() {
        System.out.println("Running");
    }
}
```

Start:

```java
MyThread thread = new MyThread();
thread.start();
```

---

# 7. Method 2 — Implement `Runnable`

⭐ **Preferred for basic platform-thread tasks**

```java
class Task implements Runnable {

    @Override
    public void run() {
        System.out.println("Running");
    }
}
```

Usage:

```java
Thread thread =
        new Thread(new Task());

thread.start();
```

Better separation:

```text
Task
 ↓
What should be executed?

Thread
 ↓
How should it execute?
```

---

# 8. Lambda-Based Thread

Because `Runnable` is a functional interface:

```java
Thread thread =
    new Thread(() -> {
        System.out.println("Running");
    });

thread.start();
```

---

# 9. `start()` vs `run()`

⭐ **Very Frequently Asked**

### Correct

```java
thread.start();
```

This requests the JVM to start a new thread of execution.

### Not equivalent

```java
thread.run();
```

This is simply a normal method call.

```text
start()
  ↓
New execution thread
  ↓
run()


run()
  ↓
Current thread
```

---

# 10. Thread Lifecycle

Java exposes these `Thread.State` values:

```text
NEW
RUNNABLE
BLOCKED
WAITING
TIMED_WAITING
TERMINATED
```

Conceptually:

```text
NEW
 ↓
start()
 ↓
RUNNABLE
 ├── BLOCKED
 ├── WAITING
 └── TIMED_WAITING
 ↓
RUNNABLE
 ↓
TERMINATED
```

---

# 11. `RUNNABLE` State

A common misconception is:

> `RUNNABLE` means the thread is currently executing on the CPU.

Not necessarily.

Java's `RUNNABLE` state includes threads that are:

```text
Ready to run
+
Actually running
```

The OS scheduler ultimately determines when a platform thread gets CPU time.

---

# 12. `BLOCKED`

A thread becomes `BLOCKED` when waiting to acquire an intrinsic monitor.

Example:

```java
synchronized (lock) {
    // critical section
}
```

If another thread owns `lock`:

```text
Thread A
 ↓
owns lock

Thread B
 ↓
BLOCKED
 ↓
waiting for lock
```

---

# 13. `WAITING`

Examples:

```java
thread.join();
```

or:

```java
object.wait();
```

or certain synchronizer operations.

The thread waits indefinitely until another event causes it to become eligible to continue.

---

# 14. `TIMED_WAITING`

Examples:

```java
Thread.sleep(1000);
```

or:

```java
thread.join(1000);
```

or:

```java
object.wait(1000);
```

The waiting operation has a time limit.

---

# 15. `sleep()`

```java
Thread.sleep(1000);
```

The current thread pauses for approximately the requested duration.

Important:

> `sleep()` does **not** release monitors held by the thread.

---

# 16. `wait()`

```java
synchronized (lock) {
    lock.wait();
}
```

When `wait()` is called:

```text
Thread
 ↓
Releases lock monitor
 ↓
WAITING
```

It is primarily a coordination mechanism.

---

# 17. `sleep()` vs `wait()`

| `sleep()`                        | `wait()`                   |
| -------------------------------- | -------------------------- |
| `Thread` method                  | `Object` method            |
| Used for timed pause             | Used for coordination      |
| Does not release monitors        | Releases object's monitor  |
| Doesn't require owning a monitor | Must own object's monitor  |
| Timed operation                  | Can be indefinite or timed |

---

# 18. `join()`

`join()` allows one thread to wait for another thread to terminate.

```java
Thread worker = new Thread(() -> {
    process();
});

worker.start();

worker.join();

System.out.println("Worker finished");
```

Conceptually:

```text
Main Thread
    │
    ├── start Worker
    │
    ├── join()
    │      ↓
    │    WAITING
    │
    └── continue after Worker terminates
```

---

# 19. Thread Priority

Java provides:

```java
thread.setPriority(...);
```

with priorities from:

```text
Thread.MIN_PRIORITY
Thread.NORM_PRIORITY
Thread.MAX_PRIORITY
```

However:

> Thread priority should **not** be used as a correctness mechanism.

Scheduling behavior is OS/JVM dependent.

---

# 20. Race Condition

⭐ **Must-Know**

Suppose:

```java
int count = 0;

void increment() {
    count++;
}
```

`count++` conceptually involves:

```text
READ
 ↓
ADD
 ↓
WRITE
```

Two threads:

```text
Thread A       Thread B

READ 0         READ 0
ADD            ADD
WRITE 1        WRITE 1
```

Final:

```text
1
```

Expected:

```text
2
```

This is a race condition.

---

# 21. Critical Section

The code accessing shared mutable state is the critical section.

```java
synchronized void increment() {
    count++;
}
```

Conceptually:

```text
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

Only one thread executes the protected section at a time for the same monitor.

---

# 22. Synchronization

Java provides:

```java
synchronized
```

Example:

```java
class Counter {

    private int count;

    public synchronized void increment() {
        count++;
    }
}
```

Synchronization provides:

```text
Mutual Exclusion
+
Visibility
+
Ordering / Happens-Before
```

---

# 23. Synchronized Block

Instead of locking an entire method:

```java
public synchronized void process() {
}
```

use:

```java
public void process() {

    doSomething();

    synchronized (lock) {
        updateSharedState();
    }

    doSomethingElse();
}
```

This can reduce contention because the lock is held for a smaller region.

---

# 24. Instance vs Static Synchronization

### Instance

```java
synchronized void method() {
}
```

Locks:

```java
this
```

### Static

```java
static synchronized void method() {
}
```

Locks:

```java
MyClass.class
```

Therefore:

```text
Instance synchronized
→ object monitor

Static synchronized
→ class monitor
```

---

# 25. `volatile`

Example:

```java
private volatile boolean running = true;
```

`volatile` provides important visibility and ordering guarantees.

But:

```java
volatile int count;

count++;
```

is still not atomic.

Why?

```text
READ
 ↓
ADD
 ↓
WRITE
```

Use `AtomicInteger` or synchronization when atomic compound updates are required.

---

# 26. AtomicInteger

```java
AtomicInteger count =
        new AtomicInteger(0);

count.incrementAndGet();
```

Common atomic classes:

```text
AtomicInteger
AtomicLong
AtomicBoolean
AtomicReference
```

They are useful for simple atomic state transitions.

---

# 27. CAS

⭐ **Advanced**

Atomic classes often rely on **Compare-And-Set** operations.

Conceptually:

```text
Current = 10

Expected = 10
New = 11

CAS:
if Current == Expected
    Current = New
```

If another thread changes the value:

```text
CAS fails
 ↓
retry / take alternative path
```

---

# 28. Java Memory Model

⭐⭐⭐⭐⭐ **Expert**

The Java Memory Model defines how threads interact with memory.

It addresses:

```text
Atomicity
Visibility
Ordering
```

Without appropriate synchronization, one thread's writes may not be observed by another thread in the way the program logically expects.

The JMM provides formal guarantees through mechanisms such as:

```text
synchronized
volatile
Locks
Thread.start()
Thread.join()
Atomic operations
```

---

# 29. Happens-Before

⭐ **Critical Interview Concept**

If A happens-before B, the JMM establishes the necessary visibility and ordering relationship from A to B.

Examples:

```text
Thread.start()
     ↓
actions in started thread
```

and:

```text
actions in thread
     ↓
successful join()
```

and:

```text
unlock monitor
     ↓
subsequent lock of same monitor
```

and:

```text
volatile write
     ↓
subsequent volatile read
```

---

# 30. ExecutorService

Creating threads manually for every task is usually not the best architecture.

Instead:

```java
ExecutorService executor =
    Executors.newFixedThreadPool(4);
```

Submit:

```java
executor.submit(() -> process());
```

Shutdown:

```java
executor.shutdown();
```

Architecture:

```text
Tasks
 ↓
ExecutorService
 ↓
Worker Threads
 ↓
Execution
```

---

# 31. Why Thread Pools?

Suppose:

```text
10,000 tasks
```

Creating:

```text
10,000 platform threads
```

can cause excessive:

* Memory usage
* Scheduling overhead
* Context switching
* Resource contention

A pool limits concurrency:

```text
10,000 tasks
      ↓
Queue
      ↓
10 worker threads
```

---

# 32. `ThreadPoolExecutor`

⭐ **Advanced**

The underlying executor model can involve:

```text
Core Pool Size
Maximum Pool Size
Work Queue
Keep-Alive Time
Thread Factory
Rejected Execution Handler
```

Conceptually:

```text
Incoming Tasks
      ↓
   Work Queue
      ↓
Worker Threads
      ↓
Execution
```

---

# 33. CPU-Bound vs I/O-Bound

⭐ **Frequently Asked**

### CPU-bound

Examples:

```text
Image processing
Encryption
Complex calculations
Machine learning computation
```

The bottleneck is CPU.

Concurrency should generally be constrained around available CPU resources and benchmarked.

### I/O-bound

Examples:

```text
Database
HTTP calls
File I/O
Network operations
```

Threads spend substantial time waiting.

Higher concurrency may be useful, but downstream resources such as database connections can become the actual bottleneck.

---

# 34. Runnable vs Callable

### Runnable

```java
Runnable task = () -> {
    process();
};
```

No returned result.

### Callable

```java
Callable<Integer> task =
    () -> calculate();
```

Returns a result and can throw checked exceptions.

Usually used with:

```java
Future<Integer>
```

---

# 35. Future

```java
Future<Integer> future =
    executor.submit(() -> calculate());
```

Get result:

```java
Integer result = future.get();
```

Problem:

```text
get()
 ↓
may block
```

---

# 36. CompletableFuture

⭐ **Must-Know**

Supports composable asynchronous operations.

```java
CompletableFuture
    .supplyAsync(() -> fetchUser())
    .thenApply(User::getName)
    .thenAccept(System.out::println);
```

Pipeline:

```text
Async Task
    ↓
Transform
    ↓
Consume
```

Useful for composing independent asynchronous operations.

---

# 37. `thenApply()` vs `thenCompose()`

### `thenApply()`

Transforms:

```text
Future<A>
 ↓
A → B
 ↓
Future<B>
```

Example:

```java
future.thenApply(user ->
    user.getName()
);
```

### `thenCompose()`

Used when the next operation itself returns a future:

```text
Future<A>
 ↓
A → Future<B>
 ↓
Future<B>
```

It prevents:

```text
Future<Future<B>>
```

---

# 38. ConcurrentHashMap

For concurrent key-value access:

```java
ConcurrentHashMap<String, Integer> map =
    new ConcurrentHashMap<>();
```

Useful operations:

```java
putIfAbsent()
compute()
computeIfAbsent()
merge()
```

It is designed for concurrent access and generally scales much better than externally synchronizing an entire `HashMap`.

---

# 39. BlockingQueue

⭐ **Important**

Useful for producer-consumer systems:

```text
Producer
   ↓
BlockingQueue
   ↓
Consumer
```

Example:

```java
BlockingQueue<Task> queue =
    new LinkedBlockingQueue<>();
```

Producer:

```java
queue.put(task);
```

Consumer:

```java
Task task = queue.take();
```

If the queue is empty:

```text
take()
 ↓
wait
```

---

# 40. Deadlock

⭐ **Very Frequently Asked**

Example:

```text
Thread A
  ↓
holds Lock A
  ↓
waits for Lock B

Thread B
  ↓
holds Lock B
  ↓
waits for Lock A
```

Neither can proceed.

---

# 41. Four Coffman Conditions

Deadlock requires:

```text
1. Mutual Exclusion
2. Hold and Wait
3. No Preemption
4. Circular Wait
```

A common prevention strategy is:

```text
Always acquire multiple locks
in the same global order.
```

Example:

```text
Thread A: A → B
Thread B: A → B
```

instead of:

```text
Thread A: A → B
Thread B: B → A
```

---

# 42. Livelock

Threads are active but don't make useful progress.

```text
Thread A
 ↓
retry
 ↓
back off

Thread B
 ↓
retry
 ↓
back off
```

### Difference

| Problem    | Behavior                                      |
| ---------- | --------------------------------------------- |
| Deadlock   | Threads blocked indefinitely                  |
| Livelock   | Threads active but no progress                |
| Starvation | A thread repeatedly fails to obtain resources |

---

# 43. Thread Safety

A class is thread-safe when it maintains its correctness under the intended concurrent usage.

Possible strategies:

```text
Immutable objects
synchronized
Locks
Atomic variables
Concurrent collections
Thread confinement
Message passing
```

Thread safety is a **design property**, not simply the presence of `synchronized`.

---

# 44. Thread Confinement

One powerful technique is to avoid sharing mutable state.

```text
Thread A
 ↓
Private state

Thread B
 ↓
Private state
```

No synchronization is required for state that is genuinely confined to one thread.

Examples include:

* Local variables
* Certain request-scoped state
* Carefully designed thread-local state

---

# 45. `ThreadLocal`

`ThreadLocal` provides each thread with its own independently stored value.

```java
ThreadLocal<Integer> local =
    ThreadLocal.withInitial(() -> 0);
```

Each thread sees its own value:

```text
Thread A → 10
Thread B → 20
Thread C → 30
```

Useful for thread-confined context, but dangerous when values are large or when cleanup is neglected, particularly with pooled threads.

---

# 46. ForkJoinPool

Designed for tasks that can be recursively split.

Conceptually:

```text
Large Task
    ↓
 ┌──┴──┐
 A     B
 ↓     ↓
A1 A2 B1 B2
```

Uses **work stealing**.

```text
Worker A
Queue: T1 T2 T3

Worker B
Queue: T4
```

Worker B can steal work from another worker when it becomes idle.

---

# 47. Java 21 Virtual Threads

⭐⭐⭐⭐⭐ **Modern Java Interview Topic**

Java 21 introduced virtual threads as a lightweight concurrency mechanism.

Example:

```java
Thread.startVirtualThread(() -> {
    processRequest();
});
```

Or:

```java
try (ExecutorService executor =
         Executors.newVirtualThreadPerTaskExecutor()) {

    executor.submit(() -> processRequest());
}
```

Architecture:

```text
Virtual Threads
      ↓
JVM scheduling
      ↓
Carrier Platform Threads
      ↓
CPU
```

---

# 48. Platform Thread vs Virtual Thread

| Platform Thread                | Virtual Thread                               |
| ------------------------------ | -------------------------------------------- |
| OS-backed execution resource   | JVM-managed lightweight thread               |
| Higher memory cost             | Much cheaper per thread                      |
| Suitable for general execution | Excellent for high-concurrency I/O workloads |
| Limited practical count        | Very large numbers can be created            |
| Traditional model              | Modern Java 21 model                         |

Virtual threads do **not** create additional CPU cores.

---

# 49. Virtual Threads and I/O

Traditional model:

```text
Request
 ↓
Platform Thread
 ↓
DB wait
 ↓
Thread remains a relatively expensive resource
```

Virtual-thread model:

```text
Virtual Thread
 ↓
DB wait
 ↓
Virtual thread can be parked
 ↓
Carrier thread can execute other work
```

This makes thread-per-request programming much more scalable for suitable blocking I/O workloads.

---

# 50. Virtual Threads Are Not Magic

If the task is CPU-bound:

```text
Virtual Thread
 ↓
CPU-heavy computation
 ↓
CPU bottleneck
```

Virtual threads don't increase CPU capacity.

Also consider:

```text
Database connection pool
HTTP connection pool
External API rate limits
Memory
Lock contention
```

These can become the actual bottlenecks.

---

# 51. Multithreading in Spring Boot

A typical backend:

```text
                    HTTP Requests
                          │
                          ↓
                  Spring Boot Server
                          │
                   Executor / Threads
                          │
          ┌───────────────┼───────────────┐
          ↓               ↓               ↓
       Service A       Service B       Service C
          │               │               │
          ↓               ↓               ↓
         DB              Redis           Kafka
```

Possible concurrency tools:

```text
Virtual Threads
ExecutorService
CompletableFuture
@Async
ConcurrentHashMap
BlockingQueue
Database transactions
```

The correct choice depends on the workload and consistency requirements.

---

# 52. Multithreading vs Distributed Concurrency

⭐ **Advanced Interview Trap**

Suppose you have:

```text
Spring Boot Instance A
Spring Boot Instance B
Spring Boot Instance C
```

A:

```java
synchronized
```

block protects only threads sharing that JVM's monitor.

It does **not** coordinate:

```text
Instance A ↔ Instance B
```

For distributed coordination, you may need:

```text
Database locking
Optimistic concurrency
Redis coordination
Distributed locks
Kafka/message ordering
Transactional design
```

---

# 53. Bad vs Good Design

### ❌ Bad

```java
synchronized (lock) {

    callExternalAPI();

    Thread.sleep(5000);

    databaseCall();

    updateState();
}
```

Problems:

```text
Long critical section
I/O while holding lock
High contention
Poor scalability
```

### Better

```java
callExternalAPI();

databaseCall();

synchronized (lock) {
    updateState();
}
```

Only the state transition that requires mutual exclusion is protected.

---

# 54. Common Interview Traps

### Does creating multiple threads guarantee parallel execution?

❌ No.

Parallel execution depends on available CPU cores and scheduling.

---

### Does `run()` create a new thread?

❌ No.

`start()` does.

---

### Does `volatile` make `count++` safe?

❌ No.

`count++` is a compound operation.

---

### Does `sleep()` release a lock?

❌ No.

---

### Does `wait()` release the monitor?

✅ Yes, for the monitor on which `wait()` is invoked.

---

### Does `synchronized` make a distributed application thread-safe?

❌ No.

It is JVM-local.

---

### Are virtual threads faster than platform threads?

Not necessarily.

Their major advantage is **scalable concurrency**, especially for I/O-heavy workloads.

---

# 55. Interviewer's Follow-Up Chain

```text
What is multithreading?
        ↓
Thread vs process?
        ↓
Concurrency vs parallelism?
        ↓
How do you create a thread?
        ↓
Thread vs Runnable?
        ↓
start() vs run()?
        ↓
Explain thread lifecycle
        ↓
sleep() vs wait() vs join()
        ↓
What is race condition?
        ↓
How do you make code thread-safe?
        ↓
How does synchronized work?
        ↓
What is a monitor?
        ↓
volatile vs synchronized?
        ↓
AtomicInteger?
        ↓
CAS?
        ↓
Java Memory Model?
        ↓
Happens-before?
        ↓
ExecutorService?
        ↓
ThreadPoolExecutor?
        ↓
Future vs CompletableFuture?
        ↓
Deadlock?
        ↓
How do you prevent deadlock?
        ↓
ConcurrentHashMap?
        ↓
ForkJoinPool?
        ↓
Virtual Threads?
        ↓
When should virtual threads be used?
        ↓
When should they NOT be used?
```

---

# 56. Common Candidate Mistakes

### ❌ Weak

> Multithreading means running multiple programs at the same time.

### Better

> Multithreading means executing multiple threads within a process, where the threads share process resources and can make concurrent progress.

---

### ❌ Weak

> Threads always execute simultaneously.

### Better

> Threads can execute concurrently; simultaneous execution requires actual parallel hardware execution and suitable scheduling.

---

### ❌ Weak

> Runnable is a thread.

### Better

> `Runnable` represents a task, while `Thread` represents a thread of execution that can execute that task.

---

### ❌ Weak

> Thread pool creates threads only once.

### Better

> A thread pool manages a bounded or otherwise controlled set of worker threads and schedules submitted tasks onto them; its exact creation and retirement behavior depends on the executor configuration.

---

# 57. 30-Second Revision

```text
JAVA MULTITHREADING
│
├── Thread
│   ├── Stack
│   ├── PC
│   └── Execution State
│
├── Shared
│   ├── Heap
│   └── Process Resources
│
├── Problems
│   ├── Race Condition
│   ├── Visibility
│   ├── Deadlock
│   ├── Livelock
│   └── Starvation
│
├── Safety
│   ├── synchronized
│   ├── volatile
│   ├── Atomic
│   └── Locks
│
├── Execution
│   ├── ExecutorService
│   ├── ThreadPoolExecutor
│   ├── ForkJoinPool
│   └── CompletableFuture
│
├── Concurrent Collections
│   ├── ConcurrentHashMap
│   └── BlockingQueue
│
└── Java 21
    └── Virtual Threads
```

### Golden mental model

```text
Multiple Tasks
      ↓
Multiple Threads
      ↓
Shared State?
      ↓
      Yes
      ↓
Race Condition?
      ↓
      Yes
      ↓
Synchronization / Atomic / Lock
      ↓
JMM
      ↓
Visibility + Ordering + Atomicity
      ↓
Scale with Executors / Virtual Threads
```

---

# 58. Master Interview Test

Answer without looking back:

1. What is multithreading?
2. Thread vs process?
3. Multithreading vs concurrency vs parallelism?
4. Explain the complete Java thread lifecycle.
5. Why does `start()` create a new execution path while `run()` does not?
6. **Explain `sleep()`, `wait()`, and `join()` in terms of thread state, monitor ownership, and coordination.**
7. **Why is `count++` unsafe when multiple threads access it? Explain at the read-modify-write level.**
8. **Explain the Java Memory Model and happens-before relationship with `synchronized`, `volatile`, `start()`, and `join()`.**
9. **Design a thread-safe producer-consumer system using `ExecutorService`, `BlockingQueue`, and appropriate synchronization. Explain why each component is required.**
10. **Design the concurrency architecture for a Java 21 Spring Boot application handling 100,000 concurrent I/O-heavy HTTP requests while also running CPU-intensive background jobs. Decide where to use virtual threads, platform-thread pools, `CompletableFuture`, `BlockingQueue`, synchronization, and backpressure. Identify the likely bottlenecks and failure modes.**
