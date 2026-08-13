
### `decisions/README.md`

```md
# Architecture Decision Records

This directory contains important architectural decisions for the IT Talent Platform.

ADRs document decisions that have a meaningful impact on the architecture, development process or long-term maintainability of the platform.

## Decisions

| ADR | Decision | Status |
|---|---|---|
| ADR-001 | Multi-Repository Architecture | Accepted |
| ADR-002 | Frontend Technology — React + Vite | Accepted |
| ADR-003 | Backend Technology Stack — NestJS + Prisma + PostgreSQL | Accepted |
| ADR-004 | Deployment Strategy | Accepted |

## Current Architecture Decisions

The current MVP architecture is based on:

- three independent repositories;
- React + Vite + TypeScript + Oxlint for the frontend;
- NestJS + TypeScript + Prisma + PostgreSQL for the backend;
- versioned REST API communication;
- independently deployed frontend and backend;
- managed PostgreSQL in production;
- backend-enforced security and authorization.

## ADR Lifecycle

An ADR can have one of the following statuses:

- Proposed
- Accepted
- Superseded
- Deprecated

Once an architectural decision has been implemented, it should normally remain documented even if it is later replaced.

## Creating a New ADR

New decisions should use:

```text
ADR-NNN-short-description.md