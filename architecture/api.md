# IT Talent Platform — API Specification

**Document:** `api.md`  
**Version:** `0.4.0`  
**Status:** Current API Specification  
**Last updated:** 2026-11-09

---

# 1. Purpose

This document describes the REST API of the IT Talent Platform.

The API is the communication layer between the frontend and backend.

```text
it-talent-frontend
        │
        │ HTTP / JSON
        ▼
it-talent-backend
        │
        ▼
Prisma / PostgreSQL
```

The frontend does not access PostgreSQL directly.

The backend is responsible for:

- Authentication
- Authorization
- Request validation
- Business logic
- Database access
- Job management
- Candidate profiles
- Company and recruiter data
- Skills
- Job requirements
- Applications
- Dashboard functionality

The actual NestJS controllers, DTOs, guards, services, automated tests, and generated Swagger documentation are the implementation source of truth.

This document describes the known API contract and must be updated when the backend implementation changes.

---

# 2. API Base Path

The backend uses the global API prefix:

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

The default local configuration is:

```text
http://localhost:3000
```

The local API base URL is therefore:

```text
http://localhost:3000/api/v1
```

The port can be changed through backend environment configuration.

---

# 4. API Format

The API uses JSON for normal requests and responses.

```text
Content-Type: application/json
```

The API uses UUID identifiers for domain resources where defined by the Prisma schema and corresponding DTOs.

The exact request and response structures are defined by the backend implementation.

---

# 5. Authentication

Authentication uses JWT bearer tokens.

The login operation returns an access token that is used for protected API requests.

Protected requests use the following header:

```http
Authorization: Bearer <accessToken>
```

The backend validates the token before allowing access to protected endpoints.

Authentication is implemented using:

- JWT authentication guard
- JWT strategy
- Bearer authentication
- Role-based guards where required

Authentication configuration is managed through backend environment variables.

Selected authentication operations are protected by rate limiting.

---

# 6. Roles

The platform supports three application roles:

```text
CANDIDATE
RECRUITER
ADMIN
```

Role-based authorization is enforced by the backend.

The frontend may adapt its navigation and user interface according to the authenticated role.

Frontend restrictions are not a security boundary. The backend remains authoritative for access control.

---

# 7. Request Validation

Incoming requests are validated using NestJS `ValidationPipe`.

The current configuration enables:

```text
whitelist
transform
forbidNonWhitelisted
```

This means:

- DTO validation is applied to incoming data.
- Known values can be transformed to expected types.
- Unsupported properties are rejected.

Validation rules are defined by individual DTOs.

The frontend may also provide immediate form validation, but backend validation remains authoritative.

---

# 8. Authentication API

## 8.1 Register

```http
POST /api/v1/auth/register
```

Creates a user account.

Example request:

```json
{
  "email": "user@example.com",
  "password": "secure-password",
  "role": "CANDIDATE"
}
```

The request is validated by the registration DTO.

The documented validation requirements include:

- `email` must be a valid email address.
- `password` must meet the configured minimum length.
- `role` must be a valid user role if the registration DTO accepts a role.

**Security requirement:** Public registration must not allow users to grant themselves administrative privileges. The backend must enforce the permitted registration roles independently of client input.

The exact accepted fields and response structure are defined by the current registration DTO and service.

## 8.2 Login

```http
POST /api/v1/auth/login
```

Example request:

```json
{
  "email": "user@example.com",
  "password": "secure-password"
}
```

The login endpoint validates the credentials and returns the authentication result.

The current implementation uses JWT bearer authentication for protected API requests.

---

# 9. Users API

The Users API manages platform user accounts.

## 9.1 Current User

```http
GET /api/v1/users/me
```

Authentication required.

Returns information about the authenticated user.

Example response:

```json
{
  "id": "uuid",
  "email": "user@example.com",
  "role": "CANDIDATE"
}
```

The exact response fields are defined by the implementation.

## 9.2 Create User

```http
POST /api/v1/users
```

Authentication required.

Admin access is required according to the documented access model.

Creates a user account through the Users domain.

## 9.3 List Users

```http
GET /api/v1/users
```

Authentication required.

Admin access is required according to the documented access model.

Returns user accounts available to the authorized administrator.

## 9.4 Get User

```http
GET /api/v1/users/:id
```

Authentication required.

Returns a user according to the authorization rules implemented by the backend.

## 9.5 Update User

```http
PATCH /api/v1/users/:id
```

Authentication required.

Updates an authorized user account.

## 9.6 Delete User

```http
DELETE /api/v1/users/:id
```

Authentication required.

Admin access is required according to the documented access model.

---

# 10. Candidate API

Candidate functionality is exposed through the Candidates resource.

Candidate endpoints require authentication and candidate authorization where applicable.

## 10.1 Get My Candidate Profile

```http
GET /api/v1/candidates/me
```

Returns the candidate profile belonging to the authenticated user.

## 10.2 Create Candidate Profile

```http
POST /api/v1/candidates
```

Creates a candidate profile for the authenticated user.

The user relationship is derived from the authenticated request.

The client must not determine the profile owner by supplying an arbitrary user ID.

## 10.3 Update Candidate Profile

```http
PATCH /api/v1/candidates/me
```

Updates the candidate profile belonging to the authenticated user.

Candidate profile data may include fields such as:

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

The exact request fields and validation rules are defined by the current Candidate DTO.

---

# 11. Recruiter API

Recruiter functionality is exposed through the Recruiters resource.

## 11.1 Get My Recruiter Profile

```http
GET /api/v1/recruiters/me
```

Authentication required.

Returns the recruiter profile belonging to the authenticated user.

## 11.2 Update My Recruiter Profile

```http
PATCH /api/v1/recruiters/me
```

Authentication required.

Updates the authenticated recruiter's profile.

Recruiter data includes the recruiter's company relationship and may include a job title.

The exact fields are defined by the current backend DTOs.

---

# 12. Company API

Companies are managed through the authenticated recruiter context.

## 12.1 Create Company

```http
POST /api/v1/companies
```

Authentication required.

Creates a company and associates it with the authenticated recruiter.

The backend derives ownership information from the authenticated user.

The client must not provide ownership fields such as:

```text
userId
recruiterId
```

Example request:

```json
{
  "name": "Example Company",
  "description": "Company description"
}
```

The backend generates a unique company slug.

The documented domain rule allows a recruiter to be assigned to one company.

## 12.2 Get My Company

```http
GET /api/v1/companies/me
```

Authentication required.

Returns the company associated with the authenticated recruiter.

If the recruiter is not assigned to a company, the documented behavior is:

```text
404 Not Found
```

## 12.3 Update My Company

```http
PATCH /api/v1/companies/me
```

Authentication required.

Updates the company associated with the authenticated recruiter.

Example request:

```json
{
  "name": "Updated Company",
  "description": "Updated description"
}
```

The client does not provide a `companyId`.

## 12.4 Company Deletion

The documented API does not expose a company deletion endpoint.

The following routes are not part of this specification:

```text
DELETE /api/v1/companies/me
DELETE /api/v1/companies/:id
```

---

# 13. Skills API

Skills are shared platform resources.

The Skills API requires authentication.

## 13.1 List Skills

```http
GET /api/v1/skills
```

Supports the query parameters defined by `GetSkillsDto`.

The endpoint is used by the frontend for skill selection and management.

## 13.2 Get Skill

```http
GET /api/v1/skills/:id
```

Returns a specific skill.

The ID is validated as a UUID.

## 13.3 Create Skill

```http
POST /api/v1/skills
```

Admin access required.

Creates a platform skill.

## 13.4 Update Skill

```http
PATCH /api/v1/skills/:id
```

Admin access required.

Updates a platform skill.

## 13.5 Delete Skill

```http
DELETE /api/v1/skills/:id
```

Admin access required.

Deletes a platform skill.

---

# 14. Job API

Jobs are managed through the Jobs resource.

The Jobs API supports recruiter job management and candidate job discovery.

Authentication is required.

## 14.1 Create Job

```http
POST /api/v1/jobs
```

Creates a new job for the authenticated recruiter's company.

The backend derives the following relationships:

```text
companyId
createdByRecruiterId
```

The client does not provide these ownership fields.

A newly created job starts with:

```text
DRAFT
```

Example request:

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

The exact accepted fields and validation rules are defined by the job creation DTO.

## 14.2 Get Jobs

```http
GET /api/v1/jobs
```

The response depends on the authenticated user's role.

### Recruiter

Recruiters receive jobs belonging to their company, subject to the backend authorization rules.

### Candidate

Candidates receive available published jobs through the job discovery functionality.

Candidate discovery supports the documented filters:

- Text search
- Location
- Work mode
- Employment type
- Salary range
- Skills
- Sorting
- Pagination

## 14.3 Get Job

```http
GET /api/v1/jobs/:jobId
```

The response depends on the authenticated user's role.

### Recruiter

The recruiter can retrieve a job belonging to their company.

### Candidate

The candidate can retrieve an available published job.

Unavailable jobs, such as draft, paused, closed, archived, or expired jobs, are not exposed through candidate discovery according to the documented access model.

## 14.4 Update Job

```http
PATCH /api/v1/jobs/:jobId
```

Updates a job belonging to the authenticated recruiter's company.

The documented backend validation includes:

- Job ownership
- Job fields
- Employment type
- Work mode
- Salary values
- Salary range
- Expiration data
- Referenced skills
- Duplicate skills
- Required/preferred skill overlap

---

# 15. Job Status

The documented job status values are:

```text
DRAFT
PUBLISHED
PAUSED
CLOSED
ARCHIVED
```

A newly created job starts with:

```text
DRAFT
```

The allowed status transitions are enforced by the backend.

---

# 16. Publish Job

```http
POST /api/v1/jobs/:jobId/publish
```

Publishes a draft job.

The authenticated recruiter must belong to the company that owns the job.

The documented publish operation accepts jobs with the status:

```text
DRAFT
```

After publishing:

```text
status = PUBLISHED
publishedAt = current timestamp
```

The exact response is defined by the backend implementation.

---

# 17. Close Job

```http
POST /api/v1/jobs/:jobId/close
```

Closes a published job.

The authenticated recruiter must belong to the company that owns the job.

The documented close operation accepts jobs with the status:

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

```http
GET /api/v1/jobs
```

The discovery API supports filters represented by `GetJobsQueryDto`.

The documented discovery functionality includes:

- Keyword search
- Location
- Work mode
- Employment type
- Minimum salary
- Maximum salary
- Skills
- Sorting
- Pagination

The frontend uses this API to provide the candidate job search experience.

Exact query parameter names, accepted values, defaults, and response pagination fields are defined by the corresponding DTOs.

---

# 19. Job Requirements API

Job requirements connect jobs with platform skills.

A requirement contains:

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

## 19.1 Get Requirements

```http
GET /api/v1/jobs/:jobId/requirements
```

Returns the requirements belonging to the job.

Requirements include their associated skills.

Required skills are returned before preferred skills according to the documented response behavior.

## 19.2 Create Requirement

```http
POST /api/v1/jobs/:jobId/requirements
```

Example request:

```json
{
  "skillId": "11111111-1111-4111-8111-111111111111",
  "required": true,
  "minimumLevel": 4
}
```

Documented validation includes:

- Valid UUID
- Valid boolean `required`
- Integer `minimumLevel`
- Minimum level of 1
- Existing skill
- No duplicate skill for the job

## 19.3 Update Requirement

```http
PATCH /api/v1/jobs/:jobId/requirements/:requirementId
```

Example request:

```json
{
  "required": false,
  "minimumLevel": 3
}
```

Updates an individual job requirement.

## 19.4 Replace Requirements

```http
PATCH /api/v1/jobs/:jobId/requirements
```

Replaces the complete requirement configuration.

Example request:

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

The documented backend validation includes:

- Referenced skills
- Duplicate skills
- Required/preferred overlap

The replacement is performed transactionally.

## 19.5 Remove Requirement

```http
DELETE /api/v1/jobs/:jobId/requirements/:skillId
```

Removes the requirement associated with the specified skill.

---

# 20. Applications API

Applications connect candidates with jobs.

A candidate can apply to a job and manage their applications.

Recruiters can review applications belonging to their company's jobs.

## 20.1 Create Application

```http
POST /api/v1/jobs/:jobId/applications
```

Creates an application for the authenticated candidate.

Example request:

```json
{
  "coverLetter": "I am interested in this position because..."
}
```

The candidate relationship is derived from the authenticated user.

## 20.2 List Candidate Applications

```http
GET /api/v1/applications
```

Returns applications belonging to the authenticated candidate.

## 20.3 Get Candidate Application

```http
GET /api/v1/applications/:applicationId
```

Returns an application belonging to the authenticated candidate.

## 20.4 Withdraw Application

```http
PATCH /api/v1/applications/:applicationId/withdraw
```

Withdraws an application belonging to the authenticated candidate.

---

# 21. Recruiter Applications

Recruiters have a separate application view.

The following routes are documented in the current API specification. Their exact controller paths and authorization decorators must be checked against the backend implementation.

## 21.1 List Recruiter Applications

```http
GET /api/v1/recruiter/applications
```

Returns applications associated with jobs belonging to the authenticated recruiter's company.

## 21.2 Get Recruiter Application

```http
GET /api/v1/recruiter/applications/:applicationId
```

Returns an application belonging to the recruiter's company.

## 21.3 Update Application Status

```http
PATCH /api/v1/recruiter/applications/:applicationId/status
```

Updates the application status.

The documented status values are:

```text
PENDING
REVIEWING
ACCEPTED
REJECTED
WITHDRAWN
```

The backend is responsible for enforcing valid status transitions and recruiter ownership.

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

to determine access.

The backend derives ownership from the authenticated user and related domain records.

---

# 23. Candidate Skills

Candidate skills are represented in the database through the `CandidateSkill` relation.

The documented Prisma model supports:

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

The existence of the database relation does not automatically mean that corresponding public API operations exist.

Candidate skill endpoints should only be documented when exposed by the current backend controller.

---

# 24. Matching

The database contains a `Match` model for candidate/job matching data.

The current API specification does not define a public matching endpoint.

Therefore, no public Match API is documented here.

The presence of a database model must not be interpreted as confirmation that matching functionality is available through the REST API.

---

# 25. CV and AI Processing

The current API specification does not define public endpoints for CV upload, CV analysis, or AI job analysis.

The following routes are not part of the documented API contract:

```text
POST /api/v1/candidates/me/documents
POST /api/v1/candidates/me/analyze-cv
POST /api/v1/jobs/:id/analyze
```

These endpoints must not be treated as implemented unless they are introduced and verified in the backend.

---

# 26. Dashboard API

The platform provides role-specific dashboard functionality.

The following endpoints are documented in the current API specification.

## 26.1 Candidate Dashboard

```http
GET /api/v1/candidates/me/dashboard
```

Candidate authorization is required.

Returns dashboard information for the authenticated candidate.

## 26.2 Recruiter Dashboard

```http
GET /api/v1/recruiters/me/dashboard
```

Recruiter authorization is required.

Returns dashboard information for the authenticated recruiter.

The exact dashboard response fields are defined by the backend implementation.

---

# 27. Health API

The backend provides a health endpoint.

```http
GET /api/v1/health
```

The documented endpoint checks the backend database connection.

Example response:

```json
{
  "status": "ok",
  "service": "it-talent-backend",
  "database": "ok"
}
```

The exact response structure is defined by the health controller.

---

# 28. Swagger / OpenAPI

The backend exposes Swagger documentation at:

```text
/api/docs
```

The Swagger document is generated from the NestJS application.

The API uses bearer authentication in Swagger.

The generated OpenAPI specification should be used to verify:

- Registered endpoints
- HTTP methods
- Request schemas
- Response schemas
- Query parameters
- Authentication requirements
- Available DTO properties

Swagger is an implementation reference and should remain consistent with the backend controllers and DTOs.

---

# 29. HTTP Status Codes

The backend uses standard HTTP status codes according to the operation.

Common responses include:

| Status | Meaning |
|---|---|
| 200 | Successful request |
| 201 | Resource created |
| 400 | Invalid request or business rule |
| 401 | Authentication required or invalid |
| 403 | Insufficient permissions |
| 404 | Resource not found |
| 409 | Resource conflict |
| 500 | Internal server error |

The exact status returned by each endpoint is defined by the implementation.

---

# 30. Authorization Model

Authorization is enforced by the backend.

The documented access model is:

```text
Candidate
    │
    ├── Own candidate profile
    ├── Own applications
    └── Candidate job discovery

Recruiter
    │
    ├── Own recruiter profile
    ├── Own company
    ├── Own company jobs
    └── Own company applications

Admin
    │
    ├── User management
    └── Skill management
```

Resource ownership is resolved from the authenticated user wherever possible.

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
        │
        ▼
Recruiter
        │
        ▼
Company
        │
        ▼
Job
```

The backend derives the company and recruiter relationships.

This prevents clients from assigning resources to arbitrary users or companies.

---

# 32. Frontend API Usage

The frontend communicates with the backend through HTTP API requests.

The documented frontend integration areas include:

- Authentication
- User information
- Candidate profiles
- Recruiter profiles
- Companies
- Jobs
- Job discovery
- Job requirements
- Applications
- Skills
- Dashboards

Frontend components must not access the database directly.

The frontend API base URL is configured through:

```text
VITE_API_BASE_URL
```

---

# 33. API and Database Separation

The API contract is independent of the Prisma database representation.

The backend follows this general request flow:

```text
HTTP Request
     │
     ▼
Controller
     │
     ▼
DTO / Validation
     │
     ▼
Guard / Authorization
     │
     ▼
Service
     │
     ▼
Prisma
     │
     ▼
PostgreSQL
     │
     ▼
HTTP Response
```

Database entities are not automatically exposed as public API contracts.

Controllers and response DTOs define the external API behavior.

---

# 34. Error Handling

The backend uses NestJS exception handling and request validation.

Invalid requests can result in validation or business-rule errors.

The exact response structure is determined by the current NestJS implementation.

This document does not assume a custom error format unless it is implemented and documented by the backend.

---

# 35. Pagination and Filtering

Pagination, filtering, and sorting are implemented where required by individual endpoints.

The Jobs discovery endpoint supports the documented filters:

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

The exact query parameter names, accepted values, defaults, and response structure are defined by the corresponding DTOs and response DTOs.

Other endpoints should only document pagination or filtering when supported by their controller and DTO.

---

# 36. API Security

The backend applies authentication and authorization to protected resources.

Security responsibilities include:

- JWT authentication
- Role-based authorization
- DTO validation
- UUID validation where applicable
- Resource ownership checks
- Protected company access
- Protected job access
- Protected application access
- Rate limiting for selected authentication operations
- Trusted CORS origin configuration

Database credentials and backend secrets remain backend-only.

Public registration must not allow unauthorized role escalation.

---

# 37. Current API Resources

The following resource areas are included in this API specification.

| Resource | Documented functionality |
|---|---|
| Authentication | Registration and login |
| Users | Current user and user administration |
| Candidates | Candidate profile |
| Recruiters | Recruiter profile |
| Companies | Company creation and management |
| Skills | Skill listing and administration |
| Jobs | Creation, discovery, update, publish, and close |
| Job Requirements | Create, read, update, replace, and remove |
| Applications | Candidate and recruiter application management |
| Dashboard | Candidate and recruiter dashboards |
| Health | Backend and database health check |

The table describes the documented API scope. The backend implementation remains authoritative for actual availability.

---

# 38. Current Endpoint Overview

The following table summarizes the endpoint paths documented in this specification. Confirm each route against the current NestJS controllers and generated Swagger document before treating it as an exhaustive endpoint inventory.

| Method | Endpoint | Purpose |
|---|---|---|
| POST | `/api/v1/auth/register` | Register |
| POST | `/api/v1/auth/login` | Login |
| GET | `/api/v1/users/me` | Current user |
| POST | `/api/v1/users` | Create user |
| GET | `/api/v1/users` | List users |
| GET | `/api/v1/users/:id` | Get user |
| PATCH | `/api/v1/users/:id` | Update user |
| DELETE | `/api/v1/users/:id` | Delete user |
| GET | `/api/v1/candidates/me` | Get candidate profile |
| POST | `/api/v1/candidates` | Create candidate profile |
| PATCH | `/api/v1/candidates/me` | Update candidate profile |
| GET | `/api/v1/recruiters/me` | Get recruiter profile |
| PATCH | `/api/v1/recruiters/me` | Update recruiter profile |
| POST | `/api/v1/companies` | Create company |
| GET | `/api/v1/companies/me` | Get company |
| PATCH | `/api/v1/companies/me` | Update company |
| GET | `/api/v1/skills` | List skills |
| GET | `/api/v1/skills/:id` | Get skill |
| POST | `/api/v1/skills` | Create skill |
| PATCH | `/api/v1/skills/:id` | Update skill |
| DELETE | `/api/v1/skills/:id` | Delete skill |
| POST | `/api/v1/jobs` | Create job |
| GET | `/api/v1/jobs` | Recruiter jobs / candidate discovery |
| GET | `/api/v1/jobs/:jobId` | Get job |
| PATCH | `/api/v1/jobs/:jobId` | Update job |
| POST | `/api/v1/jobs/:jobId/publish` | Publish job |
| POST | `/api/v1/jobs/:jobId/close` | Close job |
| GET | `/api/v1/jobs/:jobId/requirements` | Get requirements |
| POST | `/api/v1/jobs/:jobId/requirements` | Create requirement |
| PATCH | `/api/v1/jobs/:jobId/requirements/:requirementId` | Update requirement |
| PATCH | `/api/v1/jobs/:jobId/requirements` | Replace requirements |
| DELETE | `/api/v1/jobs/:jobId/requirements/:skillId` | Remove requirement |
| POST | `/api/v1/jobs/:jobId/applications` | Apply to job |
| GET | `/api/v1/applications` | Candidate applications |
| GET | `/api/v1/applications/:applicationId` | Candidate application detail |
| PATCH | `/api/v1/applications/:applicationId/withdraw` | Withdraw application |
| GET | `/api/v1/recruiter/applications` | Recruiter applications |
| GET | `/api/v1/recruiter/applications/:applicationId` | Recruiter application detail |
| PATCH | `/api/v1/recruiter/applications/:applicationId/status` | Update application status |
| GET | `/api/v1/candidates/me/dashboard` | Candidate dashboard |
| GET | `/api/v1/recruiters/me/dashboard` | Recruiter dashboard |
| GET | `/api/v1/health` | Health check |

---

# 39. API Flow

The main documented platform API flows are:

## Candidate

```text
Register / Login
      │
      ▼
Candidate Profile
      │
      ▼
Skills / Preferences
      │
      ▼
Job Discovery
      │
      ▼
Job Details
      │
      ▼
Application
      │
      ▼
Application Status
```

## Recruiter

```text
Register / Login
      │
      ▼
Recruiter Profile
      │
      ▼
Company
      │
      ▼
Create Job
      │
      ▼
Job Requirements
      │
      ▼
Publish Job
      │
      ▼
Applications
      │
      ▼
Application Status
```

## Admin

```text
Login
  │
  ▼
User Management
  │
  ▼
Skill Management
```

---

# 40. API Source of Truth

The backend implementation is authoritative for API behavior.

The verification hierarchy is:

```text
NestJS Controllers
        │
        ▼
DTOs and Validation
        │
        ▼
Guards and Authorization
        │
        ▼
Services
        │
        ▼
Automated Tests
        │
        ▼
Generated Swagger / OpenAPI
        │
        ▼
api.md
```

`api.md` documents the API but does not override the backend implementation.

If an endpoint, request field, response field, status code, or authorization rule differs between this document and the backend, the backend implementation is authoritative.

Discrepancies should be corrected in the documentation after verification.

---

# 41. Related Architecture

The API is part of the following platform architecture:

```text
Frontend
   │
   ▼
REST API
   │
   ▼
NestJS Backend
   │
   ▼
Prisma
   │
   ▼
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

**Document:** `api.md`  
**Version:** `0.4.0`  
**Status:** Current API Specification  
**Last updated:** 2026-11-09

This document describes the documented REST API of the IT Talent Platform.

The NestJS backend implementation, DTOs, guards, services, automated tests, and generated Swagger/OpenAPI documentation remain the source of truth for API behavior.