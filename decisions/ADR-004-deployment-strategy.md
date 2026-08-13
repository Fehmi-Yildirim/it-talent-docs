
### `decisions/ADR-004-deployment-strategy.md`

```md
# ADR-004 — Deployment Strategy

**Status:** Accepted  
**Date:** 2026-08-12  
**Updated:** 2026-08-13

## Context

The project should be:

- inexpensive during development;
- easy to deploy;
- connected to GitHub;
- suitable for preview environments;
- capable of scaling later.

The frontend and backend are separate repositories.

## Decision

The initial deployment architecture will use Git-based continuous deployment.

The frontend and backend will be deployed independently.

The frontend will be deployed as a React/Vite application on a static or frontend hosting platform.

The backend will be deployed as a long-running Node.js/NestJS service.

The database will use managed PostgreSQL.

## Development and Deployment Flow

```text
Developer
    │
    ▼
Git
    │
    ▼
GitHub
    │
    ├──────────────────┐
    ▼                  ▼
Frontend             Backend
deployment           deployment
    │                  │
    └────────┬─────────┘
             │
             ▼
       Managed PostgreSQL