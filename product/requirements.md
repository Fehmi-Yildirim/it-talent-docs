T Talent Platform — Product Requirements

Document: requirements.md
Version: 0.1.0
Status: Draft / MVP Requirements
Last updated: 2026-08-12

1. Purpose

This document defines the functional and non-functional requirements for the initial IT Talent Platform MVP.

The requirements translate the product vision into implementable functionality.

The MVP must validate the core proposition:

Can the platform produce useful, explainable matches between IT professionals and IT jobs?

2. Scope

The MVP consists of four primary domains:

┌─────────────────────────────────────┐
│          IT Talent Platform         │
├─────────────────────────────────────┤
│                                     │
│  Candidate Experience               │
│                                     │
│  Recruiter Experience               │
│                                     │
│  Job Management                     │
│                                     │
│  Skill-Based Matching               │
│                                     │
└─────────────────────────────────────┘

Supporting domains:

authentication;
company management;
skill management;
CV processing;
AI-assisted extraction;
security;
basic administration.
3. Requirement Priorities

Requirements use the following priority model.

Priority	Meaning
P0	Mandatory for MVP
P1	Important, but MVP can launch without it
P2	Future functionality
P3	Explicitly outside current scope
4. User Roles

The MVP supports:

CANDIDATE
RECRUITER
ADMIN
5. Candidate Requirements
CAND-001 — Candidate Registration

Priority: P0

A candidate must be able to create an account using:

email;
password.

Acceptance criteria:

valid email required;
password required;
duplicate email rejected;
password stored securely;
user receives authenticated session after successful registration where appropriate.
CAND-002 — Candidate Login

Priority: P0

A candidate must be able to log in.

Acceptance criteria:

valid credentials authenticate successfully;
invalid credentials return a safe error;
password is never returned;
session/token is securely established.
CAND-003 — Candidate Profile

Priority: P0

A candidate must be able to create and edit a professional profile.

Minimum profile:

Name
Professional headline
Summary
Location
Salary expectation
Currency
Availability
Remote preference
CAND-004 — Candidate Skills

Priority: P0

A candidate must be able to add skills.

Each skill may contain:

Skill
Proficiency
Years of experience

Example:

React
Advanced
5 years
CAND-005 — Candidate Skill Editing

Priority: P0

Candidates must be able to:

add;
edit;
remove skills.

Duplicate skills must not be allowed.

CAND-006 — Candidate Skill Source

Priority: P1

The system should distinguish between:

SELF_REPORTED
AI_EXTRACTED
VERIFIED

This is important for future trust and matching quality.

CAND-007 — Candidate CV Upload

Priority: P0

A candidate must be able to upload a CV.

Initial supported format:

PDF

Possible future formats:

DOCX
TXT

The system must enforce:

file size limit;
allowed file type;
secure storage;
authorization.
CAND-008 — CV Processing

Priority: P0

After CV upload, the system should process the document.

Initial flow:

CV
 ↓
Text extraction
 ↓
AI analysis
 ↓
Skill extraction
 ↓
Skill normalization
 ↓
Candidate profile suggestion
CAND-009 — Candidate Confirmation of AI Data

Priority: P0

AI-generated information should not silently overwrite candidate information.

The candidate should eventually be able to review extracted skills.

Example:

AI detected:

✓ React
✓ TypeScript
✓ AWS
? Kubernetes

Candidate can:

Accept
Reject
Edit
CAND-010 — Candidate Job Discovery

Priority: P0

Candidates must be able to browse published jobs.

Minimum filters:

location;
remote mode;
employment type;
skill;
salary where available.
CAND-011 — Candidate Match Recommendations

Priority: P0

Candidates should receive jobs ranked by compatibility.

Example:

Senior React Developer
91% match

Frontend Engineer
87% match

Full Stack Developer
82% match
CAND-012 — Candidate Match Explanation

Priority: P0

Candidates must be able to understand why a job matches them.

Example:

Strong match:
+ React
+ TypeScript
+ AWS
+ 5 years experience

Potential gap:
- Kubernetes
CAND-013 — Candidate Visibility

Priority: P0

Candidates must have control over whether recruiters can discover their profile.

Initial state:

VISIBLE
HIDDEN
CAND-014 — Job-Seeking Status

Priority: P1

Candidates should be able to indicate:

ACTIVELY_LOOKING
OPEN_TO_OPPORTUNITIES
NOT_LOOKING
6. Recruiter Requirements
REC-001 — Recruiter Registration

Priority: P0

Recruiters must be able to create an account.

REC-002 — Recruiter Profile

Priority: P0

Recruiters should have:

Name
Job title
Company
REC-003 — Company Creation

Priority: P0

A recruiter should be able to create or join a company.

Minimum company information:

Company name
Website
Description
Location
REC-004 — Company Ownership

Priority: P0

Company resources must be isolated.

A recruiter must only be able to modify resources for companies they are authorized to manage.

REC-005 — Job Creation

Priority: P0

Recruiters must be able to create jobs.

Minimum information:

Title
Description
Location
Work mode
Employment type
Salary range
Currency
REC-006 — Job Draft

Priority: P0

New jobs should initially have:

DRAFT

status.

Recruiters may edit drafts before publishing.

REC-007 — Job Publishing

Priority: P0

Recruiters must be able to publish valid jobs.

A job cannot be published if mandatory fields are missing.

REC-008 — Job Editing

Priority: P0

Recruiters must be able to edit their jobs.

REC-009 — Job Closing

Priority: P0

Recruiters must be able to close jobs.

Closed jobs should no longer appear as active opportunities.

7. Job Requirements
JOB-001 — Required Skills

Priority: P0

Recruiters must be able to specify required skills.

Example:

React — Level 4 — Required
TypeScript — Level 4 — Required
AWS — Level 3 — Required
JOB-002 — Preferred Skills

Priority: P0

Recruiters must be able to specify preferred skills.

Example:

Kubernetes — Level 3 — Preferred
Docker — Level 3 — Preferred
JOB-003 — Skill Weight

Priority: P1

Recruiters should eventually be able to define the importance of a skill.

Example:

React       30%
TypeScript  25%
AWS         20%
Node.js     15%
Docker      10%
JOB-004 — AI Job Analysis

Priority: P0

The system should analyze a job description and suggest:

skills;
experience requirements;
seniority;
relevant technologies.

The recruiter must be able to review the suggestions.

8. Skill Requirements
SKILL-001 — Skill Catalog

Priority: P0

The platform must maintain a normalized skill catalog.

Examples:

React
TypeScript
Node.js
Python
Java
AWS
Azure
Kubernetes
PostgreSQL
Docker
SKILL-002 — Skill Search

Priority: P0

Users must be able to search for skills.

Example:

rea...

returns:

React
React Native
SKILL-003 — Skill Normalization

Priority: P0

The platform should normalize common variations.

Example:

React.js
ReactJS
React JS

should resolve to:

React
9. Matching Requirements

This is the core MVP domain.

MATCH-001 — Candidate/Job Matching

Priority: P0

The backend must calculate a compatibility score between candidates and jobs.

MATCH-002 — Skill Matching

Priority: P0

The matching engine must compare candidate skills with job requirements.

Example:

Job requires:

React       Level 4
TypeScript  Level 4
AWS         Level 3

Candidate:

React       Level 5
TypeScript  Level 4
AWS         Level 4

This should produce a strong skill match.

MATCH-003 — Required Skill Penalty

Priority: P0

Missing required skills must have a stronger impact on the match score than missing preferred skills.

MATCH-004 — Experience Matching

Priority: P0

The matching engine should consider relevant professional experience.

MATCH-005 — Location Matching

Priority: P0

The engine should consider:

candidate location;
job location;
remote work;
hybrid work;
onsite requirements.
MATCH-006 — Salary Matching

Priority: P0

Where salary information is available, the engine should consider compatibility between:

Candidate expectation

and:

Job salary range
MATCH-007 — Availability Matching

Priority: P1

The engine should consider candidate availability.

MATCH-008 — Preference Matching

Priority: P1

The engine should eventually consider:

remote preference;
employment type;
industry;
company preferences.
MATCH-009 — Match Score

Priority: P0

The match score must be represented as:

0–100
MATCH-010 — Match Components

Priority: P0

The score should be decomposable into components.

Example:

Overall       91
Skills        95
Experience    90
Location     100
Salary        85
Availability 100
Preferences   80
MATCH-011 — Match Explanation

Priority: P0

The system must provide understandable reasons for the score.

MATCH-012 — Match Ranking

Priority: P0

Candidates should be rankable by match score.

Example:

1. Candidate A — 94%
2. Candidate B — 91%
3. Candidate C — 87%
10. Recruiter Candidate Discovery
DISC-001 — Candidate Search

Priority: P0

Recruiters must be able to discover candidates.

DISC-002 — Skill Filter

Priority: P0

Recruiters must be able to filter by skill.

DISC-003 — Location Filter

Priority: P0

Recruiters must be able to filter by location.

DISC-004 — Experience Filter

Priority: P1

Recruiters should be able to filter by experience.

DISC-005 — Candidate Match Ranking

Priority: P0

Recruiters should be able to view candidates ranked by match quality for a specific job.

11. Candidate Privacy Requirements
PRIV-001 — Profile Visibility

Priority: P0

Candidates must control whether their profile is discoverable.

PRIV-002 — CV Visibility

Priority: P0

A CV must not be publicly accessible.

PRIV-003 — Recruiter Authorization

Priority: P0

Recruiters may only access candidate information they are authorized to see.

PRIV-004 — Data Minimization

Priority: P0

The platform should expose only information required for the current business operation.

12. Authentication Requirements
AUTH-001 — Registration

Priority: P0

Users can register.

AUTH-002 — Login

Priority: P0

Users can authenticate.

AUTH-003 — Logout

Priority: P0

Users can terminate their session.

AUTH-004 — Password Hashing

Priority: P0

Passwords must use secure hashing.

AUTH-005 — Password Reset

Priority: P1

Users should eventually be able to reset forgotten passwords.

13. Administration
ADMIN-001 — Admin Role

Priority: P0

The platform must support an administrative role.

ADMIN-002 — Skill Management

Priority: P1

Admins should be able to:

create skills;
edit skills;
merge duplicate skills;
deactivate obsolete skills.
ADMIN-003 — User Management

Priority: P1

Admins should eventually be able to:

view users;
suspend users;
restore users.
14. Notifications

Notifications are deliberately limited in MVP.

NOTIF-001 — Basic System Notifications

Priority: P1

Potential notifications:

CV processing complete
New relevant match
Job published

Email notifications may be postponed.

15. Messaging
MSG-001 — Recruiter/Candidate Messaging

Priority: P2

Direct messaging is not part of the initial MVP.

Future flow:

Recruiter
   ↓
Candidate
   ↓
Conversation
16. Applications
APP-001 — Job Application

Priority: P2

Traditional applications are outside the first MVP.

The initial product focuses on matching and discovery.

17. Payments
PAY-001 — Payments

Priority: P3

Payment functionality is explicitly excluded from MVP.

The architecture should remain extensible for future subscriptions.

18. Analytics
ANA-001 — Basic Product Analytics

Priority: P1

The platform should eventually track anonymized product events.

Examples:

PROFILE_COMPLETED
JOB_CREATED
JOB_PUBLISHED
MATCH_VIEWED
CANDIDATE_VIEWED
CV_UPLOADED

Analytics implementation should respect privacy requirements.

19. Non-Functional Requirements
NFR-001 — Performance

Priority: P0

Normal API requests should generally respond quickly.

Initial target:

Typical API response < 500ms

excluding long-running operations such as:

AI processing;
CV parsing;
bulk matching.

This is an initial engineering target, not a contractual SLA.

NFR-002 — Scalability

Priority: P1

The architecture should support future horizontal scaling.

The backend should avoid relying on:

local persistent files;
process-local state;
single-server assumptions.
NFR-003 — Availability

Priority: P1

The production system should target reasonable availability.

Exact SLA will be defined only after production infrastructure is selected.

NFR-004 — Security

Priority: P0

The system must comply with the security architecture defined in:

architecture/security.md
NFR-005 — Maintainability

Priority: P0

The codebase should use:

TypeScript;
clear modules;
automated tests;
linting;
formatting;
documented APIs.
NFR-006 — Testing

Priority: P0

Core business logic must have automated tests.

Especially:

Authentication
Authorization
Matching
Skill normalization
Job requirements
Candidate privacy
NFR-007 — API Documentation

Priority: P0

The backend should provide OpenAPI/Swagger documentation.

NFR-008 — Observability

Priority: P1

The production system should provide:

structured logs;
error tracking;
basic health monitoring.
NFR-009 — Accessibility

Priority: P1

The frontend should follow modern accessibility principles.

Target:

WCAG 2.1 AA

where reasonably achievable during MVP development.

NFR-010 — Responsive Design

Priority: P0

The application must work on:

desktop;
tablet;
mobile.

The primary recruiter experience may be desktop-oriented, but the candidate experience should be strongly mobile-friendly.

20. AI Requirements
AI-001 — Structured Output

AI integrations must return structured data where possible.

AI-002 — Validation

AI output must be validated before entering the database.

AI-003 — Explainability

AI-generated information should be identifiable.

AI-004 — Human Confirmation

Important AI-generated profile information should be reviewable by the user.

AI-005 — Provider Abstraction

The backend should not tightly couple the domain model to one AI provider.

Conceptually:

AI Service
    │
    ├── Provider A
    ├── Provider B
    └── Future Provider

This makes provider changes easier.

21. Out of Scope for MVP

The following are explicitly excluded from MVP:

Native mobile applications
Advanced ATS
Enterprise SSO
Advanced billing
Payments
Recruitment agency management
Complex interview scheduling
Video interviews
Chat
Social networking
Learning platform
Automated hiring decisions
Advanced salary intelligence
International tax/payroll

These may be revisited later.

22. MVP User Journeys
Candidate Journey
Register
   ↓
Create Profile
   ↓
Add Skills
   ↓
Upload CV
   ↓
Review Extracted Skills
   ↓
Browse Jobs
   ↓
View Match
   ↓
Understand Match
Recruiter Journey
Register
   ↓
Create Company
   ↓
Create Job
   ↓
Define Skills
   ↓
Publish Job
   ↓
View Matches
   ↓
Inspect Candidates
23. MVP End-to-End Scenario

The following scenario represents the core product.

Step 1

Candidate creates:

Senior React Developer
6 years experience
Amsterdam
Hybrid

Skills:

React 5
TypeScript 5
Node.js 4
AWS 3
Step 2

Recruiter creates:

Senior Frontend Engineer
Amsterdam
Hybrid

Requirements:

React 4 — Required
TypeScript 4 — Required
AWS 3 — Preferred
Kubernetes 3 — Preferred
Step 3

Matching engine calculates:

Overall: 91%
Step 4

System explains:

Strong matches:
React
TypeScript
AWS

Potential gap:
Kubernetes

Location:
Excellent

Salary:
Compatible

This is the fundamental MVP demonstration.

24. Acceptance Criteria for MVP

The MVP can be considered functionally complete when:

Candidate
 can register;
 can log in;
 can create profile;
 can add skills;
 can upload CV;
 can review extracted skills;
 can browse jobs;
 can see matches;
 can understand match explanations.
Recruiter
 can register;
 can create company;
 can create job;
 can define skills;
 can publish job;
 can see candidate matches;
 can inspect match details.
Platform
 authentication works;
 authorization works;
 company isolation works;
 candidate privacy works;
 skills are normalized;
 matching works;
 AI extraction works;
 API is documented;
 core tests pass.
25. MVP Definition of Done

The MVP is ready for controlled testing when:

Frontend
    ↓
Backend
    ↓
Database
    ↓
Authentication
    ↓
Candidate
    ↓
Recruiter
    ↓
Job
    ↓
Skills
    ↓
Matching
    ↓
AI assistance

works end-to-end in a deployed environment.

The application must also pass the minimum security and testing requirements.

26. Product Requirements Status

Version: 0.1.0

Status: MVP scope defined.

Future changes should be evaluated against:

product vision;
MVP objective;
user value;
implementation cost;
security/privacy impact.

27. Next Document

The next document is:

product/roadmap.md

It will convert these requirements into concrete development phases:

Phase 0 — Repository & tooling
Phase 1 — Backend foundation
Phase 2 — Frontend foundation
Phase 3 — Authentication
Phase 4 — Candidate
Phase 5 — Recruiter/company
Phase 6 — Jobs & skills
Phase 7 — Matching engine
Phase 8 — AI/CV processing
Phase 9 — Integration testing
Phase 10 — Deployment
Phase 11 — Closed beta