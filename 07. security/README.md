# Spring Security

## 1. Core Concepts of Spring Security 
### **Authentication**:
Authentication is the process of proving that the entity (user, service, or system) is who it claims to be.
It relies on credentials and secrets that can be validated against a trusted source.

| **Concept** | **Description** |
| --- | --- |
| **[Principal](ca://s?q=Explain_Principal_in_Authentication)** | The entity being authenticated — usually a user, service account, or system identity. In Java/Spring, represented by ``Authentication.getPrincipal()``. |
| **[Credentials](ca://s?q=Explain_Credentials_in_Authentication)** | The proof of identity — passwords, tokens, certificates, or API keys. |
| **[Secrets](ca://s?q=Explain_Secrets_in_Authentication)** | Sensitive data used to verify credentials (e.g., private keys, hashed passwords). Must be stored securely. They are never shared directly with the client; only used by the server to check authenticity. |
| **[Identity Provider (IdP)](ca://s?q=Explain_Identity_Provider_IdP)** | The system that validates credentials and issues identity tokens (e.g., Azure Entra, Keycloak). |
| **[Authentication Token](ca://s?q=Explain_Authentication_Token)** | A representation of successful authentication — JWT, session ID, or OAuth2 access token. |
| **[Subject](ca://s?q=Explain_Subject_in_Security_Context)** | The security context holding the authenticated principal and its roles/permissions. |


### **Authorization**:
**Authorization** is the next logical layer after authentication. Once the system knows *who* you are, authorization decides *what you can do*.  

Authorization determines whether an **authenticated principal** has permission to perform a specific action or access a resource.  
It’s all about **roles**, **permissions**, and **policies**.

### Stateful vs Stateless Authentications
<img width="1358" height="905" alt="image" src="https://github.com/user-attachments/assets/9f8a35c1-f205-4a0e-9b21-f333f5f8e960" />


### Authorization Models

| **Model** | **Description** |
|------------|----------------|
| **Role-Based Access Control (RBAC)** | Users are assigned roles; roles define permissions. |
| **Attribute-Based Access Control (ABAC)** | Decisions based on attributes (user, resource, environment). |
| **Policy-Based Access Control (PBAC)** | Uses explicit policies (e.g., JSON/YAML rules) for complex conditions. |

### CIA Triad
 - **Confidentiality** → Ensures sensitive data is accessible only to authorized users.
   Tools: Encryption, Access Controls, Multi-Factor Authentication (MFA).
 - **Integrity** → Protects data from unauthorized modification.
   Tools: Checksums, Hashes, Audit Logs.
 - **Availability** → Keeps systems and data accessible when needed.
   Tools: Redundancy, Failover Systems, Backups.

### Defense in Depth
 - Multiple layers of security controls prevent single points of failure.
   Examples: Firewalls, Intrusion Detection Systems (IDS), Endpoint Security, Cloud Security.

### Encryption
- **Purpose** → Encryption transforms readable data into an unreadable format using mathematical algorithms, ensuring that even if attackers intercept the data, they cannot understand it without the proper key. It’s the backbone of **confidentiality** in the CIA triad.  

- **Types of Encryption**  
  - **Symmetric (AES)** → Same key for encryption and decryption. Fast, used for securing large volumes of data (e.g., databases, file systems).  
  - **Asymmetric (RSA)** → Uses a public/private key pair. Slower but ideal for secure key exchange and digital signatures.  
  - **Hybrid (TLS/SSL)** → Combines both: RSA for exchanging keys, AES for bulk data transfer.  

- **Applications**  
  - **Data in Transit** → Protects communication channels (emails, VPNs, HTTPS websites).  
  - **Data at Rest** → Secures stored information (databases, hard drives, backups).  
  - **Authentication & Integrity** → Digital signatures and certificates verify identity and prevent tampering.  

- **Real-World Examples**  
  - Online banking transactions secured with **TLS/SSL**.  
  - Encrypted messaging apps like **Signal** using end-to-end encryption.  
  - Cloud storage providers encrypt files at rest with **AES-256**.  

- **Challenges**  
  - **Key Management** → Losing or leaking keys compromises security.  
  - **Performance Overhead** → Heavy encryption can slow systems if not optimized.  
  - **Quantum Threats** → Future quantum computers may break RSA; hence **post-quantum cryptography** is emerging.  

## 2. Security Architecture of Spring Boot

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


## CSRF: Cross Site Request Forgery

### Definition
- **CSRF** is an attack where a malicious site tricks a logged‑in user’s browser into sending unauthorized requests to a trusted application.  
- Exploits the fact that browsers automatically attach cookies/session IDs with requests.

### How CSRF Works
1. User logs into a trusted site (e.g., banking app).  
2. Session cookie is stored in the browser.  
3. Malicious site sends a crafted request to the trusted site.  
4. Browser automatically attaches the cookie → request looks valid.  
5. Trusted site executes the action (e.g., money transfer).

### Spring Security Behavior
- CSRF protection is **enabled by default** for state‑changing requests (`POST`, `PUT`, `DELETE`).  
- Requires a **CSRF token** to be included in the request.  
- Token is generated by the server and must be sent back by the client.

### Implementation
- **Enabled (default)** → protects session‑based apps.  
- **Disabled** for stateless APIs (JWT, OAuth2) because:
  - No session cookies to protect.  
  - Authentication is explicit via headers.  

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
        .csrf().disable() // disable for JWT APIs
        .authorizeRequests()
          .anyRequest().authenticated();
}
```

### CSRF Token Usage
- Token available in request attribute `_csrf`.  
- Example in Thymeleaf:
```html
<form method="post" action="/transfer">
    <input type="hidden" name="${_csrf.parameterName}" value="${_csrf.token}" />
    <button type="submit">Submit</button>
</form>
```

### Best Practices
- Keep CSRF enabled for **web apps with sessions/cookies**.  
- Disable CSRF for **stateless REST APIs** (JWT, OAuth2).  
- Always use **HTTPS**.  
- Rotate and expire CSRF tokens regularly.  
- Combine with **XSS protection** to prevent token theft.

## CORS
- **CORS (Cross‑Origin Resource Sharing)** is a browser security feature that controls how resources can be requested from a different domain (origin).  
- Prevents malicious sites from making unauthorized requests to another domain using a logged‑in user’s credentials.  
- Example: `frontend.com` calling APIs hosted on `backend.com`.

### Why CORS Matters
- Browsers enforce **Same‑Origin Policy** → requests allowed only if protocol, domain, and port match.  
- CORS relaxes this restriction by allowing servers to specify which origins can access their resources.  
- Without proper CORS config, frontend apps cannot call backend APIs hosted on different domains.

### Spring Boot Implementation

### 1. Global CORS Configuration
```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                .allowedOrigins("http://localhost:3000") // frontend origin
                .allowedMethods("GET", "POST", "PUT", "DELETE")
                .allowedHeaders("*")
                .allowCredentials(true);
    }
}
```


### 2. Controller‑Level CORS
```java
@RestController
@CrossOrigin(origins = "http://localhost:3000")
public class DemoController {
    @GetMapping("/hello")
    public String hello() {
        return "Hello World";
    }
}
```

### 3. Security Config with CORS
```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.cors().and().csrf().disable()
        .authorizeRequests()
        .anyRequest().authenticated();
}
```

## ### Key Headers
- **Access-Control-Allow-Origin** → which domains can access.  
- **Access-Control-Allow-Methods** → allowed HTTP methods.  
- **Access-Control-Allow-Headers** → allowed request headers.  
- **Access-Control-Allow-Credentials** → whether cookies/credentials are allowed.  
- **Access-Control-Max-Age** → how long preflight results can be cached.


## ### Best Practices
- Restrict origins to trusted domains (avoid `*` in production).  
- Allow only necessary methods and headers.  
- Use HTTPS for secure communication.  
- Combine with authentication (JWT/OAuth2) for stronger security.  


## Basic Authentication in Spring Boot

### Overview
Basic Authentication is the simplest form of authentication where the client sends the username and password in the `Authorization` header.  
The credentials are Base64 encoded (`username:password`) but **not encrypted**, so HTTPS is strongly recommended.

### 1. Username & Password (Postman UI)
- In Postman, go to **Authorization** tab.  
- Select **Basic Auth**.  
- Enter **Username** and **Password**.  
- Postman automatically encodes and adds the header.

<img width="600" height="201" alt="image" src="https://github.com/user-attachments/assets/dcae81d3-5dab-4be9-bcfe-773cb0a819db" />

### 2. Authorization Header (Manual)
- Encode `username:password` in Base64.  
- Add header:  
  ```
  Authorization: Basic dXNlcjpwYXNzd29yZA==
  ```

<img width="600" height="261" alt="image" src="https://github.com/user-attachments/assets/5413f963-56cd-4402-8bdf-ed57b6be626e" />

### Spring Boot Implementations

### 1. Security Configuration
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
              .anyRequest().authenticated()
            .and()
            .httpBasic(); // Enables Basic Auth
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication()
            .withUser("user")
            .password("{noop}password") // {noop} means plain text
            .roles("USER");
    }
}
```

### 2. Testing with Postman
- Send a request with **Basic Auth** credentials.  
- Spring Security validates against the in-memory user.  
- If valid → returns response.  
- If invalid → returns `401 Unauthorized`.


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


## Easy Flow of Validation – OAuth 2.0 + Azure Entra ID

### 1. User Request
- User tries to access a protected endpoint in your Spring Boot app.  
- Spring Security sees the request needs authentication.


### 2. Redirect to Azure AD
- Spring Security automatically redirects the user to **Azure Entra ID login page**.  
- User enters credentials (username/password, MFA, etc.).


### 3. Authorization Code
- After successful login, Azure AD sends back an **authorization code** to your app’s redirect URI (`/login/oauth2/code/azure`).  
- This code is short‑lived and proves the user authenticated.


### 4. Token Exchange
- Spring Boot (via OAuth2 client) exchanges the code with Azure AD.  
- Azure AD returns:
  - **ID Token (JWT)** → contains user identity claims (email, name, tenant).  
  - **Access Token (JWT)** → used to call APIs.  
  - (Optional) **Refresh Token** → to get new tokens without re‑login.


### 5. Token Validation
- Spring Security validates the JWT:
  - Checks **signature** (signed by Azure AD).  
  - Checks **issuer** (matches your tenant).  
  - Checks **expiration** (token not expired).  
- If valid → user is authenticated in Spring Security context.


### 6. Access Granted
- User can now access protected endpoints.  
- You can inject the authenticated principal:
```java
@GetMapping("/user")
public Map<String, Object> user(@AuthenticationPrincipal OAuth2User principal) {
    return principal.getAttributes(); // contains Azure AD claims
}
```


### Flow Recap
1. User requests resource → Spring Security redirects to Azure AD.  
2. User logs in → Azure AD sends authorization code.  
3. Spring Boot exchanges code → gets JWT tokens.  
4. Spring Security validates tokens.  
5. User gains access → claims available in app.


### Best Practices
- Always use **HTTPS** for redirect URIs.  
- Store **Client Secret** securely (e.g., Azure Key Vault).  
- Use **PKCE** for extra protection in public clients.  
- Map **Azure AD roles/groups** to Spring Security authorities.  
- For REST APIs → configure as a **Resource Server** to validate JWTs directly.
