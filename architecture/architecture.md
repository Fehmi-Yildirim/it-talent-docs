IT Talent Platform — System Architecture

Document: architecture.md
Version: 0.1.0
Status: Draft / MVP Architecture
Last updated: 2026-08-12

1. Purpose

The IT Talent Platform is a skills-based recruitment and talent marketplace focused on the IT industry.

The platform connects:

IT companies and recruiters
IT professionals
job seekers, juniors and career switchers

The primary purpose of the MVP is to provide better matching between IT vacancies and candidates by focusing on skills, experience, preferences and requirements, rather than relying primarily on CV keyword matching.

The core proposition is:

Find the IT professional who actually fits the job, not simply the candidate with the best-looking CV.

2. Product Vision

The long-term vision is to build an intelligent IT talent marketplace in which:

candidates maintain a skills-based professional profile;
companies define vacancies in terms of skills and requirements;
AI assists in extracting and normalizing skills;
the matching engine identifies relevant candidates;
recruiters receive transparent explanations for every match;
professionals receive relevant career and job recommendations.

The platform should eventually support the complete IT talent lifecycle:

Profile
   ↓
Skills
   ↓
Career / Job Discovery
   ↓
Matching
   ↓
Assessment
   ↓
Recruitment
   ↓
Hiring
   ↓
Career Development

The MVP will implement only the first part of this lifecycle.

3. Architectural Goals

The architecture must be:

simple enough for rapid MVP development;
modular;
maintainable;
testable;
secure;
GDPR-conscious;
API-first;
cloud-ready;
suitable for GitHub-based development;
suitable for Vercel deployment;
extensible without premature microservices;
suitable for future AI functionality.

4. Architectural Principles
4.1 Backend owns business logic

The frontend must not contain critical business rules.

The backend is responsible for:

validation;
authorization;
matching;
scoring;
AI orchestration;
database operations;
security;
business rules.

The frontend is responsible primarily for:

presentation;
navigation;
user interaction;
client-side state;
form handling;
API consumption.
4.2 API-first

Frontend and backend communicate through a documented API.

The frontend must not directly access PostgreSQL.

Browser
   ↓
Next.js / React
   ↓ HTTPS
NestJS API
   ↓
PostgreSQL
4.3 Skills-first

Skills are a fundamental domain concept.

The platform should not depend on raw CV text or job-description keywords as its primary data model.

Instead:

Candidate
    ↓
CandidateSkill
    ↓
Skill

and:

Job
    ↓
JobRequirement
    ↓
Skill

This allows the platform to compare candidates and jobs using normalized skills.

4.4 AI is an assistant, not the source of truth

AI may extract or interpret information, but AI output must be:

structured;
validated;
stored in appropriate domain models;
traceable where practical.

The application must not blindly persist arbitrary AI output.

4.5 Modular monolith

The initial backend will be a modular monolith.

We will not start with microservices.

The application will contain clear domain modules inside one NestJS application.

If a module later becomes sufficiently complex or requires independent scaling, it may be extracted into a separate service.

5. High-Level Architecture
                              INTERNET
                                  │
                                  ▼
                       ┌─────────────────────┐
                       │      Next.js        │
                       │       React         │
                       │     Frontend        │
                       └──────────┬──────────┘
                                  │
                              HTTPS / REST
                                  │
                                  ▼
                       ┌─────────────────────┐
                       │       NestJS        │
                       │      Backend        │
                       │      REST API       │
                       └──────┬──────┬───────┘
                              │      │
                 ┌────────────┘      └─────────────┐
                 ▼                                  ▼
          ┌──────────────┐                   ┌──────────────┐
          │ PostgreSQL   │                   │ AI Services  │
          │              │                   │              │
          │ Prisma ORM   │                   │ LLM / AI     │
          └──────────────┘                   └──────────────┘

Future infrastructure may include:

                  ┌─────────────────┐
                  │ Search Engine   │
                  │ OpenSearch      │
                  └─────────────────┘

                  ┌─────────────────┐
                  │ Object Storage  │
                  │ CV / Documents  │
                  └─────────────────┘

                  ┌─────────────────┐
                  │ Queue / Worker  │
                  │ Background Jobs │
                  └─────────────────┘

These components are not required for MVP 1.0 unless a concrete requirement emerges.

6. Repository Architecture

The project consists of three Git repositories.

it-talent/
│
├── it-talent-frontend/
├── it-talent-backend/
└── it-talent-docs/

Corresponding GitHub repositories:

GitHub
│
├── it-talent-frontend
├── it-talent-backend
└── it-talent-docs

7. Frontend Technology

The frontend will use:

Next.js
React
TypeScript
Tailwind CSS

The frontend will be deployed through Vercel.

Expected architecture:

it-talent-frontend/
│
├── app/
├── components/
├── lib/
├── public/
├── tests/
├── .env.example
├── package.json
└── README.md

8. Frontend Domain Structure

The application will be organized around domain functionality.

Example:

app/
├── (auth)/
│   ├── login/
│   └── register/
│
├── recruiter/
│   ├── dashboard/
│   ├── jobs/
│   ├── candidates/
│   └── matches/
│
├── candidate/
│   ├── dashboard/
│   ├── profile/
│   ├── skills/
│   └── jobs/
│
└── jobs/

Shared components:

components/
├── ui/
├── forms/
├── jobs/
├── candidates/
├── matching/
└── layout/

Application utilities:

lib/
├── api/
├── auth/
├── validation/
└── utils/

The exact directory structure may evolve as implementation begins.

9. Backend Technology

The backend will use:

NestJS
TypeScript
Prisma
PostgreSQL
REST API

Expected structure:

it-talent-backend/
│
├── src/
├── prisma/
├── test/
├── .env.example
├── Dockerfile
├── package.json
└── README.md

10. Backend Modules

Initial modules:

src/
├── auth/
├── users/
├── companies/
├── candidates/
├── recruiters/
├── jobs/
├── skills/
├── matching/
├── ai/
└── common/

Future modules may include:

├── applications/
├── messaging/
├── assessments/
├── subscriptions/
└── notifications/

These will not be implemented until required.

11. Core Domain Model

The primary domain entities are:

User
Candidate
Recruiter
Company
Job
Skill
CandidateSkill
JobRequirement
Match

Conceptually:

                         ┌───────────┐
                         │   User    │
                         └─────┬─────┘
                               │
                  ┌────────────┴────────────┐
                  ▼                         ▼
             Candidate                  Recruiter
                  │                         │
                  │                         ▼
                  │                      Company
                  │                         │
                  │                         ▼
                  │                        Job
                  │                         │
                  ▼                         ▼
           CandidateSkill             JobRequirement
                  │                         │
                  └──────────┐   ┌──────────┘
                             ▼   ▼
                             Skill

Candidate ─────────────── Match ─────────────── Job

12. User Model

A User represents an authenticated person.

A user may have a role such as:

CANDIDATE
RECRUITER
ADMIN

The initial authorization model is role-based.

Example:

User
 ├── Candidate
 │
 ├── Recruiter
 │      └── Company
 │
 └── Admin

A recruiter must only have access to resources belonging to their company or resources explicitly made available to them.

13. Company Model

A company represents an organization using the platform.

A company can have multiple recruiters:

Company
│
├── Recruiter A
├── Recruiter B
└── Recruiter C

Company ownership and access rules will be enforced by the backend.

14. Candidate Model

A candidate represents an IT professional or job seeker.

Candidate data may include:

name;
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

Sensitive personal information must be minimized.

15. Job Model

A job represents a vacancy published by a company.

A job may include:

title;
description;
company;
location;
remote/hybrid/on-site preference;
salary range;
employment type;
experience requirements;
skills;
availability requirements.

The job description can be processed by the AI layer to generate structured requirements.

16. Skill Model

Skill is a normalized platform entity.

Example:

Skill
├── id
├── name
├── category
├── description
└── aliases

Example:

React

Aliases:
- React.js
- ReactJS

Different representations of the same skill should map to the same normalized skill where possible.

17. CandidateSkill

CandidateSkill represents a candidate's relationship with a skill.

Potential attributes:

CandidateSkill
├── candidateId
├── skillId
├── proficiencyLevel
├── yearsOfExperience
├── source
└── confidence

Possible sources:

CV
PROFILE
ASSESSMENT
SELF_REPORTED
VERIFIED
AI_EXTRACTED

The exact enumeration will be finalized in database.md.

18. JobRequirement

A JobRequirement represents a skill or requirement for a vacancy.

Potential attributes:

JobRequirement
├── jobId
├── skillId
├── minimumLevel
├── required
└── weight

Example:

Job: Senior React Developer

React
minimumLevel: 4
required: true
weight: 0.30

TypeScript
minimumLevel: 3
required: true
weight: 0.20

AWS
minimumLevel: 2
required: false
weight: 0.10

19. AI Architecture

AI will initially perform two major functions.

19.1 CV skill extraction
CV
 │
 ▼
AI extraction
 │
 ▼
Structured skills
 │
 ▼
Validation
 │
 ▼
CandidateSkill
19.2 Job requirement extraction
Job Description
 │
 ▼
AI extraction
 │
 ▼
Structured requirements
 │
 ▼
Validation
 │
 ▼
JobRequirement

AI should return structured data rather than unstructured prose wherever possible.

20. AI Data Boundary

The frontend must never communicate directly with an AI provider.

Instead:

Frontend
   ↓
Backend
   ↓
AI Provider
   ↓
Backend
   ↓
Database

API credentials remain exclusively on the backend.

Environment variables will be used for credentials.

21. Matching Engine

The matching engine calculates the compatibility between a candidate and a job.

Initial matching factors:

Skill Match
Experience Match
Location Match
Salary Match
Availability Match
Preference Match

An initial conceptual weighting:

Skill Match          50%
Experience Match     15%
Location Match       10%
Salary Match         10%
Availability Match    5%
Preferences           10%

These values are initial configuration only.

The final weights will be tested against real recruiter feedback.

Weights must be configurable in the backend.

22. Match Transparency

A match must not be presented as an unexplained number.

Instead of:

91% match

the platform should provide:

Overall Match: 91%

Skills              95%
Experience           90%
Location            100%
Salary               85%
Availability        100%
Preferences          80%

Strong matches:
✓ React
✓ TypeScript
✓ Node.js
✓ AWS

Potential gap:
△ Kubernetes

This transparency is important for recruiter trust.

23. API Architecture

The API will be versioned.

Initial version:

/api/v1

Authentication:

POST /api/v1/auth/register
POST /api/v1/auth/login
POST /api/v1/auth/refresh

GET /api/v1/users/me

Companies:

GET /api/v1/companies/:id

Jobs:

POST /api/v1/jobs
GET /api/v1/jobs
GET /api/v1/jobs/:id
PUT /api/v1/jobs/:id

Candidates:

GET /api/v1/candidates
GET /api/v1/candidates/:id

Skills:

GET /api/v1/skills
POST /api/v1/skills

Matching:

POST /api/v1/jobs/:id/match
GET /api/v1/jobs/:id/matches
GET /api/v1/matches/:id

The API specification will be expanded in architecture/api.md.

24. API Contract

The API is the contract between frontend and backend.
Frontend development must not depend on direct database structures.
The backend API should expose domain-level resources.

For example, the frontend should request:
GET /api/v1/jobs/123/matches
rather than attempting to reproduce the matching algorithm itself.

25. Database

PostgreSQL will be the primary relational database.
Prisma will be used as the ORM.
Initial entities:
User
Company
Recruiter
Candidate
Job
Skill
CandidateSkill
JobRequirement
Match

Additional entities will be introduced when required.
The complete database design will be documented separately in:

architecture/database.md

26. Authentication

Authentication will be handled by the backend.

The system will support:

registration;
login;
logout;
token refresh;
authenticated sessions;
role-based authorization.

The precise authentication implementation will be finalized during backend implementation.

The system must support secure token handling and avoid exposing sensitive authentication data unnecessarily to browser-side JavaScript.

27. Authorization

Authentication answers: Who are you?
Authorization answers: What are you allowed to access?
Authorization must be enforced by the backend.

Examples:
Candidate → own profile
Recruiter → company jobs
Recruiter → candidates permitted by platform rules
Admin → administrative resources

A frontend UI restriction is not sufficient security.

28. Deployment Architecture

The initial infrastructure:

                     GitHub
                        │
             ┌──────────┴──────────┐
             ▼                     ▼
       Frontend repo          Backend repo
             │                     │
             ▼                     ▼
          Vercel                Vercel
             │                     │
             └──────────┬──────────┘
                        │
                        ▼
                   PostgreSQL

The documentation repository is independent.

29. Development Workflow

The expected workflow is:

Feature branch
      ↓
Local development
      ↓
Git commit
      ↓
GitHub
      ↓
Vercel Preview
      ↓
Testing
      ↓
Pull Request
      ↓
Review
      ↓
Merge into main
      ↓
Production deployment

Production deployments must originate from the protected main branch.

30. Git Branch Strategy

Initial strategy:

main
└── feature/*

Examples:

feature/auth
feature/candidate-profile
feature/job-management
feature/matching
feature/recruiter-dashboard

We will avoid an unnecessarily complicated Git flow during the MVP.

31. Environment Configuration

Environment-specific secrets must never be committed to GitHub.

Each repository will contain:

.env.example

Local secret files such as:

.env
.env.local

must be included in .gitignore.

Example frontend variable:

NEXT_PUBLIC_API_URL=

Example backend variables:

DATABASE_URL=
JWT_SECRET=
AI_API_KEY=

Actual values must be configured through the appropriate environment management system.

32. Security Principles

Security is a first-class architectural concern.

The platform must include:

secure password hashing;
authentication protection;
role-based authorization;
server-side validation;
input sanitization where appropriate;
rate limiting;
CORS configuration;
secure headers;
secret management;
audit logging where appropriate;
controlled file access;
least-privilege access.

Security details will be documented in:

architecture/security.md
33. GDPR / Privacy

The platform will potentially process:

names;
contact information;
employment information;
CVs;
skills;
salary preferences;
location;
career information.

Therefore privacy must be considered from the beginning.

Principles include:

data minimization;
purpose limitation;
controlled access;
appropriate retention;
deletion capabilities;
export capabilities where required;
secure storage;
auditability.

The exact legal implementation must be reviewed before production launch.

34. File and CV Storage

CVs and other uploaded documents should not initially be stored directly inside PostgreSQL as large binary objects unless there is a compelling reason.

The future architecture should use object storage:

Candidate
   │
   └── CV metadata
           │
           ▼
      Object Storage

For MVP development, the precise storage provider will be selected when CV upload functionality is implemented.

35. Search

PostgreSQL will initially be sufficient for basic queries.

A dedicated search engine such as OpenSearch may be introduced later when:

candidate volume increases;
full-text search becomes important;
advanced filtering is required;
semantic search is introduced.

Do not introduce OpenSearch prematurely.

36. Background Processing

Long-running tasks should eventually be moved to background jobs.

Potential background operations:

CV processing
AI extraction
Bulk candidate matching
Email notifications
Search indexing
Analytics processing

For MVP, synchronous processing may be used where acceptable.

A queue/worker architecture will be introduced when processing time or volume requires it.

37. Testing Strategy

Testing will exist at multiple levels.

Backend
unit tests;
service tests;
integration tests;
API tests.
Frontend
component tests;
form validation tests;
end-to-end tests.
Matching Engine

The matching engine requires dedicated tests because its output directly affects product value.

Example:

Candidate A
Job B

Expected:
Skill Match >= 90%
Overall Match >= 85%

The exact thresholds will be defined during implementation.

38. Observability

As the platform develops, we should introduce:

structured application logs;
error tracking;
API performance monitoring;
database monitoring;
AI usage monitoring.

The MVP should avoid unnecessary infrastructure but must make production errors diagnosable.

39. MVP Scope
Recruiter

The MVP supports:

account creation;
company profile;
recruiter profile;
job creation;
job editing;
job viewing;
candidate discovery;
candidate profile viewing;
matching;
match score;
match explanation.
Candidate

The MVP supports:

account creation;
candidate profile;
CV upload;
skills;
experience;
salary preference;
location preference;
availability;
job discovery;
matching results.
AI

The MVP supports:

CV → skill extraction;
job description → requirement extraction;
skill normalization;
match explanation.
40. Features Explicitly Outside MVP 1.0

The following are deliberately postponed:

Payments
Messaging
Mobile application
Advanced technical assessments
ATS integrations
LinkedIn automated sourcing
Complex recommendation engine
Advanced analytics
Enterprise SSO
Automated recruitment workflows

These features may be added later based on user validation.

41. Future Product Evolution

The long-term platform may evolve into:

                    IT Talent Platform
                           │
          ┌────────────────┼────────────────┐
          ▼                ▼                ▼
      Recruiting        Careers          Learning
          │                │                │
          ▼                ▼                ▼
       Matching       Recommendations    Skill gaps
          │                │                │
          └────────────────┼────────────────┘
                           ▼
                    Skills Intelligence

Potential future features:

technical assessments;
verified skills;
career recommendations;
salary intelligence;
learning recommendations;
talent pools;
recruiter analytics;
AI sourcing;
ATS integrations;
candidate communication;
employer branding.
42. Scalability Strategy

The platform will initially use:

Next.js
+
NestJS modular monolith
+
PostgreSQL

Scaling will happen incrementally.

Possible future evolution:

                API
                 │
       ┌─────────┼─────────┐
       ▼         ▼         ▼
     Auth      Jobs     Matching
                           │
                           ▼
                      AI Service

If necessary, individual modules can later become independent services.

The architecture must therefore maintain clear domain boundaries even while operating as one application.

43. Architectural Decision Records

Important decisions will be documented under:

decisions/

Initial ADRs:

ADR-001 — Separate frontend and backend repositories
ADR-002 — Next.js for frontend
ADR-003 — NestJS for backend
ADR-004 — PostgreSQL as primary database
ADR-005 — Skills-first data model
ADR-006 — Modular monolith architecture
ADR-007 — Vercel deployment
ADR-008 — REST API

Each ADR should document:

Context
Decision
Alternatives
Consequences
44. Initial Architecture Decisions

The following decisions are considered accepted for Architecture v0.1:

Decision	Choice
Frontend	Next.js / React
Language	TypeScript
Styling	Tailwind CSS
Backend	NestJS
API	REST
Database	PostgreSQL
ORM	Prisma
Architecture	Modular monolith
AI	Backend-mediated AI services
Hosting	Vercel
Source control	GitHub
Repository model	Separate frontend/backend/docs
Matching	Skills-first
Search	PostgreSQL initially
File storage	External object storage when required
Microservices	Deferred
45. Development Philosophy

The project will follow:

Build the smallest useful system first.

We will prioritize:

validated product assumptions;
clean domain models;
working end-to-end flows;
recruiter usability;
candidate usability;
measurable matching quality.

We will avoid building infrastructure simply because it might be useful someday.

46. Definition of MVP Success

The MVP should demonstrate one complete end-to-end workflow:

Recruiter
   │
   ▼
Creates vacancy
   │
   ▼
AI extracts requirements
   │
   ▼
Candidate profiles exist
   │
   ▼
Candidate skills are normalized
   │
   ▼
Matching engine runs
   │
   ▼
Candidates ranked
   │
   ▼
Recruiter understands WHY
   │
   ▼
Recruiter selects candidate

If this workflow works reliably and recruiters find it genuinely useful, we have a foundation for the commercial product.

47. Next Architecture Documents

After this document, the documentation repository should contain:

it-talent-docs/
│
├── README.md
│
├── architecture/
│   ├── architecture.md      ← CURRENT
│   ├── database.md          ← NEXT
│   ├── api.md
│   └── security.md
│
├── product/
│   ├── vision.md
│   ├── requirements.md
│   └── roadmap.md
│
└── decisions/
    └── README.md

Status of architecture.md: COMPLETE — v0.1.0