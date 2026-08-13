# Foundations of Spring Framework

**The foundations of the Spring Framework lie in its core principles of *Dependency Injection (DI)*, *Inversion of Control (IoC)*, and a *modular architecture* that simplifies enterprise Java development by handling infrastructure concerns so developers can focus on business logic.** It provides a lightweight container, integrates seamlessly with Java EE/Jakarta EE, and supports both traditional MVC and modern reactive programming models.

[docs.spring.io](https://docs.spring.io/spring-framework/reference/overview.html)  [docs.spring.io](https://docs.spring.io/spring-framework/docs/4.3.26.RELEASE/spring-framework-reference/html/overview.html)  


## 1. Why Spring & Spring Boot Matter More Than Servlet/JSP/JSF

1. **Modern Development Practices**  
   - Spring emphasizes **Dependency Injection (DI)** and **Aspect-Oriented Programming (AOP)**, leading to modular, testable, and maintainable code.  
   - In contrast, Servlet/JSP/JSF applications often rely on tightly coupled components, manual dependency management, and hard-coded logic, making them harder to test and evolve.

2. **Lightweight and Scalable**  
   - Spring applications are lightweight (core jars <-12 MB) due to DI and support **non-blocking I/O** with Spring WebFlux for high-performance, reactive systems.  
   - Servlets and JSP can be resource-heavy, less efficient, and not well-suited for modern scalable architectures.

3. **Separation of Concerns & Testability**  
   - Spring MVC enforces clear separation between **controller, service, and view layers**, improving maintainability and unit testing.  
   - JSP/JSF often mixes presentation with business logic, resulting in tightly coupled and harder-to-maintain applications.

4. **Rich Ecosystem for Enterprise Needs**  
   - Spring provides powerful modules like **Spring Data, Spring Security, Spring Cloud**, and **Spring Batch**, covering enterprise-grade requirements.  
   - Servlet/JSP/JSF lack these advanced modules, requiring custom implementations or external libraries.

5. **Easy Integration with Modern Tools & APIs**  
   - Spring integrates seamlessly with relational/NoSQL databases, REST APIs, messaging systems, and third-party libraries.  
   - With Servlets/JSP, building REST APIs or integrating modern tools requires extensive boilerplate and manual configuration.

6. **Reduced Boilerplate with Spring Boot**  
   - Spring Boot simplifies development with **auto-configuration, starter dependencies, and embedded servers (Tomcat/Jetty)**.  
   - Developers can run apps with minimal setup, unlike Servlets/JSP which demand manual `web.xml` configuration and external server deployment.

## 2. Core Foundations of Spring Framework

### A. **Dependency Injection (DI) & Inversion of Control (IoC)**
* **Inversion of Control (IoC)** is the _principle_ in which the framework manages the creation, configuration, and lifecycle of application objects (beans).
* **Dependency Injection (DI)** is the _mechanism_ Spring uses to implement IoC by automatically providing the required dependencies to objects.
* This approach promotes **loose coupling**, improves maintainability, and makes applications easier to test.
* **Example:** A service class does not create its repository using `new`. Instead, Spring injects the repository into the service through constructor, setter, or field injection.

### B. **Modular Architecture**
Spring is divided into ~20 modules grouped into:
- **Core Container** → `Bean Factory`, `Application Context`  
- **Data Access/Integration** → JDBC, ORM, Transactions.  
- **Web** → Spring MVC, WebFlux (reactive).  
- **AOP** → Aspect-Oriented Programming for cross-cutting concerns.  
- **Messaging** → Integration with JMS, Kafka, RabbitMQ.  
- **Testing** → JUnit/TestNG support.  [docs.spring.io](https://docs.spring.io/spring-framework/docs/4.3.26.RELEASE/spring-framework-reference/html/overview.html)  

### C. **POJO-based Development**
- Applications are built using **Plain Old Java Objects**.  
- Enterprise services (transactions, security, messaging) can be applied without forcing inheritance from framework classes.

### D. **Spring MVC & WebFlux**
- **Spring MVC** → Servlet-based, synchronous request handling.  
- **Spring WebFlux** → Reactive, non-blocking, built on Reactor for high scalability.

### E. **Integration with Java EE/Jakarta EE**
- Spring complements EE by integrating selected APIs (JPA, JMS, JMX) rather than adopting the full specification.  [docs.spring.io](https://docs.spring.io/spring-framework/reference/overview.html)  

## 2. Inversion of Control (IoC)
<p align = "center">
<img width="400" height="300" alt="image" src="https://github.com/user-attachments/assets/44b1f90d-ab49-4ab3-a1cd-fa44cf1edf46" />
</p>

- **Definition:** A design principle where the control of object creation and dependency management is inverted from the application code to a container/framework.  
- **Impact:** Instead of classes instantiating their dependencies directly, the framework provides them.
- **Benefit:** Promotes loose coupling, easier testing, and better separation of concerns.

### A. Two Main Approaches
1. **Dependency Injection (DI)**  
   - Framework injects dependencies into objects.  
   - Types:  
     - **Constructor Injection** → Dependencies passed via constructor.  
     - **Field Injection** → Dependencies set via fields.  
     - **Setter Injection** → Dependencies set via setter methods.  
     - **Method Injection** → Dependencies passed via specific methods.  


      | **Injection Type** | **How It Works** | **Best Use Case** | **Time of Execution** |
      | --- | --- | --- | --- |
      | **[Constructor Injection](ca://s?q=Constructor_Injection_in_Spring)** | Dependencies via constructor | Mandatory dependencies, immutability | **At bean instantiation (first step)** |
      | **[Field Injection](ca://s?q=Field_Injection_in_Spring)** | Dependencies injected directly into fields | Quick setup, simple apps, but less testable | **After construction, via reflection before initialization** |
      | **[Setter Injection](ca://s?q=Setter_Injection_in_Spring)** | Dependencies via setter methods | Optional dependencies, reconfiguration | **After construction, during dependency population** |
      | **[Method Injection](ca://s?q=Method_Injection_in_Spring)** | Dependencies via specific method | Rare cases, prototype beans | **On method call (runtime, not at bean creation)** |

      ```java
      import org.springframework.beans.factory.annotation.Autowired;
      import org.springframework.stereotype.Component;
      
      @Component
      public class DemoService {
      
          // Field Injection → dependency injected directly into the field
          @Autowired
          private FieldDependency fieldDependency;
      
          private final ConstructorDependency constructorDependency;
          private SetterDependency setterDependency;
      
          // Constructor Injection → mandatory dependency
          @Autowired
          public DemoService(ConstructorDependency constructorDependency) {
              this.constructorDependency = constructorDependency;
          }
      
          // Setter Injection → optional dependency
          @Autowired
          public void setSetterDependency(SetterDependency setterDependency) {
              this.setterDependency = setterDependency;
          }
      
          // Method Injection → dependency passed into a specific method
          @Autowired
          public void performTask(MethodDependency methodDependency) {
              constructorDependency.execute();
              if (setterDependency != null) {
                  setterDependency.run();
              }
              fieldDependency.process();
              methodDependency.doWork();
          }
      }
      
      ```
2. **Dependency Lookup (DL)**  
   - Objects actively look up dependencies from a container/service.  
   - Patterns:  
     - **Service Locator Pattern** → Central registry provides dependencies. Example: ServiceLocator.getService("PaymentService")  
     - **Factory Pattern** → Factory class creates and supplies objects. Example: ConnectionFactory.createConnection().
      - **JNDI Lookup Pattern** → Java Naming and Directory Interface used to fetch resources. Example: ctx.lookup("java:comp/env/jdbc/MyDB").

### B. Comparison Table

| Aspect | **Dependency Injection (DI)** | **Dependency Lookup (DL)** |
|--------|-------------------------------|-----------------------------|
| Control | Framework injects dependencies | Object requests dependencies |
| Coupling | Loose coupling | Tighter coupling |
| Testability | High | Lower |
| Examples | Spring IoC container | Service Locator, JNDI |

### C. Quick Notes
- **IoC Principle:** Framework controls object lifecycle.  
- **DI vs DL:** DI is *passive* (dependencies given), DL is *active* (dependencies fetched).  
- **Spring Framework:** Popular for DI (constructor, setter).  
- **Service Locator:** Considered an anti-pattern in modern design (hides dependencies).  
- **Factory Pattern:** Still widely used for controlled object creation.  

## 3. Spring Core Modules
<img width="760" height="512" alt="image" src="https://github.com/user-attachments/assets/48eb7142-a6b0-4ed0-a4d3-7ed4431080db" />

The **Spring Core Container** is the set of JARs that provide the fundamental IoC/DI functionality:

- **spring-core**  
  - Foundation classes and utilities.  
  - Provides the core framework features (resource loading, type conversion, etc.).

- **spring-beans**  
  - Handles bean creation, wiring, and lifecycle.  
  - Implements Dependency Injection (DI).

- **spring-context**  
  - Provides the `ApplicationContext`.  
  - Adds enterprise features: events, resource loading, internationalization.  
  - Builds on `spring-core` and `spring-beans`.

- **spring-expression (SpEL)**  
  - Expression Language for querying/manipulating objects at runtime.  
  - Used in configuration, annotations, and conditional logic.

### Container Interfaces
Within these modules, Spring defines **two main container interfaces**:

- **BeanFactory**:
   - The simplest container, providing basic DI functionality.
   - Lazily loads beans (created only when requested).
   - Lightweight, suitable for memory-constrained environments.
   - No support for advanced features like event handling, AOP, or internationalization.
     ```java
      BeanFactory factory = new XmlBeanFactory(new FileSystemResource("beans.xml"));
      MyBean bean = (MyBean) factory.getBean("myBean");
      ```
- **ApplicationContext**:
   - A superset of BeanFactory with enterprise-level features.
   - Eagerly loads beans at startup (by default).
   - Supports event propagation, AOP, internationalization, and message resources.
   - Provides specialized contexts like WebApplicationContext for web apps.  
     ```java
       ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
       MyBean bean = (MyBean) context.getBean("myBean");
     ```
     ```java
       @Configuration
       public class AppConfig {
           @Bean
           public MyBean myBean() {
               return new MyBean();
           }
       }
       ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
   ```
The **modules (spring-core, spring-beans, spring-context, spring-expression)** provide the infrastructure, while the **interfaces (BeanFactory, ApplicationContext)** define how you interact with the container.

```
SPRING CORE CONTAINER
   │
   ├── spring-core        → Foundation utilities
   ├── spring-beans       → Bean creation & DI
   ├── spring-context     → ApplicationContext, events, resources
   └── spring-expression  → SpEL (dynamic values)
```

- **BeanFactory** lives in `spring-beans`.  
- **ApplicationContext** lives in `spring-context`.  
- Together, they are powered by the **core container modules**.


## 4. Historical Context
- **Introduced in 2003** to simplify the complexity of J2EE.  
- Became popular for its lightweight container and ease of testing.  
- **Spring 6.0 (2022)** requires **Java 17+**, aligning with modern JVM features.  [docs.spring.io](https://docs.spring.io/spring-framework/reference/overview.html)  

| **Version** | **Year** | **Java Support** | **Key Features** |
| --- | --- | --- | --- |
| **Spring 1.0** | 2004 | JDK 1.3 | IoC, DI, AOP, MVC |
| **Spring 2.0** | 2006 | JDK 1.4 | AspectJ, XML config, JPA/JMS |
| **Spring 3.0** | 2009 | JDK 5 | REST, SpEL, annotations |
| **Spring 4.0** | 2013 | JDK 6–8 | Java 8, WebSocket, caching |
| **Spring 5.0** | 2017 | JDK 8–11 | WebFlux, Kotlin, reactive |
| **Spring 5.3** | 2020 | JDK 8–21 | LTS, Java EE 8 |
| **Spring 6.0** | 2022 | JDK 17–21 | Jakarta EE 9+, cloud-native |
| **Spring 6.2** | 2025 | JDK 17–25 | Performance, observability |
| **Spring 7.0** | 2026 | JDK 25 | Jakarta EE 11, modern reactive |

## 5. Spring Boot

**Spring Boot** is a project built on top of the Spring Framework that makes it easier to create stand‑alone, production‑ready Spring applications with minimal configuration. It eliminates boilerplate setup and provides opinionated defaults, so developers can focus on business logic rather than infrastructure.  

### A. Key Features of Spring Boot

- **Auto-Configuration**  
  Automatically configures beans based on classpath settings, properties, and environment.  
  Example: If `spring-boot-starter-data-jpa` is on the classpath, it configures JPA automatically.

- **No XML Configuration**
  Uses Java-based annotations (@SpringBootApplication) instead of XML.

- **Spring Boot Starters**  
  Predefined dependency bundles for common use cases.  
  Examples: `spring-boot-starter-web`, `spring-boot-starter-data-jpa`, `spring-boot-starter-security`.

- **Embedded Servers**  
  Ships with Tomcat, Jetty, or Undertow embedded, so you can run apps with `java -jar` without external server setup.

- **Standalone Applications**
   No need for external servers (like Tomcat).
   Comes with embedded servers (Tomcat, Jetty, or Undertow).
   Runs as a self-contained JAR (Java Archive) with java -jar.

- **Spring Boot CLI**  
  Command-line tool to quickly prototype apps using Groovy scripts.

- **Actuator**  
  Provides production-ready features like health checks, metrics, auditing, and monitoring endpoints.

- **Spring Boot DevTools**  
  Enables hot reloading, live reload, and developer productivity enhancements.

- **Externalized Configuration**  
  Centralized configuration via `application.properties` or `application.yml`, environment variables, and command-line arguments.

## 6. Spring Boot vs Spring Framework

| **Aspect** | **Spring Framework** | **Spring Boot** |
|------------|----------------------|-----------------|
| **Setup** | Manual configuration | Auto-configuration |
| **Dependencies** | Add individually | Starters bundle dependencies |
| **Server** | Needs external server | Embedded Tomcat/Jetty/Undertow |
| **Monitoring** | Manual setup | Actuator endpoints |
| **Focus** | Flexible, modular | Rapid development, production-ready |

| **Spring Boot Version** | **Release Year** | **Spring Framework Version** | **Java Support** | **Key Features** |
| --- | --- | --- | --- | --- |
| **[Spring Boot 1.x](ca://s?q=Spring_Boot_1.x_Features)** | 2014–2016 | Spring Framework 4.x | Java 6–8 | Initial release, embedded Tomcat/Jetty, auto-configuration |
| **[Spring Boot 2.x](ca://s?q=Spring_Boot_2.x_Features)** | 2018–2020 | Spring Framework 5.x | Java 8–14 | WebFlux (reactive), Kotlin support, actuator improvements |
| **[Spring Boot 2.7](ca://s?q=Spring_Boot_2.7_Features)** | 2022 | Spring Framework 5.3.x | Java 8–17 | Last 2.x branch, long-term support |
| **[Spring Boot 3.0](ca://s?q=Spring_Boot_3.0_Features)** | 2022 | Spring Framework 6.0 | Java 17+ | Migration to ``jakarta.*``, native image support, observability |
| **[Spring Boot 3.2](ca://s?q=Spring_Boot_3.2_Features)** | 2024 | Spring Framework 6.1 | Java 17–21 | Enhanced AOT (Ahead-of-Time) compilation, improved observability |
| **[Spring Boot 3.3](ca://s?q=Spring_Boot_3.3_Features)** | 2025 | Spring Framework 6.2 | Java 17–25 | Performance tuning, cloud-native enhancements |
| **[Spring Boot 7.x](ca://s?q=Spring_Boot_7.x_Features)** | 2026 | Spring Framework 7.0 | Java 25 | Latest release, Jakarta EE 11 support, advanced reactive features |

## 7. Features of Spring Boot Parent
  - **Dependency Management** → Automatically manages versions of Spring and third-party libraries.
  - **Plugin Management** → Configures Maven plugins like maven-compiler-plugin, maven-surefire-plugin.
  - **Default Properties** → Provides default encoding, Java version, and resource filtering.
  - **Inheritance** → Child projects inherit these defaults, reducing boilerplate.

   ```maven
   <parent>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-parent</artifactId>
       <version>3.3.0</version>
       <relativePath/> <!-- lookup parent from repository -->
   </parent>
   ```

## 8. When to use What?

| **Choice** | **Best Use Case** | **Why It Fits** |
| --- | --- | --- |
| **[Spring Framework](ca://s?q=Spring_Framework_Use_Cases)** | Full control over configurations | You want to manually configure beans, XML, or annotations for maximum flexibility |
|  | Working with legacy systems requiring XML | Many older enterprise apps still rely on XML-based configuration |
|  | Building highly customized architectures | Complex, non-standard setups where Boot’s opinionated defaults don’t fit |
| **[Spring Boot](ca://s?q=Spring_Boot_Use_Cases)** | Rapid prototyping or microservices development | Auto-configuration + starters make setup fast |
|  | Avoiding boilerplate configuration | Boot removes repetitive XML/Java config |
|  | Need embedded servers or Actuator for monitoring | Run apps with ``java ``-jar`` and get health/metrics endpoints out of the box |


## 9. Spring ApplicationContext 

### Definition
- **ApplicationContext** is the **central interface** in Spring for providing configuration information to the application.  
- It manages **Spring beans**, their lifecycle, and dependencies.  
- It’s an advanced version of **BeanFactory** with extra enterprise features.


### Key Responsibilities
- **Bean Factory** → Manages bean creation and lifecycle.  
- **Dependency Injection** → Automatically wires dependencies.  
- **Event Propagation** → Publishes and listens to application events.  
- **Internationalization** → Supports message sources for i18n.  
- **Environment Access** → Provides property values (`application.properties`).  
- **Profiles** → Activates beans conditionally using `@Profile`.


### Creating ApplicationContext
#### Spring Boot Style
```java
ApplicationContext context = SpringApplication.run(AppConfig.class);
ApplicationContext context = SpringApplication.run(AppConfig.class, args);

SpringApplication app = new SpringApplication(AppConfig.class);
context = app.run(args);
```

#### Traditional Ways
- **AnnotationConfigApplicationContext** → `new AnnotationConfigApplicationContext(AppConfig.class)`  
- **ClassPathXmlApplicationContext** → `new ClassPathXmlApplicationContext("com/example/app-config.xml")`  
- **FileSystemXmlApplicationContext** → `new FileSystemXmlApplicationContext("C:/Users/app-config.xml")`


### Obtaining Beans
```java
MyService service = context.getBean(MyService.class);
service.doWork();
```
- Beans can be fetched by **type** or **name**.  
- Each bean has a **unique identifier**.


### Summary Table

| Feature | Description |
|---------|-------------|
| **Bean Factory** | Manages bean lifecycle |
| **Dependency Injection** | Injects dependencies automatically |
| **Event Propagation** | Publishes/listens to events |
| **Internationalization** | Supports i18n message sources |
| **Environment Access** | Reads properties |
| **Profiles** | Conditional bean activation |


### Exam-Oriented Points
- ApplicationContext is **container + enterprise features**.  
- Ensures beans are created in the **right order**.  
- Supports **DI, events, profiles, i18n, environment access**.  
- Can be used in **unit tests** for lightweight context loading.  
- Eliminates need for a **full Java EE server**.

## 10. Bean Scopes
- Spring manages lifecycle of beans, each bean has its scope
- Default scope is singleton - one instance per application context
- If none of the Spring scopes is appropriate, custom scopes can be defined
- Scope can be defined by @Scope (eg. @Scope(BeanDefinition.SCOPE_SINGLETON)) annotation on the class-level of bean class

#### Available Scopes

- **Singleton**  
  *One instance per ApplicationContext (default).*  
  🔹 Example: A **database connection pool manager** or **service class** like `UserService`.  
  → You want one shared instance across the app to avoid multiple pools or duplicate logic.

- **Prototype**  
  *New instance every time bean is requested.*  
  🔹 Example: A **PDF generator** or **email builder**.  
  → Each request needs a fresh object with different content, so prototype scope makes sense.

- **Request** (Web only)  
  *One instance per HTTP request.*  
  🔹 Example: A **form‑backing bean** in a Spring MVC controller.  
  → Each HTTP request gets its own bean to hold request‑specific data.

- **Session** (Web only)  
  *One instance per user session.*  
  🔹 Example: A **shopping cart bean** in an e‑commerce site.  
  → Each user’s cart persists across multiple requests until the session ends.

- **Global‑Session** (Portlet environment only)  
  *One global session shared among all portlets.*  
  🔹 Example: A **user profile bean** shared across multiple portlets in a portal application.  
  → Rarely used today, but relevant in legacy portlet systems.

- **Custom Scope**  
  *Define your own lifecycle rules.*  
  🔹 Example: A **tenant‑specific bean** in a multi‑tenant SaaS app.  
  → Each tenant gets its own bean instance, managed by a custom scope.

- **Additional Scopes (Spring Web Flow)**  
  *Conversation scope, etc.*  
  🔹 Example: A **wizard‑style form bean** that persists across multiple steps in a flow.  
  → Not needed for certification, but useful in complex workflows.


#### Quick Cheat Sheet

| Scope | Real‑World Example | Why Useful |
|-------|-------------------|------------|
| Singleton | Database pool, Service class | Shared, efficient |
| Prototype | Report generator, Email builder | Fresh per use |
| Request | Form bean | Request‑specific data |
| Session | Shopping cart | User session persistence |
| Global‑Session | User profile in portlets | Shared across portlets |
| Custom | Tenant‑specific bean | Multi‑tenant lifecycle |


```java
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;
import org.springframework.web.context.WebApplicationContext;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
public class ScopeWebDemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(ScopeWebDemoApplication.class, args);
    }
}

@Component
@Scope("singleton")   // default
class SingletonBean {
    private int counter = 0;
    public int increment() { return ++counter; }
}

@Component
@Scope("request")     // new bean per HTTP request
class RequestBean {
    private int counter = 0;
    public int increment() { return ++counter; }
}

@Component
@Scope(WebApplicationContext.SCOPE_SESSION) // one bean per user session
class SessionBean {
    private int counter = 0;
    public int increment() { return ++counter; }
}

@RestController
@RequiredArgsConstructor   // Lombok generates constructor for final fields
//Since Spring 4.3, in Spring Boot, if a bean is a constructor parameter of another bean, Spring automatically injects it — no @Autowired needed.
class DemoController {
    private final SingletonBean singletonBean;
    private final RequestBean requestBean;
    private final SessionBean sessionBean;

    @GetMapping("/test")
    public String testScopes() {
        return "Singleton: " + singletonBean.increment() +
               " | Request: " + requestBean.increment() +
               " | Session: " + sessionBean.increment();
    }
}
```

## 11. Bean Lifecycle 

**The Spring Bean Lifecycle describes the journey of a bean inside the IoC container: from instantiation, dependency injection, initialization, usage, and finally destruction. Understanding this sequence is critical for managing resources, debugging autowiring issues, and customizing bean behavior.**

### Lifecycle Phases
1. **Instantiation**  
   - Bean object is created via constructor or factory method.  
   - At this stage, dependencies are not yet injected.

2. **Dependency Injection**  
   - Spring injects required dependencies (constructor, setter, or field injection).  
   - Autowired fields are populated here.

3. **Aware Callbacks**  
   - If bean implements `BeanNameAware`, `ApplicationContextAware`, etc., Spring calls these methods to provide contextual information.

4. **BeanPostProcessor Pre-Initialization**  
   - `postProcessBeforeInitialization()` runs.  
   - Framework-level hooks (e.g., proxy preparation) happen here.

5. **Initialization Callbacks**  
   - `@PostConstruct` annotated methods run.  
   - `InitializingBean.afterPropertiesSet()` executes if implemented.  
   - Custom init method (`@Bean(initMethod="...")`) runs.  
   - Bean is now fully initialized.

6. **BeanPostProcessor Post-Initialization**  
   - `postProcessAfterInitialization()` runs.  
   - AOP proxies are often created here.

7. **Bean Ready for Use**  
   - Application can call business methods on the bean.

8. **Destruction Callbacks**  
   - Triggered when the container shuts down.  
   - `@PreDestroy` annotated methods run.  
   - `DisposableBean.destroy()` executes if implemented.  
   - Custom destroy method (`@Bean(destroyMethod="...")`) runs.  
   - Resources (threads, DB connections) are released.


### Lifecycle Summary Table

| Phase | Description | Example |
|-------|-------------|---------|
| **Instantiation** | Bean object created | Constructor call |
| **Dependency Injection** | Dependencies injected | `@Autowired` fields |
| **Aware Callbacks** | Context info provided | `ApplicationContextAware` |
| **Pre-Init** | Before init processing | `postProcessBeforeInitialization()` |
| **Initialization** | Bean setup | `@PostConstruct`, `afterPropertiesSet()` |
| **Post-Init** | After init processing | AOP proxy creation |
| **Usage** | Bean ready | Business logic execution |
| **Destruction** | Cleanup | `@PreDestroy`, `destroy()` |


### Example Code

```java
@Component
public class MyBean implements InitializingBean, DisposableBean {

    @PostConstruct
    public void init() {
        System.out.println("Bean initialized");
    }

    @Override
    public void afterPropertiesSet() {
        System.out.println("afterPropertiesSet called");
    }

    @PreDestroy
    public void cleanup() {
        System.out.println("Bean cleanup");
    }

    @Override
    public void destroy() {
        System.out.println("DisposableBean destroy called");
    }
}
```


### Notes Points
- **Default scope = singleton** → created once at container startup, destroyed at shutdown.  
- **Prototype scope** → new instance per request; destruction callbacks not called automatically.  
- **Lifecycle hooks** → `@PostConstruct` and `@PreDestroy` are most recommended.  
- **BeanPostProcessor** → critical for framework features like AOP and proxy creation.  


## 12. Spring Configuration
- can be XML or java based
- Externalized from the bean class → separation of concerns

   | Configuration Type | Example | Usage |
   | --- | --- | --- |
   | XML | ``<bean ``id="..."/>`` | Legacy apps |
   | Annotation | ``@Component``, ``@Service`` | Modern apps |
   | Java Config | ``@Configuration ``+ ``@Bean`` | Explicit bean definitions |
   | Auto-Config | Starter dependencies | Spring Boot defaults |

The **`@Value` annotation** in Spring is used to inject values into fields, method parameters, or constructor arguments directly from property sources (like `application.properties`, environment variables, or even SpEL expressions). It’s one of the simplest ways to externalize configuration.


## 13. `@Value` Annotation — Complete Notes

### Purpose
- **Property Injection** → Inject values from `application.properties` or `application.yml`.  
- **Environment Variables** → Read system/env variables.  
- **SpEL Expressions** → Evaluate Spring Expression Language (SpEL).  
- **Default Values** → Provide fallback if property is missing.

Perfect — let’s dive into **`@ConfigurationProperties`**, which is the more powerful alternative to `@Value` when you need to bind **structured configuration** (groups of related properties) into strongly typed Java objects.


## 14. `@ConfigurationProperties` — Complete Notes

### Purpose
- **Property Binding** → Maps hierarchical properties from `application.properties` or `application.yml` into POJOs.  
- **Type-Safe Configuration** → Ensures compile-time safety with strongly typed fields.  
- **Bulk Injection** → Injects groups of related properties at once (instead of multiple `@Value` annotations).  
- **Profiles Support** → Works seamlessly with Spring profiles.



### Usage Example

#### 1. **Define Properties**
```yaml
app:
  name: MySpringApp
  timeout: 5000
  servers:
    - server1
    - server2
    - server3
```

#### 2. **Create POJO**
```java
@Component
@ConfigurationProperties(prefix = "app")
public class AppProperties {
    private String name;
    private int timeout;
    private List<String> servers;

    // getters and setters
}
```

#### 3. **Use in Service**
```java
@Service
public class MyService {
    private final AppProperties appProperties;

    public MyService(AppProperties appProperties) {
        this.appProperties = appProperties;
    }

    public void printConfig() {
        System.out.println(appProperties.getName());
        System.out.println(appProperties.getTimeout());
        System.out.println(appProperties.getServers());
    }
}
```



### Comparison: `@Value` vs `@ConfigurationProperties`

| Feature | **@Value** | **@ConfigurationProperties** |
|---------|----------------|----------------|
| Scope | Single property injection | Bulk binding of related properties |
| Type Safety | Weak (string-based) | Strong (POJO fields) |
| Structure | Flat | Hierarchical (nested objects, lists, maps) |
| Best Use | Simple values | Complex configs (DB, API, app settings) |



### Notes
- `@ConfigurationProperties` is **preferred** for structured configs.  
- Requires **getter/setter methods** (or Lombok `@Data`).  
- Can bind to **nested objects** and **collections**.  
- Works with **profiles** for environment-specific configs.  
- More maintainable than scattering multiple `@Value` annotations.  

#### Usage Examples

##### 1. **Injecting from `application.properties`**
```properties
app.name=MySpringApp
app.timeout=5000
```

```java
@Component
public class MyService {
    @Value("${app.name}")
    private String appName;

    @Value("${app.timeout}")
    private int timeout;
}
```


##### 2. **Default Values**
```java
@Value("${app.version:1.0}")
private String version; // Defaults to 1.0 if not set
```


##### 3. **Environment Variables**
```java
@Value("${JAVA_HOME}")
private String javaHome;
```


##### 4. **SpEL (Spring Expression Language)**
```java
@Value("#{2 * 1024}")
private int bufferSize; // 2048

@Value("#{systemProperties['user.name']}")
private String userName;
```


##### 5. **Injecting Lists/Arrays**
```properties
app.servers=server1,server2,server3
```

```java
@Value("${app.servers}")
private List<String> servers;
```


### Summary Table

| Feature | Example | Description |
|---------|---------|-------------|
| **Property Injection** | `@Value("${app.name}")` | Reads from `application.properties` |
| **Default Values** | `@Value("${app.version:1.0}")` | Fallback if missing |
| **Environment Variables** | `@Value("${JAVA_HOME}")` | Reads system env |
| **SpEL Expressions** | `@Value("#{2*1024}")` | Dynamic evaluation |


### Exam-Oriented Notes
- `@Value` is **field-level injection** (simpler than `@ConfigurationProperties`).  
- Best for **simple values** (strings, numbers, booleans).  
- For **complex structured configs**, prefer `@ConfigurationProperties`.  
- Supports **SpEL**, making it very flexible.  
- Works with **constructor injection** too.
