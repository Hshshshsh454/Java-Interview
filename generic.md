# Java Generics

**Difficulty:** ⭐ Intermediate → Expert
**Interview Relevance:** ⭐⭐⭐⭐⭐
**Category:** Java Core / Type System / Collections / Compile Time / Type Erasure / API Design

> **Core idea:** Java Generics provide **compile-time type safety and reusable parameterized types**. Advanced interviews focus on **type erasure, bounded type parameters, wildcards, PECS, invariance, generic methods, generic classes, raw types, bridge methods, and limitations caused by erasure**.

---

# 0. Interview Relevance & Question Mapping

| Topic                  | Priority |
| ---------------------- | -------: |
| What are Generics?     |    ⭐⭐⭐⭐⭐ |
| Why Generics exist     |    ⭐⭐⭐⭐⭐ |
| Generic classes        |    ⭐⭐⭐⭐⭐ |
| Generic methods        |    ⭐⭐⭐⭐⭐ |
| Type parameters        |    ⭐⭐⭐⭐⭐ |
| Bounded types          |    ⭐⭐⭐⭐⭐ |
| Wildcards              |    ⭐⭐⭐⭐⭐ |
| `extends` vs `super`   |    ⭐⭐⭐⭐⭐ |
| PECS                   |    ⭐⭐⭐⭐⭐ |
| Invariance             |    ⭐⭐⭐⭐⭐ |
| Type Erasure           |    ⭐⭐⭐⭐⭐ |
| Raw types              |     ⭐⭐⭐⭐ |
| Generic arrays         |     ⭐⭐⭐⭐ |
| Generic exceptions     |     ⭐⭐⭐⭐ |
| Bridge methods         |     ⭐⭐⭐⭐ |
| Heap pollution         |     ⭐⭐⭐⭐ |
| Reifiable types        |     ⭐⭐⭐⭐ |
| Collections + Generics |    ⭐⭐⭐⭐⭐ |

---

# 1. Precise Definition

### Interview-ready answer

> **Generics are Java's mechanism for parameterizing classes, interfaces, and methods with types, allowing compile-time type checking and reusable APIs while largely implementing generic type information through type erasure at runtime.**

Example:

```java
List<String> names = new ArrayList<>();
```

Here:

```text
List
 ↓
Type parameter
 ↓
String
```

---

# 2. Why Do Generics Exist?

Without generics:

```java
List list = new ArrayList();

list.add("Java");
list.add(100);

String s = (String) list.get(1);
```

This can fail at runtime:

```text
ClassCastException
```

With generics:

```java
List<String> list =
        new ArrayList<>();

list.add("Java");
```

This is rejected:

```java
list.add(100); // compile-time error
```

### Main benefit

```text
Generic Type
     ↓
Compile-time checking
     ↓
Fewer runtime type errors
     ↓
Less casting
     ↓
Reusable APIs
```

---

# 3. Generic Class

Example:

```java
class Box<T> {

    private T value;

    public void set(T value) {
        this.value = value;
    }

    public T get() {
        return value;
    }
}
```

Usage:

```java
Box<String> box =
        new Box<>();

box.set("Java");

String value = box.get();
```

Here:

```text
T
↓
Type parameter
```

When instantiated:

```text
Box<String>
```

`T` is logically `String` for compile-time type checking.

---

# 4. Multiple Type Parameters

```java
class Pair<K, V> {

    private K key;
    private V value;

    Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }

    K getKey() {
        return key;
    }

    V getValue() {
        return value;
    }
}
```

Usage:

```java
Pair<Integer, String> pair =
        new Pair<>(1, "Java");
```

Common naming conventions:

| Symbol | Meaning |
| ------ | ------- |
| `T`    | Type    |
| `E`    | Element |
| `K`    | Key     |
| `V`    | Value   |
| `N`    | Number  |
| `R`    | Result  |

These are conventions, not language requirements.

---

# 5. Generic Method

A method can have its own type parameter.

```java
public static <T> void print(T value) {
    System.out.println(value);
}
```

Usage:

```java
print("Java");
print(100);
print(3.14);
```

Important syntax:

```text
public static <T> void
             ↑
       type parameter
```

The `<T>` belongs to the **method**, not necessarily the class.

---

# 6. Generic Method Returning a Type

```java
public static <T> T identity(T value) {
    return value;
}
```

Usage:

```java
String s =
    identity("Java");

Integer n =
    identity(100);
```

The compiler infers `T`.

---

# 7. Generic Interface

```java
interface Repository<T> {

    void save(T entity);

    T findById(int id);
}
```

Implementation:

```java
class UserRepository
        implements Repository<User> {

    public void save(User user) {
    }

    public User findById(int id) {
        return null;
    }
}
```

This is common in framework/API design.

---

# 8. Generic Invariance

⭐ **Very Important**

This does **not** compile:

```java
List<Integer> integers =
        new ArrayList<>();

List<Number> numbers = integers;
```

Why?

Because:

```text
Integer extends Number
```

but:

```text
List<Integer>
```

is **not** a subtype of:

```text
List<Number>
```

Java generics are generally **invariant**.

---

# 9. Why Is Invariance Necessary?

Imagine Java allowed:

```java
List<Integer>
      ↓
List<Number>
```

Then:

```java
List<Integer> integers =
    new ArrayList<>();

List<Number> numbers = integers;

numbers.add(3.14);
```

Now the original `List<Integer>` contains a `Double`.

That would violate type safety.

Therefore:

```text
Integer <: Number

but

List<Integer> ≠ subtype of List<Number>
```

---

# 10. Wildcards

Wildcards allow controlled flexibility.

Three major forms:

```text
?
? extends T
? super T
```

---

# 11. Unbounded Wildcard

```java
List<?> list;
```

Means:

> A list of some unknown type.

You can safely read:

```java
Object value = list.get(0);
```

But you generally cannot add arbitrary values:

```java
list.add("Java"); // compile-time error
```

because the actual element type is unknown.

---

# 12. Upper-Bounded Wildcard

```java
List<? extends Number>
```

Means:

> A list of some unknown type that is `Number` or a subtype of `Number`.

Possible:

```text
List<Integer>
List<Double>
List<Float>
```

You can read:

```java
Number n = list.get(0);
```

But cannot safely add a `Number`:

```java
list.add(10); // not allowed
```

because the actual list might be:

```text
List<Double>
```

---

# 13. Lower-Bounded Wildcard

```java
List<? super Integer>
```

Means:

> A list of some unknown type that is `Integer` or a supertype of `Integer`.

Possible:

```text
List<Integer>
List<Number>
List<Object>
```

You can safely add:

```java
list.add(10);
```

because every allowed destination type can accept an `Integer`.

Reading gives only:

```java
Object value = list.get(0);
```

because the exact type is unknown.

---

# 14. PECS

⭐ **Must-Know**

**PECS = Producer Extends, Consumer Super**

### Producer

If you're primarily **reading** values:

```java
List<? extends Number>
```

Use:

```text
extends
```

### Consumer

If you're primarily **writing** values:

```java
List<? super Integer>
```

Use:

```text
super
```

Mental model:

```text
Reading → extends

Writing → super
```

---

# 15. PECS Example

```java
static double sum(
        List<? extends Number> numbers) {

    double total = 0;

    for (Number n : numbers) {
        total += n.doubleValue();
    }

    return total;
}
```

This accepts:

```java
List<Integer>
List<Double>
List<Long>
```

because all produce `Number` values.

---

# 16. Consumer Example

```java
static void addNumbers(
        List<? super Integer> list) {

    list.add(10);
    list.add(20);
}
```

Works with:

```java
List<Integer>
List<Number>
List<Object>
```

---

# 17. `extends` Type Parameter vs Wildcard

These are different concepts:

```java
<T extends Number>
```

versus:

```java
<? extends Number>
```

### Type parameter

```java
<T extends Number>
```

introduces a named type variable.

Example:

```java
static <T extends Number>
T process(T value) {
    return value;
}
```

### Wildcard

```java
<? extends Number>
```

represents an unknown type.

---

# 18. Bounded Type Parameter

You can restrict a type parameter:

```java
class Calculator<T extends Number> {

    private T value;
}
```

Now:

```java
Calculator<Integer> c1;
Calculator<Double> c2;
```

But:

```java
Calculator<String> c3;
```

is invalid.

---

# 19. Multiple Bounds

A type parameter can have multiple bounds:

```java
<T extends Number & Comparable<T>>
```

The first bound must be a class if one is present; additional bounds can be interfaces.

Example:

```java
static <T extends Number & Comparable<T>>
T process(T value) {
    return value;
}
```

Conceptually:

```text
T
├── must be Number
└── must implement Comparable<T>
```

---

# 20. Generic Method vs Wildcard

Compare:

```java
static <T> T first(List<T> list)
```

and:

```java
static Object first(List<?> list)
```

The first preserves a relationship between input and output:

```text
List<T>
 ↓
T
```

The second only says:

```text
List of unknown type
 ↓
Object
```

This distinction matters when designing APIs.

---

# 21. Type Erasure

⭐⭐⭐⭐⭐ **Most Important Advanced Topic**

Java generics are primarily implemented through **type erasure**.

Consider:

```java
List<String>
```

At runtime, the generic parameter is generally not represented as a reified `String` parameter on the ordinary collection object.

Conceptually:

```text
Compile time
List<String>
     ↓
Type checking
     ↓
Erasure
     ↓
List
```

The compiler inserts necessary casts where appropriate.

---

# 22. Why Does Java Use Type Erasure?

A major historical reason is compatibility with existing Java code and the JVM's pre-generics type system.

Java could introduce generics while retaining compatibility with much existing bytecode and APIs.

This is known as:

> **Backward compatibility / migration compatibility**

---

# 23. Type Erasure Example

Source:

```java
List<String> names =
    new ArrayList<>();

String name =
    names.get(0);
```

Conceptually, after erasure:

```java
List names =
    new ArrayList();

String name =
    (String) names.get(0);
```

The cast is effectively inserted by the compiler.

---

# 24. Generic Type Information at Runtime

This causes important limitations.

You cannot normally do:

```java
if (obj instanceof List<String>) {
}
```

because the runtime cannot distinguish:

```text
List<String>
```

from:

```text
List<Integer>
```

in the ordinary erased representation.

You can use:

```java
if (obj instanceof List<?>) {
}
```

because `List<?>` is reifiable.

---

# 25. Reifiable vs Non-Reifiable Types

⭐ **Advanced**

A **reifiable type** is a type whose relevant type information is fully available at runtime.

Examples:

```text
String
Integer
List<?>
List
```

Common non-reifiable types:

```text
List<String>
List<Integer>
List<T>
```

This distinction explains several restrictions in Java generics.

---

# 26. Why Can't You Create Generic Arrays?

This is illegal:

```java
T[] array =
    new T[10];
```

Why?

Because arrays are **reified**, while generic type arguments are generally erased.

The JVM would not know the runtime component type needed to create:

```text
T[]
```

when `T` is not reified.

---

# 27. Why Can You Create Generic Collections?

You can:

```java
List<T> list =
    new ArrayList<>();
```

because the actual generic type parameter does not need to be represented as a runtime array component type.

This is one reason collections and arrays behave differently with generics.

---

# 28. Raw Types

Example:

```java
List list =
    new ArrayList();
```

This is a **raw type**.

It disables much of generic type checking.

Example:

```java
List list = new ArrayList();

list.add("Java");
list.add(100);
```

This compiles with warnings.

Prefer:

```java
List<String> list =
    new ArrayList<>();
```

---

# 29. Why Raw Types Are Dangerous

Raw types can create:

```text
Unchecked warnings
 ↓
Unsafe values
 ↓
Runtime ClassCastException
```

Example:

```java
List list =
    new ArrayList();

list.add("Java");
list.add(100);

String s = (String) list.get(1);
```

Runtime:

```text
ClassCastException
```

---

# 30. Heap Pollution

⭐ **Advanced**

Heap pollution occurs when a variable of a parameterized type refers to an object that is not of that parameterized type.

A common source is:

```text
Raw types
+
Unchecked operations
+
Unsafe varargs
```

Example:

```java
List<String> strings =
    new ArrayList<>();

List raw = strings;

raw.add(100);
```

Now the `List<String>` reference can observe a value that isn't a `String`.

---

# 31. Generic Varargs

Consider:

```java
static <T> void process(T... values) {
}
```

Because arrays are reified while generic component types are erased, generic varargs can generate heap-pollution warnings in certain situations.

The compiler may report:

```text
Possible heap pollution from parameterized vararg type
```

Use:

```java
@SafeVarargs
```

only when the method is genuinely safe with respect to its varargs parameter.

Do not use it simply to suppress warnings.

---

# 32. Static Members and Type Parameters

You cannot use a class's type parameter as the type of a static field.

Illegal:

```java
class Box<T> {

    static T value;
}
```

Why?

`T` belongs to each **instance/type parameterization**, while static members belong to the class itself.

Conceptually:

```text
Box<String>
Box<Integer>
```

cannot each have a different class-level static `T`.

---

# 33. Static Generic Method

However, a static method can declare its own type parameter:

```java
class Utility {

    static <T> T identity(T value) {
        return value;
    }
}
```

Here:

```text
<T>
```

belongs to the method.

---

# 34. Generic Constructors

Constructors can participate in generic classes:

```java
class Box<T> {

    private T value;

    Box(T value) {
        this.value = value;
    }
}
```

You can also have a constructor with its own type parameter in appropriate designs.

---

# 35. Generic Exception Restrictions

You cannot directly create a generic class extending `Throwable`:

```java
class MyException<T>
        extends Exception {
}
```

Java disallows generic classes that directly or indirectly extend `Throwable`.

This is because exception handling relies on runtime type information that conflicts with erased generic type parameters.

---

# 36. Bridge Methods

⭐⭐⭐⭐ **Advanced JVM Question**

Type erasure can create situations where the erased method signature no longer matches the overriding relationship expected by the JVM.

The Java compiler may generate a **synthetic bridge method** to preserve polymorphism after erasure.

Example conceptually:

```java
class Parent<T> {
    T get() { ... }
}

class Child extends Parent<String> {
    String get() { ... }
}
```

After erasure, the JVM-level signatures need compatibility.

The compiler can generate a bridge method similar conceptually to:

```java
Object get() {
    return get(); // String version
}
```

The exact generated bytecode should be viewed with tools such as `javap`.

---

# 37. Generics + Inheritance

Suppose:

```java
class Animal {}

class Dog extends Animal {}
```

Then:

```text
Dog extends Animal
```

but:

```text
List<Dog>
```

does not extend:

```text
List<Animal>
```

Again:

```text
Generic types are invariant
```

Use:

```java
List<? extends Animal>
```

when you need a list of some subtype of `Animal`.

---

# 38. Wildcard Capture

⭐ **Advanced**

Consider:

```java
void process(List<?> list) {
    list.set(0, list.get(0));
}
```

This can be understood using **capture conversion**.

The wildcard is treated conceptually as an unknown specific type:

```text
List<?>
   ↓
List<CAP>
```

where `CAP` represents an unknown captured type.

This allows the compiler to preserve type safety even when the exact type is unknown.

---

# 39. Helper Method for Wildcard Capture

A common pattern:

```java
static void reverse(List<?> list) {
    reverseHelper(list);
}

private static <T> void reverseHelper(
        List<T> list) {

    // T is now a named type
}
```

The helper captures the unknown wildcard type as `T`.

This is an advanced generic programming technique.

---

# 40. Generic Bounds and PECS Together

Suppose you want to copy data:

```java
static <T> void copy(
        List<? super T> destination,
        List<? extends T> source) {

    for (T item : source) {
        destination.add(item);
    }
}
```

This expresses:

```text
source
→ produces T

destination
→ consumes T
```

Therefore:

```text
Producer → extends
Consumer → super
```

This is one of the best examples of PECS.

---

# 41. Collections + Generics

This is where generics are most commonly used.

```java
List<String>
Set<Integer>
Map<String, User>
Queue<Task>
```

Without generics:

```java
List
Set
Map
Queue
```

you lose compile-time type safety.

---

# 42. Generic API Design

Bad:

```java
Object findUser(int id);
```

Caller:

```java
User user =
    (User) repository.findUser(1);
```

Better:

```java
User findUser(int id);
```

For reusable APIs:

```java
interface Repository<T> {

    T findById(int id);

    void save(T entity);
}
```

Now:

```java
Repository<User>
```

provides type-safe operations.

---

# 43. Generics in Spring

Generics are heavily used in Spring APIs.

Example:

```java
ResponseEntity<User>
```

or:

```java
List<User>
```

and:

```java
JpaRepository<User, Long>
```

Conceptually:

```text
JpaRepository
      ↓
Entity Type
      ↓
User

ID Type
      ↓
Long
```

Generics allow Spring APIs to remain reusable while preserving compile-time type information.

---

# 44. Generic DTO Example

```java
public class ApiResponse<T> {

    private T data;
    private String message;

    public ApiResponse(
            T data,
            String message) {

        this.data = data;
        this.message = message;
    }

    public T getData() {
        return data;
    }
}
```

Usage:

```java
ApiResponse<User> response;

ApiResponse<List<User>> response2;
```

This creates a reusable response abstraction.

---

# 45. `<?>` vs `<T>`

Compare:

```java
void print(List<?> list)
```

and:

```java
<T> void print(List<T> list)
```

Both can accept lists of arbitrary types.

But `<T>` gives you a **named type variable** that can be related to other parameters/return values.

Example:

```java
<T> T first(List<T> list)
```

preserves the type relationship.

With:

```java
Object first(List<?> list)
```

the return type is only `Object`.

---

# 46. Generic Method Type Inference

Java can infer generic type arguments:

```java
var list = List.of("A", "B", "C");
```

The compiler infers:

```text
List<String>
```

For generic methods:

```java
static <T> T identity(T value)
```

calling:

```java
String s = identity("Java");
```

allows the compiler to infer:

```text
T = String
```

---

# 47. Diamond Operator

Instead of:

```java
List<String> list =
    new ArrayList<String>();
```

Java allows:

```java
List<String> list =
    new ArrayList<>();
```

The compiler infers the constructor's type arguments from the target context.

---

# 48. Generic Arrays vs Collections

### Arrays

Covariant:

```java
String[] strings =
    new String[10];

Object[] objects = strings;
```

But:

```java
objects[0] = 10;
```

causes:

```text
ArrayStoreException
```

### Generics

Invariant:

```java
List<String>
```

cannot become:

```java
List<Object>
```

This is a major difference between Java arrays and generic collections.

---

# 49. Arrays + Generics Trap

This is legal:

```java
List<String>[] array;
```

as a declaration.

But direct creation is not:

```java
new List<String>[10]; // illegal
```

because generic array creation is not allowed.

You may encounter unchecked workarounds using raw or wildcard arrays, but these should be used cautiously.

---

# 50. Common Interview Traps

### ❌ `List<Integer>` is a subtype of `List<Number>`

False.

Generics are invariant.

---

### ❌ `? extends Number` means you can add any Number.

False.

You can safely **read** as `Number`, but cannot safely add arbitrary `Number` values.

---

### ❌ `? super Integer` means you can only read Integer.

False.

You can safely **add Integer**, but reading produces only `Object` statically.

---

### ❌ Generics exist at runtime exactly as written.

False.

Java primarily implements them through type erasure.

---

### ❌ `T` and `?` are identical.

False.

`T` is a named type variable; `?` represents an unknown type.

---

### ❌ Generics work exactly like C++ templates.

False.

Java generics and C++ templates have fundamentally different implementation and compilation models.

---

# 51. Java Generics vs C++ Templates

| Java Generics                              | C++ Templates                        |
| ------------------------------------------ | ------------------------------------ |
| Primarily type erasure                     | Compile-time template instantiation  |
| Generic type info mostly erased            | Specialized/generated code possible  |
| Limited runtime generic information        | Strong compile-time metaprogramming  |
| Type checking during compilation           | Extensive compile-time substitution  |
| Cannot directly create `new T()`           | Template mechanisms differ           |
| Type erasure creates specific restrictions | Different constraints and trade-offs |

This is useful if an interviewer asks about language-level differences.

---

# 52. Common Candidate Mistakes

### Weak

> Generics allow different data types.

### Better

> Generics parameterize types so APIs can operate over different compile-time types while preserving type safety and reducing casts.

---

### Weak

> `extends` means inheritance.

### Better

> In a wildcard such as `? extends Number`, `extends` establishes an upper bound on the unknown type. In `<T extends Number>`, it establishes a bound on a named type parameter.

---

### Weak

> `super` means parent class.

### Better

> In `? super T`, `super` establishes a lower bound: the unknown type is `T` or one of its supertypes.

---

# 53. 30-Second Revision

```text
JAVA GENERICS
│
├── Purpose
│   ├── Type Safety
│   ├── Reusability
│   └── Less Casting
│
├── Type Parameters
│   ├── <T>
│   ├── <K,V>
│   └── <E>
│
├── Bounds
│   └── <T extends Number>
│
├── Wildcards
│   ├── ?
│   ├── ? extends T
│   └── ? super T
│
├── PECS
│   ├── Producer → extends
│   └── Consumer → super
│
├── Core Property
│   └── Invariance
│
└── Runtime
    └── Type Erasure
        ├── Raw Types
        ├── Heap Pollution
        ├── Reifiable Types
        └── Bridge Methods
```

### Golden mental model

```text
Generic API
    ↓
Compile-Time Type Parameter
    ↓
Type Checking
    ↓
Safe API Usage
    ↓
Type Erasure
    ↓
Runtime JVM Compatibility
```

---

# 54. Master Interview Test

Answer without looking back:

1. What are Java Generics?
2. Why were Generics introduced?
3. What is a generic class?
4. What is a generic method?
5. What is the difference between `<T>` and `<?>`?
6. **Why is `List<Integer>` not a subtype of `List<Number>` even though `Integer` extends `Number`?**
7. **Explain `? extends T` vs `? super T` using PECS and a real code example.**
8. **Explain type erasure. Why can't Java create `new T[]`, use `instanceof List<String>`, or create a generic exception class?**
9. **Explain heap pollution, raw types, reifiable types, wildcard capture, and bridge methods. How are these related to type erasure?**
10. **Design a generic repository/API abstraction for a Spring Boot application that supports `User`, `Product`, and `Order`. Use bounded type parameters and wildcards where appropriate, and explain every generic type decision.**
