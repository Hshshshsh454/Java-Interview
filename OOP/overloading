# Method Overloading in Java

## 0. PYQ Reference & Syllabus Mapping

* **PYQ relevance:** Frequently asked in Java/OOP university examinations.
* **Common PYQ forms:**

  * Define method overloading with example.
  * Explain compile-time polymorphism.
  * Differentiate method overloading and method overriding.
  * Explain rules of method overloading.
  * Write a Java program demonstrating method overloading.
* **Syllabus mapping:**
  **OOP → Polymorphism → Compile-Time Polymorphism → Method Overloading**

---

## 1. Definition

**Method overloading** is a feature of Java in which **multiple methods within the same class have the same method name but different parameter lists**.

The parameter list can differ in:

* Number of parameters
* Type of parameters
* Order of parameters

Example:

```java
class Calculator {

    int add(int a, int b) {
        return a + b;
    }

    int add(int a, int b, int c) {
        return a + b + c;
    }

    double add(double a, double b) {
        return a + b;
    }
}
```

All three methods are named `add`, but their parameter lists are different.

---

## 2. Its Use

### 1. Compile-Time Polymorphism

Method overloading is an example of **compile-time polymorphism** because the compiler determines which method should be called.

```java
Calculator c = new Calculator();

c.add(10, 20);        // add(int, int)
c.add(10, 20, 30);    // add(int, int, int)
c.add(10.5, 20.5);    // add(double, double)
```

### 2. Improves Readability

Instead of using different names:

```java
addTwoNumbers()
addThreeNumbers()
addDoubleNumbers()
```

we can use one meaningful name:

```java
add()
```

### 3. Provides Multiple Ways to Perform an Operation

The same operation can accept different input combinations.

---

## 3. Its Components

A method-overloading declaration consists of:

| Component       | Role                                            |
| --------------- | ----------------------------------------------- |
| Method name     | Must generally remain the same                  |
| Parameter count | Can be different                                |
| Parameter types | Can be different                                |
| Parameter order | Can be different                                |
| Return type     | **Cannot alone distinguish overloaded methods** |

### Valid

```java
void display(int x) { }

void display(int x, int y) { }
```

### Valid

```java
void display(int x) { }

void display(double x) { }
```

### Valid

```java
void display(int x, double y) { }

void display(double x, int y) { }
```

### Invalid

```java
int display(int x) {
    return x;
}

double display(int x) {
    return x;
}
```

Changing **only the return type** does not constitute method overloading.

---

## 4. Its Types

Method overloading can be classified according to how the parameter list differs.

### 1. Different Number of Parameters

```java
class Test {

    void show(int a) {
        System.out.println(a);
    }

    void show(int a, int b) {
        System.out.println(a + " " + b);
    }
}
```

### 2. Different Data Types

```java
class Test {

    void show(int a) {
        System.out.println(a);
    }

    void show(double a) {
        System.out.println(a);
    }
}
```

### 3. Different Order of Parameters

```java
class Test {

    void show(int a, double b) {
        System.out.println("int-double");
    }

    void show(double a, int b) {
        System.out.println("double-int");
    }
}
```

---

## 5. Sub-types / Sub-topics

### A. Constructor Overloading

Constructors can also be overloaded.

```java
class Student {

    Student() {
    }

    Student(int id) {
    }

    Student(int id, String name) {
    }
}
```

### B. Static Method Overloading

Static methods can be overloaded.

```java
class Test {

    static void print(int x) {
    }

    static void print(String x) {
    }
}
```

### C. Varargs and Overloading

Varargs can participate in overload resolution.

```java
void show(int a) {
}

void show(int... a) {
}
```

For a call such as:

```java
show(10);
```

the fixed-arity method is generally preferred over the variable-arity method.

### D. Overloading and Type Promotion

Java may perform primitive widening when resolving an overloaded method.

```java
void show(int x) {
    System.out.println("int");
}

void show(double x) {
    System.out.println("double");
}
```

For:

```java
show(10);
```

Java selects:

```text
show(int)
```

because it is an exact match.

---

## 6. Diagram / Flow chart

```text
                 Method Call
                     │
                     ▼
              ┌─────────────┐
              │ Method Name │
              │    add()    │
              └──────┬──────┘
                     │
                     ▼
             Examine Parameters
                     │
          ┌──────────┼──────────┐
          ▼          ▼          ▼
       int,int   int,int,int  double,double
          │          │          │
          ▼          ▼          ▼
      add(int,2) add(int,3) add(double,2)
          │          │          │
          └──────────┼──────────┘
                     ▼
              Selected Method
                     │
                     ▼
              Compile Time
```

### Core Concept

```text
Same Method Name
       +
Different Parameter List
       ↓
Method Overloading
       ↓
Compile-Time Polymorphism
```

---

## 7. Natural Language Breakdown

Think of a **calculator's `add()` button**.

You want the same operation—addition—but you may provide different numbers of inputs:

```text
add(10, 20)
add(10, 20, 30)
add(10.5, 20.5)
```

The calculator understands that these are different versions of the same operation.

In Java:

```text
                 add()
                  │
        ┌─────────┼─────────┐
        ▼         ▼         ▼
    2 integers  3 integers  2 doubles
        │         │         │
        ▼         ▼         ▼
    add(int,int)
    add(int,int,int)
    add(double,double)
```

The **method name stays the same**, but the **parameters change**.

### Exam Memory Rule

> **Same name + different parameter list = Method Overloading = Compile-Time Polymorphism.**

**Important:** Changing only the return type is **not** method overloading.
