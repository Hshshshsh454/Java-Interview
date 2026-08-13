# JVM — Java Virtual Machine

**Difficulty:** ⭐ Advanced → Expert
**Interview Relevance:** ⭐⭐⭐⭐⭐
**Category:** Java Core / Runtime / Memory Management / JIT / GC / Performance

> **Core idea:** The JVM is the **runtime environment that executes Java bytecode**. For an advanced interview, you should understand not just *what the JVM is*, but **class loading, runtime memory, bytecode execution, JIT compilation, garbage collection, threads, synchronization, and JVM diagnostics**.

---

# 0. Interview Relevance & Question Mapping

| Topic                       | Priority |
| --------------------------- | -------: |
| JVM Architecture            |    ⭐⭐⭐⭐⭐ |
| JDK vs JRE vs JVM           |    ⭐⭐⭐⭐⭐ |
| Class Loader                |    ⭐⭐⭐⭐⭐ |
| Bytecode                    |    ⭐⭐⭐⭐⭐ |
| JVM Runtime Data Areas      |    ⭐⭐⭐⭐⭐ |
| Stack vs Heap               |    ⭐⭐⭐⭐⭐ |
| Method Area / Metaspace     |    ⭐⭐⭐⭐⭐ |
| Execution Engine            |    ⭐⭐⭐⭐⭐ |
| Interpreter                 |    ⭐⭐⭐⭐⭐ |
| JIT Compiler                |    ⭐⭐⭐⭐⭐ |
| Garbage Collection          |    ⭐⭐⭐⭐⭐ |
| Strong/Weak/Soft References |     ⭐⭐⭐⭐ |
| Java Threads                |    ⭐⭐⭐⭐⭐ |
| Synchronization             |    ⭐⭐⭐⭐⭐ |
| `volatile`                  |    ⭐⭐⭐⭐⭐ |
| `synchronized`              |    ⭐⭐⭐⭐⭐ |
| Escape Analysis             |     ⭐⭐⭐⭐ |
| JVM Tuning                  |     ⭐⭐⭐⭐ |
| JVM Diagnostics             |     ⭐⭐⭐⭐ |
| Virtual Threads             |    ⭐⭐⭐⭐⭐ |

### ⭐ Frequently Asked

* What is JVM?
* JVM vs JDK vs JRE?
* How does Java code execute?
* What is bytecode?
* What is the class loader?
* Explain JVM architecture.
* Heap vs Stack?
* What is Metaspace?
* What is the method area?
* What is the execution engine?
* Interpreter vs JIT?
* What is HotSpot?
* How does JIT optimization work?
* How does garbage collection work?
* What causes `OutOfMemoryError`?
* StackOverflowError vs OutOfMemoryError?
* What is `volatile`?
* `synchronized` vs `volatile`?
* What is happens-before?
* How do virtual threads interact with the JVM?

---

# 1. Precise Definition

### Interview-ready answer

> **The JVM is an abstract execution machine defined by the Java Virtual Machine Specification that loads and verifies Java bytecode, manages runtime memory, executes bytecode through interpretation and JIT compilation, provides garbage collection, and supplies runtime services such as threading and synchronization.**

The JVM allows the same bytecode to run on different operating systems as long as an appropriate JVM implementation exists.

---

# 2. Why Does the JVM Exist?

Java source code:

```java
public class Main {
    public static void main(String[] args) {
        System.out.println("Hello");
    }
}
```

Compilation:

```text
Main.java
   ↓
javac
   ↓
Main.class
   ↓
Bytecode
```

Execution:

```text
Main.class
   ↓
JVM
   ↓
Machine instructions
   ↓
CPU
```

The JVM provides an abstraction layer:

```text
Java Code
    ↓
Bytecode
    ↓
JVM
    ↓
Operating System
    ↓
CPU
```

Therefore:

> **Java source is not directly compiled into native machine code in the usual JVM execution model.**

---

# 3. JDK vs JRE vs JVM

⭐ **Classic Interview Question**

```text
JDK
│
├── Development Tools
│   ├── javac
│   ├── javadoc
│   └── jdb
│
└── Runtime
    └── JVM
```

Historically:

```text
JDK
 ↓
JRE
 ↓
JVM
```

### JVM

Executes bytecode.

### JRE

Historically represented the runtime components required to run Java applications, including the JVM and libraries.

### JDK

Development environment containing tools for building Java applications plus the runtime.

### Modern Java nuance

Since Java 9, the standalone traditional JRE distribution is no longer provided by Oracle in the old form.

For modern development:

```text
JDK
 ↓
JVM + Java runtime libraries + development tools
```

---

# 4. JVM Architecture

```text
                     JVM
┌──────────────────────────────────────────────┐
│                                              │
│              Class Loader Subsystem          │
│                      ↓                       │
│              Runtime Data Areas              │
│                      ↓                       │
│               Execution Engine               │
│                /          \                  │
│          Interpreter       JIT               │
│                      ↓                       │
│                  Native Code                 │
│                                              │
│              Garbage Collector               │
│                                              │
│              Native Interface                │
└──────────────────────────────────────────────┘
```

Major components:

```text
1. Class Loader
2. Runtime Data Areas
3. Execution Engine
4. JIT Compiler
5. Garbage Collector
6. JNI / Native Libraries
```

---

# 5. Complete Java Execution Flow

⭐ **Extremely Important**

```text
             Java Source
                  │
                  ↓
              javac
                  │
                  ↓
              Bytecode
              .class
                  │
                  ↓
          Class Loader
                  │
                  ↓
             Verification
                  │
                  ↓
         Runtime Data Areas
                  │
                  ↓
         Execution Engine
           /          \
          ↓            ↓
    Interpreter       JIT
          \            /
           ↓          ↓
             Machine Code
                  │
                  ↓
                 CPU
```

---

# 6. Class Loader Subsystem

The Class Loader loads class definitions into the JVM.

```text
.class file
    ↓
Class Loader
    ↓
JVM memory
```

Important responsibilities:

```text
Loading
Linking
Initialization
```

---

# 7. Class Loading Phases

Conceptually:

```text
Loading
   ↓
Linking
   ├── Verification
   ├── Preparation
   └── Resolution
   ↓
Initialization
```

---

# 8. Loading

The JVM obtains the binary representation of a class and creates the corresponding class representation in the runtime.

Example:

```java
User user = new User();
```

When required, the JVM needs the `User` class definition.

```text
User.class
   ↓
Class Loader
   ↓
Class representation
```

---

# 9. Linking

Linking consists conceptually of:

```text
Verification
Preparation
Resolution
```

### Verification

Checks that the bytecode conforms to JVM requirements and is structurally valid.

### Preparation

Allocates memory for class-level/static fields and establishes default values as required by the JVM specification.

### Resolution

Converts symbolic references into direct references when required; this may be performed lazily.

---

# 10. Initialization

Static initialization occurs during class initialization.

Example:

```java
class Test {

    static int x = 100;

    static {
        System.out.println("Initialized");
    }
}
```

Conceptually:

```text
Load
 ↓
Link
 ↓
Initialize
 ↓
Class ready
```

Initialization executes class initialization logic, including static field initializers and static initialization blocks.

---

# 11. Parent Delegation Model

Class loaders generally use a parent-delegation mechanism.

Conceptually:

```text
Application ClassLoader
        ↓
Platform ClassLoader
        ↓
Bootstrap ClassLoader
```

When a class is requested:

```text
Application Loader
       ↓
Ask Parent
       ↓
Platform Loader
       ↓
Ask Parent
       ↓
Bootstrap Loader
```

The parent gets the opportunity to load the class first.

---

# 12. Why Parent Delegation?

Security and consistency.

Suppose someone creates:

```java
package java.lang;

class String {
}
```

You don't want the application class loader to replace the platform's core `java.lang.String`.

Parent delegation helps ensure trusted platform classes are loaded by trusted loaders.

---

# 13. Bootstrap, Platform, Application Class Loaders

### Bootstrap

Loads core Java platform classes.

Implemented by the JVM rather than as an ordinary Java class loader object.

### Platform Class Loader

Loads platform modules/classes that are not part of the bootstrap loader's responsibilities.

### Application Class Loader

Loads application classes and classpath/module-path dependencies.

Conceptually:

```text
Bootstrap
   ↓
Platform
   ↓
Application
```

---

# 14. Class Loader Example

```java
System.out.println(
    String.class.getClassLoader()
);
```

For core classes such as `String`, this commonly results in `null`, representing the bootstrap loader.

For your own class:

```java
System.out.println(
    Main.class.getClassLoader()
);
```

you'll typically see the application class loader.

---

# 15. Runtime Data Areas

⭐ **Major Interview Topic**

The JVM runtime memory model includes:

```text
Runtime Data Areas
│
├── Heap
├── JVM Stacks
├── PC Registers
├── Method Area
└── Native Method Stacks
```

Important distinction:

```text
Shared
vs
Per-thread
```

---

# 16. Shared vs Thread-Private Memory

### Shared

```text
Heap
Method Area
```

### Per-thread

```text
PC Register
JVM Stack
Native Method Stack
```

Conceptually:

```text
                  JVM
                   │
        ┌──────────┴──────────┐
        ↓                     ↓
     Shared               Per Thread
        │                     │
      Heap              JVM Stack
   Method Area          PC Register
                        Native Stack
```

---

# 17. Heap

⭐ **Must-Know**

The heap is the runtime memory area from which objects and arrays are allocated.

Example:

```java
User user = new User();
```

Conceptually:

```text
Stack
  │
  │ reference
  ↓
Heap
 └── User object
```

The heap is shared among JVM threads.

---

# 18. Stack

Each JVM thread has its own JVM stack.

Every method invocation creates a stack frame.

Example:

```java
main()
 ↓
calculate()
 ↓
sum()
```

Conceptually:

```text
Thread Stack
┌─────────────┐
│ sum()       │ ← top
├─────────────┤
│ calculate() │
├─────────────┤
│ main()      │
└─────────────┘
```

When `sum()` returns:

```text
sum frame removed
```

---

# 19. Stack Frame

A stack frame is associated with a method invocation.

It contains information such as:

```text
Local variables
Operand stack
Reference to runtime constant pool
Return information / frame metadata
```

Example:

```java
int add(int a, int b) {
    int result = a + b;
    return result;
}
```

The JVM frame manages the method's execution state.

---

# 20. Stack vs Heap

⭐ **Extremely Frequently Asked**

| Stack                                   | Heap                        |
| --------------------------------------- | --------------------------- |
| Per-thread                              | Shared                      |
| Method frames                           | Objects/arrays              |
| Local execution state                   | Object memory               |
| Automatically managed as methods return | Managed by GC               |
| Smaller                                 | Typically much larger       |
| `StackOverflowError` possible           | `OutOfMemoryError` possible |

### Important correction

Do **not** say:

> "Primitive variables are always stored on the stack."

That's an oversimplification.

The actual storage depends on context and JVM implementation.

---

# 21. Example: Stack + Heap

```java
public void test() {

    int x = 10;

    User user = new User();
}
```

Conceptually:

```text
Thread Stack
┌─────────────────┐
│ x = 10          │
│ user ────────────────┐
└─────────────────┘    │
                       ↓
                     Heap
                ┌─────────────┐
                │ User object │
                └─────────────┘
```

The variable `user` is a reference; the object itself resides in managed object memory.

---

# 22. Method Area

The JVM specification defines a logical **method area** for storing per-class/per-interface structures.

It can contain information related to:

* Class metadata
* Method metadata
* Runtime constant pool
* Field information
* Method bytecode

### Important modern JVM detail

In HotSpot, class metadata is generally stored in **Metaspace**, which uses native memory rather than the traditional Java heap.

So:

```text
JVM Specification
→ Method Area

HotSpot implementation
→ Metaspace is the primary implementation mechanism
```

---

# 23. Metaspace

Before Java 8:

```text
Class metadata
 ↓
PermGen
```

Java 8 onward in HotSpot:

```text
Class metadata
 ↓
Metaspace
 ↓
Native memory
```

This is a classic interview question.

### Potential issue

Excessive class loading can cause:

```text
OutOfMemoryError:
Metaspace
```

---

# 24. Runtime Constant Pool

The runtime constant pool is associated with each class/interface and contains symbolic and literal information used during execution.

Examples include:

```text
String literals
Method references
Field references
Class references
Numeric constants
```

It plays an important role in bytecode linkage.

---

# 25. PC Register

Each JVM thread has its own **Program Counter register**.

It identifies the current JVM instruction being executed by that thread.

Conceptually:

```text
Thread
 ↓
PC Register
 ↓
Current bytecode instruction
```

This allows each thread to maintain its own execution position.

---

# 26. Native Method Stack

Used for execution of native methods.

For example:

```text
Java
 ↓
JNI
 ↓
Native C/C++ code
```

Native method stack behavior is JVM-implementation-dependent.

---

# 27. Execution Engine

The execution engine executes JVM bytecode.

Major components:

```text
Execution Engine
│
├── Interpreter
├── JIT Compiler
└── Runtime Support
```

---

# 28. Interpreter

The interpreter executes bytecode instruction by instruction.

Example:

```text
Bytecode
 ↓
Instruction 1
 ↓
Instruction 2
 ↓
Instruction 3
```

Advantage:

```text
Fast startup
```

Disadvantage:

```text
Repeated code can be inefficient
```

---

# 29. JIT Compiler

⭐ **Must-Know**

**JIT = Just-In-Time compiler**

Instead of interpreting frequently executed code forever:

```text
Bytecode
   ↓
Frequently executed
   ↓
JIT
   ↓
Native machine code
```

This can dramatically improve long-running application performance.

---

# 30. HotSpot

HotSpot is a major JVM implementation.

The name reflects an important optimization idea:

```text
Application
 ↓
Many methods
 ↓
Some methods execute repeatedly
 ↓
"Hot" code
 ↓
Optimize aggressively
```

The JVM uses runtime profiling to identify hot execution paths.

---

# 31. Interpreter + JIT

A simplified model:

```text
                 Bytecode
                    ↓
              Interpreter
                    ↓
            Runtime profiling
                    ↓
               Hot code?
                /      \
              No        Yes
              ↓          ↓
        Keep executing   JIT
                         ↓
                  Native machine code
```

This provides good startup characteristics while enabling high peak performance.

---

# 32. JIT Optimizations

The JIT compiler can perform optimizations such as:

```text
Inlining
Dead-code elimination
Loop optimizations
Escape analysis
Devirtualization
Constant folding
Range analysis
```

---

# 33. Method Inlining

⭐ **Advanced**

Suppose:

```java
int add(int a, int b) {
    return a + b;
}
```

and:

```java
int result = add(10, 20);
```

The JIT may replace the call with equivalent inline operations when profitable.

Conceptually:

```text
Before:

caller
  ↓
add()
  ↓
return


After optimization:

caller
  ↓
a + b
```

This removes method-call overhead and enables further optimization.

---

# 34. Escape Analysis

⭐ **Advanced**

The JIT can analyze whether an object escapes the scope in which it was created.

Example:

```java
void test() {
    Point p = new Point(10, 20);
    use(p);
}
```

If the JVM determines the object does not escape the relevant scope, it may optimize its allocation or eliminate the allocation entirely under suitable conditions.

Possible optimizations include:

```text
Scalar replacement
Lock elimination
Allocation elimination
```

This is an optimization, not a guarantee that every local object is placed on the stack.

---

# 35. Garbage Collection

⭐ **Must-Know**

Garbage Collection automatically identifies and reclaims memory that is no longer reachable by the application.

Example:

```java
User user = new User();

user = null;
```

If no other reachable reference exists:

```text
User object
   ↓
Unreachable
   ↓
Eligible for GC
```

Important:

> **Eligible for GC does not mean immediately collected.**

---

# 36. Reachability

The JVM's GC fundamentally reasons about object reachability.

Conceptually:

```text
GC Roots
  ↓
Reachable Objects
  ↓
Live

Not reachable
  ↓
Garbage
```

Typical GC roots include references from:

* Active thread stacks
* Static references
* JNI/native references
* Other JVM-managed root structures

---

# 37. GC Roots

Conceptually:

```text
GC Roots
│
├── Thread references
├── Static references
├── JNI references
└── JVM internal references
        ↓
     Objects
```

If an object cannot be reached from a GC root through references, it is eligible for reclamation.

---

# 38. Generational Garbage Collection

A common generational model:

```text
Heap
│
├── Young Generation
│   ├── Eden
│   └── Survivor
│
└── Old Generation
```

Typical object lifecycle:

```text
new object
   ↓
Eden
   ↓
Survivor
   ↓
Old Generation
```

Not every collector uses exactly this conceptual layout, but generational hypotheses are widely used in JVM GC designs.

---

# 39. Minor / Young Collection

Young-generation collection focuses on younger objects.

Because many applications have high object mortality among recently created objects:

```text
Many new objects
       ↓
Most become unreachable quickly
       ↓
Young collection can reclaim them efficiently
```

---

# 40. Major / Old Collection

Terminology varies by collector, so avoid treating "major GC" as one universal technical event.

Broadly:

```text
Old-generation-heavy collection
        ↓
Potentially more expensive work
```

Modern collectors have different collection strategies and pause characteristics.

---

# 41. Stop-the-World

Some JVM operations temporarily stop application threads.

This is called:

> **Stop-The-World (STW)**

Conceptually:

```text
Application Threads
      ↓
     STOP
      ↓
 JVM operation
      ↓
    RESUME
```

Important:

> **Not every GC phase necessarily stops all application threads.**

Modern collectors perform substantial concurrent work.

---

# 42. Common JVM Garbage Collectors

Modern JVMs include collectors such as:

```text
Serial GC
Parallel GC
G1 GC
ZGC
Shenandoah
```

### G1

Designed for large heaps with predictable pause-time goals.

### ZGC

Designed for very low pause times and large heaps.

### Parallel GC

Prioritizes throughput.

---

# 43. G1 GC

G1 divides the heap into regions rather than relying on one simple contiguous young/old layout.

Conceptually:

```text
Heap
┌───┬───┬───┬───┬───┐
│ R │ R │ R │ R │ R │
├───┼───┼───┼───┼───┤
│ R │ R │ R │ R │ R │
└───┴───┴───┴───┴───┘
```

The JVM tracks region contents and can prioritize regions with more reclaimable garbage.

---

# 44. ZGC

ZGC is designed for extremely low pause times.

Conceptually:

```text
Large Heap
    ↓
Concurrent GC work
    ↓
Very short pauses
```

Java 21 also introduced:

> **Generational ZGC**

which applies generational collection concepts to ZGC.

---

# 45. `System.gc()`

You can request:

```java
System.gc();
```

But this is only a **request/hint** to the JVM.

It does not guarantee:

```text
Immediate GC
```

Do not rely on it for application correctness.

---

# 46. Memory Leak in Java

Java has GC, but memory leaks can still happen.

Example:

```java
static List<Object> cache =
        new ArrayList<>();
```

If objects are continuously added:

```text
Object
 ↓
Static List
 ↓
GC Root
 ↓
Object remains reachable
```

Even if the application no longer logically needs the object, GC cannot reclaim it while it remains reachable.

### Key concept

> **Garbage collection eliminates unreachable objects, not logically unnecessary but still reachable objects.**

---

# 47. `OutOfMemoryError`

Possible causes:

```text
Heap exhaustion
Metaspace exhaustion
Direct/native memory exhaustion
Excessive object retention
Large allocations
Class-loader leaks
```

Example:

```java
List<int[]> list = new ArrayList<>();

while (true) {
    list.add(new int[1_000_000]);
}
```

Eventually:

```text
OutOfMemoryError
```

may occur.

---

# 48. `StackOverflowError`

Usually caused by excessive stack-frame growth.

Classic example:

```java
void recursive() {
    recursive();
}
```

Flow:

```text
recursive()
 ↓
recursive()
 ↓
recursive()
 ↓
...
 ↓
StackOverflowError
```

### Difference

```text
StackOverflowError
→ thread stack exhausted

OutOfMemoryError
→ some JVM/native memory resource exhausted
```

---

# 49. JVM Thread Model

Traditional Java thread:

```text
Java Thread
    ↓
Platform/OS Thread
```

Modern Java:

```text
Platform Thread
Virtual Thread
```

Java 21 introduced virtual threads as a major production-ready concurrency feature.

---

# 50. JVM and `synchronized`

`synchronized` provides:

* Mutual exclusion
* Memory visibility
* Happens-before relationships

Example:

```java
synchronized void increment() {
    count++;
}
```

Conceptually:

```text
Thread A
   ↓
Acquire monitor
   ↓
Critical section
   ↓
Release monitor
```

Another thread attempting the same monitor may have to wait.

---

# 51. `volatile`

`volatile` provides visibility guarantees for a variable and participates in the Java Memory Model's happens-before relationships.

Example:

```java
private volatile boolean running = true;
```

Thread A:

```java
running = false;
```

Thread B can reliably observe the updated volatile value under the JMM's rules.

### Important

`volatile` does **not** make compound operations atomic.

This is not safe simply because `count` is volatile:

```java
count++;
```

because:

```text
read
 +
increment
 +
write
```

is a compound operation.

---

# 52. `synchronized` vs `volatile`

| `volatile`                                        | `synchronized`                    |
| ------------------------------------------------- | --------------------------------- |
| Visibility/order guarantees                       | Mutual exclusion + visibility     |
| No monitor locking for the variable access itself | Uses monitor synchronization      |
| Does not make compound operations atomic          | Can make critical sections atomic |
| Good for state flags                              | Good for shared mutable state     |

---

# 53. Java Memory Model

⭐ **Expert Topic**

The Java Memory Model defines rules for:

```text
Threads
Visibility
Ordering
Atomicity
Happens-before
```

Without a memory model:

```text
Thread A
   ↓
Write

Thread B
   ↓
Read
```

would have ambiguous visibility/ordering semantics across modern CPUs and compiler/JIT optimizations.

---

# 54. Happens-Before

A happens-before relationship establishes ordering and visibility guarantees.

Examples include:

```text
Unlock
  happens-before
subsequent lock on same monitor
```

and:

```text
volatile write
  happens-before
subsequent volatile read
```

Also:

```text
Thread.start()
  happens-before
actions in started thread
```

and:

```text
Thread completion
  happens-before
successful join return
```

---

# 55. JNI

**JNI = Java Native Interface**

It allows Java code to interact with native code.

Conceptually:

```text
Java
 ↓
JNI
 ↓
C / C++
 ↓
Operating System
```

Used when integrating with:

* Native libraries
* OS APIs
* Existing C/C++ systems
* Performance-sensitive native components

Modern Java also has the Foreign Function & Memory API as a newer approach for many native interoperability use cases.

---

# 56. JVM Security

The JVM historically provided a Security Manager-based model, but the Security Manager has been deprecated for removal.

Modern application security should primarily rely on:

```text
OS/container isolation
Process boundaries
Application security
Authentication
Authorization
Sandboxing where appropriate
```

Do not present the Security Manager as the modern default security boundary.

---

# 57. JVM Monitoring

Production JVMs can expose:

```text
Heap usage
GC activity
Thread counts
CPU usage
Class loading
JIT compilation
Metaspace
Native memory
```

Common tools:

```text
jcmd
jstack
jmap
jstat
JFR
JConsole
VisualVM
```

---

# 58. `jstack`

Used to inspect thread stacks.

Conceptually:

```text
Thread-1
  WAITING

Thread-2
  RUNNABLE

Thread-3
  BLOCKED
```

Useful for diagnosing:

* Deadlocks
* Thread contention
* Stuck threads
* Unexpected blocking

---

# 59. Java Flight Recorder

**JFR** is a low-overhead JVM/application monitoring and profiling technology.

It can capture information about:

```text
CPU
GC
Threads
Locks
I/O
JIT
Exceptions
```

Useful for production performance analysis.

---

# 60. JVM Tuning

Do not start with random JVM flags.

First determine:

```text
Problem
 ↓
Measurement
 ↓
Evidence
 ↓
Change
 ↓
Benchmark
 ↓
Validate
```

Common concerns:

```text
Heap size
GC selection
GC pause targets
Metaspace
Thread count
Native memory
CPU
Allocation rate
```

---

# 61. JVM Performance Investigation

Suppose application latency increases.

Don't immediately increase heap.

Investigate:

```text
Latency
 ↓
CPU?
 ↓
GC pauses?
 ↓
Allocation rate?
 ↓
Thread contention?
 ↓
Database latency?
 ↓
Lock contention?
 ↓
JIT behavior?
 ↓
Native memory?
```

The JVM is only one part of the system.

---

# 62. Complete JVM Mental Model

```text
                     JVM
                      │
        ┌─────────────┴─────────────┐
        ↓                           ↓
 Class Loader                Runtime Memory
        │                           │
        ↓                     ┌─────┼─────┐
     Bytecode                 ↓     ↓     ↓
        │                    Heap  Stack Metaspace
        ↓
 Execution Engine
    │          │
Interpreter   JIT
    │          │
    └────┬─────┘
         ↓
   Native Machine Code
         ↓
        CPU

       Garbage Collector
              ↓
       Reclaim Memory
```

---

# 63. Java Source → CPU

This is one of the most important flows to remember:

```text
.java
  ↓
javac
  ↓
.class
  ↓
Bytecode
  ↓
Class Loader
  ↓
Verification / Linking / Initialization
  ↓
Runtime Data Areas
  ↓
Interpreter
  ↓
Profiling
  ↓
JIT Compilation
  ↓
Native Machine Code
  ↓
CPU
```

---

# 64. JVM + Java 21

The modern JVM becomes especially interesting with Java 21:

```text
Java 21
│
├── Virtual Threads
│       ↓
│   JVM scheduling
│
├── Pattern Matching
│       ↓
│   Language/runtime support
│
├── Records
│       ↓
│   Compact data modeling
│
├── Sealed Classes
│       ↓
│   Controlled hierarchies
│
└── Generational ZGC
        ↓
    Low-latency GC
```

---

# 65. Common Interview Traps

### ❌ "JVM converts Java directly into machine code."

Oversimplified.

Better:

> `javac` normally produces JVM bytecode. The JVM can interpret bytecode and JIT-compile hot code into native machine code at runtime.

---

### ❌ "Objects always live on the heap."

The Java programming model treats objects as heap objects, but JVM/JIT optimizations such as escape analysis can eliminate or transform allocations.

---

### ❌ "GC deletes objects."

More precisely:

> GC identifies unreachable objects and reclaims memory associated with them.

---

### ❌ "Calling `System.gc()` forces garbage collection."

No.

It is only a request/hint.

---

### ❌ "volatile makes variables thread-safe."

Too broad.

`volatile` provides visibility/order guarantees but does not make compound operations atomic.

---

### ❌ "Stack stores primitives and heap stores objects."

Oversimplified.

The JVM specification does not require that simplistic mapping for every value; implementation and optimization details matter.

---

# 66. Interviewer Follow-Up Chain

```text
What is JVM?
     ↓
JDK vs JRE vs JVM?
     ↓
How does Java code execute?
     ↓
What is bytecode?
     ↓
What does javac produce?
     ↓
What is ClassLoader?
     ↓
Explain Loading → Linking → Initialization
     ↓
Why parent delegation?
     ↓
Explain JVM memory areas
     ↓
Stack vs Heap?
     ↓
What is Metaspace?
     ↓
What is a stack frame?
     ↓
What is interpreter?
     ↓
What is JIT?
     ↓
How does JVM identify hot code?
     ↓
What is method inlining?
     ↓
What is escape analysis?
     ↓
How does GC determine garbage?
     ↓
What are GC roots?
     ↓
Young vs old generation?
     ↓
G1 vs ZGC?
     ↓
What causes OutOfMemoryError?
     ↓
StackOverflowError?
     ↓
volatile vs synchronized?
     ↓
Happens-before?
     ↓
How does Java 21 virtual-thread scheduling work?
```

---

# 67. Common Candidate Mistakes

### Weak

> "Heap is for objects and stack is for variables."

### Strong

> "The heap is the shared runtime area from which objects and arrays are allocated, while each JVM thread has its own stack containing frames for method execution. References and primitive values can occur in different runtime structures depending on context and JVM implementation."

---

### Weak

> "JIT compiles Java into machine code."

### Strong

> "The JIT compiler dynamically compiles frequently executed bytecode into native machine code, using runtime profiling to apply optimizations such as inlining and other speculative optimizations."

---

### Weak

> "Garbage collection happens when an object becomes null."

### Strong

> "An object becomes eligible for collection when it is no longer reachable from GC roots. Assigning `null` to one reference only makes the object collectible if no other relevant reachable references remain."

---

# 68. 30-Second Revision

```text
JVM
│
├── Class Loader
│   ├── Loading
│   ├── Linking
│   └── Initialization
│
├── Runtime Data Areas
│   ├── Heap
│   ├── JVM Stack
│   ├── PC Register
│   ├── Method Area
│   └── Native Stack
│
├── Execution Engine
│   ├── Interpreter
│   └── JIT
│
├── Garbage Collector
│
└── Native Interface
```

### Most important distinctions

```text
JDK
→ Development environment

JRE
→ Historical runtime packaging concept

JVM
→ Executes bytecode

Heap
→ Objects/arrays

Stack
→ Per-thread method frames

Metaspace
→ HotSpot class metadata storage

Interpreter
→ Executes bytecode

JIT
→ Compiles hot code

GC
→ Reclaims unreachable objects

volatile
→ Visibility/order

synchronized
→ Mutual exclusion + visibility
```

### Golden execution model

```text
Java
 ↓
Bytecode
 ↓
JVM
 ↓
Interpreter + JIT
 ↓
Machine Code
 ↓
CPU
```

---

# 69. Master Interview Test

Answer without looking back:

1. What is the JVM?
2. Explain JDK vs JRE vs JVM.
3. What happens when you execute a `.java` file?
4. What is bytecode?
5. Explain the Class Loader subsystem.
6. Explain Loading, Linking, and Initialization.
7. **Explain the JVM runtime data areas and distinguish shared memory from thread-private memory.**
8. **Explain Stack vs Heap vs Metaspace, including what actually causes `StackOverflowError` and `OutOfMemoryError`.**
9. **Explain how the JVM combines interpretation, runtime profiling, and JIT compilation to optimize a long-running application.**
10. **A Java 21 Spring Boot application has high latency, increasing heap usage, frequent GC activity, and thousands of concurrent requests. Explain how you would investigate the JVM using heap/GC metrics, thread dumps, JFR, JIT behavior, and virtual-thread characteristics before changing JVM configuration.**
