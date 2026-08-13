# Multithreading in Java

## 0. PYQ Reference & Syllabus Mapping

* **PYQ relevance:** **Highly important** topic in Java university examinations.
* **Common PYQ forms:**

  * Define multithreading.
  * Explain the life cycle of a thread.
  * Explain different ways to create threads.
  * Differentiate process and thread.
  * Explain thread synchronization.
  * Explain daemon threads.
  * Explain inter-thread communication.
  * Explain thread priority and methods of `Thread` class.
* **Syllabus mapping:**
  **Java → Multithreading → Thread → Thread Life Cycle → Thread Creation → Thread Methods → Synchronization → Inter-thread Communication → Daemon Thread**

---

## 1. Definition

**Multithreading** is the concurrent execution of **multiple threads within a single process**, where each thread represents an independent path of execution.

A **thread** is the smallest unit of execution that can be scheduled by the operating system/JVM.

Example:

```java
class MyTask extends Thread {

    public void run() {
        System.out.println("Thread is running");
    }

    public static void main(String[] args) {
        MyTask t1 = new MyTask();
        MyTask t2 = new MyTask();

        t1.start();
        t2.start();
    }
}
```

Here, `t1` and `t2` are separate threads that can execute concurrently.

> **Multithreading = execution of multiple threads concurrently within a process.**

---

## 2. Its Use

### 1. Better CPU Utilization

Multiple threads can keep CPU resources occupied while other threads are waiting for I/O.

### 2. Concurrent Execution

Different tasks can progress concurrently.

Example:

```text
Thread 1 → Download file
Thread 2 → Process data
Thread 3 → Update UI
```

### 3. Improved Responsiveness

A long-running operation can execute in another thread without blocking the main thread.

### 4. Resource Sharing

Threads belonging to the same process share resources such as:

* Heap memory
* Objects
* Class metadata
* Open resources

### 5. Parallelism

On a multicore processor, independent threads may execute simultaneously on different CPU cores.

### 6. Background Processing

Multithreading supports background operations such as:

* Logging
* Monitoring
* Network communication
* File processing
* Scheduled tasks

---

## 3. Its Components

### 1. Thread

The fundamental execution unit.

```java
Thread t = new Thread();
```

### 2. `Runnable`

Represents a task that can be executed by a thread.

```java
Runnable task = () -> {
    System.out.println("Task running");
};
```

### 3. Thread Scheduler

The scheduler determines which runnable thread gets CPU execution time.

### 4. Thread Stack

Each thread has its **own stack**, containing:

* Local variables
* Method call frames
* Operand-related execution state

### 5. Shared Heap

Threads within the same process generally share the heap.

```text
Thread 1 ──┐
Thread 2 ──┼──> Shared Heap
Thread 3 ──┘
```

### 6. Synchronization Mechanisms

Used to control access to shared mutable state.

Examples:

* `synchronized`
* `Lock`
* `ReentrantLock`
* Atomic classes

### 7. Executor Framework

Provides higher-level thread management.

Important abstractions include:

```text
Executor
ExecutorService
ScheduledExecutorService
Future
Callable
```

---

## 4. Its Types

### 1. User Thread

A normal application thread.

```text
User Thread
     ↓
Performs application work
     ↓
Can keep JVM alive
```

### 2. Daemon Thread

A background thread that does not keep the JVM alive after all user threads have terminated.

```java
thread.setDaemon(true);
```

### 3. Platform Thread

Traditional Java threads backed by operating-system threads.

```java
Thread t = new Thread(task);
t.start();
```

### 4. Virtual Thread

Introduced as a major concurrency feature in **Java 21**, virtual threads are lightweight threads designed especially for large numbers of concurrent, mostly blocking tasks.

```java
Thread.startVirtualThread(() -> {
    System.out.println("Virtual thread");
});
```

---

## 5. Sub-types / Sub-topics

### A. Creating a Thread

#### Method 1: Extending `Thread`

```java
class MyThread extends Thread {

    @Override
    public void run() {
        System.out.println("Running");
    }
}

class Main {
    public static void main(String[] args) {
        MyThread t = new MyThread();
        t.start();
    }
}
```

#### Method 2: Implementing `Runnable`

```java
class Task implements Runnable {

    @Override
    public void run() {
        System.out.println("Running");
    }
}

class Main {
    public static void main(String[] args) {

        Thread t = new Thread(new Task());
        t.start();
    }
}
```

`Runnable` is generally preferred when you want to separate the **task** from the **thread mechanism**.

---

### B. Thread Life Cycle

A Java thread can move through states represented by `Thread.State`:

```text
NEW
 │
 │ start()
 ▼
RUNNABLE
 │
 ├───────────────┐
 │               │
 ▼               ▼
BLOCKED        WAITING
 │               │
 │               │
 └───────┬───────┘
         ▼
     RUNNABLE
         │
         ▼
    TERMINATED
```

Important states:

* `NEW`
* `RUNNABLE`
* `BLOCKED`
* `WAITING`
* `TIMED_WAITING`
* `TERMINATED`

---

### C. Important Thread Methods

| Method            | Purpose                                              |
| ----------------- | ---------------------------------------------------- |
| `start()`         | Starts a new thread                                  |
| `run()`           | Contains thread/task logic                           |
| `sleep()`         | Temporarily pauses execution                         |
| `join()`          | Waits for another thread to terminate                |
| `interrupt()`     | Requests interruption of a thread                    |
| `isAlive()`       | Checks whether thread has started and not terminated |
| `currentThread()` | Returns currently executing thread                   |
| `getName()`       | Gets thread name                                     |
| `setName()`       | Sets thread name                                     |
| `getPriority()`   | Gets priority                                        |
| `setPriority()`   | Sets priority                                        |
| `isDaemon()`      | Checks daemon status                                 |
| `setDaemon()`     | Sets daemon status before starting                   |

---

### D. `start()` vs `run()`

This is an important examination question.

```java
Thread t = new Thread(() -> {
    System.out.println("Hello");
});

t.start();
```

`start()` creates/schedules a new thread of execution.

But:

```java
t.run();
```

is simply a normal method invocation; it does **not** create a new thread.

```text
start()
  ↓
New thread execution
  ↓
run()
```

Whereas:

```text
run()
  ↓
Normal method call
  ↓
Current thread executes it
```

---

### E. Synchronization

When multiple threads access shared mutable data, race conditions can occur.

```java
synchronized void increment() {
    count++;
}
```

Synchronization provides controlled access to critical sections.

```text
Thread 1 ──┐
           │
           ▼
      Critical Section
           ▲
           │
Thread 2 ──┘
```

---

### F. Race Condition

A **race condition** occurs when the result of a program depends on the timing/interleaving of concurrent threads accessing shared state.

Example:

```java
count++;
```

This is not necessarily one indivisible operation.

Conceptually:

```text
Read count
   ↓
Add 1
   ↓
Write count
```

Two threads performing these operations concurrently can produce an incorrect result.

---

### G. Inter-Thread Communication

Threads may coordinate using mechanisms such as:

```text
wait()
notify()
notifyAll()
```

Modern Java applications may also use higher-level concurrency utilities such as:

* `BlockingQueue`
* `CountDownLatch`
* `CyclicBarrier`
* `Semaphore`
* `CompletableFuture`

---

### H. Executor Framework

Instead of manually creating a large number of threads, tasks can be submitted to an executor.

```java
ExecutorService executor =
        Executors.newFixedThreadPool(3);

executor.submit(() -> {
    System.out.println("Task executed");
});

executor.shutdown();
```

Conceptually:

```text
Tasks
  │
  ▼
ExecutorService
  │
  ▼
Thread Pool
  │
  ├── Thread 1
  ├── Thread 2
  └── Thread 3
```

---

### I. Virtual Threads

Java 21 provides virtual threads:

```java
Thread.startVirtualThread(() -> {
    System.out.println("Task");
});
```

They are particularly useful when an application needs **very high concurrency with blocking I/O**.

---

## 6. Diagram / Flow chart

### Complete Multithreading Model

```text
                    Java Process
                         │
              ┌──────────┼──────────┐
              │          │          │
              ▼          ▼          ▼
          Thread 1    Thread 2    Thread 3
              │          │          │
              ▼          ▼          ▼
           Stack       Stack       Stack
              │          │          │
              └──────────┼──────────┘
                         │
                         ▼
                    Shared Heap
                         │
                         ▼
                Shared Objects/Data
                         │
                         ▼
                  Synchronization
                         │
                         ▼
                 Safe Concurrent
                    Execution
```

### Thread Execution Flow

```text
        Thread Created
              │
              ▼
             NEW
              │
           start()
              │
              ▼
          RUNNABLE
              │
       ┌──────┼───────────┐
       │      │           │
       ▼      ▼           ▼
    BLOCKED WAITING TIMED_WAITING
       │      │           │
       └──────┼───────────┘
              ▼
          RUNNABLE
              │
              ▼
         TERMINATED
```

---

## 7. Natural Language Breakdown

Imagine a **restaurant**.

Without multithreading, one worker might handle everything sequentially:

```text
Take Order
    ↓
Prepare Food
    ↓
Serve Food
    ↓
Take Payment
    ↓
Clean Table
```

Each task waits for the previous task.

With multithreading:

```text
                 Restaurant
                     │
        ┌────────────┼────────────┐
        ▼            ▼            ▼
     Worker 1     Worker 2     Worker 3
        │            │            │
      Orders       Cooking      Cleaning
```

All workers can work **concurrently**.

In Java:

```text
Thread 1 → Network request
Thread 2 → Database operation
Thread 3 → File processing
Thread 4 → Logging
```

The important distinction is:

### Concurrency

Multiple tasks **make progress during overlapping time periods**.

### Parallelism

Multiple tasks **actually execute simultaneously**, typically on different CPU cores.

```text
Concurrency:
Task A ────────
       Task B ────────

Parallelism:
Core 1: Task A ────────
Core 2: Task B ────────
```

### Exam Memory Rule

> **Multithreading is the concurrent execution of multiple threads within a process to improve responsiveness, resource utilization, and application throughput.**

**Key chain to remember:**

```text
Process
  ↓
Multiple Threads
  ↓
Concurrent Execution
  ↓
Shared Resources
  ↓
Synchronization
  ↓
Safe Multithreaded Application
```
