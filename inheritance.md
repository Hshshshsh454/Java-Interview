# Inheritance in OOP

**Difficulty:** ⭐ Must-Know
**Interview Relevance:** ⭐⭐⭐⭐⭐
**Core OOP Pillar:** Yes

---

## 1. Precise Definition

**Inheritance** is an OOP mechanism in which a new class derives properties and behavior from an existing class and can extend or specialize that behavior.

### Interview-ready answer

> **Inheritance allows a subclass to reuse and specialize the accessible state and behavior of a superclass, establishing an `is-a` relationship between the two types.**

Example:

```text
Vehicle
   ↑
   │
  Car
```

A `Car` **is a** `Vehicle`.

---

# 2. Why Does Inheritance Exist?

Without inheritance, you might duplicate common behavior:

```java
class Car {
    void start() {}
    void stop() {}
}

class Bike {
    void start() {}
    void stop() {}
}
```

With inheritance:

```java
class Vehicle {

    void start() {
        System.out.println("Vehicle starting");
    }

    void stop() {
        System.out.println("Vehicle stopping");
    }
}

class Car extends Vehicle {
}
```

Now:

```java
Car car = new Car();

car.start();
car.stop();
```

The common behavior is defined once.

### Main purposes

* Code reuse
* Specialization
* Polymorphism
* Establishing type relationships
* Extending existing behavior

But **inheritance should not be used merely for code reuse**.

The relationship should generally represent a meaningful **is-a** relationship.

---

# 3. Core Relationship: `is-a`

This is one of the most important interview concepts.

```text
Animal
  ↑
 Dog
```

A dog **is an** animal.

Therefore:

```java
class Dog extends Animal
```

makes conceptual sense.

But:

```text
Car
  ↑
Engine
```

does not make sense because:

> A car is **not** an engine.

That's a **has-a** relationship:

```text
Car ───────► Engine
     has-a
```

which usually suggests **composition**, not inheritance.

---

# 4. Basic Java Example

```java
class Animal {

    void eat() {
        System.out.println("Animal eats");
    }
}

class Dog extends Animal {

    void bark() {
        System.out.println("Dog barks");
    }
}
```

Usage:

```java
public class Main {

    public static void main(String[] args) {

        Dog dog = new Dog();

        dog.eat();
        dog.bark();
    }
}
```

Output:

```text
Animal eats
Dog barks
```

The subclass receives accessible behavior from its superclass.

---

# 5. What Exactly Is Inherited?

This is an important Java interview area.

A subclass can inherit accessible members of its superclass, but there are important exceptions.

| Superclass Member       | Inherited?                                      |
| ----------------------- | ----------------------------------------------- |
| `public` methods        | ✅                                               |
| `protected` methods     | ✅                                               |
| package-private members | Depends on package                              |
| `private` members       | ❌ Directly inaccessible                         |
| Constructors            | ❌                                               |
| Static members          | Accessible through subclass, but not overridden |
| Final methods           | ✅, but cannot be overridden                     |

### Important distinction

A private field can exist inside the superclass object portion, but the subclass **cannot directly access it**.

```java
class Parent {

    private int x;
}

class Child extends Parent {

    void test() {
        // x = 10; ❌
    }
}
```

---

# 6. Types of Inheritance

Java supports:

### 1. Single Inheritance

```text
A
↓
B
```

```java
class B extends A {
}
```

---

### 2. Multilevel Inheritance

```text
A
↓
B
↓
C
```

```java
class A {}
class B extends A {}
class C extends B {}
```

---

### 3. Hierarchical Inheritance

```text
       A
     /   \
    B     C
```

```java
class B extends A {}
class C extends A {}
```

---

### 4. Multiple Inheritance

```text
    A     B
     \   /
       C
```

Java **does not support multiple inheritance of classes**.

This is illegal:

```java
class C extends A, B { } // ❌
```

However, Java supports implementing multiple interfaces:

```java
class C implements A, B {
}
```

---

### 5. Hybrid Inheritance

A combination of multiple inheritance structures.

Java does not support arbitrary hybrid inheritance through classes because it does not support multiple class inheritance.

---

# 7. Why Doesn't Java Support Multiple Class Inheritance?

⭐ **Frequently Asked**

Consider:

```text
       A
      / \
     B   C
      \ /
       D
```

Suppose:

```java
class A {
    void show() {
        System.out.println("A");
    }
}

class B extends A {
}

class C extends A {
    void show() {
        System.out.println("C");
    }
}
```

If `D` inherited from both `B` and `C`, which `show()` should execute?

```text
D.show()
  ?
 / \
B   C
```

This is the classic **diamond problem**.

Java avoids this ambiguity by disallowing multiple inheritance of classes.

---

# 8. Inheritance + Method Overriding

Inheritance becomes particularly powerful when combined with overriding.

```java
class Animal {

    void sound() {
        System.out.println("Animal sound");
    }
}

class Dog extends Animal {

    @Override
    void sound() {
        System.out.println("Bark");
    }
}
```

Now:

```java
Animal animal = new Dog();

animal.sound();
```

Output:

```text
Bark
```

Why?

Because Java uses **runtime method dispatch** for applicable instance methods.

This connects:

```text
Inheritance
     +
Method Overriding
     ↓
Runtime Polymorphism
```

---

# 9. Internal Runtime Model

Consider:

```java
Animal animal = new Dog();
```

There are two important types:

```text
Reference Type
      ↓
    Animal

Object Type
      ↓
      Dog
```

Conceptually:

```text
animal
  │
  │ reference
  ↓
┌──────────────┐
│ Dog Object   │
│              │
│ Animal part  │
│ Dog part     │
└──────────────┘
```

When:

```java
animal.sound();
```

is executed, Java's runtime dispatch mechanism selects the overridden implementation applicable to the actual object.

```text
Animal reference
      ↓
   Dog object
      ↓
runtime dispatch
      ↓
Dog.sound()
```

The exact JVM implementation details can vary, but the Java language semantics require the appropriate overridden instance method to execute.

---

# 10. Constructor Execution

⭐ **Frequently Asked**

Constructors are **not inherited**.

But when a subclass object is created, superclass construction occurs first.

Example:

```java
class Parent {

    Parent() {
        System.out.println("Parent");
    }
}

class Child extends Parent {

    Child() {
        System.out.println("Child");
    }
}
```

```java
Child obj = new Child();
```

Output:

```text
Parent
Child
```

Conceptually:

```text
new Child()
    ↓
Parent constructor
    ↓
Child constructor
```

If you don't explicitly write a constructor invocation, Java may insert an implicit `super()` where permitted.

---

# 11. `super` Keyword

`super` refers to the superclass portion/current superclass context.

### Calling superclass method

```java
class Animal {

    void sound() {
        System.out.println("Animal sound");
    }
}

class Dog extends Animal {

    @Override
    void sound() {

        super.sound();

        System.out.println("Bark");
    }
}
```

Output:

```text
Animal sound
Bark
```

### Calling superclass constructor

```java
class Dog extends Animal {

    Dog() {
        super();
    }
}
```

`super()` must appear as the first statement of a constructor when explicitly used.

---

# 12. `private`, `final`, and `static` Methods

These are major interview traps.

### Private

```java
class Parent {

    private void show() {}
}
```

A subclass cannot override this method because the method is not accessible/inherited as an overridable method.

---

### Final

```java
class Parent {

    final void show() {}
}
```

The method can be inherited, but cannot be overridden.

```java
class Child extends Parent {

    // void show() {} ❌
}
```

---

### Static

Static methods are associated with the class rather than dynamically dispatched like instance methods.

If a subclass declares a static method with the same signature, this is **method hiding**, not overriding.

---

# 13. Inheritance vs Composition

⭐ **Very Important**

### Inheritance

```text
Car
 ↑
Vehicle
```

**is-a**

### Composition

```text
Car ─────► Engine
            ↑
          has-a
```

### Comparison

| Inheritance                   | Composition                     |
| ----------------------------- | ------------------------------- |
| Is-a relationship             | Has-a relationship              |
| Strong coupling               | Generally more flexible         |
| Reuses/extends type behavior  | Delegates behavior              |
| Creates class hierarchy       | Creates object collaboration    |
| Can become rigid              | Easier to replace components    |
| Supports subtype polymorphism | Supports composition/delegation |

---

# 14. Why "Composition Over Inheritance"?

Suppose:

```java
class Car extends Engine {
}
```

This is conceptually wrong.

Instead:

```java
class Car {

    private Engine engine;

    void start() {
        engine.start();
    }
}
```

Now:

```text
Car
 │
 └── Engine
```

The car **has an engine**.

### Design principle

> Prefer composition when you need to reuse behavior without creating a genuine subtype relationship.

Inheritance is not automatically bad.

Use inheritance when **substitutability and type hierarchy make sense**.

---

# 15. Liskov Substitution Principle

Inheritance strongly connects with **LSP**.

The principle says, in practical terms:

> A subtype should be usable wherever its supertype is expected without violating the expected behavior.

Example:

```text
Bird
 ↑
Penguin
```

If `Bird` defines:

```java
void fly()
```

then making `Penguin` inherit from it can create a design problem because penguins cannot fly.

This suggests the abstraction is wrong.

A better model might separate:

```text
Bird
 │
 ├── FlyingBird
 │
 └── Penguin
```

This is an important **inheritance design interview question**.

---

# 16. Real-World Production Example

Suppose an e-commerce system has:

```text
Payment
   ↑
   ├── CardPayment
   ├── UPIPayment
   └── NetBankingPayment
```

However, in production code, you may prefer an interface:

```java
interface PaymentProcessor {

    void process(double amount);
}
```

Then:

```java
class CardPayment implements PaymentProcessor {
    
    public void process(double amount) {
        // Card logic
    }
}
```

This illustrates an important distinction:

> **Not every "is-a" conceptual relationship needs class inheritance.**

Interfaces are often preferable when the goal is to define a capability/contract rather than share implementation/state.

---

# 17. Common Interview Traps

### Can constructors be inherited?

❌ No.

### Can constructors be overridden?

❌ No.

### Can private methods be overridden?

❌ No.

### Can final methods be overridden?

❌ No.

### Can static methods be overridden?

❌ No. They can be **hidden**.

### Does Java support multiple inheritance?

❌ Not through classes.

### Can Java implement multiple interfaces?

✅ Yes.

```java
class C implements A, B {
}
```

### Does a subclass automatically get access to private superclass fields?

❌ No.

### Is inheritance always better than composition?

❌ No.

### Is inheritance primarily for code reuse?

**Incomplete.**

Inheritance should primarily model a meaningful subtype relationship; code reuse is a consequence/benefit, not sufficient justification.

---

# 18. Bad Design vs Good Design

### ❌ Bad

```java
class Engine {
    void start() {}
}

class Car extends Engine {
}
```

Problem:

```text
Car is-a Engine ❌
```

### ✅ Good

```java
class Car {

    private Engine engine;

    void start() {
        engine.start();
    }
}
```

Relationship:

```text
Car has-an Engine ✅
```

---

# 19. Interviewer Follow-Up Chain

A realistic interview progression:

```text
What is inheritance?
       ↓
Why do we need it?
       ↓
What is an is-a relationship?
       ↓
What are inheritance types?
       ↓
Does Java support multiple inheritance?
       ↓
Why not?
       ↓
What is the diamond problem?
       ↓
Are constructors inherited?
       ↓
Can private methods be overridden?
       ↓
What is method hiding?
       ↓
How does overriding work at runtime?
       ↓
Inheritance vs composition?
       ↓
Why composition over inheritance?
       ↓
Explain Liskov Substitution Principle.
       ↓
Give an example where inheritance causes bad design.
```

---

# 20. Common Candidate Mistakes

### ❌ Weak answer

> Inheritance means one class gets properties of another class.

**Problem:** Too vague.

### Better answer

> Inheritance is a mechanism where a subclass derives accessible behavior and state from a superclass and can specialize that behavior. It establishes an `is-a` relationship and enables subtype polymorphism.

---

### ❌ Weak answer

> Inheritance is used for code reuse.

**Problem:** Incomplete.

Better:

> Inheritance can provide code reuse, but its more important role is modeling a subtype relationship and enabling polymorphism. Using inheritance solely for implementation reuse can produce tight coupling and fragile hierarchies.

---

# 21. 30-Second Revision

```text
INHERITANCE
     ↓
Superclass
     ↓
Subclass
     ↓
Reuse + Specialization
     ↓
"is-a" relationship
     ↓
Overriding
     ↓
Runtime Polymorphism
```

### Remember these five points:

1. **`extends` → class inheritance**
2. **Constructors are not inherited**
3. **Private methods are not overridden**
4. **Static methods are hidden, not overridden**
5. **Use inheritance for genuine subtype relationships; prefer composition when you need flexible behavior reuse**

---

# 22. Master Interview Test

Answer these without looking back:

1. What is inheritance?
2. Why does OOP provide inheritance?
3. What is an `is-a` relationship?
4. What types of inheritance does Java support?
5. Why doesn't Java support multiple class inheritance?
6. Are constructors inherited or overridden?
7. What happens when a superclass reference refers to a subclass object?
8. **Why are static methods hidden rather than overridden?**
9. **When would you choose composition instead of inheritance?**
10. **Explain how a badly designed inheritance hierarchy can violate the Liskov Substitution Principle.**
