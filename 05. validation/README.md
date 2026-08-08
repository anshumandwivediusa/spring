# Data Validation in Spring Boot

## Overview
Data validation ensures that incoming request data is **correct, safe, and meaningful** before it reaches your business logic. Spring Boot integrates with **Jakarta Bean Validation (JSR 380)** using **Hibernate Validator** by default.

## Setup
Add the dependency (if not already included via Spring Boot Starter Web):

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

## Common Validation Annotations

| Annotation             | Purpose                                         | Example                                               |
| ---------------------- | ----------------------------------------------- | ----------------------------------------------------- |
| **`@NotNull`**         | Value must not be `null`                        | `@NotNull private String name;`                       |
| **`@Null`**            | Value must be `null`                            | `@Null private String generatedId;`                   |
| **`@NotEmpty`**        | Must not be `null` or empty                     | `@NotEmpty private String name;`                      |
| **`@NotBlank`**        | String must not be `null`, empty, or whitespace | `@NotBlank private String username;`                  |
| **`@Size`**            | String/collection/map/array size                | `@Size(min=3, max=20) private String name;`           |
| **`@Email`**           | Must have valid email format                    | `@Email private String email;`                        |
| **`@Pattern`**         | Must match regular expression                   | `@Pattern(regexp="\\d{10}") private String phone;`    |
| **`@Min`**             | Number must be ≥ specified value                | `@Min(18) private int age;`                           |
| **`@Max`**             | Number must be ≤ specified value                | `@Max(60) private int age;`                           |
| **`@DecimalMin`**      | Decimal number must be ≥ value                  | `@DecimalMin("0.0") private BigDecimal amount;`       |
| **`@DecimalMax`**      | Decimal number must be ≤ value                  | `@DecimalMax("100000.00") private BigDecimal amount;` |
| **`@Positive`**        | Number must be > 0                              | `@Positive private BigDecimal amount;`                |
| **`@PositiveOrZero`**  | Number must be ≥ 0                              | `@PositiveOrZero private int balance;`                |
| **`@Negative`**        | Number must be < 0                              | `@Negative private int value;`                        |
| **`@NegativeOrZero`**  | Number must be ≤ 0                              | `@NegativeOrZero private int adjustment;`             |
| **`@Digits`**          | Controls integer/fraction digit count           | `@Digits(integer=10, fraction=2)`                     |
| **`@AssertTrue`**      | Value must be `true`                            | `@AssertTrue private boolean accepted;`               |
| **`@AssertFalse`**     | Value must be `false`                           | `@AssertFalse private boolean blocked;`               |
| **`@Past`**            | Date/time must be in the past                   | `@Past private LocalDate birthDate;`                  |
| **`@PastOrPresent`**   | Date/time must be past or present               | `@PastOrPresent private LocalDate date;`              |
| **`@Future`**          | Date/time must be in the future                 | `@Future private LocalDate expiryDate;`               |
| **`@FutureOrPresent`** | Date/time must be future or present             | `@FutureOrPresent private LocalDate startDate;`       |


## Example DTO
```java
public class UserDTO {
    @NotNull(message = "ID cannot be null")
    private Long id;

    @NotBlank(message = "Name is required")
    private String name;

    @Email(message = "Invalid email format")
    private String email;

    @Min(value = 18, message = "Age must be at least 18")
    private int age;
}
```

## Controller Example
```java
@RestController
@RequestMapping("/users")
public class UserController {

    @PostMapping
    public ResponseEntity<UserDTO> createUser(@Valid @RequestBody UserDTO userDto) {
        // If validation fails, Spring automatically returns 400 Bad Request with error details
        return ResponseEntity.ok(userDto);
    }
}
```

## Handling Validation Errors
Spring Boot automatically returns a **400 Bad Request** with error messages.  
You can customize error handling using `@ControllerAdvice`:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String, String>> handleValidationErrors(MethodArgumentNotValidException ex) {
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getFieldErrors().forEach(error ->
            errors.put(error.getField(), error.getDefaultMessage()));
        return ResponseEntity.badRequest().body(errors);
    }
}
```

Example Response:
```json
{
  "name": "Name is required",
  "email": "Invalid email format"
}
```

## Summary
- Use **Bean Validation annotations** on DTOs/entities.  
- Apply **@Valid** in controller methods to trigger validation.  
- Customize error responses with **@ControllerAdvice**.  
- Ensures **clean, safe, and predictable API inputs**.  

