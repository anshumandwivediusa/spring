# Microservices
## 1. What are Microservices?
- **Microservices** → An architectural style where an application is broken into **small, independent services**.  
- Each service handles a **specific business capability** (e.g., user management, payment, inventory), domains, team capabilities.  
- Services communicate via **lightweight protocols** (usually HTTP/REST, gRPC, or messaging).  

<p align = "center">
<img width="500" height="400" alt="image" src="https://github.com/user-attachments/assets/45069518-fea8-4048-a761-74f72404e6e4" />
</p>

### Key Characteristics
 ```                   
                    Client (Web/Mobile)
                            │
                            │
                     API Gateway
                            │
      ┌──────────────┬──────────────┬──────────────┐
      │              │              │              │
 User Service   Product Service  Order Service  Payment Service
      │              │              │              │
      │              │              │              │
 User DB       Product DB      Order DB      Payment DB
                                           │
                                           │
                                  Notification Service
                                           │
                                    Email / SMS
 ```                                  
- **Loosely Coupled** → Services can evolve independently.  
- **Independent Deployment** → Each service can be deployed without affecting others.  
- **Polyglot Development** → Different services can use different languages/frameworks (Java, Python, Node.js, Go).  
- **Scalability** → Scale only the services that need more resources.  
- **Resilience** → Failure in one service doesn’t crash the whole system.  

### Example Scenario
Imagine an **e-commerce application** split into microservices:
- **User Service** → handles registration, login.  
- **Product Service** → manages catalog.  
- **Order Service** → processes orders.  
- **Payment Service** → handles transactions.  
- **Notification Service** → sends emails/SMS.  

Each service runs independently, communicates via APIs, and can be scaled separately.  


### Benefits vs Challenges

| **Aspect** | **Benefits** | **Challenges** |
|------------|--------------|----------------|
| **Development** | Faster, parallel development | Requires skilled teams |
| **Deployment** | Independent updates | Complex CI/CD pipelines |
| **Scalability** | Scale specific services | Distributed system complexity |
| **Resilience** | Fault isolation | Network latency, monitoring overhead |


## 2. API Gateway

The **API Gateway pattern** is one of the most important **microservice patterns** because it provides a single-entry point for all client requests into a microservices system. Instead of clients calling each service directly, they interact with the gateway, which handles routing, security, and aggregation.  

- **Without API Gateway** → Clients must call each microservice directly (complex, error-prone).  
- **With API Gateway** → Clients call one endpoint, gateway handles the rest.
 
### Key Features of API Gateway
- **Single Entry Point** → Clients don’t need to know about individual services.  
- **Routing** → Directs requests to the appropriate microservice.  
- **Aggregation** → Combines responses from multiple services into one.  
- **Security** → Handles authentication, authorization, SSL termination.  
- **Cross-cutting concerns** → Logging, monitoring, rate limiting, caching.  

### Example Flow
1. Client (Web/Mobile) sends request → API Gateway.  
2. Gateway authenticates request, applies policies.  
3. Gateway routes request to **User Service**, **Order Service**, or **Payment Service**.  
4. Gateway aggregates responses if needed and sends back to client.  

### Benefits vs Challenges

| **Benefits** | **Challenges** |
|--------------|----------------|
| Simplifies client communication | Gateway can become a bottleneck |
| Centralized security & monitoring | Adds extra hop (latency) |
| Enables protocol translation (REST ↔ gRPC) | Requires high availability |
| Supports aggregation | Complexity in configuration |

### Example Implementation
- **Spring Cloud Gateway** (Java/Spring ecosystem)  
- **Netflix Zuul** (legacy, but widely known)  
- **Kong, NGINX, Traefik** (open-source gateways)  
- **AWS API Gateway, Azure API Management** (cloud-native solutions)  

### Azure API Management Capabilities
  - **API Gateway** → Acts as a single-entry point for APIs, handling routing, request/response transformation, and protocol translation.
  - **Security** → Provides authentication, authorization, OAuth 2.0, JWT validation, IP filtering, and SSL termination.
  - **Rate Limiting & Quotas** → Control API usage with throttling, quotas, and policies to prevent abuse.
  - **Transformation** → Modify requests/responses (e.g., format conversion, header injection, payload reshaping).
  - **Monitoring & Analytics** → Built-in dashboards for usage metrics, performance, and error tracking.
  - **Developer Portal** → Self-service portal for API documentation, onboarding, and testing.
  - **Versioning & Revision Control** → Manage multiple API versions and revisions without breaking clients.
  - **Caching** → Improve performance by caching responses at the gateway.
  - **Policy Engine** → Apply reusable policies (security, transformation, validation) declaratively.
  - **Hybrid & Multi-cloud Support** → Deploy APIs across Azure, on-premises, or other clouds.
  - **Integration** → Works seamlessly with Azure services (Functions, Logic Apps, Event Grid, Service Bus).


## 2. Microservice Patterns

<img width="1024" height="1536" alt="image" src="https://github.com/user-attachments/assets/a3da106b-2e09-4866-9159-273521968d1b" />

## 3. Azure Microservice
That infographic perfectly captures the **layered architecture** for Azure‑based microservices. Here’s how to interpret it and where each component fits:  

### Flow Breakdown
1. **Azure Front Door**  
   - Global entry point for HTTP(S) traffic.  
   - Provides global load balancing, SSL offload, and edge caching.  
   - Routes requests to the nearest region.  

2. **Application Gateway**  
   - Regional layer with **Web Application Firewall (WAF)**.  
   - Handles TLS termination, URL routing, and protection against attacks.  
   - Operates at Layer 7 (HTTP/HTTPS).  

3. **Azure API Management**  
   - Governance and lifecycle layer for APIs.  
   - Adds authentication, rate limiting, transformation, and analytics.  
   - Provides a **developer portal** and manages API versions.  
   - Ideal for external or partner‑facing APIs.  

4. **API Gateway (Ingress)**  
   - Local routing inside your **AKS cluster**.  
   - Handles retries, circuit breaking, and internal service discovery.  
   - Operates at the microservice boundary.  

5. **Microservices Boundary**  
   - Individual services (User, Order, Payment, etc.) running in AKS pods.  
   - Communicate via internal DNS or service mesh (Istio, Linkerd).  


### Layer Responsibilities

| **Layer** | **Purpose** | **Scope** |
|------------|--------------|------------|
| **Front Door** | Global routing, CDN, SSL offload | Multi‑region |
| **Application Gateway** | Regional WAF, routing | Region |
| **API Management** | API governance, analytics, developer portal | Enterprise‑wide |
| **API Gateway (Ingress)** | Internal routing, retries, service discovery | Cluster |
| **Microservices** | Business logic | Pod level |


### Easy Way to Remember
- **Front Door** → Global traffic manager.  
- **App Gateway** → Regional security shield.  
- **API Management** → Strategic governance layer.  
- **API Gateway** → Local router for microservices.  
