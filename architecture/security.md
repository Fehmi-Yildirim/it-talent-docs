# IT Talent Platform — Security Architecture

**Document:** `security.md`
**Version:** 0.4.0
**Status:** Accepted
**Last updated:** 2026-09-10

## 1. Purpose

This document describes the security architecture of the IT Talent Platform.

Security is enforced primarily by the backend and applies to authentication, authorization, validation, API access, database access, and protection of user and recruitment data.

The platform uses a React/Vite frontend, a NestJS backend, Prisma, and PostgreSQL.

---

## 2. Security Principles

The platform follows these principles:

* Authentication is handled by the backend.
* Authorization is enforced by the backend.
* Client-side restrictions are not considered security controls.
* Incoming API data is validated before processing.
* Database access is restricted to the backend.
* Passwords are stored as secure hashes.
* Sensitive database fields are not exposed directly through API responses.
* Secrets remain server-side.
* Resource ownership is enforced by backend business logic.

---

## 3. Security Architecture

The security boundary is structured as follows:

```text
User
 │
 ▼
React + Vite Frontend
 │
 │ HTTP/HTTPS
 ▼
NestJS Backend
 │
 ├── Authentication
 ├── Authorization
 ├── Validation
 ├── Business Logic
 └── API Access Control
 │
 ▼
Prisma
 │
 ▼
PostgreSQL
```

The frontend does not communicate directly with PostgreSQL.

The NestJS backend is the primary security boundary between the application interface and persistent data.

---

## 4. Frontend Security Boundary

The frontend is responsible for:

* user interface;
* navigation;
* forms;
* client-side interaction;
* application state;
* API communication;
* language selection;
* displaying role-specific functionality.

The frontend must not be trusted to determine:

* user identity;
* user role;
* ownership;
* permissions;
* access to protected resources.

Client-side validation improves the user experience, while authoritative validation and authorization are performed by the backend.

---

## 5. Backend Security Boundary

The NestJS backend is responsible for:

* authentication;
* authorization;
* request validation;
* business rules;
* database access;
* user and role management;
* protected resource access;
* ownership rules.

Security-sensitive decisions are therefore made on the server.

---

## 6. API Protection

The backend exposes the API under:

```text
/api/v1
```

The API uses a versioned base path so that API contracts can evolve without changing the security boundary of the existing version.

Example:

```text
GET /api/v1/health
```

All protected API operations must pass through the backend authentication and authorization mechanisms.

---

## 7. Authentication

Authentication is handled by the NestJS backend.

The authentication architecture uses:

* JWT authentication;
* Passport;
* Passport JWT;
* Argon2 password hashing.

User passwords are stored as password hashes rather than plaintext passwords.

Conceptually:

```text
Password
   │
   ▼
Argon2
   │
   ▼
passwordHash
   │
   ▼
Database
```

Password hashes are internal security data and must not be returned through normal API responses.

---

## 8. Authorization and Roles

The platform has three user roles:

* `CANDIDATE`
* `RECRUITER`
* `ADMIN`

The effective role is determined by the authenticated backend user context.

The frontend may use role information to display appropriate navigation and functionality, but backend authorization remains authoritative.

### Candidate

Candidates can access resources belonging to their own account and candidate profile.

Candidate-specific security boundaries include:

* own profile;
* own skills;
* own applications;
* own candidate information.

### Recruiter

Recruiters can access recruiter functionality and resources associated with their recruiter/company context.

### Admin

Administrators have access to administrative functionality such as user and skill management.

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

* DTO-based validation;
* transformation of incoming values;
* removal/rejection of unexpected properties;
* protection against accepting fields that are not part of an endpoint's DTO.

API endpoints use explicit DTOs for operations such as authentication, users, candidates, skills, companies, jobs, and job requirements.

Validation takes place before business logic is executed.

---

## 11. CORS

The backend enables CORS for frontend API communication.

The allowed origin is configurable through the environment configuration.

Credentialed requests must use an explicitly trusted frontend origin.

Production deployments should therefore configure the frontend origin explicitly rather than relying on permissive wildcard access.

---

## 12. Database Security

PostgreSQL is the platform's persistent database.

Prisma provides the backend database access layer.

The architecture is:

```text
React
  │
  ▼
NestJS
  │
  ▼
Prisma
  │
  ▼
PostgreSQL
```

Database credentials remain on the backend.

They must not be:

* exposed to the frontend;
* included in client-side configuration;
* returned by API responses;
* written to application logs;
* committed to source control.

---

## 13. Database Integrity

The database uses structured identifiers and relational constraints to maintain data integrity.

Relationships between entities such as users, candidates, recruiters, companies, skills, jobs, and job requirements are managed through the database schema and Prisma.

Many-to-many relationships use dedicated relation models where required.

Database constraints provide an additional layer of protection against invalid or duplicate relationships.

---

## 14. Sensitive Data

The platform processes personal and professional information, including:

* email addresses;
* candidate profiles;
* professional skills;
* salary information;
* availability;
* work preferences;
* company information;
* job information;
* applications.

API responses must expose only information appropriate to the authenticated user and requested operation.

Database entities should not automatically be treated as public API response objects.

Response DTOs should be used where sensitive or internal fields must be excluded.

---

## 15. API Response Security

Internal security fields must not be exposed through normal API responses.

Examples include:

```text
passwordHash
```

and other internal authentication or authorization metadata.

The intended data flow is:

```text
Database Entity
      │
      ▼
Business Logic
      │
      ▼
Response DTO
      │
      ▼
JSON Response
```

This keeps the database model separate from the public API contract.

---

## 16. Error Handling

API errors should not expose internal implementation details.

Production responses should not reveal:

* password hashes;
* database credentials;
* JWT secrets;
* stack traces;
* internal file paths;
* database queries;
* other security-sensitive configuration.

Errors should use predictable HTTP responses and messages appropriate to the API contract.

---

## 17. Secrets and Environment Configuration

Security-sensitive configuration must remain outside the source code.

Examples include:

```text
DATABASE_URL
JWT_SECRET
CORS_ORIGIN
```

Environment-specific values should be supplied through environment configuration or the deployment platform's secret-management facilities.

Local environment files containing credentials must not be committed to the repository.

An `.env.example` file may document required variable names without containing real credentials.

---

## 18. HTTPS

Production communication between clients and the platform API must use HTTPS.

This protects authentication information and application data while it is transmitted between the frontend and backend.

Plain HTTP is appropriate only for controlled local development environments.

---

## 19. Security Headers

Production infrastructure should provide appropriate HTTP security headers.

Relevant protections include:

* Content Security Policy;
* `X-Content-Type-Options`;
* `Referrer-Policy`;
* frame protection;
* Strict Transport Security.

The exact configuration belongs to the production deployment layer.

---

## 20. Authentication and Browser Security

Authentication credentials must be handled securely by the application.

The authentication mechanism must protect authenticated requests against common browser-based risks, including:

* credential theft;
* unauthorized token reuse;
* XSS-related credential exposure;
* CSRF where applicable;
* session-related attacks.

Authentication decisions remain server-side regardless of how the frontend stores or sends authentication state.

---

## 21. Rate Limiting and Abuse Protection

Authentication and other security-sensitive API operations should be protected against excessive or automated requests.

Particularly important areas include:

```text
POST /api/v1/auth/login
POST /api/v1/auth/register
```

Rate limiting and abuse protection belong to the API security layer and should be configured according to the production deployment requirements.

---

## 22. Data Privacy

The platform handles personal and professional recruitment information.

Security controls support privacy principles such as:

* controlled access;
* data minimization;
* appropriate data exposure;
* secure storage;
* account-level access control;
* protection of personal information.

Privacy and GDPR requirements apply to the handling of candidate and recruiter data and should remain aligned with the platform's data model and operational policies.

---

## 23. Security Testing

Security-sensitive functionality should be covered by automated tests where applicable.

Important test areas include:

* authentication;
* invalid credentials;
* protected endpoints;
* role-based authorization;
* ownership checks;
* DTO validation;
* rejection of unexpected properties;
* sensitive response fields;
* database relationship integrity.

---

## 24. Security Development Principle

The platform follows:

> **Secure by default, validate on the backend, and trust as little as possible.**

Security controls are part of the application architecture rather than only a deployment concern.

Every protected feature should define:

* who can access it;
* which resources can be accessed;
* which operations are permitted;
* which input is accepted;
* which information can be returned.

---

## 25. Relationship with Other Architecture Documents

This document is aligned with:

* `architecture.md`
* `database.md`
* `api.md`

Security decisions should remain consistent with the actual backend, database, and API implementation.

When a security-sensitive architectural decision changes, the relevant architecture documentation should be updated.

---

## 26. Security Status

**Security Architecture: ACCEPTED**

The IT Talent Platform uses backend-enforced authentication, authorization, validation, database access control, and protected handling of application data as core security principles.

The backend remains the authoritative security boundary for the platform.
