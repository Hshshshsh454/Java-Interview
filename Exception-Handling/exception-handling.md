# Exception Handling in Java

**Difficulty:** ⭐ Must-Know → Expert
**Interview Relevance:** ⭐⭐⭐⭐⭐
**Category:** Java Core / JVM / OOP / Backend / Spring Boot

---

## 0. Interview Relevance & Question Mapping

Exception handling is a **core Java interview topic** and becomes especially important in Spring Boot/backend development.

### Priority

| Topic                      | Priority |
| -------------------------- | -------- |
| `try-catch`                | ⭐⭐⭐⭐⭐    |
| `finally`                  | ⭐⭐⭐⭐⭐    |
| Checked vs Unchecked       | ⭐⭐⭐⭐⭐    |
| `throw` vs `throws`        | ⭐⭐⭐⭐⭐    |
| Exception hierarchy        | ⭐⭐⭐⭐⭐    |
| Custom exceptions          | ⭐⭐⭐⭐     |
| Try-with-resources         | ⭐⭐⭐⭐⭐    |
| Exception propagation      | ⭐⭐⭐⭐⭐    |
| Multi-catch                | ⭐⭐⭐      |
| Suppressed exceptions      | ⭐⭐⭐⭐     |
| JVM exception flow         | ⭐⭐⭐⭐     |
| Spring `@ExceptionHandler` | ⭐⭐⭐⭐⭐    |

### ⭐ Frequently Asked

* What is an exception?
* Exception vs Error?
* Checked vs unchecked exception?
* `throw` vs `throws`?
* `final` vs `finally` vs `finalize()`?
* How does `try-catch-finally` work?
* What happens if `finally` contains `return`?
* Can we have `try` without `catch`?
* Can we have multiple `catch` blocks?
* Why must specific exceptions come before general exceptions?
* What is exception propagation?
* What is try-with-resources?
* What is a custom exception?
* How does Spring Boot handle exceptions globally?

---

# 1. Precise Definition

An **exception** is an object representing an abnormal condition that disrupts the normal execution flow of a program.

**Exception handling** is Java's mechanism for detecting, propagating, and handling such exceptional conditions.

### Interview-ready answer

> **Exception handling is a Java mechanism for handling abnormal runtime conditions through constructs such as `try`, `catch`, `finally`, `throw`, and `throws`, allowing the program to recover, translate, or terminate gracefully instead of relying on uncontrolled failure.**

---

# 2. Why Does Exception Handling Exist?

Without exception handling:

```java
int result = 10 / 0;

System.out.println("Continue");
```

Execution terminates because division by zero causes an exception.

With handling:

```java
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Cannot divide by zero");
}
```

Flow:

```text
Normal execution
      ↓
Exceptional condition
      ↓
Exception object created
      ↓
Search for handler
      ↓
catch
      ↓
Continue / recover / terminate
```

---

# 3. Exception Hierarchy

The basic hierarchy is:

```text
                    Throwable
                   /         \
               Error       Exception
                            /       \
                   RuntimeException  Other Exceptions
```

Examples:

```text
Throwable
│
├── Error
│   ├── OutOfMemoryError
│   └── StackOverflowError
│
└── Exception
    │
    ├── IOException
    ├── SQLException
    │
    └── RuntimeException
        ├── NullPointerException
        ├── ArithmeticException
        ├── ArrayIndexOutOfBoundsException
        └── IllegalArgumentException
```

---

# 4. `Error` vs `Exception`

⭐ **Frequently Asked**

### Error

Usually represents serious conditions that applications generally should not attempt to recover from.

Examples:

```java
OutOfMemoryError
StackOverflowError
```

### Exception

Represents conditions that application code may reasonably handle or propagate.

Examples:

```java
IOException
SQLException
NullPointerException
```

### Important

Do not simply say:

> "Errors cannot be caught."

They **can technically be caught** because `Error` extends `Throwable`.

But catching serious JVM errors as ordinary application exceptions is generally inappropriate unless there is a very specific reason.

---

# 5. Checked Exceptions

A **checked exception** is an exception that the compiler requires you to handle or declare, excluding `RuntimeException` and its subclasses.

Examples:

```java
IOException
SQLException
ClassNotFoundException
```

Example:

```java
void readFile() throws IOException {
}
```

or:

```java
try {
    readFile();
} catch (IOException e) {
}
```

Conceptually:

```text
Checked Exception
       ↓
Compiler checks
       ↓
Handle OR declare
```

---

# 6. Unchecked Exceptions

Unchecked exceptions are subclasses of:

```text
RuntimeException
```

Examples:

```java
NullPointerException
IllegalArgumentException
ArithmeticException
IndexOutOfBoundsException
```

The compiler does not require explicit handling or declaration.

Example:

```java
int x = 10 / 0;
```

can throw:

```text
ArithmeticException
```

without requiring:

```java
throws ArithmeticException
```

---

# 7. Checked vs Unchecked

⭐ **Extremely Frequently Asked**

| Checked                                        | Unchecked                             |
| ---------------------------------------------- | ------------------------------------- |
| Compiler-enforced handling/declaring           | No compiler requirement               |
| Extends `Exception` but not `RuntimeException` | Extends `RuntimeException`            |
| Often external/recoverable conditions          | Often programming/contract violations |
| `IOException`                                  | `NullPointerException`                |
| `SQLException`                                 | `IllegalArgumentException`            |

### Important nuance

The distinction is a **language/compiler rule**, not simply:

> checked = recoverable
> unchecked = unrecoverable

Real-world API design requires more nuance.

---

# 8. `try-catch`

Basic structure:

```java
try {
    // risky code
} catch (ExceptionType e) {
    // handling
}
```

Example:

```java
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Invalid arithmetic operation");
}
```

Flow:

```text
try
 ↓
Exception?
 ├── No → Continue
 └── Yes
      ↓
   matching catch
      ↓
   handler
```

---

# 9. Multiple `catch` Blocks

```java
try {
    // code
}
catch (ArithmeticException e) {
    // arithmetic problem
}
catch (NullPointerException e) {
    // null problem
}
catch (Exception e) {
    // general fallback
}
```

Java selects the first compatible handler.

---

# 10. Why Must Specific `catch` Come First?

This is invalid:

```java
try {
}
catch (Exception e) {
}
catch (IOException e) { // ❌
}
```

Why?

Because:

```text
IOException
     ↓
Exception
```

The first `catch` already catches `IOException`.

Therefore the second block is unreachable.

Correct:

```java
catch (IOException e) {
}
catch (Exception e) {
}
```

General rule:

```text
Specific
   ↓
General
```

---

# 11. Multi-Catch

Java allows multiple exception types in one `catch`.

```java
try {
    // code
}
catch (IOException | SQLException e) {
    System.out.println("Operation failed");
}
```

Useful when different exceptions require identical handling.

---

# 12. `finally`

`finally` is used for cleanup logic that should execute when control leaves the `try`/`catch` structure, subject to abnormal JVM termination scenarios.

Example:

```java
try {
    System.out.println("Try");
}
catch (Exception e) {
    System.out.println("Catch");
}
finally {
    System.out.println("Finally");
}
```

Possible output:

```text
Try
Finally
```

If an exception occurs:

```text
Try
Catch
Finally
```

---

# 13. Why Does `finally` Exist?

Historically, `finally` is used for resource cleanup:

```text
Open resource
     ↓
Use resource
     ↓
Exception?
 ┌───┴───┐
No      Yes
 ↓       ↓
Use     Handle
 └───┬───┘
     ↓
Cleanup
```

Modern Java generally prefers **try-with-resources** for `AutoCloseable` resources.

---

# 14. Can `finally` Execute Without `catch`?

✅ Yes.

```java
try {
    System.out.println("Try");
}
finally {
    System.out.println("Finally");
}
```

A `try` must be followed by at least one of:

```text
catch
or
finally
```

---

# 15. Can `try` Exist Without `catch`?

Yes, if it has `finally`.

```java
try {
    // code
}
finally {
    // cleanup
}
```

But this is invalid:

```java
try {
}
```

because neither `catch` nor `finally` follows it.

---

# 16. `finally` and `return`

⭐ **Major Interview Trap**

Consider:

```java
static int test() {

    try {
        return 10;
    }
    finally {
        System.out.println("Finally");
    }
}
```

Output:

```text
Finally
```

Return still occurs.

The `finally` block executes before the method actually completes.

---

# 17. Dangerous `return` in `finally`

Consider:

```java
static int test() {

    try {
        return 10;
    }
    finally {
        return 20;
    }
}
```

Result:

```text
20
```

The `finally` return overrides the pending return.

### Recommendation

**Avoid `return`, `throw`, or other control-flow statements in `finally` unless there is a compelling reason.**

They can suppress the original result or exception and make debugging difficult.

---

# 18. `throw`

`throw` explicitly throws an exception object.

```java
if (age < 18) {
    throw new IllegalArgumentException("Age must be 18+");
}
```

Flow:

```text
Condition
   ↓
Invalid?
   ↓
throw
   ↓
Exception propagation
```

---

# 19. `throws`

`throws` declares that a method may propagate specified exceptions to its caller.

```java
void readFile() throws IOException {
    // ...
}
```

It does **not itself throw** an exception.

---

# 20. `throw` vs `throws`

⭐ **Extremely Frequently Asked**

| `throw`                        | `throws`                             |
| ------------------------------ | ------------------------------------ |
| Actually throws an exception   | Declares possible propagation        |
| Used inside method body        | Used in method signature             |
| Followed by exception object   | Followed by exception types          |
| One exception object at a time | Can declare multiple exception types |

Example:

```java
throw new IOException();
```

vs:

```java
void read() throws IOException {
}
```

Mental model:

```text
throw
→ "Throw this now."

throws
→ "This method may propagate this."
```

---

# 21. Exception Propagation

Consider:

```java
void methodA() {
    methodB();
}

void methodB() {
    methodC();
}

void methodC() {
    throw new RuntimeException();
}
```

If `methodC()` does not handle the exception:

```text
methodC()
   ↓
methodB()
   ↓
methodA()
   ↓
caller
   ↓
matching handler
```

This is **exception propagation**.

---

# 22. Call Stack

Conceptually:

```text
main()
  ↓
methodA()
  ↓
methodB()
  ↓
methodC()
```

If `methodC()` throws:

```text
methodC()
   ↓
Search handler
   ↓
Not found
   ↓
Pop methodC
   ↓
Search methodB
   ↓
Not found
   ↓
Search methodA
   ↓
...
```

The JVM searches the call stack for a compatible exception handler.

---

# 23. Internal Exception Flow

⭐ **Advanced**

When an exception occurs:

```text
1. Exceptional condition occurs
        ↓
2. JVM creates/uses an exception object
        ↓
3. Current execution is interrupted
        ↓
4. JVM searches current method for matching handler
        ↓
5. If none exists, stack unwinding begins
        ↓
6. Caller is examined
        ↓
7. Matching handler found
        ↓
8. Handler executes
```

If no handler is found:

```text
Uncaught exception
      ↓
Thread terminates
      ↓
UncaughtExceptionHandler / runtime reporting
```

---

# 24. Stack Trace

Example:

```java
throw new RuntimeException("Something failed");
```

The exception can contain a stack trace showing the execution path that led to the exception.

Conceptually:

```text
Exception
   ↓
Stack Trace
   ↓
main()
 ↓
service()
 ↓
repository()
 ↓
failure()
```

Useful methods:

```java
e.getMessage();
e.getCause();
e.getStackTrace();
e.printStackTrace();
```

In production, use an appropriate logging framework rather than blindly printing stack traces.

---

# 25. Exception Chaining

An exception can preserve its underlying cause.

```java
try {
    // database operation
}
catch (SQLException e) {
    throw new ServiceException(
        "Unable to load user",
        e
    );
}
```

Now:

```text
ServiceException
      ↓
cause
      ↓
SQLException
```

This is called **exception chaining**.

It is important in layered applications because low-level implementation details can be translated into domain/application-level exceptions while preserving the root cause.

---

# 26. Custom Exception

You can define your own exception.

### Unchecked custom exception

```java
class InsufficientBalanceException
        extends RuntimeException {

    public InsufficientBalanceException(String message) {
        super(message);
    }
}
```

Usage:

```java
if (balance < amount) {
    throw new InsufficientBalanceException(
        "Insufficient balance"
    );
}
```

---

# 27. Checked Custom Exception

You can extend `Exception` directly:

```java
class PaymentException extends Exception {

    public PaymentException(String message) {
        super(message);
    }
}
```

Then:

```java
void processPayment()
        throws PaymentException {

    throw new PaymentException("Payment failed");
}
```

The caller must handle or declare the checked exception.

---

# 28. When Should You Create Custom Exceptions?

Use custom exceptions when they communicate a meaningful domain or application condition.

Examples:

```text
UserNotFoundException
OrderNotFoundException
InsufficientBalanceException
PaymentFailedException
InvalidOrderStateException
```

This is more expressive than:

```java
throw new Exception("Error");
```

---

# 29. Try-With-Resources

⭐ **Very Important**

For resources implementing `AutoCloseable`, Java provides try-with-resources.

Instead of:

```java
try {
    // use resource
}
finally {
    // close resource
}
```

use:

```java
try (BufferedReader reader =
        new BufferedReader(
            new FileReader("data.txt"))) {

    String line = reader.readLine();
}
```

The resource is automatically closed.

---

# 30. Why Try-With-Resources Is Better

It provides:

* Automatic cleanup
* Less boilerplate
* Better exception handling
* Support for suppressed exceptions

Flow:

```text
Acquire resource
      ↓
Use resource
      ↓
Exception?
      ↓
Close resource automatically
      ↓
Propagate/handle exception
```

---

# 31. `AutoCloseable`

Try-with-resources works with resources implementing:

```java
AutoCloseable
```

Examples include many:

* Streams
* Readers
* Writers
* Database resources

Example:

```java
class Resource implements AutoCloseable {

    @Override
    public void close() {
        System.out.println("Closed");
    }
}
```

Then:

```java
try (Resource r = new Resource()) {
    System.out.println("Using");
}
```

Output:

```text
Using
Closed
```

---

# 32. Suppressed Exceptions

⭐ **Advanced Interview Question**

Suppose:

```text
try block
   ↓
Exception A
   ↓
resource.close()
   ↓
Exception B
```

Which exception should be primary?

Try-with-resources preserves the primary exception and records the close-time exception as a **suppressed exception**.

You can inspect them with:

```java
for (Throwable t : e.getSuppressed()) {
    System.out.println(t);
}
```

This is one reason try-with-resources is preferable to manually writing cleanup code incorrectly.

---

# 33. `final` vs `finally` vs `finalize()`

⭐ **Classic Interview Question**

### `final`

Keyword used for:

* Variables
* Methods
* Classes

```java
final int x = 10;
```

### `finally`

Exception-handling block:

```java
try {
}
finally {
}
```

### `finalize()`

Historically associated with garbage-collection cleanup, but it has been **deprecated for removal** and should not be used for resource management.

Modern Java applications should use:

* Try-with-resources
* `AutoCloseable`
* Explicit cleanup mechanisms

---

# 34. Spring Boot Exception Handling

In Spring Boot REST APIs, exception handling is often centralized.

Example:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<String> handle(
            UserNotFoundException ex) {

        return ResponseEntity
                .status(HttpStatus.NOT_FOUND)
                .body(ex.getMessage());
    }
}
```

Flow:

```text
HTTP Request
     ↓
Controller
     ↓
Service
     ↓
Exception
     ↓
@RestControllerAdvice
     ↓
@ExceptionHandler
     ↓
HTTP Error Response
```

This avoids duplicating error-handling logic across controllers.

---

# 35. Exception Handling Architecture

A production backend might use:

```text
Controller
    ↓
Service
    ↓
Repository
    ↓
Exception
    ↓
Global Exception Handler
    ↓
Standard Error Response
```

Example response:

```json
{
  "status": 404,
  "message": "User not found",
  "timestamp": "..."
}
```

The exact schema should be designed consistently across the API.

---

# 36. Exception Handling and OOP

Exception handling demonstrates several OOP concepts.

### Inheritance

```text
Throwable
   ↓
Exception
   ↓
RuntimeException
   ↓
CustomException
```

### Polymorphism

```java
catch (Exception e)
```

can catch many subclasses.

### Encapsulation

Exception objects encapsulate:

* Error message
* Cause
* Stack trace
* Suppressed exceptions

---

# 37. Exception Handling and SOLID

Exception design also connects to good architecture.

### Single Responsibility

Don't mix:

```text
Business logic
+
HTTP error formatting
+
Logging
+
Database exception translation
```

inside every method.

Instead:

```text
Service
 → business logic

Repository
 → persistence

Global handler
 → HTTP exception representation
```

This is especially useful in Spring Boot.

---

# 38. Bad vs Good Design

### ❌ Bad

```java
try {
    processPayment();
}
catch (Exception e) {
    System.out.println("Something went wrong");
}
```

Problems:

* Catches everything indiscriminately
* Loses useful context
* May hide programming bugs
* Doesn't communicate failure semantics

### Better

```java
try {
    processPayment();
}
catch (PaymentGatewayException e) {
    log.error("Payment gateway failed", e);
    throw new PaymentException(
        "Payment could not be completed", e
    );
}
```

The exception is:

```text
Caught
  ↓
Logged/contextualized
  ↓
Translated
  ↓
Propagated
```

---

# 39. Common Interview Traps

### Can we catch `Throwable`?

Technically yes.

But catching `Throwable` indiscriminately is usually poor practice because it includes `Error`.

---

### Can we catch `Exception`?

Yes.

But broad catches can hide bugs and make recovery semantics unclear.

---

### Can one `try` have multiple `catch` blocks?

✅ Yes.

---

### Can one `catch` handle multiple exception types?

✅ Yes, using multi-catch.

```java
catch (IOException | SQLException e)
```

---

### Can `finally` be skipped?

Yes, under abnormal termination such as:

```java
System.exit(...)
```

or catastrophic JVM/process termination.

Therefore avoid claiming:

> "`finally` always executes."

More precise:

> **`finally` normally executes when control leaves the associated `try`/`catch` construct, but abnormal JVM/process termination can prevent it.**

---

### Can a `finally` block throw an exception?

✅ Yes.

But it can obscure or replace the original exception, so this should be handled carefully.

---

# 40. Interviewer Follow-Up Chain

```text
What is an exception?
       ↓
Exception vs Error?
       ↓
Checked vs unchecked?
       ↓
Exception hierarchy?
       ↓
try-catch-finally?
       ↓
Why finally?
       ↓
throw vs throws?
       ↓
Exception propagation?
       ↓
How does JVM find a catch block?
       ↓
What happens during stack unwinding?
       ↓
What is exception chaining?
       ↓
What is try-with-resources?
       ↓
What are suppressed exceptions?
       ↓
Custom exceptions?
       ↓
When should you use checked exceptions?
       ↓
How does Spring Boot handle exceptions?
       ↓
How would you design global exception handling?
```

---

# 41. Common Candidate Mistakes

### ❌ Weak

> Checked exceptions happen at compile time and unchecked exceptions happen at runtime.

Not precise.

**Better:**

> Checked exceptions are subject to compile-time checking for handling or declaration. Unchecked exceptions are `RuntimeException` subclasses and are not subject to that requirement. Both ultimately represent runtime exception objects when thrown.

---

### ❌ Weak

> `throws` throws the exception.

Wrong.

**Better:**

> `throw` explicitly throws an exception object; `throws` declares that a method may propagate specified exception types.

---

### ❌ Weak

> `finally` always executes.

Too absolute.

**Better:**

> `finally` normally executes when leaving the associated `try`/`catch`, but abnormal JVM/process termination can prevent it.

---

# 42. 30-Second Revision

```text
Exception Handling
│
├── try
│   → risky code
│
├── catch
│   → handle exception
│
├── finally
│   → cleanup
│
├── throw
│   → explicitly throw
│
└── throws
    → declare propagation
```

### Hierarchy

```text
Throwable
├── Error
└── Exception
    ├── Checked
    └── RuntimeException
        └── Unchecked
```

### Most important distinctions

```text
throw
→ actually throws

throws
→ declares

checked
→ compiler requires handle/declare

unchecked
→ compiler does not require handle/declare

break
→ loop control

exception
→ abnormal control flow
```

### Modern resource handling

```text
try-with-resources
        ↓
AutoCloseable
        ↓
automatic cleanup
```

---

# 43. Master Interview Test

Answer without looking back:

1. What is an exception?
2. Explain the Java exception hierarchy.
3. Difference between `Exception` and `Error`?
4. Checked vs unchecked exception?
5. Explain `try-catch-finally`.
6. Difference between `throw` and `throws`?
7. What is exception propagation?
8. **What exactly happens inside the JVM when an exception is thrown and no handler exists in the current method?**
9. **Why is try-with-resources preferable to manually closing resources in `finally`, and what are suppressed exceptions?**
10. **Design a production Spring Boot exception-handling architecture that converts domain exceptions into consistent HTTP responses without leaking low-level database or infrastructure details.**
