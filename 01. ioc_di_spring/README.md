# Foundations of Spring Framework

**The foundations of the Spring Framework lie in its core principles of *Dependency Injection (DI)*, *Inversion of Control (IoC)*, and a *modular architecture* that simplifies enterprise Java development by handling infrastructure concerns so developers can focus on business logic.** It provides a lightweight container, integrates seamlessly with Java EE/Jakarta EE, and supports both traditional MVC and modern reactive programming models.

[docs.spring.io](https://docs.spring.io/spring-framework/reference/overview.html)  [docs.spring.io](https://docs.spring.io/spring-framework/docs/4.3.26.RELEASE/spring-framework-reference/html/overview.html)  

## 1. Core Foundations of Spring Framework

<p align = "center">
   <img width="500" height="366" alt="image" src="https://github.com/user-attachments/assets/d21c3554-a21d-47e4-be19-7a826c763c3f" />
</p>

### A. **Dependency Injection (DI) & Inversion of Control (IoC)**
* **Inversion of Control (IoC)** is the _principle_ in which the framework manages the creation, configuration, and lifecycle of application objects (beans).
* **Dependency Injection (DI)** is the _mechanism_ Spring uses to implement IoC by automatically providing the required dependencies to objects.
* This approach promotes **loose coupling**, improves maintainability, and makes applications easier to test.
* **Example:** A service class does not create its repository using `new`. Instead, Spring injects the repository into the service through constructor, setter, or field injection.

### B. **Modular Architecture**
Spring is divided into ~20 modules grouped into:
- **Core Container** → `spring-core`, `spring-context`, `spring-beans` (DI, IoC).  
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
- **Definition:** A design principle where the control of object creation and dependency management is inverted from the application code to a container/framework.  
- **Impact:** Instead of classes instantiating their dependencies directly, the framework provides them.
- **Benefit:** Promotes loose coupling, easier testing, and better separation of concerns.

<p align = "center">
<img width="400" height="300" alt="image" src="https://github.com/user-attachments/assets/44b1f90d-ab49-4ab3-a1cd-fa44cf1edf46" />
</p>


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
| **Module Group** | **Key Modules** | **Features** |
| --- | --- | --- |
| **Core Container** | Core, Beans, Context, SpEL | IoC, DI, bean lifecycle, runtime expressions |
| **Data Access** | JDBC, ORM, OXM, JMS, Transactions | Database access, ORM integration, messaging, transactions |
| **Web** | Web, MVC, WebFlux, WebSocket, Web Services | Traditional & reactive web apps, REST/SOAP, real-time communication |
| **AOP** | Spring AOP, AspectJ | Cross-cutting concerns, declarative transactions |
| **Instrumentation** | Instrument | Class instrumentation, classloader support |
| **Messaging** | Messaging | Async messaging, WebSocket/STOMP |
| **Testing** | Test | JUnit/TestNG integration, context caching |

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


## Spring ApplicationContext — Complete Notes

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
