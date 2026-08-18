# IT Talent Platform — API Specification

**Document:** api.md  
**Version:** 0.1.1  
**Status:** Draft / API Architecture Baseline  
**Last updated:** 2026-08-18

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

The frontend must not directly access PostgreSQL.

This document distinguishes between:

endpoints currently implemented;
endpoints that are partially implemented;
planned endpoints belonging to the target platform architecture.

The actual NestJS controllers, DTOs, guards and services are the implementation source of truth.

2. API Implementation Status

The following status model is used throughout this document.

Status	Meaning
✅ Implemented	Endpoint currently exists and is part of the active backend implementation
🟡 Foundation / Partial	Related functionality exists, but the complete endpoint/domain is not implemented
🔵 Planned / Roadmap	Target architecture only

The presence of an endpoint in this document does not mean that the endpoint currently exists.

3. API Principles

The API follows these architectural principles:

REST-oriented resources;
JSON request and response bodies;
HTTPS in deployed environments;
versioned API paths;
explicit validation;
predictable HTTP status codes;
authentication where required;
backend-enforced authorization;
DTO-based request validation;
domain-level API resources;
pagination for large collections where required;
filtering and sorting where appropriate.

The API must remain independent from the internal Prisma/database structure.

4. Base URL
Local development

The intended local API base path is:

http://localhost:3001/api/v1

The exact local port and prefix must match the backend configuration.

Production

The production API will eventually use a dedicated backend domain:

https://<backend-domain>/api/v1

The production domain is not fixed in this document.

5. API Versioning

Status: 🟡 Architecture / Foundation

The target API version is:

/api/v1

Example:

GET /api/v1/jobs
GET /api/v1/candidates

Breaking API changes should result in a new major API version.

The actual global prefix must match the NestJS application configuration.

6. Content Type

Standard API requests and responses use JSON.

Content-Type: application/json

Future file-upload functionality may use:

multipart/form-data

or signed object-storage uploads.

CV upload is currently roadmap functionality.

7. Authentication

Status: 🟡 Foundation / Partial

Authentication is handled by the backend.

Conceptually:

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

Authentication must never rely exclusively on frontend restrictions.

The exact token/session transport mechanism must match the current backend implementation.

8. Roles

Status: 🟡 Foundation / Partial

The target platform roles are:

CANDIDATE
RECRUITER
ADMIN

Authorization must be enforced by the backend.

Not every role is necessarily fully implemented yet.

9. HTTP Status Codes

The target API uses conventional HTTP status codes.

Status	Meaning
200	Successful request
201	Resource created
204	Successful request with no body
400	Invalid request
401	Authentication required/invalid
403	Insufficient permissions
404	Resource not found
409	Resource conflict
422	Validation error
429	Rate limit exceeded
500	Internal server error
503	Service unavailable

The actual status code returned by the current backend is authoritative.

10. Standard Error Format

Status: 🟡 Foundation / Partial

The target API should use a consistent error structure.

Example:

{
  "statusCode": 400,
  "code": "INVALID_REQUEST",
  "message": "The request could not be processed.",
  "details": []
}

Validation example:

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

The exact error structure must follow the current NestJS exception/filter implementation.

11. Pagination

Status: 🔵 Planned / Endpoint-dependent

Collection endpoints should support pagination where required.

Example:

GET /api/v1/jobs?page=1&limit=20

Target response:

{
  "data": [],
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 120,
    "totalPages": 6
  }
}

The backend should enforce a maximum limit.

For example:

limit <= 100

Pagination should only be documented for endpoints that actually implement it.

12. Sorting

Status: 🔵 Planned

Collection endpoints may support sorting.

Example:

GET /api/v1/jobs?sort=createdAt&order=desc

Only explicitly supported fields may be used.

13. Filtering

Status: 🔵 Planned

Collection endpoints may support filtering.

Example:

GET /api/v1/jobs?status=PUBLISHED&workMode=REMOTE

Future candidate discovery may support:

GET /api/v1/candidates?skill=react&location=Amsterdam

Advanced filtering is roadmap functionality.

14. Authentication Endpoints
Register

Status: ✅ Implemented / Verify against current controller contract

Target:

POST /api/v1/auth/register

Request:

{
  "email": "user@example.com",
  "password": "secure-password",
  "role": "CANDIDATE"
}

The exact request and response schema must match the implemented DTO.

15. Login

Status: ✅ Implemented / Verify against current controller contract

Target:

POST /api/v1/auth/login

Request:

{
  "email": "user@example.com",
  "password": "secure-password"
}

The backend creates the authenticated state according to the implemented authentication mechanism.

The documentation must not assume JWT/cookie/refresh-token behavior unless it exists in the implementation.

16. Current User

Status: 🟡 Foundation / Partial

Target:

GET /api/v1/users/me

Returns information belonging to the authenticated user.

Example:

{
  "id": "...",
  "email": "...",
  "role": "CANDIDATE"
}

The exact response must match the current backend DTO.

17. Candidate Profile

Status: 🟡 Foundation / Partial

Target endpoints:

GET /api/v1/candidates/me
POST /api/v1/candidates
PATCH /api/v1/candidates/me

Example profile payload:

{
  "headline": "Senior React Developer",
  "summary": "Software engineer with 6 years of experience.",
  "location": "Amsterdam",
  "salaryMin": 5500,
  "salaryMax": 6500,
  "currency": "EUR",
  "remotePreference": "HYBRID"
}

Not every field in this example is necessarily implemented.

The actual DTO/schema is authoritative.

18. Candidate Skills

Status: 🔵 Planned / Roadmap

Target endpoints:

GET /api/v1/candidates/me/skills
POST /api/v1/candidates/me/skills
PATCH /api/v1/candidates/me/skills/:id
DELETE /api/v1/candidates/me/skills/:id

Example:

{
  "skillId": "...",
  "proficiencyLevel": 4,
  "yearsOfExperience": 5
}

The complete CandidateSkill relationship is not currently part of the implemented API foundation.

19. Candidate Discovery

Status: 🔵 Planned / Roadmap

Target:

GET /api/v1/candidates

Potential filters:

skill
location
experience
page
limit

Example:

GET /api/v1/candidates
  ?skill=react
  &location=Amsterdam
  &page=1
  &limit=20

Candidate discovery requires recruiter/company authorization and privacy controls.

20. Candidate Detail

Status: 🔵 Planned / Roadmap

Target:

GET /api/v1/candidates/:id

The returned information must depend on the authenticated user's permissions.

Private candidate information must not automatically be exposed.

21. Company

Status: 🔵 Planned / Roadmap

Target endpoints:

GET /api/v1/companies/:id
PATCH /api/v1/companies/me

Company authorization will be required.

Company functionality is not currently part of the implemented MVP foundation.

22. Recruiter Profile

Status: 🔵 Planned / Roadmap

Target:

GET /api/v1/recruiters/me
PATCH /api/v1/recruiters/me

Recruiter functionality depends on the Company/Recruiter domain implementation.

23. Jobs

Status: 🔵 Planned / Roadmap

Target:

POST /api/v1/jobs
GET /api/v1/jobs
GET /api/v1/jobs/:id
PATCH /api/v1/jobs/:id

Example:

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

The Jobs domain depends on Company and Recruiter relationships.

24. Job Publishing

Status: 🔵 Planned / Roadmap

Target:

POST /api/v1/jobs/:id/publish

The backend should validate required information before publishing.

25. Job Closing

Status: 🔵 Planned / Roadmap

Target:

POST /api/v1/jobs/:id/close

Closed jobs should not appear in normal active job discovery.

26. Job Requirements

Status: 🔵 Planned / Roadmap

Target:

GET /api/v1/jobs/:id/requirements
POST /api/v1/jobs/:id/requirements
PATCH /api/v1/jobs/:jobId/requirements/:requirementId

Example:

{
  "skillId": "...",
  "minimumLevel": 4,
  "required": true,
  "weight": 0.3
}

This functionality depends on the JobRequirement database model.

27. Skills

Status: ✅ Implemented

The Skills domain is part of the current backend foundation.

Target endpoint:

GET /api/v1/skills

Potential search:

GET /api/v1/skills?search=react

Example response:

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

The actual endpoint path, query parameters and response structure must match the implemented controller.

28. Skill Creation

Status: 🟡 Foundation / Partial

Target:

POST /api/v1/skills

Skill creation should be restricted to authorized platform functionality.

Candidates should not be able to arbitrarily create global platform skills.

The exact authorization rules must match the current implementation.

29. Matching

Status: 🔵 Planned / Roadmap

Matching is a core future domain.

Target:

POST /api/v1/jobs/:id/match

Conceptually:

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

The matching engine is not currently considered implemented merely because this endpoint is documented.

30. Job Matches

Status: 🔵 Planned / Roadmap

Target:

GET /api/v1/jobs/:id/matches

Potential query:

?minScore=70&page=1&limit=20

Target response:

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
31. Match Detail

Status: 🔵 Planned / Roadmap

Target:

GET /api/v1/matches/:id

The response should eventually contain:

overall score;
component scores;
strengths;
gaps;
relevant candidate information;
relevant job information.
32. Match Explanation

Status: 🔵 Planned / Roadmap

Target structure:

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

The explanation must be generated from actual matching data.

AI may assist with natural-language presentation, but must not invent match reasons.

33. CV Upload

Status: 🔵 Planned / Roadmap

Potential future endpoint:

POST /api/v1/candidates/me/documents

Possible upload mechanism:

multipart/form-data

or:

Frontend
   ↓
Backend requests upload URL
   ↓
Object Storage
   ↓
Backend receives metadata
   ↓
AI processing

No CV upload API should be considered implemented until the corresponding backend functionality exists.

34. AI Processing

Status: 🔵 Planned / Roadmap

AI providers should not be exposed directly to the frontend.

Instead, the backend should expose domain-level operations.

Example:

POST /api/v1/candidates/me/analyze-cv

Conceptually:

CV
 ↓
Candidate Processing Service
 ↓
AI Provider
 ↓
Structured CandidateSkill

AI credentials remain backend-only.

35. AI Job Analysis

Status: 🔵 Planned / Roadmap

Potential endpoint:

POST /api/v1/jobs/:id/analyze

The backend may:

read the job description;
send appropriate content to the AI provider;
extract skills;
normalize skills;
create/update JobRequirements;
return structured results.

This functionality is not currently part of the implemented API.

36. Idempotency

Status: 🔵 Planned

Idempotency may eventually be required for retry-sensitive operations such as:

AI processing;
document processing;
bulk matching;
payments.

It should be introduced when a concrete operation requires it.

37. Rate Limiting

Status: 🔵 Security / Implementation Requirement

The API should implement rate limiting.

Particularly sensitive endpoints include:

POST /auth/login
POST /auth/register
POST /jobs/:id/match
POST /candidates/me/analyze-cv
POST /jobs/:id/analyze

Exact limits depend on the actual implementation and deployment environment.

38. API Security

Production API traffic must use HTTPS.

The backend must validate:

authentication;
authorization;
input;
query parameters;
resource ownership;
file type and size where file uploads exist.

The backend must never trust client-provided:

companyId
candidateId
userId
role
permissions

where these values can be derived from authenticated context.

39. API Validation

Status: 🟡 Foundation / Partial

NestJS DTO validation should be used for incoming requests.

Examples of future DTOs:

CreateJobDto
UpdateCandidateDto
CreateSkillDto
CreateJobRequirementDto

Validation must happen before business logic.

Only DTOs that actually exist in the backend should be considered implemented.

40. API DTO Strategy

Database entities must not automatically become API responses.

Target flow:

Prisma Entity
       ↓
Response DTO
       ↓
Frontend

This prevents accidental exposure of:

password hashes;
internal fields;
private metadata;
database implementation details.
41. API Documentation

Status: 🔵 Planned / Development Requirement

The backend should expose OpenAPI/Swagger documentation.

Target development endpoint:

/api/docs

The exact route depends on the NestJS configuration.

OpenAPI should eventually become the primary machine-readable API contract for frontend development.

42. Frontend API Client

Status: 🟡 Foundation / Partial

The frontend should communicate through a centralized API layer.

Target structure:

lib/api/
├── client.ts
├── auth.ts
├── jobs.ts
├── candidates.ts
├── skills.ts
└── matches.ts

Components should not duplicate raw API logic throughout the application.

43. API Error Handling in Frontend

The frontend should map backend errors to appropriate UI states.

HTTP	Frontend behavior
401	Login/session handling
403	Permission message
404	Not found
422	Form validation
429	Retry/rate-limit message
500	Generic server error

The exact implementation depends on the current frontend API client.

44. API Evolution

Non-breaking changes may be introduced within /v1.

Breaking changes require a new API version.

Example:

/api/v1
/api/v2

The MVP should avoid unnecessary breaking changes.

45. Current API Scope
Implemented / Foundation
Authentication
Users
Skills
Initial Candidate/profile functionality
Partial / Foundation
Role-based access foundation
Candidate domain
API validation
Frontend/backend API communication
Planned
Companies
Recruiters
Candidate Skills
Candidate Discovery
Jobs
Job Requirements
Matching
Match explanations
CV processing
AI CV analysis
AI Job analysis
46. Target MVP API Scope

The target MVP API is intended to eventually support:

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

This is the target architecture, not a statement that every component is currently implemented.

47. API Implementation Order

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

48. Health Check

Status: 🟡 Verify against implementation

Target:

GET /api/v1/health

Example:

{
  "status": "ok"
}

A health endpoint should eventually support deployment and monitoring.

The exact route must match the backend implementation.

49. API Definition of Done

An endpoint is considered implemented when:

request schema is defined;
authentication requirements are defined;
authorization requirements are defined;
validation is implemented;
response schema is defined;
error behavior is defined;
automated tests exist;
OpenAPI documentation exists where appropriate.

Documentation alone does not make an endpoint implemented.

50. Source of Truth

For API implementation, the following hierarchy applies:

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

If api.md differs from the actual backend implementation, the discrepancy must be identified and corrected.

The documentation must not describe roadmap functionality as implemented functionality.

51. Next Step

The documentation structure is now:

it-talent-docs/
│
├── README.md
│
├── architecture/
│   ├── architecture.md
│   ├── database.md
│   ├── api.md
│   └── security.md          ← NEXT
│
├── product/
│   ├── vision.md
│   ├── requirements.md
│   └── roadmap.md
│
└── decisions/
    └── README.md

The next document to review is:

architecture/security.md

The same rule applies:

✅ Implemented
🟡 Foundation / Partial
🔵 Planned / Roadmap
52. Document Status

Document: api.md
Version: 0.1.1
Status: Draft / API Architecture Baseline
Last updated: 2026-08-18

This document describes the current API foundation together with the target API architecture.

The actual NestJS implementation remains the source of truth.