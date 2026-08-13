

# `ADR-003-backend-stack.md`

# ADR-003 — Backend Technology Stack

**Status:** Accepted  
**Date:** 2026-08-12

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

The backend should be modular and maintainable.

## Decision

The backend will use:

- NestJS;
- TypeScript;
- Prisma;
- PostgreSQL.

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

Using TypeScript across frontend and backend reduces context switching and allows shared conceptual models.

### Prisma

Prisma provides:

- type-safe database access;
- migrations;
- schema management;
- good TypeScript integration.

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
Controller
     │
     ▼
Service
     │
     ▼
Domain Logic
     │
     ▼
Repository / Prisma
     │
     ▼
PostgreSQL