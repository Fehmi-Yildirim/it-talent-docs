# IT Talent Platform — Database Architecture

**Document:** `database.md`  
**Version:** `0.3.0`  
**Status:** Current Database Architecture  
**Last updated:** 2026-11-09

---

## 1. Purpose

This document describes the database architecture of the IT Talent Platform.

The database provides the relational foundation for user accounts, candidate and recruiter profiles, companies, jobs, skills, matching data, and job applications.

The database architecture uses:

- PostgreSQL as the relational database;
- Prisma ORM for database access;
- UUIDs as primary identifiers;
- foreign-key relationships for relational integrity;
- indexes and unique constraints for data integrity and query performance;
- timestamps on persisted entities where applicable.

The Prisma schema is the authoritative source for the implemented database model.

**Primary schema location:**

```text
it-talent-backend/prisma/schema.prisma
```

This document describes the database model. It does not imply that every database entity has a corresponding public API endpoint or frontend feature.

---

## 2. Database Technology

| Area | Technology |
|---|---|
| Database | PostgreSQL |
| ORM | Prisma |
| Identifier type | UUID |
| Database access | Backend application through Prisma |
| Schema definition | `prisma/schema.prisma` |
| Migration system | Prisma Migrate |

The backend is responsible for database access. The frontend communicates with the backend API and does not connect directly to PostgreSQL.

---

## 3. Current Entity Model

The documented Prisma domain model contains the following entities:

- `User`
- `Candidate`
- `Recruiter`
- `Company`
- `Job`
- `Skill`
- `CandidateSkill`
- `JobRequirement`
- `Match`
- `Application`

The main domain relationships are:

```text
User
├── Candidate
│   ├── CandidateSkill ── Skill
│   ├── Match ─────────── Job
│   └── Application ───── Job
│
└── Recruiter
    └── Company
        └── Job
            ├── JobRequirement ── Skill
            ├── Match ─────────── Candidate
            └── Application ───── Candidate
```

The actual relationship cardinalities, optionality, foreign-key constraints, and deletion behavior are defined in `schema.prisma`.

---

## 4. UUID Strategy

Primary identifiers use UUIDs.

Example:

```text
550e8400-e29b-41d4-a716-446655440000
```

The Prisma schema uses UUID identifiers in the following form:

```prisma
id String @id @default(uuid()) @db.Uuid
```

UUIDs provide globally unique identifiers and are used consistently across the main domain entities.

The exact identifier definition for each model is determined by the Prisma schema.

---

## 5. Timestamp Strategy

Persisted entities use timestamps where applicable:

```text
createdAt
updatedAt
```

The common Prisma definitions are:

```prisma
createdAt DateTime @default(now())
updatedAt DateTime @updatedAt
```

Some domain entities also contain additional date fields, such as:

```text
availabilityDate
publishedAt
expiresAt
calculatedAt
```

Not every model necessarily contains every timestamp or date field. The Prisma schema defines the exact fields and defaults.

---

## 6. User

**Status:** Implemented in the documented Prisma model

The `User` entity represents authentication identity and platform role information.

Table:

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

- `id` — UUID primary key;
- `email` — unique email address;
- `passwordHash` — stored password hash;
- `role` — platform role;
- `status` — account status;
- `createdAt` — creation timestamp;
- `updatedAt` — last update timestamp.

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

A user account can be associated with candidate-specific or recruiter-specific profile information through the corresponding profile relations.

The exact optionality and constraints are defined in the Prisma schema.

---

## 7. Candidate

**Status:** Implemented in the documented Prisma model

The `Candidate` entity connects a user account with professional profile information.

Table:

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

The documented relationship between `User` and `Candidate` is one-to-one:

```text
User
 |
 └── Candidate
```

The `userId` field is unique, ensuring that a user can have at most one associated Candidate record.

A candidate is also related to:

- `CandidateSkill`;
- `Match`;
- `Application`.

Candidate ownership and access permissions are enforced by the backend application.

---

## 8. Recruiter

**Status:** Implemented in the documented Prisma model

The `Recruiter` entity connects a user account with recruiter-specific information.

Table:

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
 |
 └── Recruiter
      |
      ├── Company
      |
      └── Jobs
```

The `userId` field is unique.

A recruiter can be associated with a company through `companyId`. This field is indexed for company-based queries.

The exact optionality of the company relationship is defined in the Prisma schema.

---

## 9. Company

**Status:** Implemented in the documented Prisma model

The `Company` entity represents an organization using the platform.

Table:

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

A company can be associated with multiple recruiters and multiple jobs, according to the documented domain model.

---

## 10. Job

**Status:** Implemented in the documented Prisma model

The `Job` entity represents a vacancy managed through the platform.

Table:

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
 |
 └── Job
      ├── JobRequirement
      ├── Match
      └── Application

Recruiter
 |
 └── Job
```

A job belongs to a company and records the recruiter who created it.

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

The documented index strategy includes company, status, publication and expiry combinations, and creation date.

---

## 11. Skill

**Status:** Implemented in the documented Prisma model

The `Skill` entity provides a normalized skill catalog used by candidate profiles and job requirements.

Table:

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

- `CandidateSkill`;
- `JobRequirement`.

The documented skill categories are:

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

The Prisma schema defines the authoritative category values.

---

## 12. CandidateSkill

**Status:** Implemented in the documented Prisma model

`CandidateSkill` represents the relationship between a candidate and a skill.

Table:

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
    |
    └── CandidateSkill ── Skill
```

The combination of:

```text
candidateId + skillId
```

is unique.

This prevents duplicate candidate-skill relationships.

Indexes are defined for candidate and skill references.

The existence of this database entity does not, by itself, establish that a dedicated public API endpoint exists for managing candidate skills.

---

## 13. Candidate Skill Source

Candidate skills contain a `source` field that records the origin of the skill information.

The documented values are:

```text
SELF_REPORTED
CV
AI_EXTRACTED
ASSESSMENT
VERIFIED
RECRUITER_CONFIRMED
```

This allows the platform to distinguish between self-reported, extracted, assessed, and otherwise confirmed skill information.

Additional fields include:

- `confidence` — an optional confidence value where applicable;
- `verified` — indicates whether the skill has been verified.

The Prisma schema defines the exact field types, defaults, and allowed values.

---

## 14. Candidate Skill Proficiency

`CandidateSkill` contains:

```text
proficiencyLevel
```

The documented Prisma model stores this value as an integer.

The current documented schema does not define a separate proficiency enum.

Consequently, this architecture describes proficiency as an integer rather than introducing additional database-level proficiency categories.

Any range validation or interpretation of the integer belongs to the application layer unless explicitly enforced by the database.

---

## 15. JobRequirement

**Status:** Implemented in the documented Prisma model

`JobRequirement` connects a job with a required or preferred skill.

Table:

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
 |
 └── JobRequirement ── Skill
```

The combination of:

```text
jobId + skillId
```

is unique.

This prevents the same skill from being added multiple times to one job.

Indexes are defined for job and skill references.

---

## 16. Required and Preferred Skills

A job requirement contains:

```text
required
```

This Boolean field distinguishes required skills from skills that are not marked as required.

For example:

```text
required = true
```

means the skill is required.

```text
required = false
```

means the skill is not marked as required.

Additional fields include:

- `minimumLevel` — the minimum proficiency level specified for the requirement;
- `weight` — a value that can contribute to matching calculations.

The database stores these values. Their interpretation and use in matching belong to backend application logic.

---

## 17. Match

**Status:** Implemented in the documented Prisma model

The `Match` entity stores calculated compatibility information between a candidate and a job.

Table:

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
    |
    └── Match ── Job
```

The combination of:

```text
candidateId + jobId
```

is unique.

This ensures one stored Match record per candidate-job pair.

Indexes are defined for candidate, job, and overall score.

The existence of the `Match` model does not imply that matching is exposed through a public API endpoint. API availability is documented separately in `architecture/api.md`.

---

## 18. Match Scores and Explanation

The database stores an overall score and component scores:

```text
overallScore
skillScore
experienceScore
locationScore
salaryScore
availabilityScore
preferenceScore
```

The matching calculation belongs to backend application logic.

The database stores the resulting match data rather than implementing the matching algorithm inside PostgreSQL.

The `Match` entity also contains:

```prisma
explanation Json?
```

This optional JSON field allows structured match explanation data to be stored alongside the calculated scores.

The exact explanation structure is defined by the backend implementation.

---

## 19. Application

**Status:** Implemented in the documented Prisma model

The `Application` entity represents a candidate's application for a job.

Table:

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
    |
    └── Application ── Job
```

The combination of:

```text
candidateId + jobId
```

is unique.

This prevents a candidate from creating duplicate applications for the same job.

Indexes are defined for:

```text
candidateId
jobId
status
```

---

## 20. Application Status

The documented application statuses are:

```text
PENDING
REVIEWING
ACCEPTED
REJECTED
WITHDRAWN
```

The status represents the current state of a candidate's application.

Allowed status transitions and the authorization to perform them are responsibilities of the backend application.

---

## 21. Entity Relationships

The main domain relationships are:

```text
User
 |
 ├── Candidate
 |     ├── CandidateSkill ── Skill
 |     ├── Match ─────────── Job
 |     └── Application ───── Job
 |
 └── Recruiter
       |
       └── Company
             |
             └── Job
                   ├── JobRequirement ── Skill
                   ├── Match ─────────── Candidate
                   └── Application ───── Candidate
```

These relationships are represented through Prisma relations and database foreign keys.

The diagram is a conceptual overview. The Prisma schema defines the exact cardinalities, optional relations, and referential actions.

---

## 22. Unique Constraints

The documented unique constraints include:

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

These constraints protect the integrity of identity fields and domain relationships.

The actual constraint definitions and names are determined by the Prisma schema and generated migrations.

---

## 23. Index Strategy

The documented Prisma model defines indexes to support common relationship and filtering operations.

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

Additional indexes should be introduced when query performance measurements and access patterns justify them.

The Prisma schema and database migrations remain authoritative for the implemented indexes.

---

## 24. Referential Integrity

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

Foreign-key constraints help prevent invalid references.

The Prisma schema defines relationship behavior, including deletion and update actions. Documentation must not assume cascade deletion or other referential actions without checking the actual schema.

---

## 25. Decimal Data

The documented database model uses Prisma `Decimal` fields for financial, experience, weighting, and scoring values where appropriate.

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

Decimal fields avoid relying on binary floating-point representations for values that require decimal precision.

The exact precision, scale, optionality, and database types are defined by the Prisma schema.

---

## 26. JSON Data

The documented database model uses JSON for:

```text
Match.explanation
```

This field stores structured match explanation data.

Other core domain information remains represented through relational columns and entities.

The structure and validation of JSON content are responsibilities of the backend application unless additional database constraints are explicitly defined.

---

## 27. Database and Matching Separation

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

The resulting match information is stored in:

```text
Match
```

Conceptually:

```text
PostgreSQL
    |
    ├── Candidate + Skills
    |
    └── Job + Requirements
             |
             ▼
       Matching Logic
             |
             ▼
           Match
```

Matching calculations belong to backend application logic.

PostgreSQL stores the source data and resulting match records; it does not define the matching algorithm.

---

## 28. Data Ownership and Authorization

The database model separates information by domain.

**Candidate**

Associated data includes:

- candidate profile;
- candidate skills;
- candidate matches;
- candidate applications.

**Recruiter**

Associated data includes:

- recruiter account information;
- company association;
- recruiter-created jobs.

**Company**

Associated data includes:

- company information;
- associated recruiters;
- company jobs.

**Platform**

Platform-level data includes:

- skill catalog;
- job requirements;
- matching records;
- application state.

These associations describe the domain model, not a complete authorization policy.

The backend application is responsible for enforcing authentication, role permissions, resource ownership, and access restrictions.

---

## 29. Database Transactions

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

Transaction boundaries should be defined according to the consistency requirements of each backend operation.

---

## 30. Migration Strategy

Database schema changes should use Prisma Migrate.

Expected workflow:

```text
Update schema.prisma
        ↓
Create Prisma migration
        ↓
Review generated SQL
        ↓
Run tests
        ↓
Commit schema + migration
        ↓
Deploy migration
```

Migration files should remain version-controlled in the backend repository.

Production database changes should be applied through the project's deployment and migration process rather than through undocumented manual schema changes.

---

## 31. Database Development

The backend uses PostgreSQL through Prisma.

The database connection is configured through backend environment configuration.

The Prisma schema defines:

- models;
- relations;
- enums;
- indexes;
- unique constraints;
- default values;
- database field types.

The backend environment example documents the database connection configuration.

Database credentials and connection strings must remain outside frontend configuration.

---

## 32. Database Security

Database credentials must remain backend-only.

Sensitive database configuration must not be committed to the repository.

The frontend communicates with the backend API rather than connecting directly to PostgreSQL.

The backend is responsible for:

- authentication;
- authorization;
- input validation;
- database access;
- protection of user and recruitment data.

Database access should follow the principle of least privilege.

Production credentials should be managed through the deployment environment or an appropriate secrets-management solution.

---

## 33. Personal Data

The database contains personal and professional information, including:

- email addresses;
- candidate profile information;
- salary preferences;
- availability information;
- professional skills;
- recruiter and company information;
- job application data.

Access to this information must be controlled by backend authorization.

Data retention, deletion, and export requirements should be implemented and documented in accordance with the platform's applicable privacy and legal requirements.

---

## 34. Current Database Scope

The documented Prisma model covers the following domain areas:

| Domain | Entity |
|---|---|
| Authentication | User |
| Candidate | Candidate |
| Recruiter | Recruiter |
| Company | Company |
| Jobs | Job |
| Skills | Skill |
| Candidate skills | CandidateSkill |
| Job requirements | JobRequirement |
| Matching | Match |
| Applications | Application |

The database therefore represents the core candidate, recruiter, company, job, skill, matching, and application domains.

A database entity does not automatically imply a corresponding public API endpoint or user-facing feature.

---

## 35. Current Entity Status

The documented entity status is:

| Entity | Status |
|---|---|
| User | Implemented |
| Candidate | Implemented |
| Recruiter | Implemented |
| Company | Implemented |
| Job | Implemented |
| Skill | Implemented |
| CandidateSkill | Implemented |
| JobRequirement | Implemented |
| Match | Implemented |
| Application | Implemented |

These statuses refer specifically to the documented Prisma data model.

They should be reviewed against `prisma/schema.prisma` whenever the schema changes.

---

## 36. Current Entity Relationship Diagram

The following diagram provides a conceptual overview of the domain model:

```text
                         ┌──────────────┐
                         │     users    │
                         └──────┬───────┘
                                │
                  ┌─────────────┴─────────────┐
                  │                           │
                  ▼                           ▼
           ┌──────────────┐            ┌──────────────┐
           │  candidates  │            │  recruiters  │
           └──────┬───────┘            └──────┬───────┘
                  │                           │
          ┌───────┼────────┐                  ▼
          │       │        │           ┌──────────────┐
          ▼       ▼        ▼           │   companies  │
   ┌──────────┐ ┌────────┐ ┌──────────────┐ └──────┬───────┘
   │candidate_│ │ matches│ │ applications │        │
   │skills    │ └───┬────┘ └──────┬───────┘        ▼
   └────┬─────┘     │             │          ┌──────────────┐
        │           │             │          │     jobs     │
        ▼           │             │          └──────┬───────┘
   ┌──────────┐     │             │                 │
   │  skills  │◄────┼─────────────┼──────────┐      │
   └──────────┘     │             │          │      ▼
                    │             │   ┌───────────────┐
                    │             │   │job_requirements│
                    │             │   └───────┬───────┘
                    │             │           │
                    └─────────────┴───────────┘
```

This diagram is conceptual and does not replace the Prisma schema as the authoritative relationship definition.

---

## 37. Prisma as Source of Truth

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

Migration files provide the version-controlled history of database changes.

Documentation must remain aligned with the implemented schema and migrations.

When the schema changes, the corresponding database documentation should be reviewed and updated.

---

## 38. Relationship to API Architecture

The database and API describe different layers of the platform.

The database defines:

- persisted entities;
- fields and data types;
- relations and foreign keys;
- indexes and unique constraints;
- database-level defaults and enums.

The API defines:

- available HTTP endpoints;
- request and response contracts;
- authentication and authorization requirements;
- validation behavior;
- operations exposed to frontend clients.

For example, the presence of `Match` or `CandidateSkill` in the Prisma schema does not establish that a public matching or candidate-skill endpoint exists.

The API contract is documented separately in:

```text
architecture/api.md
```

Both documents should be reviewed when a change affects the database and the API.

---

## 39. Current Database Definition

The IT Talent Platform database provides the relational foundation for:

- user accounts and roles;
- candidate profiles;
- recruiter profiles;
- companies;
- job vacancies;
- normalized skills;
- candidate skills;
- job skill requirements;
- candidate-job matching records;
- job applications.

The model connects these domains through relational entities, UUID identifiers, foreign keys, unique constraints, indexes, timestamps, enums, Decimal fields, and JSON match explanations.

The precise implementation is defined by the Prisma schema and its migration history.

---

## 40. Document Status

**Document:** `database.md`  
**Version:** `0.3.0`  
**Status:** Current Database Architecture  
**Last updated:** 2026-11-09

The Prisma schema remains the authoritative implementation source for the database model.

Any future database change should be reflected in:

1. `prisma/schema.prisma`;
2. the corresponding Prisma migration;
3. backend code affected by the schema change;
4. `database.md`, where the documented architecture changes;
5. `api.md`, where the exposed API contract changes.
