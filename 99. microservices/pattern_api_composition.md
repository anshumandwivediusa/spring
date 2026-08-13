# API Composition

**The API Composition pattern is a microservices design approach where a dedicated layer aggregates data from multiple services into a single unified response, reducing client complexity and improving efficiency.** It’s especially useful when clients need data spread across different services, such as order details, customer info, and shipping status.


## What is API Composition?
- **Definition**: A pattern that combines responses from multiple microservices into one API endpoint.  
- **Purpose**: Simplifies client-side logic by avoiding multiple service calls.  
- **Mechanism**: A composer service or API gateway orchestrates calls, merges results, and returns a unified payload.  


## How It Works
1. **Client Request** → Sent to a single composition endpoint.  
2. **Composer Layer** → Fans out requests to relevant microservices.  
3. **Aggregation** → Collects responses and merges them.  
4. **Unified Response** → Returned to the client in one call.  

Example:  
- **Order Service** → Order details  
- **Customer Service** → Customer info  
- **Product Service** → Product details  
- **Shipping Service** → Delivery status  
→ Composer merges all into one response.  [codelit.io](https://codelit.io/blog/api-composition-pattern)  


## Comparison Table

| **Aspect** | **Client-Side Composition** | **API Composition Pattern** |
|------------|-----------------------------|-----------------------------|
| **Complexity** | Client must handle multiple calls | Centralized in composer |
| **Performance** | Multiple round trips | Single aggregated response |
| **Coupling** | Client tightly coupled to services | Client decoupled |
| **Failure Handling** | Client must manage retries | Composer manages resilience |
| **Best Use Case** | Simple apps | Complex microservices |


## Benefits
- **Single Entry Point**: Clients interact with one endpoint.  
- **Reduced Complexity**: No need for client-side aggregation.  
- **Flexibility**: Different clients (web, mobile) can get tailored responses.  
- **Performance**: Parallel calls reduce latency.  


## Challenges
- **Gateway Bloat**: Risk of putting too much business logic in API gateway.  
- **Single Point of Failure**: If composer fails, all requests fail.  
- **Consistency**: Handling partial failures across services.  


## Notes
- Works well with **GraphQL**, where resolvers fetch data from multiple services.  
- Often used in **Backend for Frontend (BFF)** designs to tailor responses per client type.  
- Keep the composer **thin**—only coordinate and merge, avoid embedding business logic.  [blog.singhabhinav.in](https://blog.singhabhinav.in/microservices-patterns-api-composition-1a115c78237a)  

---
