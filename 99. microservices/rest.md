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

    | **Method** | **Purpose** | **Conceptual Reason** | **Example Use Case** |
    | --- | --- | --- | --- |
    | **[GET](ca://s?q=HTTP_GET_method)** | Retrieve a resource | Safe, idempotent; does not change server state | ``GET ``/customers/123`` → fetch customer details |
    | **[POST](ca://s?q=HTTP_POST_method)** | Create a new resource | Non-idempotent; each call creates something new | ``POST ``/customers`` → add a new customer |
    | **[PUT](ca://s?q=HTTP_PUT_method)** | Update/replace a resource | Idempotent; repeated calls yield same result | ``PUT ``/customers/123`` → update customer info |
    | **[PATCH](ca://s?q=HTTP_PATCH_method)** | Partially update a resource | Idempotent; modifies only specified fields | ``PATCH ``/customers/123`` → update just the email |
    | **[DELETE](ca://s?q=HTTP_DELETE_method)** | Remove a resource | Idempotent; repeated calls have same effect | ``DELETE ``/customers/123`` → delete customer |
    | **[HEAD](ca://s?q=HTTP_HEAD_method)** | Retrieve metadata only | Same as GET but without body | ``HEAD ``/customers/123`` → check if resource exists |
    | **[OPTIONS](ca://s?q=HTTP_OPTIONS_method)** | Discover supported methods | Used for CORS and API introspection | ``OPTIONS ``/customers`` → see allowed operations |

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
