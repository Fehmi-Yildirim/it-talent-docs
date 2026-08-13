# ADR-002 — Frontend Technology

**Status:** Accepted  
**Date:** 2026-08-12

## Context

The IT Talent platform requires a modern web application for:

- candidates;
- recruiters;
- companies;
- job discovery;
- dashboards;
- authentication;
- matching results.

The frontend must provide a responsive user experience and a maintainable architecture that can evolve as the platform grows.

The frontend is maintained as a separate repository:

`it-talent-frontend`

## Decision

The frontend will use:

- React;
- Vite;
- TypeScript;
- Oxlint.

The frontend will be implemented as a client-side React application and will communicate with the backend through a versioned REST API.

## Rationale

### React

React provides the component-based UI model required by the platform.

It allows us to build reusable components for:

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
- a lightweight frontend architecture;
- simple deployment to modern hosting platforms.

Vite is preferred over Next.js because the initial platform architecture separates the frontend application from the backend API.

### TypeScript

TypeScript provides static type checking and improves maintainability as the application grows.

It will be used throughout the frontend codebase.

### Oxlint

Oxlint is used as the initial JavaScript and TypeScript linter.

It provides fast static analysis and integrates directly with the Vite project.

## Architecture

Conceptually:

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
        Backend REST API
              │
              ▼
        it-talent-backend