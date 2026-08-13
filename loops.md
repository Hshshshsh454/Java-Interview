# Loops in Java

**Difficulty:** ⭐ Must-Know
**Interview Relevance:** ⭐⭐⭐⭐⭐
**Category:** Java Core / Control Flow / DSA Fundamentals

---

## 0. Interview Relevance

Loops allow a program to **execute a block of code repeatedly** based on a condition or traversal requirement.

### ⭐ Frequently Asked

* What is a loop?
* `for` vs `while` vs `do-while`?
* What is an infinite loop?
* What is the enhanced `for` loop?
* Difference between `break` and `continue`?
* How do nested loops execute?
* What is the time complexity of nested loops?
* Can a `for` loop omit initialization/condition/update?
* What happens with `for(;;)`?
* How does a loop work internally at the bytecode level?
* When should you choose each loop?

---

# 1. Precise Definition

A **loop** is a control-flow construct that repeatedly executes a block of statements while a specified condition remains satisfied or while elements remain to be processed.

### Interview-ready answer

> **A loop repeatedly executes a block of code according to a termination condition or traversal rule, allowing repetitive operations without duplicating source code.**

Basic model:

```text
Initialize
    ↓
Check condition
    ↓
 ┌──Yes──→ Execute body
 │              ↓
 │           Update
 │              ↓
 └──────── Condition
                ↓
               No
                ↓
              Exit
```

---

# 2. Why Do Loops Exist?

Without loops:

```java
System.out.println(1);
System.out.println(2);
System.out.println(3);
System.out.println(4);
System.out.println(5);
```

With a loop:

```java
for (int i = 1; i <= 5; i++) {
    System.out.println(i);
}
```

The loop provides:

* Repetition
* Less duplicated code
* Easier maintenance
* Data traversal
* Algorithmic iteration

Loops are fundamental to:

* Arrays
* Collections
* Searching
* Sorting
* String processing
* Graph traversal
* Matrix problems
* Backend batch processing

---

# 3. Types of Loops in Java

```text
Loops
│
├── for
│
├── while
│
├── do-while
│
└── enhanced for / for-each
```

---

# 4. `for` Loop

The `for` loop is useful when initialization, condition, and iteration/update can naturally be expressed together.

```java
for (int i = 0; i < 5; i++) {
    System.out.println(i);
}
```

### Structure

```text
for (initialization; condition; update) {
    body
}
```

Execution order:

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

# 5. `for` Loop Internal Execution

Consider:

```java
for (int i = 0; i < 3; i++) {
    System.out.println(i);
}
```

Conceptually equivalent to:

```java
int i = 0;

while (i < 3) {
    System.out.println(i);
    i++;
}
```

Execution:

```text
i = 0
 ↓
i < 3
 ↓
print(0)
 ↓
i++
 ↓
i < 3
 ↓
print(1)
 ↓
i++
 ↓
...
```

---

# 6. `while` Loop

A `while` loop checks its condition **before** executing its body.

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
 ┌──┴──┐
Yes    No
 ↓      ↓
Body   Exit
 ↓
Update
 ↓
Condition
```

Because the condition is checked first, the body may execute **zero times**.

```java
while (false) {
    System.out.println("Hello");
}
```

Nothing executes.

---

# 7. `do-while` Loop

`do-while` executes the body before checking the condition.

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
Update
 ↓
Condition
 ↓
 ┌───────┐
Yes      No
 ↓        ↓
Body     Exit
```

Therefore:

```java
do {
    System.out.println("Hello");
} while (false);
```

prints:

```text
Hello
```

⭐ **The body executes at least once.**

---

# 8. Enhanced `for` Loop

Also called the **for-each loop**.

```java
int[] numbers = {10, 20, 30};

for (int number : numbers) {
    System.out.println(number);
}
```

This is useful when you need to process each element but don't need explicit index control.

Conceptually:

```text
Array
 ↓
Element 1
 ↓
Element 2
 ↓
Element 3
 ↓
End
```

---

# 9. `for` vs `while` vs `do-while`

| Feature            | `for`                | `while`                    | `do-while`        |
| ------------------ | -------------------- | -------------------------- | ----------------- |
| Condition check    | Before body          | Before body                | After body        |
| Minimum executions | 0                    | 0                          | 1                 |
| Best for           | Structured iteration | Condition-driven iteration | Must execute once |
| Initialization     | Usually header       | Usually before             | Usually before    |
| Update             | Usually header       | Usually body               | Usually body      |

### Mental model

```text
for
→ "I know the iteration structure."

while
→ "Continue while this condition is true."

do-while
→ "Execute once, then decide."
```

---

# 10. Infinite Loops

A loop that never reaches its termination condition is an **infinite loop**.

### `while`

```java
while (true) {
    System.out.println("Running");
}
```

### `for`

```java
for (;;) {
    System.out.println("Running");
}
```

`for(;;)` is a valid infinite loop because all three `for` components are optional.

---

# 11. Infinite Loop With a Termination Condition

An infinite loop isn't necessarily a bug.

For example:

```java
while (true) {

    String command = readCommand();

    if (command.equals("exit")) {
        break;
    }
}
```

Flow:

```text
while(true)
    ↓
Read command
    ↓
exit?
 ┌──┴──┐
Yes    No
 ↓      ↓
break  repeat
 ↓
Exit
```

This pattern appears in:

* Servers
* Event loops
* Message consumers
* Interactive applications

---

# 12. `break`

`break` immediately terminates the nearest enclosing loop.

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
Condition
 ↓
i == 5?
 ↓ yes
break
 ↓
Exit
```

---

# 13. `continue`

`continue` skips the remaining statements of the current iteration.

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

Flow:

```text
Iteration
   ↓
Condition
   ↓
continue?
   ↓ yes
Next iteration
```

---

# 14. `break` vs `continue`

⭐ **Frequently Asked**

| `break`                      | `continue`                    |
| ---------------------------- | ----------------------------- |
| Terminates loop              | Skips current iteration       |
| Control leaves loop          | Control remains in loop       |
| No further iterations        | Future iterations continue    |
| Useful for early termination | Useful for filtering/skipping |

Example:

```text
break
 ↓
EXIT LOOP


continue
 ↓
SKIP CURRENT ITERATION
 ↓
NEXT ITERATION
```

---

# 15. `return` Inside a Loop

`return` is different from both.

```java
static void test() {

    for (int i = 0; i < 10; i++) {

        if (i == 5) {
            return;
        }

        System.out.println(i);
    }
}
```

When `i == 5`:

```text
return
 ↓
Exit loop
 ↓
Exit method
```

So:

```text
break     → exit loop
continue  → next iteration
return    → exit method
```

---

# 16. Nested Loops

A loop inside another loop is a **nested loop**.

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

Total iterations:

```text
3 × 3 = 9
```

---

# 17. Nested Loop Time Complexity

If:

```java
for (int i = 0; i < n; i++) {

    for (int j = 0; j < n; j++) {

    }
}
```

outer loop:

```text
n
```

inner loop:

```text
n
```

Total:

```text
n × n = n²
```

Therefore:

```text
O(n²)
```

### Important

Don't blindly say:

> "Nested loops always mean O(n²)."

Consider:

```java
for (int i = 0; i < n; i++) {

    for (int j = 0; j < 10; j++) {
    }
}
```

This is:

```text
O(n × 10)
= O(n)
```

Complexity depends on the **actual number of iterations**.

---

# 18. Dependent Nested Loops

Consider:

```java
for (int i = 0; i < n; i++) {

    for (int j = i; j < n; j++) {

    }
}
```

The inner loop executes:

```text
n + (n-1) + (n-2) + ... + 1
```

which is:

```text
n(n+1)/2
```

Therefore:

```text
O(n²)
```

---

# 19. Logarithmic Loop

Consider:

```java
for (int i = 1; i < n; i *= 2) {
    System.out.println(i);
}
```

Values:

```text
1
2
4
8
16
32
...
```

The number of iterations is approximately:

```text
log₂(n)
```

Therefore:

```text
O(log n)
```

This pattern appears in:

* Binary search
* Divide-and-conquer algorithms
* Tree algorithms

---

# 20. Multiple Sequential Loops

Consider:

```java
for (int i = 0; i < n; i++) {
}

for (int j = 0; j < n; j++) {
}
```

This is:

```text
O(n) + O(n)
= O(2n)
= O(n)
```

Sequential loops are **added**, not multiplied.

---

# 21. Loop With Multiplicative Growth

```java
for (int i = 1; i < n; i *= 3) {
}
```

Values:

```text
1
3
9
27
81
...
```

Number of iterations:

```text
O(log₃ n)
```

Since Big-O ignores constant bases:

```text
O(log n)
```

---

# 22. Loop With Division

```java
for (int i = n; i > 0; i /= 2) {
}
```

Values:

```text
n
n/2
n/4
n/8
...
1
```

Complexity:

```text
O(log n)
```

---

# 23. Loop Control and `break`

Consider:

```java
for (int i = 0; i < n; i++) {

    if (arr[i] == target) {
        break;
    }
}
```

Worst case:

```text
O(n)
```

Best case:

```text
O(1)
```

Average complexity depends on the distribution of the target.

This is an important interview distinction:

> A loop's syntactic structure does not always determine its exact runtime.

---

# 24. Loop Control and `continue`

Example:

```java
for (int i = 0; i < n; i++) {

    if (arr[i] % 2 == 0) {
        continue;
    }

    process(arr[i]);
}
```

The loop still potentially examines all `n` elements.

Therefore:

```text
O(n)
```

`continue` changes the work inside iterations, not necessarily the number of iterations.

---

# 25. Scope of Loop Variables

Consider:

```java
for (int i = 0; i < 5; i++) {
    System.out.println(i);
}
```

`i` is scoped to the `for` statement/body.

This is invalid:

```java
System.out.println(i); // ❌
```

But:

```java
int i;

for (i = 0; i < 5; i++) {
}

System.out.println(i); // ✅
```

because `i` was declared outside the loop.

---

# 26. Multiple Variables in a `for` Loop

Java allows multiple expressions in the `for` header:

```java
for (int i = 0, j = 10; i < j; i++, j--) {
    System.out.println(i + " " + j);
}
```

This can be useful for two-pointer algorithms.

Example:

```text
i → → →


← ← ← j
```

Common in:

* Two-pointer problems
* Palindrome checking
* Array partitioning

---

# 27. Loop With No Condition

This:

```java
for (;;) {
}
```

is valid.

It creates an infinite loop.

Equivalent conceptually to:

```java
while (true) {
}
```

A termination mechanism must eventually occur if the program is expected to exit:

```java
break;
return;
throw;
```

---

# 28. Loop and Arrays

Classic array traversal:

```java
int[] numbers = {10, 20, 30, 40};

for (int i = 0; i < numbers.length; i++) {
    System.out.println(numbers[i]);
}
```

Why use `numbers.length`?

Because the loop adapts automatically to the array size.

Avoid hardcoding:

```java
i < 4
```

when the actual size is available.

---

# 29. Loop and Collections

For a collection:

```java
List<String> names = List.of(
    "A",
    "B",
    "C"
);
```

You can use:

```java
for (String name : names) {
    System.out.println(name);
}
```

Or an index-based loop when the data structure/API supports efficient indexed access:

```java
for (int i = 0; i < names.size(); i++) {
    System.out.println(names.get(i));
}
```

The best choice depends on the data structure and whether you need indices.

For example, repeated `get(i)` on a linked list can be inefficient.

---

# 30. Internal Working

At the JVM bytecode level, loops are implemented using:

* Load/store instructions
* Comparison instructions
* Conditional branch instructions
* Unconditional jump instructions

Conceptually:

```text
Condition
   ↓
Compare
   ↓
Conditional Branch
 ┌───────┴────────┐
 ↓                ↓
Body             Exit
 ↓
Update
 ↓
Jump back
 ↓
Condition
```

The Java compiler translates source-level loops into bytecode branches.

The JVM/JIT may later optimize hot loops through techniques such as:

* Loop optimizations
* Inlining
* Range-check elimination
* Loop unrolling in some circumstances

Do not assume a particular optimization will always occur; it is JVM/runtime dependent.

---

# 31. Production Connection

Loops are fundamental in backend systems.

### Batch processing

```text
100,000 records
      ↓
Loop
      ↓
Validate
      ↓
Transform
      ↓
Store
```

### Message processing

```text
while(serviceRunning)
        ↓
receive message
        ↓
process
        ↓
acknowledge
        ↓
repeat
```

### Pagination

```text
page = 1

while(hasMorePages)
    fetch page
    process data
    page++
```

### Retry logic

```text
attempt = 1

while(attempt <= maxAttempts)
    try operation
    if success → break
    attempt++
```

---

# 32. Bad vs Good Loop Design

### ❌ Bad

```java
for (int i = 0; i < users.size(); i++) {

    if (users.get(i).isActive()) {
        if (users.get(i).hasPermission()) {
            if (users.get(i).isVerified()) {
                process(users.get(i));
            }
        }
    }
}
```

Deep nesting can reduce readability.

### Better

```java
for (User user : users) {

    if (!user.isActive()) continue;
    if (!user.hasPermission()) continue;
    if (!user.isVerified()) continue;

    process(user);
}
```

This uses **guard-style filtering** and makes the main operation easier to see.

---

# 33. Common Interview Traps

### Is `for` always better when the number of iterations is known?

Not necessarily.

Choose the loop based on clarity and required control.

---

### Is a nested loop always `O(n²)`?

❌ No.

It depends on the iteration bounds.

---

### Does `continue` terminate a loop?

❌ No.

It only skips the current iteration.

---

### Does `break` terminate the method?

❌ No.

It terminates the nearest applicable loop or switch.

---

### Does `return` only exit a loop?

❌ No.

It exits the current method.

---

### Does `do-while` always execute once?

✅ Yes, if control reaches the loop.

---

### Is `for(;;)` invalid?

❌ No.

It is a valid infinite loop.

---

# 34. Interviewer Follow-Up Chain

```text
What is a loop?
      ↓
Why do we need loops?
      ↓
Types of loops in Java?
      ↓
for vs while?
      ↓
while vs do-while?
      ↓
What is enhanced for?
      ↓
break vs continue?
      ↓
What is an infinite loop?
      ↓
What happens with for(;;)?
      ↓
How do nested loops execute?
      ↓
What is the complexity of nested loops?
      ↓
What if the inner loop starts from i?
      ↓
What if i doubles each iteration?
      ↓
What happens at the bytecode level?
      ↓
How would you optimize a hot loop?
      ↓
How do loops affect memory/cache/GC?
```

---

# 35. Common Candidate Mistakes

### ❌ Weak

> `for` is used for a fixed number of iterations and `while` is used for an unknown number.

### Better

> `for` is convenient when initialization, termination, and update form a structured iteration pattern. `while` is preferable when the continuation condition is the primary concern. Neither requires the iteration count to be known exactly in advance.

---

### ❌ Weak

> Nested loops are always O(n²).

### Better

> Nested loops are not automatically O(n²). The complexity depends on how many times the inner loop executes for each outer iteration.

---

# 36. 30-Second Revision

```text
LOOPS
│
├── for
│    → structured iteration
│
├── while
│    → condition-first
│
├── do-while
│    → body-first
│
└── enhanced for
     → element traversal
```

### Control:

```text
break
→ exit loop

continue
→ skip current iteration

return
→ exit method
```

### Complexity patterns:

```text
n iterations        → O(n)

n × n               → O(n²)

n + n               → O(n)

i *= 2              → O(log n)

i /= 2              → O(log n)

nested dependent
loops               → analyze actual bounds
```

---

# 37. Master Interview Test

Answer these without looking back:

1. What is a loop and why is it required?
2. Explain the execution order of a `for` loop.
3. Difference between `for`, `while`, and `do-while`?
4. What is the enhanced `for` loop?
5. Difference between `break`, `continue`, and `return`?
6. What is an infinite loop, and when can it be useful?
7. Why is a nested loop not necessarily `O(n²)`?
8. What is the complexity of a loop where `i` doubles every iteration?
9. **Analyze the time complexity of a nested loop where the inner loop starts from `i` instead of `0`.**
10. **At the JVM level, how is a loop represented, and what kinds of optimizations can a JIT compiler potentially apply to a hot loop?**
