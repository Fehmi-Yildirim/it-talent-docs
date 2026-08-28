# IT Talent Platform — API Specification

**Document:** `api.md`
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
* planned endpoints belonging to the target platform architecture.

The actual NestJS controllers, DTOs, guards and services are the implementation source of truth.

---

# 2. API Implementation Status

The following status model is used throughout this document.

| Status                  | Meaning                                                                           |
| ----------------------- | --------------------------------------------------------------------------------- |
| ✅ Implemented           | Endpoint currently exists and is part of the active backend implementation        |
| 🟡 Foundation / Partial | Related functionality exists, but the complete endpoint/domain is not implemented |
| 🔵 Planned / Roadmap    | Target architecture only                                                          |

The presence of an endpoint in this document does not mean that the endpoint currently exists.

## 2.1 Contract Status Rules

This document distinguishes strictly between the current API contract and the target architecture.

The following rules apply:

* An endpoint marked `✅ Implemented` must exist in the current backend implementation.
* An endpoint marked `🟡 Foundation / Partial` must have identifiable backend functionality, but may not yet represent a complete domain contract.
* An endpoint marked `🔵 Planned / Roadmap` must not be treated as an implemented API.
* Example request and response payloads are illustrative unless explicitly identified as the implemented DTO/schema.
* When this document differs from the backend implementation, the discrepancy must be recorded and resolved rather than silently treating the documented behavior as implemented.
* Target API changes must not be considered canonical until they have been explicitly reviewed and accepted.

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

Example:

```http
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

* `CANDIDATE`
* `RECRUITER`
* `ADMIN`

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

```http
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

```http
GET /api/v1/jobs?sort=createdAt&order=desc
```

Only explicitly supported fields may be used.

---

# 13. Filtering

**Status:** 🔵 Planned

Collection endpoints may support filtering.

Example:

```http
GET /api/v1/jobs?status=PUBLISHED&workMode=REMOTE
```

Future candidate discovery may support:

```http
GET /api/v1/candidates?skill=react&location=Amsterdam
```

Advanced filtering is roadmap functionality.

---

# 14. Authentication Endpoints

## Register

**Status:** ✅ Implemented / Verify against current controller contract

Target:

```http
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

---

# 15. Login

**Status:** ✅ Implemented / Verify against current controller contract

Target:

```http
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

# 16. Current User

**Status:** 🟡 Foundation / Partial

Target:

```http
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

# 17. Candidate Profile

**Status:** 🟡 Foundation / Partial

Target endpoints:

```http
GET /api/v1/candidates/me
POST /api/v1/candidates
PATCH /api/v1/candidates/me
```

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

---

# 18. Candidate Skills

**Status:** 🔵 Planned / Roadmap

Target endpoints:

```http
GET /api/v1/candidates/me/skills
POST /api/v1/candidates/me/skills
PATCH /api/v1/candidates/me/skills/:id
DELETE /api/v1/candidates/me/skills/:id
```

Example:

```json
{
  "skillId": "...",
  "proficiencyLevel": 4,
  "yearsOfExperience": 5
}
```

The complete CandidateSkill relationship is not currently part of the implemented API foundation.

---

# 19. Candidate Discovery

**Status:** 🔵 Planned / Roadmap

Target:

```http
GET /api/v1/candidates
```

Potential filters:

* skill
* location
* experience
* page
* limit

Example:

```http
GET /api/v1/candidates?skill=react&location=Amsterdam&page=1&limit=20
```

Candidate discovery requires recruiter/company authorization and privacy controls.

---

# 20. Candidate Detail

**Status:** 🔵 Planned / Roadmap

Target:

```http
GET /api/v1/candidates/:id
```

The returned information must depend on the authenticated user's permissions.

Private candidate information must not automatically be exposed.

---

# 21. Company

**Status:** 🔵 Planned / Roadmap

Target endpoints:

```http
GET /api/v1/companies/:id
PATCH /api/v1/companies/me
```

Company authorization will be required.

Company functionality is not currently part of the implemented MVP foundation.

---

# 22. Recruiter Profile

**Status:** 🔵 Planned / Roadmap

Target:

```http
GET /api/v1/recruiters/me
PATCH /api/v1/recruiters/me
```

Recruiter functionality depends on the Company/Recruiter domain implementation.

---

# 23. Jobs

**Status:** 🔵 Planned / Roadmap

Target:

```http
POST /api/v1/jobs
GET /api/v1/jobs
GET /api/v1/jobs/:id
PATCH /api/v1/jobs/:id
```

Example:

```json
{
  "title": "Senior React Developer",
  "description": "We are looking for...",
  "location": "Amsterdam",
  "workMode": "HYBRID",
  "employmentType": "FULL_TIME",
  "salaryMin": 5500,
  "salaryMax": 6500,
  "currency": "EUR"
}
```

The Jobs domain depends on Company and Recruiter relationships.

---

# 24. Job Publishing

**Status:** 🔵 Planned / Roadmap

Target:

```http
POST /api/v1/jobs/:id/publish
```

The backend should validate required information before publishing.

---

# 25. Job Closing

**Status:** 🔵 Planned / Roadmap

Target:

```http
POST /api/v1/jobs/:id/close
```

Closed jobs should not appear in normal active job discovery.

---

# 26. Job Requirements

**Status:** 🔵 Planned / Roadmap

Target:

```http
GET /api/v1/jobs/:id/requirements
POST /api/v1/jobs/:id/requirements
PATCH /api/v1/jobs/:jobId/requirements/:requirementId
```

Example:

```json
{
  "skillId": "...",
  "minimumLevel": 4,
  "required": true,
  "weight": 0.3
}
```

This functionality depends on the JobRequirement database model.

---

# 27. Skills

**Status:** ✅ Implemented

The Skills domain is part of the current backend foundation.

Target endpoint:

```http
GET /api/v1/skills
```

Potential search:

```http
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

# 28. Skill Creation

**Status:** 🟡 Foundation / Partial

Target:

```http
POST /api/v1/skills
```

Skill creation should be restricted to authorized platform functionality.

Candidates should not be able to arbitrarily create global platform skills.

The exact authorization rules must match the current implementation.

---

# 29. Matching

**Status:** 🔵 Planned / Roadmap

Matching is a core future domain.

Target:

```http
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

# 30. Job Matches

**Status:** 🔵 Planned / Roadmap

Target:

```http
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

# 31. Match Detail

**Status:** 🔵 Planned / Roadmap

Target:

```http
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

# 32. Match Explanation

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

# 33. CV Upload

**Status:** 🔵 Planned / Roadmap

Potential future endpoint:

```http
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

# 34. AI Processing

**Status:** 🔵 Planned / Roadmap

AI providers should not be exposed directly to the frontend.

Instead, the backend should expose domain-level operations.

Example:

```http
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

# 35. AI Job Analysis

**Status:** 🔵 Planned / Roadmap

Potential endpoint:

```http
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

# 36. Idempotency

**Status:** 🔵 Planned

Idempotency may eventually be required for retry-sensitive operations such as:

* AI processing;
* document processing;
* bulk matching;
* payments.

It should be introduced when a concrete operation requires it.

---

# 37. Rate Limiting

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

# 38. API Security

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

# 39. API Validation

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

# 40. API DTO Strategy

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

# 41. API Documentation

**Status:** 🔵 Planned / Development Requirement

The backend should expose OpenAPI/Swagger documentation.

Target development endpoint:

```text
/api/docs
```

The exact route depends on the NestJS configuration.

OpenAPI should eventually become the primary machine-readable API contract for frontend development.

---

# 42. Frontend API Client

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

# 43. API Error Handling in Frontend

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

# 44. API Evolution

Non-breaking changes may be introduced within `/v1`.

Breaking changes require a new API version.

Example:

```text
/api/v1
/api/v2
```

The MVP should avoid unnecessary breaking changes.

---

# 45. Current API Scope

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

---

# 46. Target MVP API Scope

The target MVP API is intended to eventually support:

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

# 47. API Implementation Order

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

# 48. Health Check

**Status:** 🟡 Verify against implementation

Target:

```http
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

# 49. API Definition of Done

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

# 50. API Contract and Source of Truth

The API contract is the shared interface between the backend, frontend, and documentation.

The following sources must be kept aligned:

1. Backend implementation

   * Controllers
   * DTOs
   * Guards
   * Services

2. Automated tests

   * Unit tests
   * Integration tests
   * Contract tests
   * End-to-end tests

3. OpenAPI specification

4. This document (`architecture/api.md`)

No single implementation artifact is sufficient by itself to define the complete API contract.

If a discrepancy is found between the documented contract and the implemented API, the discrepancy must be explicitly identified and resolved.

The accepted API contract must be reflected consistently in:

```text
Backend
    ↓
OpenAPI
    ↓
Frontend
    ↓
Documentation
    ↓
Automated contract/E2E tests
```

---

# 51. Current API Inventory

This section records the API endpoints that are currently part of the backend implementation.

The inventory is intentionally limited to endpoints that can be verified against the current backend implementation.

## Authentication

| Method | Endpoint                | Status        |
| ------ | ----------------------- | ------------- |
| POST   | `/api/v1/auth/register` | ✅ Implemented |
| POST   | `/api/v1/auth/login`    | ✅ Implemented |

## Users

| Method | Endpoint                     | Status                  |
| ------ | ---------------------------- | ----------------------- |
| GET    | `/api/v1/users/me`           | 🟡 Foundation / Partial |
| GET    | `/api/v1/users/me/candidate` | 🟡 Foundation / Partial |
| POST   | `/api/v1/users/me/candidate` | 🟡 Foundation / Partial |
| POST   | `/api/v1/users`              | 🟡 Foundation / Partial |
| GET    | `/api/v1/users`              | 🟡 Foundation / Partial |
| GET    | `/api/v1/users/:id`          | 🟡 Foundation / Partial |
| PATCH  | `/api/v1/users/:id`          | 🟡 Foundation / Partial |
| DELETE | `/api/v1/users/:id`          | 🟡 Foundation / Partial |

## Candidate Skills

| Method | Endpoint                           | Status                  |
| ------ | ---------------------------------- | ----------------------- |
| GET    | `/api/v1/users/me/skills`          | 🟡 Foundation / Partial |
| POST   | `/api/v1/users/me/skills`          | 🟡 Foundation / Partial |
| PATCH  | `/api/v1/users/me/skills/:skillId` | 🟡 Foundation / Partial |
| DELETE | `/api/v1/users/me/skills/:skillId` | 🟡 Foundation / Partial |

## Skills

| Method | Endpoint             | Status                  |
| ------ | -------------------- | ----------------------- |
| GET    | `/api/v1/skills`     | ✅ Implemented           |
| POST   | `/api/v1/skills`     | 🟡 Foundation / Partial |
| GET    | `/api/v1/skills/:id` | 🟡 Foundation / Partial |
| PATCH  | `/api/v1/skills/:id` | 🟡 Foundation / Partial |
| DELETE | `/api/v1/skills/:id` | 🟡 Foundation / Partial |

> This inventory is a contract-review baseline. Individual endpoint status must be verified against the backend implementation before the endpoint is considered fully compliant with the canonical contract.

---

# 52. Known Contract Gaps

The following areas require verification during the API refactor:

* Current user/candidate resource boundaries
* Candidate and CandidateSkill endpoint structure
* Request and response DTO consistency
* Authentication and authorization behavior
* Ownership enforcement
* HTTP status code consistency
* Error response consistency
* Pagination conventions
* Skills CRUD semantics
* OpenAPI coverage
* Frontend API client alignment
* Database-to-domain-to-API mapping

These gaps are not automatically considered defects until the current backend implementation, database model, frontend usage, and documented contract have been compared.

---

# 53. API Refactoring Workflow

API refactoring follows a contract-driven workflow.

```text
Current implementation
        ↓
Current API inventory
        ↓
Contract gap identification
        ↓
Target contract decision
        ↓
Backend / Frontend / Documentation implementation
        ↓
Contract tests
        ↓
Verification
        ↓
Legacy cleanup
```

Work is performed incrementally.

A single ticket should have a clearly defined scope and acceptance criteria.

Future frontend and backend tickets may be executed independently once the relevant contract has been agreed upon.

No implementation ticket should introduce undocumented API behavior.

---

# 54. Related Architecture Documentation

This API contract is part of the wider IT Talent Platform architecture.

Related documentation includes:

* `architecture/architecture.md`
* `architecture/database.md`
* `architecture/security.md`

These documents describe complementary architectural concerns.

They must remain consistent with this API contract where their content overlaps.

The order in which these documents are reviewed or updated is determined by the active project ticket and is not part of the API contract.

---

# 55. Document Status

**Document:** `architecture/api.md`

**Version:** 0.2.0

**Status:** Canonical API Contract Baseline

**Last updated:** 2026-08-28

This document establishes the canonical baseline for API contract review.

It distinguishes between:

* currently implemented API behavior;
* partially implemented functionality;
* planned target architecture.

The document must be kept aligned with the backend implementation, OpenAPI specification, frontend API usage, database/domain model, and automated contract tests.

Changes to the API contract must be traceable to an approved project ticket.
