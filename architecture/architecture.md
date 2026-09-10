# IT Talent Platform — System Architecture

**Document:** `architecture.md`
**Version:** `0.2.0`
**Status:** Architecture Baseline
**Last updated:** 2026-09-10

---

# 1. Purpose

This document describes the technical architecture of the current IT Talent Platform.

The platform consists of:

* a React/Vite frontend;
* a NestJS backend;
* a PostgreSQL database;
* Prisma as the database ORM;
* a REST API;
* role-based access;
* a shared multilingual interface.

The architecture supports the current Candidate, Recruiter, and Admin experiences.

---

# 2. Architecture Overview

The current platform follows a frontend/backend architecture.

```text
User
  │
  ▼
React + Vite
Frontend
  │
  │ REST API / HTTP
  ▼
NestJS
Backend
  │
  ▼
Prisma
  │
  ▼
PostgreSQL
```

The frontend is responsible for presentation and user interaction.

The backend is responsible for business logic, authentication, authorization, validation, and database access.

The frontend does not access PostgreSQL directly.

---

# 3. Technology Stack

## 3.1 Frontend

The frontend uses:

* React;
* Vite;
* TypeScript;
* Oxlint.

The frontend contains the application pages, components, features, services, configuration, types, and internationalization.

## 3.2 Backend

The backend uses:

* NestJS;
* TypeScript;
* Prisma;
* PostgreSQL;
* REST API.

The backend is structured as a modular application.

## 3.3 Database

PostgreSQL is the primary application database.

Prisma provides database access and schema management for the backend.

---

# 4. Repository Architecture

The project is divided into three repositories:

```text
GitHub
│
├── it-talent-frontend
├── it-talent-backend
└── it-talent-docs
```

Each repository has a separate responsibility.

### `it-talent-frontend`

Contains the React/Vite application.

### `it-talent-backend`

Contains the NestJS API and backend business logic.

### `it-talent-docs`

Contains product, architecture, API, security, and decision documentation.

---

# 5. Frontend Architecture

The frontend is organized around reusable application components and product features.

Current structure:

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

## 5.1 App

The `app` area contains application-level functionality.

## 5.2 Components

Reusable interface components are organized in the components area.

## 5.3 Features

Feature-specific functionality is grouped within the features area.

Examples include functionality related to:

* authentication;
* candidates;
* recruiters;
* jobs;
* applications;
* administration.

## 5.4 Pages

Pages represent the main application views.

The application provides role-specific pages for candidates, recruiters, and administrators.

## 5.5 Services

Services provide communication between the frontend and backend API.

## 5.6 Types

Shared frontend TypeScript types define the structures used by the application.

---

# 6. Frontend Responsibilities

The frontend is responsible for:

* displaying application interfaces;
* navigation;
* forms;
* user interaction;
* search;
* filtering;
* sorting;
* pagination;
* displaying job information;
* displaying application information;
* displaying dashboards;
* language selection;
* communicating with the backend API.

Business-critical rules remain on the backend.

---

# 7. Internationalization

The frontend contains a shared internationalization system.

Current languages are:

* English;
* Dutch.

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
EN     NL
```

User-facing functionality should use the translation system rather than hard-coded interface text where applicable.

---

# 8. Backend Architecture

The backend is implemented with NestJS and follows a modular application structure.

Current backend areas include:

```text
src/
│
├── auth/
├── users/
├── skills/
└── common/
```

The backend provides the API used by the frontend.

---

# 9. Backend Responsibilities

The backend is responsible for:

* authentication;
* authorization;
* user management;
* candidate profile functionality;
* skills;
* validation;
* database operations;
* API responses;
* business rules;
* health checks.

The backend is the authoritative layer for application business logic.

---

# 10. Authentication Architecture

Authentication is handled by the backend.

The current authentication flow is:

```text
User
  │
  ▼
Register / Login
  │
  ▼
NestJS Auth
  │
  ▼
JWT Authentication
  │
  ▼
Authenticated API Requests
```

The backend uses password hashing for stored passwords.

Authenticated requests are protected through backend authentication mechanisms.

---

# 11. Authorization

The platform uses role-based authorization.

Current roles are:

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

Frontend navigation reflects the user's role, but frontend restrictions are not considered a security boundary.

---

# 12. User Architecture

Users are represented by the backend user model.

A user contains authentication and role information.

The user model supports the platform's main roles:

* Candidate;
* Recruiter;
* Admin.

User-related operations are exposed through protected backend functionality.

---

# 13. Candidate Architecture

Candidate functionality is connected to the authenticated user/profile flow.

The candidate experience includes:

* professional profile;
* skills;
* job discovery;
* matching information;
* applications.

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

The candidate profile is used throughout the candidate experience.

---

# 14. Skills Architecture

Skills are a central domain concept within IT Talent.

The platform maintains structured skill information.

```text
Candidate
    │
    ▼
Candidate Skills
    │
    ▼
Skill
```

Jobs also use structured skills:

```text
Job
 │
 ├── Required Skills
 └── Preferred Skills
```

Skills are managed through the backend and administration functionality.

---

# 15. Job Architecture

Jobs represent IT vacancies managed by recruiters.

A job contains structured information such as:

* title;
* description;
* company;
* location;
* work mode;
* employment type;
* salary;
* required skills;
* preferred skills;
* status.

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

---

# 16. Company Architecture

Jobs are associated with a company context.

The company context connects recruiter activity with job information.

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

---

# 17. Matching Architecture

Matching connects candidate information with job information.

The matching concept uses structured data from both sides:

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

Matching information can be presented to candidates and recruiters through the application.

The matching functionality is part of the product domain and is handled by the backend.

---

# 18. Application Architecture

Applications connect candidates with jobs.

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

Application status is represented within the application.

Supported statuses include:

* pending;
* reviewing;
* accepted;
* rejected;
* withdrawn.

Application data is managed by the backend.

---

# 19. Dashboard Architecture

The frontend provides role-specific dashboards.

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

Provides information such as:

* profile completion;
* applications;
* available jobs;
* recommended jobs;
* skills;
* recent applications;
* recent jobs.

## Recruiter Dashboard

Provides information such as:

* company information;
* jobs by status;
* applications;
* recent candidates;
* recent jobs.

## Admin Dashboard

Provides access to administrative functionality.

---

# 20. Job Discovery Architecture

Job discovery is provided through the frontend and backend API.

The flow is:

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

The frontend sends search and filter parameters to the backend.

---

# 21. API Architecture

The frontend communicates with the backend through a REST API.

The API uses versioning.

Current API structure:

```text
/api/v1
```

Conceptually:

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

The frontend does not directly access database tables.

---

# 22. Current API Areas

The current backend provides API functionality for areas including:

### Authentication

* registration;
* login.

### Users

* current authenticated user;
* user-related functionality.

### Candidate Profile

* candidate profile foundation.

### Skills

* list skills;
* search/filter skills;
* retrieve skills;
* create skills;
* update skills;
* delete skills.

The API continues to evolve as platform functionality is implemented.

---

# 23. Database Architecture

PostgreSQL is used as the relational database.

Prisma is used by the NestJS backend to access the database.

The database contains structured application data used by the platform.

Conceptually:

```text
NestJS
   │
   ▼
Prisma
   │
   ▼
PostgreSQL
```

The detailed database model is documented separately in:

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

Business logic is executed on the backend.

---

# 25. Validation

Input validation is handled by the backend.

The frontend can provide immediate form validation for user experience, but backend validation remains authoritative.

The backend validates incoming API data before processing it.

---

# 26. Security Architecture

Security is applied across the application layers.

```text
Frontend
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

* secure password hashing;
* authenticated access;
* role-based authorization;
* server-side validation;
* protected API endpoints;
* controlled data access;
* secure handling of secrets.

Detailed security requirements are documented in:

`architecture/security.md`

---

# 27. Backend Module Structure

The backend uses a modular structure within one NestJS application.

Current structure:

```text
NestJS Application
│
├── Auth
├── Users
├── Skills
└── Common
```

The structure keeps related functionality separated while allowing the application to operate as one backend.

---

# 28. Frontend-to-Backend Separation

The application maintains a clear separation between presentation and business logic.

```text
Frontend
│
├── UI
├── Navigation
├── Forms
├── State
└── API Services
        │
        ▼
Backend
│
├── Authentication
├── Authorization
├── Validation
├── Business Logic
└── Database Access
```

This separation allows the frontend and backend to evolve independently.

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

Vercel is suitable for the frontend deployment.

The exact production infrastructure is configured separately from the application architecture.

---

# 30. Environment Configuration

Environment-specific configuration must not be committed to source control.

The frontend uses environment configuration for the backend API endpoint.

Example:

```text
VITE_API_URL=
```

The backend uses environment configuration for database and authentication settings.

Example:

```text
DATABASE_URL=
JWT_SECRET=
```

Actual secret values must remain outside the repository.

---

# 31. Development Structure

The project uses separate repositories for frontend, backend, and documentation.

Development follows the repository structure:

```text
Frontend
   │
   ▼
Backend API
   │
   ▼
Database
```

Changes to frontend functionality should use the backend API rather than direct database access.

---

# 32. Architecture Principles

## 32.1 Separation of Responsibilities

Frontend, backend, and database responsibilities remain clearly separated.

## 32.2 Backend-Owned Business Logic

Business-critical rules are handled by the backend.

## 32.3 API-Based Communication

Frontend and backend communicate through the REST API.

## 32.4 Role-Based Access

Platform functionality is organized around Candidate, Recruiter, and Admin roles.

## 32.5 Structured Data

Candidates, skills, jobs, companies, matches, and applications are represented as structured platform data.

## 32.6 Modular Design

Related functionality is organized into logical modules.

## 32.7 Multilingual Interface

User-facing functionality supports the platform's English and Dutch languages.

---

# 33. Current Architecture Structure

The current IT Talent architecture can be summarized as:

```text
                    IT Talent Platform
                           │
              ┌────────────┴────────────┐
              │                         │
           Frontend                  Backend
              │                         │
        React + Vite                 NestJS
        TypeScript                   TypeScript
        Oxlint                         │
              │                         │
              └──────── REST API ──────┘
                                        │
                                      Prisma
                                        │
                                        ▼
                                   PostgreSQL
```

The main product domains are:

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
Languages
```

---

# 34. Architecture Status

**Version:** 0.2.0
**Status:** Architecture Baseline
**Last updated:** 2026-09-10

This document describes the architecture of the current IT Talent Platform.

Detailed implementation information is maintained in:

* `architecture/database.md`
* `architecture/api.md`
* `architecture/security.md`

Product functionality is defined in:

* `product/vision.md`
* `product/requirements.md`
* `product/roadmap.md`
