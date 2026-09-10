# IT Talent Platform — Database Architecture

**Document:** `database.md`
**Version:** `0.2.0`
**Status:** Current Database Architecture
**Last updated:** 2026-09-10

---

# 1. Purpose

This document defines the current database architecture of the IT Talent Platform.

The database uses:

* PostgreSQL as the relational database;
* Prisma ORM for database access;
* UUIDs as primary identifiers;
* foreign-key relationships for relational integrity;
* indexes for frequently queried fields;
* timestamps on persisted entities.

The Prisma schema is the authoritative source for the implemented database model.

Schema location:

```text
it-talent-backend/prisma/schema.prisma
```

---

# 2. Database Technology

| Area              | Technology                         |
| ----------------- | ---------------------------------- |
| Database          | PostgreSQL                         |
| ORM               | Prisma                             |
| Identifier type   | UUID                               |
| Database access   | Backend application through Prisma |
| Schema definition | `prisma/schema.prisma`             |
| Migration system  | Prisma Migrate                     |

The backend does not expose the database directly to the frontend.

---

# 3. Current Entity Model

The current Prisma schema contains the following entities:

* User
* Candidate
* Recruiter
* Company
* Job
* Skill
* CandidateSkill
* JobRequirement
* Match
* Application

The main relationships are:

```text
User
 ├── Candidate
 │    ├── CandidateSkill ── Skill
 │    ├── Match ────────── Job
 │    └── Application ──── Job
 │
 └── Recruiter
      ├── Company
      └── Job

Company
 └── Job
      ├── JobRequirement ── Skill
      ├── Match ────────── Candidate
      └── Application ──── Candidate
```

These relationships are implemented in the Prisma schema.

---

# 4. UUID Strategy

Primary identifiers use UUIDs.

Example:

```text
550e8400-e29b-41d4-a716-446655440000
```

The Prisma schema defines UUID generation for the primary identifiers:

```prisma
id String @id @default(uuid()) @db.Uuid
```

UUIDs provide globally unique identifiers and are used consistently across the main domain entities.

---

# 5. Timestamp Strategy

Persisted entities use timestamps where applicable:

```text
createdAt
updatedAt
```

The database uses:

```text
createdAt → @default(now())
updatedAt → @updatedAt
```

Some domain entities also contain additional dates such as:

```text
availabilityDate
publishedAt
expiresAt
calculatedAt
```

The exact fields are defined by the Prisma schema.

---

# 6. User

**Status:** Implemented

The `User` entity represents authentication identity and platform role information.

```text
users

id
email
passwordHash
role
status
createdAt
updatedAt
```

Properties:

* `id` — UUID primary key
* `email` — unique email address
* `passwordHash` — stored password hash
* `role` — platform role
* `status` — account status
* `createdAt`
* `updatedAt`

Roles:

```text
CANDIDATE
RECRUITER
ADMIN
```

Account statuses:

```text
ACTIVE
PENDING
SUSPENDED
DELETED
```

A user can have one Candidate profile or one Recruiter profile.

---

# 7. Candidate

**Status:** Implemented

The `Candidate` entity connects a user account with professional profile information.

```text
candidates

id
userId
headline
summary
location
salaryMin
salaryMax
currency
availabilityDate
remotePreference
createdAt
updatedAt
```

The relationship between `User` and `Candidate` is one-to-one:

```text
User
 │
 └── Candidate
```

`userId` is unique, ensuring that a user has at most one Candidate record.

A Candidate is also related to:

* CandidateSkill
* Match
* Application.

---

# 8. Recruiter

**Status:** Implemented

The `Recruiter` entity connects a user account with recruiter-specific information.

```text
recruiters

id
userId
companyId
jobTitle
createdAt
updatedAt
```

Relationships:

```text
User
 │
 └── Recruiter
       │
       ├── Company
       │
       └── Jobs
```

`userId` is unique.

A recruiter can be associated with a company through `companyId`.

`companyId` is indexed for efficient company-based queries.

---

# 9. Company

**Status:** Implemented

The `Company` entity represents an organization using the platform.

```text
companies

id
name
slug
website
description
location
createdAt
updatedAt
```

Relationships:

```text
Company
 ├── Recruiters
 └── Jobs
```

The company `slug` is unique.

A company can have multiple recruiters and multiple jobs.

---

# 10. Job

**Status:** Implemented

The `Job` entity represents a vacancy published through the platform.

```text
jobs

id
companyId
createdByRecruiterId
title
description
location
employmentType
workMode
salaryMin
salaryMax
currency
status
publishedAt
expiresAt
createdAt
updatedAt
```

Relationships:

```text
Company
 │
 └── Job
      │
      ├── JobRequirement
      ├── Match
      └── Application

Recruiter
 │
 └── Job
```

A job belongs to one company and has one creating recruiter.

Employment types:

```text
FULL_TIME
PART_TIME
CONTRACT
FREELANCE
INTERNSHIP
```

Work modes:

```text
REMOTE
HYBRID
ONSITE
FLEXIBLE
```

Job statuses:

```text
DRAFT
PUBLISHED
PAUSED
CLOSED
ARCHIVED
```

Indexes are defined for company, status, publication/expiry combinations, and creation date.

---

# 11. Skill

**Status:** Implemented

Skills are a central part of the platform's candidate and job data model.

```text
skills

id
name
slug
category
description
createdAt
updatedAt
```

The `slug` is unique.

Skills are connected to:

* CandidateSkill
* JobRequirement.

Skill categories currently defined in Prisma are:

```text
FRONTEND
BACKEND
FULLSTACK
MOBILE
DEVOPS
CLOUD
DATA
AI_ML
SECURITY
DATABASE
TESTING
PROJECT_MANAGEMENT
DESIGN
OTHER
```

---

# 12. CandidateSkill

**Status:** Implemented

`CandidateSkill` represents the relationship between a candidate and a skill.

```text
candidate_skills

id
candidateId
skillId
proficiencyLevel
yearsOfExperience
source
confidence
verified
createdAt
updatedAt
```

Relationship:

```text
Candidate
    │
    └── CandidateSkill ── Skill
```

The combination of:

```text
candidateId + skillId
```

is unique, preventing duplicate candidate-skill relationships.

Indexes exist for both candidate and skill.

---

# 13. Candidate Skill Source

Candidate skills contain a `source` field.

Current values are:

```text
SELF_REPORTED
CV
AI_EXTRACTED
ASSESSMENT
VERIFIED
RECRUITER_CONFIRMED
```

This allows the database to retain the origin of candidate skill information.

The `confidence` field can store an additional confidence value where applicable.

The `verified` field indicates whether the skill has been verified.

---

# 14. Candidate Skill Proficiency

`CandidateSkill` contains:

```text
proficiencyLevel
```

The database stores this value as an integer.

The current Prisma schema does not define a separate proficiency enum.

Therefore, the database architecture treats the field as an integer rather than introducing additional database-level proficiency categories.

---

# 15. JobRequirement

**Status:** Implemented

`JobRequirement` connects a job with a required or preferred skill.

```text
job_requirements

id
jobId
skillId
minimumLevel
required
weight
createdAt
updatedAt
```

Relationship:

```text
Job
 │
 └── JobRequirement ── Skill
```

The combination of:

```text
jobId + skillId
```

is unique.

This prevents the same skill from being added multiple times to one job.

Indexes exist for both job and skill.

---

# 16. Required and Preferred Skills

A job requirement contains:

```text
required
```

This Boolean value distinguishes required skills from non-required skills.

Example:

```text
required = true
```

means the skill is required.

```text
required = false
```

means the skill is not marked as required.

`minimumLevel` and `weight` provide additional information for job-skill matching.

---

# 17. Match

**Status:** Implemented

The `Match` entity stores calculated compatibility information between a candidate and a job.

```text
matches

id
candidateId
jobId
overallScore
skillScore
experienceScore
locationScore
salaryScore
availabilityScore
preferenceScore
explanation
calculatedAt
createdAt
updatedAt
```

Relationship:

```text
Candidate
    │
    └── Match ── Job
```

The combination of:

```text
candidateId + jobId
```

is unique.

This ensures one stored Match record per candidate-job pair.

Indexes exist for candidate, job, and overall score.

---

# 18. Match Scores

The database stores an overall score and optional component scores:

```text
overallScore
skillScore
experienceScore
locationScore
salaryScore
availabilityScore
preferenceScore
```

The calculation itself belongs to backend application logic.

The database stores the resulting match data rather than implementing the matching algorithm inside PostgreSQL.

---

# 19. Match Explanation

The `Match` entity contains:

```text
explanation Json?
```

This allows structured match explanation data to be stored alongside the calculated scores.

The JSON field provides flexibility for the match explanation structure without introducing additional relational tables.

---

# 20. Application

**Status:** Implemented

The `Application` entity represents a candidate application for a job.

```text
applications

id
jobId
candidateId
coverLetter
status
createdAt
updatedAt
```

Relationships:

```text
Candidate
    │
    └── Application ── Job
```

The combination of:

```text
candidateId + jobId
```

is unique.

This prevents a candidate from creating duplicate applications for the same job.

Indexes exist for:

```text
candidateId
jobId
status
```

---

# 21. Application Status

The current application statuses are:

```text
PENDING
REVIEWING
ACCEPTED
REJECTED
WITHDRAWN
```

The status represents the current state of a candidate's application.

---

# 22. Entity Relationships

The main database relationships are:

```text
User
 │
 ├────────────── Candidate
 │                  │
 │                  ├── CandidateSkill ── Skill
 │                  │
 │                  ├── Match ────────── Job
 │                  │
 │                  └── Application ──── Job
 │
 └────────────── Recruiter
                    │
                    ├── Company
                    │
                    └── Job
```

Job relationships:

```text
Company
   │
   └── Job
        ├── JobRequirement ── Skill
        ├── Match ─────────── Candidate
        └── Application ───── Candidate
```

These relationships are represented through Prisma relations and foreign keys.

---

# 23. Unique Constraints

Current unique constraints include:

```text
User.email

Company.slug

Candidate.userId

Recruiter.userId

CandidateSkill(candidateId, skillId)

JobRequirement(jobId, skillId)

Match(candidateId, jobId)

Application(candidateId, jobId)
```

These constraints protect the integrity of identity and domain relationships.

---

# 24. Index Strategy

The current Prisma schema defines indexes where they support common relationship and filtering operations.

Examples include:

```text
Recruiter.companyId

Job.companyId
Job.status
Job(status, publishedAt)
Job(status, expiresAt)
Job.createdAt

CandidateSkill.candidateId
CandidateSkill.skillId

JobRequirement.jobId
JobRequirement.skillId

Match.candidateId
Match.jobId
Match.overallScore

Application.candidateId
Application.jobId
Application.status
```

Indexes should remain aligned with actual application query patterns.

---

# 25. Referential Integrity

Relationships use Prisma foreign-key relations.

Examples:

```text
candidates.userId
        ↓
users.id
```

```text
candidate_skills.candidateId
        ↓
candidates.id
```

```text
candidate_skills.skillId
        ↓
skills.id
```

```text
jobs.companyId
        ↓
companies.id
```

```text
jobs.createdByRecruiterId
        ↓
recruiters.id
```

```text
applications.jobId
        ↓
jobs.id
```

```text
applications.candidateId
        ↓
candidates.id
```

The Prisma schema is authoritative for relationship definitions and deletion behavior.

---

# 26. Decimal Data

Financial and scoring values use Prisma `Decimal` fields where appropriate.

Examples include:

```text
Candidate.salaryMin
Candidate.salaryMax

CandidateSkill.yearsOfExperience
CandidateSkill.confidence

Job.salaryMin
Job.salaryMax

JobRequirement.weight

Match.overallScore
Match.skillScore
Match.experienceScore
Match.locationScore
Match.salaryScore
Match.availabilityScore
Match.preferenceScore
```

This avoids representing these values as application-level floating-point fields in the Prisma model.

---

# 27. JSON Data

The database currently uses JSON for:

```text
Match.explanation
```

This is used for structured match explanation data.

Other domain information remains represented through relational columns and entities.

---

# 28. Database and Matching Separation

The database stores the information required by matching:

```text
Candidate
    +
CandidateSkill
    +
Job
    +
JobRequirement
```

The resulting match is stored as:

```text
Match
```

Conceptually:

```text
PostgreSQL
    │
    ├── Candidate + Skills
    │
    └── Job + Requirements
             │
             ▼
       Matching Logic
             │
             ▼
           Match
```

Matching calculations belong to backend application logic.

---

# 29. Data Ownership

The database model separates ownership by domain.

**Candidate**

Owns:

* candidate profile;
* candidate skills;
* candidate matches;
* candidate applications.

**Recruiter**

Owns recruiter account information and recruiter-created jobs.

**Company**

Owns company information and its associated jobs and recruiters.

**Platform**

Owns:

* skill catalog;
* job requirements;
* matching data;
* application state.

The exact authorization rules are enforced by the backend application.

---

# 30. Database Transactions

Operations affecting multiple related records should use Prisma transactions when atomicity is required.

For example, creating a job together with multiple job requirements can be handled as one transaction:

```text
Create Job
   ↓
Create JobRequirement records
   ↓
Commit
```

If an operation fails, the transaction can roll back the related database changes.

---

# 31. Migration Strategy

Database schema changes should use Prisma migrations.

Expected workflow:

```text
Update schema.prisma
        ↓
Create Prisma migration
        ↓
Run tests
        ↓
Commit schema + migration
        ↓
Deploy
```

Migration files should remain version-controlled with the backend repository.

---

# 32. Database Development

The backend uses PostgreSQL through Prisma.

The database connection is configured through the backend environment configuration.

The Prisma schema defines:

* models;
* relations;
* enums;
* indexes;
* unique constraints;
* default values;
* database field types.

---

# 33. Database Security

Database credentials must remain backend-only.

Sensitive database configuration must not be committed to the repository.

The frontend communicates with the backend API rather than connecting directly to PostgreSQL.

The backend is responsible for:

* authentication;
* authorization;
* validation;
* database access;
* protection of user and recruitment data.

---

# 34. Personal Data

The database contains personal and professional information, including:

* email addresses;
* candidate profile information;
* salary preferences;
* availability;
* professional skills;
* recruiter and company information;
* application data.

Access to this information must be controlled by backend authorization.

---

# 35. Current Database Scope

The current Prisma schema contains these implemented domain areas:

| Domain           | Entity         |
| ---------------- | -------------- |
| Authentication   | User           |
| Candidate        | Candidate      |
| Recruiter        | Recruiter      |
| Company          | Company        |
| Jobs             | Job            |
| Skills           | Skill          |
| Candidate skills | CandidateSkill |
| Job requirements | JobRequirement |
| Matching         | Match          |
| Applications     | Application    |

The database therefore represents the core candidate, recruiter, job, skill, matching, and application domains.

---

# 36. Current Entity Status

| Entity         | Status        |
| -------------- | ------------- |
| User           | ✅ Implemented |
| Candidate      | ✅ Implemented |
| Recruiter      | ✅ Implemented |
| Company        | ✅ Implemented |
| Job            | ✅ Implemented |
| Skill          | ✅ Implemented |
| CandidateSkill | ✅ Implemented |
| JobRequirement | ✅ Implemented |
| Match          | ✅ Implemented |
| Application    | ✅ Implemented |

These statuses are based specifically on the current Prisma schema.

---

# 37. Current Entity Relationship Diagram

```text
                         ┌──────────────┐
                         │    users     │
                         └──────┬───────┘
                                │
                    ┌───────────┴───────────┐
                    │                       │
                    ▼                       ▼
             ┌──────────────┐       ┌──────────────┐
             │  candidates  │       │  recruiters  │
             └──────┬───────┘       └──────┬───────┘
                    │                       │
                    │                       ▼
                    │                ┌──────────────┐
                    │                │  companies   │
                    │                └──────┬───────┘
                    │                       │
                    │                       ▼
                    │                ┌──────────────┐
                    │                │     jobs     │
                    │                └──────┬───────┘
                    │                       │
             ┌──────┴───────┐        ┌─────┴────────────┐
             │              │        │                  │
             ▼              ▼        ▼                  ▼
     ┌──────────────┐ ┌──────────┐ ┌────────────────┐ ┌──────────────┐
     │candidate_    │ │ matches  │ │job_requirements│ │ applications │
     │skills        │ └────┬─────┘ └───────┬────────┘ └──────┬───────┘
     └──────┬───────┘      │               │                 │
            │              │               │                 │
            ▼              ▼               ▼                 ▼
       ┌──────────┐     candidates       skills           candidates
       │  skills  │
       └──────────┘
```

This represents the current Prisma domain model.

---

# 38. Prisma as Source of Truth

The implementation hierarchy is:

```text
Prisma schema
      ↓
Prisma migrations
      ↓
Backend implementation
      ↓
Documentation
```

The primary database source of truth is:

```text
it-talent-backend/prisma/schema.prisma
```

Documentation must remain aligned with the implemented schema.

When the schema changes, the corresponding database documentation should be reviewed and updated.

---

# 39. Current Database Definition

The IT Talent Platform database provides the relational foundation for:

* user accounts and roles;
* candidate profiles;
* recruiter profiles;
* companies;
* job vacancies;
* normalized skills;
* candidate skills;
* job skill requirements;
* candidate-job matching;
* job applications.

The model connects these domains through explicit relational entities, UUID identifiers, foreign keys, unique constraints, indexes, timestamps, enums, Decimal fields, and JSON match explanations.

---

# 40. Document Status

**Document:** `database.md`
**Version:** `0.2.0`
**Status:** Current Database Architecture
**Last updated:** 2026-09-10

The Prisma schema remains the authoritative implementation source for the database model.

Any future database change should be reflected in:

1. `prisma/schema.prisma`;
2. the corresponding Prisma migration;
3. `database.md` where the documented architecture changes.
