# Project Lombok — Complete Guide

> **Project Lombok** is a Java library that reduces boilerplate code by generating common Java methods and constructs automatically through annotations.


# 1. What is Lombok?

**Project Lombok** is a Java library that uses annotations to automatically generate repetitive code during compilation.

Without Lombok:

```java
public class User {

    private Long id;
    private String name;

    public User() {
    }

    public User(Long id, String name) {
        this.id = id;
        this.name = name;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

With Lombok:

```java
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class User {

    private Long id;
    private String name;
}
```

Lombok removes repetitive boilerplate while keeping the source code concise.


# 2. Why Lombok?

Traditional Java classes often contain large amounts of repetitive code:

```text
Fields
  │
  ├── Getters
  ├── Setters
  ├── Constructors
  ├── toString()
  ├── equals()
  └── hashCode()
```

Lombok lets you express the intent:

```java
@Getter
@Setter
public class User {
    private Long id;
    private String name;
}
```

### Benefits

* Less boilerplate
* More readable domain classes
* Faster development
* Consistent implementations
* Useful with Spring Boot
* Builder pattern becomes easy
* Logging setup becomes simple


# 3. How Lombok Works

Lombok works primarily through **annotation processing during compilation**.

```text
                 Java Source
                     │
                     ▼
              Lombok Annotation
                     │
                     ▼
            Annotation Processing
                     │
                     ▼
             Generated Members
                     │
                     ▼
                  Compiler
                     │
                     ▼
                  .class
```

For example:

```java
@Getter
public class User {
    private String name;
}
```

Conceptually becomes:

```java
public class User {

    private String name;

    public String getName() {
        return name;
    }
}
```

The generated method doesn't normally appear as source code in your `.java` file; Lombok modifies the compilation model so the compiler sees the generated members.


# 4. Setup

## Maven

Add Lombok as a dependency:

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency>
```

For Spring Boot projects, the Spring Boot dependency management can manage the Lombok version.

## Gradle

```gradle
dependencies {
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
}
```

Your IDE may also need Lombok/annotation-processing support.


# 5. Core Annotations

| Annotation                 | Purpose                                                                 |
| -------------------------- | ----------------------------------------------------------------------- |
| `@Getter`                  | Generates getters                                                       |
| `@Setter`                  | Generates setters                                                       |
| `@ToString`                | Generates `toString()`                                                  |
| `@EqualsAndHashCode`       | Generates `equals()` and `hashCode()`                                   |
| `@NoArgsConstructor`       | No-argument constructor                                                 |
| `@RequiredArgsConstructor` | Constructor for required fields                                         |
| `@AllArgsConstructor`      | Constructor for all fields                                              |
| `@Data`                    | Combines several common annotations                                     |
| `@Value`                   | Immutable value object                                                  |
| `@Builder`                 | Builder pattern                                                         |
| `@Slf4j`                   | Adds SLF4J logger                                                       |
| `@NonNull`                 | Generates null checks                                                   |
| `@Cleanup`                 | Automatic resource cleanup                                              |
| `@SneakyThrows`            | Allows checked exceptions to be propagated without explicit declaration |
| `@With`                    | Creates modified copies                                                 |
| `@Accessors`               | Customizes getter/setter style                                          |


# 6. `@Getter` and `@Setter`

### `@Getter`

```java
@Getter
public class User {

    private Long id;
    private String name;
}
```

Conceptually:

```java
public Long getId() {
    return id;
}

public String getName() {
    return name;
}
```

### `@Setter`

```java
@Setter
public class User {

    private Long id;
    private String name;
}
```

Conceptually:

```java
public void setId(Long id) {
    this.id = id;
}

public void setName(String name) {
    this.name = name;
}
```

### Field-level usage

```java
@Getter
@Setter
public class User {

    private Long id;

    @Setter(AccessLevel.NONE)
    private String userCode;
}
```


# 7. Constructors

## `@NoArgsConstructor`

```java
@NoArgsConstructor
public class User {
    private Long id;
    private String name;
}
```

Generates:

```java
public User() {
}
```


## `@AllArgsConstructor`

```java
@AllArgsConstructor
public class User {
    private Long id;
    private String name;
}
```

Generates:

```java
public User(Long id, String name) {
    this.id = id;
    this.name = name;
}
```


## `@RequiredArgsConstructor`

Generates a constructor for required fields, primarily:

* `final` fields
* fields annotated with `@NonNull`

Example:

```java
@RequiredArgsConstructor
public class UserService {

    private final UserRepository repository;
}
```

Conceptually:

```java
public UserService(UserRepository repository) {
    this.repository = repository;
}
```

This is particularly useful with **Spring constructor injection**.


# 8. `@ToString`

```java
@ToString
public class User {

    private Long id;
    private String name;
}
```

Generates a `toString()` implementation.

Example:

```text
User(id=101, name=John)
```

### Exclude a field

```java
@ToString
public class User {

    private Long id;

    @ToString.Exclude
    private String password;
}
```

This is important for sensitive data.


# 9. `@EqualsAndHashCode`

```java
@EqualsAndHashCode
public class User {

    private Long id;
    private String name;
}
```

Generates:

```java
equals()
hashCode()
```

### Exclude fields

```java
@EqualsAndHashCode
public class User {

    private Long id;

    @EqualsAndHashCode.Exclude
    private String temporaryValue;
}
```

### Important

Be careful when using `@EqualsAndHashCode` with JPA entities.

Entity equality requires careful consideration of identifiers, proxies, and lifecycle state.


# 10. `@Data`

`@Data` is one of Lombok's most commonly used annotations.

```java
@Data
public class User {

    private Long id;
    private String name;
}
```

Conceptually, `@Data` combines:

```text
@Data
 │
 ├── @Getter
 ├── @Setter
 ├── @RequiredArgsConstructor
 ├── @ToString
 └── @EqualsAndHashCode
```

It is convenient for simple DTO/model classes.

### Avoid blindly using `@Data`

For JPA entities, `@Data` can cause problems because automatically generated:

* `equals()`
* `hashCode()`
* `toString()`

may interact badly with relationships, lazy loading, and entity identity.


# 11. `@Value`

`@Value` is designed for immutable value objects.

```java
@Value
public class Address {

    String city;
    String country;
}
```

Conceptually:

```text
@Value
  │
  ├── final class
  ├── final fields
  ├── getters
  ├── all-args constructor
  ├── equals()
  ├── hashCode()
  └── toString()
```

Unlike `@Data`, setters are not generated.


# 12. `@Builder`

`@Builder` implements the **Builder Pattern**.

Without Lombok:

```java
User user = User.builder()
        .id(101L)
        .name("John")
        .email("john@example.com")
        .build();
```

With:

```java
@Builder
public class User {

    private Long id;
    private String name;
    private String email;
}
```

Usage:

```java
User user = User.builder()
        .id(101L)
        .name("John")
        .email("john@example.com")
        .build();
```

### Why Builder?

Instead of:

```java
new User(
    101L,
    "John",
    "john@example.com",
    "India",
    true
);
```

you can write:

```java
User.builder()
    .id(101L)
    .name("John")
    .email("john@example.com")
    .country("India")
    .active(true)
    .build();
```

This improves readability when there are many parameters.


# 13. `@Slf4j`

`@Slf4j` automatically creates an SLF4J logger.

Without Lombok:

```java
private static final Logger log =
        LoggerFactory.getLogger(UserService.class);
```

With Lombok:

```java
@Slf4j
@Service
public class UserService {

    public void process() {
        log.info("Processing user");
    }
}
```

Conceptually Lombok provides:

```java
private static final Logger log =
        LoggerFactory.getLogger(UserService.class);
```


# 14. `@NonNull`

```java
public void createUser(@NonNull String name) {
    // ...
}
```

Lombok generates a null check.

Conceptually:

```java
if (name == null) {
    throw new NullPointerException("name is marked non-null but is null");
}
```

Important:

> Lombok's `@NonNull` is not the same thing as Jakarta Bean Validation's `@NotNull`.

For API validation, you commonly see:

```java
@NotNull
private String name;
```

while Lombok:

```java
@NonNull
private String name;
```

serves a different purpose.


# 15. `@Cleanup`

`@Cleanup` helps automatically close resources.

Example:

```java
@Cleanup
InputStream input = new FileInputStream("data.txt");
```

Lombok generates cleanup logic similar in intent to:

```java
try {
    // use resource
} finally {
    input.close();
}
```

For modern Java, prefer **try-with-resources** where possible because it is standard Java syntax and clearer.


# 16. `@SneakyThrows`

Normally:

```java
public void readFile() throws IOException {
    // ...
}
```

With Lombok:

```java
@SneakyThrows
public void readFile() {
    // operation that throws IOException
}
```

Lombok allows checked exceptions to escape without explicitly declaring them.

### Caution

Use this sparingly.

It can hide checked exceptions from readers of the method signature and may make exception handling less obvious.


# 17. `@With`

`@With` creates a new object with one field changed.

```java
@Value
@With
public class User {

    Long id;
    String name;
}
```

Usage:

```java
User user1 = new User(1L, "John");

User user2 = user1.withName("Mary");
```

Conceptually:

```text
user1
 │
 │ withName("Mary")
 ▼
user2
```

The original object remains unchanged.


# 18. `@Accessors`

`@Accessors` customizes accessor methods.

Example:

```java
@Getter
@Setter
@Accessors(chain = true)
public class User {

    private String name;
    private String email;
}
```

Allows:

```java
User user = new User()
        .setName("John")
        .setEmail("john@example.com");
```

Instead of:

```java
user.setName("John");
user.setEmail("john@example.com");
```


# 19. Lombok with Spring Boot

Lombok is frequently used with Spring Boot.

Example:

```java
@Service
@RequiredArgsConstructor
@Slf4j
public class UserService {

    private final UserRepository repository;

    public User findUser(Long id) {

        log.info("Finding user: {}", id);

        return repository.findById(id)
                .orElseThrow();
    }
}
```

Here:

```text
@RequiredArgsConstructor
        │
        ▼
Constructor generated
        │
        ▼
Spring injects UserRepository
```

This avoids field injection:

```java
@Autowired
private UserRepository repository;
```

and encourages constructor injection.


# 20. Lombok with DTOs

Lombok works particularly well with DTOs.

```java
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class UserResponse {

    private Long id;
    private String name;
    private String email;
}
```

Usage:

```java
UserResponse response = UserResponse.builder()
        .id(101L)
        .name("John")
        .email("john@example.com")
        .build();
```


# 21. Lombok with JPA Entities

Be careful with Lombok and JPA.

A common pattern is:

```java
@Getter
@Setter
@NoArgsConstructor
@Entity
public class User {

    @Id
    @GeneratedValue
    private Long id;

    private String name;
}
```

Avoid blindly doing:

```java
@Data
@Entity
public class User {
    ...
}
```

because generated `equals()`, `hashCode()`, and `toString()` can cause problems with:

* Entity identity
* Lazy relationships
* Bidirectional relationships
* Recursive `toString()`
* Proxy behavior

For entities, explicitly decide what should participate in:

```text
equals()
hashCode()
toString()
```


# 22. Lombok Pros and Cons

## Advantages

```text
Lombok
  │
  ├── Less boilerplate
  ├── Cleaner classes
  ├── Faster development
  ├── Easy Builder pattern
  ├── Easy logging
  └── Constructor generation
```

## Disadvantages

```text
Lombok
  │
  ├── Generated code isn't visible directly
  ├── IDE/compiler integration required
  ├── Can hide behavior
  ├── Can cause JPA pitfalls
  └── Overuse can reduce explicitness
```


# 23. Lombok vs Manual Java

### Without Lombok

```java
public class User {

    private Long id;
    private String name;

    public User() {
    }

    public User(Long id, String name) {
        this.id = id;
        this.name = name;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    @Override
    public String toString() {
        return "User{id=" + id + ", name='" + name + "'}";
    }
}
```

### With Lombok

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {

    private Long id;
    private String name;
}
```


# 24. Important Lombok Combinations

### DTO

```java
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class UserDTO {
    private Long id;
    private String name;
}
```

### Service

```java
@Service
@RequiredArgsConstructor
@Slf4j
public class UserService {

    private final UserRepository repository;
}
```

### Immutable Object

```java
@Value
@Builder
public class UserConfig {

    String name;
    String environment;
}
```

### Entity

```java
@Entity
@Getter
@Setter
@NoArgsConstructor
public class User {

    @Id
    private Long id;

    private String name;
}
```


# 25. Lombok Cheat Sheet ⭐

```text
╔══════════════════════════════════════════════╗
║              LOMBOK CHEAT SHEET             ║
╠══════════════════════════════════════════════╣
║                                              ║
║ @Getter              → Getters               ║
║ @Setter              → Setters               ║
║ @ToString            → toString()            ║
║ @EqualsAndHashCode   → equals/hashCode       ║
║                                              ║
║ @NoArgsConstructor   → ()                     ║
║ @RequiredArgsConstructor → Required fields   ║
║ @AllArgsConstructor  → All fields            ║
║                                              ║
║ @Data                → Common boilerplate   ║
║ @Value               → Immutable object      ║
║ @Builder             → Builder pattern       ║
║                                              ║
║ @Slf4j               → Logger                ║
║ @NonNull             → Null check            ║
║ @Cleanup             → Resource cleanup      ║
║ @SneakyThrows        → Hide checked throws   ║
║ @With                → Immutable copy        ║
║ @Accessors           → Custom accessors      ║
║                                              ║
╚══════════════════════════════════════════════╝
```


# 27. Recommended Usage

For a typical Spring Boot application:

```text
                    Lombok
                      │
       ┌──────────────┼──────────────┐
       │              │              │
      DTO           Service         Entity
       │              │              │
   @Data          @RequiredArgs    @Getter
   @Builder       Constructor      @Setter
   @NoArgs...     @Slf4j          @NoArgs...
       │              │              │
       ▼              ▼              ▼
  Clean DTO     Clean Service    Controlled Entity
```

### Practical rule

> **Use Lombok to remove repetitive code, but don't use annotations blindly. Understand the code Lombok generates—especially for `@Data`, `@EqualsAndHashCode`, `@ToString`, and JPA entities.**
