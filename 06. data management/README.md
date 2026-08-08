# 7. Spring Data JPA

## 7.1 Overview

**Spring Data JPA** is part of the larger Spring Data family. It simplifies the implementation of data access layers by:
- Eliminating boilerplate DAO code
- Providing powerful abstractions for CRUD operations
- Supporting custom queries with JPQL or native SQL

---

## 7.2 How Spring Data JPA and Hibernate are related?

### JPA (Java Persistence API)
JPA is like **international car standards** (what every car must have: steering, brakes, gears).

👉 It’s just a **rulebook** — you can't drive it!

JPA is a **specification** (not an implementation) that defines a standard way to manage relational data in Java applications.
Think of it as a contract or set of rules that describes how Java objects should be mapped to database tables.

### Hibernate
Hibernate is like **Toyota** — an actual car manufacturer that builds real cars following those standards, plus adds extra features.

Hibernate is a **JPA implementation** — the actual ORM (Object-Relational Mapping) framework that does the heavy lifting.
It handles:
- Database operations
- SQL generation
- Caching
- Lazy loading
- Transaction management at the ORM level

It sits between your Java objects and the database.

### Spring Data JPA
Spring Data JPA is like **Tesla Autopilot** — you just say *"take me to work"* and it handles all the driving:
- Gear shifts
- Navigation
- Parking

👉 Everything is handled automatically!

Spring Data JPA is an **abstraction layer on top of JPA implementations** (like Hibernate).
It provides:
- Repository pattern
- Query derivation
- Pagination
- Reduced boilerplate code

It works with any JPA provider (Hibernate, EclipseLink, OpenJPA, etc.)

---

### 🧠 Key Idea

JPA = Rules (Specification)

Hibernate = Implementation (Engine)

Spring Data JPA = Automation Layer (Ease of Use)

---

### Summary Table

| Concept           | What it is?                | Provides                       |
|------------------|---------------------------|--------------------------------|
| JPA              | Specification             | Rules, annotations             |
| Hibernate        | Implementation of JPA     | Actual ORM behavior            |
| Spring Data JPA  | Abstraction on top of JPA | Repositories, less boilerplate |

---

### Relationship Flow

```
Spring Data JPA (Repositories)
  ↓
JPA (spec + EntityManager API)
  ↓
Hibernate (JPA Provider / ORM)
  ↓
JDBC
  ↓
Database
```

---

## 7.3 What is ORM Framework?

**ORM = Object-Relational Mapping**

In simple words: ORM is a translator between your Java objects and database tables.

---

### The Problem ORM Solves

#### The Mismatch:

- **Java** thinks in **Objects**: User, Order, Product (with properties and methods)
- **Databases** think in **Tables**: rows, columns, foreign keys, SQL

> ORM is the bridge that automatically converts between these two different worlds!

---

### Simple Benefits:

- **Write Java, not SQL** – Think in objects, not tables
- **No manual conversion** – ORM does the translation
- **Less code** – One line vs 10+ lines of JDBC
- **Fewer errors** – No typos in SQL strings
- **Database independent** – Same code works with MySQL, PostgreSQL, Oracle

---

### ORM – The Translator

#### Java World ↔ Database World

##### User Object → USERS Table

**Java Class:**
```java
class User {
    Long id;
    String name;
    String email;
}
```

**Database Table (USERS):**

| Id | name  | email       |
|----|-------|-------------|
| 1  | John  | j@mail.com  |
| 2  | Alice | a@mail.com  |

---

##### Order Object → ORDERS Table

**Java Class:**
```java
class Order {
    Long id;
    User user;
    Double amount;
}
```

**Database Table (ORDERS):**

| Id | user_id | amount |
|----|---------|--------|
| 1  | 1       | 99.99  |
| 2  | 1       | 59.99  |

---

> **Java Reference ──► ◄── Foreign Key**
>
> In Java, relationships are represented as object references (e.g., `User user` inside `Order`).
> In the database, the same relationship is represented as a **Foreign Key** (`user_id` column in ORDERS table).

---

![ORM Diagram](imgs/orm.png)

---

## 7.4 Entity, Repository, CrudRepository, JpaRepository

### `@Entity`
Represents a table in the database.

```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String email;
}
```

### Repository Hierarchy

| Interface         | Description                              |
|-------------------|------------------------------------------|
| `Repository`      | Base interface, not used directly         |
| `CrudRepository`  | Basic CRUD operations (`save`, `findById`, `delete`) |
| `JpaRepository`   | Extends `CrudRepository`, adds pagination, sorting, and batch methods |

```java
public interface UserRepository extends JpaRepository<User, Long> {
    List<User> findByName(String name);
}
```

---

## 7.5 JPQL and Native Queries

### JPQL (Java Persistence Query Language)
Object-oriented query language — works with entity names & fields.

```java
@Query("SELECT u FROM User u WHERE u.email = ?1")
User findByEmail(String email);
```

### Native SQL
Direct SQL queries on the actual database tables.

```java
@Query(value = "SELECT * FROM users WHERE email = ?1", nativeQuery = true)
User findByEmailNative(String email);
```

---

## 7.6 Database Configuration

### H2 (In-memory database, great for dev/test)

```properties
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.h2.console.enabled=true
```

### MySQL / PostgreSQL Example

```properties
# MySQL
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=root
spring.jpa.database-platform=org.hibernate.dialect.MySQLDialect
```

```properties
# PostgreSQL
spring.datasource.url=jdbc:postgresql://localhost:5432/mydb
spring.datasource.username=postgres
spring.datasource.password=admin
spring.jpa.database-platform=org.hibernate.dialect.PostgreSQLDialect
```

---

## 7.7 Spring Boot with Hibernate

Hibernate is the default JPA implementation used in Spring Boot.

**Common Hibernate Properties:**

```properties
spring.jpa.show-sql=true                # show SQL in logs
spring.jpa.hibernate.ddl-auto=update    # auto-create tables (none, validate, update, create)
spring.jpa.properties.hibernate.format_sql=true
```

Hibernate maps Java objects to relational DB tables and handles:
- Entity lifecycle
- Query translation (JPQL to SQL)
- Lazy vs Eager fetching

---

## 7.8 DTOs and Model Mapping

### Why Use DTOs (Data Transfer Objects)?
- Avoid exposing full entity structure
- Improve performance by fetching only needed data
- Format/transform data before sending to frontend

```java
public class UserDTO {
    private String name;
    private String email;
}
```

### Mapping Entity to DTO (Manual)

```java
UserDTO dto = new UserDTO();
dto.setName(user.getName());
dto.setEmail(user.getEmail());
```

### Using ModelMapper (Optional)

```java
ModelMapper modelMapper = new ModelMapper();
UserDTO dto = modelMapper.map(user, UserDTO.class);
```

Add dependency:
```xml
<dependency>
    <groupId>org.modelmapper</groupId>
    <artifactId>modelmapper</artifactId>
    <version>3.1.0</version>
</dependency>
```

---

## 7.9 Summary Table

| Concept              | Key Role                                      |
|----------------------|-----------------------------------------------|
| `@Entity`            | Maps Java class to DB table                   |
| `JpaRepository`      | Provides CRUD + pagination + custom queries   |
| JPQL                 | Object-oriented querying                      |
| Native Query         | Direct SQL querying                           |
| `application.properties` | DB config, dialect, Hibernate options     |
| DTO                  | Transfers specific data to avoid entity leaks |
| ModelMapper          | Auto-map between Entity & DTO                 |

---

## 7.10 Spring Data JPA Annotations – Detailed Explanation

### 🔹 `@Entity`
- **Purpose**: Marks a class as a JPA entity (mapped to a database table).
- **Use Case**: Any class you want persisted in the database.
- **Example**:
  ```java
  @Entity
  public class User {
      @Id
      @GeneratedValue
      private Long id;
      private String name;
  }
  ```

### 🔹 `@Id`
- **Purpose**: Specifies the primary key of an entity.
- **Use Case**: Required for each JPA entity.
- **Example**:
  ```java
  @Id
  private Long id;
  ```

### 🔹 `@GeneratedValue`
- **Purpose**: Specifies how the primary key is generated (auto, identity, sequence).
- **Use Case**: When you want the database to generate primary keys automatically.
- **Example**:
  ```java
  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  private Long id;
  ```

### 🔹 `@Table`
- **Purpose**: Specifies the name of the table that the entity maps to.
- **Use Case**: Use when the table name is different from the class name.
- **Example**:
  ```java
  @Entity
  @Table(name = "users")
  public class User { ... }
  ```

### 🔹 `@Column`
- **Purpose**: Specifies the details of the column in the table.
- **Use Case**: Customize column names, nullability, length, etc.
- **Example**:
  ```java
  @Column(name = "user_name", nullable = false, length = 50)
  private String name;
  ```

### 🔹 `@OneToOne`
- **Purpose**: One-to-one relationship between two entities.
- **Use Case**: User has one profile.
- **Example**:
  ```java
  @OneToOne
  @JoinColumn(name = "profile_id")
  private Profile profile;
  ```

### 🔹 `@OneToMany`
- **Purpose**: One-to-many relationship.
- **Use Case**: A user has many orders.
- **Example**:
  ```java
  @OneToMany(mappedBy = "user")
  private List<Order> orders;
  ```

### 🔹 `@ManyToOne`
- **Purpose**: Many entities relate to one entity.
- **Use Case**: Many orders belong to one user.
- **Example**:
  ```java
  @ManyToOne
  @JoinColumn(name = "user_id")
  private User user;
  ```

### 🔹 `@ManyToMany`
- **Purpose**: Many-to-many relationship.
- **Use Case**: A student can enroll in many courses and vice versa.
- **Example**:
  ```java
  @ManyToMany
  @JoinTable(name = "student_course",
             joinColumns = @JoinColumn(name = "student_id"),
             inverseJoinColumns = @JoinColumn(name = "course_id"))
  private List<Course> courses;
  ```

### 🔹 `@JoinColumn`
- **Purpose**: Specifies the foreign key column.
- **Use Case**: Required in relationships to define how tables are linked.
- **Example**:
  ```java
  @ManyToOne
  @JoinColumn(name = "user_id")
  private User user;
  ```

### 🔹 `@Repository`
- **Purpose**: Marks a class as a DAO and enables exception translation into Spring's DataAccessException.
- **Use Case**: On interfaces or classes that perform DB operations.
- **Example**:
  ```java
  @Repository
  public interface UserRepository extends JpaRepository<User, Long> {
      User findByName(String name);
  }
  ```

---
