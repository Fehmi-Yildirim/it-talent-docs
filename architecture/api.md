# IT Talent Platform — API Specification

**Document:** api.md
**Version:** 0.2.0
**Status:** Canonical API Contract Baseline
**Last updated:** 2026-08-28

---

# 1. Purpose

This document defines the REST API architecture for the IT Talent Platform.

The API is the formal communication boundary between:

```text
it-talent-frontend

        │

        │ HTTPS / JSON

        ▼

it-talent-backend

        │

        ├── PostgreSQL
        ├── Business Logic
        ├── Matching Engine
        └── AI Services
```

The frontend must not directly access PostgreSQL.

This document distinguishes between:

* endpoints currently implemented;
* endpoints that are partially implemented;
* canonical target endpoints;
* planned endpoints belonging to the target platform architecture.

The actual NestJS controllers, DTOs, guards, services and tests remain the implementation source of truth.

---

# 2. API Contract Status Model

The following status model is used throughout this document.

| Status                  | Meaning                                                                           |
| ----------------------- | --------------------------------------------------------------------------------- |
| ✅ Implemented           | Endpoint currently exists and is part of the active backend implementation        |
| 🟡 Foundation / Partial | Related functionality exists, but the complete endpoint/domain is not implemented |
| 🔵 Planned / Roadmap    | Target architecture only; not currently implemented                               |

A documented target endpoint must not be interpreted as an implemented endpoint unless the backend implementation confirms its existence.

---

# 3. API Principles

The API follows these architectural principles:

* REST-oriented resources;
* JSON request and response bodies;
* HTTPS in deployed environments;
* versioned API paths;
* explicit validation;
* predictable HTTP status codes;
* authentication where required;
* backend-enforced authorization;
* DTO-based request validation;
* domain-level API resources;
* pagination for large collections where required;
* filtering and sorting where appropriate.

The API must remain independent from the internal Prisma/database structure.

---

# 4. Base URL

## Local development

The intended local API base path is:

```text
http://localhost:3001/api/v1
```

The exact local port and prefix must match the backend configuration.

## Production

The production API will eventually use a dedicated backend domain:

```text
https://<backend-domain>/api/v1
```

The production domain is not fixed in this document.

---

# 5. API Versioning

**Status:** 🟡 Architecture / Foundation

The target API version is:

```text
/api/v1
```

Examples:

```text
GET /api/v1/jobs
GET /api/v1/candidates
```

Breaking API changes should result in a new major API version.

The actual global prefix must match the NestJS application configuration.

---

# 6. Content Type

Standard API requests and responses use JSON.

```text
Content-Type: application/json
```

Future file-upload functionality may use:

```text
multipart/form-data
```

or signed object-storage uploads.

CV upload is currently roadmap functionality.

---

# 7. Authentication

**Status:** 🟡 Foundation / Partial

Authentication is handled by the backend.

Conceptually:

```text
Client
  │
  ├── Login/Register
  ▼
Backend
  │
  └── Authenticated session/token
        │
        ▼
Protected API
```

Authentication must never rely exclusively on frontend restrictions.

The exact token/session transport mechanism must match the current backend implementation.

---

# 8. Roles

**Status:** 🟡 Foundation / Partial

The target platform roles are:

```text
CANDIDATE
RECRUITER
ADMIN
```

Authorization must be enforced by the backend.

Not every role is necessarily fully implemented yet.

---

# 9. HTTP Status Codes

The target API uses conventional HTTP status codes.

| Status | Meaning                         |
| ------ | ------------------------------- |
| 200    | Successful request              |
| 201    | Resource created                |
| 204    | Successful request with no body |
| 400    | Invalid request                 |
| 401    | Authentication required/invalid |
| 403    | Insufficient permissions        |
| 404    | Resource not found              |
| 409    | Resource conflict               |
| 422    | Validation error                |
| 429    | Rate limit exceeded             |
| 500    | Internal server error           |
| 503    | Service unavailable             |

The actual status code returned by the current backend is authoritative.

---

# 10. Standard Error Format

**Status:** 🟡 Foundation / Partial

The target API should use a consistent error structure.

Example:

```json
{
  "statusCode": 400,
  "code": "INVALID_REQUEST",
  "message": "The request could not be processed.",
  "details": []
}
```

Validation example:

```json
{
  "statusCode": 422,
  "code": "VALIDATION_ERROR",
  "message": "Validation failed.",
  "details": [
    {
      "field": "email",
      "message": "Invalid email address."
    }
  ]
}
```

The exact error structure must follow the current NestJS exception/filter implementation.

---

# 11. Pagination

**Status:** 🔵 Planned / Endpoint-dependent

Collection endpoints should support pagination where required.

Example:

```text
GET /api/v1/jobs?page=1&limit=20
```

Target response:

```json
{
  "data": [],
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 120,
    "totalPages": 6
  }
}
```

The backend should enforce a maximum limit.

For example:

```text
limit <= 100
```

Pagination should only be documented for endpoints that actually implement it.

---

# 12. Sorting

**Status:** 🔵 Planned

Collection endpoints may support sorting.

Example:

```text
GET /api/v1/jobs?sort=createdAt&order=desc
```

Only explicitly supported fields may be used.

---

# 13. Filtering

**Status:** 🔵 Planned

Collection endpoints may support filtering.

Example:

```text
GET /api/v1/jobs?status=PUBLISHED&workMode=REMOTE
```

Future candidate discovery may support:

```text
GET /api/v1/candidates?skill=react&location=Amsterdam
```

Advanced filtering is roadmap functionality.

---

# 14. Authentication Endpoints

## Register

**Status:** ✅ Implemented / Verify against current controller contract

Target:

```text
POST /api/v1/auth/register
```

Request:

```json
{
  "email": "user@example.com",
  "password": "secure-password",
  "role": "CANDIDATE"
}
```

The exact request and response schema must match the implemented DTO.

## Login

**Status:** ✅ Implemented / Verify against current controller contract

Target:

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

The backend creates the authenticated state according to the implemented authentication mechanism.

The documentation must not assume JWT/cookie/refresh-token behavior unless it exists in the implementation.

---

# 15. Users

The Users domain represents the platform user account and its core user-management operations.

## Canonical Users API

The canonical Users endpoints are:

| Method | Endpoint            | Purpose                              |
| ------ | ------------------- | ------------------------------------ |
| GET    | `/api/v1/users/me`  | Get the authenticated user's account |
| POST   | `/api/v1/users`     | Create a user                        |
| GET    | `/api/v1/users`     | List users                           |
| GET    | `/api/v1/users/:id` | Get a user by ID                     |
| PATCH  | `/api/v1/users/:id` | Update a user                        |
| DELETE | `/api/v1/users/:id` | Delete a user                        |

These endpoints define the target Users resource boundary.

User-specific Candidate and Candidate Skill operations are not part of the canonical Users API.

---

# 16. Current User

**Status:** 🟡 Foundation / Partial

Canonical endpoint:

```text
GET /api/v1/users/me
```

Returns information belonging to the authenticated user.

Example:

```json
{
  "id": "...",
  "email": "...",
  "role": "CANDIDATE"
}
```

The exact response must match the current backend DTO.

---

# 17. User Management

The following endpoints belong to the canonical Users API.

## Create User

**Status:** 🟡 Foundation / Partial

```text
POST /api/v1/users
```

The exact request and response schemas must match the current backend implementation.

## List Users

**Status:** 🟡 Foundation / Partial

```text
GET /api/v1/users
```

Pagination, filtering and sorting must only be documented when supported by the backend.

## Get User

**Status:** 🟡 Foundation / Partial

```text
GET /api/v1/users/:id
```

The returned information must respect authentication, authorization and privacy rules.

## Update User

**Status:** 🟡 Foundation / Partial

```text
PATCH /api/v1/users/:id
```

The backend must enforce which fields the authenticated user or authorized administrator may modify.

## Delete User

**Status:** 🟡 Foundation / Partial

```text
DELETE /api/v1/users/:id
```

The backend must enforce appropriate authorization and resource ownership rules.

---

# 18. Candidate

Candidate functionality belongs to the Candidate domain rather than the Users domain.

## Current Backend Implementation

**Status:** 🟡 Foundation / Partial

The current backend exposes Candidate functionality through the Users resource:

```text
GET /api/v1/users/me/candidate
POST /api/v1/users/me/candidate
```

These endpoints represent the current implementation and are not the canonical long-term Candidate API.

## Canonical Candidate API

The target Candidate API is:

```text
GET /api/v1/candidates/me
POST /api/v1/candidates
PATCH /api/v1/candidates/me
```

**Status:** 🔵 Planned / Roadmap

The canonical Candidate API establishes Candidate as an independent domain resource.

The future refactoring should migrate Candidate operations from the current `/users/me/candidate` routes to the canonical `/candidates` resource.

Example profile payload:

```json
{
  "headline": "Senior React Developer",
  "summary": "Software engineer with 6 years of experience.",
  "location": "Amsterdam",
  "salaryMin": 5500,
  "salaryMax": 6500,
  "currency": "EUR",
  "remotePreference": "HYBRID"
}
```

Not every field in this example is necessarily implemented.

The actual backend DTO and response structure remain authoritative for the current implementation.

## Migration Direction

The intended API migration is:

```text
Current:

/api/v1/users/me/candidate

        ↓

Canonical:

/api/v1/candidates/me
/api/v1/candidates
```

The current routes should not be considered part of the canonical long-term API.

No backend implementation change is part of this documentation ticket.


Candidate functionality belongs to the Candidate domain rather than the canonical Users domain.

## Canonical Candidate API

Target endpoints:

```text
GET /api/v1/candidates/me
POST /api/v1/candidates
PATCH /api/v1/candidates/me
```

**Status:** 🔵 Planned / Roadmap unless confirmed by the backend implementation.

Example profile payload:

```json
{
  "headline": "Senior React Developer",
  "summary": "Software engineer with 6 years of experience.",
  "location": "Amsterdam",
  "salaryMin": 5500,
  "salaryMax": 6500,
  "currency": "EUR",
  "remotePreference": "HYBRID"
}
```

Not every field in this example is necessarily implemented.

The actual DTO/schema is authoritative.

## Legacy / Current User-Candidate Routes

The following routes may exist in the current backend implementation:

```text
GET /api/v1/users/me/candidate
POST /api/v1/users/me/candidate
```

These routes are not part of the canonical target Candidate API.

If they exist in the current backend, they represent the current implementation state and should be treated as migration/refactoring candidates for the future Candidate domain.

They must not be interpreted as the canonical long-term Candidate route structure.

---

# 19. Candidate Skills

Candidate Skills belongs to the Candidate domain and is separate from the canonical Users API.

## Canonical Candidate Skills API

Target endpoints:

```text
GET /api/v1/candidates/me/skills
POST /api/v1/candidates/me/skills
PATCH /api/v1/candidates/me/skills/:id
DELETE /api/v1/candidates/me/skills/:id
```

**Status:** 🔵 Planned / Roadmap unless confirmed by the backend implementation.

Example:

```json
{
  "skillId": "...",
  "proficiencyLevel": 4,
  "yearsOfExperience": 5
}
```

## Legacy / Current User-Skills Routes

The following routes may exist in the current backend implementation:

```text
GET /api/v1/users/me/skills
POST /api/v1/users/me/skills
PATCH /api/v1/users/me/skills/:skillId
DELETE /api/v1/users/me/skills/:skillId
```

These routes are not part of the canonical target Candidate Skills API.

If they exist in the current backend, they represent the current implementation state and should be treated as migration/refactoring candidates.

---

# 20. Candidate Discovery

**Status:** 🔵 Planned / Roadmap

Target:

```text
GET /api/v1/candidates
```

Potential filters:

```text
skill
location
experience
page
limit
```

Example:

```text
GET /api/v1/candidates
  ?skill=react
  &location=Amsterdam
  &page=1
  &limit=20
```

Candidate discovery requires recruiter/company authorization and privacy controls.

---

# 21. Candidate Detail

**Status:** 🔵 Planned / Roadmap

Target:

```text
GET /api/v1/candidates/:id
```

The returned information must depend on the authenticated user's permissions.

Private candidate information must not automatically be exposed.

---

# 22. Company

**Status:** ✅ Implemented

The Company domain is currently implemented for authenticated recruiters.

## Implemented Endpoints

| Method | Endpoint               | Purpose                                                       |
| ------ | ---------------------- | ------------------------------------------------------------- |
| POST   | `/api/v1/companies`    | Create a company and assign it to the authenticated recruiter |
| GET    | `/api/v1/companies/me` | Get the authenticated recruiter's company                     |
| PATCH  | `/api/v1/companies/me` | Update the authenticated recruiter's company                  |

## Create Company

**Status:** ✅ Implemented

```text
POST /api/v1/companies
```

Authentication is required.

Only authenticated recruiters can create a company.

When a company is created, it is automatically assigned to the authenticated recruiter. The client does not provide the recruiter ID or user ID for this relationship.

A recruiter can be assigned to at most one company. If the recruiter is already assigned to a company, the request is rejected.

Example request:

```json
{
  "name": "Example Company",
  "description": "Company description"
}
```

The backend generates a unique slug for the company.

Expected status codes:

```text
201 Created
401 Unauthorized
403 Forbidden
409 Conflict
```

## Get My Company

**Status:** ✅ Implemented

```text
GET /api/v1/companies/me
```

Authentication is required.

The endpoint returns the company assigned to the authenticated recruiter.

If the authenticated recruiter is not assigned to a company, the backend returns:

```text
404 Not Found
```

The company is resolved from the authenticated recruiter context. The client does not provide a `companyId`.

## Update My Company

**Status:** ✅ Implemented

```text
PATCH /api/v1/companies/me
```

Authentication is required.

The endpoint allows an authenticated recruiter to update their own company.

Example request:

```json
{
  "name": "Updated Company Name",
  "description": "Updated company description"
}
```

The backend derives the company from the authenticated recruiter context. The client does not provide a `companyId`.

## Company Ownership and Isolation

**Status:** ✅ Implemented

Company access is scoped to the authenticated recruiter.

A recruiter can only retrieve and update the company assigned to that recruiter through the `/companies/me` endpoints.

The backend must not rely on client-provided identifiers to determine company ownership.

Ownership isolation is covered by the E2E test suite.

## Company Deletion

**Status:** ✅ Not Implemented by Design

The current API does not expose a Company DELETE endpoint.

The following endpoints are intentionally not available:

```text
DELETE /api/v1/companies/me
DELETE /api/v1/companies/:id
```

Company deletion is therefore outside the current Company API scope.


**Status:** 🔵 Planned / Roadmap

Target endpoints:

```text
GET /api/v1/companies/:id
PATCH /api/v1/companies/me
```

Company authorization will be required.

Company functionality is not currently part of the implemented MVP foundation.

---

# 23. Recruiter Profile

**Status:** 🔵 Planned / Roadmap

Target:

```text
GET /api/v1/recruiters/me
PATCH /api/v1/recruiters/me
```

Recruiter functionality depends on the Company/Recruiter domain implementation.

---
# 24. Jobs

**Status:** ✅ Implemented

The Jobs domain is currently implemented for authenticated recruiters.

Jobs are scoped to the company assigned to the authenticated recruiter. The client does not provide a `companyId` or `createdByRecruiterId`; these relationships are derived from the authenticated recruiter context.

## Implemented Endpoints

| Method | Endpoint              | Purpose                                                     |
| ------ | --------------------- | ----------------------------------------------------------- |
| POST   | `/api/v1/jobs`        | Create a new draft job                                      |
| GET    | `/api/v1/jobs`        | Get jobs belonging to the authenticated recruiter's company |
| GET    | `/api/v1/jobs/:jobId` | Get a specific job                                          |
| PATCH  | `/api/v1/jobs/:jobId` | Update an existing job                                      |

All Jobs endpoints require authentication.

Only recruiters can access the Jobs API.

## Create Job

**Status:** ✅ Implemented

```text
POST /api/v1/jobs
```

The authenticated recruiter must be assigned to a company.

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

A newly created job always starts with:

```text
status = DRAFT
```

The backend automatically assigns:

```text
companyId
createdByRecruiterId
```

from the authenticated recruiter.

The client must not provide these ownership fields.

Validation includes:

* title length;
* description length;
* employment type;
* work mode;
* salary values;
* salary range;
* expiration date;
* referenced skill IDs;
* duplicate required skills;
* duplicate preferred skills;
* overlap between required and preferred skills.

Expected status codes include:

```text
201 Created
400 Bad Request
401 Unauthorized
403 Forbidden
```

## Get Jobs

**Status:** ✅ Implemented

```text
GET /api/v1/jobs
```

The endpoint returns jobs belonging to the authenticated recruiter's company.

Jobs are ordered by creation date, newest first.

The response includes job requirements and their associated skills.

A recruiter cannot retrieve jobs belonging to another company.

## Get Job

**Status:** ✅ Implemented

```text
GET /api/v1/jobs/:jobId
```

The endpoint returns a job only when the job belongs to the authenticated recruiter's company.

If the job does not exist or belongs to another company:

```text
404 Not Found
```

## Update Job

**Status:** ✅ Implemented

```text
PATCH /api/v1/jobs/:jobId
```

The endpoint allows the authenticated recruiter to update a job belonging to their company.

The backend validates the resulting salary range when either salary value is changed.

Required and preferred skill arrays can also be supplied to replace the complete job requirement configuration.

The backend validates:

* duplicate skills;
* required/preferred overlap;
* referenced skill existence;
* resulting salary range.

Company ownership is enforced by the backend.

---

# 25. Job Publishing

**Status:** ✅ Implemented

The Jobs API supports publishing draft jobs.

## Publish Job

```text
POST /api/v1/jobs/:jobId/publish
```

Only the authenticated recruiter belonging to the job's company can publish the job.

A job can only be published when its current status is:

```text
DRAFT
```

When successfully published:

```text
status = PUBLISHED
publishedAt = current timestamp
```

A job that is already published cannot be published again.

Expected status codes include:

```text
201 Created
400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
```

The backend is responsible for validating the current job state before publishing.

---

# 26. Job Closing

**Status:** ✅ Implemented

The Jobs API supports closing published jobs.

## Close Job

```text
POST /api/v1/jobs/:jobId/close
```

Only the authenticated recruiter belonging to the job's company can close the job.

A job can only be closed when its current status is:

```text
PUBLISHED
```

When successfully closed:

```text
status = CLOSED
```

A job that is already closed cannot be closed again.

Expected status codes include:

```text
201 Created
400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
```

The backend enforces company ownership and job status transitions.

The current Jobs API does not expose a separate job deletion endpoint.

---

# 27. Job Requirements

**Status:** ✅ Implemented

Job Requirements are implemented as a separate domain resource associated with a Job and a Skill.

Each job requirement contains:

```text
skillId
required
minimumLevel
```

The database also supports an optional `weight` field, although the current Jobs API does not expose weight through the requirement DTOs.

## Implemented Endpoints

| Method | Endpoint                                          | Purpose                        |
| ------ | ------------------------------------------------- | ------------------------------ |
| GET    | `/api/v1/jobs/:jobId/requirements`                | Get all requirements for a job |
| POST   | `/api/v1/jobs/:jobId/requirements`                | Add one requirement            |
| PATCH  | `/api/v1/jobs/:jobId/requirements/:requirementId` | Update one requirement         |
| PATCH  | `/api/v1/jobs/:jobId/requirements`                | Replace all requirements       |
| DELETE | `/api/v1/jobs/:jobId/requirements/:skillId`       | Remove one requirement         |

All requirement operations require authentication and recruiter authorization.

The job must belong to the authenticated recruiter's company.

## Create Job Requirement

**Status:** ✅ Implemented

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

* valid UUID for `skillId`;
* `required` must be boolean;
* `minimumLevel` must be an integer;
* `minimumLevel` must be at least `1`;
* the referenced skill must exist;
* the skill cannot already be assigned to the job.

## Get Job Requirements

**Status:** ✅ Implemented

```text
GET /api/v1/jobs/:jobId/requirements
```

The endpoint returns all requirements for the specified job.

Requirements are returned with their associated Skill records.

Required skills are listed before preferred skills.

Within the same requirement type, skills are ordered alphabetically by skill name.

## Update Job Requirement

**Status:** ✅ Implemented

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

Both fields are optional.

The backend validates the updated requirement and ensures that the requirement belongs to the specified job.

## Replace All Job Requirements

**Status:** ✅ Implemented

```text
PATCH /api/v1/jobs/:jobId/requirements
```

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

The supplied arrays replace the complete existing requirement configuration.

A skill cannot appear more than once within an array and cannot be both required and preferred.

All referenced skills must exist.

The replacement operation is performed transactionally so that the requirement set is replaced consistently.

## Remove Job Requirement

**Status:** ✅ Implemented

```text
DELETE /api/v1/jobs/:jobId/requirements/:skillId
```

The endpoint removes the requirement associated with the specified skill.

If the requirement does not exist:

```text
404 Not Found
```

## Job Requirement Ownership

Job requirements are always accessed through their parent Job.

The backend first verifies that the Job belongs to the authenticated recruiter's company.

This prevents a recruiter from reading or modifying requirements belonging to another company's job.

## Job Requirement Model

The current database model is:

```text
Job
 │
 └── JobRequirement
       │
       └── Skill
```

A job cannot contain the same skill more than once because the database enforces:

```text
unique(jobId, skillId)
```

The current API therefore supports both:

```text
required skill
```

and:

```text
preferred skill
```

through the `required` property.


# 28. Skills

**Status:** ✅ Implemented

The Skills domain is part of the current backend foundation.

Target endpoint:

```text
GET /api/v1/skills
```

Potential search:

```text
GET /api/v1/skills?search=react
```

Example response:

```json
{
  "data": [
    {
      "id": "...",
      "name": "React",
      "slug": "react",
      "category": "FRONTEND"
    }
  ]
}
```

The actual endpoint path, query parameters and response structure must match the implemented controller.

---

# 29. Skill Creation

**Status:** 🟡 Foundation / Partial

Target:

```text
POST /api/v1/skills
```

Skill creation should be restricted to authorized platform functionality.

Candidates should not be able to arbitrarily create global platform skills.

The exact authorization rules must match the current implementation.

---

# 30. Matching

**Status:** 🔵 Planned / Roadmap

Matching is a core future domain.

Target:

```text
POST /api/v1/jobs/:id/match
```

Conceptually:

```text
Job
 ↓
Requirements
 ↓
Candidate pool
 ↓
Skill comparison
 ↓
Experience
 ↓
Location
 ↓
Salary
 ↓
Availability
 ↓
Preferences
 ↓
Score
```

The matching engine is not currently considered implemented merely because this endpoint is documented.

---

# 31. Job Matches

**Status:** 🔵 Planned / Roadmap

Target:

```text
GET /api/v1/jobs/:id/matches
```

Potential query:

```text
?minScore=70&page=1&limit=20
```

Target response:

```json
{
  "data": [
    {
      "id": "...",
      "candidateId": "...",
      "overallScore": 91,
      "skillScore": 95,
      "experienceScore": 90,
      "locationScore": 100
    }
  ],
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 12,
    "totalPages": 1
  }
}
```

---

# 32. Match Detail

**Status:** 🔵 Planned / Roadmap

Target:

```text
GET /api/v1/matches/:id
```

The response should eventually contain:

* overall score;
* component scores;
* strengths;
* gaps;
* relevant candidate information;
* relevant job information.

---

# 33. Match Explanation

**Status:** 🔵 Planned / Roadmap

Target structure:

```json
{
  "overallScore": 91,
  "scores": {
    "skills": 95,
    "experience": 90,
    "location": 100,
    "salary": 85,
    "availability": 100,
    "preferences": 80
  },
  "strengths": [
    "React",
    "TypeScript",
    "AWS"
  ],
  "gaps": [
    "Kubernetes"
  ]
}
```

The explanation must be generated from actual matching data.

AI may assist with natural-language presentation, but must not invent match reasons.

---

# 34. CV Upload

**Status:** 🔵 Planned / Roadmap

Potential future endpoint:

```text
POST /api/v1/candidates/me/documents
```

Possible upload mechanism:

```text
Frontend
   ↓
Backend requests upload URL
   ↓
Object Storage
   ↓
Backend receives metadata
   ↓
AI processing
```

No CV upload API should be considered implemented until the corresponding backend functionality exists.

---

# 35. AI Processing

**Status:** 🔵 Planned / Roadmap

AI providers should not be exposed directly to the frontend.

Instead, the backend should expose domain-level operations.

Example:

```text
POST /api/v1/candidates/me/analyze-cv
```

Conceptually:

```text
CV
 ↓
Candidate Processing Service
 ↓
AI Provider
 ↓
Structured CandidateSkill
```

AI credentials remain backend-only.

---

# 36. AI Job Analysis

**Status:** 🔵 Planned / Roadmap

Potential endpoint:

```text
POST /api/v1/jobs/:id/analyze
```

The backend may:

* read the job description;
* send appropriate content to the AI provider;
* extract skills;
* normalize skills;
* create/update JobRequirements;
* return structured results.

This functionality is not currently part of the implemented API.

---

# 37. Idempotency

**Status:** 🔵 Planned

Idempotency may eventually be required for retry-sensitive operations such as:

* AI processing;
* document processing;
* bulk matching;
* payments.

It should be introduced when a concrete operation requires it.

---

# 38. Rate Limiting

**Status:** 🔵 Security / Implementation Requirement

The API should implement rate limiting.

Particularly sensitive endpoints include:

```text
POST /auth/login
POST /auth/register
POST /jobs/:id/match
POST /candidates/me/analyze-cv
POST /jobs/:id/analyze
```

Exact limits depend on the actual implementation and deployment environment.

---

# 39. API Security

Production API traffic must use HTTPS.

The backend must validate:

* authentication;
* authorization;
* input;
* query parameters;
* resource ownership;
* file type and size where file uploads exist.

The backend must never trust client-provided:

```text
companyId
candidateId
userId
role
permissions
```

where these values can be derived from authenticated context.

---

# 40. API Validation

**Status:** 🟡 Foundation / Partial

NestJS DTO validation should be used for incoming requests.

Examples of future DTOs:

```text
CreateJobDto
UpdateCandidateDto
CreateSkillDto
CreateJobRequirementDto
```

Validation must happen before business logic.

Only DTOs that actually exist in the backend should be considered implemented.

---

# 41. API DTO Strategy

Database entities must not automatically become API responses.

Target flow:

```text
Prisma Entity
       ↓
Response DTO
       ↓
Frontend
```

This prevents accidental exposure of:

* password hashes;
* internal fields;
* private metadata;
* database implementation details.

---

# 42. API Documentation

**Status:** 🔵 Planned / Development Requirement

The backend should expose OpenAPI/Swagger documentation.

Target development endpoint:

```text
/api/docs
```

The exact route depends on the NestJS configuration.

OpenAPI should eventually become the primary machine-readable API contract for frontend development.

---

# 43. Frontend API Client

**Status:** 🟡 Foundation / Partial

The frontend should communicate through a centralized API layer.

Target structure:

```text
lib/api/

├── client.ts
├── auth.ts
├── jobs.ts
├── candidates.ts
├── skills.ts
└── matches.ts
```

Components should not duplicate raw API logic throughout the application.

---

# 44. API Error Handling in Frontend

The frontend should map backend errors to appropriate UI states.

| HTTP | Frontend behavior        |
| ---- | ------------------------ |
| 401  | Login/session handling   |
| 403  | Permission message       |
| 404  | Not found                |
| 422  | Form validation          |
| 429  | Retry/rate-limit message |
| 500  | Generic server error     |

The exact implementation depends on the current frontend API client.

---

# 45. API Evolution

Non-breaking changes may be introduced within `/v1`.

Breaking changes require a new API version.

Example:

```text
/api/v1
/api/v2
```

The MVP should avoid unnecessary breaking changes.

---

# 46. Current API Scope

## Implemented / Foundation

* Authentication
* Users
* Skills
* Initial Candidate/profile functionality

## Partial / Foundation

* Role-based access foundation
* Candidate domain
* API validation
* Frontend/backend API communication

## Planned

* Companies
* Recruiters
* Candidate Skills
* Candidate Discovery
* Jobs
* Job Requirements
* Matching
* Match explanations
* CV processing
* AI CV analysis
* AI Job analysis

The exact implementation status must always be verified against the backend source of truth.

---

# 47. Target MVP API Scope

The target MVP API is intended to support:

```text
Authentication
        ↓
Users
        ↓
Candidates
        ↓
Candidate Skills
        ↓
Companies
        ↓
Recruiters
        ↓
Jobs
        ↓
Job Requirements
        ↓
Matching
        ↓
CV / AI processing
```

This is the target architecture, not a statement that every component is currently implemented.

---

# 48. API Implementation Order

The target implementation order is:

1. Health check
2. Database connection
3. Authentication
4. Users
5. Companies
6. Recruiters
7. Candidates
8. Skills
9. Candidate Skills
10. Jobs
11. Job Requirements
12. Matching
13. CV processing
14. AI integration

Actual implementation order may differ where existing code already provides functionality.

---

# 49. Health Check

**Status:** 🟡 Verify against implementation

Target:

```text
GET /api/v1/health
```

Example:

```json
{
  "status": "ok"
}
```

A health endpoint should eventually support deployment and monitoring.

The exact route must match the backend implementation.

---

# 50. API Definition of Done

An endpoint is considered implemented when:

* request schema is defined;
* authentication requirements are defined;
* authorization requirements are defined;
* validation is implemented;
* response schema is defined;
* error behavior is defined;
* automated tests exist;
* OpenAPI documentation exists where appropriate.

Documentation alone does not make an endpoint implemented.

---

# 51. Source of Truth

For API implementation, the following hierarchy applies:

```text
1. NestJS controllers
        ↓
2. DTOs
        ↓
3. Services / Guards
        ↓
4. Automated tests
        ↓
5. OpenAPI/Swagger
        ↓
6. api.md
```

If `api.md` differs from the actual backend implementation, the discrepancy must be identified and corrected.

The documentation must not describe roadmap functionality as implemented functionality.

For the canonical target contract, intentional architectural decisions documented through the project change process may define the desired future API shape. Such target definitions must remain clearly distinguished from the current backend implementation.

---

# 52. Document Status

**Document:** api.md
**Version:** 0.2.0
**Status:** Canonical API Contract Baseline
**Last updated:** 2026-08-28

This document distinguishes the current API implementation from the canonical target API architecture.

The actual NestJS implementation remains the source of truth for current behavior.

The canonical target contract defines the intended API direction for future refactoring and must not be interpreted as evidence that a planned endpoint is already implemented.
