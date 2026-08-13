# Representational State Transfer (REST) — Professional Notes

## 1. Definition
**REST** is an architectural style for distributed systems, introduced by Roy Fielding in his 2000 PhD dissertation. It defines principles for building scalable, stateless, and resource-oriented web services.


## 2. Why the Name?
- **Representation**: Resources (like Customer, Order) are represented in formats such as JSON, XML, or HTML.  
- **State**: The application’s state changes when clients interact with resources (e.g., updating an order).  
- **Transfer**: These representations are transferred between client and server over HTTP.  

👉 Together, REST means **transferring the state of resources through their representations**.
    
    Related Protocols of That Era - SOAP, COBRA, RMI


## 3. Core Principles
- **Statelessness**: Each request contains all necessary information; no client session is stored on the server.  
- **Uniform Interface**: Standard HTTP methods (GET, POST, PUT, DELETE).  
- **Resource Identification**: Each resource has a unique URI.  
- **Representation-Oriented**: Clients interact with resource representations, not the resources themselves.  
- **Layered System**: Architecture can include intermediaries (proxies, gateways).  
- **Cacheable**: Responses can be cached to improve performance.  


## 4. REST vs DDD
- **Aggregates → Resources**: A `CustomerAggregate` becomes `/customers/{id}`.  
- **Repositories → Persistence Layer**: Hidden behind REST endpoints.  
- **Domain Events → Async Communication**: REST handles synchronous calls; events handle async.  
- **Bounded Contexts → Microservices**: Each context exposes its own REST API.  


## 5. Example API Contract
### Customer Resource
```http
GET /customers/123
→ Returns JSON representation of CustomerAggregate

POST /customers
→ Creates new CustomerAggregate

PUT /customers/123
→ Updates Customer details

DELETE /customers/123
→ Removes CustomerAggregate
```

### Order Resource
```http
GET /orders/456
→ Returns OrderAggregate

POST /orders
→ Places new order

PATCH /orders/456→ Updates order status
```


## 6. REST in Solution Architecture
| Layer | Role | Example |
|-------|------|---------|
| **API Gateway** | Routes requests | `/customers`, `/orders` |
| **Microservices** | Implements bounded contexts | Customer Service, Order Service |
| **Persistence** | Stores aggregates | PostgreSQL, MongoDB |
| **Messaging** | Async events | Kafka, RabbitMQ |
| **Observability** | Monitoring | Prometheus, Grafana |


## 7. Key Takeaways
- REST is **not a protocol**, but an **architectural style**.  
- It emphasizes **resource representations** and **stateless interactions**.  
- In DDD, REST is the **interface layer** that exposes aggregates to external clients.  
- The name highlights the **transfer of resource state via representations**, which is the essence of web communication.
