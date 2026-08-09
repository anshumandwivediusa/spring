# Spring Security

## Core Concepts
### **Authentication**:
Authentication is the process of proving that the entity (user, service, or system) is who it claims to be.
It relies on credentials and secrets that can be validated against a trusted source.

| **Concept** | **Description** |
| --- | --- |
| **[Principal](ca://s?q=Explain_Principal_in_Authentication)** | The entity being authenticated — usually a user, service account, or system identity. In Java/Spring, represented by ``Authentication.getPrincipal()``. |
| **[Credentials](ca://s?q=Explain_Credentials_in_Authentication)** | The proof of identity — passwords, tokens, certificates, or API keys. |
| **[Secrets](ca://s?q=Explain_Secrets_in_Authentication)** | Sensitive data used to verify credentials (e.g., private keys, hashed passwords). Must be stored securely. |
| **[Identity Provider (IdP)](ca://s?q=Explain_Identity_Provider_IdP)** | The system that validates credentials and issues identity tokens (e.g., Azure Entra, Keycloak). |
| **[Authentication Token](ca://s?q=Explain_Authentication_Token)** | A representation of successful authentication — JWT, session ID, or OAuth2 access token. |
| **[Subject](ca://s?q=Explain_Subject_in_Security_Context)** | The security context holding the authenticated principal and its roles/permissions. |



- **Authorization** → Grants or denies access based on roles (group of permissions)/permissions.  
- **Security Filter Chain** → Every HTTP request passes through filters (e.g., `UsernamePasswordAuthenticationFilter`, `BasicAuthenticationFilter`).  
- **SecurityContext** → Holds authentication details for the current user.  
- **AuthenticationManager** → Delegates authentication to providers like `DaoAuthenticationProvider` or `JwtAuthenticationProvider`.

## Security Architecture of Spring Boot

<img width="500" height="350" alt="image" src="https://github.com/user-attachments/assets/c0bf09de-742a-4640-9546-3d70ab677858" />

### Flow Explanation
1. **Client Request** → The browser/API sends an HTTP request.  
2. **FilterChain** → General servlet filters (logging, compression, etc.).  
3. **DelegatingFilterProxy** → Bridges servlet filters with Spring-managed beans.  
4. **FilterChainProxy** → Entry point into Spring Security. Decides which security chain applies.  
5. **SecurityFilterChain** → Multiple security filters run in sequence:  
   - `AuthenticationFilter` → Validates identity.  
   - `AuthorizationFilter` → Checks roles/permissions.  
   - `CsrfFilter` → Protects against CSRF attacks.  
   - `ExceptionTranslationFilter` → Handles security exceptions.  
   - `FilterSecurityInterceptor` → Final access decision.  
6. **DispatcherServlet** → If allowed, request reaches Spring MVC controllers.
7. **SpringController** 

### Key Insight
- **Authentication** happens early in the chain.  
- **Authorization** happens after identity is established.  
- **Other protections** (CSRF, session management, exception handling) are layered in.  
- This ensures **every request** is consistently checked before hitting your controllers.


## Authentication Mechanisms
- **Form Login** → Standard username/password login with session.  
- **HTTP Basic** → Simple header-based authentication.  
- **OAuth2 / OpenID Connect** → Social logins (Google, GitHub) and enterprise SSO.  
- **JWT Tokens** → Stateless, token-based security for REST APIs.  
- **LDAP / X.509 Certificates** → Enterprise-grade authentication.
- **SAML** → XML-based protocol for exchanging authentication and authorization data between identity providers (IdPs) and service providers (SPs). Commonly used in enterprise SSO with providers like Okta, Azure AD, or ADFS.


### Basic Auth Methods
- **Basic** → Username and password sent with each request (simple but insecure if not over HTTPS).  
- **Digest** → Improves on Basic by hashing credentials before sending.  
- **API Keys** → A unique key identifies the client; often used in REST APIs.  
- **Session** → Server stores session state after login; client holds a session ID (cookie).


### Token-Based Auth
- **Bearer & JWT Tokens** → Tokens are passed in headers; JWTs are self-contained with claims and signatures.  
- **Access & Refresh Tokens** → Access tokens are short-lived; refresh tokens allow reissuing without re-login.


### OAuth2 and OIDC
- **OAuth2** → Delegated authorization (e.g., “Login with Google”).  
- **OpenID Connect** → Built on OAuth2, adds identity layer (who the user is, not just what they can access).


### SSO & Identity Protocols
- **SSO** → Single Sign-On lets users access multiple apps with one login.  
- Protocols like **SAML**, **OIDC**, and **OAuth2** are used to implement enterprise-grade identity federation.


### 📊 Evolution Insight
- Starts with **basic methods** (simple but less secure).  
- Moves to **tokens** (stateless, scalable).  
- Advances into **OAuth2/OIDC** (delegated, identity-aware).  
- Ends with **SSO protocols** (enterprise, cross-application).



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

