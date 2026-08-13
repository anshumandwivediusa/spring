# Microservices Architecture & Design

## Overview
Microservices architecture is about building applications as a collection of **small, independent services** that communicate over lightweight protocols. Each service is designed around a **business capability** and can be developed, deployed, and scaled independently.

_**Independent executable program that represents the specific Business goal.**_

## Core Principles

- **Bounded Contexts**  
  Each service owns a well-defined domain area, avoiding overlap.

- **Single Responsibility**  
  Services should do one thing well, aligned with business functionality.

- **Loose Coupling**  
  Services interact via APIs or messaging, minimizing dependencies.

- **High Cohesion**  
  Related functionality stays together within the same service.


## Service Decomposition Patterns

- **Business Capability** — Split services by business functions (e.g., Orders, Payments).  
- **Subdomain Decomposition** — Use Domain-Driven Design (DDD) bounded contexts.  
- **Strangler Fig Pattern** — Gradually replace monolith modules with microservices.  
- **Transactional Boundaries** — Ensure services own their data and transactions.  


## Communication Design

- **Synchronous**:
  | Category | Protocol | Type | Key Features | Best Use Case |
  | --- | --- | --- | --- | --- |
  | **Synchronous (Request–Response)** | **[REST](ca://s?q=REST_in_microservices)** | HTTP/HTTPS | Human-readable JSON/XML, simple, widely adopted | CRUD APIs, public-facing services |
  |  | **[gRPC](ca://s?q=gRPC_in_microservices)** | HTTP/2 + Protobuf | High-performance, strongly typed, streaming | Internal service-to-service calls |
  |  | **[GraphQL](ca://s?q=GraphQL_in_microservices)** | HTTP | Flexible queries, client-driven data fetching | Mobile/web frontends needing tailored data |
  |  | **[WebSockets](ca://s?q=WebSockets_in_microservices)** | TCP | Full-duplex, real-time | Chat apps, live dashboards |
  |  | **[SOAP](ca://s?q=SOAP_in_microservices)** | HTTP/XML | Strict contracts, WS-* standards | Legacy enterprise integrations |
  |  | **[RSocket](ca://s?q=RSocket_in_microservices)** | TCP/WebSocket | Reactive streams, multiplexing | Reactive microservices needing backpressure |  

- **Asynchronous** 
  | Category | Protocol | Type | Key Features | Best Use Case |
  | --- | --- | --- | --- | --- |
  | **Asynchronous (Event-Driven)** | **[Kafka](ca://s?q=Kafka_in_microservices)** | Event streaming | High throughput, partitioned topics | Event-driven workflows, analytics |
  |  | **[RabbitMQ](ca://s?q=RabbitMQ_in_microservices)** | AMQP | Reliable message queuing, routing | Task distribution, background jobs |
  |  | **[MQTT](ca://s?q=MQTT_in_microservices)** | TCP | Lightweight, pub/sub, low bandwidth | IoT devices, constrained environments |
  |  | **[ActiveMQ](ca://s?q=ActiveMQ_in_microservices)** | JMS/AMQP | Enterprise-grade broker | Legacy enterprise messaging |
  |  | **[AWS SQS](ca://s?q=AWS_SQS_in_microservices)** | Cloud queue | Fully managed, scalable | Cloud-native async workflows |
  |  | **[NATS](ca://s?q=NATS_in_microservices)** | Pub/Sub | Lightweight, simple, fast | Cloud-native event bus |
  |  | **[Redis Streams](ca://s?q=Redis_Streams_in_microservices)** | In-memory | Fast, persistent streams | Real-time analytics, caching + events |

- **Service Discovery** — Dynamic lookup of service endpoints via registry (e.g., Eureka, Consul).  
- **API Gateway** — Central entry point for routing, authentication, rate limiting.  


## Data Management

- **Database per Service** — Each service owns its persistence layer.  
- **CQRS** — Separate read and write models for scalability.  
- **Event Sourcing** — Persist state as a sequence of events.  
- **Saga Pattern** — Manage distributed transactions across services.  


## Resilience Patterns

- **Circuit Breaker** — Prevent cascading failures when a service is down.  
- **Bulkhead** — Isolate resources to contain failures.  
- **Retries & Timeouts** — Handle transient errors gracefully.  
- **Fallbacks** — Provide default responses when services fail.  


## Deployment & Scaling

- **Containerization** — Package services with Docker.  
- **Orchestration** — Kubernetes for scaling, self-healing, and service mesh.  
- **CI/CD** — Automated pipelines for continuous delivery.  
- **Feature Flags** — Progressive rollout of new features.  


## Observability

- **Distributed Tracing** — Track requests across multiple services.  
- **Centralized Logging** — Aggregate logs for debugging.  
- **Metrics & Monitoring** — Prometheus, Grafana, OpenTelemetry.  
- **Service Mesh** — Sidecar proxies for traffic management, retries, and mTLS.  


## Summary

Microservices architecture & design is about:
- Breaking systems into **small, independent services**.  
- Ensuring **loose coupling and high cohesion**.  
- Using **appropriate communication protocols**.  
- Managing **data ownership and distributed transactions**.  
- Building **resilient, observable, and secure systems**.  


Here’s an updated **README-style document on Domain-Driven Design (DDD)**, now enriched with a **banking domain example** to make the standards more concrete and practical:


# Domain-Driven Design (DDD)

## Overview
Domain-Driven Design (DDD) is a software design approach that models software around the **business domain**. It emphasizes collaboration with domain experts, a shared language, and clear boundaries to ensure that complex systems reflect real-world processes.


## Core Principles
- **Domain** — The problem space (e.g., banking, healthcare).  
- **Domain Model** — Abstractions representing domain concepts.  
- **Ubiquitous Language** — Shared vocabulary between developers and domain experts.  
- **Bounded Context** — Logical boundary where a specific model applies.  
- **Context Mapping** — Defines relationships between bounded contexts.  


## Strategic Design
- **Bounded Contexts**: Divide large systems into smaller domains (Accounts, Loans, Payments).  
- **Context Mapping**: Identify dependencies and integration points.  
- **Anti-Corruption Layer**: Protects core domain from legacy systems.  
- **Shared Kernel**: Subset of the model shared across teams.  


## Tactical Design Patterns
- **Entities** — Identity-driven objects (Customer, Account).  
- **Value Objects** — Immutable descriptors (Money, Address).  
- **Aggregates** — Consistency boundaries (Account controlling Transactions).  
- **Repositories** — Persistence abstraction (`AccountRepository`).  
- **Factories** — Encapsulate complex creation logic.  
- **Domain Events** — Immutable events (`FundsTransferredEvent`).  
- **Application Services** — Coordinate domain logic.  


## Banking Domain Example

### Entities
- **Customer** — Identity-driven, lifecycle-based.  
- **Account** — Has ID, owner, balance.  
- **Loan** — Tracks repayment schedules and interest accrual.  

### Value Objects
- **Money** — Currency + amount, immutable.  
- **InterestRate** — Immutable descriptor for loan calculations.  
- **Address** — Immutable customer detail.  

### Aggregates
- **Account Aggregate**  
  - Root: `Account`  
  - Rules: Overdraft limits, transaction posting.  
  - Controls consistency of `Transaction` entities.  

- **Loan Aggregate**  
  - Root: `Loan`  
  - Rules: Repayment schedules, interest accrual.  

### Repositories
- `AccountRepository` — Fetches accounts by ID.  
- `LoanRepository` — Fetches loans by ID.  

### Domain Events
- `TransactionPostedEvent` — Triggered when a transaction is recorded.  
- `LoanApprovedEvent` — Triggered when a loan is sanctioned.  
- `AccountClosedEvent` — Triggered when an account is terminated.  


## Summary Table

| Standard | Banking Example |
|----------|-----------------|
| **Bounded Context** | Accounts, Loans, Payments, Compliance |
| **Ubiquitous Language** | “Transaction”, “Overdraft”, “KYC” |
| **Entity** | Customer, Account, Loan |
| **Value Object** | Money, InterestRate, Address |
| **Aggregate Root** | Account controlling Transactions |
| **Repository** | AccountRepository, LoanRepository |
| **Domain Event** | LoanApproved, TransactionPosted |


## Benefits
- Aligns code with **banking business rules** (interest, overdraft, compliance).  
- Facilitates integration with **legacy systems** via anti-corruption layers.  
- Improves **scalability** and **resilience** in microservices-based banking platforms.  
- Enhances communication between **developers and domain experts**.  

# Domain-Driven Design (DDD)
Transitioning from **Domain-Driven Design (DDD)** to a **Solution Architecture** means converting your conceptual domain model into real, deployable components — code, services, and infrastructure. Let’s break this down step-by-step so you can see how your *Customer* and *Order* aggregates evolve into a working system.  

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/15cf9860-9f94-4376-b67b-2ffeb7e39270" />


## Step 1: **Define Bounded Contexts**
Each context becomes a microservice or module:
| Context | Responsibility | Example Components |
|----------|----------------|--------------------|
| **Customer Management** | Handles customer profiles, identity, contact info | `CustomerService`, `CustomerRepository`, `CustomerAggregate` |
| **Order Management** | Manages order lifecycle, payments, shipping | `OrderService`, `OrderRepository`, `OrderAggregate` |
| **Billing Context** | Processes invoices, payments, refunds | `BillingService`, `PaymentGatewayAdapter` |
| **Support Context** | Tracks tickets, complaints | `SupportService`, `TicketAggregate` |


## Step 2: **Map Aggregates to Microservices**
Each Aggregate Root becomes the **core entity** of a microservice:
- `CustomerAggregate` → `CustomerService` (REST or gRPC)
- `OrderAggregate` → `OrderService`
- Communication via **domain events** or **message queues** (Kafka, RabbitMQ).


## Step 3: **Implement Repositories**
Repositories abstract persistence:
```java
public interface CustomerRepository {
    Customer findById(CustomerId id);
    void save(Customer customer);
}
```
Use JPA, Hibernate, or Spring Data for implementation.  
Each repository belongs to its bounded context.


## Step 4: **Integrate Application Services**
Application services orchestrate domain logic:
```java
@Service
public class CustomerService {
    @Transactional
    public void placeOrder(CustomerId id, OrderDetails details) {
        Customer customer = customerRepository.findById(id);
        customer.placeOrder(details);
        customerRepository.save(customer);
        domainEventPublisher.publish(new OrderPlacedEvent(id));
    }
}
```


## Step 5: **Expose APIs**
Each context exposes REST endpoints:
- `/customers/{id}`
- `/orders/{id}`
- `/billing/invoice/{id}`  
Use **OpenAPI** or **GraphQL** for schema contracts.


## Step 6: **Deploy Solution Architecture**
Typical deployment stack:
| Layer | Technology |
|-------|-------------|
| API Gateway | Spring Cloud Gateway / Kong |
| Microservices | Spring Boot |
| Messaging | Kafka / RabbitMQ |
| Persistence | PostgreSQL / MongoDB |
| Observability | Prometheus + Grafana |
| CI/CD | GitHub Actions / Jenkins |


## Step 7: **Connect Contexts via Events**
Use **event-driven architecture**:
- `OrderPlacedEvent` triggers billing and inventory updates.
- `CustomerUpdatedEvent` syncs with CRM or analytics.




# Strangler Fig Pattern

In **Domain-Driven Design (DDD)**, the **Strangler Fig pattern** is a **modernization strategy** — not a modeling concept like aggregates or entities. It’s used to **incrementally replace a legacy system** with a new domain model or microservice architecture, inspired by how a strangler fig tree grows around its host until it fully replaces it.  


## How the Strangler Fig Pattern Works
| Step | Description |
|------|--------------|
| **1. Identify legacy boundaries** | Map the existing system’s modules and dependencies. Find seams where new functionality can be introduced. |
| **2. Define new bounded contexts** | Model new DDD contexts (e.g., Customer, Orders, Payments) that will gradually take over legacy responsibilities. |
| **3. Introduce a façade or proxy** | Route requests through a façade that can direct traffic either to the legacy system or the new DDD service. |
| **4. Incremental replacement** | Gradually move features from the old system into new bounded contexts. Each new context “strangles” part of the legacy. |
| **5. Decommission legacy** | Once all functionality is migrated, retire the old system completely. |


## Why It Fits DDD
- Enables **bounded context evolution** without a big-bang rewrite.  
- Supports **event-driven integration** between old and new systems.  
- Encourages **clean separation** of domain logic and infrastructure.  
- Works well with **microservices** and **API gateways** for routing.  


## Example
Imagine a legacy **Customer Management System**.  
You create a new **Customer Aggregate** (like your diagram) in a modern DDD service.  
The **API Gateway** routes new customer operations to the new service, while legacy operations still go to the old system. Over time, the new service handles all customer logic — the old one fades away.

<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/71feb1d4-1da9-4260-bc66-ec01ac62fb11" />
