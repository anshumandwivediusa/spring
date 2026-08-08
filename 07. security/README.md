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
- **JWT Tokens** → Stateless, token-based security for REST APIs.  
- **OAuth2 / OpenID Connect** → Social logins (Google, GitHub) and enterprise SSO.  
- **LDAP / X.509 Certificates** → Enterprise-grade authentication.

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


Would you like me to create a **step-by-step cheat sheet** with **Spring Security annotations, configurations, and code snippets** (like `@EnableWebSecurity`, `SecurityFilterChain`, `@PreAuthorize`) so you can use it directly for exam prep and project work?
