# Internal Working of Spring Boot

## 1. What Happens When We Start a Spring Boot Application?

A typical Spring Boot application starts with the following code:

```java
@SpringBootApplication
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

The call to `SpringApplication.run()` initiates the Spring Boot startup process. It creates and configures the Spring application, initializes the Spring IoC container, applies auto-configuration, and starts the application.

## 2 Step-by-Step Startup Process

### Step 1: `@SpringBootApplication`

`@SpringBootApplication` is a convenience annotation that combines three commonly used annotations:

```java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan
```

Each annotation has a specific role:

* **`@SpringBootConfiguration`** marks the class as a configuration class (equivalent to `@Configuration`).
* **`@EnableAutoConfiguration`** enables Spring Boot's auto-configuration mechanism.
* **`@ComponentScan`** scans the current package and its subpackages for Spring-managed components.

Together, they tell Spring Boot to:

* scan for application components,
* register configuration classes,
* apply auto-configuration based on the application's dependencies.


### Step 2: `SpringApplication.run()`

`SpringApplication.run()` is responsible for bootstrapping the application.

Internally, it performs several tasks:

1. Creates a `SpringApplication` instance.
2. Determines the application type:

   * Servlet application
   * Reactive application
   * Non-web application
3. Prepares the environment by loading:

   * `application.properties` or `application.yml`
   * command-line arguments
   * environment variables
4. Prints the Spring Boot banner (unless disabled).
5. Creates the appropriate `ApplicationContext`.

For example:

* `AnnotationConfigServletWebServerApplicationContext`
* `AnnotationConfigReactiveWebServerApplicationContext`
* `AnnotationConfigApplicationContext`

The `ApplicationContext` is Spring's IoC container responsible for managing all beans.



### Step 3: Component Scanning

Once the `ApplicationContext` is created, Spring scans the packages specified by `@ComponentScan`.

It discovers classes annotated with:

* `@Component`
* `@Service`
* `@Repository`
* `@Controller`
* `@RestController`
* `@Configuration`

Each discovered class is registered as a **bean definition**.

> **Note:** At this stage, Spring primarily registers bean definitions. Bean instances are generally created later during the context refresh process.



### Step 4: Auto-Configuration

Next, Spring Boot applies auto-configuration.

`@EnableAutoConfiguration` imports configuration classes listed in:

```text
META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports
```

Each auto-configuration class is activated only when its conditions are satisfied.

Common conditional annotations include:

* `@ConditionalOnClass`
* `@ConditionalOnMissingBean`
* `@ConditionalOnBean`
* `@ConditionalOnProperty`
* `@ConditionalOnWebApplication`

Example:

```java
@Configuration
@ConditionalOnClass(DataSource.class)
@ConditionalOnMissingBean(DataSource.class)
public class DataSourceAutoConfiguration {

    @Bean
    public DataSource dataSource() {
        return new HikariDataSource();
    }
}
```

This means:

> If a `DataSource` class exists on the classpath and the application hasn't already defined a `DataSource` bean, Spring Boot automatically creates one.

Examples:

* `spring-boot-starter-web` → Embedded Tomcat, Spring MVC, `DispatcherServlet`
* `spring-boot-starter-data-jpa` → `DataSource`, JPA configuration, `EntityManagerFactory`
* `spring-boot-starter-security` → Default Spring Security configuration



### Step 5: Context Refresh and Bean Creation

After bean definitions are registered, Spring refreshes the `ApplicationContext`.

During this phase, Spring:

* creates singleton beans,
* resolves dependencies,
* performs dependency injection,
* executes lifecycle callbacks,
* initializes proxies (for AOP, transactions, etc.).

Dependency injection can occur through:

* constructor injection (**recommended**),
* setter injection,
* field injection (`@Autowired`).

Lifecycle callbacks include:

* `@PostConstruct`
* `InitializingBean`
* custom init methods

After this phase, the application context is fully initialized.



### Step 6: Embedded Web Server Startup

For servlet-based web applications, Spring Boot starts the embedded web server.

By default, the embedded server is:

* Tomcat

Other supported servers include:

* Jetty
* Undertow

During startup:

* the embedded server starts,
* `DispatcherServlet` is registered,
* request mappings are initialized,
* the application begins listening for HTTP requests.

Example request flow:

```
Client
   │
   ▼
DispatcherServlet
   │
   ▼
Controller
   │
   ▼
Service
   │
   ▼
Repository
   │
   ▼
Database
   │
   ▼
Response
```



### Step 7: Application Events

Spring Boot publishes several lifecycle events during startup.

Common events include:

* `ApplicationStartingEvent`
* `ApplicationEnvironmentPreparedEvent`
* `ApplicationPreparedEvent`
* `ApplicationStartedEvent`
* `ApplicationReadyEvent`

Developers can respond to these events using:

* `ApplicationListener`
* `@EventListener`



### Step 8: `CommandLineRunner` and `ApplicationRunner`

After the application context has been refreshed and the application has started, Spring Boot executes all beans implementing:

* `CommandLineRunner`
* `ApplicationRunner`

These are commonly used for:

* loading seed data,
* performing startup validation,
* executing initialization logic.

Example:

```java
@Component
public class StartupTask implements CommandLineRunner {

    @Override
    public void run(String... args) {
        System.out.println("Application started successfully!");
    }
}
```

### Step 9: Application Ready

At this stage:

* the `ApplicationContext` is fully initialized,
* beans have been created,
* dependencies have been injected,
* auto-configuration has completed,
* the embedded server is running,
* the application is ready to process incoming requests.

Typical startup logs:

```text
Tomcat started on port(s): 8080
Started MyApplication in 2.341 seconds
```

## 3 Interview Summary

> When a Spring Boot application starts, `SpringApplication.run()` bootstraps the application. It prepares the environment, creates the appropriate `ApplicationContext`, performs component scanning, registers bean definitions, and applies auto-configuration based on the application's dependencies. During the context refresh phase, Spring creates beans, injects dependencies, initializes lifecycle callbacks, and applies AOP proxies where needed. For web applications, Spring Boot starts an embedded web server such as Tomcat and registers the `DispatcherServlet`. Once initialization is complete, the application is ready to handle incoming requests.

## 4 Startup Flow Diagram

```text
@SpringBootApplication
        │
        ▼
SpringApplication.run()
        │
        ▼
Create SpringApplication
        │
        ▼
Prepare Environment
        │
        ▼
Create ApplicationContext
        │
        ▼
Component Scan
        │
        ▼
Register Bean Definitions
        │
        ▼
Auto-Configuration
        │
        ▼
Refresh ApplicationContext
        │
        ▼
Create Beans & Inject Dependencies
        │
        ▼
Initialize AOP & Lifecycle Callbacks
        │
        ▼
Start Embedded Web Server
        │
        ▼
Publish Startup Events
        │
        ▼
Run CommandLineRunner/ApplicationRunner
        │
        ▼
Application Ready
```

## 5 Annotations used in this chapter  

### 1. Core Spring Boot Annotations

- **@SpringBootApplication**  
  Combines `@SpringBootConfiguration`, `@EnableAutoConfiguration`, and `@ComponentScan`. Entry point for Boot apps.  

- **@SpringBootConfiguration**  
  Specialized form of `@Configuration`. Marks the class as a source of bean definitions.  

- **@EnableAutoConfiguration**  
  Triggers Spring Boot’s auto-configuration based on classpath and conditions.  

- **@ComponentScan**  
  Scans packages for beans annotated with `@Component`, `@Service`, `@Repository`, `@Controller`, etc.  

### 2. Stereotype Annotations (Component Scanning)

- **@Component** → Generic Spring-managed bean.  
- **@Service** → Business logic layer beans.  
- **@Repository** → DAO layer, exception translation.  
- **@Controller** → MVC controllers.  
- **@RestController** → Combines `@Controller` + `@ResponseBody` for REST APIs.  
- **@Configuration** → Defines beans via `@Bean` methods.  

### 3. Dependency Injection Annotations

- **@Autowired** → Injects dependencies (constructor, setter, field).  
- **@Qualifier** → Resolves ambiguity when multiple beans of same type exist.  
- **@Value** → Injects values from properties or environment.  

### 4. Conditional & Auto-Config Annotations

- **@ConditionalOnClass** → Activates config if a class is on classpath.  
- **@ConditionalOnMissingBean** → Creates bean only if not already defined.  
- **@ConditionalOnProperty** → Enables config if a property is set.  
- **@ConditionalOnWebApplication** → Activates config only for web apps.  

### 5. Lifecycle & Event Annotations

- **@PostConstruct** → Runs after bean initialization.  
- **@PreDestroy** → Runs before bean destruction.  
- **@EventListener** → Listens to application events (e.g., `ApplicationReadyEvent`).  

### 6. Runner Interfaces

- **CommandLineRunner** → Executes logic after startup with command-line args.  
- **ApplicationRunner** → Similar, but uses `ApplicationArguments`.  

### 7. How They Fit Into Startup Flow
1. `@SpringBootApplication` → Entry point, triggers auto-config + scanning.  
2. Stereotype annotations (`@Component`, `@Service`, etc.) → Beans discovered during scanning.  
3. Conditional annotations → Auto-config beans created only if conditions match.  
4. Lifecycle annotations (`@PostConstruct`) → Run during context refresh.  
5. Runner interfaces → Execute after the app is fully started.  
