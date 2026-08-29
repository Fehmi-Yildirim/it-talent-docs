# IT Talent API Contract

## API Version

**Version:** `v1`
**Base Path:** `/api/v1`

All public API endpoints use the `/api/v1` base path.

---

# API Structure

The API is organized around domain resources.

```text
/api/v1
│
├── auth
│   ├── POST /register
│   └── POST /login
│
├── users
│   ├── GET    /me
│   ├── POST   /
│   ├── GET    /
│   ├── GET    /:id
│   ├── PATCH  /:id
│   └── DELETE /:id
│
├── candidates
│   ├── GET    /me
│   ├── POST   /
│   ├── PATCH  /me
│   │
│   └── /me/skills
│       ├── GET    /
│       ├── POST   /
│       ├── PATCH  /:id
│       └── DELETE /:id
│
├── skills
│   ├── GET    /
│   ├── POST   /
│   ├── GET    /:id
│   ├── PATCH  /:id
│   └── DELETE /:id
│
├── recruiters
│   ├── GET   /me
│   └── PATCH /me
│
├── companies
│   ├── POST  /
│   ├── GET   /me
│   └── PATCH /me
│
├── jobs
│   ├── POST   /
│   ├── GET    /
│   ├── GET    /:id
│   ├── PATCH  /:id
│   ├── POST   /:id/publish
│   ├── POST   /:id/close
│   │
│   └── /:id/requirements
│       ├── GET    /
│       ├── POST   /
│       ├── PATCH  /:requirementId
│       └── DELETE /:requirementId
│
└── matches
    └── GET /:id
```

## Resource Relationships

```text
User
 │
 ├── Candidate
 │    │
 │    └── CandidateSkill
 │           │
 │           └── Skill
 │
 └── Recruiter
      │
      └── Company
           │
           └── Job
                │
                ├── JobRequirement
                │      │
                │      └── Skill
                │
                └── Match
                     │
                     └── Candidate
```

The API separates user accounts from domain resources such as Candidates, Recruiters, Companies and Jobs.

User-specific domain resources should use their own resource paths rather than being treated as sub-resources of `/users`.

For example:

```text
Canonical:
GET /api/v1/candidates/me

Legacy:
GET /api/v1/users/me/candidate
```

The canonical API contract defines the intended resource structure. Legacy endpoints are documented separately and should not be interpreted as the canonical API.

---

# 1. Authentication

## Register

```http
POST /api/v1/auth/register
```

**Authentication:** Public

### Request

```json
{
  "email": "user@example.com",
  "password": "password",
  "role": "CANDIDATE"
}
```

### Response

```json
{
  "id": "uuid",
  "email": "user@example.com",
  "role": "CANDIDATE"
}
```

### Status Codes

```text
201 Created
400 Bad Request
409 Conflict
```

---

## Login

```http
POST /api/v1/auth/login
```

**Authentication:** Public

### Request

```json
{
  "email": "user@example.com",
  "password": "password"
}
```

### Response

```json
{
  "accessToken": "token"
}
```

### Status Codes

```text
200 OK
400 Bad Request
401 Unauthorized
```

---

# 2. Users

## Get Current User

```http
GET /api/v1/users/me
```

**Authentication:** Required

**Ownership:** Authenticated user

### Response

```json
{
  "id": "uuid",
  "email": "user@example.com",
  "role": "CANDIDATE"
}
```

### Status Codes

```text
200 OK
401 Unauthorized
```

---

## Create User

```http
POST /api/v1/users
```

**Authentication:** Required

**Authorization:** ADMIN

---

## List Users

```http
GET /api/v1/users
```

**Authentication:** Required

**Authorization:** ADMIN

### Query Parameters

```text
page
limit
```

---

## Get User

```http
GET /api/v1/users/:id
```

**Authentication:** Required

**Authorization:** ADMIN or permitted resource access

---

## Update User

```http
PATCH /api/v1/users/:id
```

**Authentication:** Required

**Authorization:** Resource owner or ADMIN

---

## Delete User

```http
DELETE /api/v1/users/:id
```

**Authentication:** Required

**Authorization:** ADMIN

---

# 3. Candidates

## Get Current Candidate

```http
GET /api/v1/candidates/me
```

**Authentication:** Required

**Authorization:** CANDIDATE

**Ownership:** Authenticated candidate

---

## Create Candidate

```http
POST /api/v1/candidates
```

**Authentication:** Required

**Authorization:** CANDIDATE

**Ownership:** Candidate is derived from the authenticated user.

---

## Update Current Candidate

```http
PATCH /api/v1/candidates/me
```

**Authentication:** Required

**Authorization:** CANDIDATE

**Ownership:** Authenticated candidate

---

## Legacy Candidate Endpoints

```http
GET  /api/v1/users/me/candidate
POST /api/v1/users/me/candidate
```

These endpoints are legacy candidates for replacement by the resource-oriented Candidate API above.

---

# 4. Candidate Skills

Candidate Skills belong to the Candidate domain.

## List Candidate Skills

```http
GET /api/v1/candidates/me/skills
```

**Authentication:** Required

**Authorization:** CANDIDATE

**Ownership:** Authenticated candidate

---

## Add Candidate Skill

```http
POST /api/v1/candidates/me/skills
```

**Authentication:** Required

**Authorization:** CANDIDATE

**Ownership:** Authenticated candidate

---

## Update Candidate Skill

```http
PATCH /api/v1/candidates/me/skills/:id
```

**Authentication:** Required

**Authorization:** CANDIDATE

**Ownership:** Authenticated candidate

`:id` identifies the `CandidateSkill` resource, not the global `Skill` resource.

---

## Delete Candidate Skill

```http
DELETE /api/v1/candidates/me/skills/:id
```

**Authentication:** Required

**Authorization:** CANDIDATE

**Ownership:** Authenticated candidate

---

# 5. Skills

## List Skills

```http
GET /api/v1/skills
```

**Authentication:** Required

---

## Create Skill

```http
POST /api/v1/skills
```

**Authentication:** Required

**Authorization:** ADMIN

---

## Get Skill

```http
GET /api/v1/skills/:id
```

**Authentication:** Required

---

## Update Skill

```http
PATCH /api/v1/skills/:id
```

**Authentication:** Required

**Authorization:** ADMIN

---

## Delete Skill

```http
DELETE /api/v1/skills/:id
```

**Authentication:** Required

**Authorization:** ADMIN

---

# 6. Recruiters

## Get Current Recruiter

```http
GET /api/v1/recruiters/me
```

**Authentication:** Required

**Authorization:** RECRUITER

**Ownership:** Authenticated recruiter

---

## Update Current Recruiter

```http
PATCH /api/v1/recruiters/me
```

**Authentication:** Required

**Authorization:** RECRUITER

**Ownership:** Authenticated recruiter

---

# 7. Companies

## Create Company

```http
POST /api/v1/companies
```

**Authentication:** Required

**Authorization:** RECRUITER

**Ownership:** Company owner is derived from the authenticated recruiter.

---

## Get Current Company

```http
GET /api/v1/companies/me
```

**Authentication:** Required

**Authorization:** RECRUITER

**Ownership:** Authenticated recruiter's company

---

## Update Current Company

```http
PATCH /api/v1/companies/me
```

**Authentication:** Required

**Authorization:** Company owner or authorized recruiter

**Ownership:** Authenticated recruiter's company

---

# 8. Jobs

## Create Job

```http
POST /api/v1/jobs
```

**Authentication:** Required

**Authorization:** RECRUITER

**Ownership:** Job owner is derived from the authenticated recruiter/company context.

---

## List Jobs

```http
GET /api/v1/jobs
```

**Authentication:** Public or authenticated, according to the finalized job visibility rules.

### Query Parameters

```text
page
limit
filter
```

---

## Get Job

```http
GET /api/v1/jobs/:id
```

**Authentication:** Public or authenticated, according to the finalized job visibility rules.

---

## Update Job

```http
PATCH /api/v1/jobs/:id
```

**Authentication:** Required

**Authorization:** Job owner or authorized recruiter

---

## Publish Job

```http
POST /api/v1/jobs/:id/publish
```

**Authentication:** Required

**Authorization:** Job owner or authorized recruiter

---

## Close Job

```http
POST /api/v1/jobs/:id/close
```

**Authentication:** Required

**Authorization:** Job owner or authorized recruiter

---

# 9. Job Requirements

## List Job Requirements

```http
GET /api/v1/jobs/:id/requirements
```

**Authentication:** Required

**Authorization:** Authorized access to the job

---

## Add Job Requirement

```http
POST /api/v1/jobs/:id/requirements
```

**Authentication:** Required

**Authorization:** Job owner or authorized recruiter

---

## Update Job Requirement

```http
PATCH /api/v1/jobs/:id/requirements/:requirementId
```

**Authentication:** Required

**Authorization:** Job owner or authorized recruiter

---

## Delete Job Requirement

```http
DELETE /api/v1/jobs/:id/requirements/:requirementId
```

**Authentication:** Required

**Authorization:** Job owner or authorized recruiter

---

# 10. Matching

## Match Candidates to Job

```http
POST /api/v1/jobs/:id/match
```

**Authentication:** Required

**Authorization:** Job owner or authorized recruiter

---

## List Job Matches

```http
GET /api/v1/jobs/:id/matches
```

**Authentication:** Required

**Authorization:** Job owner or authorized recruiter

---

## Get Match

```http
GET /api/v1/matches/:id
```

**Authentication:** Required

**Authorization:** Authorized access to the related job/match

---

# 11. Common API Rules

## Authentication

Protected endpoints require an authenticated user.

The authenticated identity is determined server-side.

Ownership identifiers must not be accepted from the client when they can be derived from the authenticated context.

## Error Responses

The API uses consistent error responses for validation, authentication, authorization, resource, conflict, and server errors.

Supported status codes include:

```text
400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
409 Conflict
422 Unprocessable Entity
500 Internal Server Error
```

The exact error response schema is defined in the OpenAPI specification.

## Pagination

Collection endpoints that support pagination use:

```text
?page=1&limit=20
```

Paginated responses use a consistent `data` and `meta` structure.

## Breaking Changes

Breaking changes must be documented before implementation.

Legacy endpoints should be deprecated before removal where practical.

Legacy endpoints must use the same underlying domain/service logic as their canonical replacements.
