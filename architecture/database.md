# IT Talent Platform — Database Architecture

**Document:** database.md  
**Version:** 0.1.1  
**Status:** Draft / Database Architecture Baseline  
**Last updated:** 2026-08-18

---

# 1. Purpose

This document defines the database architecture for the IT Talent Platform.

The database uses:

- PostgreSQL as the primary relational database;
- Prisma ORM for database access;
- UUIDs as primary identifiers where defined by the implementation;
- relational integrity through foreign keys;
- indexes for frequently queried fields;
- timestamps for auditable entities.

The database is designed around a skills-first recruitment model.

This document distinguishes between:

1. the current Prisma/database implementation;
2. domain foundations already present in the database;
3. planned database structures required by future functionality.

The presence of a conceptual entity in this document does not necessarily mean that the corresponding application functionality or API has already been implemented.

---

# 2. Implementation Status

The following status model is used throughout this document.

| Status | Meaning |
|---|---|
| ✅ Implemented | Currently represented in the active Prisma/database implementation |
| 🟡 Foundation / Partial | Database/domain foundation exists, but complete functionality is not implemented |
| 🔵 Planned / Roadmap | Target architecture only; not currently implemented |

The Prisma schema in:

```text
it-talent-backend/prisma/schema.prisma

3. Database Principles

The database should follow these principles:

Normalize core business data.
Avoid storing derived information unnecessarily.
Keep business relationships explicit.
Use foreign keys for relational integrity.
Use indexes based on actual query patterns.
Keep AI-generated information distinguishable from verified information.
Minimize personal data.
Avoid storing large binary files directly in PostgreSQL.
Use UTC for persisted timestamps.
Keep soft deletion available where business/legal requirements require it.
Keep database concerns separate from business logic.
Do not move the complete matching algorithm into SQL.
4. Current Database Scope

The current implementation is focused on the initial platform foundation.

Current implemented database/domain areas include:

User
Skill
Candidate foundation
Authentication-related data

The target architecture additionally includes:

Company
Recruiter
Job
CandidateSkill
JobRequirement
Match

These additional entities are part of the target domain model and should not automatically be considered implemented merely because they are described in this document.

5. High-Level Target Entity Model

The target domain model is:

User
 │
 ├───────────────┐
 │               │
 ▼               ▼
Candidate      Recruiter
 │               │
 │               ▼
 │            Company
 │               │
 │               ▼
 │              Job
 │               │
 │               ▼
 │        JobRequirement
 │               │
 ▼               ▼
CandidateSkill ── Skill
 │
 ▼
Match
 │
 ▼
Job

This is the target domain architecture.

The current implementation does not yet expose all relationships represented above.

6. Core Tables
6.1 Current / Foundation

The current database implementation contains the initial authentication/user and skills foundation.

Relevant domain concepts include:

User
Skill
Candidate

Candidate functionality is currently partial and connected to the authenticated user/profile flow.

6.2 Planned

The following tables/entities belong to the target recruitment model:

companies
recruiters
jobs
candidate_skills
job_requirements
matches

Additional tables will be introduced only when the corresponding functionality is implemented.

7. UUID Strategy

Primary identifiers use UUIDs where defined by the Prisma implementation.

Example:

550e8400-e29b-41d4-a716-446655440000

Advantages include:

globally unique identifiers;
suitability for distributed systems;
reduced exposure of sequential record counts;
suitability for future integrations.

The actual Prisma schema is authoritative regarding the identifier type and generation strategy.

8. Timestamp Strategy

Entities should use:

createdAt
updatedAt

where applicable.

Persisted timestamps should use UTC.

Example:

2026-08-18T08:00:00Z

The frontend is responsible for displaying timestamps in the user's local timezone where appropriate.

The actual presence of timestamp fields is determined by the Prisma schema.

9. User

Status: ✅ Implemented

The User entity represents authentication and common identity information.

Conceptually:

users
────────────────────────────
id
email
passwordHash
role
status
createdAt
updatedAt

The actual fields and enum values are defined by:

it-talent-backend/prisma/schema.prisma

The current implementation supports the initial authentication and authorization foundation.

Possible roles in the platform domain include:

CANDIDATE
RECRUITER
ADMIN

Account status is part of the authentication lifecycle.

The exact values must always match the Prisma implementation.

10. Company

Status: 🔵 Planned / Roadmap

A company represents an organization using the platform.

Target model:

companies
────────────────────────────
id
name
slug
website
description
location
createdAt
updatedAt

Potential relationships:

Company
   │
   ├── Recruiter
   │
   └── Job

Company functionality is not currently implemented as a complete application domain.

11. Recruiter

Status: 🔵 Planned / Roadmap

A recruiter connects a User to a Company.

Target model:

recruiters
────────────────────────────
id
userId
companyId
jobTitle
createdAt
updatedAt

Target relationships:

User
 │
 └── Recruiter
       │
       └── Company

Target constraints:

one user should have at most one recruiter profile in the MVP;
a recruiter belongs to one company;
a company can have many recruiters.

These constraints will be finalized when recruiter functionality is implemented.

12. Candidate

Status: 🟡 Foundation / Partial

A Candidate connects a User to professional profile data.

Target model:

candidates
────────────────────────────
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

The current implementation provides the initial candidate-profile foundation.

The complete target Candidate model is not yet implemented.

Future candidate information may include:

professional title;
summary;
location;
work preferences;
salary preferences;
availability;
work experience;
education;
certifications;
skills;
CV metadata.

Skills should remain separated from general profile data.

13. Candidate Privacy

Status: 🔵 Planned / Architecture Requirement

Candidate visibility is an important architectural concern.

The target model may distinguish:

PUBLIC
RECRUITER_VISIBLE
PRIVATE

The exact visibility model will be finalized when recruiter/candidate discovery functionality is implemented.

The backend must never return candidate information that the authenticated user is not authorized to view.

14. Jobs

Status: 🔵 Planned / Roadmap

The jobs entity represents vacancies.

Target model:

jobs
────────────────────────────
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

Potential statuses:

DRAFT
PUBLISHED
PAUSED
CLOSED
ARCHIVED

Potential employment types:

FULL_TIME
PART_TIME
CONTRACT
FREELANCE
INTERNSHIP

Potential work modes:

REMOTE
HYBRID
ONSITE
FLEXIBLE

These values are target-domain definitions and must be finalized during implementation.

15. Job Relationships

The target model is:

Company
   │
   └── Job
        │
        └── createdBy → Recruiter

A job belongs to:

one company;
one recruiter who created it.

A company can have many jobs.

A recruiter can create many jobs.

This relationship is planned and is not currently exposed through a complete Jobs API.

16. Skill

Status: ✅ Implemented

Skills are central to the platform.

Conceptually:

skills
────────────────────────────
id
name
slug
category
description
createdAt
updatedAt

Example:

name: React
slug: react
category: FRONTEND

The current backend provides Skills functionality.

The actual fields, constraints and enum values are defined by the Prisma schema.

17. Skill Categories

Status: 🟡 Foundation / Partial

The target architecture may use categories such as:

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

These categories should remain extensible.

Only categories actually defined in the current Prisma implementation should be considered active.

18. Skill Aliases

Status: 🔵 Planned / Roadmap

Technical skills often have multiple representations.

Example:

React
 ├── React.js
 └── ReactJS


PostgreSQL
 └── Postgres


JavaScript
 └── JS

The platform should eventually normalize equivalent representations.

For the initial implementation, application-level normalization may be sufficient.

A dedicated:

skill_aliases

table may be introduced when the vocabulary becomes large enough to justify it.

19. CandidateSkill

Status: 🔵 Planned / Roadmap

CandidateSkill represents a many-to-many relationship between candidates and skills.

Target model:

candidate_skills
────────────────────────────
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

Relationships:

Candidate
    │
    └── CandidateSkill ─── Skill

The current implementation does not yet provide the complete CandidateSkill domain/API described here.

20. Candidate Skill Proficiency

Status: 🔵 Planned / Product Definition

The target MVP may use a numerical proficiency model:

1 = Beginner
2 = Basic
3 = Intermediate
4 = Advanced
5 = Expert

This should be treated as a product-level abstraction, not as an objective industry certification.

The UI should explain what the levels mean.

The exact representation must be finalized before CandidateSkill is implemented.

21. Candidate Skill Source

Status: 🔵 Planned / Roadmap

A skill may eventually originate from:

SELF_REPORTED
CV
AI_EXTRACTED
ASSESSMENT
VERIFIED
RECRUITER_CONFIRMED

This allows the platform to distinguish between:

The candidate says they know Kubernetes.

and:

The candidate passed a Kubernetes assessment.

The exact enum will be finalized during implementation.

22. Candidate Skill Confidence

Status: 🔵 Planned / Roadmap

AI-extracted skills may contain a confidence score.

Example:

skill: React
confidence: 0.94

The range is:

0.0 → 1.0

This represents AI extraction confidence, not candidate proficiency.

These are separate concepts.

23. JobRequirement

Status: 🔵 Planned / Roadmap

Job requirements connect jobs to skills.

Target model:

job_requirements
────────────────────────────
id
jobId
skillId
minimumLevel
required
weight
createdAt
updatedAt

Example:

React
minimumLevel: 4
required: true
weight: 0.30

The complete entity will be implemented together with Jobs and the matching domain.

24. Required vs Preferred Skills

Status: 🔵 Planned / Product Definition

A job requirement can eventually be classified as:

REQUIRED

or:

PREFERRED

A candidate missing a preferred skill should not necessarily be penalized as strongly as a candidate missing a required skill.

The exact representation must be finalized in the JobRequirement implementation.

25. Match

Status: 🔵 Planned / Roadmap

The matches entity will store calculated candidate/job compatibility.

Target model:

matches
────────────────────────────
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

Conceptually:

Candidate
     │
     └──────── Match ──────── Job

The current implementation does not contain the complete matching engine or Match application functionality.

26. Match Scores

Status: 🔵 Planned / Roadmap

The target scoring model uses a consistent scale:

0 → 100

Example:

overallScore:       91
skillScore:         95
experienceScore:    90
locationScore:     100
salaryScore:        85
availabilityScore: 100
preferenceScore:    80

The exact calculation belongs to backend application logic.

The database should store results, not contain the complete matching algorithm.

27. Match Explanation

Status: 🔵 Planned / Roadmap

The platform should eventually store structured match explanation data rather than only an unstructured text blob.

Potential structure:

strengths:
- React
- TypeScript
- AWS


gaps:
- Kubernetes


notes:
- Salary range overlaps
- Candidate available within required period

The exact PostgreSQL/Prisma representation will be determined when the matching functionality is implemented.

28. Match Lifecycle

Status: 🔵 Planned / Roadmap

A match may eventually have a lifecycle such as:

CALCULATED
VIEWED
SHORTLISTED
REJECTED
CONTACTED
HIRED

Some of these states belong to the future recruitment/application workflow.

The initial Match implementation may store only the calculated result.

29. CV Metadata

Status: 🔵 Planned / Roadmap

CV files should not be stored directly in PostgreSQL.

A future candidate_documents table may store metadata:

candidate_documents
────────────────────────────
id
candidateId
fileName
mimeType
storageKey
fileSize
uploadedAt
processedAt
status

The actual file will reside in object storage.

Potential processing statuses:

UPLOADED
PROCESSING
PROCESSED
FAILED

This table should only be introduced when CV upload functionality is implemented.

30. Soft Deletion

Status: 🟡 Architecture Requirement

Not every entity should necessarily be physically deleted immediately.

Where appropriate, the platform may use:

deletedAt

Potentially relevant entities include:

users;
candidates;
companies;
jobs.

However, soft deletion must not conflict with legal requirements to permanently delete personal data.

Soft deletion should therefore be introduced deliberately rather than automatically on every table.

31. Index Strategy

Indexes should be added based on actual query patterns.

Potential indexes include:

users.email
companies.slug
jobs.companyId
jobs.status
jobs.createdAt
candidate_skills.candidateId
candidate_skills.skillId
job_requirements.jobId
job_requirements.skillId
matches.candidateId
matches.jobId
matches.overallScore

Only indexes that correspond to actual implemented fields and query patterns should be added to the Prisma schema.

Composite indexes should be introduced when query patterns justify them.

32. Unique Constraints

Target unique constraints include:

users.email
companies.slug

For CandidateSkill:

(candidateId, skillId)

For JobRequirement:

(jobId, skillId)

These relationship constraints prevent duplicate relationships.

They should only be added when the corresponding tables are implemented.

33. Referential Integrity

Foreign keys must enforce relationships.

Example:

candidate_skills.candidateId
        ↓
candidates.id

and:

candidate_skills.skillId
        ↓
skills.id

Deletion behavior must be defined deliberately.

The application should avoid accidental cascading deletion of large amounts of personal or recruitment data.

The exact Prisma onDelete behavior is part of the implementation and should be documented when the corresponding relationships are introduced.

34. Transaction Strategy

Operations affecting multiple related entities should use database transactions where atomicity is required.

Future example:

BEGIN TRANSACTION


Create Job
Create JobRequirement #1
Create JobRequirement #2
Create JobRequirement #3


COMMIT

If one operation fails, the complete operation should roll back when atomicity is required.

Transactions are application-level behavior implemented through Prisma.

35. Data Ownership

Data ownership follows the domain model.

Candidate

Owns:

candidate profile;
candidate skills;
candidate preferences;
candidate documents.
Company

Owns:

company profile;
company jobs;
recruiter relationships.
Recruiter

Acts on behalf of a company.

Platform

Owns:

normalized skills;
matching configuration;
system-level metadata.

These ownership rules become enforceable when the corresponding domains are implemented.

36. AI-generated Data

Status: 🔵 Planned / Roadmap

AI-generated data should be identifiable.

Example:

CandidateSkill
source = AI_EXTRACTED
confidence = 0.91

AI should not overwrite verified information without an explicit business rule.

Target flow:

AI suggestion
      ↓
Validation / Human confirmation
      ↓
Verified data

This distinction is particularly important for candidate skills and future matching.

37. Database and Matching Separation

The database stores the data required by the matching engine.

The matching engine itself remains application logic.

PostgreSQL
    │
    ▼
Candidate + Skills
Job + Requirements
    │
    ▼
Matching Service
    │
    ▼
Match

The complete matching algorithm must not be embedded in SQL.

38. Future Extensions

Status: 🔵 Planned / Roadmap

The database may later be extended with:

applications
candidate_documents
assessments
assessment_results
messages
notifications
subscriptions
payments
audit_logs
skill_aliases
skill_taxonomy
candidate_preferences
job_preferences

These are deliberately excluded from the current implementation unless a concrete requirement requires them.

39. Current Database Status

The current database implementation should be understood as an incremental foundation rather than the complete target domain model.

Implemented
User
Skill
Authentication-related persistence
Initial Candidate/profile persistence
Foundation / Partial
Candidate domain
Role/status model
Database relationships required by current user/profile flows
Planned
Company
Recruiter
Job
CandidateSkill
JobRequirement
Match
Candidate documents
AI-generated skill data
Matching data
Recruitment/application data

The Prisma schema is the authoritative source for what is actually implemented.

40. Initial Entity Relationship Diagram

The target MVP ERD is:

┌──────────────┐
│    users     │
└──────┬───────┘
       │
   ┌───┴──────────────┐
   │                  │
   ▼                  ▼
┌──────────────┐  ┌──────────────┐
│  candidates  │  │  recruiters  │
└──────┬───────┘  └──────┬───────┘
       │                 │
       │                 ▼
       │          ┌──────────────┐
       │          │  companies   │
       │          └──────┬───────┘
       │                 │
       │                 ▼
       │          ┌──────────────┐
       │          │     jobs     │
       │          └──────┬───────┘
       │                 │
       │                 ▼
       │          ┌──────────────────┐
       │          │ job_requirements │
       │          └────────┬─────────┘
       │                   │
       ▼                   ▼
┌────────────────┐   ┌──────────────┐
│candidate_skills│──▶│    skills    │
└───────┬────────┘   └──────────────┘
        │
        ▼
┌──────────────────────┐
│       matches        │
└──────────────────────┘
        ▲
        │
        └──────── jobs

This diagram represents the target domain architecture, not necessarily the current database schema.

41. Prisma Strategy

Prisma is the database access layer.

The schema lives in:

it-talent-backend/prisma/schema.prisma

The documentation repository describes the intended architecture.

The actual Prisma schema is the implementation source of truth.

If implementation requires a deliberate deviation from this document:

update the Prisma schema;
update database.md;
update an ADR when the deviation represents a significant architectural decision.
42. Migration Strategy

Database changes must use Prisma migrations.

Production database structures must not be manually altered without an appropriate migration.

Expected workflow:

Change Prisma schema
        ↓
Create migration
        ↓
Run tests
        ↓
Commit migration
        ↓
Deploy

Migration files must be version-controlled.

43. Database Development Environment

Local development should support PostgreSQL through Docker or another reproducible local PostgreSQL environment.

Conceptually:

Developer machine
      │
      └── Docker
            │
            └── PostgreSQL

The exact Docker Compose/database setup is defined by the backend repository.

44. Production Database

The production database will use a managed PostgreSQL service.

The provider is intentionally not fixed in this document.

Selection criteria include:

PostgreSQL compatibility;
development/free-tier suitability;
backups;
security;
Vercel compatibility;
predictable pricing;
easy migration.

The production provider should be documented separately once selected.

45. Database Security

Production database credentials must never be committed to GitHub.

Access should be controlled through environment variables.

Application users should receive only the permissions required by the application.

Database access should not be exposed directly to the public internet unless the selected managed provider requires it and appropriate security controls are configured.

Database credentials must remain backend-only.

46. Data Retention

Retention policies will be defined separately as product requirements and GDPR analysis mature.

Potential categories include:

Account data
Candidate profile
CV documents
Recruitment activity
Match history
Audit data
AI processing data

Each category may require a different retention period.

47. Database Version

This document defines:

Database Architecture v0.1.1

The schema is expected to evolve during implementation.

Changes affecting the conceptual model must be documented through:

updated database.md;
Prisma migration;
ADR when the architectural decision is significant.
48. Source of Truth

For database implementation, the following hierarchy applies:

1. Prisma schema
        ↓
2. Database migrations
        ↓
3. Backend implementation
        ↓
4. Documentation

The documentation must describe the implementation accurately.

If documentation and implementation disagree, the discrepancy must be identified and resolved rather than silently assuming that the documentation is correct.

49. Next Step

The next technical document should be:

it-talent-docs/
└── architecture/
    ├── architecture.md
    ├── database.md        ← CURRENT
    ├── api.md             ← NEXT
    └── security.md

The next review should compare architecture/api.md directly against the actual NestJS controllers and routes.

The same status model must be used:

✅ Implemented
🟡 Foundation / Partial
🔵 Planned / Roadmap
50. Document Status

Document: database.md
Version: 0.1.1
Status: Draft / Database Architecture Baseline
Last updated: 2026-08-18

This document describes the current database foundation together with the target database architecture.

Future database changes must be reflected in the Prisma schema, migrations and this document.