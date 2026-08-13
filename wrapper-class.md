# Wrapper Classes in Java

**Difficulty:** ⭐ Must-Know
**Interview Relevance:** ⭐⭐⭐⭐⭐
**Category:** Java Core / OOP / Collections / JVM

---

## 0. Interview Relevance

Wrapper classes are important because they connect:

* Primitive types
* Objects
* Autoboxing / Unboxing
* Collections
* Generics
* `Integer` caching
* `equals()` / `==`
* `null`
* Memory
* Immutability
* JVM behavior

### ⭐ Frequently Asked

* Why do wrapper classes exist?
* Primitive vs wrapper class?
* What is autoboxing?
* What is unboxing?
* Why can't `ArrayList<int>` be used?
* Why does `Integer a = 127; Integer b = 127;` make `a == b` true?
* Why can `Integer a = 128; Integer b = 128;` make `a == b` false?
* What happens when unboxing `null`?
* Are wrapper objects immutable?

---

# 1. Precise Definition

A **wrapper class** is a Java class that represents a primitive value as an object.

Java provides wrapper classes for the primitive types:

| Primitive | Wrapper     |
| --------- | ----------- |
| `byte`    | `Byte`      |
| `short`   | `Short`     |
| `int`     | `Integer`   |
| `long`    | `Long`      |
| `float`   | `Float`     |
| `double`  | `Double`    |
| `char`    | `Character` |
| `boolean` | `Boolean`   |

There is no wrapper class for `void` in the same primitive sense, but Java provides `Void` as the corresponding reference type.

### Interview-ready answer

> **Wrapper classes provide object representations of Java primitive values, allowing primitives to participate in APIs that require objects, such as collections and generics.**

---

# 2. Why Do Wrapper Classes Exist?

Primitive:

```java
int age = 21;
```

is not an object.

But many Java APIs work with objects.

For example:

```java
List<Integer> numbers = new ArrayList<>();
```

You cannot write:

```java
List<int> numbers; // ❌
```

because Java generics operate with reference types, not primitive types.

Therefore:

```text
int
 ↓
Integer
 ↓
Object-compatible representation
```

---

# 3. Primitive vs Wrapper

| Primitive                                        | Wrapper                                       |
| ------------------------------------------------ | --------------------------------------------- |
| `int`                                            | `Integer`                                     |
| Stores a value directly                          | Object/reference type                         |
| Cannot be `null`                                 | Can be `null`                                 |
| Generally lower overhead                         | Object may have allocation/reference overhead |
| Cannot call methods                              | Has methods                                   |
| Cannot be used directly as generic type argument | Can be used in generics                       |

Example:

```java
int a = 10;

Integer b = 10;
```

Conceptually:

```text
a
↓
10


b
↓
Integer object
↓
10
```

---

# 4. Autoboxing

**Autoboxing** is automatic conversion from a primitive to its corresponding wrapper type.

```java
int x = 10;

Integer y = x;
```

Java automatically converts:

```text
int
 ↓
Integer
```

Conceptually similar to:

```java
Integer y = Integer.valueOf(x);
```

For interview purposes, remember that boxing conversions commonly use the wrapper's `valueOf(...)` mechanism.

---

# 5. Unboxing

**Unboxing** is automatic conversion from a wrapper object to its corresponding primitive.

```java
Integer x = 10;

int y = x;
```

Conceptually similar to:

```java
int y = x.intValue();
```

Flow:

```text
Autoboxing

int
 ↓
Integer


Unboxing

Integer
 ↓
int
```

---

# 6. Example With Collections

```java
List<Integer> numbers = new ArrayList<>();

numbers.add(10);
numbers.add(20);
numbers.add(30);
```

Here:

```java
numbers.add(10);
```

involves autoboxing.

Conceptually:

```text
10
↓
Integer.valueOf(10)
↓
List<Integer>
```

When retrieving:

```java
int x = numbers.get(0);
```

the returned `Integer` is automatically unboxed.

```text
Integer
 ↓
int
```

---

# 7. Why Can't We Use `List<int>`?

⭐ **Frequently Asked**

This is illegal:

```java
List<int> list; // ❌
```

Generics require reference types.

Therefore:

```java
List<Integer> list; // ✅
```

The important distinction is:

```text
Generics
   ↓
Reference Types

Primitive
   ↓
Not directly supported
```

Wrapper classes bridge this gap.

---

# 8. Wrapper Classes Are Immutable

Wrapper objects such as `Integer`, `Long`, `Double`, and `Boolean` are immutable.

Example:

```java
Integer x = 10;

x = 20;
```

This does **not** modify the original `Integer` object.

Conceptually:

```text
x ───► Integer(10)

x = 20

x ───► Integer(20)
```

The reference changes.

The original object does not.

---

# 9. `Integer` Caching

⭐ **Extremely Important Interview Question**

Consider:

```java
Integer a = 127;
Integer b = 127;

System.out.println(a == b);
```

Typically:

```text
true
```

Why?

Because boxing through `Integer.valueOf()` can reuse cached `Integer` instances for a specified range. The Java specification guarantees caching for certain constant values, notably `-128` through `127` for `int` boxing, while implementations may cache additional values.

Now:

```java
Integer a = 128;
Integer b = 128;

System.out.println(a == b);
```

can produce:

```text
false
```

because two distinct wrapper objects may be used.

### Correct comparison

Use:

```java
a.equals(b)
```

rather than:

```java
a == b
```

when comparing wrapper values.

---

# 10. Why Does `==` Behave Differently?

This:

```java
Integer a = 127;
Integer b = 127;
```

can effectively use:

```text
Integer.valueOf(127)
```

for both.

```text
a ─────┐
       ↓
 Integer(127)
       ↑
b ─────┘
```

Therefore:

```java
a == b
```

can be `true`.

For values outside the guaranteed cache range:

```text
a → Integer(128)
b → Integer(128)
```

they may be different objects.

Therefore:

```java
a == b
```

can be `false`.

### Interview rule

> **Never use `==` to compare wrapper values when you mean value equality. Use `equals()` or an appropriate primitive comparison.**

---

# 11. `Integer.valueOf()` vs `new Integer()`

Modern Java code should use:

```java
Integer x = Integer.valueOf(100);
```

or simply:

```java
Integer x = 100;
```

Avoid:

```java
new Integer(100);
```

The constructor-based approach is deprecated in modern Java.

`valueOf()` can reuse cached instances where applicable.

---

# 12. The `null` Problem

This is a major interview trap.

```java
Integer x = null;

int y = x;
```

The assignment requires unboxing:

```text
Integer
   ↓
int
```

But `x` is `null`.

Result:

```text
NullPointerException
```

Conceptually:

```java
int y = x.intValue();
```

which is equivalent to attempting to call a method on `null`.

---

# 13. Another Dangerous Example

```java
Integer a = null;
Integer b = 10;

int result = a + b;
```

What happens?

Java needs primitive arithmetic, so it unboxes:

```text
a → int ❌
b → int
```

Unboxing `a` causes:

```text
NullPointerException
```

### Interview trap

Autounboxing can introduce runtime `NullPointerException`.

---

# 14. Wrapper Methods

Wrapper classes provide useful conversion methods.

Example:

```java
String s = "123";

int x = Integer.parseInt(s);
```

Result:

```text
123
```

Important methods:

```java
Integer.parseInt("123");
Integer.valueOf("123");

Double.parseDouble("10.5");

Long.parseLong("100000");
Boolean.parseBoolean("true");
```

---

# 15. `parseInt()` vs `valueOf()`

⭐ **Frequently Asked**

```java
Integer.parseInt("123");
```

returns:

```text
int
```

while:

```java
Integer.valueOf("123");
```

returns:

```text
Integer
```

| Method                 | Return Type |
| ---------------------- | ----------- |
| `Integer.parseInt()`   | `int`       |
| `Integer.valueOf()`    | `Integer`   |
| `Double.parseDouble()` | `double`    |
| `Double.valueOf()`     | `Double`    |

---

# 16. Wrapper Classes + OOP

Wrapper classes demonstrate the difference between:

```text
Primitive Value
      ↓
Not an object

Wrapper Object
      ↓
Object/reference type
      ↓
Methods
      ↓
Generics
      ↓
Collections
```

For example:

```java
Integer x = 100;

System.out.println(x.toString());
System.out.println(x.compareTo(50));
```

A primitive `int` cannot directly provide object methods.

---

# 17. Wrapper Classes + `Object`

Wrapper classes are objects.

Therefore:

```java
Integer x = 10;
```

can participate in APIs expecting:

```java
Object
```

For example:

```java
Object obj = Integer.valueOf(10);
```

This is another reason wrappers exist.

---

# 18. Memory Consideration

Primitive:

```java
int x = 10;
```

is generally represented as a primitive value.

Wrapper:

```java
Integer x = 10;
```

involves a reference to an object or cached object.

Conceptually:

```text
Primitive

x
↓
10


Wrapper

x
↓
Integer Object
↓
10
```

Therefore, large collections of wrapper objects can have more memory overhead than primitive-oriented data structures.

This matters in:

* High-performance systems
* Large datasets
* Competitive programming
* Quant systems
* Memory-sensitive applications

---

# 19. Primitive Collections

Standard Java collections use wrappers:

```java
List<Integer>
```

For performance-sensitive applications, specialized libraries or primitive arrays may avoid boxing overhead.

For example:

```java
int[] numbers = new int[1_000_000];
```

does not create one million `Integer` objects.

This can reduce:

* Object overhead
* Allocation
* GC pressure
* Memory usage

---

# 20. Wrapper Classes and `equals()`

Consider:

```java
Integer a = 1000;
Integer b = 1000;

System.out.println(a.equals(b));
```

Output:

```text
true
```

because `Integer.equals()` compares the numeric value.

Whereas:

```java
a == b
```

compares references.

Therefore:

```text
==        → identity
equals()  → value equality
```

for wrapper objects.

---

# 21. Wrapper Classes and `hashCode()`

Wrappers override `equals()` and `hashCode()` consistently.

Therefore:

```java
Map<Integer, String> map = new HashMap<>();

map.put(10, "Java");

System.out.println(map.get(10));
```

works using the wrapper's value-based hashing/equality semantics.

This is essential for:

* `HashMap`
* `HashSet`
* `HashTable`
* Other hash-based collections

---

# 22. `Character` and `Boolean`

Not every wrapper behaves exactly like `Integer`.

Examples:

```java
Character c = 'A';
Boolean flag = true;
```

They provide APIs related to their primitive values.

For example:

```java
Character.isDigit('5');
Character.isLetter('A');

Boolean.parseBoolean("true");
```

---

# 23. Real-World Backend Example

Suppose an API receives an optional user age.

Using:

```java
int age;
```

usually represents a required numeric value with a default Java primitive state.

Using:

```java
Integer age;
```

allows:

```text
age = 21
```

or:

```text
age = null
```

This distinction is often useful when `null` means:

> "Value was not supplied."

For example, in DTOs:

```java
class UserRequest {

    private Integer age;
}
```

can distinguish:

```text
21      → provided
null    → not provided
```

This is one practical reason wrappers are common in Java backend applications.

---

# 24. Bad vs Good Design

### ❌ Dangerous

```java
Integer count = null;

if (count > 0) {
    // ...
}
```

The comparison requires unboxing and can throw `NullPointerException`.

### ✅ Better

```java
if (count != null && count > 0) {
    // ...
}
```

Or choose a domain-appropriate default when `null` should not have semantic meaning.

---

# 25. Wrapper vs Primitive

| Feature         | Primitive       | Wrapper                                |
| --------------- | --------------- | -------------------------------------- |
| Example         | `int`           | `Integer`                              |
| Object          | ❌               | ✅                                      |
| Can be `null`   | ❌               | ✅                                      |
| Generics        | ❌               | ✅                                      |
| Collections     | ❌ directly      | ✅                                      |
| Methods         | ❌               | ✅                                      |
| Autoboxing      | Source          | Target                                 |
| Unboxing        | Target          | Source                                 |
| Memory overhead | Lower generally | Higher generally                       |
| Common use      | Computation     | Collections/API/domain nullable values |

---

# 26. Internal Flow

### Autoboxing

```text
int x = 10
     ↓
boxing conversion
     ↓
Integer.valueOf(10)
     ↓
Integer object/reference
```

### Unboxing

```text
Integer x = 10
     ↓
unboxing conversion
     ↓
x.intValue()
     ↓
int
```

### Important

The actual compiler-generated bytecode can be inspected, but the source-level mental model above is sufficient for most interviews.

---

# 27. Common Interview Traps

### ❌ "Wrapper classes are mutable."

Wrong.

They are immutable.

---

### ❌ "Integer always creates a new object."

Wrong.

`Integer.valueOf()` may return cached objects.

---

### ❌ "`Integer a = 10` always creates an Integer object."

The boxing operation may reuse a cached instance.

---

### ❌ "`==` compares wrapper values."

Wrong.

For wrapper references, it compares identity.

---

### ❌ "Autoboxing can never cause runtime errors."

Wrong.

Unboxing `null` can cause `NullPointerException`.

---

### ❌ "Wrapper classes are only needed for collections."

Incomplete.

They are also useful for:

* Generics
* Nullable values
* Object APIs
* Utility methods
* Parsing/conversion
* Frameworks and DTOs

---

# 28. Interviewer Follow-Up Chain

```text
What is a wrapper class?
       ↓
Why do we need wrapper classes?
       ↓
Primitive vs wrapper?
       ↓
Why can't List<int> be used?
       ↓
What is autoboxing?
       ↓
What is unboxing?
       ↓
What happens internally?
       ↓
What is Integer caching?
       ↓
Why does 127 == 127 sometimes return true?
       ↓
Why can 128 == 128 return false?
       ↓
What happens when Integer is null?
       ↓
parseInt() vs valueOf()?
       ↓
Why are wrappers immutable?
       ↓
What is the memory impact of boxing?
       ↓
When would you choose Integer over int?
```

---

# 29. Common Candidate Mistake

### ❌ Weak answer

> Wrapper classes are classes that wrap primitive data types into objects.

Technically correct, but shallow.

### ✅ Better interview answer

> Wrapper classes provide object representations of primitive values. They allow primitives to participate in object-oriented APIs such as generics and collections, support utility operations and nullable representations, and work with Java's autoboxing and unboxing conversions.

---

# 30. 30-Second Revision

```text
Primitive
   ↓
int
   ↓ boxing
Integer
   ↓
Object-compatible
   ↓
Collections / Generics
```

### Memorize these:

```text
int      → Integer
long     → Long
double   → Double
float    → Float
short    → Short
byte     → Byte
char     → Character
boolean  → Boolean
```

And:

```text
Autoboxing
primitive → wrapper

Unboxing
wrapper → primitive

== 
reference identity

equals()
value equality

null wrapper + unboxing
→ NullPointerException
```

---

# 31. Master Interview Test

Answer these without looking back:

1. What is a wrapper class?
2. Why does Java need wrapper classes?
3. What is the difference between `int` and `Integer`?
4. What is autoboxing and unboxing?
5. Why can't we use `List<int>`?
6. What is `Integer` caching?
7. Why can `Integer a = 127; Integer b = 127; a == b` be `true`?
8. Why can `Integer a = 128; Integer b = 128; a == b` be `false`?
9. **What happens internally when `Integer x = null; int y = x;` executes?**
10. **In a high-performance backend system, when would you choose a primitive over a wrapper, and what performance/memory trade-offs are involved?**
