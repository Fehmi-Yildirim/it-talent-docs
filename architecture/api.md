IT Talent Platform — API Specification

Document: api.md
Version: 0.1.0
Status: Draft / MVP API Specification
Last updated: 2026-08-12

1. Purpose

This document defines the REST API contract for the IT Talent Platform.

The API provides communication between:

it-talent-frontend
        │
        │ HTTPS / JSON
        ▼
it-talent-backend
        │
        ├── PostgreSQL
        ├── Matching Engine
        └── AI Services

The API is the formal boundary between frontend and backend.

The frontend must not directly access the database.

2. API Principles

The API follows these principles:

RESTful resources;
JSON request and response bodies;
HTTPS in deployed environments;
versioned endpoints;
consistent HTTP status codes;
explicit validation;
predictable error responses;
authentication where required;
role-based authorization;
pagination for collections;
filtering and sorting where appropriate.

3. Base URL

The production API will eventually use a dedicated domain.

For development:

http://localhost:3001/api/v1

For Vercel:
https://<backend-domain>/api/v1

The actual production domain will be decided later.

4. API Versioning

The initial API version is:

/api/v1

Examples:
GET /api/v1/jobs
GET /api/v1/candidates
POST /api/v1/jobs/:id/match

Breaking API changes should result in a new major API version.

5. Content Type

Requests and responses use JSON unless explicitly documented otherwise.

Request:

Content-Type: application/json

Response:

Content-Type: application/json

File uploads such as CVs will use:

multipart/form-data

or a signed object-storage upload mechanism.

6. Authentication

Authenticated endpoints require a valid authentication mechanism.
The exact token transport strategy will be finalized during implementation.

Conceptually:

Client
  │
  ├── login
  ▼
Backend
  │
  └── authenticated session/token
        │
        ▼
Protected API

Authentication must never be implemented only in the frontend.

7. Roles

Initial roles:

CANDIDATE
RECRUITER
ADMIN

Role authorization is enforced by the backend.

8. HTTP Status Codes

The API will use conventional HTTP status codes.

Status	Meaning
200	Successful request
201	Resource created
204	Successful request with no response body
400	Invalid request
401	Authentication required/invalid
403	Insufficient permissions
404	Resource not found
409	Resource conflict
422	Validation error
429	Rate limit exceeded
500	Internal server error
503	Service temporarily unavailable
9. Standard Error Format

Errors should have a consistent structure.

Example:

{
  "statusCode": 400,
  "code": "INVALID_REQUEST",
  "message": "The request could not be processed.",
  "details": []
}

Validation error:

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

The exact error schema may be refined during backend implementation.

10. Pagination

Collection endpoints should support pagination.

Example:

GET /api/v1/jobs?page=1&limit=20

Response:

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
11. Sorting

Collection endpoints may support sorting.

Example:

GET /api/v1/jobs?sort=createdAt&order=desc

Only explicitly supported fields may be used for sorting.

12. Filtering

Filtering should use query parameters.

Example:

GET /api/v1/jobs?status=PUBLISHED&workMode=REMOTE

Candidate search may eventually support:

GET /api/v1/candidates?skill=react&location=Amsterdam

Advanced filtering will be implemented incrementally.

13. Authentication Endpoints
Register
POST /api/v1/auth/register

Request:

{
  "email": "user@example.com",
  "password": "secure-password",
  "role": "CANDIDATE"
}

Response:

{
  "user": {
    "id": "...",
    "email": "user@example.com",
    "role": "CANDIDATE"
  }
}

The exact authentication response will depend on the final session/token implementation.

14. Login
POST /api/v1/auth/login

Request:

{
  "email": "user@example.com",
  "password": "secure-password"
}

Successful authentication creates an authenticated session.

15. Current User
GET /api/v1/users/me

Returns the authenticated user's basic account information and applicable profile reference.

Example:

{
  "id": "...",
  "email": "user@example.com",
  "role": "RECRUITER"
}
16. Candidate Profile
Get own candidate profile
GET /api/v1/candidates/me
Create profile
POST /api/v1/candidates
Update profile
PATCH /api/v1/candidates/me

Example:

{
  "headline": "Senior React Developer",
  "summary": "Software engineer with 6 years of experience.",
  "location": "Amsterdam",
  "salaryMin": 5500,
  "salaryMax": 6500,
  "currency": "EUR",
  "remotePreference": "HYBRID"
}
17. Candidate Skills
List candidate skills
GET /api/v1/candidates/me/skills
Add skill
POST /api/v1/candidates/me/skills

Example:

{
  "skillId": "...",
  "proficiencyLevel": 4,
  "yearsOfExperience": 5
}
Update skill
PATCH /api/v1/candidates/me/skills/:id
Remove skill
DELETE /api/v1/candidates/me/skills/:id
18. Candidate Discovery

Recruiters can search candidates subject to authorization rules.

GET /api/v1/candidates

Example:

GET /api/v1/candidates
  ?skill=react
  &location=Amsterdam
  &minExperience=3
  &page=1
  &limit=20

The backend must ensure that private candidate information is not exposed.

19. Candidate Detail
GET /api/v1/candidates/:id

The response depends on the authenticated user's permissions.

A candidate must not automatically be fully visible to every recruiter.

20. Company
Get company
GET /api/v1/companies/:id
Update own company
PATCH /api/v1/companies/me

Only authorized company users may modify company information.

21. Recruiter Profile
Get recruiter profile
GET /api/v1/recruiters/me
Update recruiter profile
PATCH /api/v1/recruiters/me
22. Jobs
Create Job

Recruiter only.

POST /api/v1/jobs

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
23. List Jobs
GET /api/v1/jobs

Possible query parameters:

status
companyId
workMode
employmentType
location
page
limit
sort
order

Public job discovery will expose only appropriate fields.

24. Get Job
GET /api/v1/jobs/:id
25. Update Job

Recruiter/company authorization required.

PATCH /api/v1/jobs/:id
26. Publish Job
POST /api/v1/jobs/:id/publish

The backend should validate the job before publishing.

For example:

title required;
description required;
company required;
minimum required data present.
27. Close Job
POST /api/v1/jobs/:id/close

Closed jobs should not appear in normal active job discovery.

28. Job Requirements
List requirements
GET /api/v1/jobs/:id/requirements
Add requirement
POST /api/v1/jobs/:id/requirements

Example:

{
  "skillId": "...",
  "minimumLevel": 4,
  "required": true,
  "weight": 0.3
}
Update requirement
PATCH /api/v1/jobs/:jobId/requirements/:requirementId
29. Skills
Search skills
GET /api/v1/skills

Example:

GET /api/v1/skills?search=react

Response:

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
30. Skill Creation

Skill creation should normally be restricted to the platform/admin or controlled AI normalization process.

POST /api/v1/skills

Regular candidates should not be able to arbitrarily create platform-wide skills.

31. Matching

Matching is one of the central API domains.

Calculate matches for job
POST /api/v1/jobs/:id/match

This requests matching between the job and eligible candidates.

The backend performs:

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
32. Get Job Matches
GET /api/v1/jobs/:id/matches

Example:

GET /api/v1/jobs/:id/matches
  ?minScore=70
  &page=1
  &limit=20

Response:

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
33. Get Match Detail
GET /api/v1/matches/:id

The response should contain:

overall score;
component scores;
strong matches;
gaps;
relevant candidate information;
relevant job information.
34. Match Explanation

Example:

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

The explanation should be generated from actual matching data.

AI may assist with natural-language presentation, but should not invent match reasons.

35. CV Upload

CV upload will be introduced through a dedicated endpoint.

Possible initial API:

POST /api/v1/candidates/me/documents

The endpoint may accept:

multipart/form-data

However, the preferred production architecture may use signed object-storage uploads:

Frontend
   ↓
Backend requests upload URL
   ↓
Object Storage
   ↓
Backend receives file metadata
   ↓
AI processing

This design will be finalized when CV functionality is implemented.

36. AI Processing

AI endpoints should generally not be publicly exposed as raw provider proxies.

Instead of:

POST /ai/openai

the API should expose domain-level operations.

For example:

POST /api/v1/candidates/me/analyze-cv

or internally:

CV
 ↓
Candidate processing service
 ↓
AI provider
 ↓
Structured CandidateSkill
37. AI Job Analysis

Similarly:

POST /api/v1/jobs/:id/analyze

The backend may:

read the job description;
send appropriate content to the AI service;
extract skills;
normalize skills;
create/update job requirements;
return the structured result.
38. Idempotency

Operations that may be retried should eventually support idempotency.

This is particularly relevant for:

payments;
AI processing;
document processing;
bulk matching.

The MVP may initially implement this only where required.

39. Rate Limiting

The API should implement rate limiting.

Particularly sensitive endpoints:

POST /auth/login
POST /auth/register
POST /jobs/:id/match
POST /candidates/me/analyze-cv
POST /jobs/:id/analyze

Exact limits will be determined during implementation and deployment.

40. API Security

All production API traffic must use HTTPS.

The backend must validate:

authentication;
authorization;
input;
file type;
file size;
query parameters;
resource ownership.

Never trust client-provided:

companyId
candidateId
userId
role
permissions

The backend determines these values from the authenticated context wherever possible.

41. API Validation

NestJS validation should be applied to incoming DTOs.

For example:

CreateJobDto
UpdateCandidateDto
CreateSkillDto
CreateJobRequirementDto

Validation should happen before business logic.

42. API DTO Strategy

Database entities must not automatically become API responses.

We should use DTOs / response models.

For example:

Prisma Candidate
       ↓
CandidateResponseDto
       ↓
Frontend

This prevents accidental exposure of:

password hashes;
internal fields;
private metadata;
database implementation details.
43. API Documentation

The backend should expose OpenAPI/Swagger documentation during development.

Expected development endpoint:

/api/docs

The exact route can be changed later.

OpenAPI should become an important reference for frontend development.

44. Frontend API Client

The frontend should communicate with the backend through a centralized API layer.

Conceptually:

lib/api/
├── client.ts
├── auth.ts
├── jobs.ts
├── candidates.ts
├── skills.ts
└── matches.ts

React components should not contain raw fetch logic everywhere.

Instead:

Component
   ↓
API client
   ↓
REST API
45. API Error Handling in Frontend

The frontend should translate API errors into appropriate UI states.

Example:

401 → login/session handling
403 → permission message
404 → not found
422 → form validation
429 → retry/rate limit message
500 → generic error

Backend error codes should therefore remain stable.

46. API Evolution

Non-breaking changes may be introduced within /v1.

Breaking changes require:

/api/v2

The API must avoid unnecessary breaking changes during MVP development.

47. MVP API Scope

The first implementation should focus on:

Authentication
Users
Candidates
Candidate Skills
Companies
Recruiters
Jobs
Job Requirements
Skills
Matching
CV processing
AI job analysis

The following are postponed:

Applications
Messaging
Notifications
Payments
Subscriptions
Assessments
ATS integrations
48. API Implementation Order

The backend API should be implemented in this order:

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

This order follows the dependency structure of the domain.

49. Health Check

The backend should expose a health endpoint.

GET /api/v1/health

Example:

{
  "status": "ok"
}

Later this can include dependency checks.

50. API Definition of Done

The API architecture is considered implemented for an endpoint when:

request schema is defined;
authentication requirements are defined;
authorization requirements are defined;
validation is implemented;
response schema is defined;
error behavior is defined;
automated tests exist;
OpenAPI documentation exists where appropriate.
51. Next Step

The documentation architecture now contains:

it-talent-docs/
│
├── README.md
│
├── architecture/
│   ├── architecture.md      ✓
│   ├── database.md          ✓
│   ├── api.md               ✓
│   └── security.md          ← NEXT
│
├── product/
│   ├── vision.md
│   ├── requirements.md
│   └── roadmap.md
│
└── decisions/
    └── README.md

Daarmee hebben we nu Architecture → Database → API vastgelegd.