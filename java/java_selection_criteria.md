# Java

Java is a statically typed, object-oriented, memory-safe language that runs on the Java Virtual Machine (JVM). It is most widely chosen for long-running enterprise systems, large-team backend services, Android development, and high-throughput transactional applications.

```text
Java source code
    |
    v
javac compiler
    |
    v
Java bytecode (.class / JAR)
    |
    v
JVM
    |
    v
optimized machine code
    |
    v
CPU
```

Java’s core value is not that it is the fastest language available. Its value is the combination of:

- Mature ecosystem
- Strong backward compatibility
- Excellent tooling
- Stable long-running server behavior
- Large talent pool
- Proven enterprise frameworks
- Strong concurrency and observability support

---

## 1. Quick Overview

| Area | Java |
|---|---|
| Created | 1995 |
| Paradigm | Object-oriented, imperative, functional features since Java 8 |
| Typing | Static, strong |
| Execution | Compiled to bytecode, executed by JVM using interpreter + JIT |
| Memory management | Automatic garbage collection |
| Concurrency | Threads, virtual threads, executors, CompletableFuture |
| Primary domain | Enterprise backend, Android, big data, financial systems |
| Major frameworks | Spring Boot, Jakarta EE, Quarkus, Micronaut, Hibernate |
| Build tools | Maven, Gradle |
| Package format | JAR, WAR, native image |
| Deployment | JVM process, container, Kubernetes, serverless with caveats |

Java is designed around the idea of **Write Once, Run Anywhere (WORA)**:

```text
Write Java once
    |
    v
Compile to bytecode
    |
    v
Run on any OS that has a compatible JVM
```

The Java source code is portable, but the JVM implementation itself is platform-specific.

---

## 2. JDK, JRE, and JVM

These three terms are often confused.

```text
JDK
  |-- Development tools: javac, javadoc, jdb, jar
  |-- Compiler: javac
  |-- JRE
        |-- JVM
        |-- Core class libraries
```

### 2.1 JVM: Java Virtual Machine

The **JVM** is the runtime engine that executes Java bytecode.

```text
.class bytecode
    |
    v
JVM
    |-- Class loader
    |-- Bytecode verifier
    |-- Interpreter
    |-- JIT compiler
    |-- Garbage collector
    |-- Runtime memory areas
    v
machine code executed by CPU
```

The JVM is an abstraction over the operating system and hardware. It provides:

- Bytecode execution
- Memory management
- Garbage collection
- Thread scheduling
- Runtime optimization
- Security and bytecode verification
- Platform abstraction

Important point:

```text
Java bytecode is platform independent.
JVM implementations are platform dependent.
```

You need a Windows JVM to run Java on Windows, a Linux JVM to run Java on Linux, and a macOS JVM to run Java on macOS. But the same compiled `.class` file or JAR can usually run on all of them.

### 2.2 JRE: Java Runtime Environment

The **JRE** contains everything needed to **run** Java applications:

- JVM
- Core Java class libraries
- Supporting runtime files

```text
JRE = JVM + core libraries
```

If you only need to run an existing Java application, you need a JRE.

You do **not** need a JRE to compile Java code.

### 2.3 JDK: Java Development Kit

The **JDK** contains everything needed to **develop** Java applications:

- JRE
- `javac`: Java compiler
- `java`: application launcher
- `jar`: archive tool
- `javadoc`: documentation generator
- `jdb`: debugger
- Other development and diagnostic tools

```text
JDK = JRE + development tools
```

Modern Java distributions often blur the JRE/JDK separation because modular runtimes can be built with `jlink`, but conceptually:

| Component | Purpose | Contains JVM? | Can compile code? | Can run code? |
|---|---|---:|---:|---:|
| JVM | Executes bytecode | Yes | No | Yes |
| JRE | Runs Java programs | Yes | No | Yes |
| JDK | Develops Java programs | Yes | Yes | Yes |

---

## 3. How a Java Program Runs

### Step 1: Write source code

```java
public class HelloJava {
    public static void main(String[] args) {
        System.out.println("Hello, Java");
    }
}
```

The file must be named:

```text
HelloJava.java
```

### Step 2: Compile to bytecode

```bash
javac HelloJava.java
```

This produces:

```text
HelloJava.class
```

`javac` does not produce Windows or Linux machine code directly. It produces portable JVM bytecode.

### Step 3: JVM loads and verifies bytecode

```bash
java HelloJava
```

The JVM:

1. Loads `HelloJava.class`
2. Verifies bytecode safety
3. Allocates runtime memory
4. Initializes classes
5. Finds `main(String[] args)`
6. Begins execution

### Step 4: Interpretation and JIT compilation

At startup, the JVM may interpret bytecode. As methods run repeatedly, the JIT compiler identifies “hot” code and compiles it into optimized native machine code.

```text
Cold code
    |
    v
interpreted bytecode
    |
    v
method becomes hot
    |
    v
JIT compiles it
    |
    v
optimized native code
```

This is why a Java service can have slower initial performance but excellent steady-state throughput after warm-up.

### Step 5: CPU executes machine code

```text
JIT-compiled native code
    |
    v
CPU
    |
    v
output
```

---

## 4. Execution Model

Java is best understood as:

```text
Ahead-of-time compilation to bytecode
    +
Runtime interpretation
    +
Just-in-time native compilation
```

It is not purely compiled like Go or Rust, and not purely interpreted like early JavaScript or simple Python scripts.

### 4.1 Bytecode

Bytecode is an intermediate instruction set for the JVM.

```text
Java source
    |
    v
portable bytecode
    |
    v
platform-specific JVM
```

This gives Java portability without requiring the source code to be shipped.

### 4.2 JIT compilation

The JIT compiler improves performance by compiling frequently used bytecode into native code.

Benefits:

- Excellent long-running throughput
- Runtime-specific optimizations
- Inlining, escape analysis, loop optimizations
- Adaptive optimization based on real workload behavior

Trade-offs:

- Startup warm-up
- Higher initial memory use
- Less predictable latency during compilation
- More complex profiling and tuning

### 4.3 AOT and GraalVM native image

Java can also be ahead-of-time compiled using GraalVM Native Image.

```text
Java source
    |
    v
GraalVM native image
    |
    v
native executable
```

Benefits:

- Much faster startup
- Lower memory use
- Better fit for serverless and short-lived workloads

Trade-offs:

- Longer build time
- Some reflection and dynamic-proxy usage needs configuration
- Less mature ecosystem compatibility than normal JVM mode
- Loses some JIT-based adaptive optimization

---

## 5. Memory Management

Java uses automatic memory management through a garbage collector.

### 5.1 Memory areas

```text
JVM memory
  |-- Heap
  |     |-- Young generation
  |     |-- Old generation
  |-- Metaspace
  |-- Thread stacks
  |-- Native memory
```

Most Java objects live on the **heap**.

```java
User user = new User("Safal");
```

Here:

- `user` is a reference stored on the stack or inside another object.
- The actual `User` object is allocated on the heap.

### 5.2 Garbage collection

The garbage collector automatically removes objects that are no longer reachable.

```text
object created
    |
    v
object referenced
    |
    v
references removed
    |
    v
object unreachable
    |
    v
GC reclaims memory
```

Example:

```java
void process() {
    User user = new User("Safal");
    // user is reachable

    user = null;
    // object may now become unreachable
}
```

After the method ends, or after `user` no longer references the object, the object becomes eligible for garbage collection.

### 5.3 GC benefits

- Developers do not manually free memory
- Prevents many use-after-free and dangling-pointer bugs
- Makes large application development safer
- Mature GCs are highly optimized for server workloads

### 5.4 GC trade-offs

- GC consumes CPU
- Can introduce pause times
- Memory use is usually higher than Rust, C++, or Go
- Latency-sensitive systems need GC tuning

Common collectors:

| Collector | Best suited for |
|---|---|
| Serial GC | Small single-core environments |
| Parallel GC | Throughput-oriented batch workloads |
| G1 GC | Balanced general-purpose server workloads |
| ZGC | Very large heaps and low-pause requirements |
| Shenandoah | Low-pause, concurrent evacuation |

For most modern Spring Boot services, G1 is a strong default. ZGC or Shenandoah may be considered for latency-sensitive services with large heaps.

### 5.5 Common Java memory problems

Java removes manual memory freeing, but it does not remove all memory problems.

| Problem | Cause |
|---|---|
| Memory leak | Objects remain referenced but are no longer needed |
| High GC pressure | Too many short-lived objects |
| Long GC pauses | Large heap, poor GC choice, or allocation spikes |
| OutOfMemoryError | Heap exhausted or memory leak |
| StackOverflowError | Deep or infinite recursion |
| Native memory leak | Off-heap resources not released |

Example leak:

```java
List<byte[]> cache = new ArrayList<>();

void handleRequest() {
    cache.add(new byte[1024 * 1024]);
}
```

The objects are still reachable through `cache`, so GC cannot reclaim them.

---

## 6. Type System

Java is statically and strongly typed.

```java
int age = 25;
String name = "Safal";
List<String> users = new ArrayList<>();
```

The compiler knows:

- `age` is an `int`
- `name` is a `String`
- `users` contains `String` values

### 6.1 Static typing

Type errors are caught before runtime.

```java
int age = "twenty"; // compile-time error
```

This makes refactoring safer, especially in large enterprise codebases.

### 6.2 Strong typing

Java does not silently convert unrelated types in most cases.

```java
String number = "10";
int value = number; // compile-time error
```

You must convert explicitly:

```java
int value = Integer.parseInt(number);
```

### 6.3 Generics

Generics provide compile-time type safety for collections and APIs.

```java
List<String> names = new ArrayList<>();
names.add("Safal");
String first = names.get(0);
```

Without generics, older Java code required casts:

```java
List names = new ArrayList();
names.add("Safal");
String first = (String) names.get(0);
```

### 6.4 Null safety

Java has historically suffered from `NullPointerException`.

```java
String name = user.getName();
int length = name.length(); // NPE if name is null
```

Modern Java provides tools such as:

```java
Optional<String> name = Optional.ofNullable(user.getName());
int length = name.map(String::length).orElse(0);
```

But Java’s null safety is opt-in and less enforced than Kotlin or Rust.

---

## 7. Object-Oriented Programming

Java is strongly object-oriented.

```java
public class Order {
    private final String id;
    private final BigDecimal amount;

    public Order(String id, BigDecimal amount) {
        this.id = id;
        this.amount = amount;
    }

    public String getId() {
        return id;
    }

    public BigDecimal getAmount() {
        return amount;
    }
}
```

Core OOP concepts:

| Concept | Meaning | Java feature |
|---|---|---|
| Encapsulation | Hide internal state | Private fields, getters/setters |
| Inheritance | Reuse behavior | `extends` |
| Polymorphism | Same interface, different behavior | Interfaces, overriding |
| Abstraction | Hide implementation details | Abstract classes, interfaces |

Modern Java also supports functional programming:

```java
List<String> names = users.stream()
    .map(User::getName)
    .filter(name -> name.startsWith("S"))
    .toList();
```

And records for immutable data carriers:

```java
public record User(String id, String name, String email) {}
```

---

## 8. Concurrency Model

Java has one of the most mature concurrency ecosystems.

### 8.1 Threads

A Java thread is an OS-level execution path.

```text
JVM process
  |-- Platform thread 1
  |-- Platform thread 2
  |-- Platform thread 3
```

```java
Thread thread = new Thread(() -> {
    System.out.println("Running in another thread");
});

thread.start();
thread.join();
```

Platform threads are powerful but relatively expensive. Creating thousands or millions of them can exhaust memory and OS resources.

### 8.2 ExecutorService

Instead of manually creating threads, production Java usually uses thread pools.

```java
ExecutorService executor = Executors.newFixedThreadPool(20);

executor.submit(() -> {
    return callDatabase();
});
```

Benefits:

- Reuse threads
- Control concurrency
- Queue work
- Manage shutdown cleanly

### 8.3 CompletableFuture

`CompletableFuture` supports asynchronous composition.

```java
CompletableFuture<User> userFuture =
    CompletableFuture.supplyAsync(() -> fetchUser(id));

CompletableFuture<Order> orderFuture =
    CompletableFuture.supplyAsync(() -> fetchOrder(id));

CompletableFuture.combine(userFuture, orderFuture, (user, order) ->
    buildResponse(user, order)
);
```

This is useful when multiple independent I/O calls can run in parallel.

### 8.4 Virtual threads

Since Java 21, virtual threads provide lightweight concurrency.

```text
Traditional platform threads
    |-- limited number
    |-- mapped to OS threads

Virtual threads
    |-- very large number possible
    |-- managed and scheduled by JVM
```

```java
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    executor.submit(() -> {
        return callExternalApi();
    });
}
```

Virtual threads are especially useful for I/O-heavy services:

- REST APIs
- Database-heavy services
- Microservices calling other services
- High-concurrency request handling

They do not make CPU-bound computation faster. They help when threads spend much of their time waiting.

### 8.5 Shared state and race conditions

Java threads can share memory, which creates data-race risk.

```java
class Counter {
    private int count = 0;

    void increment() {
        count++; // not atomic
    }
}
```

Two threads may read the same value and overwrite each other’s update.

Safe approaches:

```java
AtomicInteger count = new AtomicInteger();

void increment() {
    count.incrementAndGet();
}
```

Or use locks:

```java
class Counter {
    private int count = 0;

    synchronized void increment() {
        count++;
    }
}
```

Or prefer immutable data and message passing where possible.

---

## 9. Performance Characteristics

### 9.1 Startup

Normal JVM applications have slower startup than Go or Rust.

```text
Go / Rust binary
    |
    v
almost immediate execution

Java JVM application
    |
    v
JVM startup
    |
    v
class loading
    |
    v
JIT warm-up
    |
    v
peak performance
```

This matters for:

- Serverless functions
- Autoscaling from zero
- CLI tools
- Short-lived jobs

It matters less for long-running enterprise services.

### 9.2 Steady-state throughput

After warm-up, Java can achieve very high throughput because JIT can optimize based on actual runtime behavior.

```text
Cold start: slower
Warm-up period: improving
Steady state: very fast
```

This is why Java remains strong for high-traffic backend systems that stay running for weeks or months.

### 9.3 Memory use

Java typically uses more memory than Go or Rust because of:

- JVM runtime overhead
- Garbage collector overhead
- Object headers
- Larger default heaps
- More abstraction layers

A small Spring Boot service may comfortably use hundreds of MB of RAM, while an equivalent Go service may use far less.

### 9.4 Latency

Java can deliver excellent latency after warm-up, but GC pauses and JIT compilation can create tail-latency spikes.

For strict p99/p999 latency systems, teams may choose:

- ZGC or Shenandoah
- Careful heap sizing
- Allocation reduction
- Avoiding stop-the-world-heavy workloads
- Native image in selected cases
- Rust/C++ for the most extreme requirements

---

## 10. Ecosystem and Frameworks

Java’s ecosystem is one of its biggest advantages.

### 10.1 Spring Boot

Spring Boot is the dominant Java framework for backend services.

```text
Spring Boot
  |-- Dependency injection
  |-- REST APIs
  |-- Security
  |-- Data access
  |-- Transactions
  |-- Observability
  |-- Configuration
  |-- Testing support
```

Example REST controller:

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    private final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.getUser(id);
    }
}
```

Spring Boot is especially strong for:

- Enterprise REST APIs
- Microservices
- Internal platforms
- Banking and insurance systems
- Large teams with established Java skills

### 10.2 Hibernate / JPA

Hibernate and Jakarta Persistence API handle object-relational mapping.

```java
@Entity
public class User {

    @Id
    private Long id;

    private String email;
}
```

Benefits:

- Less manual SQL for common CRUD
- Portable persistence layer
- Transaction management

Trade-offs:

- Hidden SQL can cause performance issues
- N+1 query problems
- Complex mappings can become difficult to debug

### 10.3 Jakarta EE

Jakarta EE is the enterprise Java specification ecosystem.

It includes specifications for:

- Servlets
- RESTful web services
- Persistence
- Transactions
- Dependency injection
- Security

Spring Boot is more common in modern application development, while Jakarta EE remains important in many established enterprise environments.

### 10.4 Quarkus and Micronaut

Modern JVM frameworks focus on:

- Faster startup
- Lower memory use
- Native-image support
- Cloud-native deployment

They are useful when Java’s traditional startup cost is a problem.

---

## 11. Major Industry Usage

| Industry / domain | Why Java is used |
|---|---|
| Banking and finance | Transactions, security, mature ecosystem, auditability |
| Insurance | Complex domain rules, long-lived systems, integration |
| E-commerce | High-throughput backend services, mature tooling |
| Telecommunications | Large-scale backend systems |
| Healthcare | Enterprise integrations, compliance-oriented ecosystems |
| Android | Official/native Android development ecosystem |
| Big data | Hadoop, Kafka clients, Spark, Flink, Elasticsearch |
| Enterprise SaaS | Large teams, maintainability, mature frameworks |

Java is especially dominant where systems must run reliably for many years and evolve across many teams.

---

## 12. Where Java Excels

Java excels when you need:

- Long-running backend services
- Large enterprise codebases
- Strong transactional support
- Mature ORM and database ecosystem
- Excellent observability and profiling tools
- Large teams working on the same system
- Stable APIs and backward compatibility
- High steady-state throughput
- Rich enterprise integrations

A typical strong Java use case:

```text
Banking payment service
    |
    v
Spring Boot API
    |
    v
PostgreSQL
    |
    v
Kafka events
    |
    v
Downstream services
```

Java is well suited because the service is long-running, transactional, integration-heavy, and maintained by a large team.

---

## 13. Where Java Struggles

Java is less ideal when you need:

- Extremely fast startup
- Minimal memory footprint
- Small CLI binaries
- Low-level hardware control
- Predictable no-GC latency
- Rapid data-science experimentation
- Browser-side execution
- Small, simple scripts

For example:

| Requirement | Better fit |
|---|---|
| Tiny cloud CLI binary | Go or Rust |
| Data analysis notebook | Python |
| Browser UI | TypeScript / JavaScript |
| OS kernel or driver | C / Rust |
| Game engine | C++ |
| Ultra-low-latency trading system | C++ or carefully tuned Java/Rust |
| Serverless cold-start-sensitive function | Go, Rust, Node.js, or Java native image |

---

## 14. Java vs Related Languages

| Criteria | Java | Go | Rust | Python | JavaScript/TypeScript |
|---|---|---|---|---|---|
| Execution | Bytecode + JIT | AOT native binary | AOT native binary | Interpreted / JIT variants | JIT in V8-like engines |
| Startup | Slower | Very fast | Very fast | Fast to start, slower execution | Fast |
| Memory management | GC | GC | Ownership / borrowing | GC | GC |
| Raw CPU performance | High after warm-up | High | Highest practical safety-performance tier | Lower for pure Python | Moderate |
| Concurrency | Threads, virtual threads | Goroutines | Async / threads / ownership | asyncio, multiprocessing | Event loop |
| Learning curve | Moderate | Low to moderate | High | Low | Low to moderate |
| Enterprise ecosystem | Excellent | Good | Growing | Excellent for AI/data | Excellent for web |
| Best domain | Enterprise backend | Cloud services | Systems / performance-critical software | AI, data, scripting | Web frontend/full-stack |

---

## 15. When to Choose Java

Choose Java when most of these are true:

- You are building a long-running backend service.
- The team already knows Java or the organization has JVM expertise.
- You need mature frameworks, libraries, and enterprise integrations.
- You need strong transactional and database support.
- You expect the system to live for years.
- Multiple teams will maintain the codebase.
- Steady-state throughput matters more than instant cold start.
- You need mature observability, profiling, and operational tooling.

## 16. When Not to Choose Java

Avoid Java as the default when:

- You need the smallest possible container or binary.
- Cold-start latency is the primary constraint.
- You are building a simple automation script.
- You need direct hardware access.
- You are doing exploratory ML/data work.
- You need a browser frontend.
- The team has no JVM experience and the service is small.

---

## 17. Mental Model

```text
Choose Java when you need
a mature, stable, long-running
enterprise backend platform.

It trades some startup speed,
memory efficiency, and simplicity
for ecosystem maturity,
operational tooling,
and large-scale maintainability.
```

Java is not outdated. It remains one of the strongest choices for serious backend and enterprise systems—but it should be chosen because the workload benefits from the JVM ecosystem, not simply because “Java is used in enterprises.”
