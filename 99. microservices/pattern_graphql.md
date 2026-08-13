GraphQL is a **query language and runtime** for APIs that provides a flexible, efficient way for clients to request exactly the data they need—no more, no less. Unlike REST, which exposes fixed endpoints, GraphQL exposes a **single endpoint** with a schema that defines types and relationships.


## Core Concepts
- **Schema** → Defines types, queries, mutations, and subscriptions.  
- **Queries** → Read-only operations to fetch data.  
- **Mutations** → Operations that modify data.  
- **Resolvers** → Functions that fetch data for fields in the schema.  
- **Subscriptions** → Real-time updates via WebSockets.  


## How It Works
1. **Client sends query** → specifying fields needed.  
2. **GraphQL server parses query** → validates against schema.  
3. **Resolvers execute** → fetch data from DBs, services, or APIs.  
4. **Response returned** → exactly matches query shape.  

Example:
```graphql
query {
  order(id: "123") {
    id
    customer {
      name
      email
    }
    products {
      name
      price
    }
  }
}
```
Response:
```json
{
  "order": {
    "id": "123",
    "customer": {
      "name": "Alice",
      "email": "alice@example.com"
    },
    "products": [
      { "name": "Laptop", "price": 1200 },
      { "name": "Mouse", "price": 25 }
    ]
  }
}
```


## Comparison with REST

| **Aspect** | **REST** | **GraphQL** |
|------------|----------|-------------|
| **Endpoints** | Multiple | Single |
| **Data Fetching** | Fixed payloads | Client specifies fields |
| **Over-fetching** | Common | Avoided |
| **Under-fetching** | Common | Avoided |
| **Real-time** | Limited | Subscriptions supported |

## Benefits
- **Efficiency** → Fetch only required fields.  
- **Flexibility** → Clients tailor queries.  
- **Strong Typing** → Schema ensures consistency.  
- **Tooling** → Rich ecosystem (Apollo, GraphQL Playground).  


## Challenges
- **Complexity** → Requires schema design and resolvers.  
- **Caching** → Harder than REST.  
- **Security** → Must guard against overly deep queries.  
- **Performance** → N+1 query problem if resolvers aren’t optimized.  


## Notes
- GraphQL works well with **API Composition** since resolvers can aggregate data from multiple services.  
- Often paired with **CQRS** for separating queries and mutations.  
- Useful in **Microservices** and **BFF** architectures.  
