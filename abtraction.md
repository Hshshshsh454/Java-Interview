# Abstraction in OOP

**Difficulty:** ⭐ Must-Know
**Interview Relevance:** ⭐⭐⭐⭐⭐
**Core OOP Pillar:** Yes

## 1. Precise Definition

**Abstraction** is the OOP principle of **exposing only the essential behavior of an object while hiding its implementation details**.

### Interview-ready answer

> **Abstraction focuses on what an object does rather than how it does it. It reduces implementation complexity by exposing a controlled interface while hiding internal details.**

---

## 2. Why Does Abstraction Exist?

Suppose you use:

```java
payment.pay(1000);
```

You don't need to know whether internally the payment system uses:

* HTTP API
* database transaction
* encryption
* authentication
* retry logic
* Kafka
* third-party payment gateway

You only need to know **what operation is available**.

So abstraction provides:

```text
Complex Implementation
        ↓
      HIDDEN
        ↓
Simple Interface
        ↓
      USER
```

### Main benefits

* Reduces complexity
* Reduces coupling
* Improves maintainability
* Makes implementations replaceable
* Supports polymorphism
* Improves extensibility

---

# 3. How Is Abstraction Achieved in Java?

Primarily through:

### 1. Abstract Classes

```java
abstract class Animal {

    abstract void sound();

    void sleep() {
        System.out.println("Sleeping");
    }
}
```

### 2. Interfaces

```java
interface Payment {
    void pay(double amount);
}
```

The interface exposes **what must be done**, while implementations determine **how it is done**.

---

# 4. Abstract Class Example

```java
abstract class Animal {

    abstract void sound();

    void eat() {
        System.out.println("Eating");
    }
}

class Dog extends Animal {

    @Override
    void sound() {
        System.out.println("Bark");
    }
}

public class Main {
    public static void main(String[] args) {

        Animal animal = new Dog();

        animal.sound();
        animal.eat();
    }
}
```

Output:

```text
Bark
Eating
```

### What's happening?

```text
Animal
  │
  │ abstract sound()
  │ concrete eat()
  ↓
Dog
  │
  └── implements sound()
```

`Animal` defines the abstraction.

`Dog` provides the implementation.

---

# 5. Interface Example

```java
interface Payment {
    void pay(double amount);
}
```

Implementation:

```java
class UPI implements Payment {

    @Override
    public void pay(double amount) {
        System.out.println("Processing UPI payment");
    }
}
```

Another implementation:

```java
class CreditCard implements Payment {

    @Override
    public void pay(double amount) {
        System.out.println("Processing card payment");
    }
}
```

Client code:

```java
Payment payment = new UPI();

payment.pay(1000);
```

The client depends on:

```text
Payment
   ↑
   │
   ├── UPI
   │
   └── CreditCard
```

Therefore, we can change:

```java
Payment payment = new UPI();
```

to:

```java
Payment payment = new CreditCard();
```

without changing the client-facing operation:

```java
payment.pay(1000);
```

This is one of the most important practical benefits of abstraction.

---

# 6. Abstraction vs Encapsulation

⭐ **Frequently Asked**

| Abstraction                                       | Encapsulation                                       |
| ------------------------------------------------- | --------------------------------------------------- |
| Focuses on **what**                               | Focuses on **how data/implementation is protected** |
| Hides complexity                                  | Hides internal state/details                        |
| Design-level concept                              | Implementation mechanism/principle                  |
| Usually achieved with interfaces/abstract classes | Commonly achieved with access modifiers             |
| Answers "What can this object do?"                | Answers "How is its internal state controlled?"     |

### Simple distinction

```text
Abstraction
    ↓
What should the user see?

Encapsulation
    ↓
How should the internal state be protected?
```

---

# 7. Abstraction + Polymorphism

These concepts work together.

```java
Payment payment = new UPI();
payment.pay(1000);
```

Here:

**Abstraction**

```text
Payment interface
```

defines what the system can do.

**Polymorphism**

```text
UPI
CreditCard
PayPal
```

allows different implementations to be used through the same abstraction.

```text
             Payment
                │
       ┌────────┼────────┐
       ↓        ↓        ↓
      UPI   CreditCard  PayPal
       │        │        │
       └────────┼────────┘
                ↓
          payment.pay()
```

---

# 8. Real-World Spring Boot Example

This pattern appears constantly in backend development.

```java
public interface PaymentService {
    void processPayment(double amount);
}
```

Implementation:

```java
@Service
public class UpiPaymentService implements PaymentService {

    @Override
    public void processPayment(double amount) {
        // UPI gateway logic
    }
}
```

Another:

```java
@Service
public class CardPaymentService implements PaymentService {

    @Override
    public void processPayment(double amount) {
        // Card gateway logic
    }
}
```

The controller can depend on:

```java
private final PaymentService paymentService;
```

rather than directly depending on:

```java
UpiPaymentService
```

This gives:

```text
Controller
    ↓
PaymentService
    ↓
 ┌──┴──────────┐
 ↓             ↓
UPI           Card
```

This reduces coupling and makes implementations easier to replace or test.

---

# 9. Advanced Interview Question

### Why is abstraction important if we already have encapsulation?

**Answer:**

> Encapsulation protects and controls access to internal state and implementation, whereas abstraction exposes only the essential interface and hides unnecessary complexity. They solve related but different problems.

---

# 10. Important Interview Traps

### Can we instantiate an abstract class?

❌ No.

```java
Animal a = new Animal(); // Compilation error
```

But:

```java
Animal a = new Dog();
```

is valid.

---

### Can an abstract class have constructors?

✅ Yes.

```java
abstract class Animal {

    Animal() {
        System.out.println("Animal constructor");
    }
}
```

The constructor executes when a subclass object is created.

---

### Can an abstract class contain concrete methods?

✅ Yes.

```java
abstract class Animal {

    abstract void sound();

    void eat() {
        System.out.println("Eating");
    }
}
```

---

### Can an abstract class contain fields?

✅ Yes.

---

### Can an interface have implemented methods in modern Java?

✅ Yes.

Interfaces can contain:

* `default` methods
* `static` methods
* `private` methods

So the old statement:

> "Interfaces can contain only abstract methods."

is **incorrect for modern Java**.

---

# 11. Abstraction and SOLID

Abstraction strongly connects with:

### Dependency Inversion Principle

> High-level modules should depend on abstractions rather than concrete implementations.

Instead of:

```java
OrderService → UpiPaymentService
```

prefer:

```text
OrderService
     ↓
PaymentService
     ↑
 ┌───┴────┐
UPI      Card
```

This improves:

* Testability
* Extensibility
* Maintainability
* Loose coupling

---

# 12. 30-Second Revision

> **Abstraction means hiding unnecessary implementation details and exposing only essential behavior. In Java, it is primarily implemented using interfaces and abstract classes. It reduces complexity and coupling and works closely with polymorphism and dependency inversion.**

### Remember:

```text
ABSTRACTION
     ↓
WHAT?

ENCAPSULATION
     ↓
HOW IS INTERNAL STATE PROTECTED?

POLYMORPHISM
     ↓
WHICH IMPLEMENTATION?
```

### ⭐ Most important interview question

**"What is the difference between abstraction and encapsulation?"**

If you can answer that clearly **and explain it using an interface + concrete implementation**, you understand the core concept.
