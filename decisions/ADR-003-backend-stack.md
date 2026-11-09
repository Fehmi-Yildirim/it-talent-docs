# ADR-003 — Backend Technology Stack

**Status:** Accepted  
**Date:** 2026-08-12  
**Updated:** 2026-11-09

## Context

The IT Talent Platform requires a backend application that provides:

- REST API endpoints.
- Authentication and authorization.
- Request validation.
- Business logic.
- User and role management.
- Candidate and recruiter functionality.
- Skills management.
- Company and job functionality.
- Application management.
- Database access.

The backend is maintained in the separate `it-talent-backend` repository.

The backend technology must support a modular architecture, reliable database access, maintainable application logic, and a clear security boundary between the frontend and protected platform resources.

## Decision

The backend uses the following technologies:

- **NestJS** as the backend application framework.
- **TypeScript** as the programming language.
- **Prisma** as the database access layer and ORM.
- **PostgreSQL** as the primary relational database.

The backend exposes a versioned REST API under:

```text
/api/v1
```

## Rationale

### NestJS

NestJS provides the application structure used by the backend, including:

- Modular organization.
- Dependency injection.
- Controllers.
- Services.
- Guards.
- Middleware.
- Decorators.
- Request validation.
- Testing support.

This structure supports the separation of API responsibilities, application logic, and cross-cutting concerns.

The backend is organized into domain-specific modules, including authentication, users, candidates, recruiters, companies, jobs, applications, skills, settings, and dashboards.

### TypeScript

TypeScript is used throughout the backend.

It provides static typing for:

- Controllers.
- Services.
- Data Transfer Objects (DTOs).
- Domain types.
- Database access.
- API logic.
- Application configuration.

This improves maintainability and helps detect type-related issues during development.

### Prisma

Prisma is used as the database access layer between the NestJS application and PostgreSQL.

It provides:

- Type-safe database access.
- Schema management.
- Database migrations.
- Generated TypeScript types.
- Structured relational queries.

Prisma also provides a consistent way to manage database schema changes throughout development.

### PostgreSQL

PostgreSQL is used as the primary relational database.

The platform contains related data entities such as:

- Users.
- Candidates.
- Recruiters.
- Companies.
- Skills.
- Jobs.
- Job requirements.
- Applications.
- Candidate skills.
- Matching results.

PostgreSQL provides relational structures, constraints, and transaction support for maintaining data integrity.

## Architecture

The backend request flow is structured around NestJS controllers, application services, validation, and database access.

```text
HTTP Request
     │
     ▼
NestJS Application
     │
     ├── Authentication
     ├── Authorization
     ├── Request Validation
     ├── Rate Limiting
     │
     ▼
  Controller
     │
     ▼
   Service
     │
     ▼
   Prisma
     │
     ▼
 PostgreSQL
```

Controllers expose REST API endpoints and handle incoming requests.

Services contain application and business logic.

Validation ensures that incoming data conforms to the expected request structure and rules.

Prisma handles database communication, while PostgreSQL stores the platform's relational data.

The backend also uses centralized configuration management and scheduled-task support where required by application functionality.

## Security Boundary

The backend is the authoritative application and security boundary.

It is responsible for:

- Authenticating users.
- Enforcing roles and permissions.
- Validating incoming data.
- Applying business rules.
- Checking access to protected resources.
- Accessing the database.
- Returning controlled API responses.

The frontend communicates with the backend through the REST API and does not access PostgreSQL directly.

Client-side validation and access checks may improve the user experience, but they do not replace server-side validation and authorization.

## Consequences

### Positive

- Clear separation between API, business logic, and database access.
- Strong TypeScript integration across the backend.
- Type-safe database operations through Prisma.
- Relational data integrity through PostgreSQL.
- Modular backend structure suitable for the platform's different domains.
- Centralized application configuration.
- Dedicated support for request validation and rate limiting.
- Versioned REST API boundary between frontend and backend.

### Negative

- Frontend functionality that depends on backend API changes requires coordination between the frontend and backend repositories.
- Database schema changes require corresponding Prisma schema and migration updates.
- The NestJS module structure and dependencies require ongoing maintenance.
- Database migrations must be reviewed and applied carefully to avoid data loss or compatibility issues.

## Result

The IT Talent Platform backend is implemented with **NestJS and TypeScript**, using **Prisma** as the database access layer and **PostgreSQL** as the primary relational database.

The backend exposes platform functionality through the versioned `/api/v1` REST API.

The backend remains responsible for server-side authentication, authorization, request validation, business logic, and database access.