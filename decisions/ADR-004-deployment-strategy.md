
# `ADR-004-deployment-strategy.md`

# ADR-004 — Deployment Strategy

**Status:** Accepted  
**Date:** 2026-08-12

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

The frontend will be deployed using Vercel or an equivalent Next.js-compatible platform.

The backend will be deployed independently using a platform suitable for long-running Node.js/NestJS services.

The database will use managed PostgreSQL.

## Development Flow

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
    └───────┬───────┘
            ▼
        PostgreSQL