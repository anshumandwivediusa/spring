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

# **complete Spring Boot GraphQL example**

## Project Structure
```
src/main/java/com/example/graphql/
 ├── controller/
 │    └── GraphQLController.java
 ├── model/
 │    └── Customer.java
 ├── service/
 │    └── CustomerService.java
 ├── resolver/
 │    └── CustomerResolver.java
 └── GraphqlApplication.java
src/main/resources/
 └── schema.graphqls
```


## Schema Definition (`schema.graphqls`)
```graphql
type Customer {
    id: ID!
    name: String!
    email: String!
}

type Query {
    getCustomerById(id: ID!): Customer
    listCustomers: [Customer]
}

type Mutation {
    createCustomer(name: String!, email: String!): Customer
}
```


## Model (`Customer.java`)
```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Customer {
    private String id;
    private String name;
    private String email;
}
```


## Service Layer (`CustomerService.java`)
```java
@Service
public class CustomerService {

    private final Map<String, Customer> customerRepo = new HashMap<>();

    public Customer getCustomerById(String id) {
        return customerRepo.get(id);
    }

    public List<Customer> listCustomers() {
        return new ArrayList<>(customerRepo.values());
    }

    public Customer createCustomer(String name, String email) {
        String id = UUID.randomUUID().toString();
        Customer customer = new Customer(id, name, email);
        customerRepo.put(id, customer);
        return customer;
    }
}
```


## 🔗 Resolver (`CustomerResolver.java`)
```java
@Component
public class CustomerResolver implements GraphQLQueryResolver, GraphQLMutationResolver {

    @Autowired
    private CustomerService customerService;

    public Customer getCustomerById(String id) {
        return customerService.getCustomerById(id);
    }

    public List<Customer> listCustomers() {
        return customerService.listCustomers();
    }

    public Customer createCustomer(String name, String email) {
        return customerService.createCustomer(name, email);
    }
}
```


## 🚀 Application (`GraphqlApplication.java`)
```java
@SpringBootApplication
public class GraphqlApplication {
    public static void main(String[] args) {
        SpringApplication.run(GraphqlApplication.class, args);
    }
}
```


## 📊 Example Queries

### Query
```graphql
query {
  getCustomerById(id: "123") {
    id
    name
    email
  }
}
```

### Mutation
```graphql
mutation {
  createCustomer(name: "Anshuman", email: "anshuman@example.com") {
    id
    name
    email
  }
}
```


## Notes
- Use **GraphQL Java Tools** for schema-to-resolver mapping.  
- Combine with **Spring Boot Starter GraphQL** for auto-configuration.  
- Add **Exception Handling** for better error responses.  
- Integrate with **CQRS** by separating queries and mutations cleanly.  
