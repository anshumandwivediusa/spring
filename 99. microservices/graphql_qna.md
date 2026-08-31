# GraphQL Questions & **Answer**s (100 Quick Points)

### 1. **What is GraphQL?**  
****Answer****: 
1. **GraphQL** is a **dynamic query language** designed specifically for APIs, allowing clients to describe the exact shape of the data they want.  
2. It works with a **runtime engine** that validates queries against a schema and executes resolvers to fetch data.  
3. Unlike REST, which returns fixed payloads, GraphQL avoids **over‑fetching** (extra unused fields) and **under‑fetching** (missing fields requiring multiple calls).  
4. Clients can combine multiple resources into a **single request**, reducing network overhead and improving efficiency.  
5. For example, a query like:  
   ```graphql
   query { user(id:"1"){ name email } }
   ```  
   returns only `name` and `email`, demonstrating how GraphQL delivers **precisely what the client asks for**.  

**Example:**  
```graphql
query {
  user(id: "1") {
    name
    email
  }
}
```
This fetches only `name` and `email` instead of the entire user object.


### 2. **How is GraphQL different from REST?**  
****Answer****: REST exposes fixed endpoints, while GraphQL uses a single endpoint where clients specify fields.  
**Example:** REST → `/users/1` returns all fields. GraphQL → `/graphql` lets you choose only `name` and `email`.


### 3. **What is a GraphQL schema?**  
****Answer****: A schema defines types, queries, mutations, and subscriptions.  
**Example:**  
```graphql
# User type definition
type User {
  id: ID!
  name: String!
  email: String!
}

# Queries (read operations)
type Query {
  getUserById(id: ID!): User
  listUsers: [User]
}

# Mutations (write operations)
type Mutation {
  createUser(name: String!, email: String!): User
  updateUser(id: ID!, name: String, email: String): User
  deleteUser(id: ID!): Boolean
}

# Subscriptions (real-time updates)
type Subscription {
  userCreated: User
  userUpdated: User
  userDeleted: ID
}
```



### 4. **What are resolvers in GraphQL?**  
**Answer**: Resolvers are backend functions that map schema fields to actual data sources. They execute when a query, mutation, or subscription is called.  
**Example (Java/Spring Boot):**  
```java
public User getUserById(String id) {
    return userRepository.findById(id)
                         .orElseThrow(() -> new RuntimeException("User not found"));
}
```


### 5. **What is the difference between Query and Mutation?**  
**Answer**: Queries are used to fetch/read data, while mutations are used to create, update, or delete data.  
**Example:**  
```graphql
query { user(id: "1") { name } }
mutation { createUser(name: "Anshuman") { id } }
```


### 6. **What are GraphQL subscriptions?**  
**Answer**: Subscriptions enable real-time communication between client and server, typically using WebSockets.  
**Example:**  
```graphql
subscription {
  userCreated {
    id
    name
  }
}
```


### 7. **What are scalar types in GraphQL?**  
**Answer**: Scalars are built-in primitive types like `Int`, `Float`, `String`, `Boolean`, and `ID`. They represent basic values.  
**Example:**  
```graphql
type Product {
  id: ID!
  price: Float!
  name: String!
}
```


### 8. **What are object types in GraphQL?**  
**Answer**: Object types group related fields into a structured entity, representing domain models.  
**Example:**  
```graphql
type Customer {
  id: ID!
  name: String!
  orders: [Order]
}
```


### 9. **What are input types in GraphQL?**  
**Answer**: Input types allow passing structured arguments into mutations, making them reusable and organized.  
**Example:**  
```graphql
input CustomerInput {
  name: String!
  email: String!
}
mutation {
  createCustomer(input: {name:"Anshuman", email:"a@b.com"}) { id }
}
```


### 10. **What are enums in GraphQL?**  
**Answer**: Enums restrict a field to a fixed set of values, ensuring consistency and validation.  
**Example:**  
```graphql
enum Status { ACTIVE INACTIVE }
type User { id: ID! status: Status! }
```


### 11. **What are interfaces in GraphQL?**  
**Answer**: Abstract types implemented by multiple objects.  
**Example:**  
```graphql
interface Vehicle { id: ID! speed: Int! }
type Car implements Vehicle { id: ID! speed: Int! doors: Int! }
```


12. **What are union types in GraphQL?**  
**Answer**: A field can return one of several types.  
**Example:**  
```graphql
union SearchResult = User | Product
```


13. **What is a root query?**  
**Answer**: The entry point of a schema for fetching data.  
**Example:**  
```graphql
type Query {
  user(id: ID!): User
}
```


14. **What are nested queries in GraphQL?**  
**Answer**: Queries that fetch related data in one request.  
**Example:**  
```graphql
query {
  customer(id:"1") {
    name
    orders { id total }
  }
}
```


15. **What are fragments in GraphQL?**  
**Answer**: Reusable query parts.  
**Example:**  
```graphql
fragment userFields on User { id name email }
query { user(id:"1"){ ...userFields } }
```


16. **What are aliases in GraphQL?**  
**Answer**: Rename fields in query results.  
**Example:**  
```graphql
query {
  first: user(id:"1"){ name }
  second: user(id:"2"){ name }
}
```


17. **What are directives in GraphQL?**  
**Answer**: Control query execution (`@include`, `@skip`).  
**Example:**  
```graphql
query($withEmail: Boolean!) {
  user(id:"1"){ name email @include(if:$withEmail) }
}
```


18. **What are variables in GraphQL?**  
**Answer**: Dynamic values passed into queries.  
**Example:**  
```graphql
query($id: ID!) { user(id:$id){ name } }
```


19. **What is mutation structure in GraphQL?**  
**Answer**: Similar to queries but modifies data.  
**Example:**  
```graphql
mutation {
  createUser(name:"Anshuman", email:"a@b.com") { id name }
}
```


20. **What is the return type in mutation?**  
**Answer**: Mutations must specify return fields, often the modified object.  
**Example:**  
```graphql
mutation {
  updateUser(id:"1", name:"Updated") { id name }
}
```


Here’s an **elaborated version of Q&A 21–40** with short examples and context so they’re more practical and exam‑ready:


### 21. **What are batch mutations?**  
**Answer**: Batch mutations allow multiple mutations in a single request, reducing network overhead.  
**Example:**  
```graphql
mutation {
  createUser(name:"A"){id}
  updateUser(id:"1", name:"B"){id}
}
```


### 22. **What is the use case of subscriptions?**  
**Answer**: Subscriptions enable real‑time features like chat, notifications, or dashboards.  
**Example:** A stock price app uses subscriptions to push live price updates to clients.


### 23. **What transport is used for subscriptions?**  
**Answer**: Typically **WebSockets**, which maintain persistent connections for real‑time communication.  
**Example:** Apollo Client uses `ws://` connections for GraphQL subscriptions.


### 24. **What is a subscription resolver?**  
**Answer**: A resolver that publishes events to subscribed clients when data changes.  
**Example:**  
```java
public Publisher<User> userCreated() {
    return userPublisher;
}
```


### 25. **What is the GraphQL execution engine?**  
**Answer**: It parses queries, validates them against the schema, and executes resolvers to fetch data.  
**Example:** Apollo Server’s execution engine handles query parsing and resolver mapping.


### 26. **What is batching in GraphQL?**  
**Answer**: Batching combines multiple queries into one request to reduce round trips.  
**Example:** Instead of two separate queries for `user` and `orders`, both can be batched together.


### 27. **What is the N+1 problem in GraphQL?**  
**Answer**: Occurs when nested queries trigger excessive DB calls (e.g., fetching each user’s orders individually).  
**Example:** Querying 10 users with orders may cause 11 DB calls instead of 2.


### 28. **What is DataLoader in GraphQL?**  
**Answer**: A utility to batch and cache requests, solving the N+1 problem.  
**Example:** DataLoader groups multiple `getOrdersByUserId` calls into one DB query.


### 29. **What is GraphQL error format?**  
**Answer**: Standard JSON response with an `errors` array.  
**Example:**  
```json
{
  "errors": [{ "message": "User not found" }]
}
```


### 30. **What are partial results in GraphQL?**  
**Answer**: GraphQL can return available data along with error details.  
**Example:**  
```json
{
  "data": { "user": null },
  "errors": [{ "message": "User not found" }]
}
```


### 31. **What are custom errors in GraphQL?**  
**Answer**: Application‑specific errors thrown from resolvers.  
**Example:** Throwing `InvalidEmailException` when creating a user with a bad email.


### 32. **What is caching in GraphQL?**  
**Answer**: Storing query or field results to improve performance.  
**Example:** Apollo Client caches query results locally to avoid repeated server calls.


### 33. **What are persisted queries in GraphQL?**  
**Answer**: Predefined queries stored server‑side to reduce payload size and improve security.  
**Example:** Client sends a query ID instead of the full query string.


### 34. **What is query cost analysis in GraphQL?**  
**Answer**: Evaluating query complexity to prevent expensive requests.  
**Example:** Limiting queries that fetch deeply nested data across many nodes.


### 35. **What is pagination in GraphQL?**  
**Answer**: Fetching data in chunks using offset or cursor‑based techniques.  
**Example:**  
```graphql
query {
  users(first: 10, after:"cursor123") { id name }
}
```


### 36. **What is authentication in GraphQL?**  
**Answer**: Verifying user identity, often via JWT tokens in headers.  
**Example:**  
```http
Authorization: Bearer <jwt-token>
```


### 37. **What is authorization in GraphQL?**  
**Answer**: Controlling access to fields or operations based on user roles.  
**Example:** Only `admin` role can access `deleteUser` mutation.


### 38. **What is query depth limiting in GraphQL?**  
**Answer**: Restricting how deeply queries can nest to prevent abuse.  
**Example:** Allow max depth of 5 levels in queries.


### 39. **What is rate limiting in GraphQL?**  
**Answer**: Limiting requests per client to protect resources.  
**Example:** Allow max 100 queries per minute per user.


### 40. **What is GraphiQL?**  
**Answer**: An in‑browser IDE for testing GraphQL queries interactively.  
**Example:** Developers use GraphiQL to run queries like:  
```graphql
query { user(id:"1"){ name email } }
```

### 41. **What is Apollo Client?**  
**Answer**: Apollo Client is a popular frontend library for managing GraphQL queries, caching, and state. It simplifies data fetching in React, Angular, or Vue apps.  
**Example:**  
```javascript
const { data } = useQuery(GET_USER);
```


### 42. **What is Apollo Server?**  
**Answer**: Apollo Server is a Node.js GraphQL server implementation that connects schemas and resolvers to data sources.  
**Example:**  
```javascript
const server = new ApolloServer({ typeDefs, resolvers });
```


### 43. **What is Relay?**  
**Answer**: Relay is Facebook’s GraphQL client with advanced caching and pagination, optimized for large apps.  
**Example:** It uses **cursor-based pagination** for efficient infinite scrolling.


### 44. **What is Spring Boot GraphQL?**  
**Answer**: Spring Boot GraphQL integrates Java applications with GraphQL using resolvers and schema definitions.  
**Example:**  
```java
public class UserResolver implements GraphQLQueryResolver {
    public User getUserById(String id) { return userService.findById(id); }
}
```


### 45. **What is schema stitching in GraphQL?**  
**Answer**: Schema stitching combines multiple GraphQL schemas into one unified schema.  
**Example:** Merging `UserSchema` and `OrderSchema` so clients query both seamlessly.


### 46. **What is GraphQL federation?**  
**Answer**: Federation distributes GraphQL across microservices, unified by a gateway.  
**Example:** A `User` type in one service and `Orders` in another, combined via Apollo Federation.


### 47. **What is introspection in GraphQL?**  
**Answer**: Introspection allows querying the schema itself to discover available types and fields.  
**Example:**  
```graphql
query { __schema { types { name } } }
```


### 48. **What are custom scalars in GraphQL?**  
**Answer**: Custom scalars are user-defined types like `Date`, `UUID`, or `Email` for domain-specific values.  
**Example:**  
```graphql
scalar Date
type Event { id: ID! date: Date! }
```


### 49. **What is middleware in GraphQL?**  
**Answer**: Middleware intercepts queries for logging, authentication, or validation before execution.  
**Example:** Adding a middleware to check JWT tokens before resolver execution.


### 50. **What is the single endpoint concept in GraphQL?**  
**Answer**: GraphQL serves all queries, mutations, and subscriptions through one endpoint (`/graphql`).  
**Example:** Instead of multiple REST endpoints (`/users`, `/orders`), GraphQL uses one endpoint with flexible queries.


Would you like me to **format these into flashcards** (question on one side, **Answer** on the other) so you can practice interactively, like a quiz set?
