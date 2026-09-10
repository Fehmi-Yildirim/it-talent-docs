# ADR-003 — Backend Technology Stack

**Status:** Accepted
**Date:** 2026-08-12
**Updated:** 2026-09-10

## Context

The IT Talent Platform requires a backend application that provides:

* REST API endpoints;
* authentication;
* authorization;
* request validation;
* business logic;
* user and role management;
* skills management;
* company and job functionality;
* database access.

The backend is maintained in the separate `it-talent-backend` repository.

## Decision

The backend uses:

* NestJS;
* TypeScript;
* Prisma;
* PostgreSQL.

The backend exposes a versioned REST API under:

```text
/api/v1
```

## Rationale

### NestJS

NestJS provides the application structure used by the backend, including:

* modular organization;
* dependency injection;
* controllers;
* services;
* guards;
* middleware;
* decorators;
* request validation;
* testing support.

This provides a clear structure for the platform's API and business logic.

### TypeScript

TypeScript is used throughout the backend.

It provides static typing for:

* controllers;
* services;
* DTOs;
* domain types;
* database access;
* API logic.

### Prisma

Prisma is the database access layer between the NestJS application and PostgreSQL.

It provides:

* type-safe database access;
* schema management;
* migrations;
* generated TypeScript types;
* structured relational queries.

### PostgreSQL

PostgreSQL is used as the primary database.

The platform contains relational data such as:

* users;
* candidates;
* recruiters;
* companies;
* skills;
* jobs;
* job requirements;
* applications.

PostgreSQL provides the relational structure and constraints required to maintain these relationships.

## Architecture

The current backend request flow is structured around controllers, services, validation, and database access:

```text
HTTP Request
     │
     ▼
NestJS
     │
     ├── Authentication
     │
     ├── Authorization
     │
     ├── Validation
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

Controllers expose the REST API, services contain application logic, and Prisma handles communication with PostgreSQL.

## Security Boundary

The backend is the authoritative application and security boundary.

It is responsible for:

* authenticating users;
* enforcing roles and permissions;
* validating incoming data;
* applying business rules;
* accessing protected database resources;
* returning controlled API responses.

The frontend does not access PostgreSQL directly.

## Consequences

### Positive

* Clear separation between API, business logic, and database access.
* Strong TypeScript integration across the backend.
* Type-safe database operations through Prisma.
* Relational data integrity through PostgreSQL.
* Modular backend structure suitable for the platform's different domains.
* Versioned REST API boundary between frontend and backend.

### Negative

Frontend functionality that depends on backend API changes requires coordination between the frontend and backend repositories.

## Result

The IT Talent Platform backend is implemented with **NestJS and TypeScript**, using **Prisma** as the database access layer and **PostgreSQL** as the primary relational database.

The backend exposes the platform functionality through the versioned `/api/v1` REST API.
