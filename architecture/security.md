# IT Talent Platform — Security Architecture

**Document:** `security.md`  
**Version:** `0.5.0`  
**Status:** Accepted  
**Last updated:** 2026-11-09

---

## 1. Purpose

This document describes the security architecture of the IT Talent Platform.

Security is enforced primarily by the backend and applies to authentication, authorization, validation, API access, database access, and the protection of user and recruitment data.

The platform uses:

- React and Vite for the frontend;
- NestJS for the backend;
- Prisma ORM for database access;
- PostgreSQL as the relational database.

The NestJS backend is the authoritative security boundary between the frontend and persistent application data.

This document distinguishes between implemented backend security mechanisms, architectural principles, and production security requirements.

---

## 2. Security Principles

The platform follows these principles:

- Authentication is handled by the backend.
- Authorization is enforced by the backend.
- Client-side restrictions are not considered security controls.
- Incoming API data is validated before processing.
- Database access is restricted to the backend.
- Passwords are stored as secure password hashes.
- Sensitive database fields are not exposed directly through API responses.
- Secrets remain server-side.
- Resource ownership is enforced by backend business logic.
- Access is granted according to the principle of least privilege.
- Security-sensitive operations are protected against abuse.

Security controls must be applied consistently across the application rather than relying on frontend behavior.

---

## 3. Security Architecture

The security boundary is structured as follows:

```text
User
 |
 ▼
React + Vite Frontend
 |
 | HTTP/HTTPS
 ▼
NestJS Backend
 |
 ├── Authentication
 ├── Authorization
 ├── Validation
 ├── Business Logic
 └── API Access Control
 |
 ▼
Prisma
 |
 ▼
PostgreSQL
```

The frontend does not communicate directly with PostgreSQL.

The NestJS backend is responsible for authentication, authorization, request validation, business rules, and controlled database access.

The database provides an additional layer of integrity through relational constraints and data types.

---

## 4. Frontend Security Boundary

The frontend is responsible for:

- user interface;
- navigation;
- forms;
- client-side interaction;
- application state;
- API communication;
- language selection;
- displaying role-specific functionality.

The frontend must not be trusted to determine:

- user identity;
- user role;
- resource ownership;
- permissions;
- access to protected resources.

Client-side validation improves the user experience, while authoritative validation and authorization must be performed by the backend.

Hiding a navigation item or restricting a frontend route does not replace backend access control.

---

## 5. Backend Security Boundary

The NestJS backend is responsible for:

- authentication;
- authorization;
- request validation;
- business rules;
- database access;
- user and role management;
- protected resource access;
- ownership enforcement;
- controlled API responses;
- security-sensitive configuration.

Security-sensitive decisions must be made on the server.

Every protected operation must verify the authenticated user and the permissions required for the requested resource and action.

---

## 6. API Protection

The backend exposes the API under:

```text
/api/v1
```

The versioned base path allows API contracts to evolve while maintaining a defined API version.

Example:

```text
GET /api/v1/health
```

Protected API operations must pass through the backend authentication and authorization mechanisms.

Public endpoints, such as health checks and permitted authentication operations, must be explicitly identified and should expose only the information necessary for their purpose.

The API contract is documented separately in `architecture/api.md`.

---

## 7. Authentication

Authentication is handled by the NestJS backend.

The documented authentication stack includes:

- JWT authentication;
- Passport;
- Passport JWT;
- Argon2 password hashing.

User passwords are stored as password hashes rather than plaintext passwords.

Conceptually:

```text
Password
   |
   ▼
Argon2
   |
   ▼
passwordHash
   |
   ▼
Database
```

Password hashes are internal security data and must never be returned through normal API responses.

Authentication tokens must be validated by the backend before protected operations are performed.

Token expiration, signing configuration, and secret management must follow the backend authentication implementation and production configuration.

---

## 8. Authorization and Roles

The platform defines three user roles:

- `CANDIDATE`
- `RECRUITER`
- `ADMIN`

The effective role is determined by the authenticated backend user context.

The frontend may use role information to display appropriate navigation and functionality, but backend authorization remains authoritative.

### Candidate

Candidates may access resources belonging to their own account and candidate profile, subject to the permissions implemented by the backend.

Candidate-specific security boundaries include:

- own profile;
- own candidate information;
- own applications;
- candidate-related skills and matching information where access is supported.

### Recruiter

Recruiters may access recruiter functionality and resources associated with their authorized recruiter and company context.

Access to company information, jobs, and applications must be checked by the backend.

### Admin

Administrators may access administrative functionality, such as user and skill management, according to the implemented permission rules.

Administrative privileges must not be granted through untrusted client input.

Public registration must not allow a user to assign themselves the `ADMIN` role.

---

## 9. Resource Ownership

Ownership is enforced by the backend.

The client must not be trusted to determine sensitive ownership identifiers such as:

```text
userId
candidateId
recruiterId
companyId
```

Where an endpoint represents the authenticated user's own resource, ownership should be resolved from the authenticated user context.

For example:

```text
PATCH /api/v1/candidates/me
```

should operate on the candidate associated with the authenticated account rather than relying on a client-provided user identifier.

For resources identified by a URL parameter, the backend must verify that the authenticated user is authorized to access and modify the referenced resource.

This protects against insecure direct object reference vulnerabilities and unauthorized cross-account access.

---

## 10. Input Validation

The backend uses NestJS `ValidationPipe` for API request validation.

The global validation configuration includes:

```text
whitelist: true
transform: true
forbidNonWhitelisted: true
```

This provides:

- DTO-based validation;
- transformation of incoming values;
- rejection of unexpected properties;
- protection against accepting fields that are not part of an endpoint's DTO.

API endpoints use explicit DTOs for operations such as authentication, users, candidates, skills, companies, jobs, and job requirements.

Validation takes place before business logic is executed.

DTO validation does not replace authorization checks or domain-specific business validation. Both must be applied where required.

---

## 11. CORS

The backend enables Cross-Origin Resource Sharing (CORS) for frontend API communication.

Allowed origins are configurable through the backend environment configuration.

The `CORS_ORIGIN` environment variable defines trusted frontend origins.

Credentialed requests must use explicitly trusted origins.

Production deployments must configure the intended frontend origin explicitly. Wildcard origin access is not appropriate for the production configuration.

CORS is a browser access-control mechanism. It does not replace authentication, authorization, or server-side request validation.

---

## 12. Database Security

PostgreSQL is the platform's persistent database.

Prisma provides the backend database access layer.

The architecture is:

```text
React
  |
  ▼
NestJS
  |
  ▼
Prisma
  |
  ▼
PostgreSQL
```

Database credentials remain on the backend.

They must not be:

- exposed to the frontend;
- included in client-side configuration;
- returned by API responses;
- written to application logs;
- committed to source control.

Database access must be restricted to the backend application and authorized operational processes.

---

## 13. Database Integrity

The database uses structured identifiers and relational constraints to maintain data integrity.

Relationships between entities such as users, candidates, recruiters, companies, skills, jobs, and job requirements are managed through the Prisma schema and database migrations.

Dedicated relation models are used for domain relationships where required, such as:

- `CandidateSkill`;
- `JobRequirement`.

Database constraints provide an additional layer of protection against invalid references and duplicate relationships.

Database integrity does not replace application-level authorization. A valid database relationship does not automatically mean that a user is permitted to access the related record.

---

## 14. Sensitive Data

The platform processes personal and professional information, including:

- email addresses;
- candidate profiles;
- professional skills;
- salary information;
- availability;
- work preferences;
- company information;
- job information;
- applications.

API responses must expose only information appropriate to the authenticated user and requested operation.

Database entities must not automatically be treated as public API response objects.

Response DTOs or explicit response mapping should be used to exclude sensitive and internal fields.

Access to personal information must follow the backend authorization rules and the principle of least privilege.

---

## 15. API Response Security

Internal security fields must not be exposed through normal API responses.

An example is:

```text
passwordHash
```

Other internal authentication and authorization metadata must also remain protected.

The intended data flow is:

```text
Database Entity
      |
      ▼
Business Logic
      |
      ▼
Response DTO
      |
      ▼
JSON Response
```

This keeps the database model separate from the public API contract.

Response mapping must be reviewed whenever database entities or API response structures change.

---

## 16. Error Handling

API errors should not expose internal implementation details.

Production responses must not reveal:

- password hashes;
- database credentials;
- JWT secrets;
- stack traces;
- internal file paths;
- database queries;
- sensitive environment configuration.

Errors should use predictable HTTP responses and messages appropriate to the API contract.

Detailed diagnostic information may be recorded in appropriately protected server-side logs, but must not be exposed to clients.

Error logging must avoid recording passwords, authentication tokens, and other sensitive request data.

---

## 17. Secrets and Environment Configuration

Security-sensitive configuration must remain outside source code.

Examples include:

```text
DATABASE_URL
JWT_SECRET
CORS_ORIGIN
```

Environment-specific values should be supplied through environment configuration or the deployment platform's secret-management facilities.

Local environment files containing credentials must not be committed to the repository.

An `.env.example` file may document required variable names and safe example values without containing real credentials.

The backend environment configuration validates required settings.

Production secrets must:

- use sufficiently strong values;
- be accessible only to authorized services and operators;
- be rotated when exposure is suspected;
- never be included in frontend build-time configuration.

The JWT secret must meet the minimum length and security requirements defined by the backend configuration.

---

## 18. HTTPS

Production communication between clients and the platform API must use HTTPS.

This protects authentication information and application data while it is transmitted between the frontend and backend.

Plain HTTP is appropriate only for controlled local development environments.

Production deployments should also ensure that redirects, proxy configuration, and trusted transport settings do not unintentionally permit insecure communication.

---

## 19. Security Headers

Production infrastructure should provide appropriate HTTP security headers.

Relevant protections include:

- Content Security Policy (CSP);
- `X-Content-Type-Options`;
- `Referrer-Policy`;
- frame protection, such as `frame-ancestors` or `X-Frame-Options`;
- Strict Transport Security (HSTS).

The exact configuration belongs to the backend or production deployment layer.

The presence of a security header must be verified in actual HTTP responses. Listing a header as an architectural requirement does not establish that it is already configured.

---

## 20. Authentication and Browser Security

Authentication credentials must be handled securely by the application.

The authentication mechanism must protect authenticated requests against common browser-based risks, including:

- credential theft;
- unauthorized token reuse;
- XSS-related credential exposure;
- CSRF where applicable;
- session-related attacks.

The exact browser security controls depend on the implemented token transport and storage strategy.

If authentication uses cookies, cookie attributes such as `HttpOnly`, `Secure`, and `SameSite` must be configured appropriately, and CSRF protection must be considered.

If authentication uses bearer tokens in frontend-managed storage, the application must account for the risks of token exposure through cross-site scripting.

Authentication decisions remain server-side regardless of how the frontend stores or sends authentication state.

The actual token storage and transport implementation should be documented and verified separately.

---

## 21. Rate Limiting and Abuse Protection

The backend uses NestJS throttling for authentication-related operations.

The documented configuration includes separate rate limits for:

```text
POST /api/v1/auth/login
POST /api/v1/auth/register
```

The environment configuration includes:

```text
AUTH_LOGIN_TTL_MS=60000
AUTH_LOGIN_LIMIT=5

AUTH_REGISTER_TTL_MS=60000
AUTH_REGISTER_LIMIT=3
```

These values define the configured time windows and request limits for the corresponding authentication operations.

Rate limiting helps reduce automated abuse, excessive authentication attempts, and registration spam.

Additional protection may be appropriate for other sensitive operations, depending on actual traffic patterns and abuse risks.

Production deployments should review rate-limit settings and account for reverse proxies or multiple backend instances where applicable.

---

## 22. Data Privacy

The platform handles personal and professional recruitment information.

Security controls support privacy principles such as:

- controlled access;
- data minimization;
- appropriate data exposure;
- secure storage;
- account-level access control;
- protection of personal information;
- appropriate data retention and deletion.

Privacy and GDPR requirements apply to the handling of candidate and recruiter data.

The platform's data model, operational procedures, and privacy policies should remain aligned with applicable legal requirements.

Technical access controls alone do not establish full GDPR compliance.

---

## 23. Security Testing

Security-sensitive functionality should be covered by automated tests where applicable.

Important test areas include:

- authentication;
- invalid credentials;
- protected endpoints;
- role-based authorization;
- ownership checks;
- DTO validation;
- rejection of unexpected properties;
- sensitive response fields;
- database relationship integrity;
- authentication rate limiting;
- unauthorized access to other users' resources.

Tests should verify both successful access by authorized users and rejection of unauthorized requests.

Security tests should be maintained as the API and domain model evolve.

---

## 24. Security Development Principle

The platform follows:

> **Secure by default, validate on the backend, and trust as little as possible.**

Security controls are part of the application architecture rather than only a deployment concern.

Every protected feature should define:

- who can access it;
- which resources can be accessed;
- which operations are permitted;
- which input is accepted;
- which information can be returned;
- how unauthorized access is rejected.

Security-sensitive changes should be reviewed for their impact on authentication, authorization, data exposure, and ownership.

---

## 25. Relationship with Other Architecture Documents

This document is aligned with:

- `architecture.md`;
- `database.md`;
- `api.md`.

Each document describes a different architectural concern:

| Document | Responsibility |
|---|---|
| `architecture.md` | Overall system architecture and component responsibilities |
| `database.md` | Persisted entities, relationships, constraints, and indexes |
| `api.md` | HTTP endpoints, request and response contracts |
| `security.md` | Authentication, authorization, validation, and data protection |

Security decisions must remain consistent with the actual backend, database, and API implementation.

When a security-sensitive architectural decision changes, the relevant documentation must be reviewed and updated.

---

## 26. Security Status

**Security Architecture: ACCEPTED**

The IT Talent Platform uses backend-enforced authentication, authorization, validation, database access control, and protected handling of application data as core security principles.

The backend remains the authoritative security boundary for the platform.

The accepted security architecture defines the controls and requirements that must be maintained as the platform evolves. Production security settings must be verified against the deployed configuration and actual application behavior.
