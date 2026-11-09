# Architecture Decision Records

This directory contains the Architecture Decision Records (ADRs) for the IT Talent Platform.

ADRs document important architectural decisions that define how the platform is structured, developed, and maintained.

Each ADR records the context, decision, rationale, and consequences of an architectural choice. Together, they provide a history of the platform's architectural direction.

## Decisions

| ADR | Decision | Status |
|---|---|---|
| ADR-001 | Multi-Repository Architecture | Accepted |
| ADR-002 | Frontend Technology — React + Vite + TypeScript | Accepted |
| ADR-003 | Backend Technology Stack — NestJS + Prisma + PostgreSQL | Accepted |
| ADR-004 | Deployment Strategy | Accepted |

## Current Architecture

The documented IT Talent Platform architecture is based on the following decisions and technologies:

- Three independent repositories:
  - `it-talent-frontend`
  - `it-talent-backend`
  - `it-talent-docs`
- React, Vite, TypeScript, and Oxlint for the frontend.
- NestJS and TypeScript for the backend.
- Prisma as the database access layer.
- PostgreSQL as the primary relational database.
- Versioned REST API communication under `/api/v1`.
- Independent deployment of frontend and backend applications.
- Backend-enforced authentication, authorization, and request validation.
- English and Dutch frontend language support.

These points describe the documented architecture and technology choices. Specific deployment providers, production infrastructure, and operational configurations are documented separately when established.

## ADR Lifecycle

ADRs use the following statuses:

- **Proposed** — The decision is under discussion and has not yet been approved.
- **Accepted** — The decision is approved and forms part of the documented architecture.
- **Superseded** — The decision has been replaced by a newer architectural decision.
- **Deprecated** — The decision is no longer applicable but remains documented for historical reference.

Accepted decisions remain documented when they are later replaced, providing a record of the architectural evolution of the platform.

When an ADR is superseded, the replacement ADR should reference the previous decision where appropriate.

## Creating a New ADR

New architectural decisions should use the following naming convention:

```text id="g4u8zn"
ADR-NNN-short-description.md
```

Each ADR should clearly describe:

- **Context** — The problem, situation, or requirement that led to the decision.
- **Decision** — The architectural choice that was made.
- **Rationale** — Why this option was selected.
- **Consequences** — The positive and negative effects of the decision.

New ADRs should be added to this index.

Use sequential numbering and keep ADR titles, filenames, statuses, and index entries consistent.

An existing ADR should be updated or superseded when an architectural decision changes. Historical decisions should not be silently rewritten.