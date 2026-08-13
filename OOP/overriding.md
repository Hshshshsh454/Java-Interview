# Method Overriding in Java

## 0. PYQ Reference & Syllabus Mapping

* **PYQ relevance:** Frequently asked in Java/OOP university examinations.
* **Common PYQ forms:**

  * Define method overriding with example.
  * Explain runtime polymorphism using method overriding.
  * Differentiate method overloading and method overriding.
  * Explain rules of method overriding.
  * Write a Java program demonstrating method overriding.
* **Syllabus mapping:**
  **OOP → Inheritance → Polymorphism → Runtime Polymorphism → Method Overriding**

---

## 1. Definition

**Method overriding** is a Java mechanism in which a **subclass provides its own implementation of a method that is already defined in its superclass**, with the same method signature.

Example:

```java
class Animal {
    void sound() {
        System.out.println("Animal makes sound");
    }
}

class Dog extends Animal {
    @Override
    void sound() {
        System.out.println("Dog barks");
    }
}
```

Here, `Dog` overrides the `sound()` method inherited from `Animal`.

---

## 2. Its Use

### 1. Runtime Polymorphism

Method overriding enables **runtime polymorphism**, where the method implementation is selected based on the **actual object**, not merely the reference type.

```java
Animal a = new Dog();
a.sound();
```

Output:

```text
Dog barks
```

Although the reference is `Animal`, the actual object is `Dog`, so `Dog.sound()` executes.

### 2. Specialized Behavior

A subclass can modify inherited behavior according to its specific requirements.

```text
Animal → general behavior
Dog    → specialized behavior
```

### 3. Extensibility

Existing superclass behavior can be customized by subclasses without modifying the superclass.

### 4. Supports Dynamic Method Dispatch

The JVM determines the overridden instance method at runtime based on the actual object.

---

## 3. Its Components

| Component          | Description                                      |
| ------------------ | ------------------------------------------------ |
| **Superclass**     | Contains the original method                     |
| **Subclass**       | Provides the new implementation                  |
| **Method name**    | Must be the same                                 |
| **Parameter list** | Must be the same                                 |
| **Return type**    | Same or covariant return type                    |
| **Inheritance**    | Required                                         |
| **`@Override`**    | Recommended annotation for compiler verification |

Example:

```java
class Parent {
    void show() {
        System.out.println("Parent");
    }
}

class Child extends Parent {
    @Override
    void show() {
        System.out.println("Child");
    }
}
```

---

## 4. Its Types

Method overriding is primarily associated with **instance-method overriding**.

### 1. Normal Instance Method Overriding

```java
class A {
    void display() {
        System.out.println("A");
    }
}

class B extends A {
    @Override
    void display() {
        System.out.println("B");
    }
}
```

### 2. Covariant Return Type

An overriding method can return a subtype of the original return type.

```java
class Animal {
}

class Dog extends Animal {
}

class Parent {
    Animal getAnimal() {
        return new Animal();
    }
}

class Child extends Parent {
    @Override
    Dog getAnimal() {
        return new Dog();
    }
}
```

`Dog` is a subtype of `Animal`, so this is valid.

---

## 5. Sub-types / Sub-topics

### A. Access Modifier Rule

An overriding method **cannot reduce the visibility** of the inherited method.

Valid:

```java
class Parent {
    protected void show() {
    }
}

class Child extends Parent {
    @Override
    public void show() {
    }
}
```

Invalid:

```java
class Parent {
    public void show() {
    }
}

class Child extends Parent {
    @Override
    protected void show() {
    }
}
```

`public → protected` reduces visibility.

---

### B. `final` Method Cannot Be Overridden

```java
class Parent {
    final void show() {
    }
}

class Child extends Parent {
    // Cannot override show()
}
```

A `final` method cannot be overridden.

---

### C. `static` Methods Are Not Overridden

Static methods are associated with the class rather than dynamically dispatched like instance methods.

They can be **hidden**, not overridden.

```java
class Parent {
    static void show() {
        System.out.println("Parent");
    }
}

class Child extends Parent {
    static void show() {
        System.out.println("Child");
    }
}
```

This is **method hiding**, not overriding.

---

### D. `private` Methods Cannot Be Overridden

A private method is not inherited by the subclass and therefore cannot be overridden.

```java
class Parent {
    private void show() {
    }
}

class Child extends Parent {
    // This is not overriding
    void show() {
    }
}
```

---

### E. Exception Rule

An overriding method cannot throw a **broader checked exception** than the superclass method.

```java
class Parent {
    void show() throws IOException {
    }
}

class Child extends Parent {
    @Override
    void show() throws FileNotFoundException {
    }
}
```

This is valid because `FileNotFoundException` is a subclass of `IOException`.

---

### F. `@Override` Annotation

`@Override` tells the compiler that the method is intended to override a superclass method.

```java
@Override
void sound() {
    System.out.println("Bark");
}
```

It helps detect mistakes in method signatures.

---

## 6. Diagram / Flow chart

### Basic Overriding

```text
             Parent
        ┌───────────────┐
        │ sound()       │
        │ General Sound │
        └───────┬───────┘
                │
             extends
                │
                ▼
              Dog
        ┌───────────────┐
        │ sound()       │
        │ Bark          │
        └───────────────┘
```

### Runtime Polymorphism

```text
Animal a = new Dog();
        │
        ▼
Reference Type = Animal
        │
        ▼
Actual Object = Dog
        │
        ▼
     a.sound()
        │
        ▼
Dog.sound() executes
        │
        ▼
Runtime Polymorphism
```

### Core Concept

```text
Inheritance
     +
Same Method Signature
     +
Subclass Provides New Implementation
     ↓
Method Overriding
     ↓
Runtime Polymorphism
```

---

## 7. Natural Language Breakdown

Think of a **parent company defining a standard process**.

The parent says:

```text
Animal:
    sound() → "Make a sound"
```

A specific animal can implement that behavior differently:

```text
Animal
  │
  ├── Dog  → sound() = Bark
  │
  ├── Cat  → sound() = Meow
  │
  └── Cow  → sound() = Moo
```

Now:

```java
Animal a = new Dog();
a.sound();
```

The variable is of type `Animal`, but the actual object is `Dog`.

Therefore:

```text
Animal reference
       ↓
Dog object
       ↓
Dog's overridden sound()
       ↓
"Bark"
```

### Exam Memory Rule

> **Same method signature + inheritance + subclass implementation = Method Overriding = Runtime Polymorphism.**

### Overloading vs Overriding — One-Line Difference

| Overloading                      | Overriding                           |
| -------------------------------- | ------------------------------------ |
| Same class generally             | Parent–child relationship            |
| Different parameter list         | Same method signature                |
| Compile-time polymorphism        | Runtime polymorphism                 |
| Inheritance not required         | Inheritance required                 |
| Method selection at compile time | Instance method selection at runtime |
