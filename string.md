# String in Java

**Difficulty:** ⭐ Must-Know
**Interview Relevance:** ⭐⭐⭐⭐⭐
**Category:** Java Core / OOP / JVM / Memory

---

## 0. Interview Relevance & Question Mapping

### Why String is important

`String` is one of the **most frequently tested Java topics** because it connects:

* OOP
* Immutability
* Heap memory
* String Pool
* `final`
* `equals()` vs `==`
* `hashCode()`
* Garbage Collection
* `StringBuilder`
* `StringBuffer`
* Thread safety
* Performance
* JVM internals

### ⭐ Frequently Asked

* Why is `String` immutable?
* What is the String Pool?
* Difference between `==` and `.equals()`?
* How many objects are created by `new String("Java")`?
* Why is String immutable?
* Difference between `String`, `StringBuilder`, and `StringBuffer`?
* What does `intern()` do?
* Why is `String` final?
* Why is `String` commonly used as a `HashMap` key?
* What happens internally during string concatenation?

---

# 1. Precise Definition

`String` is a **final, immutable class in `java.lang`** used to represent a sequence of characters.

```java
String name = "Divyansh";
```

### Interview-ready answer

> **In Java, `String` is an immutable object representing a sequence of characters. String literals can be stored in the JVM's String Pool, and immutability provides benefits such as safe sharing, stable hash codes, and predictable behavior.**

---

# 2. Why Does String Exist?

Applications constantly need text:

```text
Names
Passwords
URLs
JSON
File paths
SQL
HTTP headers
Messages
Logs
```

Java provides `String` as an object abstraction around textual data.

Instead of manually managing character arrays:

```java
char[] name = {'J', 'a', 'v', 'a'};
```

we can write:

```java
String name = "Java";
```

This provides:

* Convenient APIs
* Immutability
* String pooling
* `equals()`
* `hashCode()`
* Searching
* Splitting
* Replacing
* Formatting
* Conversion APIs

---

# 3. Is String a Primitive?

❌ No.

```java
String s = "Java";
```

`String` is a **reference type**.

The primitive types are:

```text
byte
short
int
long
float
double
char
boolean
```

Therefore:

```java
String s = "Java";
```

means `s` is a reference to a `String` object.

---

# 4. String Class Hierarchy

Conceptually:

```text
Object
   ↑
String
```

`String` is:

```java
public final class String
```

Therefore:

```java
class MyString extends String {
}
```

is illegal because `String` is `final`.

---

# 5. Why Is String Immutable?

⭐ **One of the most important Java interview questions**

Once a `String` object is created, its contents cannot be changed.

```java
String s = "Java";

s.concat(" Programming");

System.out.println(s);
```

Output:

```text
Java
```

Why?

Because:

```java
s.concat(" Programming");
```

creates/returns another String rather than modifying the existing object.

Correct:

```java
s = s.concat(" Programming");
```

Now:

```text
Java Programming
```

But importantly, the original `"Java"` object itself did not change.

---

# 6. Internal Mental Model of Immutability

```java
String s = "Java";
```

Conceptually:

```text
s
│
▼
┌─────────────┐
│ "Java"      │
└─────────────┘
```

After:

```java
s = s + " World";
```

conceptually:

```text
        ┌─────────────┐
s ─────►│ "Java World"│
        └─────────────┘

Old "Java"
    │
    └── remains unchanged
```

The **reference can change**.

The **String object cannot**.

This distinction is critical.

---

# 7. Why Was String Designed as Immutable?

There are several important reasons.

## 1. String Pool Safety

String literals can be shared.

```java
String a = "Java";
String b = "Java";
```

Both can refer to the same pooled String object.

```text
a ─────┐
       ↓
   "Java"
       ↑
b ─────┘
```

If Strings were mutable, changing `a` could unexpectedly affect `b`.

Immutability makes sharing safe.

---

## 2. Security

Strings are frequently used for:

* File paths
* URLs
* Class names
* Database connections
* Authentication-related values
* Configuration

Immutable values cannot be changed after validation.

For example:

```text
Validate path
     ↓
Create String
     ↓
String cannot change
     ↓
Use safely
```

---

## 3. HashMap / HashSet

Strings are frequently used as keys:

```java
Map<String, Integer> map = new HashMap<>();

map.put("Java", 10);
```

If the String's contents could change after insertion, its `hashCode()` could change.

That could make the key difficult to locate.

Immutability provides stable value-based identity.

---

## 4. Thread Safety

Immutable objects are naturally easier to share between threads.

Multiple threads can safely read the same String because nobody can modify its contents.

---

# 8. String Pool

⭐ **Extremely Frequently Asked**

Java maintains a special pool for String literals.

Consider:

```java
String a = "Java";
String b = "Java";
```

Conceptually:

```text
String Pool

┌───────────────┐
│    "Java"     │
└───────▲───────┘
        │
   ┌────┴────┐
   │         │
   a         b
```

Therefore:

```java
System.out.println(a == b);
```

typically prints:

```text
true
```

because both references can refer to the same pooled String object.

---

# 9. `==` vs `equals()`

⭐ **Must-Know**

This is one of the most common Java interview questions.

### `==`

For references, `==` compares whether the references point to the **same object**.

### `equals()`

For `String`, `equals()` compares the **contents**.

Example:

```java
String a = new String("Java");
String b = new String("Java");

System.out.println(a == b);
System.out.println(a.equals(b));
```

Output:

```text
false
true
```

Because:

```text
a ───► "Java"
           
b ───► "Java"
```

They are different objects but contain equal characters.

### Remember:

```text
==        → same reference?
equals()  → same content?
```

---

# 10. String Literal vs `new String()`

Consider:

```java
String a = "Java";
String b = new String("Java");
```

Conceptually:

```text
String Pool
┌─────────────┐
│   "Java"    │
└──────▲──────┘
       │
       a


Heap
┌─────────────┐
│   "Java"    │
└──────▲──────┘
       │
       b
```

Therefore:

```java
a == b
```

is:

```text
false
```

while:

```java
a.equals(b)
```

is:

```text
true
```

---

# 11. How Many Objects?

⭐ **Classic Trap Question**

```java
String s = new String("Java");
```

Potentially **two String objects** are involved:

```text
1. "Java" literal → String Pool
2. new String("Java") → a distinct String object
```

The exact object/lifecycle discussion can depend on whether the literal was already present in the pool, so avoid blindly saying "always exactly two."

The key interview point is:

> `new String("Java")` explicitly creates a distinct String object, while `"Java"` is a String literal that uses the String Pool.

---

# 12. `intern()`

`intern()` returns the canonical pooled representation of a String.

Example:

```java
String a = new String("Java");
String b = a.intern();

String c = "Java";
```

Then:

```java
System.out.println(b == c);
```

will be:

```text
true
```

Conceptually:

```text
a ─────► Heap String "Java"

b ─────┐
       ├──► Pool String "Java"
c ─────┘
```

---

# 13. String Concatenation

Consider:

```java
String s = "Hello";

s = s + " World";
```

Because String is immutable, concatenation does not modify the original object.

A new String result is produced.

For non-constant expressions, modern Java compilers typically use `invokedynamic`-based string concatenation machinery rather than simply translating every `+` into an explicit `StringBuilder`.

Conceptually:

```text
"Hello"
   +
" World"
   ↓
New String
   ↓
s
```

---

# 14. Compile-Time Constant Concatenation

Consider:

```java
String s = "Hello" + "World";
```

Both operands are compile-time constants.

The compiler can fold this into:

```java
String s = "HelloWorld";
```

This is different from:

```java
String a = "Hello";
String s = a + "World";
```

because `a` is a variable expression rather than two compile-time string literals.

---

# 15. String vs StringBuilder vs StringBuffer

⭐ **Frequently Asked**

| Feature                               | String     | StringBuilder            | StringBuffer                        |
| ------------------------------------- | ---------- | ------------------------ | ----------------------------------- |
| Mutable                               | ❌          | ✅                        | ✅                                   |
| Thread-safe operations                | Immutable  | ❌                        | Synchronized                        |
| Performance for repeated modification | Poorer     | Usually best             | Usually slower than StringBuilder   |
| Typical use                           | Fixed text | Single-threaded mutation | Legacy/shared synchronized mutation |
| `append()`                            | ❌          | ✅                        | ✅                                   |

### Example

Bad for heavy repeated concatenation:

```java
String result = "";

for (int i = 0; i < 10000; i++) {
    result += i;
}
```

Better:

```java
StringBuilder result = new StringBuilder();

for (int i = 0; i < 10000; i++) {
    result.append(i);
}
```

---

# 16. Why StringBuilder Is Faster

With immutable String:

```text
String
 ↓
new result
 ↓
copy/create
 ↓
new result
 ↓
copy/create
 ↓
...
```

Repeated modification can generate substantial allocation/copying overhead.

`StringBuilder` maintains mutable character data and grows its internal storage as necessary.

```text
StringBuilder
     ↓
[characters...]
     ↓
append()
     ↓
same builder
```

Therefore it is usually preferable for repeated string construction.

---

# 17. String and Memory

A useful conceptual model is:

```text
Stack
┌──────────────────┐
│ String reference │
└────────┬─────────┘
         │
         ▼
Heap
┌──────────────────┐
│ String object    │
│ character data   │
└──────────────────┘
```

The exact JVM memory representation is implementation-dependent.

Modern Java implementations may use compact strings internally, so do not assume that every String is literally backed by a `char[]`.

---

# 18. String and `hashCode()`

`String` overrides `hashCode()` based on its contents.

Therefore:

```java
String a = "Java";
String b = new String("Java");

System.out.println(a.equals(b));       // true
System.out.println(a.hashCode() == b.hashCode()); // true
```

This follows the Java contract:

> Equal objects must have equal hash codes.

That's why String works well as a `HashMap` key.

```java
Map<String, Integer> map = new HashMap<>();

map.put("Java", 100);

System.out.println(map.get(new String("Java")));
```

This can successfully retrieve the value because equality and hash code are content-based.

---

# 19. Important String Methods

### Length

```java
s.length();
```

### Character access

```java
s.charAt(0);
```

### Equality

```java
s.equals(other);
```

### Case-insensitive equality

```java
s.equalsIgnoreCase(other);
```

### Search

```java
s.contains("Java");
s.indexOf("a");
```

### Substring

```java
s.substring(0, 4);
```

### Replace

```java
s.replace("Java", "Python");
```

### Split

```java
s.split(",");
```

### Trim / whitespace

```java
s.trim();
s.strip();
```

`strip()` is Unicode-aware and is generally preferable when appropriate in modern Java.

---

# 20. Important Edge Case: Null

This:

```java
String s = null;

s.length();
```

causes:

```text
NullPointerException
```

But:

```java
String s = null;

System.out.println(s == null);
```

is valid.

For safe equality when one side might be null:

```java
Objects.equals(a, b);
```

or:

```java
"Java".equals(s);
```

when `"Java"` is known non-null.

---

# 21. String and `char[]`

You can convert:

```java
char[] chars = {'J', 'a', 'v', 'a'};

String s = new String(chars);
```

And:

```java
char[] chars = s.toCharArray();
```

This is sometimes relevant when working with APIs requiring mutable character arrays.

---

# 22. Security Interview Point

A common discussion is:

> Why are passwords sometimes handled with `char[]` rather than `String`?

Because `String` is immutable, its contents cannot be explicitly cleared by application code after use.

A mutable `char[]` can be overwritten:

```java
char[] password = {'s', 'e', 'c', 'r', 'e', 't'};

// use password

Arrays.fill(password, '\0');
```

This does **not** guarantee complete memory erasure in every environment, but it gives the application more control than an immutable String.

---

# 23. Production Connection

Strings appear everywhere in backend systems:

```text
HTTP Request
     ↓
Headers → String
     ↓
JSON → String/Text
     ↓
Database → String
     ↓
Logging → String
     ↓
URLs → String
     ↓
Authentication data → String
```

In high-throughput systems, excessive temporary String creation can contribute to:

* Allocation pressure
* GC activity
* CPU overhead
* Memory consumption

Therefore string handling can become a performance consideration.

---

# 24. Common Interview Traps

### ❌ "String is a primitive."

Wrong.

**String is a reference type/class.**

---

### ❌ "String is mutable."

Wrong.

**String is immutable.**

---

### ❌ "`==` compares String values."

Wrong.

For references, `==` compares reference identity.

Use:

```java
equals()
```

for content equality.

---

### ❌ "StringBuilder is synchronized."

Wrong.

`StringBuilder` is not synchronized.

---

### ❌ "StringBuffer is always better because it is thread-safe."

Wrong.

Synchronization has overhead, and thread safety does not automatically make it the better choice.

---

### ❌ "String Pool is the same thing as the entire Heap."

Wrong.

The String Pool is a special logical area/managed pool of canonical String literals; it is not equivalent to "the whole heap."

---

# 25. Interviewer Follow-Up Chain

A realistic interview drill:

```text
What is String?
      ↓
Is String primitive or reference type?
      ↓
Why is String immutable?
      ↓
What is the String Pool?
      ↓
Why does Java use a String Pool?
      ↓
Difference between == and equals()?
      ↓
How many objects are created here?
      ↓
What does intern() do?
      ↓
Why is String final?
      ↓
Why is String useful as a HashMap key?
      ↓
String vs StringBuilder?
      ↓
StringBuilder vs StringBuffer?
      ↓
What happens internally with String concatenation?
      ↓
What happens at compile time vs runtime?
      ↓
How does String immutability improve security?
      ↓
What are the performance implications of repeated concatenation?
```

---

# 26. Common Candidate Mistakes

### Weak Answer

> String is immutable because Java doesn't allow us to change it.

### Better Answer

> String is immutable because once a String object is created, its character sequence cannot be modified. This enables safe sharing through the String Pool, stable hash codes, easier thread safety, and safer use in security-sensitive contexts.

---

### Weak Answer

> `==` checks values and `equals()` checks references.

### Correct

```text
==       → reference identity for objects
equals() → logical/content equality when overridden appropriately
```

For `String`, `equals()` compares character content.

---

# 27. 30-Second Revision

```text
STRING
  ↓
Reference Type
  ↓
Immutable
  ↓
String Pool
  ↓
Safe Sharing
  ↓
Stable hashCode
  ↓
Good HashMap Key
```

### Remember:

```text
"Java"                  → String literal / Pool
new String("Java")      → distinct String object
==                      → reference identity
equals()                → content equality
StringBuilder           → mutable, preferred for repeated construction
StringBuffer            → mutable + synchronized methods
intern()                → canonical pooled String
```

---

# 28. Master Interview Test

Answer these without looking back:

1. What is `String` in Java?
2. Why is `String` immutable?
3. What is the String Pool?
4. What is the difference between `==` and `equals()` for Strings?
5. How many String objects can be involved in `new String("Java")`?
6. What does `intern()` do?
7. Why is `String` declared `final`?
8. Why is String a good `HashMap` key?
9. **What happens internally when you repeatedly concatenate Strings inside a loop?**
10. **Why would you choose `StringBuilder` over `String` or `StringBuffer` in a high-performance backend application?**
