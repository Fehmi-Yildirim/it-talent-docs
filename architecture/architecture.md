# IT Talent Platform — System Architecture

**Document:** `architecture.md`  
**Version:** `0.3.0`  
**Status:** Architecture Baseline  
**Last updated:** 2026-11-09

---

# 1. Purpose

This document describes the system architecture of the IT Talent Platform and establishes a technical overview of its frontend, backend, database, API, and application responsibilities.

The platform is built around the following technologies:

- React and Vite frontend
- TypeScript
- NestJS backend
- PostgreSQL database
- Prisma ORM
- REST API
- JWT-based authentication
- Role-based authorization
- English and Dutch interface support

The architecture supports the platform's Candidate, Recruiter, and Admin experiences.

This document describes the known architectural structure. Detailed implementation contracts and domain-specific information are maintained in the corresponding architecture and product documents.

---

# 2. Architecture Overview

The platform follows a frontend/backend architecture with a relational database.

```text
User
 │
 ▼
React + Vite
Frontend
 │
 │ HTTP / REST API
 ▼
NestJS
Backend
 │
 ▼
Prisma ORM
 │
 ▼
PostgreSQL
```

The main responsibilities are separated as follows:

- **Frontend:** presentation, navigation, user interaction, and API communication.
- **Backend:** authentication, authorization, validation, business logic, and data access.
- **Database:** persistent storage of application data.
- **Prisma:** database access and schema management within the backend.

The frontend does not access PostgreSQL directly. All application data access is handled through the backend API.

---

# 3. Technology Stack

## 3.1 Frontend

The frontend uses:

- React 19
- React DOM
- Vite 8
- TypeScript
- React Router
- Oxlint
- Prettier
- Vitest and Testing Library

The application also uses:

- TipTap for rich-text editing functionality
- DOMPurify for HTML sanitization
- An internationalization system for language-specific interface text

The frontend contains the application pages, reusable components, product features, services, configuration, types, and internationalization functionality.

## 3.2 Backend

The backend uses:

- NestJS
- TypeScript
- Prisma
- PostgreSQL
- REST API
- JWT-based authentication

The backend is structured as a modular NestJS application.

It uses environment configuration, request validation, and rate limiting for selected authentication operations.

## 3.3 Database

PostgreSQL is the primary application database.

Prisma provides database access and schema management for the NestJS backend.

The database model and relationships are documented separately in `architecture/database.md`.

---

# 4. Repository Architecture

The project is divided into three GitHub repositories:

```text
GitHub
│
├── it-talent-frontend
├── it-talent-backend
└── it-talent-docs
```

Each repository has a separate responsibility.

### `it-talent-frontend`

Contains the React/Vite application, including the user interface, frontend features, routing, API services, and internationalization.

### `it-talent-backend`

Contains the NestJS API, application modules, backend business logic, authentication, authorization, and database integration.

### `it-talent-docs`

Contains product documentation, architecture documentation, API and security documentation, and Architecture Decision Records.

---

# 5. Frontend Architecture

The frontend is organized around reusable application components and product features.

The main source structure is:

```text
src/
│
├── app/
├── components/
├── config/
├── features/
├── i18n/
├── pages/
├── services/
├── types/
├── index.css
└── main.tsx
```

## 5.1 Application Entry Point

The application starts through `main.tsx`.

The current provider setup includes:

```text
I18nProvider
     │
     ▼
AuthProvider
     │
     ▼
RouterProvider
```

These providers establish the shared internationalization, authentication, and routing context for the application.

## 5.2 App

The `app` area contains application-level functionality and configuration.

## 5.3 Components

Reusable interface components are organized in the components area.

## 5.4 Features

Feature-specific functionality is grouped within the features area.

Product areas include functionality related to authentication, candidates, recruiters, jobs, applications, and administration.

## 5.5 Pages

Pages represent the main application views.

The application provides role-specific experiences for candidates, recruiters, and administrators.

## 5.6 Services

Services provide communication between the frontend and backend API.

The frontend uses a configurable API base URL rather than accessing the database directly.

## 5.7 Types

Frontend TypeScript types define the data structures used by the application.

## 5.8 Testing and Code Quality

The frontend uses Vitest and Testing Library for testing.

Oxlint and Prettier support code quality and consistent formatting.

---

# 6. Frontend Responsibilities

The frontend is responsible for:

- Displaying application interfaces
- Navigation and routing
- Forms and user interaction
- Search and filtering interfaces
- Sorting and pagination interfaces
- Displaying candidate, recruiter, and job information
- Displaying dashboards
- Language selection
- Communicating with the backend API

Business-critical rules and authorization decisions remain the responsibility of the backend.

---

# 7. Internationalization

The frontend contains a shared internationalization system.

Current languages are:

- English
- Dutch

English is the default language.

The translation system provides language-specific interface text and English fallback behavior.

Conceptually:

```text
Application
    │
    ▼
Language
    │
 ┌──┴──┐
 ▼     ▼
 EN    NL
```

User-facing functionality should use the translation system rather than hard-coded interface text wherever applicable.

---

# 8. Backend Architecture

The backend is implemented with NestJS and follows a modular application structure.

The application module currently registers the following functional modules:

```text
src/
│
├── applications/
├── auth/
├── candidates/
├── companies/
├── dashboard/
├── database/
├── health/
├── jobs/
├── recruiters/
├── settings/
├── skills/
└── users/
```

The backend also includes shared functionality and infrastructure components.

The modular structure separates related domain responsibilities while keeping the application within one NestJS backend.

---

# 9. Backend Responsibilities

The backend is responsible for:

- Authentication
- Authorization
- User management
- Candidate functionality
- Recruiter functionality
- Company functionality
- Job functionality
- Application functionality
- Skills management
- Dashboard-related functionality
- Settings
- Request validation
- Database operations
- API responses
- Business rules
- Health checks

The backend is the authoritative layer for application business logic and access control.

The presence of a registered module does not necessarily mean that every planned feature within that domain is fully implemented.

---

# 10. Authentication Architecture

Authentication is handled by the backend.

The current authentication architecture uses JWT-based authentication.

Conceptually, the authentication flow is:

```text
User
 │
 ▼
Register / Login
 │
 ▼
NestJS Authentication Module
 │
 ▼
Credential Verification
 │
 ▼
JWT Authentication
 │
 ▼
Authenticated API Requests
```

The backend uses password hashing for stored passwords.

Authenticated API requests are handled through backend authentication mechanisms.

Authentication configuration is managed through environment variables.

The backend also applies rate limiting to selected login and registration operations.

---

# 11. Authorization

The platform uses role-based authorization.

The main application roles are:

```text
CANDIDATE
RECRUITER
ADMIN
```

Authorization is enforced by the backend.

Conceptually:

```text
User
 │
 ├── CANDIDATE
 │
 ├── RECRUITER
 │
 └── ADMIN
```

Frontend navigation and page access reflect the authenticated user's role.

However, frontend restrictions are not considered a security boundary. The backend remains responsible for enforcing authorization on protected operations.

---

# 12. User Architecture

Users are represented by the backend user model.

A user contains authentication and role-related information.

The user architecture supports the platform's main roles:

- Candidate
- Recruiter
- Admin

User-related operations are exposed through backend API functionality.

Detailed user and profile relationships are documented in `architecture/database.md`.

---

# 13. Candidate Architecture

Candidate functionality is connected to the authenticated user and candidate profile flow.

The candidate experience is designed around:

- Professional profile information
- Skills
- Job discovery
- Matching information
- Applications

Conceptually:

```text
User
 │
 ▼
Candidate Profile
 │
 ├── Professional Information
 ├── Skills
 ├── Preferences
 └── Applications
```

The candidate profile provides the foundation for candidate-facing functionality.

The exact fields and relationships are defined by the database model and documented separately.

---

# 14. Skills Architecture

Skills are a central domain concept within IT Talent.

The platform maintains structured skill information.

Candidate skills connect candidates with skills:

```text
Candidate
    │
    ▼
Candidate Skills
    │
    ▼
Skill
```

Jobs can also be associated with structured skill requirements:

```text
Job
 │
 ├── Required Skills
 └── Preferred Skills
```

Skills are managed through backend functionality and administration-related workflows.

---

# 15. Job Architecture

Jobs represent IT vacancies managed through the recruiter experience.

The job domain is designed to support structured information such as:

- Title
- Description
- Company
- Location
- Work mode
- Employment type
- Salary
- Required skills
- Preferred skills
- Status

Conceptually:

```text
Company
   │
   ▼
Job
   │
   ├── Job Information
   ├── Required Skills
   └── Preferred Skills
```

The actual fields, relationships, and supported operations are defined by the backend implementation.

---

# 16. Company Architecture

Jobs are associated with a company context.

The company domain connects recruiter activity with company and job information.

Conceptually:

```text
Recruiter
    │
    ▼
Company
    │
    ▼
Jobs
```

Company-related functionality is part of the recruiter experience.

The database documentation defines the detailed relationships.

---

# 17. Matching Architecture

Matching connects candidate information with job information.

The matching concept can use structured data from both sides:

```text
Candidate
 │
 ├── Skills
 ├── Experience
 ├── Location
 ├── Salary
 ├── Availability
 └── Preferences
       │
       ▼
    Matching
       │
       ▼
      Job
```

Matching information is part of the platform's product domain.

The exact matching algorithm, scoring model, and implementation status should be documented separately when confirmed by the backend implementation.

---

# 18. Application Architecture

Applications connect candidates with jobs.

Conceptually:

```text
Candidate
    │
    ▼
Job
    │
    ▼
Application
    │
    ├── Cover Letter
    ├── Date
    └── Status
```

Application data is managed by the backend.

The application domain supports status-based workflows. The exact supported status values and transition rules are defined by the current backend implementation and should be maintained in the API and database documentation.

---

# 19. Dashboard Architecture

The frontend provides role-specific dashboard experiences.

Conceptually:

```text
Authenticated User
       │
       ▼
      Role
       │
 ┌─────┼─────┐
 ▼     ▼     ▼
Candidate Recruiter Admin
Dashboard Dashboard Dashboard
```

## Candidate Dashboard

The candidate dashboard is designed to present candidate-related information, such as profile information, applications, and job discovery.

## Recruiter Dashboard

The recruiter dashboard is designed to present recruiter-related information, such as company and job information and application activity.

## Admin Dashboard

The admin dashboard provides access to administrative functionality.

The exact dashboard content depends on the available backend functionality and frontend implementation.

---

# 20. Job Discovery Architecture

Job discovery is provided through the frontend and backend API.

The conceptual flow is:

```text
Candidate
   │
   ▼
Job Discovery
   │
   ├── Search
   ├── Location
   ├── Work Mode
   ├── Employment Type
   ├── Salary
   ├── Skills
   ├── Sorting
   └── Pagination
   │
   ▼
Job Results
   │
   ▼
Job Details
```

The frontend sends supported search and filter parameters to the backend.

The backend is responsible for processing the request and returning the corresponding results.

---

# 21. API Architecture

The frontend communicates with the backend through a REST API.

The backend uses the `/api/v1` API prefix.

```text
React + Vite
      │
      │ HTTP
      ▼
NestJS REST API
      │
      ▼
Prisma
      │
      ▼
PostgreSQL
```

The API is the communication boundary between frontend and backend.

The frontend does not directly access database tables.

Detailed endpoint information is maintained in `architecture/api.md`.

---

# 22. Backend API Areas

The backend application currently registers modules for the following areas:

- Applications
- Authentication
- Candidates
- Companies
- Dashboard
- Health
- Jobs
- Recruiters
- Settings
- Skills
- Users

The database module provides the database integration layer.

The API exposes functionality through these modules as implementation progresses.

The exact endpoints, HTTP methods, request schemas, response schemas, and authorization requirements are documented separately in `architecture/api.md`.

---

# 23. Database Architecture

PostgreSQL is used as the relational database.

Prisma is used by the NestJS backend to access the database.

The database stores structured application data used by the platform.

```text
NestJS
   │
   ▼
Prisma
   │
   ▼
PostgreSQL
```

The detailed database model, entities, relationships, and constraints are documented in:

`architecture/database.md`

---

# 24. Data Flow

A typical application request follows this flow:

```text
User
 │
 ▼
Frontend
 │
 ▼
API Request
 │
 ▼
NestJS Controller
 │
 ▼
Backend Service
 │
 ▼
Prisma
 │
 ▼
PostgreSQL
 │
 ▼
Backend Response
 │
 ▼
Frontend
 │
 ▼
User
```

The backend validates and processes incoming requests.

Business logic and database operations are executed on the backend.

The frontend presents the resulting response to the user.

---

# 25. Validation

Input validation is handled by the backend.

The backend uses request validation to ensure incoming data conforms to the expected API contracts.

The current validation configuration supports:

- Whitelisting allowed properties
- Transforming incoming values where applicable
- Rejecting non-whitelisted properties

The frontend may also provide immediate form validation to improve the user experience.

Frontend validation does not replace backend validation.

---

# 26. Security Architecture

Security is applied across the application layers.

```text
Frontend
   │
   ▼
API Boundary
   │
   ▼
Authentication
   │
   ▼
Authorization
   │
   ▼
Validation
   │
   ▼
Business Logic
   │
   ▼
Database
```

Important security principles include:

- Secure password hashing
- JWT-based authentication
- Role-based authorization
- Server-side validation
- Protected API endpoints
- Controlled data access
- Rate limiting for selected authentication operations
- Explicit CORS configuration
- Secure handling of environment variables and secrets

The backend uses trusted CORS origins configured through environment variables.

Detailed security requirements and implementation information are documented in:

`architecture/security.md`

---

# 27. Backend Module Structure

The backend uses a modular structure within one NestJS application.

```text
NestJS Application
│
├── Applications
├── Auth
├── Candidates
├── Companies
├── Dashboard
├── Database
├── Health
├── Jobs
├── Recruiters
├── Settings
├── Skills
└── Users
```

The module structure keeps related functionality separated while allowing the application to operate as one backend.

Shared functionality is organized separately from the domain modules.

---

# 28. Frontend-to-Backend Separation

The application maintains a clear separation between presentation and business logic.

```text
Frontend
│
├── UI
├── Navigation
├── Forms
├── Authentication Context
├── Internationalization
└── API Services
        │
        ▼
Backend
│
├── Authentication
├── Authorization
├── Validation
├── Business Logic
├── Domain Modules
└── Database Access
        │
        ▼
    PostgreSQL
```

This separation allows the frontend and backend to evolve independently while communicating through a defined API contract.

---

# 29. Deployment Architecture

The application is designed to run as separate frontend and backend applications.

Conceptually:

```text
GitHub
  │
  ├───────────────┐
  ▼               ▼
Frontend         Backend
  │               │
  ▼               ▼
Deployment       Deployment
  │               │
  └───────┬───────┘
          ▼
      PostgreSQL
```

Vercel is a suitable deployment option for the frontend.

The exact production hosting provider, backend infrastructure, database hosting, networking, and deployment pipeline are configured separately from the application architecture.

No specific production infrastructure should be assumed unless it is documented in the deployment configuration or a corresponding Architecture Decision Record.

---

# 30. Environment Configuration

Environment-specific configuration must not be committed to source control.

The frontend uses environment configuration for the backend API endpoint.

Example:

```text
VITE_API_BASE_URL=http://localhost:3000/api/v1
```

The backend uses environment configuration for application and infrastructure settings, including:

```text
NODE_ENV=
PORT=
DATABASE_URL=
JWT_SECRET=
CORS_ORIGIN=
```

Additional backend configuration includes authentication rate limits and CV upload settings.

Actual secret values must remain outside the repository.

Example environment files should contain placeholders or safe development defaults only.

---

# 31. Development Structure

The project uses separate repositories for frontend, backend, and documentation.

The local development architecture follows this structure:

```text
Frontend Application
        │
        ▼
Backend API
        │
        ▼
PostgreSQL Database
```

The frontend communicates with the backend API.

The backend communicates with PostgreSQL through Prisma.

Changes to frontend functionality must use the backend API rather than direct database access.

---

# 32. Architecture Principles

## 32.1 Separation of Responsibilities

Frontend, backend, and database responsibilities remain clearly separated.

## 32.2 Backend-Owned Business Logic

Business-critical rules and authorization decisions are handled by the backend.

## 32.3 API-Based Communication

Frontend and backend communicate through the REST API.

## 32.4 Role-Based Access

Platform functionality is organized around Candidate, Recruiter, and Admin roles.

## 32.5 Structured Data

Candidates, skills, jobs, companies, matches, and applications are represented as structured platform data.

## 32.6 Modular Design

Related functionality is organized into logical NestJS modules.

## 32.7 Multilingual Interface

User-facing functionality supports the platform's English and Dutch languages.

## 32.8 Configuration Outside Source Code

Environment-specific configuration and secrets are managed outside committed application source code.

---

# 33. Current Architecture Summary

The IT Talent Platform architecture can be summarized as follows:

```text
                 IT Talent Platform
                         │
            ┌────────────┴────────────┐
            │                         │
         Frontend                  Backend
            │                         │
       React + Vite                 NestJS
       TypeScript                   TypeScript
       React Router                 Domain Modules
       Oxlint                       JWT Authentication
       Vitest                       Validation
            │                         │
            └──────── REST API ───────┘
                                      │
                                    Prisma
                                      │
                                      ▼
                                 PostgreSQL
```

The main product domains include:

```text
Authentication
Users
Candidates
Recruiters
Companies
Skills
Jobs
Matching
Applications
Dashboards
Administration
Internationalization
```

These domains describe the platform's functional scope. Their presence in this overview does not imply that every planned capability is fully implemented.

---

# 34. Architecture Status

**Version:** 0.3.0  
**Status:** Architecture Baseline  
**Last updated:** 2026-11-09

This document describes the known architecture of the IT Talent Platform.

Detailed implementation information is maintained in:

- `architecture/database.md`
- `architecture/api.md`
- `architecture/security.md`

Product functionality is defined in:

- `product/vision.md`
- `product/requirements.md`
- `product/roadmap.md`

Architecture documentation must remain consistent with the current frontend and backend implementations.

When implementation changes affect system structure, API behavior, security, or data relationships, the relevant documentation should be reviewed and updated accordingly.