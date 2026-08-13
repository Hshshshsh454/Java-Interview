# Spring Boot

**Difficulty:** ⭐ Must-Know → Expert
**Interview Relevance:** ⭐⭐⭐⭐⭐
**Category:** Java Backend / Spring Framework / Enterprise Development

---

## 0. Interview Relevance & Question Mapping

Spring Boot is one of the highest-value topics for **Java Backend Developer interviews**.

### Priority

| Area                           | Priority |
| ------------------------------ | -------- |
| Spring Core / IoC / DI         | ⭐⭐⭐⭐⭐    |
| Spring Boot Auto-Configuration | ⭐⭐⭐⭐⭐    |
| REST APIs                      | ⭐⭐⭐⭐⭐    |
| Spring MVC                     | ⭐⭐⭐⭐⭐    |
| Spring Data JPA                | ⭐⭐⭐⭐⭐    |
| Spring Security                | ⭐⭐⭐⭐⭐    |
| Transactions                   | ⭐⭐⭐⭐⭐    |
| Configuration / Profiles       | ⭐⭐⭐⭐     |
| Actuator                       | ⭐⭐⭐⭐     |
| Exception Handling             | ⭐⭐⭐⭐     |
| Production / Deployment        | ⭐⭐⭐⭐     |

### ⭐ Frequently Asked

* What is Spring Boot?
* Spring vs Spring Boot?
* What is IoC?
* What is Dependency Injection?
* What is a Spring Bean?
* What is `ApplicationContext`?
* How does Spring Boot auto-configuration work?
* What is `@SpringBootApplication`?
* What is component scanning?
* `@Component` vs `@Service` vs `@Repository`?
* `@Controller` vs `@RestController`?
* How does a REST request flow through Spring Boot?
* What is Spring Boot Starter?
* What is Spring Data JPA?
* What is `@Transactional`?
* How does Spring Security work?
* What happens during Spring Boot application startup?

---

# 1. Precise Definition

**Spring Boot** is a framework built on top of the **Spring Framework** that simplifies the development, configuration, execution, and deployment of Spring-based applications by providing opinionated defaults, auto-configuration, starter dependencies, embedded servers, and production-oriented features.

### Interview-ready answer

> **Spring Boot simplifies Spring application development by providing auto-configuration, starter dependencies, embedded servers, externalized configuration, and production-ready features, reducing boilerplate while still allowing developers to customize the underlying Spring components.**

---

# 2. Why Does Spring Boot Exist?

Traditional Spring applications historically required significant configuration.

Conceptually:

```text
Traditional Spring
       ↓
Many dependencies
       ↓
Manual configuration
       ↓
Server configuration
       ↓
Bean configuration
       ↓
Application deployment
```

Spring Boot aims for:

```text
Spring Boot
     ↓
Starter dependencies
     ↓
Auto-configuration
     ↓
Embedded server
     ↓
Minimal configuration
     ↓
Run application
```

### Core objective

> **Convention over configuration without removing configurability.**

---

# 3. Spring vs Spring Boot

⭐ **Frequently Asked**

| Spring Framework                             | Spring Boot                                             |
| -------------------------------------------- | ------------------------------------------------------- |
| Core enterprise framework                    | Simplifies Spring application development               |
| More explicit configuration historically     | Convention/opinionated defaults                         |
| Requires more setup                          | Auto-configuration                                      |
| Server setup often external                  | Embedded servers commonly used                          |
| Dependency management is more manual         | Starters simplify dependency selection                  |
| Production features require additional setup | Actuator and ecosystem make production readiness easier |

### Important

Spring Boot is **not a replacement for Spring Framework**.

It is built on the Spring ecosystem.

```text
Spring Framework
      ↑
Spring Boot
      ↑
Spring Boot Application
```

---

# 4. Core Architecture

A typical Spring Boot backend:

```text
                  Client
                    │
                    ↓
              HTTP Request
                    │
                    ↓
              Embedded Server
                    │
                    ↓
             Spring MVC Layer
                    │
                    ↓
              Controller
                    │
                    ↓
               Service
                    │
                    ↓
             Repository
                    │
                    ↓
                Database
```

Example:

```text
React / Mobile / Client
          ↓
       REST API
          ↓
     Controller
          ↓
       Service
          ↓
      Repository
          ↓
      PostgreSQL
```

---

# 5. `@SpringBootApplication`

This annotation is central to a Spring Boot application.

```java
@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

`@SpringBootApplication` is effectively a combination of:

```text
@Configuration
@EnableAutoConfiguration
@ComponentScan
```

### Therefore:

```text
@SpringBootApplication
        │
        ├── Configuration
        │
        ├── Auto-Configuration
        │
        └── Component Scanning
```

---

# 6. `SpringApplication.run()`

Consider:

```java
SpringApplication.run(Application.class, args);
```

This starts the Spring Boot application.

Conceptually:

```text
main()
  ↓
SpringApplication.run()
  ↓
Create ApplicationContext
  ↓
Load configuration
  ↓
Component scanning
  ↓
Create/configure beans
  ↓
Auto-configuration
  ↓
Start embedded web server
  ↓
Application ready
```

This startup sequence is an extremely important interview topic.

---

# 7. IoC — Inversion of Control

⭐ **Must-Know**

Normally, your code might create dependencies itself:

```java
PaymentService service =
    new PaymentService();
```

With Spring:

```java
@Service
public class PaymentService {
}
```

Spring manages the object.

Conceptually:

```text
Without IoC

Developer
   ↓
new Object()
   ↓
Application


With IoC

Application
    ↓
Spring Container
    ↓
Creates Object
    ↓
Injects Object
```

### Interview-ready answer

> **IoC means the responsibility for creating, configuring, and managing application objects is transferred from application code to a container or framework.**

---

# 8. Dependency Injection

Dependency Injection is a mechanism used to implement IoC.

Suppose:

```java
@Service
public class OrderService {

    private final PaymentService paymentService;

    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

Spring provides the dependency.

```text
Spring Container
      │
      ├── PaymentService
      │
      ↓
OrderService
      │
      └── paymentService
```

### Preferred approach

**Constructor injection**

```java
public OrderService(PaymentService paymentService) {
    this.paymentService = paymentService;
}
```

It makes dependencies explicit and supports immutable fields.

---

# 9. Spring Bean

A **Spring Bean** is an object that is instantiated, configured, and managed by the Spring IoC container.

Example:

```java
@Service
public class UserService {
}
```

Spring detects the class and creates a bean when component scanning/configuration allows it.

Conceptually:

```text
Class
 ↓
Component Scan
 ↓
Spring Container
 ↓
Bean
 ↓
Dependency Injection
```

---

# 10. `ApplicationContext`

`ApplicationContext` is a central Spring container interface responsible for managing beans and providing application infrastructure.

Conceptually:

```text
ApplicationContext
       │
       ├── Bean definitions
       ├── Bean instances
       ├── Dependency injection
       ├── Events
       ├── Resources
       └── Configuration
```

Example:

```java
ApplicationContext context =
    SpringApplication.run(Application.class, args);

UserService service =
    context.getBean(UserService.class);
```

In normal application code, however, prefer dependency injection rather than manually calling `getBean()`.

---

# 11. Component Scanning

Spring Boot can scan packages for components such as:

```java
@Component
@Service
@Repository
@Controller
```

Example:

```text
com.example
│
├── Application.java
│
├── controller
│
├── service
│
└── repository
```

If the application class is in:

```text
com.example
```

Spring's component scanning can discover components in appropriate subpackages.

---

# 12. Stereotype Annotations

### `@Component`

Generic Spring-managed component.

```java
@Component
public class EmailClient {
}
```

### `@Service`

Typically used for service/business logic.

```java
@Service
public class OrderService {
}
```

### `@Repository`

Typically used for persistence/data-access components.

```java
@Repository
public class UserRepository {
}
```

### `@Controller`

Used for Spring MVC controllers.

```java
@Controller
public class UserController {
}
```

These are all components in Spring's component model, with semantic distinctions.

---

# 13. `@RestController`

For REST APIs:

```java
@RestController
@RequestMapping("/users")
public class UserController {

    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.findById(id);
    }
}
```

`@RestController` is effectively:

```text
@Controller
+
@ResponseBody
```

The returned object is serialized into the HTTP response body by the configured message converters.

---

# 14. REST Request Flow

A typical request:

```text
GET /users/10
       ↓
Embedded Server
       ↓
DispatcherServlet
       ↓
Handler Mapping
       ↓
Controller
       ↓
Service
       ↓
Repository
       ↓
Database
       ↓
Repository
       ↓
Service
       ↓
Controller
       ↓
HTTP Response
```

### ⭐ Interview question

**What is `DispatcherServlet`?**

> It is Spring MVC's front controller responsible for receiving requests and coordinating request mapping, handler invocation, exception handling, and response processing.

---

# 15. Layered Architecture

A common Spring Boot structure:

```text
Controller
    ↓
Service
    ↓
Repository
    ↓
Database
```

### Controller

Handles HTTP concerns.

### Service

Contains business logic.

### Repository

Handles data persistence.

This separation improves:

* Maintainability
* Testability
* Separation of concerns
* Dependency management

---

# 16. Auto-Configuration

⭐ **Extremely Important**

Auto-configuration attempts to configure Spring components based on:

* Classpath contents
* Existing beans
* Application properties
* Conditional configuration
* Environment

For example, if appropriate web dependencies are present, Spring Boot can configure web infrastructure automatically.

Conceptually:

```text
Dependencies
     +
Configuration
     +
Environment
     ↓
Auto-Configuration
     ↓
Configured Application
```

---

# 17. How Auto-Configuration Works

Suppose you add a relevant starter dependency.

```text
spring-boot-starter-web
```

This brings appropriate dependencies onto the classpath.

Spring Boot detects relevant classes/configuration and applies conditional auto-configuration.

Conceptually:

```text
Classpath
   ↓
Is required class available?
   ↓
Yes
   ↓
Is matching bean already defined?
   ↓
No
   ↓
Create default configuration
```

This is powered heavily by Spring Boot's conditional configuration infrastructure.

---

# 18. `@Conditional`

Spring Boot uses conditional configuration extensively.

Conceptually:

```text
@Configuration
       ↓
@Conditional...
       ↓
Condition satisfied?
    /        \
  Yes         No
   ↓           ↓
Create       Skip
bean         config
```

Examples include conditions based on:

* Class presence
* Bean presence
* Property values
* Resource availability
* Web application type

This is a major reason Spring Boot can configure applications automatically without blindly overriding explicit application configuration.

---

# 19. Spring Boot Starters

A **starter** is a dependency descriptor that brings together a suitable set of dependencies for a particular capability.

Examples:

```text
spring-boot-starter-web
spring-boot-starter-data-jpa
spring-boot-starter-security
spring-boot-starter-validation
```

Instead of manually selecting many related libraries, a starter provides a curated dependency set.

---

# 20. Embedded Server

Spring Boot web applications commonly use an embedded servlet container.

For example:

```text
Application
    ↓
Embedded Tomcat
    ↓
HTTP Server
```

Therefore you can commonly run:

```bash
java -jar application.jar
```

rather than manually deploying the application to a separately installed application server.

Spring Boot also supports alternatives such as Jetty and Undertow depending on configuration and application type.

---

# 21. Configuration

Spring Boot supports externalized configuration.

Example:

```properties
server.port=8081
```

or YAML:

```yaml
server:
  port: 8081
```

Then:

```text
Configuration
     ↓
Environment
     ↓
Spring Boot
     ↓
Application
```

This allows configuration to vary between environments without changing application code.

---

# 22. Profiles

Profiles allow environment-specific configuration.

```text
application.yml

application-dev.yml
application-test.yml
application-prod.yml
```

Example:

```properties
spring.profiles.active=dev
```

Conceptually:

```text
Application
    ↓
Profile
    ↓
dev / test / prod
    ↓
Environment-specific configuration
```

Useful for:

* Database configuration
* Logging
* External services
* Development settings
* Production settings

---

# 23. Spring Data JPA

Spring Boot commonly integrates with Spring Data JPA.

Entity:

```java
@Entity
public class User {

    @Id
    @GeneratedValue
    private Long id;

    private String name;
}
```

Repository:

```java
public interface UserRepository
        extends JpaRepository<User, Long> {
}
```

Then:

```java
userRepository.findById(id);
```

Spring Data can provide repository implementations automatically.

Conceptually:

```text
Controller
   ↓
Service
   ↓
JpaRepository
   ↓
Spring Data
   ↓
JPA / Hibernate
   ↓
Database
```

---

# 24. `@Transactional`

⭐ **Frequently Asked**

`@Transactional` defines a transaction boundary around a method/class according to Spring's transaction infrastructure.

Example:

```java
@Transactional
public void transferMoney(
        Long from,
        Long to,
        BigDecimal amount) {

    debit(from, amount);
    credit(to, amount);
}
```

Conceptually:

```text
Begin Transaction
       ↓
Debit
       ↓
Credit
       ↓
Success?
  /          \
Yes          No
 ↓            ↓
Commit      Rollback
```

But exact rollback behavior depends on transaction configuration and exception types.

---

# 25. Spring Security

A typical security flow:

```text
HTTP Request
     ↓
Security Filter Chain
     ↓
Authentication
     ↓
Authorization
     ↓
Controller
```

For JWT-based APIs:

```text
Client
  ↓
JWT
  ↓
Security Filter
  ↓
Validate Token
  ↓
Authentication Context
  ↓
Authorization
  ↓
Controller
```

Important distinction:

> **Authentication = Who are you?**

> **Authorization = What are you allowed to do?**

---

# 26. Exception Handling

Instead of handling every exception inside every controller:

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
Controller
    ↓
Exception
    ↓
Global Handler
    ↓
HTTP Error Response
```

This centralizes error handling.

---

# 27. Spring Boot Actuator

Actuator provides production-oriented endpoints and instrumentation.

Common uses include:

* Health checks
* Metrics
* Application information
* Monitoring integration

Conceptually:

```text
Application
    ↓
Actuator
    ↓
Health / Metrics / Monitoring
```

This is particularly useful in containerized and cloud environments.

---

# 28. Internal Startup Flow

⭐ **Advanced Interview Question**

When you run:

```java
SpringApplication.run(Application.class, args);
```

conceptually:

```text
main()
  ↓
SpringApplication
  ↓
Environment preparation
  ↓
ApplicationContext creation
  ↓
Configuration discovery
  ↓
Component scanning
  ↓
Auto-configuration
  ↓
Bean definitions
  ↓
Bean instantiation
  ↓
Dependency injection
  ↓
Bean initialization
  ↓
Embedded server startup
  ↓
ApplicationReadyEvent
```

The actual lifecycle is more detailed and involves multiple event listeners, post-processors, and context lifecycle phases.

---

# 29. Bean Lifecycle

A simplified lifecycle:

```text
Bean Definition
      ↓
Instantiate
      ↓
Dependency Injection
      ↓
Aware callbacks / post-processing
      ↓
Initialization callbacks
      ↓
Bean Ready
      ↓
Application Running
      ↓
Destroy
```

Important extension points include:

* `BeanPostProcessor`
* `InitializingBean`
* `@PostConstruct`
* `DisposableBean`
* `@PreDestroy`

---

# 30. Spring Boot vs Traditional Java Application

### Traditional

```text
main()
 ↓
new Service()
 ↓
new Repository()
 ↓
new DatabaseClient()
 ↓
Manually connect everything
```

### Spring Boot

```text
main()
 ↓
Spring Container
 ↓
Create Beans
 ↓
Resolve Dependencies
 ↓
Inject Dependencies
 ↓
Start Application
```

This is the practical impact of IoC/DI.

---

# 31. Production Architecture

A typical production Spring Boot service:

```text
                    Client
                      │
                      ↓
                Load Balancer
                      │
                      ↓
                Spring Boot
                      │
        ┌─────────────┼─────────────┐
        ↓             ↓             ↓
   Controller      Security      Validation
        │
        ↓
     Service
        │
   ┌────┴────┐
   ↓         ↓
Repository  Kafka/Redis
   │
   ↓
PostgreSQL
```

Additional infrastructure may include:

```text
Docker
Kubernetes
Redis
Kafka
Prometheus
Grafana
API Gateway
CI/CD
```

Spring Boot commonly serves as the application layer inside this architecture.

---

# 32. Common Interview Traps

### Is Spring Boot a replacement for Spring?

❌ No.

It builds on the Spring ecosystem and simplifies application configuration/development.

---

### Is `@Autowired` required for dependency injection?

❌ No.

Constructor injection can work without explicitly putting `@Autowired` on a single constructor.

---

### Is every Java object a Spring Bean?

❌ No.

Only objects managed/created through the Spring container are Spring Beans.

---

### Is `@Component` the same as `@Service`?

Functionally they are both component stereotypes, but `@Service` communicates service-layer intent.

---

### Does `@Transactional` work on every method call?

Not necessarily.

Spring commonly implements declarative transactions through proxies/interceptors, so **self-invocation** and proxy boundaries are important considerations.

---

### Does Spring Boot eliminate configuration?

❌ No.

It reduces boilerplate and provides defaults; configuration remains possible and often necessary.

---

# 33. Bad vs Good Design

### ❌ Tight coupling

```java
class OrderService {

    private PaymentService paymentService =
            new PaymentService();
}
```

Problems:

* Harder to test
* Concrete dependency
* Manual lifecycle
* Harder to replace

### ✅ Dependency Injection

```java
@Service
class OrderService {

    private final PaymentService paymentService;

    OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

Now Spring manages the dependency relationship.

---

# 34. Design Principles Connection

Spring Boot heavily relies on:

### Dependency Inversion

```text
High-level service
       ↓
Abstraction
       ↑
Implementation
```

### Single Responsibility

```text
Controller → HTTP
Service    → Business logic
Repository → Persistence
```

### Open/Closed Principle

Interfaces and dependency injection allow implementations to be replaced with less modification to consumers.

### Loose Coupling

```text
OrderService
     ↓
PaymentProcessor
     ↑
 ┌───┴────┐
UPI      Card
```

---

# 35. Interviewer Follow-Up Chain

A strong interviewer can progressively drill down:

```text
What is Spring Boot?
       ↓
Spring vs Spring Boot?
       ↓
Why does Spring Boot exist?
       ↓
What is IoC?
       ↓
What is Dependency Injection?
       ↓
What is a Bean?
       ↓
What is ApplicationContext?
       ↓
How does component scanning work?
       ↓
What is @SpringBootApplication?
       ↓
How does auto-configuration work?
       ↓
What are Spring Boot starters?
       ↓
How does a REST request reach a controller?
       ↓
What is DispatcherServlet?
       ↓
How does Spring create and inject beans?
       ↓
Explain Bean lifecycle.
       ↓
How does @Transactional work?
       ↓
What is proxy-based AOP?
       ↓
Why does self-invocation affect @Transactional?
       ↓
How does Spring Security process a request?
       ↓
How would you design a production Spring Boot service?
```

---

# 36. Common Candidate Mistakes

### ❌ Weak

> Spring Boot is used to create Java web applications easily.

### Better

> Spring Boot is an opinionated layer over the Spring ecosystem that simplifies application setup through auto-configuration, starters, embedded servers, externalized configuration, and production-oriented features.

---

### ❌ Weak

> IoC and DI are the same thing.

### Better

> IoC is the broader principle of transferring control of object creation and lifecycle to a container. Dependency Injection is one mechanism through which dependencies are supplied to objects.

---

### ❌ Weak

> `@Autowired` creates the object.

### Better

> Spring's IoC container manages bean creation and dependency resolution. `@Autowired` is one way of expressing a dependency injection point; constructor injection can also be used without the annotation for a single constructor.

---

# 37. 30-Second Revision

```text
Spring Boot
     ↓
Simplifies Spring
     ↓
┌─────────────────────┐
│ IoC / DI             │
│ Auto-Configuration   │
│ Starters             │
│ Embedded Server      │
│ External Config      │
│ Production Features  │
└─────────────────────┘
```

### Core architecture

```text
Request
   ↓
Controller
   ↓
Service
   ↓
Repository
   ↓
Database
```

### Core concepts

```text
IoC
→ Container controls object lifecycle

DI
→ Dependencies are supplied to objects

Bean
→ Spring-managed object

ApplicationContext
→ Spring IoC container

Auto-Configuration
→ Conditional configuration based on environment/classpath

Starter
→ Curated dependency set

@Transactional
→ Declarative transaction boundary
```

---

# 38. Master Interview Test

Answer without looking back:

1. What is Spring Boot and why was it introduced?
2. Spring vs Spring Boot?
3. What is IoC?
4. What is Dependency Injection and how is it different from IoC?
5. What is a Spring Bean?
6. What does `@SpringBootApplication` contain?
7. **How does Spring Boot auto-configuration determine what to configure?**
8. **Explain the complete lifecycle of a Spring Boot application from `main()` to a running REST API.**
9. **How does a request travel from the client through `DispatcherServlet`, Controller, Service, Repository, and Database and back?**
10. **Explain how Spring's proxy-based architecture affects `@Transactional`, self-invocation, and transaction boundaries in a production application.**
