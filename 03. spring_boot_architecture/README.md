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


| **Aspect** | **[@RequestParam](ca://s?q=RequestParam_annotation)** | **[@RequestBody](ca://s?q=RequestBody_annotation)** |
| --- | --- | --- |
| **Source of Data** | Query string (``?id=1&name=Anshuman``) or form fields | Request body (JSON, XML, plain text) |
| **Typical HTTP Method** | Commonly used with **GET** (but can also be used with POST if form data is sent as parameters) | Commonly used with **POST/PUT/PATCH** (methods that carry payloads) |
| **Data Type** | Simple values (String, int, boolean) | Complex objects (POJOs, lists, arrays) |
| **Conversion** | Spring automatically converts query parameters to method arguments | Uses **HttpMessageConverters** (e.g., Jackson) to map JSON/XML → Java object |
| **Example Request** | ``GET ``/users?id=1&name=Anshuman`` | ``POST ``/users`` with body: ``{"id":1,"name":"Anshuman"}`` |
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
```


