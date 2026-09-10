# Architecture Decision Records

This directory contains the Architecture Decision Records (ADRs) for the IT Talent Platform.

ADRs document important architectural decisions that define how the platform is structured and developed.

## Decisions

| ADR     | Decision                                                | Status   |
| ------- | ------------------------------------------------------- | -------- |
| ADR-001 | Multi-Repository Architecture                           | Accepted |
| ADR-002 | Frontend Technology — React + Vite                      | Accepted |
| ADR-003 | Backend Technology Stack — NestJS + Prisma + PostgreSQL | Accepted |
| ADR-004 | Deployment Strategy                                     | Accepted |

## Current Architecture

The current IT Talent Platform architecture is based on:

* three independent repositories;
* React + Vite + TypeScript + Oxlint for the frontend;
* NestJS + TypeScript for the backend;
* Prisma as the database access layer;
* PostgreSQL as the primary database;
* versioned REST API communication under `/api/v1`;
* independently deployed frontend and backend applications;
* backend-enforced authentication, authorization, and validation;
* English and Dutch frontend support.

## ADR Lifecycle

ADRs use the following statuses:

* **Proposed** — decision is under discussion;
* **Accepted** — decision is approved and forms part of the architecture;
* **Superseded** — replaced by a newer architectural decision;
* **Deprecated** — no longer applicable.

Accepted decisions remain documented when they are later replaced, providing a record of the architectural evolution of the platform.

## Creating a New ADR

New architectural decisions should use the following naming convention:

```text id="g4u8zn"
ADR-NNN-short-description.md
```

Each ADR should clearly describe:

* the context;
* the decision;
* the rationale;
* the consequences.

New ADRs should be added to this README.
