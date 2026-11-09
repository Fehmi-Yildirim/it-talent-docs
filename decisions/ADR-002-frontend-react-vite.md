# ADR-002 — Frontend Technology

**Status:** Accepted  
**Date:** 2026-08-12  
**Updated:** 2026-11-09

## Context

The IT Talent Platform requires a web application for candidates, recruiters, and administrators.

The frontend provides the user interface for:

- Authentication
- Candidate profiles
- Recruiter functionality
- Job discovery
- Job details
- Applications
- Dashboards
- Skill management
- Language selection

The frontend is maintained in the separate `it-talent-frontend` repository and communicates with the backend through the REST API.

The frontend technology must support a maintainable, component-based application with static typing, client-side navigation, and a consistent development workflow.

## Decision

The frontend uses the following technologies:

- **React** for the user interface.
- **React DOM** for rendering the React application in the browser.
- **Vite** for development and production builds.
- **TypeScript** for static typing.
- **React Router DOM** for client-side routing.
- **Oxlint** for JavaScript and TypeScript linting.

The application is implemented as a client-side React application.

The frontend also uses dedicated libraries for internationalization, rich-text editing, and HTML sanitization where required by application functionality.

## Rationale

### React

React provides the component-based architecture used for:

- Reusable UI components.
- Pages and application views.
- Forms.
- Dashboards.
- Job discovery.
- Candidate and recruiter functionality.
- Application management.

Its component model supports the development and maintenance of separate application features.

### Vite

Vite is used as the frontend development server and build tool.

It provides the development and production build environment for the React application.

### TypeScript

TypeScript is used throughout the frontend codebase.

It provides static typing for:

- Components and properties.
- Application logic.
- API communication.
- Configuration.
- Domain types.

This helps detect type-related issues during development and improves maintainability.

### React Router DOM

React Router DOM is used for client-side navigation.

It supports navigation between application pages without requiring a full browser reload for every route change.

### Oxlint

Oxlint is used for JavaScript and TypeScript linting.

It provides static analysis as part of the frontend development workflow and helps maintain consistent code quality.

## Frontend Architecture

The frontend follows a layered, feature-oriented structure.

The main architectural responsibilities include:

- Application entry point and providers.
- Shared UI components.
- Feature-specific functionality.
- Page-level views.
- Services for communication with the backend.
- Type definitions.
- Configuration.
- Internationalization.

The application entry point initializes the internationalization and authentication providers before rendering the router.

Conceptually, the frontend architecture can be represented as follows:

```text
React + Vite + TypeScript
          │
          ▼
    Application Entry
          │
          ├── I18nProvider
          ├── AuthProvider
          │
          ▼
    React Router
          │
          ├── Pages
          ├── Features
          ├── Components
          ├── Services
          ├── Types
          └── Configuration
                  │
                  ▼
           REST API
                  │
                  ▼
          NestJS Backend
```

The frontend is responsible for presentation, user interaction, and client-side application behavior.

The backend remains responsible for server-side authentication, authorization, request validation, business logic, and database access.

Client-side checks must not replace backend authorization or validation.

## Internationalization

The frontend supports the following languages:

- English (`en`).
- Dutch (`nl`).

English is the default language.

The internationalization layer provides translated interface text and supports fallback behavior when a translation is unavailable.

## API Configuration

The frontend communicates with the backend through a configurable API base URL.

The API base URL is configured through the Vite environment variable:

```env
VITE_API_BASE_URL=http://localhost:3000/api/v1
```

This allows the frontend to use different backend environments without changing application source code.

## Consequences

### Positive

- Clear component-based frontend architecture.
- Static typing throughout the application.
- Modern development and production build workflow.
- Client-side navigation between application pages.
- Clear separation between frontend presentation and backend responsibilities.
- Frontend functionality can evolve independently from backend implementation, provided the API contract remains compatible.
- Multilingual interface support is integrated into the frontend architecture.
- Linting supports consistent code quality.

### Negative

- Frontend and backend changes may need to be coordinated when the API contract changes.
- Client-side routing requires appropriate server configuration when deploying the application.
- Frontend dependencies and framework versions require regular maintenance.
- Client-side validation and access checks cannot replace server-side security controls.

## Result

The IT Talent Platform frontend is implemented as a client-side React application using React DOM, Vite, TypeScript, and React Router DOM.

Oxlint provides static analysis, while the application uses dedicated providers for authentication and internationalization.

The frontend communicates with the NestJS backend through the versioned REST API and supports English and Dutch.