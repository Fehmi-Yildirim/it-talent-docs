
### `decisions/ADR-003-backend-stack.md`

```md
# ADR-003 — Backend Technology Stack

**Status:** Accepted  
**Date:** 2026-08-12  
**Updated:** 2026-08-13

## Context

The backend must provide:

- REST API;
- authentication;
- authorization;
- business logic;
- matching;
- AI integration;
- database access;
- validation;
- API documentation.

The backend must be modular, secure and maintainable.

## Decision

The backend will use:

- NestJS;
- TypeScript;
- Prisma;
- PostgreSQL.

The backend will expose a versioned REST API.

## Rationale

### NestJS

NestJS provides:

- modular architecture;
- dependency injection;
- guards;
- middleware;
- interceptors;
- decorators;
- testing support.

This fits the complexity expected from the platform.

### TypeScript

TypeScript provides static typing throughout the backend and keeps the frontend and backend technology stacks aligned.

### Prisma

Prisma provides:

- type-safe database access;
- schema management;
- migrations;
- strong TypeScript integration.

Prisma will be the primary database access layer.

### PostgreSQL

PostgreSQL is selected because the platform requires:

- relational data;
- strong consistency;
- complex relationships;
- filtering;
- transactions;
- indexing;
- future analytical capabilities.

## Architecture

```text
HTTP Request
     │
     ▼
Rate Limiting
     │
     ▼
Authentication
     │
     ▼
Authorization
     │
     ▼
Controller
     │
     ▼
Validation
     │
     ▼
Service
     │
     ▼
Domain / Business Logic
     │
     ▼
Prisma
     │
     ▼
PostgreSQL