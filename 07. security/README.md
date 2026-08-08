# Spring Security

## Core Concepts
- **Authentication** → Verifies user identity (login, tokens, certificates).  
- **Authorization** → Grants or denies access based on roles/permissions.  
- **Security Filter Chain** → Every HTTP request passes through filters (e.g., `UsernamePasswordAuthenticationFilter`, `BasicAuthenticationFilter`).  
- **SecurityContext** → Holds authentication details for the current user.  
- **AuthenticationManager** → Delegates authentication to providers like `DaoAuthenticationProvider` or `JwtAuthenticationProvider`.


## Authentication Mechanisms
- **Form Login** → Standard username/password login with session.  
- **HTTP Basic** → Simple header-based authentication.  
- **OAuth2 / OpenID Connect** → Social logins (Google, GitHub) and enterprise SSO.  
- **JWT Tokens** → Stateless, token-based security for REST APIs.  
- **LDAP / X.509 Certificates** → Enterprise-grade authentication.
- **SAML** → XML-based protocol for exchanging authentication and authorization data between identity providers (IdPs) and service providers (SPs). Commonly used in enterprise SSO with providers like Okta, Azure AD, or ADFS.


## Authorization
- **Role-Based Access Control (RBAC)** → `hasRole("ADMIN")`.  
- **Permission-Based Access Control** → Fine-grained authorities.  
- **Method-Level Security** → `@PreAuthorize`, `@PostAuthorize`, `@Secured`.  
- **Domain Object Security (ACLs)** → Access control lists for specific entities.


## Password Security
- **Encoders** → `BCryptPasswordEncoder`, `Argon2PasswordEncoder`.  
- **DelegatingPasswordEncoder** → Supports multiple algorithms.  
- **Best Practice** → Always hash + salt passwords.


## Session, CSRF, and CORS
- **Session Management** → Prevent fixation, concurrency issues, use Redis for distributed apps.  
- **CSRF Protection** → Enabled by default; disable only for stateless APIs.  
- **CORS Configuration** → Allow cross-origin requests securely.


## Advanced Features
- **Multi-Factor Authentication (MFA)** → TOTP, WebAuthn.  
- **Security Headers** → CSP, HSTS, X-Frame-Options, Clickjacking protection.  
- **Brute Force Prevention** → Lock accounts after repeated failed attempts.  
- **Testing** → `@WithMockUser`, `MockMvc` for security tests.


## Quick Reference Table

| Feature | Purpose | Example |
|---------|---------|---------|
| **Authentication** | Verify identity | Form login, JWT |
| **Authorization** | Control access | `@PreAuthorize("hasRole('ADMIN')")` |
| **CSRF** | Prevent cross-site request forgery | Enabled by default |
| **CORS** | Cross-origin requests | `http.cors()` |
| **Password Encoding** | Secure storage | BCrypt |


## Best Practices
- Always use **BCrypt or Argon2** for password encoding.  
- Prefer **JWT/OAuth2** for stateless APIs.  
- Keep **CSRF enabled** unless building stateless REST APIs.  
- Apply **method-level security** for sensitive business logic.  
- Regularly update dependencies to patch vulnerabilities.  


## OAuth 2.0 with AZ Entra Id

### Step-by-Step Implementation

#### 1. **Dependencies**
Add these to your `pom.xml`:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-oauth2-client</artifactId>
</dependency>
```


#### 2. **Register App in Azure AD**
- Go to **Azure Portal → Azure Active Directory → App registrations**.  
- Register a new application.  
- Note down:
  - **Client ID**
  - **Client Secret**
  - **Tenant ID**
  - **Redirect URI** (e.g., `http://localhost:8080/login/oauth2/code/azure`).


#### 3. **Configure `application.yml`**
```yaml
spring:
  security:
    oauth2:
      client:
        registration:
          azure:
            client-id: YOUR_CLIENT_ID
            client-secret: YOUR_CLIENT_SECRET
            scope: openid, profile, email
            redirect-uri: "{baseUrl}/login/oauth2/code/azure"
            authorization-grant-type: authorization_code
        provider:
          azure:
            issuer-uri: https://login.microsoftonline.com/YOUR_TENANT_ID/v2.0
```


#### 4. **Security Configuration**
Spring Boot auto-configures the OAuth2 login flow.  
You can customize with a `SecurityFilterChain`:

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/public/**").permitAll()
                .anyRequest().authenticated()
            )
            .oauth2Login(Customizer.withDefaults()); // Azure AD login
        return http.build();
    }
}
```


#### 5. **Access User Info**
You can inject the authenticated principal:
```java
@GetMapping("/user")
public Map<String, Object> user(@AuthenticationPrincipal OAuth2User principal) {
    return principal.getAttributes(); // Contains claims from Azure AD
}
```


### Flow Summary

| Step | Action |
|------|--------|
| **App Registration** | Register app in Azure AD, get Client ID/Secret |
| **Config** | Add OAuth2 client settings in `application.yml` |
| **Login Flow** | Spring Security redirects to Azure AD login |
| **Token Exchange** | Azure AD returns ID token + access token |
| **User Info** | Spring app extracts claims (email, roles, etc.) |


### Best Practices
- Use **PKCE** for enhanced security.  
- Store secrets securely (e.g., Azure Key Vault).  
- Map Azure AD groups/roles to Spring Security authorities.  
- For APIs, use **JWT validation** with `spring-boot-starter-oauth2-resource-server`.

