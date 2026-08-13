IT Talent Platform — Product Roadmap

Document: roadmap.md
Version: 0.1.0
Status: MVP Development Roadmap
Last updated: 2026-08-12

1. Purpose

This roadmap defines the planned development sequence for the IT Talent Platform.

The roadmap translates the product requirements into technical and product-development phases.

The primary objective is to reach a working MVP as quickly as possible without compromising the architectural foundation.

2. Development Strategy

Development follows an incremental approach.

We do not build the entire system first and test it afterward.

Instead:

Design
  ↓
Implement
  ↓
Test
  ↓
Integrate
  ↓
Deploy
  ↓
Validate
  ↓
Next feature

Each phase should produce something that works.

3. Repository Structure

The project consists of three repositories:

it-talent-frontend
it-talent-backend
it-talent-docs

Conceptually:

GitHub
│
├── it-talent-frontend
│
├── it-talent-backend
│
└── it-talent-docs

The repositories remain independently deployable.

4. Technology Baseline
Frontend

Initial technology direction:

Next.js
React
TypeScript
Tailwind CSS
Backend

Initial technology direction:

NestJS
TypeScript
Prisma
PostgreSQL
Documentation
Markdown
Architecture Decision Records
API documentation
Product documentation
5. Environment Strategy

We will use at least:

Development
Staging
Production

For the initial project, staging may be lightweight.

Local development:

localhost

Preview deployments:

GitHub branch / pull request
        ↓
Vercel preview

Production:

main
 ↓
Production deployment
6. Phase 0 — Project Foundation

Priority: P0

Goal: establish repositories, tooling and development conventions.

Tasks
Documentation
 architecture documentation
 database architecture
 API architecture
 security architecture
 product vision
 product requirements
 roadmap
Repositories
 create GitHub repositories
 initialize frontend repository
 initialize backend repository
 initialize docs repository
Development standards
 .gitignore
 .editorconfig
 ESLint
 Prettier
 TypeScript configuration
 README
 environment examples
7. Phase 1 — Backend Foundation

Priority: P0

Goal: create a clean and testable NestJS backend.

Tasks
 initialize NestJS;
 configure TypeScript;
 configure environment variables;
 configure Prisma;
 configure PostgreSQL;
 establish module structure;
 add global validation;
 add exception handling;
 add logging;
 add health endpoint;
 configure CORS;
 configure API versioning;
 configure Swagger/OpenAPI.

Target API structure:

/api/v1
8. Phase 2 — Database Foundation

Priority: P0

Goal: implement the initial data model.

Initial entities:

User
CandidateProfile
RecruiterProfile
Company
Job
Skill
CandidateSkill
JobSkill
Match

Potential supporting entities:

CV
AuditLog
Session

The database schema must follow:

architecture/database.md
9. Phase 3 — Authentication

Priority: P0

Goal: secure user accounts.

Candidate
 registration;
 login;
 logout;
 authenticated session;
 password hashing.
Recruiter
 registration;
 login;
 logout;
 authenticated session.
Backend
 authentication guard;
 role guard;
 authorization policies;
 authentication tests.
10. Phase 4 — Frontend Foundation

Priority: P0

Goal: establish the React/Next.js application.

Tasks
 initialize Next.js;
 configure TypeScript;
 configure Tailwind;
 establish component architecture;
 establish layout;
 establish navigation;
 create API client;
 create authentication state;
 create error handling;
 create loading states.

Initial pages:

/
 /login
 /register
 /dashboard
11. Phase 5 — Candidate Profile

Priority: P0

Goal: allow candidates to create a meaningful profile.

Backend
 candidate profile CRUD;
 candidate skills;
 candidate preferences;
 visibility settings.
Frontend
 profile page;
 profile editor;
 skill selector;
 proficiency selector;
 preferences.

Example:

Candidate Profile

Name
Professional title
Summary

Skills
[React] [TypeScript] [Node.js]

Experience
6 years

Location
Amsterdam

Remote
Hybrid

Availability
Open to opportunities
12. Phase 6 — Recruiter & Company

Priority: P0

Goal: allow recruiters to operate within a company.

Backend
 recruiter profile;
 company CRUD;
 recruiter-company relationship;
 company authorization.
Frontend
 recruiter dashboard;
 company profile;
 recruiter settings.
13. Phase 7 — Jobs

Priority: P0

Goal: allow recruiters to create and publish jobs.

Backend
 job CRUD;
 job status;
 job skills;
 required skills;
 preferred skills;
 publishing;
 closing.
Frontend
Recruiter Dashboard
       │
       ├── Jobs
       │
       ├── Create Job
       │
       └── Job Detail
14. Phase 8 — Skill System

Priority: P0

Goal: establish a normalized skill vocabulary.

Tasks
 skill database;
 skill search;
 skill autocomplete;
 skill normalization;
 duplicate prevention;
 basic administration.

Initial seed data may include:

JavaScript
TypeScript
React
Next.js
Node.js
Python
Java
C#
.NET
PHP
Go
Rust
AWS
Azure
GCP
Docker
Kubernetes
Terraform
PostgreSQL
MySQL
MongoDB
Git
Linux

The list should grow based on actual product usage.

15. Phase 9 — Job Discovery

Priority: P0

Goal: allow candidates to find jobs.

Frontend:

Jobs
│
├── Search
├── Filters
└── Job cards

Filters:

skill;
location;
remote;
employment type;
salary.
16. Phase 10 — Matching Engine

Priority: P0

This is the core technical milestone.

The first version should be deterministic and understandable.

Conceptually:

Candidate
     │
     ├── Skills
     ├── Experience
     ├── Location
     ├── Salary
     └── Preferences
            │
            ▼
       Matching Engine
            │
            ▼
           Score
17. Initial Matching Algorithm

The first implementation should not depend entirely on AI.

Example weighting:

Skills         50%
Experience     20%
Location       10%
Salary         10%
Preferences    10%

Total:

100%

These weights are initial assumptions and should later be validated using real data.

18. Required vs Preferred Skills

Example:

Required:
React
TypeScript

Preferred:
AWS
Docker

Missing:

Required skill → strong penalty

Preferred skill → smaller penalty

This distinction is fundamental.

19. Match Explanation

The engine should return structured information such as:

{
  score: 91,
  skillScore: 95,
  experienceScore: 90,
  locationScore: 100,
  salaryScore: 85,
  preferenceScore: 80,
  strengths: [],
  gaps: []
}

The frontend converts this into a human-readable explanation.

20. Phase 11 — Candidate Match Dashboard

Priority: P0

Candidate dashboard:

Recommended Jobs

Senior React Developer
91% Match

Frontend Engineer
87% Match

Full Stack Developer
82% Match

Job detail:

91% Match

Why?

✓ React
✓ TypeScript
✓ AWS
✓ Amsterdam
✓ Hybrid

Potential gap:

○ Kubernetes
21. Phase 12 — Recruiter Candidate Matching

Priority: P0

Recruiter job page:

Senior React Developer

Matching Candidates

1. Candidate A   94%
2. Candidate B   91%
3. Candidate C   87%
4. Candidate D   83%

Recruiter can inspect the explanation.

22. Phase 13 — CV Upload

Priority: P0**

Candidate flow:

Upload CV
    ↓
Validate file
    ↓
Store file
    ↓
Extract text
    ↓
Process

Initially:

PDF only
23. Phase 14 — AI CV Analysis

Priority: P0

AI-assisted processing:

CV
 ↓
Text
 ↓
AI
 ↓
Structured profile suggestions
 ↓
Validation
 ↓
Candidate review

The AI must not directly modify trusted candidate information without appropriate validation/review.

24. Phase 15 — AI Job Analysis

Priority: P0

Recruiter enters:

Job description

AI suggests:

Skills
Experience
Seniority
Technologies

Recruiter reviews the result.

25. Phase 16 — Integration

Priority: P0

At this stage:

Frontend
     │
     ▼
Backend
     │
     ├── PostgreSQL
     │
     └── AI Provider

must work end-to-end.

26. Phase 17 — Security Hardening

Priority: P0

Before public testing:

 authorization review;
 IDOR testing;
 rate limiting;
 CORS;
 security headers;
 input validation;
 upload security;
 secret review;
 dependency audit;
 authentication testing.
27. Phase 18 — Automated Testing

Priority: P0

Testing layers:

Unit
 ↓
Integration
 ↓
API
 ↓
End-to-End

Priority test areas:

Authentication
Authorization
Candidate profile
Company isolation
Jobs
Skills
Matching
CV processing
28. Phase 19 — CI/CD

Priority: P0

GitHub Actions should eventually perform:

Pull Request
     │
     ├── Install
     ├── Lint
     ├── Type check
     ├── Unit tests
     ├── Build
     └── Security checks

Only successful builds should be eligible for merging.

29. Phase 20 — Deployment

Priority: P0

Initial deployment architecture:

GitHub
   │
   ├───────────────┐
   ▼               ▼
Frontend         Backend
Vercel           Hosting platform
   │               │
   └───────┬───────┘
           ▼
       PostgreSQL

The frontend can use Vercel preview deployments for pull requests.

The exact backend hosting provider will be selected during implementation based on cost, PostgreSQL support, networking and deployment simplicity.

30. Phase 21 — Closed Beta

Priority: P0**

Before public launch:

5–20 candidates
+
3–10 recruiters
+
realistic jobs

The objective is not scale.

The objective is learning.

31. Beta Questions

We need to discover:

Candidates
Do they complete their profile?
Do they trust the match score?
Are explanations understandable?
Are recommended jobs relevant?
Recruiters
Do they trust ranked candidates?
Does matching save time?
Are candidate profiles useful?
Would they pay for this?
32. Phase 22 — Matching Improvement

After beta, improve matching based on real feedback.

Possible improvements:

Skill synonyms
Skill relationships
Seniority
Experience relevance
Industry
Company preferences
Candidate preferences
Semantic similarity

Only after deterministic matching is working should more sophisticated AI/ML techniques be introduced.

33. Phase 23 — Monetization Experiment

Priority: P1

Once the product demonstrates value:

Free
   ↓
Professional
   ↓
Business

Possible paid features:

advanced candidate search;
higher search limits;
advanced matching;
talent pools;
analytics;
AI processing;
recruiter collaboration.
34. Phase 24 — Public MVP

The public MVP should contain:

Candidate
    ├── Account
    ├── Profile
    ├── Skills
    ├── CV
    └── Job Matching

Recruiter
    ├── Account
    ├── Company
    ├── Jobs
    └── Candidate Matching
35. Post-MVP Roadmap

Potential Phase 2 capabilities:

Applications
Messaging
Saved candidates
Saved jobs
Recruiter notes
Talent pools
Notifications
Email integration
36. Phase 3 — Talent Intelligence

Potential future capabilities:

Skills graph
Skill-gap analysis
Career recommendations
Salary intelligence
Talent trends
Market analytics
37. Phase 4 — Enterprise

Potential enterprise features:

SSO
SCIM
Advanced RBAC
Audit exports
Enterprise analytics
ATS integrations
HRIS integrations
API access
38. Phase 5 — Marketplace

Long-term vision:

Candidate
     │
     ▼
Talent Marketplace
     ▲
     │
Recruiter / Company

Potential capabilities:

direct recruitment;
talent pools;
premium discovery;
recruitment services;
partner ecosystem.
39. Roadmap Principles

The project follows five rules.

Rule 1 — Build the smallest useful product

Do not build future functionality prematurely.

Rule 2 — Validate the matching hypothesis

Matching is the core product.

Rule 3 — Prefer deterministic systems first

AI should enhance the product, not hide weak business logic.

Rule 4 — Deploy early

The system should run online long before the product is "finished."

Rule 5 — Real users determine priorities

After the first beta, roadmap priorities should be adjusted based on actual usage.

40. High-Level Timeline

The exact dates will depend on development speed, but the sequence is:

Foundation
    ↓
Backend
    ↓
Database
    ↓
Authentication
    ↓
Frontend
    ↓
Candidate
    ↓
Recruiter
    ↓
Jobs
    ↓
Skills
    ↓
Matching
    ↓
AI
    ↓
Integration
    ↓
Security
    ↓
Testing
    ↓
Deployment
    ↓
Beta
41. MVP Critical Path

The critical path is:

Database
   ↓
Authentication
   ↓
Candidate Profile
   ↓
Recruiter / Company
   ↓
Job
   ↓
Skills
   ↓
Matching
   ↓
Frontend
   ↓
End-to-End

CV/AI functionality is important, but the product should still be demonstrable without AI.

That is a deliberate architectural decision.

42. First Demonstrable Version

Our first meaningful demo should be:

Candidate
   │
   ├── creates profile
   └── adds skills
          │
          ▼
       Database
          ▲
          │
Recruiter
   │
   ├── creates company
   └── creates job
          │
          ▼
     Required Skills
          │
          ▼
    Matching Engine
          │
          ▼
      Match: 91%

If this works, we already have a real product prototype.

Everything else builds on top of this.

43. Current Status
Architecture       ████████████████████ 100%
Product Vision     ████████████████████ 100%
Requirements       ████████████████████ 100%
Roadmap            ████████████████████ 100%

Repositories       ░░░░░░░░░░░░░░░░░░░░   0%
Backend             ░░░░░░░░░░░░░░░░░░░░   0%
Frontend            ░░░░░░░░░░░░░░░░░░░░   0%
Database            ░░░░░░░░░░░░░░░░░░░░   0%
Matching            ░░░░░░░░░░░░░░░░░░░░   0%
AI                  ░░░░░░░░░░░░░░░░░░░░   0%
44. Next Step

De productdocumentatie is hiermee voor de MVP in principe compleet.

Onze documentatiestructuur:

it-talent-docs/
│
├── README.md
│
├── architecture/
│   ├── architecture.md
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