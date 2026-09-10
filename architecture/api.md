# IT Talent Platform — API Specification

**Document:** api.md
**Version:** 0.3.0
**Status:** Current API Specification
**Last updated:** 2026-09-10

---

# 1. Purpose

This document defines the current REST API of the IT Talent Platform.

The API is the communication layer between the frontend and backend.

```text
it-talent-frontend
        │
        │ HTTPS / JSON
        ▼
it-talent-backend
        │
        └── PostgreSQL / Prisma
```

The frontend does not access PostgreSQL directly.

The backend is responsible for:

* authentication;
* authorization;
* validation;
* business logic;
* database access;
* job management;
* candidate profiles;
* company and recruiter data;
* skills;
* job requirements;
* applications;
* dashboards.

The actual NestJS controllers, DTOs, guards, services and tests are the implementation source of truth.

---

# 2. API Base Path

The backend uses the global prefix:

```text
/api/v1
```

Examples:

```text
POST /api/v1/auth/login
GET  /api/v1/jobs
GET  /api/v1/skills
```

The global prefix is configured in the NestJS application bootstrap.

---

# 3. Local API

The backend listens on the configured application port.

The default configuration is:

```text
http://localhost:3000
```

Therefore the local API base path is:

```text
http://localhost:3000/api/v1
```

The port can be changed through the backend environment configuration.

---

# 4. API Format

The API uses JSON for normal requests and responses.

```text
Content-Type: application/json
```

The API uses UUID identifiers for domain resources where defined by the Prisma schema.

---

# 5. Authentication

Authentication uses JWT access tokens.

Login returns an access token which is used for protected API requests.

Protected requests use:

```text
Authorization: Bearer <accessToken>
```

The backend validates the token before allowing access to protected endpoints.

Authentication is implemented through:

* JWT authentication guard;
* JWT strategy;
* bearer authentication;
* role guards where required.

---

# 6. Roles

The platform supports three user roles:

```text
CANDIDATE
RECRUITER
ADMIN
```

Role-based authorization is enforced by the backend.

The frontend may adapt its navigation and UI according to the authenticated role, but backend authorization remains authoritative.

---

# 7. Request Validation

Incoming requests are validated with NestJS `ValidationPipe`.

The current configuration enables:

```text
whitelist
transform
forbidNonWhitelisted
```

This means:

* DTO validation is applied to incoming data;
* known values can be transformed to the expected types;
* unsupported properties are rejected.

Validation rules are defined by the individual DTOs.

---

# 8. Authentication API

## Register

```text
POST /api/v1/auth/register
```

Creates a user account.

Request:

```json
{
  "email": "user@example.com",
  "password": "secure-password",
  "role": "CANDIDATE"
}
```

Validation:

* `email` must be a valid email address;
* `password` must contain at least 8 characters;
* `role` must be a valid `UserRole`.

Supported roles:

```text
CANDIDATE
RECRUITER
ADMIN
```

---

## Login

```text
POST /api/v1/auth/login
```

Request:

```json
{
  "email": "user@example.com",
  "password": "secure-password"
}
```

The login endpoint validates the credentials and returns the authenticated access token.

The current implementation uses JWT bearer authentication.

---

# 9. Users API

The Users API manages platform user accounts.

## Current User

```text
GET /api/v1/users/me
```

Authentication required.

Returns the authenticated user's account information.

Example:

```json
{
  "id": "uuid",
  "email": "user@example.com",
  "role": "CANDIDATE"
}
```

---

## Create User

```text
POST /api/v1/users
```

Authentication required.

Admin access is required.

Creates a user account through the Users domain.

---

## List Users

```text
GET /api/v1/users
```

Authentication required.

Admin access is required.

Returns the available user accounts.

---

## Get User

```text
GET /api/v1/users/:id
```

Authentication required.

Returns a user according to the authorization rules implemented by the backend.

---

## Update User

```text
PATCH /api/v1/users/:id
```

Authentication required.

Updates an authorized user account.

---

## Delete User

```text
DELETE /api/v1/users/:id
```

Authentication required.

Admin access is required.

---

# 10. Candidate API

Candidate functionality is exposed through the dedicated Candidates resource.

Candidate endpoints require authentication and candidate authorization.

## Get My Candidate Profile

```text
GET /api/v1/candidates/me
```

Returns the candidate profile belonging to the authenticated user.

---

## Create Candidate Profile

```text
POST /api/v1/candidates
```

Creates a candidate profile for the authenticated user.

The user relationship is derived from the authenticated request.

The client does not provide the user ID.

---

## Update Candidate Profile

```text
PATCH /api/v1/candidates/me
```

Updates the candidate profile belonging to the authenticated user.

Candidate profile data includes fields such as:

```text
headline
summary
location
salaryMin
salaryMax
currency
availabilityDate
remotePreference
```

The exact request fields are defined by the current Candidate DTO.

---

# 11. Recruiter API

Recruiter functionality is exposed through the Recruiters resource.

## Get My Recruiter Profile

```text
GET /api/v1/recruiters/me
```

Authentication required.

Returns the recruiter profile belonging to the authenticated user.

---

## Update My Recruiter Profile

```text
PATCH /api/v1/recruiters/me
```

Authentication required.

Updates the authenticated recruiter's profile.

Recruiter data includes the recruiter's company relationship and optional job title.

---

# 12. Company API

Companies are managed through the authenticated recruiter context.

## Create Company

```text
POST /api/v1/companies
```

Authentication required.

Creates a company and assigns it to the authenticated recruiter.

The client does not provide:

```text
userId
recruiterId
```

The backend derives the relationship from the authenticated user.

Example:

```json
{
  "name": "Example Company",
  "description": "Company description"
}
```

The backend generates a unique company slug.

A recruiter can only be assigned to one company.

---

## Get My Company

```text
GET /api/v1/companies/me
```

Authentication required.

Returns the company associated with the authenticated recruiter.

If the recruiter is not assigned to a company:

```text
404 Not Found
```

---

## Update My Company

```text
PATCH /api/v1/companies/me
```

Authentication required.

Updates the company associated with the authenticated recruiter.

Example:

```json
{
  "name": "Updated Company",
  "description": "Updated description"
}
```

The client does not provide a `companyId`.

---

## Company Deletion

The current API does not expose a company deletion endpoint.

There is no:

```text
DELETE /api/v1/companies/me
DELETE /api/v1/companies/:id
```

---

# 13. Skills API

Skills are shared platform resources.

The Skills API is protected by authentication.

## List Skills

```text
GET /api/v1/skills
```

Supports the query parameters defined by `GetSkillsDto`.

The endpoint is used by the frontend for skill selection and management.

---

## Get Skill

```text
GET /api/v1/skills/:id
```

Returns a specific skill.

The ID is validated as a UUID.

---

## Create Skill

```text
POST /api/v1/skills
```

Admin access required.

Creates a platform skill.

---

## Update Skill

```text
PATCH /api/v1/skills/:id
```

Admin access required.

Updates a platform skill.

---

## Delete Skill

```text
DELETE /api/v1/skills/:id
```

Admin access required.

Deletes a platform skill.

---

# 14. Job API

Jobs are managed through the Jobs resource.

The Jobs API supports both:

* recruiter job management;
* candidate job discovery.

Authentication is required.

---

## Create Job

```text
POST /api/v1/jobs
```

Creates a new job for the authenticated recruiter's company.

The backend derives:

```text
companyId
createdByRecruiterId
```

from the authenticated recruiter.

The client does not provide these ownership fields.

A newly created job starts with:

```text
DRAFT
```

Example:

```json
{
  "title": "Senior React Developer",
  "description": "We are looking for an experienced React developer.",
  "location": "Amsterdam",
  "employmentType": "FULL_TIME",
  "workMode": "HYBRID",
  "salaryMin": 5500,
  "salaryMax": 6500,
  "currency": "EUR",
  "requiredSkillIds": [
    "11111111-1111-4111-8111-111111111111"
  ],
  "preferredSkillIds": [
    "22222222-2222-4222-8222-222222222222"
  ]
}
```

---

## Get Jobs

```text
GET /api/v1/jobs
```

The response depends on the authenticated user's role.

### Recruiter

Recruiters receive jobs belonging to their company.

### Candidate

Candidates receive available published jobs through the job discovery functionality.

Candidate discovery supports:

* text search;
* location;
* work mode;
* employment type;
* salary range;
* skills;
* sorting;
* pagination.

---

## Get Job

```text
GET /api/v1/jobs/:jobId
```

The response depends on the authenticated user's role.

### Recruiter

The recruiter can retrieve a job belonging to their company.

### Candidate

The candidate receives an available published job.

Unavailable jobs such as draft, paused, closed, archived or expired jobs are not exposed through candidate discovery.

---

## Update Job

```text
PATCH /api/v1/jobs/:jobId
```

Updates a job belonging to the authenticated recruiter's company.

The backend validates:

* job ownership;
* job fields;
* employment type;
* work mode;
* salary values;
* salary range;
* expiration data;
* referenced skills;
* duplicate skills;
* required/preferred skill overlap.

---

# 15. Job Status

Jobs use the following status values:

```text
DRAFT
PUBLISHED
PAUSED
CLOSED
ARCHIVED
```

A newly created job starts as:

```text
DRAFT
```

---

# 16. Publish Job

```text
POST /api/v1/jobs/:jobId/publish
```

Publishes a draft job.

The authenticated recruiter must belong to the company that owns the job.

A job can be published when its current status is:

```text
DRAFT
```

After publishing:

```text
status = PUBLISHED
publishedAt = current timestamp
```

---

# 17. Close Job

```text
POST /api/v1/jobs/:jobId/close
```

Closes a published job.

The authenticated recruiter must belong to the company that owns the job.

A job can be closed when its current status is:

```text
PUBLISHED
```

After closing:

```text
status = CLOSED
```

---

# 18. Job Discovery

Candidate job discovery is available through:

```text
GET /api/v1/jobs
```

The discovery API supports the filters represented by `GetJobsQueryDto`.

Current discovery functionality includes:

* keyword search;
* location;
* work mode;
* employment type;
* minimum salary;
* maximum salary;
* skills;
* sorting;
* pagination.

The frontend uses this API to provide the candidate job search experience.

---

# 19. Job Requirements API

Job requirements connect jobs with platform skills.

Each requirement contains:

```text
skillId
required
minimumLevel
```

The database also supports an optional:

```text
weight
```

field.

---

## Get Requirements

```text
GET /api/v1/jobs/:jobId/requirements
```

Returns the requirements belonging to the job.

Requirements include their associated skills.

Required skills are returned before preferred skills.

---

## Create Requirement

```text
POST /api/v1/jobs/:jobId/requirements
```

Example:

```json
{
  "skillId": "11111111-1111-4111-8111-111111111111",
  "required": true,
  "minimumLevel": 4
}
```

Validation includes:

* valid UUID;
* valid boolean `required`;
* integer `minimumLevel`;
* minimum level of 1;
* existing skill;
* no duplicate skill for the job.

---

## Update Requirement

```text
PATCH /api/v1/jobs/:jobId/requirements/:requirementId
```

Example:

```json
{
  "required": false,
  "minimumLevel": 3
}
```

Updates an individual job requirement.

---

## Replace Requirements

```text
PATCH /api/v1/jobs/:jobId/requirements
```

Replaces the complete requirement configuration.

Example:

```json
{
  "requiredSkillIds": [
    "11111111-1111-4111-8111-111111111111"
  ],
  "preferredSkillIds": [
    "22222222-2222-4222-8222-222222222222"
  ]
}
```

The backend validates:

* referenced skills;
* duplicate skills;
* required/preferred overlap.

The replacement is performed transactionally.

---

## Remove Requirement

```text
DELETE /api/v1/jobs/:jobId/requirements/:skillId
```

Removes the requirement associated with the specified skill.

---

# 20. Applications API

Applications are part of the current platform.

A candidate can apply to a job and manage their applications.

Recruiters can review applications belonging to their company.

---

## Create Application

```text
POST /api/v1/jobs/:jobId/applications
```

Creates an application for the authenticated candidate.

Example:

```json
{
  "coverLetter": "I am interested in this position because..."
}
```

The candidate relationship is derived from the authenticated user.

---

## List Candidate Applications

```text
GET /api/v1/applications
```

Returns applications belonging to the authenticated candidate.

---

## Get Candidate Application

```text
GET /api/v1/applications/:applicationId
```

Returns an application belonging to the authenticated candidate.

---

## Withdraw Application

```text
PATCH /api/v1/applications/:applicationId/withdraw
```

Withdraws an application belonging to the authenticated candidate.

---

# 21. Recruiter Applications

Recruiters have a separate application view.

## List Recruiter Applications

```text
GET /api/v1/recruiter/applications
```

Returns applications associated with jobs belonging to the authenticated recruiter's company.

---

## Get Recruiter Application

```text
GET /api/v1/recruiter/applications/:applicationId
```

Returns an application that belongs to the recruiter's company.

---

## Update Application Status

```text
PATCH /api/v1/recruiter/applications/:applicationId/status
```

Updates the application status.

Supported application statuses are:

```text
PENDING
REVIEWING
ACCEPTED
REJECTED
WITHDRAWN
```

---

# 22. Application Ownership

Application access is controlled by the authenticated user.

Candidates can access their own applications.

Recruiters can access applications belonging to their company's jobs.

The client does not provide ownership information such as:

```text
candidateId
companyId
```

for determining access.

The backend derives ownership from the authenticated user and related domain records.

---

# 23. Candidate Skills

Candidate skills are represented in the database through the `CandidateSkill` relation.

The current Prisma model supports:

```text
candidateId
skillId
proficiencyLevel
yearsOfExperience
source
confidence
verified
```

The database also enforces a unique candidate/skill relationship.

Candidate skill API operations should only be documented when exposed by the current backend controller.

---

# 24. Matching

The current database contains a `Match` model for candidate/job matching data.

The current API specification does not expose a matching controller or matching endpoint.

Therefore this document does not define a public matching endpoint.

The current platform API should not assume that a Match API is available.

---

# 25. CV and AI Processing

The current API does not expose CV upload, CV analysis or AI job-analysis endpoints.

Therefore the following are not part of the current API contract:

```text
POST /api/v1/candidates/me/documents
POST /api/v1/candidates/me/analyze-cv
POST /api/v1/jobs/:id/analyze
```

---

# 26. Dashboard API

The platform provides role-specific dashboard endpoints.

## Candidate Dashboard

```text
GET /api/v1/candidates/me/dashboard
```

Candidate authorization is required.

Returns dashboard information for the authenticated candidate.

The dashboard supports the candidate experience including profile, applications, jobs and related overview information.

---

## Recruiter Dashboard

```text
GET /api/v1/recruiters/me/dashboard
```

Recruiter authorization is required.

Returns dashboard information for the authenticated recruiter.

The dashboard provides recruiter-specific overview information such as company, jobs and applications.

---

# 27. Health API

The backend provides a health endpoint.

```text
GET /api/v1/health
```

The endpoint checks the backend database connection.

Example response:

```json
{
  "status": "ok",
  "service": "it-talent-backend",
  "database": "ok"
}
```

---

# 28. Swagger / OpenAPI

The backend exposes Swagger documentation.

```text
/api/docs
```

The Swagger document is generated directly from the NestJS application.

The current API uses bearer authentication in Swagger:

```text
access-token
```

Swagger therefore provides the machine-readable representation of the implemented controller contract.

---

# 29. HTTP Status Codes

The backend uses standard HTTP responses according to the operation.

Common responses include:

| Status | Meaning                            |
| ------ | ---------------------------------- |
| 200    | Successful request                 |
| 201    | Resource created                   |
| 400    | Invalid request or business rule   |
| 401    | Authentication required or invalid |
| 403    | Insufficient permissions           |
| 404    | Resource not found                 |
| 409    | Resource conflict                  |
| 500    | Internal server error              |

The exact status returned by each endpoint is defined by the implementation.

---

# 30. Authorization Model

Authorization is enforced at the backend.

Examples:

```text
Candidate
    ↓
Own candidate profile
Own applications
Candidate job discovery

Recruiter
    ↓
Own recruiter profile
Own company
Own company jobs
Own company applications

Admin
    ↓
User management
Skill management
```

Resource ownership is resolved from the authenticated user whenever possible.

---

# 31. Resource Ownership

The API avoids relying on client-provided ownership identifiers when they can be derived from authentication.

Examples include:

```text
companyId
createdByRecruiterId
candidateId
```

For example, when a recruiter creates a job:

```text
Authenticated User
        ↓
Recruiter
        ↓
Company
        ↓
Job
```

The backend derives the company and recruiter relationships.

---

# 32. Frontend API Usage

The frontend communicates with the backend through HTTP API requests.

Current frontend functionality uses the API for:

* authentication;
* user information;
* candidate profiles;
* recruiter profiles;
* companies;
* jobs;
* job discovery;
* job requirements;
* applications;
* skills;
* dashboards.

Frontend components should not access the database directly.

---

# 33. API and Database Separation

The API contract is independent from the Prisma database representation.

The backend follows the general flow:

```text
HTTP Request
     ↓
Controller
     ↓
DTO / Validation
     ↓
Guard / Authorization
     ↓
Service
     ↓
Prisma
     ↓
PostgreSQL
     ↓
Response
```

Database entities are not automatically exposed as public API contracts.

---

# 34. Error Handling

The backend uses NestJS exception handling and validation.

Invalid requests can result in validation or business-rule errors.

The exact response structure is determined by the current NestJS implementation.

The API documentation must therefore not assume a custom error format that is not implemented.

---

# 35. Pagination and Filtering

Pagination, filtering and sorting are implemented where required by the current endpoint.

The Jobs discovery endpoint supports:

```text
search
location
workMode
employmentType
salaryMin
salaryMax
skills
sorting
pagination
```

The exact query parameter names and response structure are defined by the corresponding DTOs and response DTOs.

Other endpoints should only document pagination or filtering when implemented by their controller and DTOs.

---

# 36. API Security

The backend applies authentication and authorization to protected resources.

Security responsibilities include:

* JWT authentication;
* role authorization;
* DTO validation;
* UUID validation where applicable;
* resource ownership checks;
* protected company access;
* protected job access;
* protected application access.

Database credentials and backend secrets remain backend-only.

---

# 37. Current API Resources

The current API contains the following resource areas:

| Resource         | Current functionality                          |
| ---------------- | ---------------------------------------------- |
| Authentication   | Register and login                             |
| Users            | Current user and user administration           |
| Candidates       | Candidate profile                              |
| Recruiters       | Recruiter profile                              |
| Companies        | Company creation and management                |
| Skills           | Skill listing and administration               |
| Jobs             | Creation, discovery, update, publish and close |
| Job Requirements | Create, read, update, replace and remove       |
| Applications     | Candidate and recruiter application management |
| Dashboard        | Candidate and recruiter dashboards             |
| Health           | Backend/database health check                  |

---

# 38. Current Endpoint Overview

| Method | Endpoint                                               | Purpose                              |
| ------ | ------------------------------------------------------ | ------------------------------------ |
| POST   | `/api/v1/auth/register`                                | Register                             |
| POST   | `/api/v1/auth/login`                                   | Login                                |
| GET    | `/api/v1/users/me`                                     | Current user                         |
| POST   | `/api/v1/users`                                        | Create user                          |
| GET    | `/api/v1/users`                                        | List users                           |
| GET    | `/api/v1/users/:id`                                    | Get user                             |
| PATCH  | `/api/v1/users/:id`                                    | Update user                          |
| DELETE | `/api/v1/users/:id`                                    | Delete user                          |
| GET    | `/api/v1/candidates/me`                                | Get candidate profile                |
| POST   | `/api/v1/candidates`                                   | Create candidate profile             |
| PATCH  | `/api/v1/candidates/me`                                | Update candidate profile             |
| GET    | `/api/v1/recruiters/me`                                | Get recruiter profile                |
| PATCH  | `/api/v1/recruiters/me`                                | Update recruiter profile             |
| POST   | `/api/v1/companies`                                    | Create company                       |
| GET    | `/api/v1/companies/me`                                 | Get company                          |
| PATCH  | `/api/v1/companies/me`                                 | Update company                       |
| GET    | `/api/v1/skills`                                       | List skills                          |
| GET    | `/api/v1/skills/:id`                                   | Get skill                            |
| POST   | `/api/v1/skills`                                       | Create skill                         |
| PATCH  | `/api/v1/skills/:id`                                   | Update skill                         |
| DELETE | `/api/v1/skills/:id`                                   | Delete skill                         |
| POST   | `/api/v1/jobs`                                         | Create job                           |
| GET    | `/api/v1/jobs`                                         | Recruiter jobs / candidate discovery |
| GET    | `/api/v1/jobs/:jobId`                                  | Get job                              |
| PATCH  | `/api/v1/jobs/:jobId`                                  | Update job                           |
| POST   | `/api/v1/jobs/:jobId/publish`                          | Publish job                          |
| POST   | `/api/v1/jobs/:jobId/close`                            | Close job                            |
| GET    | `/api/v1/jobs/:jobId/requirements`                     | Get requirements                     |
| POST   | `/api/v1/jobs/:jobId/requirements`                     | Create requirement                   |
| PATCH  | `/api/v1/jobs/:jobId/requirements/:requirementId`      | Update requirement                   |
| PATCH  | `/api/v1/jobs/:jobId/requirements`                     | Replace requirements                 |
| DELETE | `/api/v1/jobs/:jobId/requirements/:skillId`            | Remove requirement                   |
| POST   | `/api/v1/jobs/:jobId/applications`                     | Apply to job                         |
| GET    | `/api/v1/applications`                                 | Candidate applications               |
| GET    | `/api/v1/applications/:applicationId`                  | Candidate application detail         |
| PATCH  | `/api/v1/applications/:applicationId/withdraw`         | Withdraw application                 |
| GET    | `/api/v1/recruiter/applications`                       | Recruiter applications               |
| GET    | `/api/v1/recruiter/applications/:applicationId`        | Recruiter application detail         |
| PATCH  | `/api/v1/recruiter/applications/:applicationId/status` | Update application status            |
| GET    | `/api/v1/candidates/me/dashboard`                      | Candidate dashboard                  |
| GET    | `/api/v1/recruiters/me/dashboard`                      | Recruiter dashboard                  |
| GET    | `/api/v1/health`                                       | Health check                         |

---

# 39. API Flow

The main platform API flows are:

## Candidate

```text
Register / Login
      ↓
Candidate Profile
      ↓
Skills / Preferences
      ↓
Job Discovery
      ↓
Job Details
      ↓
Application
      ↓
Application Status
```

## Recruiter

```text
Register / Login
      ↓
Recruiter Profile
      ↓
Company
      ↓
Create Job
      ↓
Job Requirements
      ↓
Publish Job
      ↓
Applications
      ↓
Application Status
```

## Admin

```text
Login
  ↓
User Management
  ↓
Skill Management
```

---

# 40. API Source of Truth

For the current API, the implementation hierarchy is:

```text
NestJS Controllers
        ↓
DTOs
        ↓
Guards
        ↓
Services
        ↓
Automated Tests
        ↓
Swagger / OpenAPI
        ↓
api.md
```

`api.md` documents the current API but does not override the backend implementation.

If an endpoint, request field, response field or authorization rule differs between this document and the backend, the backend implementation is authoritative.

---

# 41. Related Architecture

The API is part of the following platform architecture:

```text
Frontend
   ↓
REST API
   ↓
NestJS Backend
   ↓
Prisma
   ↓
PostgreSQL
```

Related documentation:

```text
it-talent-docs/
├── product/
│   ├── vision.md
│   ├── requirements.md
│   └── roadmap.md
│
└── architecture/
    ├── architecture.md
    ├── database.md
    ├── api.md
    └── security.md
```

---

# 42. Document Status

**Document:** api.md
**Version:** 0.3.0
**Status:** Current API Specification
**Last updated:** 2026-09-10

This document describes the current REST API of the IT Talent Platform.

The NestJS backend implementation, DTOs, guards, services, tests and generated Swagger/OpenAPI documentation remain the source of truth for API behavior.
