# Spring Boot Architectures

## 1. Spring Boot Architecture Overview
### 1. **Core Layer**
- **Spring Boot Starter Parent** → Provides dependency and plugin management.  
- **Spring Boot Dependencies BOM** → Ensures consistent versions across modules.  
- **Auto-Configuration** → Configures beans automatically based on classpath and conditions.  
- **SpringApplication** → Entry point that bootstraps the application.

### 2. **Configuration Layer**
- **Externalized Configuration** → Uses `application.properties` or `application.yml`.  
- **Profiles** → Environment-specific configurations (`dev`, `test`, `prod`).  
- **Conditional Annotations** → Smart bean creation (`@ConditionalOnClass`, `@ConditionalOnProperty`, etc.).

### 3. **Application Layer**
- **Component Scan** → Detects beans (`@Component`, `@Service`, `@Repository`, `@Controller`).  
- **Dependency Injection** → Managed by IoC container (`@Autowired`, `@Qualifier`).  
- **Business Logic** → Encapsulated in services and repositories.

### 4. **Web Layer**
- **Embedded Servers** → Tomcat, Jetty, Undertow.  
- **Spring MVC / WebFlux** → Handles HTTP requests (`@RestController`, `@RequestMapping`).  
- **DispatcherServlet** → Central request dispatcher in servlet apps.  
- **Reactive Stack** → Non-blocking, event-driven processing.

### 5. **Data Access Layer**
- **Spring Data JPA** → ORM integration with Hibernate.  
- **Spring JDBC** → Simplified database access.  
- **Transaction Management** → Declarative transactions with `@Transactional`.

### 6. **Actuator & Monitoring**
- **Spring Boot Actuator** → Health checks, metrics, auditing, monitoring endpoints.  
- **Micrometer Integration** → Metrics to Prometheus, Grafana, CloudWatch.  

### 7. **Startup Flow**
1. `@SpringBootApplication` → Combines config + auto-config + scanning.  
2. `SpringApplication.run()` → Bootstraps context.  
3. Component scanning → Registers beans.  
4. Auto-configuration → Applies defaults based on classpath.  
5. Context refresh → Beans created, dependencies injected.  
6. Embedded server startup → Tomcat/Jetty/Undertow.  
7. Actuator endpoints exposed → Monitoring ready.  

## 2. Architecture Layers Summary

| **Layer** | **Components** | **Purpose** |
|--|-|-|
| **Core** | Starter Parent, BOM, Auto-Config | Bootstrap & dependency management |
| **Config** | Externalized config, Profiles, Conditional annotations | Environment & smart bean creation |
| **Application** | Component scanning, DI, Services | Business logic |
| **Web** | MVC/WebFlux, DispatcherServlet, Embedded servers | Request handling |
| **Data** | JPA, JDBC, Transactions | Persistence |
| **Monitoring** | Actuator, Micrometer | Health & metrics |

### Architectural Layers in Spring

| **Layer** | **Spring Components** | **Responsibilities** |
|-----------|------------------------|-----------------------|
| **Presentation Layer** | `@Controller`, `@RestController`, `DispatcherServlet`, ViewResolvers | Handles HTTP requests, maps URLs to controllers, prepares responses (HTML, JSON, XML) |
| **Business Layer** | `@Service`, AOP (transactions, logging, security) | Encapsulates business logic, applies cross-cutting concerns |
| **Persistence Layer** | `@Repository`, Spring Data JPA, JDBC, ORM | Manages database operations, translates exceptions, abstracts persistence APIs |
| **Integration Layer** | JMS, Web Services, Messaging, Spring Integration | Connects with external systems (messaging, SOAP/REST services, queues) |
| **Infrastructure Layer** | IoC Container, BeanFactory, ApplicationContext | Provides dependency injection, bean lifecycle management, configuration |
| **Cross-Cutting Layer** | Spring AOP, Security, Validation | Applies concerns like authentication, authorization, caching, logging |

### How These Layers Interact
1. **Client Request** → Hits the **Presentation Layer** (`DispatcherServlet`, controllers).  
2. **Business Logic** → Delegated to **Service Layer**.  
3. **Persistence** → Services call repositories for DB operations.  
4. **Integration** → External systems (messaging, APIs) are accessed if needed.  
5. **Infrastructure** → IoC container wires everything together.  
6. **Cross-Cutting Concerns** → Applied transparently via AOP (e.g., transactions, security).  

### Example Flow
```text
Client Request
   │
   ▼
@Controller / @RestController
   │
   ▼
@Service (Business Logic)
   │
   ▼
@Repository (Persistence)
   │
   ▼
Database / External System
```

## 3. Presentation Layer

### A. Presentation Layer Components (Spring)

- **@Controller**  
  - Handles incoming **http/web requests**.  
  - Returns **views** (HTML, JSP, Thymeleaf templates).  
  - Typically used in **MVC applications**.

     ```
      @Controller
      public class HomeController {
      
          @GetMapping("/")
          public String home() {
              return "home"; // Renders home.html (or another configured view)
          }
      }
     ```

- **@ResponseBody**
   - @ResponseBody is a Spring MVC annotation that tells Spring to write the return value of a controller method directly into the HTTP response body instead of treating it as a view name.
   - Why is @ResponseBody Needed? Normally, @Controller methods are associated with view resolution.
   - If we use @Controller,
        - Spring interprets return "home"; as: view "home";
        - Return Value --> View Name = "home" --> ViewResolver --> home.html / JSP
     else we use @RestController/@ResponseBody,
        - Spring interprets the return value as: Return Value
        - Return Value --> @ResponseBody --> HttpMessageConverter --> HTTP Response Body
  ```
   @Controller
   public class UserController {
   
       @GetMapping("/user")
       @ResponseBody
       public User getUser() {
           return new User(101, "John");
       }
   }
   ```
   - One of the most common uses of @ResponseBody is building REST APIs or converting Java Objects into JSON
   - @ResponseBody with String
   ```
   @GetMapping("/hello")
   @ResponseBody
   public String hello() {
       return "Hello World";
   }
   Result: Hello World
  ```
- @ResponseBody with Collections
  ```
  @GetMapping("/users")
   @ResponseBody
   public List<User> getUsers() {
       return List.of(
           new User(1, "John"),
           new User(2, "Mary")
       );
   }
   Result:
   [
     {
       "id": 1,
       "name": "John"
     },
     {
       "id": 2,
       "name": "Mary"
     }
   ]
  ```
  - Method-Level: Only this method returns data directly.
  - Class-Level: All handler methods behave as if they have @ResponseBody.

- **@RequestBody**  
  - @RequestBody is used in Spring MVC and Spring Boot to bind the HTTP request body (JSON, XML, or plain text) directly to a Java object.
  - Converts incoming request data into a Java object using HttpMessageConverters (e.g., Jackson for JSON).
  - Simplifies handling of REST API requests.
  - Eliminates manual parsing of request payloads.

| Annotation      | Direction       | Purpose                          |
| --------------- | --------------- | -------------------------------- |
| `@RequestBody`  | Client → Server | HTTP request body → Java object  |
| `@ResponseBody` | Server → Client | Java object → HTTP response body |


- **@RestController**  
  - Specialized controller for **RESTful APIs**.  
  - Combines `@Controller` + `@ResponseBody`.  
  - Returns **JSON/XML** directly instead of views.  

- **DispatcherServlet**  
  - The **front controller** in Spring MVC.  
  - Routes requests to the appropriate controller.  
  - Manages request lifecycle (mapping, view resolution, exception handling).  

- **ViewResolver**  
  - Resolves logical view names returned by controllers into actual templates.  
  - Example: `"home"` → `/WEB-INF/views/home.jsp` or `home.html` (Thymeleaf).  

- **@RequestMapping**  
  - Maps **URLs** (and HTTP methods) to controller methods.  
  - Example: `@RequestMapping("/users")` → handles requests to `/users`.  
  - Variants: `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`.  

### B. Presentation Layer Components (Request/Response)


- **@PathVariable**  
  `@PathVariable` is used to bind values directly from the **URI path** into method parameters, making RESTful endpoints expressive by embedding identifiers in the URL (e.g., `/users/10`). By default, it is **required**, but can be marked optional with `required=false` if multiple URL patterns are defined. It is ideal for resource identifiers such as IDs, slugs, or names, and Spring automatically converts the path segment into the correct parameter type.

- **@RequestParam**  
  `@RequestParam` extracts values from the **query string** (`?id=10&active=true`) or form data and maps them to method parameters. Parameters are required by default, but can be made optional with `required=false` or given a fallback using `defaultValue`. It is best suited for simple inputs like filters, search keywords, or flags, and works seamlessly with primitive types and strings.

- **@RequestBody**  
  `@RequestBody` binds the **HTTP request body** (JSON, XML, or plain text) to a Java object, using Spring’s **HttpMessageConverters** (e.g., Jackson for JSON). It is commonly used in **POST, PUT, or PATCH** requests where clients send structured payloads. It supports complex POJOs, lists, and arrays, and can be combined with `@Valid` for automatic validation of incoming data.

| **Aspect** | **[@RequestParam](ca://s?q=RequestParam_annotation)** | **[@RequestBody](ca://s?q=RequestBody_annotation)** |
| --- | --- | --- |
| **Source of Data** | Query string (`?id=1&name=Anshuman`) or form fields | Request body (JSON, XML, plain text) |
| **Typical HTTP Method** | Commonly used with **GET** (but can also be used with POST if form data is sent as parameters) | Commonly used with **POST/PUT/PATCH** (methods that carry payloads) |
| **Data Type** | Simple values (String, int, boolean) | Complex objects (POJOs, lists, arrays) |
| **Conversion** | Spring automatically converts query parameters to method arguments | Uses **HttpMessageConverters** (e.g., Jackson) to map JSON/XML → Java object |
| **Example Request** | ```GET ``/users?id=1&name=Anshuman`` | ``POST ``/users`` with body: ``{"id":1,"name":"Anshuman"}`` |
| **Example Code** | ``java ``@GetMapping("/users") ``public ``User ``getUser(@RequestParam ``Long ``id, ``@RequestParam ``String ``name) ``{ ``return ``new ``User(id, ``name); ``}`` | ``java ``@PostMapping("/users") ``public ``User ``createUser(@RequestBody ``User ``user) ``{ ``return ``user; ``}`` |

| **Aspect** | **[@PathVariable](ca://s?q=PathVariable_annotation)** | **[@RequestParam](ca://s?q=RequestParam_annotation)** |
| --- | --- | --- |
| **Source of Data** | Extracts values from the **URL path** | Extracts values from the **query string** or form data |
| **Typical Use Case** | Identifying a specific resource by ID or name | Filtering, searching, or passing optional parameters |
| **Example Request** | `GET /users/10` | `GET /users?id=10&active=true` |
| **Example Code** | ```java @GetMapping("/users/{id}") public User getUser(@PathVariable Long id) { return userService.findById(id); }``` | ```java @GetMapping("/users") public User getUser(@RequestParam Long id, @RequestParam boolean active) { return userService.findByIdAndStatus(id, active); }``` |
| **Data Type** | Usually single identifiers (IDs, slugs, names) | Simple values (String, int, boolean) |
| **Optional Support** | Always required unless marked with `required=false` | Can be optional with `required=false` or `defaultValue` |
| **REST Style** | Fits **RESTful design** (resources identified in path) | Fits query-based filtering or optional inputs |

```java
   /users?id=10&active=true

   @GetMapping("/users/{id}")
   public User getUser(
       @PathVariable Long id,
       @RequestParam(defaultValue = "false") boolean active) {
       return userService.findByIdAndStatus(id, active);
   }

   @GetMapping("/search") //required = false
   public String search(@RequestParam(required = false) String keyword) {
       return keyword != null ? "Searching for: " + keyword : "No keyword provided";
   }

   @GetMapping("/search") //defaultValue
   public String search(@RequestParam(defaultValue = "all") String keyword) {
       return "Searching for: " + keyword;
   }

   @GetMapping({"/users", "/users/{id}"})
   public String getUser(@PathVariable(required = false) Long id) {
       return id != null ? "User ID: " + id : "All users";
   }
```

### C. Lombok + Validation Overview
- **Lombok** reduces boilerplate code (getters, setters, constructors, builders).  
- **Validation annotations** (`@NotNull`, `@NotBlank`, `@Email`, `@Size`) come from **Bean Validation API (JSR‑380)**.  
- **@Valid** in controllers triggers automatic validation of incoming request data.  
- Lombok and validation annotations **work together seamlessly**: Lombok generates the code, validation enforces rules.

#### 1. **Entity/DTO Definition**
- Use Lombok annotations like `@Data`, `@Builder`, `@AllArgsConstructor`, `@NoArgsConstructor`.  
- Add validation annotations on fields.  

```java
@Data
public class UserDTO {
    @NotBlank(message = "Name is mandatory")
    private String name;

    @Email(message = "Invalid email format")
    private String email;

    @Size(min = 8, message = "Password must be at least 8 characters")
    private String password;
}
```

When designing DTOs in Spring Boot, **optional fields** are simply those without strict validation constraints like `@NotNull` or `@NotBlank`. Lombok can generate the boilerplate code (getters, setters, constructors) while the Bean Validation API handles rules for mandatory fields. If a field is optional, you either leave it without validation annotations or use flexible ones like `@Size(min=0)` or `@Nullable`. This way, when a client omits the field in a JSON payload, validation still passes and the property remains `null` or empty. For query parameters, you can mark them optional with **@RequestParam(required=false)** or provide a **defaultValue**, while for path variables you need multiple URL mappings to support optionality. In short, optional fields in DTOs allow APIs to accept partial data gracefully without breaking validation, while still enforcing rules on critical fields.

Note: 
_In Spring validation, the difference between **null** and **blank** is important when designing DTOs. A field is **null** when it is completely absent or not provided in the request payload, and you enforce its presence with **@NotNull**. A field is **blank** when it exists but contains an empty string (`""`) or only whitespace, and you enforce meaningful content with **@NotBlank**. In practice, `@NotNull` ensures the property is supplied, while `@NotBlank` ensures it has actual characters. Together, they help distinguish between missing values and empty inputs, allowing you to design DTOs that clearly separate **mandatory fields** from **optional ones** and enforce stricter rules only where necessary.
_
#### 2. **Controller Layer**
- Use `@Valid` with `@RequestBody` or `@RequestParam`.  
- Spring automatically validates incoming data before executing logic.  
```java
@PostMapping("/users")
public ResponseEntity<String> createUser(@Valid @RequestBody UserDTO user) {
    return ResponseEntity.ok("User created: " + user.getName());
}
```

#### 3. **Validation Flow**
- Client sends JSON → Spring maps to DTO using Jackson.  
- `@Valid` triggers validation via Hibernate Validator.  
- If constraints fail → Spring returns **400 Bad Request** with error details.  
- If valid → Controller executes normally.

#### 4. **Error Handling**
- Use `BindingResult` for manual error handling.  
- Or define a **@ControllerAdvice** class for global exception handling.  
```java
@ExceptionHandler(MethodArgumentNotValidException.class)
public ResponseEntity<String> handleValidationErrors(MethodArgumentNotValidException ex) {
    return ResponseEntity.badRequest().body("Validation failed: " + ex.getMessage());
}
```

### Summary
- Lombok simplifies DTO/entity creation.  
- Validation annotations enforce rules.  
- `@Valid` integrates both, ensuring **clean code + robust validation**.  
- Together, they make Spring Boot APIs **concise, maintainable, and safe**.  

## Business Layer
In a typical **Spring Boot layered architecture**, the **Business Layer** (also called the **Service Layer**) sits between the **Controller Layer** and the **Data Access Layer (Repository/DAO)**. Its role is to encapsulate the **business logic** of the application — the rules, workflows, and operations that define how data should be processed beyond simple CRUD.

### Business Layer Responsibilities
- **Business Logic** → Implements core rules of the application (e.g., calculating discounts, validating transactions, applying workflows).  
- **Service Classes** → Typically annotated with `@Service` to indicate business logic components.  
- **Transaction Management** → Handles transactional boundaries (`@Transactional`) to ensure data consistency.  
- **Integration** → Coordinates between multiple repositories, external APIs, or other services.  
- **Validation** → Performs domain‑specific checks beyond simple DTO validation.  
- **Abstraction** → Shields controllers from persistence details, ensuring separation of concerns.  

### Example
```java
@Service
public class UserService {

    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    @Transactional
    public User registerUser(User user) {
        // Business rule: email must be unique
        if (userRepository.existsByEmail(user.getEmail())) {
            throw new IllegalArgumentException("Email already registered");
        }
        return userRepository.save(user);
    }
}
```

### Layered Architecture Flow
1. **Controller Layer** → Handles HTTP requests (`@RestController`).  
2. **Business Layer** → Applies business rules (`@Service`).  
3. **Data Access Layer** → Interacts with database (`@Repository`).  

### Summary
The **Business Layer** is the **heart of the application**, where rules and workflows live. It ensures controllers remain thin (focused on request/response handling) and repositories remain simple (focused on persistence). This separation makes applications **cleaner, testable, and maintainable**.  


