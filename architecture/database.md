IT Talent Platform — Database Architecture

Document: database.md
Version: 0.1.0
Status: Draft / MVP Database Architecture
Last updated: 2026-08-12

1. Purpose
This document defines the initial relational database architecture for the IT Talent Platform.

The database will use:
PostgreSQL as the primary database;
Prisma ORM for database access;
UUIDs as primary identifiers;
relational integrity through foreign keys;
indexes for frequently queried fields;
timestamps for auditable entities.

The database is designed around a skills-first recruitment model.

2. Database Principles

The database must follow these principles:
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

3. High-Level Entity Model

The initial database contains:

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

4. Core Tables

Initial tables:
users
companies
recruiters
candidates
jobs
skills
candidate_skills
job_requirements
matches

Additional tables will be added when functionality requires them.

5. UUID Strategy

All primary keys will use UUIDs.

Example:
id = "550e8400-e29b-41d4-a716-446655440000"

Advantages:
globally unique;
suitable for distributed systems;
avoids exposing sequential record counts;
suitable for future integrations.

PostgreSQL will use UUID-compatible types.
Prisma will generate UUIDs for new records.

6. Timestamp Strategy

Entities should use:
createdAt
updatedAt

where applicable.

All timestamps are stored in UTC.

Example:
createdAt = 2026-08-12T08:00:00Z

The frontend converts timestamps to the user's local timezone where appropriate.

7. User

The users table represents authentication and common identity information.

Conceptual model:

users
────────────────────────────
id
email
passwordHash
role
status
createdAt
updatedAt
Fields
Field	Type	Required	Description
id	UUID	yes	Primary key
email	string	yes	Unique login email
passwordHash	string	yes	Hashed password
role	enum	yes	User role
status	enum	yes	Account status
createdAt	timestamp	yes	Creation timestamp
updatedAt	timestamp	yes	Last update

Possible roles:

CANDIDATE
RECRUITER
ADMIN

Possible statuses:

ACTIVE
PENDING
SUSPENDED
DELETED

8. Company

The companies table represents organizations using the platform.

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

Possible fields:

Field	Type	Required
id	UUID	yes
name	string	yes
slug	string	yes
website	string	no
description	text	no
location	string	no
createdAt	timestamp	yes
updatedAt	timestamp	yes

slug should be unique.

9. Recruiter

A recruiter connects a User to a Company.

recruiters
────────────────────────────
id
userId
companyId
jobTitle
createdAt
updatedAt

Relationships:

User
 │
 └── Recruiter
       │
       └── Company

Constraints:

one user should have at most one recruiter profile in MVP;
a recruiter belongs to one company;
a company can have many recruiters.
10. Candidate

A candidate connects a User to professional profile data.

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

The candidate table should contain profile-level data.

Skills are stored separately.

11. Candidate Privacy

Candidate visibility is an important architectural concern.

The database should allow us to distinguish:

PUBLIC
RECRUITER_VISIBLE
PRIVATE

The exact visibility model will be finalized before implementation.

The frontend must never receive candidate information the authenticated user is not authorized to view.

12. Jobs

The jobs table represents vacancies.

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

Possible status:

DRAFT
PUBLISHED
PAUSED
CLOSED
ARCHIVED

Possible employment types:

FULL_TIME
PART_TIME
CONTRACT
FREELANCE
INTERNSHIP

Possible work modes:

REMOTE
HYBRID
ONSITE
FLEXIBLE
13. Job Relationships

A job belongs to:

one company;
one recruiter who created it.

A company can have many jobs.

A recruiter can create many jobs.

Company
   │
   └── Job
        │
        └── createdBy → Recruiter
14. Skill

Skills are central to the entire platform.

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
15. Skill Categories

Initial categories may include:

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

16. Skill Aliases

Because technical skills have many names, the platform should eventually support aliases.

Example:

React
 ├── React.js
 └── ReactJS

PostgreSQL
 └── Postgres

JavaScript
 └── JS

For MVP, aliases can initially be represented in a simple field or application-level normalization.

A dedicated skill_aliases table may be introduced when the vocabulary becomes large.

17. CandidateSkill

candidate_skills is a many-to-many relationship between candidates and skills.

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
18. Candidate Skill Proficiency

The MVP will use a simple numerical proficiency model.

Example:

1 = Beginner
2 = Basic
3 = Intermediate
4 = Advanced
5 = Expert

This should be treated as a product-level abstraction, not an objective industry certification.

The UI should explain what the levels mean.

19. Candidate Skill Source

A skill may originate from:

SELF_REPORTED
CV
AI_EXTRACTED
ASSESSMENT
VERIFIED
RECRUITER_CONFIRMED

This allows the platform to distinguish between:

"The candidate says they know Kubernetes."

and:

"The candidate passed a Kubernetes assessment."

20. Candidate Skill Confidence

AI-extracted skills may include a confidence score.

Example:

skill: React
confidence: 0.94

The range is:

0.0 → 1.0

This score indicates AI extraction confidence, not candidate proficiency.

These are two different concepts.

21. JobRequirement

Job requirements connect jobs to skills.

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
22. Required vs Preferred Skills

A job requirement can be:

REQUIRED

or:

PREFERRED

This distinction is important for matching.

A candidate missing a preferred skill should not necessarily be penalized as strongly as a candidate missing a required skill.

23. Match

The matches table stores calculated candidate/job compatibility.

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
24. Match Scores

All scores should use a consistent scale.

Recommended:

0 → 100

Example:

overallScore:       91
skillScore:         95
experienceScore:    90
locationScore:     100
salaryScore:        85
availabilityScore: 100
preferenceScore:    80

The exact calculation is owned by the backend matching engine.

25. Match Explanation

The platform should store structured match explanation data rather than only a text blob.

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

The exact database representation will be determined during Prisma implementation.

26. Match Lifecycle

A match may be generated automatically or manually.

Potential status:

CALCULATED
VIEWED
SHORTLISTED
REJECTED
CONTACTED
HIRED

Some of these statuses may be postponed until the application/recruitment workflow is implemented.

For MVP, the database may initially store only the calculated match.

27. CV Metadata

CV files should not be stored directly in PostgreSQL.

Instead, a future candidate_documents table should store metadata:

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

Possible processing statuses:

UPLOADED
PROCESSING
PROCESSED
FAILED

This table can be introduced when CV upload is implemented.

28. Soft Deletion

Not every entity should necessarily be physically deleted immediately.

Where appropriate, we may use:

deletedAt

This is particularly relevant for:

users;
candidates;
companies;
jobs.

However, soft deletion must not conflict with legal requirements to permanently delete personal data.

29. Index Strategy

Indexes should be added based on actual access patterns.

Initial candidates:

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

Composite indexes will be introduced where query patterns justify them.

30. Unique Constraints

Likely unique constraints:

users.email
companies.slug

For candidate skills:

(candidateId, skillId)

For job requirements:

(jobId, skillId)

This prevents duplicate relationships.

31. Referential Integrity

Foreign keys must enforce relationships.

For example:

candidate_skills.candidateId
        ↓
candidates.id

and:

candidate_skills.skillId
        ↓
skills.id

Deletion behavior must be defined deliberately.

We should avoid accidental cascading deletion of large amounts of data.

32. Transaction Strategy

Operations affecting multiple related entities should use database transactions where required.

Example:

Creating a job and its requirements:

BEGIN TRANSACTION

Create Job
Create JobRequirement #1
Create JobRequirement #2
Create JobRequirement #3

COMMIT

If one operation fails, the complete operation should roll back where atomicity is required.

33. Data Ownership

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
34. AI-generated Data

AI-generated data should be identifiable.

For example:

CandidateSkill
source = AI_EXTRACTED
confidence = 0.91

AI should not overwrite verified information without an explicit business rule.

Potential future model:

AI suggestion
      ↓
Human confirmation
      ↓
Verified data
35. Database and Matching Separation

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

We should not embed the complete matching algorithm in SQL.

36. Future Extensions

The database can later be extended with:

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

These are deliberately excluded from the initial schema unless implementation requires them.

37. Initial Entity Relationship Diagram

The conceptual MVP ERD:

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
38. Prisma Strategy

Prisma will be the database access layer.

The schema will live in:

it-talent-backend/prisma/schema.prisma

The documentation repository defines the intended model.

The actual Prisma schema is the implementation of that model.

If implementation requires a deviation, the documentation must be updated.

39. Migration Strategy

Database changes must use Prisma migrations.

We will not manually alter production database structures.

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

40. Database Development Environment

Local development should support PostgreSQL through Docker.

Conceptually:

Developer machine
      │
      └── Docker
            │
            └── PostgreSQL

This gives developers a reproducible database environment.

The exact Docker Compose setup will be implemented in the backend repository.

41. Production Database

The production database will be a managed PostgreSQL service.

The database provider is intentionally not fixed in this document.

Selection criteria:

PostgreSQL compatibility;
reasonable free/development tier;
backups;
security;
Vercel compatibility;
predictable pricing;
easy migration.

42. Database Security

Production database credentials must never be committed to GitHub.

Access should be controlled through environment variables.

Application users should receive only the permissions required by the application.

Database access should not be exposed directly to the public internet unless the selected managed provider requires it and appropriate security controls are configured.

43. Data Retention

Retention policies will be defined separately as the product requirements and GDPR analysis mature.

Potential categories:

Account data
Candidate profile
CV documents
Recruitment activity
Match history
Audit data
AI processing data

Each category may require a different retention period.

44. Database Version

This document defines:

Database Architecture v0.1.0

The schema is expected to evolve during implementation.

Changes affecting the conceptual model must be documented through:

updated database.md;
Prisma migration;
ADR when the architectural decision is significant.

45. Next Step

With architecture.md and database.md defined, the next technical document should be:

it-talent-docs/
└── architecture/
    ├── architecture.md
    ├── database.md        ← CURRENT
    ├── api.md             ← NEXT
    └── security.md