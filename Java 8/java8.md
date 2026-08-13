# Java 8

**Difficulty:** ⭐ Must-Know → Advanced
**Interview Relevance:** ⭐⭐⭐⭐⭐
**Category:** Java Core / Functional Programming / Collections / Streams / Concurrency

---

## 0. Interview Relevance & Question Mapping

Java 8 is one of the **highest-priority Java interview topics** because it introduced functional programming features that fundamentally changed how Java code is written.

### ⭐ Priority

| Topic                             | Priority |
| --------------------------------- | -------: |
| Lambda Expressions                |    ⭐⭐⭐⭐⭐ |
| Functional Interfaces             |    ⭐⭐⭐⭐⭐ |
| Stream API                        |    ⭐⭐⭐⭐⭐ |
| `map()` / `filter()` / `reduce()` |    ⭐⭐⭐⭐⭐ |
| `collect()` / Collectors          |    ⭐⭐⭐⭐⭐ |
| Method References                 |     ⭐⭐⭐⭐ |
| Optional                          |    ⭐⭐⭐⭐⭐ |
| Default Methods                   |     ⭐⭐⭐⭐ |
| Static Interface Methods          |      ⭐⭐⭐ |
| Date & Time API                   |     ⭐⭐⭐⭐ |
| CompletableFuture                 |    ⭐⭐⭐⭐⭐ |
| Parallel Streams                  |     ⭐⭐⭐⭐ |
| `forEach()`                       |      ⭐⭐⭐ |
| Interface evolution               |      ⭐⭐⭐ |

### ⭐ Frequently Asked

* What are the major features introduced in Java 8?
* What is a lambda expression?
* What is a functional interface?
* `Predicate` vs `Function` vs `Consumer` vs `Supplier`?
* What is the Stream API?
* Stream vs Collection?
* `map()` vs `flatMap()`?
* `filter()` vs `map()`?
* `reduce()` vs `collect()`?
* Intermediate vs terminal operations?
* What is lazy evaluation in Streams?
* What is `Optional`?
* What are default methods?
* Can an interface have static methods?
* What is a method reference?
* Sequential vs parallel stream?
* How does `CompletableFuture` work?
* What is effectively final?
* Can a lambda modify a local variable?

---

# 1. Precise Definition

**Java 8** is a major Java release that introduced functional-programming capabilities and significant API improvements while maintaining Java's object-oriented foundation.

### Interview-ready answer

> **Java 8 introduced lambda expressions, functional interfaces, the Stream API, method references, Optional, default and static interface methods, the modern Date-Time API, and CompletableFuture, enabling more declarative, functional, and expressive Java programming.**

---

# 2. Why Was Java 8 Introduced?

Before Java 8, collection processing often required verbose imperative code.

### Before Java 8

```java
List<String> names = new ArrayList<>();

for (String name : users) {
    if (name.startsWith("A")) {
        names.add(name);
    }
}
```

Java 8:

```java
List<String> names = users.stream()
        .filter(name -> name.startsWith("A"))
        .toList();
```

The second approach expresses **what** we want rather than explicitly describing every iteration step.

---

# 3. Major Java 8 Features

```text
Java 8
│
├── Lambda Expressions
├── Functional Interfaces
├── Stream API
├── Method References
├── Optional
├── Default Interface Methods
├── Static Interface Methods
├── New Date-Time API
├── CompletableFuture
├── Improved Collections API
└── Functional Programming Support
```

---

# 4. Lambda Expression

⭐ **Must-Know**

A lambda is a concise representation of a function-like behavior that can be passed as a value.

Syntax:

```java
(parameters) -> expression
```

Example:

```java
(a, b) -> a + b
```

Instead of:

```java
new Comparator<Integer>() {
    @Override
    public int compare(Integer a, Integer b) {
        return Integer.compare(a, b);
    }
}
```

Java 8:

```java
(a, b) -> Integer.compare(a, b)
```

---

# 5. Lambda Structure

```text
(a, b) -> a + b
 │   │       │
 │   │       └── Body
 │   └────────── Parameters
 └────────────── Lambda
```

Examples:

```java
x -> x * 2
```

```java
(a, b) -> a + b
```

```java
() -> System.out.println("Hello")
```

---

# 6. Why Does Lambda Exist?

The main motivation is to pass **behavior** as an argument.

Before:

```java
sort(list, comparatorObject);
```

With lambda:

```java
list.sort((a, b) -> a.getAge() - b.getAge());
```

Mental model:

```text
Traditional Java
Object → Behavior

Java 8
Lambda → Behavior
```

---

# 7. Functional Interface

⭐ **Extremely Frequently Asked**

A functional interface has **exactly one abstract method**.

Example:

```java
@FunctionalInterface
interface Calculator {
    int calculate(int a, int b);
}
```

Lambda:

```java
Calculator c =
        (a, b) -> a + b;
```

Use:

```java
System.out.println(c.calculate(10, 20));
```

Output:

```text
30
```

---

# 8. `@FunctionalInterface`

This annotation tells the compiler that the interface is intended to have one abstract method.

```java
@FunctionalInterface
interface Printer {
    void print(String value);
}
```

If you add another abstract method:

```java
void log();
```

the compiler rejects the interface.

### Important

A functional interface can still contain:

* One abstract method
* Multiple default methods
* Multiple static methods
* Methods inherited from `Object` don't count as additional abstract methods for this purpose

---

# 9. Built-in Functional Interfaces

Java provides common functional interfaces in:

```java
java.util.function
```

The four most important:

```text
Predicate
Function
Consumer
Supplier
```

---

# 10. Predicate

`Predicate<T>` represents a function that:

```text
T → boolean
```

Example:

```java
Predicate<Integer> isEven =
        n -> n % 2 == 0;
```

Usage:

```java
System.out.println(isEven.test(10));
```

Output:

```text
true
```

Typical use:

```java
filter()
```

---

# 11. Function

`Function<T, R>` represents:

```text
T → R
```

Example:

```java
Function<String, Integer> length =
        s -> s.length();
```

Usage:

```java
length.apply("Java");
```

Result:

```text
4
```

Typical use:

```text
Transformation
```

---

# 12. Consumer

`Consumer<T>`:

```text
T → nothing
```

Example:

```java
Consumer<String> print =
        s -> System.out.println(s);
```

Usage:

```java
print.accept("Java");
```

Typical use:

```text
forEach()
```

---

# 13. Supplier

`Supplier<T>`:

```text
() → T
```

Example:

```java
Supplier<Double> random =
        () -> Math.random();
```

Usage:

```java
random.get();
```

Typical use:

```text
Lazy creation
Factories
Default values
```

---

# 14. Functional Interface Cheat Sheet

| Interface       | Input | Output    | Main Method |
| --------------- | ----- | --------- | ----------- |
| `Predicate<T>`  | `T`   | `boolean` | `test()`    |
| `Function<T,R>` | `T`   | `R`       | `apply()`   |
| `Consumer<T>`   | `T`   | `void`    | `accept()`  |
| `Supplier<T>`   | none  | `T`       | `get()`     |

Mental model:

```text
Predicate → "Should I?"
Function  → "Transform this"
Consumer  → "Do something with this"
Supplier  → "Give me something"
```

---

# 15. Stream API

⭐ **Most Important Java 8 Topic**

A Stream represents a pipeline for processing elements from a source.

Example:

```java
List<Integer> result =
        numbers.stream()
               .filter(n -> n % 2 == 0)
               .map(n -> n * 2)
               .toList();
```

Conceptually:

```text
Collection
    ↓
  Stream
    ↓
 filter
    ↓
  map
    ↓
 collect
    ↓
 Result
```

---

# 16. Stream vs Collection

⭐ **Frequently Asked**

| Collection             | Stream                         |
| ---------------------- | ------------------------------ |
| Stores data            | Processes data                 |
| Data structure         | Processing abstraction         |
| Can iterate repeatedly | Stream generally consumed once |
| Eager data structure   | Supports lazy pipelines        |
| CRUD/data management   | Transform/filter/reduce        |

Important:

> **A Stream does not normally store the data itself. It provides a pipeline for processing elements from a source.**

---

# 17. Stream Pipeline

A stream pipeline generally consists of:

```text
Source
  ↓
Intermediate Operations
  ↓
Terminal Operation
```

Example:

```java
numbers.stream()
       .filter(n -> n > 10)
       .map(n -> n * 2)
       .collect(Collectors.toList());
```

Here:

```text
Source
→ numbers

Intermediate
→ filter
→ map

Terminal
→ collect
```

---

# 18. Intermediate Operations

Intermediate operations return another Stream.

Examples:

```java
filter()
map()
flatMap()
sorted()
distinct()
limit()
skip()
peek()
```

They are generally **lazy**.

Example:

```java
numbers.stream()
       .filter(n -> n > 10);
```

Nothing necessarily gets processed yet because there is no terminal operation.

---

# 19. Terminal Operations

Terminal operations produce a result or side effect and consume the stream.

Examples:

```java
collect()
forEach()
reduce()
count()
min()
max()
findFirst()
findAny()
anyMatch()
allMatch()
noneMatch()
```

Example:

```java
long count = numbers.stream()
        .filter(n -> n > 10)
        .count();
```

---

# 20. Lazy Evaluation

⭐ **Frequently Asked**

Consider:

```java
numbers.stream()
       .filter(n -> {
           System.out.println(n);
           return n > 10;
       });
```

You may see no output.

Why?

Because `filter()` is intermediate.

Add:

```java
.count();
```

Now the pipeline executes.

```text
Stream definition
      ↓
No terminal operation
      ↓
No pipeline execution
```

---

# 21. `filter()`

Used to retain elements satisfying a condition.

```java
List<Integer> even =
        numbers.stream()
               .filter(n -> n % 2 == 0)
               .toList();
```

Flow:

```text
1 → false
2 → true
3 → false
4 → true
```

Result:

```text
[2, 4]
```

---

# 22. `map()`

Transforms every element.

```java
List<Integer> doubled =
        numbers.stream()
               .map(n -> n * 2)
               .toList();
```

```text
1 → 2
2 → 4
3 → 6
```

Mental model:

```text
map
↓
one element → one transformed element
```

---

# 23. `filter()` vs `map()`

| `filter()`                        | `map()`                      |
| --------------------------------- | ---------------------------- |
| Selects elements                  | Transforms elements          |
| Uses predicate                    | Uses function                |
| Output may contain fewer elements | Usually one output per input |
| `T → boolean`                     | `T → R`                      |

Example:

```java
.filter(x -> x > 10)
```

vs:

```java
.map(x -> x * 2)
```

---

# 24. `flatMap()`

⭐ **Very Frequently Asked**

`flatMap()` transforms and flattens nested structures.

Suppose:

```text
[
 [1, 2],
 [3, 4],
 [5, 6]
]
```

`map()`:

```text
Stream<List<Integer>>
```

`flatMap()`:

```text
Stream<Integer>
```

Example:

```java
List<Integer> result =
        lists.stream()
             .flatMap(List::stream)
             .toList();
```

Result:

```text
[1, 2, 3, 4, 5, 6]
```

Mental model:

```text
map
→ one-to-one transformation

flatMap
→ transform + flatten
```

---

# 25. `reduce()`

Used to combine multiple elements into one result.

Example:

```java
int sum =
        numbers.stream()
               .reduce(0, Integer::sum);
```

Flow:

```text
1
 ↓
1 + 2
 ↓
3 + 3
 ↓
6 + 4
 ↓
10
```

General model:

```text
Many values
     ↓
combine
     ↓
One value
```

---

# 26. `collect()`

`collect()` is commonly used to accumulate stream results.

```java
List<String> names =
        users.stream()
             .map(User::getName)
             .collect(Collectors.toList());
```

Common collectors:

```java
Collectors.toList()
Collectors.toSet()
Collectors.groupingBy()
Collectors.partitioningBy()
Collectors.joining()
Collectors.counting()
Collectors.summingInt()
```

---

# 27. `groupingBy()`

⭐ **Frequently Asked**

Example:

```java
Map<String, List<Employee>> employees =
        list.stream()
            .collect(
                Collectors.groupingBy(
                    Employee::getDepartment
                )
            );
```

Conceptually:

```text
Employees
   ↓
department
   ↓
Group
   ├── IT
   ├── HR
   └── Finance
```

---

# 28. `partitioningBy()`

Splits elements into two groups based on a predicate.

```java
Map<Boolean, List<Integer>> result =
        numbers.stream()
               .collect(
                   Collectors.partitioningBy(
                       n -> n % 2 == 0
                   )
               );
```

Result conceptually:

```text
true  → even numbers
false → odd numbers
```

---

# 29. Method References

Method references provide shorter lambda syntax when an existing method already matches the required functional interface.

Instead of:

```java
names.forEach(name ->
    System.out.println(name)
);
```

write:

```java
names.forEach(System.out::println);
```

Types:

```text
ClassName::staticMethod
object::instanceMethod
ClassName::instanceMethod
ClassName::new
```

---

# 30. Constructor Reference

```java
Supplier<User> supplier =
        User::new;
```

Equivalent conceptually to:

```java
Supplier<User> supplier =
        () -> new User();
```

Useful with functional APIs and factories.

---

# 31. `Optional`

⭐ **Very Frequently Asked**

`Optional<T>` represents the presence or absence of a value.

Instead of:

```java
User user = findUser(id);

if (user != null) {
    ...
}
```

an API may return:

```java
Optional<User>
```

Example:

```java
Optional<User> user =
        repository.findById(id);
```

---

# 32. Optional Operations

Common methods:

```java
isPresent()
isEmpty()
orElse()
orElseGet()
orElseThrow()
map()
flatMap()
filter()
ifPresent()
```

Example:

```java
String name = user
        .map(User::getName)
        .orElse("Unknown");
```

---

# 33. `orElse()` vs `orElseGet()`

⭐ **Interview Trap**

```java
optional.orElse(createDefault());
```

The argument can be evaluated even when the Optional contains a value.

With:

```java
optional.orElseGet(() -> createDefault());
```

the supplier is invoked only when needed.

Mental model:

```text
orElse
→ eager argument evaluation

orElseGet
→ lazy supplier evaluation
```

---

# 34. Optional Misuse

Don't automatically use:

```java
Optional<String> name;
```

for every field.

`Optional` is primarily intended as a clear API return-value abstraction for possible absence, rather than a universal replacement for `null`.

Avoid:

```java
optional.get();
```

without establishing presence.

Prefer:

```java
optional.orElse(...)
optional.orElseThrow(...)
optional.map(...)
```

where appropriate.

---

# 35. Default Methods

⭐ **Frequently Asked**

Java 8 allows interfaces to contain default method implementations.

```java
interface Vehicle {

    default void start() {
        System.out.println("Starting");
    }
}
```

Why?

Primarily to allow interfaces to evolve without forcing every existing implementation to immediately implement a newly added method.

---

# 36. Static Interface Methods

Java 8 also allows static methods in interfaces.

```java
interface MathUtil {

    static int add(int a, int b) {
        return a + b;
    }
}
```

Call:

```java
MathUtil.add(10, 20);
```

Static interface methods are not inherited as instance methods.

---

# 37. Default Method Conflict

Suppose:

```java
interface A {
    default void show() {}
}

interface B {
    default void show() {}
}
```

Then:

```java
class C implements A, B {
}
```

causes a conflict.

The class must resolve it:

```java
class C implements A, B {

    @Override
    public void show() {
        A.super.show();
    }
}
```

This is an important Java 8 interface interview question.

---

# 38. Effectively Final

⭐ **Frequently Asked**

A local variable captured by a lambda must be final or **effectively final**.

This works:

```java
int x = 10;

Runnable r =
        () -> System.out.println(x);
```

This does not:

```java
int x = 10;

x = 20;

Runnable r =
        () -> System.out.println(x);
```

Why?

The local variable is no longer effectively final.

Important distinction:

> Lambdas capture local variables by value, and the captured local variable cannot subsequently be reassigned.

---

# 39. Lambda and Variable Capture

Example:

```java
int x = 10;

Runnable r = () -> {
    System.out.println(x);
};
```

Conceptually:

```text
Local variable
      ↓
Captured by lambda
      ↓
Lambda object/behavior
```

This is different from directly sharing mutable local variables between closures as in some other languages.

---

# 40. New Date-Time API

Java 8 introduced:

```java
java.time
```

Important classes:

```text
LocalDate
LocalTime
LocalDateTime
Instant
ZonedDateTime
Duration
Period
DateTimeFormatter
```

Example:

```java
LocalDate today =
        LocalDate.now();
```

Format:

```java
DateTimeFormatter formatter =
        DateTimeFormatter.ofPattern("dd-MM-yyyy");
```

The API is generally immutable and thread-safe, unlike many older date/time APIs.

---

# 41. `CompletableFuture`

⭐ **Advanced / Backend**

Java 8 introduced `CompletableFuture` for asynchronous and composable computation.

Example:

```java
CompletableFuture
    .supplyAsync(() -> fetchUser())
    .thenApply(User::getName)
    .thenAccept(System.out::println);
```

Flow:

```text
Async Task
   ↓
thenApply
   ↓
Transform
   ↓
thenAccept
   ↓
Consume
```

---

# 42. `thenApply()` vs `thenCompose()`

⭐ **Advanced Interview Question**

`thenApply()` transforms a result:

```java
future.thenApply(user -> user.getName());
```

If the function itself returns a `CompletableFuture`, `thenCompose()` flattens the nested future.

Conceptually:

```text
thenApply

Future<A>
   ↓
Function<A, B>
   ↓
Future<B>
```

Whereas:

```text
thenCompose

Future<A>
   ↓
Function<A, Future<B>>
   ↓
Future<B>
```

This is analogous to the `map` vs `flatMap` distinction.

---

# 43. `CompletableFuture` Combination

Example:

```java
CompletableFuture<User> user =
        getUser();

CompletableFuture<Order> orders =
        getOrders();
```

Combine:

```java
user.thenCombine(
    orders,
    (u, o) -> new UserOrders(u, o)
);
```

Conceptually:

```text
User Future ──────┐
                  ├──→ Combined Result
Order Future ─────┘
```

---

# 44. Parallel Streams

Java 8 supports:

```java
numbers.parallelStream()
```

Conceptually:

```text
Input
 ↓
Split
 ├── Worker 1
 ├── Worker 2
 ├── Worker 3
 └── Worker 4
 ↓
Combine
```

But:

> **Parallel streams are not automatically faster.**

They introduce overhead and use the common ForkJoinPool by default.

They are best considered when:

* Work is sufficiently large
* Operations are CPU-oriented
* Operations are independent
* Shared mutable state is avoided
* Benchmarking supports the choice

---

# 45. Sequential vs Parallel Stream

| Sequential              | Parallel                                |
| ----------------------- | --------------------------------------- |
| One processing pipeline | Multiple workers                        |
| Lower overhead          | Parallelization overhead                |
| Easier reasoning        | More concurrency concerns               |
| Good for many workloads | Useful for suitable CPU-bound workloads |

Do not use parallel streams merely because multiple CPU cores exist.

---

# 46. Java 8 and OOP

Java 8 did **not** make Java a purely functional language.

Java remains primarily object-oriented but gained functional programming capabilities.

```text
Java
├── OOP
│   ├── Classes
│   ├── Objects
│   ├── Inheritance
│   └── Polymorphism
│
└── Functional Features
    ├── Lambdas
    ├── Functional Interfaces
    ├── Streams
    └── Method References
```

---

# 47. Production Connection

Java 8 features are heavily relevant in backend code.

### Spring Boot

Streams:

```java
users.stream()
     .filter(User::isActive)
     .map(UserDto::from)
     .toList();
```

### Data processing

```text
Database
 ↓
Collection
 ↓
Stream
 ↓
Filter
 ↓
Transform
 ↓
Collect
```

### Async services

```text
Service A
   ↓
CompletableFuture
   ↓
Service B + Service C
   ↓
Combine
```

### Null-safe API design

```java
Optional<User>
```

---

# 48. Bad vs Good Design

### ❌ Bad Stream Usage

```java
users.stream()
     .filter(...)
     .map(...)
     .forEach(user -> database.save(user));
```

This may hide expensive side effects inside a stream pipeline.

Streams are strongest for transformations and aggregation, not for arbitrary side-effect-heavy workflows.

### Better

Separate computation from persistence when appropriate:

```text
Fetch
 ↓
Transform
 ↓
Validate
 ↓
Persist
```

---

# 49. Common Interview Traps

### Is a Stream a data structure?

❌ No.

It is a processing abstraction over a source.

### Can a Stream be reused?

Generally ❌ no.

Once a terminal operation consumes it, attempting to reuse the same stream results in `IllegalStateException`.

### Does `map()` modify the original collection?

❌ No.

It creates a transformed stream.

### Are streams always lazy?

Intermediate stream operations are generally lazy; terminal operations trigger evaluation.

### Is `parallelStream()` always faster?

❌ No.

### Is `Optional` a replacement for all `null`s?

❌ No.

### Can a functional interface have default methods?

✅ Yes.

### Can an interface have static methods?

✅ Yes.

### Can a lambda implement a normal interface with two abstract methods?

❌ No.

---

# 50. Interviewer Follow-Up Chain

```text
What are Java 8 features?
        ↓
Why were lambdas introduced?
        ↓
What is a functional interface?
        ↓
Predicate vs Function vs Consumer vs Supplier?
        ↓
What is Stream API?
        ↓
Stream vs Collection?
        ↓
Intermediate vs terminal operation?
        ↓
Why are streams lazy?
        ↓
map vs filter?
        ↓
map vs flatMap?
        ↓
reduce vs collect?
        ↓
What happens internally during stream execution?
        ↓
Sequential vs parallel stream?
        ↓
What is Optional?
        ↓
orElse vs orElseGet?
        ↓
What are default methods?
        ↓
Why were default methods introduced?
        ↓
What is CompletableFuture?
        ↓
thenApply vs thenCompose?
        ↓
How would you use Java 8 features in Spring Boot?
```

---

# 51. Common Candidate Mistakes

### ❌ Weak

> Lambda is an anonymous function.

Better:

> A lambda expression provides an implementation of the abstract method of a functional interface and allows behavior to be represented more concisely.

---

### ❌ Weak

> Stream stores the data.

Wrong.

> A Stream represents a pipeline for processing elements from a source; the source itself typically remains the collection or other data provider.

---

### ❌ Weak

> `flatMap()` is used to convert lists into one list.

Too narrow.

Better:

> `flatMap()` maps each input element to a stream and then flattens the resulting streams into a single stream.

---

# 52. 30-Second Revision

```text
JAVA 8
│
├── Lambda
│      ↓
│   Behavior as value
│
├── Functional Interface
│      ↓
│   One abstract method
│
├── Stream API
│      ↓
│   filter → map → reduce/collect
│
├── Method Reference
│      ↓
│   ::
│
├── Optional
│      ↓
│   Represent possible absence
│
├── Default Methods
│      ↓
│   Interface evolution
│
├── Date-Time API
│      ↓
│   java.time
│
└── CompletableFuture
       ↓
    Async composition
```

### Core functional interfaces

```text
Predicate  → T → boolean
Function   → T → R
Consumer   → T → void
Supplier   → () → T
```

### Core Stream concepts

```text
Source
 ↓
Intermediate operations
 ↓
Terminal operation
```

### Most important differences

```text
filter → select
map → transform
flatMap → transform + flatten
reduce → many → one
collect → accumulate
```

---

# 53. Master Interview Test

Answer without looking back:

1. What are the major features introduced in Java 8?
2. What is a lambda expression and why was it introduced?
3. What is a functional interface?
4. Explain `Predicate`, `Function`, `Consumer`, and `Supplier`.
5. What is the difference between a Collection and a Stream?
6. Explain intermediate and terminal Stream operations.
7. **Why are Stream operations lazy, and when does the actual pipeline execution begin?**
8. **Explain `map()`, `flatMap()`, `reduce()`, and `collect()` with examples and their appropriate use cases.**
9. **How does `CompletableFuture` enable asynchronous composition, and what is the difference between `thenApply()` and `thenCompose()`?**
10. **In a production Spring Boot application, design a Java 8-based data-processing pipeline using Streams, functional interfaces, Optional, and CompletableFuture. Explain where each feature is appropriate and where using it would make the code worse.**
