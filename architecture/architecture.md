# IT Talent Platform — System Architecture

**Document:** architecture.md  
**Version:** 0.1.1  
**Status:** Draft / Architecture Baseline  
**Last updated:** 2026-08-18

---

# 1. Purpose

The IT Talent Platform is a skills-based recruitment and talent marketplace focused on the IT industry.

The platform connects:

- IT companies and recruiters
- IT professionals
- job seekers, juniors and career switchers

The primary product goal is to provide better matching between IT vacancies and candidates by focusing on skills, experience, preferences and requirements rather than relying primarily on CV keyword matching.

The core proposition is:

> Find the IT professional who actually fits the job, not simply the candidate with the best-looking CV.

This document describes both:

1. the current technical implementation;
2. the target architecture and future product direction.

Not every component described in the target architecture has been implemented yet.

---

# 2. Implementation Status

To avoid confusion between the current implementation and the target architecture, this document uses the following status model.

| Status | Meaning |
|---|---|
| ✅ Implemented | Functionality currently exists in the codebase |
| 🟡 Foundation / Partial | Some implementation or database foundation exists, but the complete functionality is not available |
| 🔵 Planned / Roadmap | Part of the target architecture but not currently implemented |

The architecture and product vision may therefore describe functionality that is planned for future iterations.

---

# 3. Product Vision

The long-term vision is to build an intelligent IT talent marketplace in which:

- candidates maintain a skills-based professional profile;
- companies define vacancies in terms of skills and requirements;
- AI assists in extracting and normalizing skills;
- the matching engine identifies relevant candidates;
- recruiters receive transparent explanations for every match;
- professionals receive relevant career and job recommendations.

The platform should eventually support the complete IT talent lifecycle:

```text
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

4. Architectural Goals

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

5. Architectural Principles
5.1 Backend owns business logic

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
5.2 API-first

Frontend and backend communicate through a documented API.

The frontend must not directly access PostgreSQL.

Current target architecture:

Browser
   ↓
React + Vite
   ↓ HTTPS
NestJS API
   ↓
PostgreSQL
5.3 Skills-first

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

The database contains the foundation for this model, while the complete CandidateSkill, JobRequirement and matching APIs remain planned.

5.4 AI is an assistant, not the source of truth

AI may extract or interpret information, but AI output must be:

structured;
validated;
stored in appropriate domain models;
traceable where practical.

The application must not blindly persist arbitrary AI output.

AI functionality is currently planned and is not part of the current backend implementation.

5.5 Modular monolith

The initial backend is a modular monolith.

We will not start with microservices.

The application will contain clear domain modules inside one NestJS application.

If a module later becomes sufficiently complex or requires independent scaling, it may be extracted into a separate service.

6. High-Level Architecture
6.1 Current implementation architecture
                              INTERNET
                                  │
                                  ▼
                       ┌─────────────────────┐
                       │    React + Vite     │
                       │      Frontend       │
                       └──────────┬──────────┘
                                  │
                              HTTPS / REST
                                  │
                                  ▼
                       ┌─────────────────────┐
                       │       NestJS        │
                       │      Backend        │
                       │      REST API       │
                       └──────────┬──────────┘
                                  │
                                  ▼
                       ┌─────────────────────┐
                       │     PostgreSQL      │
                       │      Prisma ORM     │
                       └─────────────────────┘

The frontend is currently a React/Vite foundation and does not yet implement the complete product UI.

The backend currently contains the initial authentication, users, candidate-profile and skills functionality.

6.2 Target architecture

Future architecture may additionally include:

                       ┌─────────────────────┐
                       │      AI Services    │
                       │       LLM / AI      │
                       └──────────▲──────────┘
                                  │
                                  │
                       ┌──────────┴──────────┐
                       │       NestJS        │
                       │      Backend        │
                       └──────────┬──────────┘
                                  │
                ┌─────────────────┴─────────────────┐
                ▼                                   ▼
       ┌─────────────────┐                 ┌─────────────────┐
       │   PostgreSQL    │                 │ Future Services │
       │      Prisma     │                 │ Search / Queue  │
       └─────────────────┘                 └─────────────────┘

Future infrastructure may include:

OpenSearch;
object storage;
queues/workers;
AI services.

These components are planned and are not required for the current implementation.

7. Repository Architecture

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
8. Frontend Technology
8.1 Current implementation

The frontend currently uses:

React;
Vite;
TypeScript;
Oxlint.

The current frontend is a basic React/Vite application foundation.

The complete product UI has not yet been implemented.

8.2 Target architecture

The frontend is intended to use:

React;
Vite;
TypeScript;
a consistent component architecture;
client-side routing where required;
API-based communication with the NestJS backend.

Styling technology may be selected and formalized separately.

The frontend is intended to be deployable through Vercel.

9. Frontend Domain Structure

The target frontend will eventually be organized around domain functionality.

Possible structure:

src/
├── app/
├── components/
├── features/
│   ├── auth/
│   ├── candidate/
│   ├── recruiter/
│   ├── jobs/
│   └── matching/
├── lib/
└── tests/

The exact directory structure may evolve as implementation progresses.

The recruiter and candidate application areas are currently planned and are not yet implemented as complete product interfaces.

10. Backend Technology

The backend uses:

NestJS;
TypeScript;
Prisma;
PostgreSQL;
REST API.

The backend follows a modular monolith architecture.

11. Backend Modules
11.1 Currently implemented

The current backend contains functionality for:

src/
├── auth/
├── users/
├── skills/
└── common/

Candidate functionality currently exists as part of the user/profile flow.

11.2 Planned modules

The following domains are part of the target architecture but are not currently implemented as complete application modules:

companies/
recruiters/
jobs/
matching/
ai/

Future modules may include:

applications/
messaging/
assessments/
subscriptions/
notifications/

These will not be implemented until required.

12. Current Backend Functionality

The current implementation provides the following functionality.

12.1 Authentication
Implemented
user registration;
user login;
password hashing;
JWT-based authentication;
authentication guards;
role-based authorization foundation.
Partial / incomplete
account activation lifecycle.
Planned
refresh tokens;
logout/session invalidation;
complete session lifecycle.
12.2 Users
Implemented
authenticated current-user access;
role information;
user management functionality;
administrative user operations;
role-based access controls.

Additional authorization and response-contract improvements may be made during implementation hardening.

12.3 Candidate profile
Foundation / Partial

Candidate functionality currently exists through the authenticated user/profile flow.

The current implementation supports the initial candidate-profile foundation.

The complete target Candidate domain is broader and remains partially implemented.

Planned
complete candidate API;
advanced candidate skills;
work experience;
education;
certifications;
CV upload;
CV processing.
12.4 Skills
Implemented

The current backend provides Skills functionality including:

listing skills;
searching/filtering skills;
retrieving a skill;
creating skills;
updating skills;
deleting skills.

Additional validation, authorization and test coverage may be improved during implementation hardening.

12.5 Health
Implemented

The backend provides health functionality including database connectivity checking.

The health endpoint should remain lightweight and suitable for deployment monitoring.

13. Core Domain Model

The target domain entities are:

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

Important:

The domain model represents the target architecture. The presence of a database entity does not mean that a complete API or application module currently exists for that entity.

14. User Model

A User represents an authenticated person.

Current roles include:

CANDIDATE
RECRUITER
ADMIN

The initial authorization model is role-based.

Conceptually:

User
 ├── Candidate
 │
 ├── Recruiter
 │      └── Company
 │
 └── Admin

Recruiter/company ownership rules are part of the target authorization model and will be implemented when the corresponding domains are introduced.

15. Company Model

Status: 🔵 Planned / Roadmap

A company represents an organization using the platform.

A company can have multiple recruiters:

Company
│
├── Recruiter A
├── Recruiter B
└── Recruiter C

Company ownership and access rules will be enforced by the backend when this functionality is implemented.

16. Candidate Model

Status: 🟡 Foundation / Partial

A candidate represents an IT professional or job seeker.

The target Candidate model may include:

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

The current implementation provides only the initial candidate-profile foundation.

Sensitive personal information must be minimized.

17. Job Model

Status: 🔵 Planned / Roadmap

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

The job description may eventually be processed by the AI layer to generate structured requirements.

18. Skill Model

Status: ✅ Implemented

Skill is a normalized platform entity.

Conceptually:

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

19. CandidateSkill

Status: 🔵 Planned / Roadmap

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

CV;
PROFILE;
ASSESSMENT;
SELF_REPORTED;
VERIFIED;
AI_EXTRACTED.

The exact implementation is defined separately in database.md.

The existence of the domain model does not imply that the complete CandidateSkill API is currently available.

20. JobRequirement

Status: 🔵 Planned / Roadmap

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

This functionality will be implemented together with the Job domain.

21. AI Architecture

Status: 🔵 Planned / Roadmap

AI will initially perform two major functions.

21.1 CV skill extraction
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
21.2 Job requirement extraction
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

22. AI Data Boundary

The frontend must never communicate directly with an AI provider.

Target architecture:

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

AI functionality is not currently implemented.

23. Matching Engine

Status: 🔵 Planned / Roadmap

The matching engine will eventually calculate compatibility between a candidate and a job.

Initial conceptual factors:

Skill Match;
Experience Match;
Location Match;
Salary Match;
Availability Match;
Preference Match.

An initial conceptual weighting may be:

Skill Match          50%
Experience Match     15%
Location Match       10%
Salary Match         10%
Availability Match    5%
Preferences          10%

These values are initial product assumptions only.

They are not currently implemented as production matching logic.

The final weights will be tested against real recruiter feedback.

Weights must be configurable in the backend when the matching engine is implemented.

24. Match Transparency

Status: 🔵 Planned / Roadmap

A match must not be presented as an unexplained number.

Instead of:

91% match

the platform should eventually provide:

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

25. API Architecture

The API is versioned.

Initial version:

/api/v1
25.1 Currently implemented API areas

Authentication:

POST /api/v1/auth/register
POST /api/v1/auth/login

Current user:

GET /api/v1/users/me

Candidate profile foundation:

POST /api/v1/users/me/candidate

Skills:

GET /api/v1/skills
POST /api/v1/skills

The Skills API also provides the corresponding retrieval, update and deletion functionality.

25.2 Planned API areas

Authentication:

POST /api/v1/auth/refresh
POST /api/v1/auth/logout

Candidates:

GET /api/v1/candidates
GET /api/v1/candidates/:id

Companies:

GET /api/v1/companies/:id

Jobs:

POST /api/v1/jobs
GET /api/v1/jobs
GET /api/v1/jobs/:id
PUT /api/v1/jobs/:id

Matching:

POST /api/v1/jobs/:id/match
GET /api/v1/jobs/:id/matches
GET /api/v1/matches/:id

The API specification will be expanded in:

architecture/api.md

Only endpoints marked as implemented should be treated as currently available.

26. API Contract

The API is the contract between frontend and backend.

Frontend development must not depend on direct database structures.

The backend API should expose domain-level resources.

For example, the future frontend should request:

GET /api/v1/jobs/123/matches

rather than attempting to reproduce the matching algorithm itself.

27. Database

PostgreSQL is the primary relational database.

Prisma is used as the ORM.

The target domain entities include:

User
Company
Recruiter
Candidate
Job
Skill
CandidateSkill
JobRequirement
Match

The current database model may contain foundations for functionality that is not yet exposed through complete application APIs.

The complete database design is documented separately in:

architecture/database.md
28. Authentication

Status: 🟡 Foundation / Partial

Authentication is handled by the backend.

Currently implemented
registration;
login;
password hashing;
JWT authentication;
authenticated user access;
role-based authorization foundation.
Not yet fully implemented
complete account activation lifecycle;
refresh tokens;
logout/session invalidation.

The precise authentication lifecycle must be finalized before these additional features are implemented.

The system must support secure token handling and avoid exposing sensitive authentication data unnecessarily to browser-side JavaScript.

29. Authorization

Authentication answers:

Who are you?

Authorization answers:

What are you allowed to access?

Authorization must be enforced by the backend.

Current authorization includes role-based protection for implemented resources.

Future examples include:

Candidate → own profile
Recruiter → company jobs
Recruiter → candidates permitted by platform rules
Admin → administrative resources

A frontend UI restriction is not sufficient security.

30. Deployment Architecture
Target deployment architecture
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

Vercel is the intended deployment platform.

The actual production deployment configuration is separate from the architecture definition and must be verified independently.

The documentation repository is independent.

31. Development Workflow

The intended workflow is:

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

This represents the target development workflow.

Automated CI/CD must be implemented and verified before this workflow is considered fully operational.

Production deployments should originate from the protected main branch.

32. Git Branch Strategy

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

33. Environment Configuration

Environment-specific secrets must never be committed to GitHub.

Each repository should contain an appropriate:

.env.example

Local secret files such as:

.env
.env.local

must be included in .gitignore.

Frontend

The Vite frontend should use a Vite-compatible public environment variable for the backend API URL.

Example:

VITE_API_URL=
Backend

Example variables:

DATABASE_URL=
JWT_SECRET=
AI_API_KEY=

AI_API_KEY is required only when AI functionality is implemented.

Actual secret values must be configured through the appropriate environment management system.

34. Security Principles

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

Not every listed security control is currently implemented.

35. GDPR / Privacy

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

36. File and CV Storage

Status: 🔵 Planned / Roadmap

CVs and other uploaded documents should not initially be stored directly inside PostgreSQL as large binary objects unless there is a compelling reason.

The future architecture should use object storage:

Candidate
   │
   └── CV metadata
           │
           ▼
      Object Storage

For MVP development, the precise storage provider will be selected when CV upload functionality is implemented.

37. Search

PostgreSQL will initially be sufficient for basic queries.

A dedicated search engine such as OpenSearch may be introduced later when:

candidate volume increases;
full-text search becomes important;
advanced filtering is required;
semantic search is introduced.

Do not introduce OpenSearch prematurely.

38. Background Processing

Long-running tasks should eventually be moved to background jobs.

Potential background operations:

CV processing;
AI extraction;
bulk candidate matching;
email notifications;
search indexing;
analytics processing.

For MVP, synchronous processing may be used where acceptable.

A queue/worker architecture will be introduced when processing time or volume requires it.

39. Testing Strategy

Testing will exist at multiple levels.

Current status
Backend

Testing infrastructure exists for:

unit tests;
service tests;
API/e2e testing.

Current product test coverage is incomplete and still contains starter/template coverage that must be replaced with project-specific tests.

Frontend

Frontend testing is planned but the complete application test suite is not yet implemented.

Matching Engine

Matching requires dedicated tests when the matching engine is implemented because its output directly affects product value.

Example future test:

Candidate A
Job B


Expected:
Skill Match >= 90%
Overall Match >= 85%

The exact thresholds will be defined during implementation.

40. Current MVP Implementation Status

The current implementation is incremental.

40.1 Implemented
Backend
├── NestJS
├── TypeScript
├── Prisma
├── PostgreSQL
├── REST API
├── API versioning foundation
├── Authentication
│   ├── Register
│   └── Login
├── JWT authentication
├── Password hashing
├── Users
├── Role-based authorization foundation
├── Candidate profile foundation
├── Skills
└── Health/database check


Frontend
├── React
├── Vite
├── TypeScript
└── Oxlint
40.2 Foundation / Partial
Candidate profile
Authentication lifecycle
Database/domain foundations for future domains
Testing infrastructure
Deployment architecture
40.3 Planned / Roadmap
Company
Recruiter
Jobs
CandidateSkill API
JobRequirement API
Matching
Match transparency
AI
CV upload
CV skill extraction
Job requirement extraction
Complete frontend application
Refresh tokens
Logout/session invalidation
CI/CD
Advanced testing
41. Features Outside the Current Implementation

The following features are deliberately not considered implemented merely because they exist in the target architecture:

company management;
recruiter management;
job management;
matching;
AI extraction;
CV upload;
advanced candidate skills;
advanced technical assessments;
payments;
messaging;
mobile application;
ATS integrations;
LinkedIn automated sourcing;
complex recommendation engine;
advanced analytics;
enterprise SSO;
automated recruitment workflows.

Future functionality may be added based on product validation.

42. Future Product Evolution

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
43. Scalability Strategy

The platform will initially use:

React + Vite
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

44. Architectural Decision Records

Important decisions will be documented under:

decisions/

Initial ADRs:

ADR-001 — Separate frontend and backend repositories
ADR-002 — React + Vite for frontend
ADR-003 — NestJS for backend
ADR-004 — PostgreSQL as primary database
ADR-005 — Skills-first data model
ADR-006 — Modular monolith architecture
ADR-007 — Vercel deployment
ADR-008 — REST API

Each ADR should document:

Context;
Decision;
Alternatives;
Consequences.

ADR-002 must reflect the current React + Vite decision rather than the previous Next.js architecture.

45. Initial Architecture Decisions

The following decisions are considered accepted for Architecture v0.1:

Decision	Choice
Frontend	React + Vite
Language	TypeScript
Linting	Oxlint
Backend	NestJS
API	REST
API Version	/api/v1
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
46. Development Philosophy

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

47. Definition of MVP Success

The target MVP should eventually demonstrate one complete end-to-end workflow:

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

This is the target MVP workflow, not the current implementation.

The current implementation does not yet provide this complete end-to-end workflow.

If this workflow works reliably and recruiters find it genuinely useful, it provides a foundation for the commercial product.

48. Next Architecture Documents

The documentation repository should contain:

it-talent-docs/
│
├── README.md
│
├── architecture/
│   ├── architecture.md      ← CURRENT
│   ├── database.md
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

The following documents should use the same distinction between:

implemented;
foundation/partial;
planned/roadmap.

49. Document Status

Document: architecture.md
Version: 0.1.1
Status: Draft / Architecture Baseline
Last updated: 2026-08-18

This document describes the current implementation together with the accepted target architecture.

Future implementation work must be checked against this document and the more detailed API, database, security and product documentation.

