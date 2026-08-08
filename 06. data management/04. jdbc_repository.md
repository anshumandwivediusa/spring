# JdbcRepository

## 1. Definition
- **JdbcRepository** is a **Spring Data repository abstraction** built on top of **JdbcTemplate**.  
- It provides **CRUD operations** and **derived queries** similar to JPA repositories, but without Hibernate or ORM features.  
- Entities are mapped directly to tables — no lazy loading, no proxies.


## 2. Key Features
- **Repository Pattern** → Define interfaces, Spring generates implementations.  
- **CRUD Support** → `save()`, `findById()`, `findAll()`, `deleteById()`.  
- **Derived Queries** → Methods like `findByName(String name)` auto‑generate SQL.  
- **Lightweight** → No entity manager, no persistence context.  
- **Transactions** → Works with Spring’s `@Transactional`.  
- **Mapping** → Uses simple row mapping, not full ORM.


## 3. Example – User Repository
```java
@Table("users")
public class User {
    @Id
    private Long id;
    private String name;
}

public interface UserRepository extends CrudRepository<User, Long> {
    List<User> findByName(String name);
}
```

Spring Data JDBC generates SQL like:
```sql
SELECT * FROM users WHERE name = ?
```


## 4. Common Commands
| **Method** | **Description** | **Example** |
|------------|-----------------|-------------|
| **save()** | Insert or update entity | `userRepo.save(new User(1L, "John"));` |
| **findById()** | Fetch entity by ID | `userRepo.findById(1L);` |
| **findAll()** | Fetch all records | `userRepo.findAll();` |
| **deleteById()** | Delete entity by ID | `userRepo.deleteById(1L);` |
| **Derived Queries** | Auto‑generated SQL from method names | `List<User> findByName(String name);` |


## 5. Advantages
- Less overhead than JPA/Hibernate.  
- Simple CRUD with repository abstraction.  
- SQL is still visible and predictable.  
- Good for microservices or apps where ORM is unnecessary.


## 6.Limitations
- No lazy loading.  
- No entity relationships (only aggregates).  
- Less feature‑rich than JPA (no JPQL, no caching, no advanced ORM features).  


## 7. Comparison

| **Aspect** | **JdbcRepository** | **Spring Data JPA** |
|------------|---------------------|----------------------|
| Mapping | Direct table mapping | ORM entity mapping |
| Relationships | Limited (aggregate roots only) | Full support (OneToMany, ManyToMany) |
| Lazy Loading | ❌ | ✅ |
| Queries | Derived + SQL | Derived + JPQL + native SQL |
| Overhead | Low | Higher (Hibernate, EntityManager) |


## 8. Quick Facts
- **JdbcRepository** = Repository abstraction over JDBC.  
- **Best use case** → Lightweight apps, microservices, CRUD without ORM.  
- **Key methods** → `save()`, `findById()`, `findAll()`, `deleteById()`.  
- **Derived queries** → Auto‑generate SQL from method names.  
- **No ORM features** → Unlike JPA, it doesn’t manage entity lifecycle or relationships.  
