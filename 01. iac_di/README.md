# Foundations of Spring Framework

**The foundations of the Spring Framework lie in its core principles of *Dependency Injection (DI)*, *Inversion of Control (IoC)*, and a *modular architecture* that simplifies enterprise Java development by handling infrastructure concerns so developers can focus on business logic.** It provides a lightweight container, integrates seamlessly with Java EE/Jakarta EE, and supports both traditional MVC and modern reactive programming models.

[docs.spring.io](https://docs.spring.io/spring-framework/reference/overview.html)  [docs.spring.io](https://docs.spring.io/spring-framework/docs/4.3.26.RELEASE/spring-framework-reference/html/overview.html)  

## 1. Core Foundations of Spring Framework

### **Dependency Injection (DI) & Inversion of Control (IoC)**
* **Inversion of Control (IoC)** is the _principle_ in which the framework manages the creation, configuration, and lifecycle of application objects (beans).
* **Dependency Injection (DI)** is the _mechanism_ Spring uses to implement IoC by automatically providing the required dependencies to objects.
* This approach promotes **loose coupling**, improves maintainability, and makes applications easier to test.
* **Example:** A service class does not create its repository using `new`. Instead, Spring injects the repository into the service through constructor, setter, or field injection.

### **Modular Architecture**
Spring is divided into ~20 modules grouped into:
- **Core Container** → `spring-core`, `spring-context`, `spring-beans` (DI, IoC).  
- **Data Access/Integration** → JDBC, ORM, Transactions.  
- **Web** → Spring MVC, WebFlux (reactive).  
- **AOP** → Aspect-Oriented Programming for cross-cutting concerns.  
- **Messaging** → Integration with JMS, Kafka, RabbitMQ.  
- **Testing** → JUnit/TestNG support.  [docs.spring.io](https://docs.spring.io/spring-framework/docs/4.3.26.RELEASE/spring-framework-reference/html/overview.html)  

### **POJO-based Development**
- Applications are built using **Plain Old Java Objects**.  
- Enterprise services (transactions, security, messaging) can be applied without forcing inheritance from framework classes.

### **Spring MVC & WebFlux**
- **Spring MVC** → Servlet-based, synchronous request handling.  
- **Spring WebFlux** → Reactive, non-blocking, built on Reactor for high scalability.

### **Integration with Java EE/Jakarta EE**
- Spring complements EE by integrating selected APIs (JPA, JMS, JMX) rather than adopting the full specification.  [docs.spring.io](https://docs.spring.io/spring-framework/reference/overview.html)  

## 2. Inversion of Control (IoC)
- **Definition:** A design principle where the control of object creation and dependency management is inverted from the application code to a container/framework.  
- **Impact:** Instead of classes instantiating their dependencies directly, the framework provides them.
- **Benefit:** Promotes loose coupling, easier testing, and better separation of concerns.

<p align = "center">
<img width="400" height="300" alt="image" src="https://github.com/user-attachments/assets/44b1f90d-ab49-4ab3-a1cd-fa44cf1edf46" />
</p>


### Two Main Approaches
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

### Comparison Table

| Aspect | **Dependency Injection (DI)** | **Dependency Lookup (DL)** |
|--------|-------------------------------|-----------------------------|
| Control | Framework injects dependencies | Object requests dependencies |
| Coupling | Loose coupling | Tighter coupling |
| Testability | High | Lower |
| Examples | Spring IoC container | Service Locator, JNDI |

### Quick Notes
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

## Spring Boot

**Spring Boot** is a project built on top of the Spring Framework that makes it easier to create stand‑alone, production‑ready Spring applications with minimal configuration. It eliminates boilerplate setup and provides opinionated defaults, so developers can focus on business logic rather than infrastructure.  

### Key Features of Spring Boot

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

- **Spring Boot CLI**  
  Command-line tool to quickly prototype apps using Groovy scripts.

- **Actuator**  
  Provides production-ready features like health checks, metrics, auditing, and monitoring endpoints.

- **Spring Boot DevTools**  
  Enables hot reloading, live reload, and developer productivity enhancements.

- **Externalized Configuration**  
  Centralized configuration via `application.properties` or `application.yml`, environment variables, and command-line arguments.

### Spring Boot vs Spring Framework

| **Aspect** | **Spring Framework** | **Spring Boot** |
|------------|----------------------|-----------------|
| **Setup** | Manual configuration | Auto-configuration |
| **Dependencies** | Add individually | Starters bundle dependencies |
| **Server** | Needs external server | Embedded Tomcat/Jetty/Undertow |
| **Monitoring** | Manual setup | Actuator endpoints |
| **Focus** | Flexible, modular | Rapid development, production-ready |

