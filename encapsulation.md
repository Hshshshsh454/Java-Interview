# Encapsulation in OOP

**Difficulty:** ⭐ Must-Know
**Interview Relevance:** ⭐⭐⭐⭐⭐
**Core OOP Pillar:** Yes

---

## 1. Precise Definition

**Encapsulation** is the OOP principle of **bundling an object's state (data) and behavior (methods) together while controlling external access to that state**.

### Interview-ready answer

> **Encapsulation protects an object's internal state by restricting direct access and exposing controlled operations through methods. It helps maintain data integrity, enforce invariants, and reduce coupling.**

---

# 2. Why Does Encapsulation Exist?

Consider a bank account.

Without encapsulation:

```java
account.balance = -50000;
```

Any external code could directly modify the balance.

With encapsulation:

```text
External Code
     ↓
 deposit()
 withdraw()
     ↓
Private balance
```

The object controls **how its state can change**.

For example:

```java
class BankAccount {

    private double balance;

    public void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
        }
    }
}
```

Now this is impossible:

```java
account.balance = -50000; // ❌
```

The object maintains control over its own state.

---

# 3. Core Mechanism

In Java, encapsulation is commonly implemented using:

* `private` fields
* `public`/controlled methods
* Access modifiers
* Validation
* Immutable objects where appropriate

The basic structure is:

```text
              Object
        ┌─────────────────┐
        │ private state   │
        │                 │
        │   balance       │
        │   password      │
        │   accountId     │
        │                 │
        │ controlled      │
        │ behavior        │
        │                 │
        │ deposit()       │
        │ withdraw()      │
        └────────┬────────┘
                 ↑
          Controlled Access
                 ↑
          External Code
```

---

# 4. Basic Java Example

```java
class BankAccount {

    private double balance;

    public void deposit(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("Invalid amount");
        }

        balance += amount;
    }

    public double getBalance() {
        return balance;
    }
}
```

Usage:

```java
public class Main {

    public static void main(String[] args) {

        BankAccount account = new BankAccount();

        account.deposit(5000);

        System.out.println(account.getBalance());
    }
}
```

### Important point

The caller cannot directly modify:

```java
balance
```

because it is:

```java
private
```

Instead, the caller must use:

```java
deposit()
```

which allows the class to enforce rules.

---

# 5. Encapsulation Is More Than Getters and Setters

⭐ **Important interview trap**

A common beginner definition is:

> "Encapsulation means making variables private and generating getters/setters."

That's **incomplete**.

This:

```java
class User {

    private int age;

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

technically restricts direct field access, but the setter may expose unrestricted mutation.

Better:

```java
class User {

    private int age;

    public void setAge(int age) {

        if (age < 0) {
            throw new IllegalArgumentException("Age cannot be negative");
        }

        this.age = age;
    }
}
```

The important principle is:

> **The object controls access to its state.**

Not simply:

> **"Use getters and setters."**

---

# 6. Encapsulation and Data Validation

This is one of its most important practical benefits.

```java
class Employee {

    private double salary;

    public void setSalary(double salary) {

        if (salary < 0) {
            throw new IllegalArgumentException(
                "Salary cannot be negative"
            );
        }

        this.salary = salary;
    }
}
```

The class maintains an **invariant**:

```text
salary >= 0
```

External code cannot bypass the rule through the public API.

---

# 7. Encapsulation + Immutability

Strong encapsulation often works with immutable objects.

```java
final class User {

    private final String username;

    public User(String username) {
        this.username = username;
    }

    public String getUsername() {
        return username;
    }
}
```

There is no setter.

Therefore:

```text
Object Creation
      ↓
 State initialized
      ↓
 State cannot be changed
      ↓
 Immutable Object
```

This is useful for:

* Thread safety
* Predictability
* Caching
* Value objects
* Concurrent systems

---

# 8. Access Modifiers

Encapsulation relies heavily on Java access control.

| Modifier    | Same Class | Same Package | Subclass | Everywhere |
| ----------- | ---------: | -----------: | -------: | ---------: |
| `private`   |          ✅ |            ❌ |       ❌* |          ❌ |
| default     |          ✅ |            ✅ |      ✅** |          ❌ |
| `protected` |          ✅ |            ✅ |        ✅ |          ❌ |
| `public`    |          ✅ |            ✅ |        ✅ |          ✅ |

* A subclass cannot directly access a superclass's `private` members.

** Package access has important rules for inheritance and access.

### Typical encapsulation approach

```java
private
```

for internal state and controlled methods for operations.

---

# 9. Encapsulation vs Abstraction

⭐ **Frequently Asked**

| Encapsulation                                  | Abstraction                                 |
| ---------------------------------------------- | ------------------------------------------- |
| Protects internal state                        | Hides unnecessary implementation complexity |
| Controls access                                | Exposes essential behavior                  |
| Focuses on **access/control**                  | Focuses on **what is exposed**              |
| Uses access modifiers, APIs, object boundaries | Uses interfaces, abstract classes, APIs     |
| Helps preserve invariants                      | Helps manage complexity                     |
| Implementation/design mechanism                | Design-level concept                        |

### Mental model

```text
Abstraction
    ↓
"What should the user know?"

Encapsulation
    ↓
"What should the user be allowed to access/change?"
```

---

# 10. Encapsulation + Inheritance

Suppose:

```java
class Vehicle {

    private int speed;

    public int getSpeed() {
        return speed;
    }
}
```

A subclass:

```java
class Car extends Vehicle {

}
```

cannot directly access:

```java
speed
```

because `speed` is private.

It must use the exposed API.

This protects the superclass's internal representation.

---

# 11. Encapsulation + Polymorphism

Encapsulation allows implementations to change internally without necessarily affecting clients.

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
        // Internal implementation
    }
}
```

The caller only uses:

```java
payment.pay(1000);
```

Internal payment processing can change without changing the client-facing operation.

This is where **encapsulation, abstraction, and polymorphism** often work together.

---

# 12. Internal Working

At the JVM level, `private` is an access-control property enforced by Java's language/runtime model.

Consider:

```java
class Account {

    private int balance;
}
```

Conceptually:

```text
Class Account
     │
     ├── balance
     │     ↓
     │   private
     │
     └── methods
           ↓
      Controlled Access
```

External code attempting:

```java
account.balance
```

does not satisfy Java's access rules and results in a compilation error.

The important distinction:

> Encapsulation is primarily a **design principle**, while `private` is one of the **language mechanisms** used to implement it.

---

# 13. Bad Design vs Good Design

### ❌ Bad

```java
class BankAccount {

    public double balance;
}
```

Anyone can do:

```java
account.balance = -100000;
```

Problems:

* No validation
* No control
* Weak invariants
* High coupling to representation

### ✅ Better

```java
class BankAccount {

    private double balance;

    public void withdraw(double amount) {

        if (amount <= 0) {
            throw new IllegalArgumentException();
        }

        if (amount > balance) {
            throw new IllegalStateException("Insufficient balance");
        }

        balance -= amount;
    }
}
```

Now the object owns the rules governing its state.

---

# 14. Production Example

Consider an e-commerce `Order`.

You don't want arbitrary code doing:

```java
order.status = "SHIPPED";
```

Instead:

```java
order.ship();
```

The method can enforce:

```text
CREATED
   ↓
CONFIRMED
   ↓
PAID
   ↓
SHIPPED
   ↓
DELIVERED
```

For example:

```java
class Order {

    private OrderStatus status;

    public void ship() {

        if (status != OrderStatus.PAID) {
            throw new IllegalStateException(
                "Order must be paid before shipping"
            );
        }

        status = OrderStatus.SHIPPED;
    }
}
```

This is **domain encapsulation**.

The object protects a business invariant.

---

# 15. Design Principle Connection

Encapsulation strongly supports:

### High Cohesion

Data and behavior that operate on that data stay together.

```text
Order
 ├── order data
 ├── validate()
 ├── cancel()
 ├── ship()
 └── calculateTotal()
```

### Low Coupling

External classes don't need to know the internal representation.

```text
Client
   ↓
Order API
   ↓
Internal State
```

### Information Hiding

Implementation details can change without forcing clients to change.

---

# 16. Common Interview Traps

### Can encapsulation exist without `private`?

**Yes.**

`private` is a common mechanism, but encapsulation is the broader design principle of controlling access to state and implementation.

---

### Are getters and setters always good encapsulation?

**No.**

Blindly exposing every field through getters/setters can weaken encapsulation.

Prefer meaningful operations:

```java
order.cancel();
```

instead of:

```java
order.setStatus(CANCELLED);
```

when business rules govern the transition.

---

### Does `private` automatically mean good encapsulation?

**No.**

You can have private fields and still expose a poor API.

Good encapsulation requires:

* Appropriate visibility
* Controlled mutation
* Strong invariants
* Meaningful public behavior
* Hidden implementation details

---

# 17. Interview Follow-Up Chain

A strong interviewer may go:

### Q1

**What is encapsulation?**

↓

### Q2

**Why do we need it?**

↓

### Q3

**How do you implement it in Java?**

↓

### Q4

**Is encapsulation just private variables + getters/setters?**

↓

### Q5

**What is the difference between encapsulation and abstraction?**

↓

### Q6

**Can you give a real-world example?**

↓

### Q7

**How does encapsulation improve maintainability?**

↓

### Q8

**Can a subclass access a private field? Why not?**

↓

### Q9

**Can we achieve encapsulation without getters/setters?**

↓

### Q10

**How would you design an immutable class with strong encapsulation?**

That progression is much closer to an actual interview.

---

# 18. Common Candidate Mistakes

### ❌ Weak

> Encapsulation means hiding data using private variables.

### Problem

This describes only one mechanism.

### ✅ Better

> Encapsulation is the design principle of keeping an object's state and behavior together while controlling how external code accesses and modifies that state. In Java, access modifiers such as `private`, combined with controlled APIs, are commonly used to achieve it.

---

# 19. 30-Second Revision

```text
ENCAPSULATION
      ↓
Bundle state + behavior
      ↓
Control external access
      ↓
Protect invariants
      ↓
Reduce coupling
      ↓
Hide implementation details
```

### Remember:

**Abstraction → What should be exposed?**

**Encapsulation → What should be accessible and how can state change?**

**Inheritance → What can be reused/extended?**

**Polymorphism → Which implementation can be used through a common type?**

---

# 20. Master Interview Test

Try answering these **without looking back**:

1. What is encapsulation?
2. Why is encapsulation important?
3. Is `private` synonymous with encapsulation?
4. Why are getters and setters not sufficient to guarantee good encapsulation?
5. What is the difference between encapsulation and abstraction?
6. How does encapsulation help maintain object invariants?
7. Can a subclass directly access a superclass's private field?
8. How would you design a strongly encapsulated `BankAccount`?
9. How does encapsulation reduce coupling?
10. **Expert:** How would you design an immutable, thread-safe value object while preserving strong encapsulation?
