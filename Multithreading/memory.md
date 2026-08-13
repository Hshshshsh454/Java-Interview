# Memory Allocation in Java

## 0. PYQ Reference & Syllabus Mapping

* **PYQ relevance:** **Highly important** for Java/JVM examinations, especially under **JVM architecture, memory management, garbage collection, and runtime data areas**.
* **Common PYQ forms:**

  * Explain memory allocation in Java.
  * Explain Stack and Heap memory.
  * Explain JVM runtime memory areas.
  * Differentiate stack and heap memory.
  * Explain object allocation and garbage collection.
  * Explain method area/metaspace.
* **Syllabus mapping:**
  **Java → JVM → Runtime Data Areas → Memory Allocation → Stack → Heap → Method Area → Garbage Collection**

---

## 1. Definition

**Memory allocation in Java** is the process by which the **JVM reserves and manages memory for classes, objects, method execution, local variables, and other runtime data during program execution**.

Java primarily uses JVM-managed memory areas such as:

* **Heap**
* **Java Stack**
* **Method Area / Metaspace**
* **PC Register**
* **Native Method Stack**

The programmer does not explicitly deallocate objects using mechanisms such as `free()` or `delete`; **garbage collection automatically reclaims memory occupied by unreachable objects**.

---

## 2. Its Use

### 1. Store Objects

Objects created using `new` are normally allocated in the **heap**.

```java
Student s = new Student();
```

Conceptually:

```text
new Student()
      ↓
    Heap
```

### 2. Store Local Variables and Method Frames

Each Java thread has its own JVM stack.

```java
void calculate() {
    int x = 10;
}
```

The method execution uses a **stack frame** containing local variables and other execution information.

### 3. Store Class-Level Runtime Information

The JVM maintains class-related metadata in the **method area**, implemented as **Metaspace** in modern HotSpot JVMs.

### 4. Support Multithreading

Each thread has its own:

```text
Java Stack
PC Register
Native Method Stack
```

while threads generally share the:

```text
Heap
Class metadata
```

### 5. Automatic Memory Management

The JVM uses **Garbage Collection (GC)** to reclaim memory from objects that are no longer reachable.

---

## 3. Its Components

### A. Heap

The **heap** is the runtime memory area from which objects and arrays are generally allocated.

```java
Student s = new Student();
```

Conceptually:

```text
Stack                  Heap
─────                  ────
s ───────────────────► Student object
```

The heap is **shared among JVM threads**.

---

### B. Java Stack

Each thread has its own JVM stack.

Whenever a method is invoked, a **stack frame** is created.

```text
main()
  ↓
calculate()
  ↓
process()
```

Each invocation gets its own frame.

```text
┌─────────────────┐
│ process() frame │
├─────────────────┤
│ calculate frame │
├─────────────────┤
│ main() frame    │
└─────────────────┘
```

---

### C. Method Area / Metaspace

The JVM method area stores class-level information such as:

* Class metadata
* Method metadata
* Runtime constant-pool information
* Field information

In HotSpot JVMs, **Metaspace** is used for class metadata and is allocated from native memory.

---

### D. PC Register

Each JVM thread has its own **Program Counter (PC) register**.

It identifies the JVM instruction currently being executed by that thread.

---

### E. Native Method Stack

Used for execution of **native methods**, typically methods implemented outside Java, such as through JNI.

---

## 4. Its Types

### 1. Stack Allocation

Associated with method execution and thread-local stack frames.

```text
Thread
  ↓
Java Stack
  ↓
Stack Frames
  ↓
Local variables / execution state
```

### 2. Heap Allocation

Used primarily for objects and arrays.

```java
Car c = new Car();
```

```text
Heap
 └── Car object
```

### 3. Metaspace/Class Metadata Allocation

Used for JVM-managed class metadata in HotSpot.

### 4. Native Memory Allocation

The JVM itself and native components may use memory outside the Java heap, including Metaspace and other native structures.

---

## 5. Sub-types / Sub-topics

### A. Primitive Local Variable

```java
void test() {
    int x = 10;
}
```

`x` is a local variable associated with the method's stack frame.

---

### B. Object Allocation

```java
Student s = new Student();
```

Conceptually:

```text
Stack                    Heap
─────                    ────
s ─────────────────────► Student object
```

The **reference variable** `s` and the **object itself** are conceptually different entities.

---

### C. Array Allocation

```java
int[] arr = new int[5];
```

The array object is allocated on the heap.

```text
Stack                  Heap
─────                  ────
arr ────────────────► [10][20][30][40][50]
```

---

### D. Garbage Collection

Suppose:

```java
Student s = new Student();

s = null;
```

The `Student` object may become **eligible for garbage collection** if there are no other reachable references to it.

```text
Before:

s ─────► Student Object


After:

s ─────► null

Student Object
      ↓
Unreachable
      ↓
Eligible for GC
```

**Important:** Eligible for GC does not mean that the object is immediately collected.

---

### E. Escape Analysis

The JVM's JIT compiler can analyze whether an object escapes a method or thread.

Under suitable conditions, the JVM may optimize object allocation, potentially using:

* Scalar replacement
* Elimination of unnecessary allocations
* Lock elimination

Therefore, saying **"every object always physically exists on the heap"** is an oversimplification of JVM implementation behavior.

---

### F. Memory Allocation in Multithreading

Each thread has separate thread-local runtime areas:

```text
Thread 1 → Stack + PC
Thread 2 → Stack + PC
Thread 3 → Stack + PC
```

The heap is generally shared:

```text
Thread 1 ──┐
Thread 2 ──┼──► Shared Heap
Thread 3 ──┘
```

This is why shared mutable heap objects can require **synchronization**.

---

## 6. Diagram / Flow chart

### JVM Memory Structure

```text
                    JVM
                     │
          ┌──────────┴──────────┐
          │                     │
     Shared Areas          Per-Thread Areas
          │                     │
    ┌─────┴─────┐        ┌──────┼─────────┐
    │           │        │      │         │
    ▼           ▼        ▼      ▼         ▼
  Heap      Method     Stack    PC     Native
            Area                Register  Stack
             │
             ▼
          Metaspace
```

### Object Allocation

```text
        Java Program
             │
             ▼
      Student s = new Student()
             │
             ▼
       JVM allocates object
             │
             ▼
            Heap
             │
             ▼
       Student Object
             ▲
             │
             │ reference
             │
           Stack
             │
             ▼
              s
```

### Garbage Collection Flow

```text
Object Created
      │
      ▼
Reachable Object
      │
      ▼
References Removed
      │
      ▼
Unreachable Object
      │
      ▼
Eligible for GC
      │
      ▼
Garbage Collector
      │
      ▼
Memory Reclaimed
```

---

## 7. Natural Language Breakdown

Think of JVM memory like a **college campus**.

### Heap = Common Storage Area

Objects are like equipment stored in a common storage room.

```text
Student Object
Car Object
Employee Object
```

Many threads can access objects in this shared area.

### Stack = Personal Desk

Every thread has its own desk.

```text
Thread 1 → Desk 1
Thread 2 → Desk 2
Thread 3 → Desk 3
```

When a method runs, its execution information is placed on that thread's stack.

### Metaspace = College Records Office

It stores information about loaded classes and their metadata.

### Garbage Collector = Cleaning Staff

When an object is no longer reachable:

```text
Object no longer needed
        ↓
Unreachable
        ↓
Eligible for GC
        ↓
GC eventually reclaims memory
```

### Exam Memory Rule

> **Java memory allocation is primarily managed by the JVM through runtime memory areas such as the heap, stacks, and method area, with garbage collection automatically reclaiming memory occupied by unreachable objects.**

### Most Important Distinction

```text
STACK
→ Thread-specific
→ Method frames
→ Local execution state


HEAP
→ Shared among threads
→ Objects and arrays
→ Garbage collected


METASPACE
→ Class metadata
→ Native memory in HotSpot
```
