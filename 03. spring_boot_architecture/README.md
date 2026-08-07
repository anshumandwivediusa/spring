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
