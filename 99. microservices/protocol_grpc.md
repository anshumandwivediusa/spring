# Architecture Overview
- **Server Microservice** → Exposes gRPC services (business logic).  
- **Client Microservice** → Calls the server using gRPC stubs.  
- Communication happens over **HTTP/2** using **Protobuf** contracts.  


## Step 1: Define the Protobuf Contract
Create `customer.proto` in a shared module (so both server and client can use it):

```proto
syntax = "proto3";

option java_multiple_files = true;
option java_package = "com.example.grpc";
option java_outer_classname = "CustomerProto";

service CustomerService {
  rpc GetCustomer (CustomerRequest) returns (CustomerResponse);
}

message CustomerRequest {
  string id = 1;
}

message CustomerResponse {
  string id = 1;
  string name = 2;
  string email = 3;
}
```

👉 Compile with `protoc` or Maven plugin to generate Java stubs.


## Step 2: Server Microservice (Spring Boot)

### Dependency
```xml
<dependency>
  <groupId>net.devh</groupId>
  <artifactId>grpc-spring-boot-starter</artifactId>
  <version>2.14.0.RELEASE</version>
</dependency>
```

### Service Implementation
```java
@GrpcService
public class CustomerServiceImpl extends CustomerServiceGrpc.CustomerServiceImplBase {

    @Override
    public void getCustomer(CustomerRequest request,
                            StreamObserver<CustomerResponse> responseObserver) {
        CustomerResponse response = CustomerResponse.newBuilder()
                .setId(request.getId())
                .setName("Anshuman")
                .setEmail("anshuman@example.com")
                .build();

        responseObserver.onNext(response);
        responseObserver.onCompleted();
    }
}
```

👉 This microservice listens for gRPC calls and responds with customer data.


## Step 3: Client Microservice (Spring Boot)

### Dependency
Same `grpc-spring-boot-starter` dependency.

### Client Stub Usage
```java
@Service
public class CustomerClient {

    @GrpcClient("customerService")
    private CustomerServiceGrpc.CustomerServiceBlockingStub customerStub;

    public CustomerResponse getCustomer(String id) {
        CustomerRequest request = CustomerRequest.newBuilder().setId(id).build();
        return customerStub.getCustomer(request);
    }
}
```

👉 The client microservice uses the generated stub to call the server.


## Step 4: Configuration
In `application.yml` of the **client**:
```yaml
grpc:
  client:
    customerService:
      address: static://localhost:9090
      negotiationType: plaintext
```

In `application.yml` of the **server**:
```yaml
grpc:
  server:
    port: 9090
```


## Key Insight
- **Server microservice** → Implements gRPC service logic.  
- **Client microservice** → Calls server via generated stubs.  
- **Protobuf contract** → Shared between both, ensuring strong typing.  
- **HTTP/2 transport** → Enables multiplexing and streaming.  
