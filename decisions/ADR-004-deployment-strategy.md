# ADR-004 — Deployment Strategy

**Status:** Accepted  
**Date:** 2026-08-12  
**Updated:** 2026-11-09

## Context

The IT Talent Platform consists of separate frontend, backend, and documentation repositories.

The frontend and backend have different runtime requirements and can therefore be deployed independently.

The deployment architecture must support:

- Independent frontend deployment.
- Independent backend deployment.
- PostgreSQL as the persistent application database.
- Git-based development and delivery.
- Separate configuration for frontend and backend environments.
- Secure handling of environment-specific configuration and secrets.

The deployment strategy should follow the separation established by the platform's multi-repository architecture.

## Decision

The IT Talent Platform uses an independent deployment model for the frontend and backend.

The logical deployment architecture consists of:

- The React/Vite frontend as a frontend web application.
- The NestJS backend as a Node.js service.
- PostgreSQL as the application database.

The frontend communicates with the backend through the versioned REST API.

The backend communicates with PostgreSQL through Prisma.

The specific hosting providers and production infrastructure are deployment choices and are not prescribed by this decision.

## Deployment Architecture

The logical architecture is represented as follows:

```text id="n7f3qk"
                 GitHub
                /      \
               /        \
              ▼          ▼
         Frontend      Backend
         React/Vite    NestJS
             │            │
             │  REST API  │
             └────────────┘
                    │
                    ▼
               PostgreSQL
```

The frontend and backend are separate application deployments. PostgreSQL is accessed only by the backend.

## Deployment Flow

The repositories provide the source code for their respective application deployments.

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
 Frontend         Backend
 deployment       deployment
    │               │
    │               ▼
    │           PostgreSQL
    │
    ▼
Web Application
```

Frontend and backend deployments are independent.

A frontend deployment does not require the backend application to be packaged with it. Likewise, the backend does not contain the frontend application.

The deployment process may use automated CI/CD workflows, depending on the selected hosting and delivery infrastructure.

## Environment Configuration

Frontend and backend configuration is maintained separately.

### Frontend

The frontend uses environment configuration for values such as the backend API URL.

The API base URL is configured through:

```env id="r4o6go"
VITE_API_BASE_URL=http://localhost:3000/api/v1
```

Environment-specific values must be configured for the corresponding deployment environment.

Only values intended to be available to the browser may be included in frontend environment variables. Frontend environment variables must never contain secrets.

### Backend

The backend uses environment configuration for values such as:

- Database connection settings.
- Authentication secrets.
- CORS configuration.
- Runtime configuration.
- File upload configuration.
- Rate-limiting settings.

Sensitive values must be provided through the deployment environment or an appropriate secrets-management mechanism.

Secrets must not be committed to Git repositories or exposed to the frontend.

## Database

PostgreSQL is the persistent application database.

The backend communicates with PostgreSQL through Prisma.

The frontend never connects directly to the database.

Database schema changes are managed through Prisma migrations.

Database migrations must be reviewed and applied as part of the appropriate deployment process.

Production database backups and recovery procedures should be configured according to the requirements of the selected hosting environment.

## Environment Separation

Development, testing, and production environments should use separate configuration.

In particular:

- Database connections must point to the intended environment.
- Production secrets must be different from development secrets.
- CORS origins must be explicitly configured for the relevant frontend environments.
- Production configuration must not rely on development defaults.
- Environment-specific settings must be maintained outside committed source code.

## Consequences

### Positive

- Frontend and backend can be deployed independently.
- Each application can use its own runtime configuration.
- Deployment boundaries match the repository boundaries.
- Database access remains isolated behind the backend.
- Changes to one application do not require bundling the other application into the same deployment artifact.
- Hosting providers can be selected independently for frontend and backend requirements.
- Environment-specific configuration can be managed separately.

### Negative

- Frontend and backend deployments must remain compatible with the REST API contract.
- Changes affecting both applications require coordination between their repositories.
- Separate deployments introduce additional configuration and operational responsibilities.
- Database migrations and application releases must be coordinated to avoid compatibility problems.
- Monitoring, backups, security updates, and recovery procedures must be managed for the relevant deployment services.

## Result

The IT Talent Platform uses a **separate deployment model for frontend and backend applications**, connected through the versioned REST API and backed by PostgreSQL.

The deployment architecture follows the separation established by the multi-repository structure.

The specific hosting providers, production infrastructure, and CI/CD implementation remain environment-dependent and must be documented separately when they are established.