# Garbage Collection (GC) in Java

## 0. PYQ Reference & Syllabus Mapping

* **PYQ relevance:** **Very important** topic under Java memory management and JVM.
* **Common PYQ forms:**

  * Define Garbage Collection.
  * Explain the working of Garbage Collector.
  * Explain eligible objects for garbage collection.
  * Explain `System.gc()`.
  * Explain different GC algorithms.
  * Differentiate `finalize()` and Garbage Collection.
  * Explain heap generations.
* **Syllabus mapping:**
  **Java → JVM → Memory Management → Heap → Garbage Collection → Generational GC → GC Algorithms**

---

## 1. Definition

**Garbage Collection (GC)** is an **automatic memory-management mechanism of the JVM that identifies objects that are no longer reachable by the running program and reclaims the memory associated with those objects**.

Example:

```java
class Demo {
    public static void main(String[] args) {

        Demo d = new Demo();

        d = null;   // Object may become unreachable
    }
}
```

After:

```java
d = null;
```

if no other reachable reference points to the `Demo` object, that object becomes **eligible for garbage collection**.

> **GC automatically reclaims memory occupied by unreachable objects.**

---

## 2. Its Use

### 1. Automatic Memory Management

Java does not normally require programmers to explicitly release object memory using `free()` or `delete`.

### 2. Prevent Memory Accumulation

GC reclaims memory occupied by objects that are no longer reachable.

### 3. Reduce Memory Leaks Caused by Unused Objects

Objects that become unreachable can eventually be reclaimed.

### 4. Improve Application Reliability

Automatic memory management reduces errors associated with manual memory deallocation.

### 5. Heap Management

GC manages the Java heap by identifying and reclaiming memory associated with dead objects.

---

## 3. Its Components

### 1. Heap

Garbage collection primarily operates on the **Java heap**, where objects and arrays are allocated.

### 2. Reachability

The GC determines whether objects are reachable from **GC roots**.

Typical GC roots include:

* Active thread references
* Local variables in active stack frames
* Static references
* JNI references

### 3. Garbage Collector

The JVM's GC implementation identifies unreachable objects and reclaims their memory.

### 4. Young Generation

Traditionally associated with recently allocated objects.

```text
Eden
Survivor 0
Survivor 1
```

### 5. Old Generation

Traditionally contains objects that survive multiple young-generation collections.

### 6. GC Roots

Objects directly reachable from roots form the starting point for reachability analysis.

---

## 4. Its Types

GC implementations in Java vary by JVM and configuration. Important collectors include:

### 1. Serial GC

Uses a single GC thread for collection work.

Suitable for certain smaller or simpler applications.

### 2. Parallel GC

Uses multiple threads for garbage-collection work.

It is designed for high throughput.

### 3. G1 Garbage Collector

**G1 (Garbage-First) GC** divides the heap into regions and attempts to prioritize regions with more reclaimable garbage.

It is designed to provide a balance between throughput and pause-time goals.

### 4. Z Garbage Collector (ZGC)

A low-latency collector designed for very large heaps and short pause times.

### 5. Shenandoah GC

A low-pause garbage collector designed to perform much of its work concurrently with application execution.

---

## 5. Sub-types / Sub-topics

### A. Eligible for Garbage Collection

An object becomes eligible when it is no longer reachable.

```java
Demo d = new Demo();

d = null;
```

Conceptually:

```text id="4k2nq4"
Before:

d ───────► Object


After:

d ───────► null

Object
  ↓
Unreachable
  ↓
Eligible for GC
```

**Important:** Eligible for GC does **not** mean the object is immediately collected.

---

### B. `System.gc()`

Java provides:

```java
System.gc();
```

It requests that the JVM perform garbage collection.

However:

> **`System.gc()` does not guarantee that GC will immediately occur.**

The JVM ultimately decides whether and when collection occurs.

---

### C. `Runtime.getRuntime().gc()`

Another way to request GC is:

```java
Runtime.getRuntime().gc();
```

It has the same fundamental limitation: it is a **request**, not a guarantee.

---

### D. Generational Garbage Collection

The traditional generational model is based on the observation that **most objects die young**.

Conceptually:

```text id="31f6b4"
                Heap
                 │
        ┌────────┴────────┐
        │                 │
        ▼                 ▼
Young Generation     Old Generation
        │                 │
     New Objects      Long-lived Objects
        │
        ▼
 Minor/Young GC
        │
        ▼
 Surviving Objects
        │
        ▼
 Old Generation
```

---

### E. Mark and Sweep

A simplified GC algorithm:

```text id="3e4k9p"
Objects
   ↓
Mark reachable objects
   ↓
Identify unreachable objects
   ↓
Sweep/reclaim their memory
```

---

### F. Mark-Compact

After identifying unreachable objects, surviving objects may be compacted to reduce fragmentation.

```text id="2gqfkp"
Before:

[Live][Dead][Live][Dead][Live]

After:

[Live][Live][Live][Free][Free]
```

---

### G. Stop-the-World

Some GC phases may temporarily pause application threads.

```text id="p3s0pr"
Application Threads
██████████  PAUSE  ██████████
                 ↑
                GC
```

Modern collectors such as G1, ZGC, and Shenandoah perform substantial work concurrently to reduce pause times, but **"concurrent" does not mean that every GC operation is completely pause-free**.

---

### H. `finalize()` — Important Exam Point

Historically, Java provided:

```java
protected void finalize() throws Throwable {
}
```

However, **finalization has been deprecated since Java 9 and is deprecated for removal in modern Java**.

It should not be used as a normal resource-management mechanism.

For resources such as files or database connections, use:

```java
try (FileInputStream file = new FileInputStream("data.txt")) {
    // use resource
}
```

This uses **try-with-resources** and `AutoCloseable`.

---

### I. Memory Leak vs Garbage Collection

Garbage collection does not eliminate every possible memory leak.

Example:

```java
static List<Object> list = new ArrayList<>();
```

If objects remain reachable through `list` even though the application no longer logically needs them, GC cannot reclaim them.

```text id="zqu2m7"
Static List
    │
    ▼
Object
    │
    ▼
Still reachable
    │
    ▼
GC cannot reclaim it
```

This is a common form of **logical memory leak** in managed-memory languages.

---

## 6. Diagram / Flow chart

### Basic GC Process

```text id="q7u8yo"
             Object Creation
                   │
                   ▼
                 Heap
                   │
                   ▼
            Object is used
                   │
                   ▼
       References disappear
                   │
                   ▼
       Is object reachable?
              /          \
            YES           NO
             │             │
             ▼             ▼
         Keep object    Mark eligible
                           │
                           ▼
                    Garbage Collector
                           │
                           ▼
                    Reclaim memory
```

### Reachability Model

```text id="8unmzh"
                     GC Roots
                        │
             ┌──────────┴──────────┐
             ▼                     ▼
         Object A               Object B
             │
             ▼
         Object C

Object D ──────────► No reference

A, B, C → Reachable
D       → Unreachable → Eligible for GC
```

### Generational Model

```text id="1h6az4"
                    Heap
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
        Young                   Old
     Generation             Generation
          │                     │
       Eden +               Long-lived
     Survivors                objects
          │
          ▼
      GC occurs
          │
          ▼
  Surviving objects
          │
          ▼
     May be promoted
       to Old Gen
```

---

## 7. Natural Language Breakdown

Think of GC like **automatic housekeeping in a building**.

Objects are like items placed in rooms.

```text id="2o4wpx"
Create object
     ↓
Object is being used
     ↓
Object is no longer reachable
     ↓
Nobody can access it
     ↓
Housekeeping identifies it
     ↓
Space can eventually be reclaimed
```

The key concept is **reachability**.

Suppose:

```java id="w6v6r1"
Student s = new Student();
```

Initially:

```text id="xq18fr"
s ───────► Student Object
```

The object is reachable.

Then:

```java id="y3j3sa"
s = null;
```

Now:

```text id="8u6ygr"
s ───────► null

Student Object
      ↓
No reachable reference
      ↓
Eligible for GC
```

The GC can eventually reclaim that object's memory.

### Exam Memory Rule

> **Garbage Collection is the JVM's automatic mechanism for reclaiming heap memory occupied by objects that are no longer reachable from GC roots.**

### Remember These 5 Points

```text id="7xk1j2"
GC
│
├── Automatic memory management
├── Mainly operates on Heap
├── Uses reachability analysis
├── Unreachable objects become eligible
└── Collection timing is controlled by JVM
```

**Most important:** `System.gc()` **requests** garbage collection; it does **not guarantee immediate collection**.
