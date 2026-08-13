# Saga Pattern
The **Saga Pattern** is a microservices design pattern used to manage **distributed transactions** across multiple services without relying on a traditional two-phase commit. Instead of locking resources, it breaks a transaction into a sequence of **local transactions** coordinated through events or commands, ensuring eventual consistency.


## What is Saga?
- **Definition**: A sequence of local transactions where each step updates data within a service and publishes an event to trigger the next step.  
- **Purpose**: Handle long-running, multi-service business processes reliably.  
- **Consistency**: Achieved through compensating transactions rather than strict locks.  


## Saga Execution Styles
1. **Choreography**  
   - No central coordinator.  
   - Services listen for events and react accordingly.  
   - Example: Order Service emits `OrderCreated` → Payment Service listens and processes payment → Shipping Service listens and ships.  

2. **Orchestration**  
   - A central **orchestrator** tells each service what to do.  
   - Services execute local transactions and report back.  
   - Example: Orchestrator calls Payment Service → then Shipping Service → then Notification Service.  


## Comparison Table

| **Aspect** | **Choreography** | **Orchestration** |
|------------|------------------|-------------------|
| **Control** | Decentralized | Centralized |
| **Coupling** | Loose | Tighter |
| **Complexity** | Simple for small flows | Easier for complex flows |
| **Failure Handling** | Each service compensates | Orchestrator manages compensations |
| **Best Use Case** | Few services, simple flows | Many services, complex flows |


## Benefits
- **Resilience**: No global locks, services remain autonomous.  
- **Scalability**: Works well in distributed microservices.  
- **Flexibility**: Supports both event-driven and command-driven flows.  


## Challenges
- **Complexity**: Requires careful design of compensating transactions.  
- **Debugging**: Harder to trace across multiple services.  
- **Consistency**: Eventual consistency may confuse users.  


## Notes
- Commonly used in **Order Management**, **Payment Processing**, and **Travel Booking**.  
- Often combined with **CQRS** and **Event Sourcing**.  
- Compensating transactions must be carefully designed to undo partial work.  
