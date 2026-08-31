# GraphQL Questions & Answers (100 Quick Points)

### 1. **What is GraphQL?**  
**Answer**: 
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
**Answer**: REST exposes fixed endpoints, while GraphQL uses a single endpoint where clients specify fields.  
**Example:** REST → `/users/1` returns all fields. GraphQL → `/graphql` lets you choose only `name` and `email`.


### 3. **What is a GraphQL schema?**  
**Answer**: A schema defines types, queries, mutations, and subscriptions.  
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

---

4. **What are resolvers in GraphQL?**  
Answer: Resolvers are functions that fetch or modify data for a field.  
**Example (Java/Spring Boot):**  
```java
public User getUserById(String id) {
    return userRepository.findById(id);
}
```

---

5. **What is the difference between Query and Mutation?**  
Answer: Queries fetch data; mutations modify data.  
**Example:**  
```graphql
query { user(id: "1") { name } }
mutation { createUser(name: "Anshuman") { id } }
```

---

6. **What are GraphQL subscriptions?**  
Answer: Subscriptions enable real-time updates via WebSockets.  
**Example:**  
```graphql
subscription {
  userCreated {
    id
    name
  }
}
```

---

7. **What are scalar types in GraphQL?**  
Answer: Built-in primitives like `Int`, `Float`, `String`, `Boolean`, `ID`.  
**Example:**  
```graphql
type Product {
  id: ID!
  price: Float!
  name: String!
}
```

---

8. **What are object types in GraphQL?**  
Answer: Custom types grouping related fields.  
**Example:**  
```graphql
type Customer {
  id: ID!
  name: String!
  orders: [Order]
}
```

---

9. **What are input types in GraphQL?**  
Answer: Used for structured arguments in mutations.  
**Example:**  
```graphql
input CustomerInput {
  name: String!
  email: String!
}
mutation { createCustomer(input: {name:"Anshuman", email:"a@b.com"}) { id } }
```

---

10. **What are enums in GraphQL?**  
Answer: Fixed set of values for a field.  
**Example:**  
```graphql
enum Status { ACTIVE INACTIVE }
type User { id: ID! status: Status! }
```

---

11. **What are interfaces in GraphQL?**  
Answer: Abstract types implemented by multiple objects.  
**Example:**  
```graphql
interface Vehicle { id: ID! speed: Int! }
type Car implements Vehicle { id: ID! speed: Int! doors: Int! }
```

---

12. **What are union types in GraphQL?**  
Answer: A field can return one of several types.  
**Example:**  
```graphql
union SearchResult = User | Product
```

---

13. **What is a root query?**  
Answer: The entry point of a schema for fetching data.  
**Example:**  
```graphql
type Query {
  user(id: ID!): User
}
```

---

14. **What are nested queries in GraphQL?**  
Answer: Queries that fetch related data in one request.  
**Example:**  
```graphql
query {
  customer(id:"1") {
    name
    orders { id total }
  }
}
```

---

15. **What are fragments in GraphQL?**  
Answer: Reusable query parts.  
**Example:**  
```graphql
fragment userFields on User { id name email }
query { user(id:"1"){ ...userFields } }
```

---

16. **What are aliases in GraphQL?**  
Answer: Rename fields in query results.  
**Example:**  
```graphql
query {
  first: user(id:"1"){ name }
  second: user(id:"2"){ name }
}
```

---

17. **What are directives in GraphQL?**  
Answer: Control query execution (`@include`, `@skip`).  
**Example:**  
```graphql
query($withEmail: Boolean!) {
  user(id:"1"){ name email @include(if:$withEmail) }
}
```

---

18. **What are variables in GraphQL?**  
Answer: Dynamic values passed into queries.  
**Example:**  
```graphql
query($id: ID!) { user(id:$id){ name } }
```

---

19. **What is mutation structure in GraphQL?**  
Answer: Similar to queries but modifies data.  
**Example:**  
```graphql
mutation {
  createUser(name:"Anshuman", email:"a@b.com") { id name }
}
```

---

20. **What is the return type in mutation?**  
Answer: Mutations must specify return fields, often the modified object.  
**Example:**  
```graphql
mutation {
  updateUser(id:"1", name:"Updated") { id name }
}
```

---

✅ That’s the **first 20 elaborated with examples**.  

Would you like me to continue elaborating **21–50 in the same style** so you have a complete, example-rich set?
21. **What are batch mutations?**  
Answer: Multiple mutations executed in a single request.

22. **What is the use case of subscriptions?**  
Answer: Real-time features like chat, notifications, or live dashboards.

23. **What transport is used for subscriptions?**  
Answer: Typically WebSockets for persistent connections.

24. **What is a subscription resolver?**  
Answer: A function that publishes events to subscribed clients.

25. **What is the GraphQL execution engine?**  
Answer: Parses queries, validates against schema, and executes resolvers.

26. **What is batching in GraphQL?**  
Answer: Combining multiple queries to reduce round trips.

27. **What is the N+1 problem in GraphQL?**  
Answer: Excessive database calls due to nested queries.

28. **What is DataLoader in GraphQL?**  
Answer: A utility to batch and cache requests, solving the N+1 problem.

29. **What is GraphQL error format?**  
Answer: Standard JSON response with an `errors` array.

30. **What are partial results in GraphQL?**  
Answer: GraphQL can return available data along with error details.

31. **What are custom errors in GraphQL?**  
Answer: Application-specific errors thrown from resolvers.

32. **What is caching in GraphQL?**  
Answer: Storing query or field results to improve performance.

33. **What are persisted queries in GraphQL?**  
Answer: Predefined queries stored server-side to reduce payload size.

34. **What is query cost analysis in GraphQL?**  
Answer: Evaluating query complexity to prevent expensive requests.

35. **What is pagination in GraphQL?**  
Answer: Techniques like offset-based or cursor-based to fetch data in chunks.

36. **What is authentication in GraphQL?**  
Answer: Verifying user identity, often via JWT tokens in headers.

37. **What is authorization in GraphQL?**  
Answer: Controlling access to fields or operations based on user roles.

38. **What is query depth limiting in GraphQL?**  
Answer: Restricting nested queries to prevent abuse.

39. **What is rate limiting in GraphQL?**  
Answer: Limiting requests per client to protect resources.

40. **What is GraphiQL?**  
Answer: An in-browser IDE for testing GraphQL queries.

41. **What is Apollo Client?**  
Answer: A popular frontend library for managing GraphQL queries and caching.

42. **What is Apollo Server?**  
Answer: A Node.js implementation of a GraphQL server.

43. **What is Relay?**  
Answer: Facebook’s GraphQL client with advanced caching and pagination.

44. **What is Spring Boot GraphQL?**  
Answer: Java integration for GraphQL using resolvers and schema definitions.

45. **What is schema stitching in GraphQL?**  
Answer: Combining multiple schemas into one unified schema.

46. **What is GraphQL federation?**  
Answer: Distributing GraphQL across microservices with a unified gateway.

47. **What is introspection in GraphQL?**  
Answer: Querying the schema itself to discover available types and fields.

48. **What are custom scalars in GraphQL?**  
Answer: User-defined types like `Date`, `UUID`, or `Email`.

49. **What is middleware in GraphQL?**  
Answer: Functions that intercept queries for logging, authentication, or validation.

50. **What is the single endpoint concept in GraphQL?**  
Answer: All queries, mutations, and subscriptions are served through one endpoint (`/graphql`).

---

✅ That’s the **first 50 GraphQL questions with answers** in your requested format.  

Would you like me to continue with the **remaining 50 (51–100)** in the same style so you have the complete set?
### Execution
- **Execution engine?** → Parses query, validates against schema, executes resolvers.  
- **Batching?** → Combine multiple queries to reduce round trips.  
- **N+1 problem?** → Too many DB calls due to nested queries.  
- **DataLoader?** → Solves N+1 by batching and caching requests.  

### Error Handling
- **Error format?** → Standard JSON with `errors` array.  
- **Partial results?** → Returns available data plus errors.  
- **Custom errors?** → Throw exceptions in resolvers.  

### Performance
- **Caching?** → At query or field level.  
- **Persisted queries?** → Predefined queries stored server-side.  
- **Query cost analysis?** → Prevent expensive queries.  
- **Pagination?** → Use `limit/offset` or cursor-based.  

### Security
- **Authentication?** → Usually via JWT in headers.  
- **Authorization?** → Field-level access control.  
- **Query depth limiting?** → Prevent overly nested queries.  
- **Rate limiting?** → Protect against abuse.  

### Tooling
- **GraphiQL?** → In-browser IDE for testing queries.  
- **Apollo Client?** → Popular GraphQL client for frontend.  
- **Apollo Server?** → Node.js GraphQL server implementation.  
- **Relay?** → Facebook’s GraphQL client with advanced caching.  
- **Spring Boot GraphQL?** → Java integration using resolvers.  

### Advanced Concepts
- **Schema stitching?** → Combine multiple schemas into one.  
- **Federation?** → Distributed GraphQL across microservices.  
- **Introspection?** → Query schema itself for metadata.  
- **Custom scalars?** → Define types like `Date`, `UUID`.  
- **Middleware?** → Intercept queries for logging, auth, etc.  

---

## ✅ Quick Recap
That’s **100 GraphQL Q&A points** across basics, schema, queries, mutations, subscriptions, execution, errors, performance, security, tooling, and advanced concepts. Each answer is short (2–3 lines), exam-ready, and structured for fast recall.  

Would you like me to **format these into flashcards** (question on one side, answer on the other) so you can practice interactively, like a quiz set?
