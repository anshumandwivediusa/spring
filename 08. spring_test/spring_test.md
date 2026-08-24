# Spring Testing

**Spring Testing covers a layered ecosystem: unit tests with JUnit/Mockito, slice tests for controllers/repositories, integration tests with `@SpringBootTest` and Testcontainers, and specialized frameworks like MockMvc and the Spring TestContext.** These topics are essential for exam prep and interviews, as they demonstrate how Spring ensures reliability across services, repositories, and REST APIs.  [docs.spring.io](https://docs.spring.io/spring-framework/docs/5.2.25.RELEASE/spring-framework-reference/testing.html)  [chanhle.dev](https://chanhle.dev/en/blog/spring-boot-testing-guide)  [javathinking.com](https://www.javathinking.com/blog/testing-in-spring-boot/)  


## Core Spring Test Topics

- **Unit Testing**  
  - JUnit 5/6 for fast, isolated tests.  
  - **Mockito** for mocking dependencies.  
  - `@MockBean` for injecting mocks into the Spring context.  

- **Integration Testing**  
  - `@SpringBootTest` loads full application context.  
  - **Testcontainers** for real DB/service testing.  
  - Transaction management with `@Transactional`.  

- **Slice Testing**  
  - `@WebMvcTest` → Controllers.  
  - `@DataJpaTest` → Repositories.  
  - `@RestClientTest` → REST clients.  

- **Spring TestContext Framework**  
  - Context caching, dependency injection, transaction rollback.  
  - Annotations: `@ContextConfiguration`, `@ActiveProfiles`, `@Sql`.  

- **Spring MVC Testing**  
  - **MockMvc** for simulating HTTP requests.  
  - `TestRestTemplate` for REST endpoints.  

- **Database Testing**  
  - In-memory DBs (H2) for quick tests.  
  - SQL script execution with `@Sql`.  

- **Security Testing**  
  - `spring-security-test` dependency.  
  - Mock authenticated users with `@WithMockUser`.  


## 📊 Comparison Table

| Layer | Tools/Frameworks | Typical Usage |
|-------|------------------|---------------|
| **Unit** | JUnit 5/6, Mockito | Fast, isolated service logic |
| **Slice** | `@WebMvcTest`, `@DataJpaTest` | Controllers, repositories |
| **Integration** | `@SpringBootTest`, Testcontainers | Full context, DB/services |
| **Behavioral** | Spock | BDD-style expressive tests |
| **Web/API** | MockMvc, TestRestTemplate | REST endpoints, HTTP simulation |


## ⚠️ Best Practices & Pitfalls
- **Keep unit tests fast** → avoid loading full context.  
- **Use in-memory DBs** for repository tests.  
- **Leverage context caching** to prevent slow test runs.  
- **Rollback transactions** to ensure clean state.  
- **Avoid over-mocking** in integration tests.  


## ✅ In short
Spring Testing spans **unit, slice, integration, and behavioral layers**, supported by frameworks like JUnit, Mockito, Testcontainers, and MockMvc. Mastering these ensures you can test everything from isolated service logic to full-stack behavior in Spring Boot projects.  
