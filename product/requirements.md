# IT Talent Platform — Product Requirements

**Document:** `requirements.md`  
**Version:** `0.3.0`  
**Status:** Product Requirements  
**Last updated:** 2026-11-09

---

# 1. Purpose

This document defines the functional and product requirements of the IT Talent Platform.

The platform aims to connect IT candidates and recruiters through a structured recruitment experience based on:

- professional candidate profiles;
- skills and experience;
- job listings;
- candidate and job information;
- matching information;
- job applications.

These requirements describe the expected product functionality and user experience.

A requirement in this document does not automatically mean that the functionality is already implemented. Implementation status must be verified against the frontend, backend, API, and database documentation.

---

# 2. Scope

The platform is organized into the following functional areas:

```text
IT Talent Platform
│
├── Authentication
├── Candidate
│   ├── Profile
│   ├── Skills
│   ├── Job Discovery
│   └── Applications
├── Recruiter
│   ├── Profile
│   ├── Company Context
│   ├── Jobs
│   ├── Candidates
│   └── Applications
├── Matching
├── Skills
├── Dashboard
├── Administration
└── Internationalization
```

The platform has three primary user roles:

- `CANDIDATE`
- `RECRUITER`
- `ADMIN`

The user interface supports English and Dutch.

## 2.1 Requirement Classification

Requirements are classified as follows:

| Priority | Meaning |
|---|---|
| Must | Essential functionality for the core product |
| Should | Important functionality that improves the product |
| Could | Optional functionality or a future enhancement |

Implementation status must be tracked separately from priority.

---

# 3. User Roles

## ROLE-001 — Candidate

Candidates use the platform to:

- manage their professional profile;
- maintain their skills and experience;
- discover relevant jobs;
- submit applications;
- review application information and status.

## ROLE-002 — Recruiter

Recruiters use the platform to:

- manage recruiter-related information;
- work within a company context;
- create and manage jobs;
- review candidate-related information;
- review applications associated with their jobs.

## ROLE-003 — Administrator

Administrators have access to platform administration functionality, subject to the permissions implemented by the backend.

Administrative functionality may include:

- user management;
- skill management;
- platform configuration.

Role assignment and permission enforcement must be controlled by the backend.

---

# 4. Authentication Requirements

## AUTH-001 — User Registration

**Priority:** Must

Users must be able to create an account using the registration functionality.

Registration must collect the information required by the application, including:

- email address;
- password;
- permitted account role.

The backend must validate registration data.

Public registration must not allow users to assign themselves the `ADMIN` role.

## AUTH-002 — User Login

**Priority:** Must

Users must be able to authenticate using their account credentials.

Invalid credentials must result in an appropriate error response without exposing sensitive authentication information.

## AUTH-003 — Logout

**Priority:** Must

Authenticated users must be able to end their authenticated session through the application's logout functionality.

The frontend and backend must handle the authentication state consistently.

## AUTH-004 — Role-Based Access

**Priority:** Must

The platform must restrict functionality according to the authenticated user's permissions.

Candidates, recruiters, and administrators must only access functionality permitted for their role.

Authorization must be enforced by the backend and must not rely exclusively on frontend navigation or visibility.

## AUTH-005 — Authentication State

**Priority:** Must

The application must handle authenticated and unauthenticated states consistently.

Protected functionality must not be accessible without valid authentication.

---

# 5. Candidate Requirements

## CAND-001 — Candidate Profile

**Priority:** Must

Candidates must be able to manage their professional profile.

Profile information may include:

- professional headline;
- professional summary;
- location;
- salary expectation;
- salary currency;
- availability;
- remote-work preference.

The backend must validate and persist supported profile information.

## CAND-002 — Candidate Skills

**Priority:** Must

Candidates must be able to manage their skills.

Skill information may include:

- skill;
- proficiency level;
- years of experience;
- skill source or verification information, where supported.

Candidate skill management should support:

- adding skills;
- updating skill information;
- removing skills.

The available fields and allowed values must follow the backend data model.

## CAND-003 — Candidate Dashboard

**Priority:** Should

Candidates should have access to a personal dashboard.

Depending on implemented functionality, the dashboard may provide:

- profile completion information;
- application information;
- available jobs;
- recommended jobs;
- candidate skills;
- recent applications;
- recently published jobs.

Dashboard information must be based on data the authenticated candidate is authorized to access.

---

# 6. Job Discovery Requirements

## JOBDISC-001 — Job Listing

**Priority:** Must

Candidates must be able to browse available jobs.

A job listing should provide relevant information such as:

- job title;
- location;
- work mode;
- employment type;
- salary information, where available;
- company information.

Only jobs that are available for candidate discovery should be presented as active opportunities.

## JOBDISC-002 — Job Search

**Priority:** Should

Candidates should be able to search for jobs using relevant information such as:

- job title;
- keywords;
- location;
- skills.

Search behavior and supported parameters must be consistent with the backend API.

## JOBDISC-003 — Job Filtering

**Priority:** Should

Candidates should be able to filter jobs using supported criteria, such as:

- location;
- work mode;
- employment type;
- skills;
- minimum salary;
- maximum salary.

Potential work modes include:

- remote;
- hybrid;
- onsite;
- flexible.

The available filter options must reflect the supported backend values.

## JOBDISC-004 — Job Sorting

**Priority:** Should

Candidates should be able to sort available jobs using supported sorting options.

The selected sorting option must be applied consistently to the displayed results.

## JOBDISC-005 — Job Pagination

**Priority:** Should

Job listings should support pagination or another suitable result-loading mechanism when multiple jobs are available.

Pagination parameters and response metadata must follow the API contract.

## JOBDISC-006 — Job Details

**Priority:** Must

Candidates must be able to view detailed information about an individual job.

Job details may include:

- title;
- description;
- company information;
- location;
- work mode;
- employment type;
- salary information;
- required skills;
- preferred skills.

Unavailable or optional information must be presented appropriately.

---

# 7. Application Requirements

## APP-001 — Apply for Job

**Priority:** Must

Candidates must be able to submit an application for an eligible job.

An application is associated with:

- a candidate;
- a job;
- an application date;
- an application status.

A cover letter may be included where supported.

The backend must validate the application and enforce the relevant business rules.

## APP-002 — Candidate Application List

**Priority:** Must

Candidates must be able to view their own applications.

Candidates must not be able to access another candidate's applications without appropriate authorization.

## APP-003 — Application Details

**Priority:** Should

Candidates should be able to view details of an individual application they are authorized to access.

## APP-004 — Application Status

**Priority:** Must

The application data model supports the following statuses:

- `PENDING`
- `REVIEWING`
- `ACCEPTED`
- `REJECTED`
- `WITHDRAWN`

The frontend must display status information consistently with the backend values.

## APP-005 — Withdraw Application

**Priority:** Should

Candidates should be able to withdraw their own applications when the application state and business rules permit withdrawal.

The backend must validate whether withdrawal is allowed.

## APP-006 — Recruiter Application Overview

**Priority:** Must

Recruiters must be able to review applications associated with jobs they are authorized to manage.

Recruiters must not be able to access applications belonging to unrelated companies or jobs.

---

# 8. Recruiter Requirements

## REC-001 — Recruiter Profile

**Priority:** Must

Recruiters must be able to manage supported recruiter profile information.

The recruiter experience may include company-related information.

## REC-002 — Company Context

**Priority:** Must

Recruiter jobs must be associated with the appropriate company context.

The backend must enforce the relationship between recruiters, companies, and jobs.

## REC-003 — Job Creation

**Priority:** Must

Recruiters must be able to create jobs when authorized.

Job information may include:

- title;
- description;
- location;
- work mode;
- employment type;
- salary information;
- required skills;
- preferred skills.

The backend must validate submitted job information.

## REC-004 — Job Management

**Priority:** Must

Recruiters must be able to manage jobs they are authorized to access.

Supported actions may include:

- creating jobs;
- editing jobs;
- publishing jobs;
- closing jobs.

The backend must enforce the permitted job lifecycle transitions.

## REC-005 — Candidate Overview

**Priority:** Should

Recruiters should be able to view candidate-related information through authorized recruiter functionality.

Candidate information must be limited to data permitted by the platform's privacy and authorization rules.

## REC-006 — Application Review

**Priority:** Must

Recruiters must be able to review applications associated with their jobs.

Where supported, recruiters should be able to update application status according to permitted business rules.

---

# 9. Recruiter Dashboard

## REC-DASH-001 — Recruiter Dashboard

**Priority:** Should

Recruiters should have access to a dashboard containing relevant recruitment information.

Depending on implemented functionality, this may include:

- company information;
- jobs grouped by status;
- application information;
- recent candidates;
- recent jobs.

Dashboard data must respect recruiter and company authorization boundaries.

---

# 10. Job Requirements

## JOB-001 — Structured Job Information

**Priority:** Must

A job must support structured information appropriate to the recruitment process, including:

- title;
- description;
- company;
- location;
- work mode;
- employment type;
- salary information, where applicable.

## JOB-002 — Required Skills

**Priority:** Must

Jobs must support required skills where skill requirements are part of the job definition.

## JOB-003 — Preferred Skills

**Priority:** Should

Jobs should support preferred skills separately from required skills where supported.

## JOB-004 — Job Status

**Priority:** Must

Jobs must have a status used for job management and presentation.

Recruiters must only be able to perform lifecycle actions permitted by their authorization and the job's current state.

The exact statuses and transitions must follow the backend implementation.

---

# 11. Skill Requirements

## SKILL-001 — Skill Catalog

**Priority:** Must

The platform must maintain a structured skill catalog.

Examples include:

- React;
- TypeScript;
- Node.js;
- Python;
- Java;
- AWS;
- Azure;
- Kubernetes;
- PostgreSQL;
- Docker.

The catalog should support consistent skill selection across candidate profiles and job requirements.

## SKILL-002 — Skill Search

**Priority:** Should

Users should be able to search available skills when selecting skills for profiles or jobs.

## SKILL-003 — Skill Management

**Priority:** Should

Authorized administrators should be able to manage skills through the administration functionality.

Skill management must preserve data consistency with existing candidate and job skill relationships.

---

# 12. Matching Requirements

## MATCH-001 — Candidate and Job Matching

**Priority:** Should

The platform aims to provide matching information between candidates and jobs.

Potential matching inputs include:

- skills;
- experience;
- location;
- salary expectations;
- availability;
- preferences.

The exact matching inputs depend on the implemented matching logic.

## MATCH-002 — Skill Matching

**Priority:** Should

Candidate skills should be comparable with job skill requirements.

The matching process should distinguish required and preferred skills where supported.

## MATCH-003 — Match Score

**Priority:** Should

Where matching scores are provided, compatibility may be represented on a scale from 0 to 100.

The score calculation and interpretation must be documented separately when the matching algorithm is implemented.

## MATCH-004 — Match Information

**Priority:** Could

The platform may present information explaining the relationship between candidate information and job requirements.

Such explanations must be based on actual matching data and must not imply unsupported certainty.

## MATCH-005 — Match Ranking

**Priority:** Could

The platform may rank relevant candidates or jobs according to matching relevance.

Ranking behavior must be consistent, explainable where appropriate, and based on defined matching criteria.

**Implementation note:** The presence of matching-related database models does not by itself establish that a complete matching API or user-facing matching workflow exists.

---

# 13. Administration Requirements

## ADMIN-001 — Administrative Access

**Priority:** Must

The platform must support an administrative role.

Administrative access must be granted only through trusted authorization processes.

## ADMIN-002 — User Management

**Priority:** Should

Authorized administrators should be able to manage platform users through the administration functionality.

User management may include viewing user information and managing permitted account properties or roles.

Role changes must be protected by backend authorization rules.

## ADMIN-003 — Skill Management

**Priority:** Should

Authorized administrators should be able to manage the platform skill catalog.

---

# 14. Dashboard Requirements

## DASH-001 — Candidate Dashboard

**Priority:** Should

The candidate dashboard should provide relevant personal information, such as:

- profile completion;
- applications;
- available jobs;
- recommended jobs, where supported;
- skills;
- recent applications;
- recent jobs.

## DASH-002 — Recruiter Dashboard

**Priority:** Should

The recruiter dashboard should provide relevant recruitment information, such as:

- company information;
- jobs by status;
- applications;
- recent candidates;
- recent jobs.

## DASH-003 — Admin Dashboard

**Priority:** Could

The administration experience may provide access to platform management functionality, including:

- user management;
- skill management;
- relevant administrative information.

---

# 15. Multilingual Requirements

## LANG-001 — Supported Languages

**Priority:** Must

The application interface supports:

- English;
- Dutch.

## LANG-002 — Default Language

**Priority:** Must

English is the default application language unless the application configuration specifies otherwise.

## LANG-003 — Language Selection

**Priority:** Should

Users should be able to select the application language through the language selection functionality.

## LANG-004 — Translation System

**Priority:** Must

User-interface text must use the shared translation system where applicable.

The translation system must support language-specific interface content and English fallback behavior.

## LANG-005 — Translation Consistency

**Priority:** Should

New user-facing functionality should provide translations for all supported languages.

Missing translations should be identified during development and testing.

---

# 16. User Interface Requirements

## UI-001 — Role-Based Navigation

**Priority:** Must

Application navigation must reflect the authenticated user's role and permitted functionality.

Hiding navigation items must not replace backend authorization.

## UI-002 — Responsive Interface

**Priority:** Must

The interface must support common screen sizes, including:

- desktop;
- tablet;
- mobile.

Core functionality must remain usable across supported screen sizes.

## UI-003 — Consistent Interaction

**Priority:** Should

Common interactions should be presented consistently throughout the application, including:

- searching;
- filtering;
- sorting;
- viewing details;
- editing;
- submitting;
- withdrawing;
- publishing.

## UI-004 — User Feedback

**Priority:** Must

The interface must provide appropriate feedback for relevant user actions.

This includes:

- loading states;
- successful operations;
- validation errors;
- API errors;
- empty result states.

---

# 17. Security and Privacy Requirements

## SEC-001 — Authentication Protection

**Priority:** Must

Protected functionality must require valid authentication.

## SEC-002 — Authorization

**Priority:** Must

Users must only access functionality and data permitted by their role and ownership relationships.

Authorization must be enforced by the backend.

## SEC-003 — User Data Protection

**Priority:** Must

Candidate, recruiter, company, job, and application information must be protected according to the user's authorization level.

## SEC-004 — Password Protection

**Priority:** Must

Passwords must never be stored or exposed as plain text.

Password storage must use an appropriate password-hashing mechanism.

## SEC-005 — Input Validation

**Priority:** Must

The backend must validate incoming data and reject invalid or unauthorized requests.

## SEC-006 — Administrative Role Protection

**Priority:** Must

Public registration and ordinary user operations must not allow unauthorized assignment or escalation to the `ADMIN` role.

## SEC-007 — Ownership Protection

**Priority:** Must

Users must not be able to access or modify resources belonging to other users or companies unless explicitly authorized.

## SEC-008 — Sensitive Information

**Priority:** Must

Sensitive information, credentials, and authentication tokens must not be unnecessarily exposed through API responses, logs, or frontend interfaces.

---

# 18. API Requirements

## API-001 — Backend API Communication

**Priority:** Must

The frontend must communicate with the backend through defined API services.

The frontend API base URL must be configurable through the supported environment configuration.

## API-002 — Structured Data

**Priority:** Must

API responses must provide structured data required by supported frontend functionality.

Request and response formats must follow the documented API contract.

## API-003 — Error Handling

**Priority:** Must

API errors must be handled consistently so the frontend can provide appropriate user feedback.

## API-004 — Validation

**Priority:** Must

The backend must validate request data and return appropriate validation errors.

## API-005 — Authorization Enforcement

**Priority:** Must

The backend must enforce authentication, role permissions, and resource ownership for protected API operations.

**Implementation note:** The API documentation and backend controllers define the actual available endpoints. A product requirement does not establish that an endpoint already exists.

---

# 19. Core Functional Flows

The following flows describe the intended user journeys. Individual steps must be verified against the implemented frontend and backend functionality.

## 19.1 Candidate Flow

```text
Register
   ↓
Login
   ↓
Manage Profile
   ↓
Manage Skills
   ↓
Discover Jobs
   ↓
Search / Filter / Sort
   ↓
View Job Details
   ↓
Apply
   ↓
View Application
   ↓
Track Application Status
```

## 19.2 Recruiter Flow

```text
Register
   ↓
Login
   ↓
Manage Recruiter / Company Context
   ↓
Create Job
   ↓
Add Job Information
   ↓
Add Required / Preferred Skills
   ↓
Publish / Manage Job
   ↓
Review Candidates
   ↓
Review Applications
```

## 19.3 Administrator Flow

```text
Login
   ↓
Administrative Access
   ↓
Manage Users
   ↓
Manage Skills
```

The available administrative actions depend on the implemented authorization and administration functionality.

---

# 20. Core Product Scenario

A central product scenario connects candidate information with relevant IT job opportunities.

```text
Candidate
│
├── Profile
├── Skills
├── Experience
├── Location
├── Salary Expectations
└── Preferences
        │
        ▼
     Matching
        │
        ▼
       Job
        │
        ▼
    Application
```

The intended experience allows a candidate to discover a job, review its details, apply, and track the resulting application.

The recruiter can manage the job and review associated candidates and applications, subject to the relevant authorization rules.

Matching is a product capability whose actual availability and behavior must be verified against the implementation.

---

# 21. Non-Functional Product Requirements

## NFR-001 — Maintainability

**Priority:** Should

The application should maintain a clear separation between frontend, backend, API, and database responsibilities.

## NFR-002 — Reliability

**Priority:** Must

Core user operations should handle expected failures without leaving the application in an inconsistent state.

## NFR-003 — Performance

**Priority:** Should

Common user interactions and API operations should provide a responsive experience under normal operating conditions.

## NFR-004 — Accessibility

**Priority:** Should

The user interface should follow established accessibility practices, including keyboard usability, clear labels, and appropriate visual contrast.

## NFR-005 — Testability

**Priority:** Must

Core business functionality should be testable through appropriate automated tests.

Frontend and backend validation should cover important user flows, authorization boundaries, and error conditions.

---

# 22. Functional Product Summary

The IT Talent Platform is designed to support the following core capabilities:

- user registration and authentication;
- role-based access;
- candidate profiles;
- recruiter profiles;
- company context;
- candidate skill management;
- structured job information;
- job creation and management;
- job discovery and search;
- job filtering and sorting;
- job details;
- candidate and job matching information;
- job applications;
- application status tracking;
- candidate dashboards;
- recruiter dashboards;
- administration functionality;
- user and skill management;
- English and Dutch language support;
- responsive user interface.

The actual availability of each capability must be verified against the current application implementation.

---

# 23. Implementation and Verification

The requirements in this document describe product expectations. They should not be interpreted as proof that every feature is implemented.

For each requirement, implementation status should be tracked separately:

| Status | Meaning |
|---|---|
| Implemented | Verified in the current application |
| Partially implemented | Some expected behavior exists |
| Planned | Accepted for future implementation |
| Not implemented | Not currently available |
| Needs verification | Implementation has not yet been confirmed |

Verification should use the following sources:

- frontend routes, components, and services;
- backend controllers, services, and DTOs;
- database schema and migrations;
- automated tests;
- API documentation.

When implementation differs from a product requirement, update the relevant documentation or create a product decision before treating the requirement as completed.

---

# 24. Document Information

**Document:** `requirements.md`  
**Version:** `0.3.0`  
**Status:** Product Requirements  
**Last updated:** 2026-11-09

Related documentation:

- `../architecture/architecture.md`
- `../architecture/api.md`
- `../architecture/database.md`
- `../architecture/security.md`
- `vision.md`
- `roadmap.md`

This document defines product requirements and expected user experience. Technical implementation details are maintained in the corresponding architecture and API documentation.