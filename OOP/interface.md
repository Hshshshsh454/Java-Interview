# Interface in Java

## 0. PYQ Reference & Syllabus Mapping

* **PYQ relevance:** `Interface` is a frequently examined topic in **Java/OOP university examinations**, particularly in questions related to abstraction, inheritance, polymorphism, and multiple inheritance.
* **Common PYQ forms:**

  * Define an interface in Java.
  * Explain the features and uses of interfaces.
  * Differentiate between **abstract class and interface**.
  * Explain multiple inheritance using interfaces.
  * Write a Java program using an interface.
* **Syllabus mapping:**
  **OOP → Abstraction → Interface → Implementation → Multiple Inheritance → Polymorphism**

---

## 1. Definition

An **interface** in Java is a reference type that defines a contract containing method declarations, constants, and, in modern Java, `default`, `static`, and `private` methods, which can be implemented by classes.

A class implements an interface using the **`implements`** keyword.

```java
interface Animal {
    void sound();
}

class Dog implements Animal {
    public void sound() {
        System.out.println("Bark");
    }
}
```

Here:

* `Animal` → interface
* `sound()` → abstract method
* `Dog` → implementing class
* `implements` → keyword used to establish the relationship

---

## 2. Its Use

### 1. Achieve Abstraction

An interface specifies **what an object must do** without necessarily specifying how it does it.

### 2. Achieve Multiple Inheritance

Java does not allow a class to extend multiple classes:

```java
class C extends A, B { }  // Invalid
```

But a class can implement multiple interfaces:

```java
class C implements A, B {
}
```

### 3. Achieve Loose Coupling

Programs can depend on an interface rather than a concrete implementation.

```java
interface Payment {
    void pay();
}

class UPI implements Payment {
    public void pay() {
        System.out.println("UPI Payment");
    }
}
```

The application can work with `Payment` without being tightly coupled to `UPI`.

### 4. Support Polymorphism

```java
Payment p = new UPI();
p.pay();
```

The reference type is `Payment`, while the actual object is `UPI`.

### 5. Define Contracts

An interface establishes a set of operations that implementing classes are expected to provide.

---

## 3. Its Components

An interface may contain:

| Component            | Description                                                      |
| -------------------- | ---------------------------------------------------------------- |
| **Abstract methods** | Methods whose implementation is provided by implementing classes |
| **Constants**        | Fields that are implicitly `public static final`                 |
| **Default methods**  | Methods with implementation, introduced in Java 8                |
| **Static methods**   | Interface-level methods, introduced in Java 8                    |
| **Private methods**  | Helper methods inside interfaces, introduced in Java 9           |
| **Nested types**     | Classes, interfaces, enums, etc., declared inside an interface   |

Example:

```java
interface Vehicle {

    int MAX_SPEED = 120;       // public static final

    void start();              // abstract method

    default void stop() {
        System.out.println("Vehicle stopped");
    }

    static void info() {
        System.out.println("Vehicle interface");
    }
}
```

---

## 4. Its Types

### 1. Normal Interface

An interface containing multiple methods and/or other members.

```java
interface Vehicle {
    void start();
    void stop();
}
```

### 2. Functional Interface

An interface containing **exactly one abstract method**.

```java
@FunctionalInterface
interface Calculator {
    int add(int a, int b);
}
```

It can be used with a **lambda expression**:

```java
Calculator c = (a, b) -> a + b;
```

### 3. Marker Interface

An interface containing **no methods or fields that define behavior**. It is used to provide metadata or mark a class for special treatment.

Examples:

```java
Serializable
Cloneable
```

---

## 5. Sub-types / Sub-topics

Important interface-related topics for examinations:

### A. Interface Implementation

```java
class Dog implements Animal {
    public void sound() {
        System.out.println("Bark");
    }
}
```

### B. Multiple Interface Implementation

```java
interface A {
    void show();
}

interface B {
    void display();
}

class C implements A, B {

    public void show() {
        System.out.println("Show");
    }

    public void display() {
        System.out.println("Display");
    }
}
```

### C. Interface Inheritance

An interface can extend another interface.

```java
interface A {
    void show();
}

interface B extends A {
    void display();
}
```

A class implementing `B` must implement both methods.

### D. Multiple Interface Inheritance

An interface can extend multiple interfaces.

```java
interface A {
    void show();
}

interface B {
    void display();
}

interface C extends A, B {
}
```

### E. Default Method

```java
interface A {
    default void show() {
        System.out.println("Default method");
    }
}
```

### F. Static Method

```java
interface A {
    static void test() {
        System.out.println("Static method");
    }
}
```

Called using:

```java
A.test();
```

### G. Functional Interface + Lambda

```java
@FunctionalInterface
interface Greeting {
    void message();
}

Greeting g = () -> System.out.println("Hello");
g.message();
```

---

## 6. Diagram / Flow chart

### Interface → Implementation

```text
             Interface
                │
                │ defines contract
                ▼
       ┌──────────────────┐
       │     Animal       │
       │──────────────────│
       │ + sound()        │
       └────────┬─────────┘
                │
           implements
                │
                ▼
       ┌──────────────────┐
       │       Dog        │
       │──────────────────│
       │ + sound()        │
       │ { Bark }         │
       └──────────────────┘
```

### Multiple Interfaces

```text
        ┌──────────────┐
        │ Interface A  │
        │  show()      │
        └──────┬───────┘
               │
               │
               ▼
          ┌───────────┐
          │  Class C  │
          │           │
          │ implements│
          │ A, B      │
          └─────▲─────┘
               │
               │
        ┌──────┴───────┐
        │ Interface B  │
        │  display()   │
        └──────────────┘
```

### Interface Hierarchy

```text
        Interface A
             │
          extends
             ▼
        Interface B
             │
          extends
             ▼
        Interface C
             │
        implements
             ▼
          Class D
```

---

## 7. Natural Language Breakdown

Think of an **interface as a contract**.

Suppose a company gives this contract:

```text
Payment System must provide:
    pay()
```

The company does not care **how** payment is performed. It only requires that `pay()` must exist.

Different classes can implement the same contract differently:

```text
                 Payment
              ┌────────────┐
              │   pay()    │
              └─────┬──────┘
                    │
          ┌─────────┼─────────┐
          ▼         ▼         ▼
        UPI       Card      Cash
          │         │         │
       pay()      pay()     pay()
```

So:

* **Interface = Contract**
* **Class = Implementation**
* **`implements` = Accepting the contract**
* **Method = Required behavior**
* **Polymorphism = Same interface, different implementations**

### One-line exam memory

> **An interface defines a contract that specifies the behavior a class must provide, thereby supporting abstraction, polymorphism, loose coupling, and multiple inheritance of type in Java.**
