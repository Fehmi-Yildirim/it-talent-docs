# ADR-004 — Deployment Strategy

**Status:** Accepted
**Date:** 2026-08-12
**Updated:** 2026-09-10

## Context

The IT Talent Platform consists of separate frontend, backend, and documentation repositories.

The frontend and backend have different runtime requirements and are therefore deployed independently.

The deployment architecture must support:

* independent frontend deployment;
* independent backend deployment;
* managed PostgreSQL;
* Git-based development and delivery;
* separate configuration for frontend and backend environments.

## Decision

The IT Talent Platform uses an independent deployment model for the frontend and backend.

The deployment architecture consists of:

* the React/Vite frontend as a frontend web application;
* the NestJS backend as a Node.js service;
* PostgreSQL as the application database.

The frontend communicates with the backend through the versioned REST API.

```text id="n7f3qk"
                    GitHub
                   /      \
                  /        \
                 ▼          ▼
        Frontend           Backend
        React/Vite         NestJS
            │                  │
            │    REST API      │
            └──────────────────┘
                       │
                       ▼
                 PostgreSQL
```

## Deployment Flow

The repositories provide the source for their respective application deployments:

```text id="0p1h5j"
Developer
    │
    ▼
Git
    │
    ▼
GitHub
    │
    ├───────────────┐
    ▼               ▼
Frontend          Backend
deployment        deployment
    │               │
    │               ▼
    │          PostgreSQL
    │
    ▼
Web Application
```

Frontend and backend deployments are independent. A frontend deployment does not require the backend application to be packaged with it, and the backend does not contain the frontend application.

## Environment Configuration

Frontend and backend configuration is maintained separately.

The frontend uses environment configuration for values such as the backend API URL.

The backend uses environment configuration for values such as:

* database connection;
* authentication secrets;
* CORS configuration;
* runtime configuration.

Secrets remain server-side and are not committed to the repositories.

## Database

PostgreSQL is treated as the persistent application database.

The backend communicates with PostgreSQL through Prisma.

The frontend never connects directly to the database.

## Consequences

### Positive

* Frontend and backend can be deployed independently.
* Each application can use its own runtime configuration.
* Deployment boundaries match the repository boundaries.
* Database access remains isolated behind the backend.
* Changes to one application do not require bundling the other application into the same deployment artifact.

### Negative

Frontend and backend deployments must remain compatible with the REST API contract.

Changes that affect both applications therefore require coordination between their respective repositories.

## Result

The IT Talent Platform uses a **separate deployment model for frontend and backend applications**, connected through the versioned REST API and backed by PostgreSQL.

The deployment architecture follows the same separation established by the project's multi-repository structure.
