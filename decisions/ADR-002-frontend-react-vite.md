# ADR-002 — Frontend Technology

**Status:** Accepted
**Date:** 2026-08-12
**Updated:** 2026-09-10

## Context

The IT Talent Platform requires a web application for candidates, recruiters, and administrators.

The frontend provides the user interface for:

* authentication;
* candidate profiles;
* recruiter functionality;
* job discovery;
* job details;
* applications;
* dashboards;
* skill management;
* language selection.

The frontend is maintained in the separate `it-talent-frontend` repository and communicates with the backend through the versioned REST API.

## Decision

The frontend uses:

* React;
* Vite;
* TypeScript;
* Oxlint.

The application is implemented as a client-side React application.

## Rationale

### React

React provides the component-based architecture used for:

* reusable UI components;
* pages and application views;
* forms;
* dashboards;
* job discovery;
* candidate and recruiter functionality;
* application management.

### Vite

Vite is used as the frontend build tool and development server.

It provides the build and development environment for the React application.

### TypeScript

TypeScript is used throughout the frontend codebase.

It provides static typing for components, application logic, API communication, configuration, and domain types.

### Oxlint

Oxlint is used for JavaScript and TypeScript linting.

It provides static analysis as part of the frontend development workflow.

## Frontend Architecture

The current frontend is organized around application, component, feature, page, service, type, and internationalization layers.

```text
React + Vite
     │
     ├── App
     ├── Components
     ├── Features
     ├── Pages
     ├── Services
     ├── Types
     ├── Configuration
     └── Internationalization
              │
              ▼
       NestJS REST API
```

The frontend is responsible for presentation and interaction, while the backend remains responsible for authentication, authorization, validation, business logic, and database access.

## Internationalization

The frontend supports:

* English (`en`);
* Dutch (`nl`).

English is the default language.

The internationalization layer provides translated interface text and falls back to English when a translation is unavailable.

## Consequences

### Positive

* Clear component-based frontend architecture.
* Strong typing throughout the application.
* Fast development and build workflow.
* Clear separation between UI and backend services.
* Frontend functionality can evolve independently from backend implementation through the REST API.
* Multilingual interface support is integrated into the frontend architecture.

### Negative

Frontend and backend changes may need to be coordinated when the API contract changes.

## Result

The IT Talent Platform frontend is implemented as a React application using Vite and TypeScript, with Oxlint for static analysis and a dedicated internationalization layer supporting English and Dutch.
