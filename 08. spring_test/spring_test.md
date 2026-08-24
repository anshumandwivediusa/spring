# Spring Testing

**Spring Testing covers a layered ecosystem: unit tests with JUnit/Mockito, slice tests for controllers/repositories, integration tests with `@SpringBootTest` and Testcontainers, and specialized frameworks like MockMvc and the Spring TestContext.** These topics are essential for exam prep and interviews, as they demonstrate how Spring ensures reliability across services, repositories, and REST APIs. 


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


Spring Testing spans **unit, slice, integration, and behavioral layers**, supported by frameworks like JUnit, Mockito, Testcontainers, and MockMvc. Mastering these ensures you can test everything from isolated service logic to full-stack behavior in Spring Boot projects.  

# Unit Testing/Mockito

- **Purpose** → Validate individual classes/methods in isolation.  
- **Frameworks** → JUnit 5/6 is the standard.  
- **Isolation** → Dependencies replaced with mocks/stubs.  
- **Annotations**:  
  - `@Test` → Marks test methods.  
  - `@BeforeEach` / `@AfterEach` → Setup/teardown.  
  - `@ExtendWith(SpringExtension.class)` → Integrates Spring context.  
- **Best Practices**:  
  - Keep tests fast (no full context unless needed).  
  - Test one behavior per method.  
  - Use descriptive names (e.g., `shouldReturnUserWhenIdExists`).  

## Mockito Essentials
- **Mocking framework** → Creates fake implementations of dependencies.  
- **Stubbing** → Define return values for mock methods.  
- **Verification** → Ensure methods are called with expected arguments.  
- **Annotations**:  
  - `@Mock` → Creates mock objects.  
  - `@InjectMocks` → Injects mocks into the tested class.  
  - `@MockBean` (Spring Boot) → Replaces a bean in the application context.  


## Example

### Service Class
```java
@Service
public class UserService {
    private final UserRepository userRepository;

    public UserService(UserRepository repo) {
        this.userRepository = repo;
    }

    public String getUserName(Long id) {
        return userRepository.findById(id).getUsername();
    }
}
```

### Unit Test with Mockito
```java
import static org.mockito.Mockito.*;
import org.junit.jupiter.api.Test;

class UserServiceTest {

    @Test
    void testGetUserName() {
        UserRepository mockRepo = mock(UserRepository.class);
        when(mockRepo.findById(1L)).thenReturn(new User(1L, "TestUser"));

        UserService service = new UserService(mockRepo);

        assertEquals("TestUser", service.getUserName(1L));
        verify(mockRepo).findById(1L);
    }
}
```



## Quick Comparison

| Concept | Unit Testing | Mockito |
|---------|--------------|---------|
| **Goal** | Test class logic | Mock dependencies |
| **Scope** | One class/method | Collaborators |
| **Speed** | Very fast | Very fast |
| **Spring Integration** | Optional | Works standalone or with Spring |



## Summary
- **Unit tests** → isolate and validate logic.  
- **Mockito** → mock dependencies, stub behavior, verify interactions.  
- Together, they make Spring services **testable, reliable, and fast**.  
