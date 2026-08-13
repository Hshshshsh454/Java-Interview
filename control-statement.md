# Control Flow Statements in Java

**Difficulty:** ⭐ Must-Know
**Interview Relevance:** ⭐⭐⭐⭐⭐
**Category:** Java Core / Programming Fundamentals

---

## 0. Interview Relevance & Question Mapping

Control flow determines **the order in which program statements execute**.

### ⭐ Frequently Asked

* What are control flow statements?
* Difference between `if`, `if-else`, and `switch`?
* `for` vs `while` vs `do-while`?
* What is the difference between `break` and `continue`?
* Can `break` be used outside loops?
* Can `continue` be used in `switch`?
* What is fall-through in `switch`?
* What happens if `while(false)` is used?
* What is the enhanced `for` loop?
* When should you use each loop?

---

# 1. Precise Definition

**Control flow statements** are statements that determine the **order, repetition, branching, or termination of execution** in a program.

Normally:

```text
Statement 1
    ↓
Statement 2
    ↓
Statement 3
    ↓
Statement 4
```

Control flow allows:

```text
         Condition
        /         \
      true        false
       ↓            ↓
   Statement A   Statement B
```

or:

```text
Statement
   ↓
Condition
   ↓
Repeat
   ↓
Condition
   ↓
Exit
```

---

# 2. Types of Control Flow

Java control flow statements can be divided into four major categories:

```text
Control Flow
│
├── 1. Selection / Decision
│      ├── if
│      ├── if-else
│      ├── else-if
│      └── switch
│
├── 2. Iteration / Looping
│      ├── for
│      ├── while
│      ├── do-while
│      └── enhanced for
│
├── 3. Branching / Jump
│      ├── break
│      ├── continue
│      └── return
│
└── 4. Exception-based Control Flow
       ├── try
       ├── catch
       ├── finally
       └── throw
```

For basic Java interviews, the first three are the core control-flow categories.

---

# 3. `if` Statement

Used when execution depends on a condition.

```java
int age = 20;

if (age >= 18) {
    System.out.println("Eligible");
}
```

Flow:

```text
age >= 18?
   │
 ┌─┴─┐
Yes  No
 ↓    ↓
Code  Skip
```

### Key point

The body executes only if the condition evaluates to `true`.

---

# 4. `if-else`

Used when there are two possible paths.

```java
int age = 16;

if (age >= 18) {
    System.out.println("Adult");
} else {
    System.out.println("Minor");
}
```

Flow:

```text
       Condition
       /       \
    true       false
     ↓           ↓
  Block A     Block B
```

---

# 5. `else-if`

Used when multiple mutually exclusive conditions need to be tested.

```java
int marks = 85;

if (marks >= 90) {
    System.out.println("A+");
} else if (marks >= 75) {
    System.out.println("A");
} else if (marks >= 60) {
    System.out.println("B");
} else {
    System.out.println("C");
}
```

Java evaluates conditions **top to bottom**.

Once one condition is true, the remaining `else-if` branches are skipped.

---

# 6. Nested `if`

An `if` inside another `if`.

```java
int age = 25;
boolean hasLicense = true;

if (age >= 18) {

    if (hasLicense) {
        System.out.println("Can drive");
    }
}
```

Flow:

```text
Age >= 18?
    ↓ yes
License?
    ↓ yes
Can drive
```

Deep nesting can make code harder to maintain, so guard clauses are often preferable.

---

# 7. `switch`

`switch` is useful when selecting among multiple discrete cases.

```java
int day = 2;

switch (day) {
    case 1:
        System.out.println("Monday");
        break;

    case 2:
        System.out.println("Tuesday");
        break;

    default:
        System.out.println("Invalid");
}
```

Flow:

```text
       day
        ↓
 ┌──────┼──────┐
 1      2     default
 ↓      ↓       ↓
Mon    Tue    Invalid
```

---

# 8. `break` in Switch

Consider:

```java
int x = 1;

switch (x) {

    case 1:
        System.out.println("One");

    case 2:
        System.out.println("Two");

    default:
        System.out.println("Other");
}
```

Output:

```text
One
Two
Other
```

Why?

Because traditional `switch` cases can **fall through** when `break` is absent.

```text
case 1
  ↓
case 2
  ↓
default
```

With:

```java
break;
```

execution exits the switch.

---

# 9. Modern `switch`

Modern Java also supports switch expressions.

Example:

```java
int day = 2;

String result = switch (day) {
    case 1 -> "Monday";
    case 2 -> "Tuesday";
    default -> "Invalid";
};
```

This avoids accidental fall-through between arrow cases.

Switch expressions can also use:

```java
yield
```

when a multi-statement case needs to produce a value.

---

# 10. `for` Loop

Used when the number of iterations or loop progression is relatively clear.

```java
for (int i = 0; i < 5; i++) {
    System.out.println(i);
}
```

Execution:

```text
Initialization
      ↓
Condition
      ↓
   Body
      ↓
Update
      ↓
Condition
      ↓
   ...
```

Output:

```text
0
1
2
3
4
```

---

# 11. `while` Loop

Used when repetition depends primarily on a condition.

```java
int i = 0;

while (i < 5) {
    System.out.println(i);
    i++;
}
```

Flow:

```text
Condition
   ↓
 true?
 /   \
yes   no
 ↓     ↓
Body  Exit
 ↓
Update
 ↓
Condition
```

### Important

`while` is a **pre-test loop**.

The condition is evaluated before the body.

Therefore:

```java
while (false) {
    System.out.println("Hello");
}
```

executes zero times.

---

# 12. `do-while`

```java
int i = 0;

do {
    System.out.println(i);
    i++;
} while (i < 5);
```

Flow:

```text
Body
 ↓
Condition
 ↓
true → Body
false → Exit
```

Unlike `while`, the body executes **at least once**.

Example:

```java
do {
    System.out.println("Hello");
} while (false);
```

Output:

```text
Hello
```

⭐ **Frequently Asked**

---

# 13. `for` vs `while` vs `do-while`

| Feature                        | `for`                      | `while`                    | `do-while`        |
| ------------------------------ | -------------------------- | -------------------------- | ----------------- |
| Condition checked              | Before body                | Before body                | After body        |
| Minimum executions             | 0                          | 0                          | 1                 |
| Best use                       | Known/structured iteration | Condition-driven iteration | Must execute once |
| Initialization/update location | Header                     | Usually separate           | Usually separate  |

### Mental model

```text
for
→ "Repeat this controlled number of times."

while
→ "Keep going while this condition is true."

do-while
→ "Do this first, then decide whether to continue."
```

---

# 14. Enhanced `for` Loop

Also called the **for-each loop**.

```java
int[] numbers = {10, 20, 30};

for (int number : numbers) {
    System.out.println(number);
}
```

Conceptually:

```text
Collection / Array
       ↓
Element 1
       ↓
Element 2
       ↓
Element 3
```

It is useful when you need each element but don't need explicit index management.

---

# 15. `break`

`break` terminates the nearest applicable loop or switch.

```java
for (int i = 0; i < 10; i++) {

    if (i == 5) {
        break;
    }

    System.out.println(i);
}
```

Output:

```text
0
1
2
3
4
```

Flow:

```text
Loop
 ↓
i == 5?
 ↓ yes
break
 ↓
Exit loop
```

---

# 16. `continue`

`continue` skips the remaining body of the current iteration and proceeds to the next iteration.

```java
for (int i = 0; i < 5; i++) {

    if (i == 2) {
        continue;
    }

    System.out.println(i);
}
```

Output:

```text
0
1
3
4
```

### Difference

```text
break
 ↓
Exit loop completely


continue
 ↓
Skip current iteration
 ↓
Next iteration
```

---

# 17. `return`

`return` terminates the current method.

```java
static int check(int age) {

    if (age < 18) {
        return 0;
    }

    return 1;
}
```

Unlike `break`, `return` exits the **method**, not merely the loop.

Example:

```java
for (int i = 0; i < 10; i++) {

    if (i == 5) {
        return;
    }
}
```

The entire method terminates.

---

# 18. Labeled Control Flow

Java supports labeled `break` and `continue`.

Example:

```java
outer:
for (int i = 0; i < 3; i++) {

    for (int j = 0; j < 3; j++) {

        if (j == 1) {
            break outer;
        }
    }
}
```

`break outer` exits the labeled outer loop.

Flow:

```text
outer loop
   ↓
inner loop
   ↓
break outer
   ↓
Exit outer loop
```

This is valid but should be used sparingly because excessive labels can reduce readability.

---

# 19. Nested Loops

Example:

```java
for (int i = 0; i < 3; i++) {

    for (int j = 0; j < 3; j++) {
        System.out.println(i + " " + j);
    }
}
```

Execution:

```text
i = 0
 ├── j = 0
 ├── j = 1
 └── j = 2

i = 1
 ├── j = 0
 ├── j = 1
 └── j = 2

i = 2
 ├── j = 0
 ├── j = 1
 └── j = 2
```

If both loops execute `n` times:

```text
Time Complexity ≈ O(n²)
```

This is important for DSA interviews.

---

# 20. Control Flow and Short-Circuit Evaluation

Control flow also interacts with logical operators.

### `&&`

```java
if (a != null && a.length() > 0) {
}
```

Java evaluates the second condition only if the first is true.

```text
a != null?
   ↓
 false → stop
 true
   ↓
a.length() > 0
```

### `||`

```java
if (x == 10 || y == 20) {
}
```

If `x == 10` is true, Java does not need to evaluate the second operand.

This is called **short-circuit evaluation**.

---

# 21. `break` vs `continue` vs `return`

⭐ **Frequently Asked**

| Statement  | Effect                  |
| ---------- | ----------------------- |
| `break`    | Exits loop/switch       |
| `continue` | Skips current iteration |
| `return`   | Exits current method    |

```text
break
 ↓
Loop/Switch ends


continue
 ↓
Current iteration ends


return
 ↓
Method ends
```

---

# 22. Internal Working

At the JVM level, Java control-flow constructs are compiled into **bytecode instructions involving branches, jumps, comparisons, and method returns**.

Conceptually:

```java
if (x > 10) {
    ...
}
```

becomes:

```text
Load x
  ↓
Compare
  ↓
Conditional branch
  ├── true → block
  └── false → next code
```

A loop is conceptually:

```text
Condition
   ↓
Conditional branch
   ↓
Body
   ↓
Jump back
   ↓
Condition
```

The exact bytecode depends on the source and compiler.

---

# 23. Production Connection

Control flow appears everywhere in backend systems.

### Authentication

```text
Request
 ↓
Token exists?
 ├── No → 401
 └── Yes
       ↓
Token valid?
 ├── No → 401
 └── Yes → Continue
```

### Payment

```text
Payment request
      ↓
Validate
      ↓
Valid?
 ├── No → Reject
 └── Yes
       ↓
Process payment
       ↓
Success?
 ├── No → Retry/Failure
 └── Yes → Complete
```

### Order processing

```text
Order
 ↓
Inventory available?
 ↓
Payment successful?
 ↓
Create order
 ↓
Ship
```

Control flow is essentially the mechanism through which application logic makes decisions and repeats operations.

---

# 24. Common Interview Traps

### Can `break` be used outside a loop?

Yes, it can also be used in a `switch`, but not as a standalone statement.

---

### Can `continue` be used with `switch`?

A bare `continue` is only valid when there is an enclosing loop to continue; it does not mean "continue the switch."

---

### Does `do-while` always execute once?

Yes, assuming normal execution reaches the loop.

---

### Can `if` directly accept an integer?

No.

```java
if (1) { } // ❌
```

Java requires a boolean expression.

```java
if (true) { } // ✅
```

This differs from languages such as C/C++ where integers can participate in truth-value contexts.

---

### Can `switch` use `String`?

Yes.

```java
String role = "ADMIN";

switch (role) {
    case "ADMIN":
        System.out.println("Admin");
        break;
}
```

---

# 25. Common Candidate Mistake

### ❌ Weak Answer

> Control flow means if-else and loops.

### ✅ Better Answer

> Control flow statements determine the order in which program instructions execute. They provide selection through constructs such as `if` and `switch`, iteration through loops such as `for` and `while`, and branching through `break`, `continue`, and `return`.

---

# 26. 30-Second Revision

```text
CONTROL FLOW
│
├── Selection
│     ├── if
│     ├── if-else
│     └── switch
│
├── Iteration
│     ├── for
│     ├── while
│     ├── do-while
│     └── enhanced for
│
└── Branching
      ├── break
      ├── continue
      └── return
```

### Remember:

```text
if
→ decision

switch
→ multiple discrete choices

for
→ structured repetition

while
→ condition-first repetition

do-while
→ body-first repetition

break
→ exit

continue
→ skip current iteration

return
→ exit method
```

---

# 27. Master Interview Test

Answer these without looking back:

1. What are control flow statements?
2. What are the major categories of control flow in Java?
3. Difference between `if-else` and `switch`?
4. Difference between `for`, `while`, and `do-while`?
5. Why does `do-while` execute at least once?
6. Difference between `break`, `continue`, and `return`?
7. What is fall-through in a traditional `switch`?
8. What is short-circuit evaluation, and how does it affect control flow?
9. **What happens at the bytecode level when Java executes an `if` or loop?**
10. **Given nested loops with conditional `break`/`continue`, how would you determine the exact execution flow and time complexity?**
