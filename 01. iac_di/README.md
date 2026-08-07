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

## Historical Context
- **Introduced in 2003** to simplify the complexity of J2EE.  
- Became popular for its lightweight container and ease of testing.  
- **Spring 6.0 (2022)** requires **Java 17+**, aligning with modern JVM features.  [docs.spring.io](https://docs.spring.io/spring-framework/reference/overview.html)  

