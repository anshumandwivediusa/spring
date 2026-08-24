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


## Comparison Table

| Layer | Tools/Frameworks | Typical Usage |
|-------|------------------|---------------|
| **Unit** | JUnit 5/6, Mockito | Fast, isolated service logic |
| **Slice** | `@WebMvcTest`, `@DataJpaTest` | Controllers, repositories |
| **Integration** | `@SpringBootTest`, Testcontainers | Full context, DB/services |
| **Behavioral** | Spock | BDD-style expressive tests |
| **Web/API** | MockMvc, TestRestTemplate | REST endpoints, HTTP simulation |


## Best Practices & Pitfalls
- **Keep unit tests fast** → avoid loading full context.  
- **Use in-memory DBs** for repository tests.  
- **Leverage context caching** to prevent slow test runs.  
- **Rollback transactions** to ensure clean state.  
- **Avoid over-mocking** in integration tests.  


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

| Concept | **[Assert](ca://s?q=JUnit_assert_usage)** | **[Verify](ca://s?q=Mockito_verify_usage)** |
| --- | --- | --- |
| **Purpose** | Checks the **output/result** of a method | Checks the **interaction/behavior** with dependencies |
| **Scope** | Validates *what* the method returned | Validates *how* the method was used |
| **Example** | ``assertEquals("TestUser", ``service.getUserName(1L));`` → ensures the service returns correct value | ``verify(mockRepo).findById(1L);`` → ensures the repository was actually called |
| **Failure Meaning** | Business logic produced wrong result | Service didn’t delegate correctly to dependency |

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

# **JUnit and Mockito annotations**

## JUnit Annotations

| **Annotation** | **Purpose** | **Usage Example** |
|-----------------|-----------------|----------------|
| `@Test` | Marks a test method | `@Test void testLogic() { ... }` |
| `@BeforeEach` | Runs before each test | `@BeforeEach void setUp() { ... }` |
| `@AfterEach` | Runs after each test | `@AfterEach void tearDown() { ... }` |
| `@BeforeAll` | Runs once before all tests | `@BeforeAll static void initAll() { ... }` |
| `@AfterAll` | Runs once after all tests | `@AfterAll static void cleanAll() { ... }` |
| `@Disabled` | Skips a test | `@Disabled("Not ready") @Test void testX() {}` |
| `@ExtendWith(SpringExtension.class)` | Integrates Spring context | `@ExtendWith(SpringExtension.class) @SpringBootTest class TestApp {}` |


```java
import org.junit.jupiter.api.*;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit.jupiter.SpringExtension;
import org.junit.jupiter.api.extension.ExtendWith;

@SpringBootTest
@ExtendWith(SpringExtension.class)   // Integrates Spring context
class UserServiceTest {

    private static UserRepository mockRepo;
    private UserService service;

    @BeforeAll
    static void initAll() {
        System.out.println(">>> Global setup before all tests");
        mockRepo = new UserRepository(); // could be a mock in real case
    }

    @BeforeEach
    void setUp() {
        System.out.println(">>> Setup before each test");
        service = new UserService(mockRepo);
    }

    @Test
    void testGetUserName() {
        System.out.println(">>> Running testGetUserName");
        User user = new User(1L, "TestUser");
        mockRepo.save(user);

        Assertions.assertEquals("TestUser", service.getUserName(1L));
    }

    @Disabled("Not implemented yet")
    @Test
    void testFeatureX() {
        // This test will be skipped
    }

    @AfterEach
    void tearDown() {
        System.out.println(">>> Cleanup after each test");
        mockRepo.clear(); // hypothetical cleanup
    }

    @AfterAll
    static void cleanAll() {
        System.out.println(">>> Global cleanup after all tests");
    }
}
```
## Mockito Annotations

| **Annotation** | **Purpose** | **Usage Example** |
|-----------------|-----------------|----------------|
| `@Mock` | Creates a mock object | `@Mock UserRepository repo;` |
| `@InjectMocks` | Injects mocks into tested class | `@InjectMocks UserService service;` |
| `@Spy` | Partial mock (real methods unless stubbed) | `@Spy List<String> list = new ArrayList<>();` |
| `@Captor` | Captures arguments | `@Captor ArgumentCaptor<User> captor;` |
| `@MockBean` | Spring Boot specific mock | `@MockBean UserRepository repo;` |
| `@RunWith(MockitoJUnitRunner.class)` | JUnit 4 integration | `@RunWith(MockitoJUnitRunner.class)` |
| `@ExtendWith(MockitoExtension.class)` | JUnit 5 integration | `@ExtendWith(MockitoExtension.class)` |

```java
import static org.mockito.Mockito.*;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.*;

@ExtendWith(MockitoExtension.class)   // JUnit 5 + Mockito integration
class UserServiceTest {

    @Mock
    private UserRepository repo;       // Mock dependency

    @InjectMocks
    private UserService service;       // Injects mock into service

    @Spy
    private List<String> spyList = new ArrayList<>();  // Partial mock

    @Captor
    private ArgumentCaptor<User> userCaptor;           // Captures arguments

    @Test
    void testGetUserName() {
        when(repo.findById(1L)).thenReturn(new User(1L, "TestUser"));

        String result = service.getUserName(1L);

        Assertions.assertEquals("TestUser", result);

        // Verify interaction
        verify(repo).findById(1L);

        // Using captor
        verify(repo).save(userCaptor.capture());
        Assertions.assertEquals("TestUser", userCaptor.getValue().getUsername());

        // Spy usage
        spyList.add("Hello");
        verify(spyList).add("Hello");
    }
}
```

```java
import static org.mockito.Mockito.*;
import org.mockito.*;
import org.testng.Assert;
import org.testng.annotations.*;

@Listeners(MockitoTestNGListener.class)   // Integrates Mockito with TestNG
public class UserServiceTest {

    @Mock
    private UserRepository repo;       // Mock dependency

    @InjectMocks
    private UserService service;       // Injects mock into service

    @Spy
    private java.util.List<String> spyList = new java.util.ArrayList<>();  // Partial mock

    @Captor
    private ArgumentCaptor<User> userCaptor;           // Captures arguments

    @BeforeClass
    public void setUp() {
        MockitoAnnotations.openMocks(this); // Initialize mocks
    }

    @Test
    public void testGetUserName() {
        when(repo.findById(1L)).thenReturn(new User(1L, "TestUser"));

        String result = service.getUserName(1L);

        // Assertion
        Assert.assertEquals(result, "TestUser");

        // Verify interaction
        verify(repo).findById(1L);

        // Using captor
        verify(repo).save(userCaptor.capture());
        Assert.assertEquals(userCaptor.getValue().getUsername(), "TestUser");

        // Spy usage
        spyList.add("Hello");
        verify(spyList).add("Hello");
    }

    @AfterClass
    public void tearDown() {
        // Optional cleanup
    }
}
```
## Quick Takeaways
- **JUnit annotations** → manage test lifecycle and execution.  
- **Mockito annotations** → simplify mocking, injection, and verification.  
- Together, they make tests **clean, maintainable, and Spring‑ready**.  
