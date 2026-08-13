
### `decisions/ADR-002-frontend-react-vite.md`

```md
# ADR-002 — Frontend Technology

**Status:** Accepted  
**Date:** 2026-08-12  
**Updated:** 2026-08-13

## Context

The IT Talent Platform requires a modern web application for:

- candidates;
- recruiters;
- companies;
- job discovery;
- dashboards;
- authentication;
- matching results.

The frontend is maintained as a separate repository:

`it-talent-frontend`

The frontend communicates with the backend through a versioned REST API.

## Decision

The frontend will use:

- React;
- Vite;
- TypeScript;
- Oxlint.

The frontend will be implemented as a client-side React application.

## Rationale

### React

React provides the component-based UI architecture required for:

- candidate profiles;
- recruiter dashboards;
- company pages;
- job listings;
- search and filtering;
- matching interfaces;
- authentication flows.

### Vite

Vite is used as the frontend build tool and development server.

It provides:

- fast local development;
- fast production builds;
- native TypeScript support;
- straightforward React integration;
- lightweight architecture;
- simple deployment.

Vite is preferred over Next.js because the platform uses a separate frontend application and backend API.

### TypeScript

TypeScript provides static type checking and improves maintainability as the application grows.

It will be used throughout the frontend codebase.

### Oxlint

Oxlint is used as the initial JavaScript and TypeScript linter.

It provides fast static analysis and integrates directly with the Vite project.

## Architecture

```text
React + Vite
     │
     ├── UI Components
     │
     ├── Pages / Routes
     │
     ├── Client State
     │
     ├── API Client
     │
     └── Authentication
              │
              ▼
        NestJS REST API
              │
              ▼
        PostgreSQL / AI