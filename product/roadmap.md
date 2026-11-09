# IT Talent Platform — Product Roadmap

**Document:** `roadmap.md`  
**Version:** `0.3.0`  
**Status:** Product Roadmap  
**Last updated:** 2026-11-09

---

# 1. Purpose

This roadmap describes the development direction and functional priorities of the IT Talent Platform.

It translates the product vision into logical development areas and proposed implementation phases.

The roadmap focuses on:

- candidates;
- recruiters;
- companies;
- skills;
- jobs;
- job discovery;
- matching;
- applications;
- dashboards;
- administration;
- platform quality and security.

This document describes development priorities and direction. It does not claim that every listed feature is already implemented.

Actual implementation status must be verified against the frontend, backend, database, API, and automated tests.

---

# 2. Roadmap Structure

The platform is organized into the following functional areas:

```text
IT Talent Platform
│
├── Foundation
│   ├── Application Structure
│   ├── Authentication
│   ├── Authorization
│   └── API Communication
│
├── Candidate
│   ├── Profile
│   ├── Skills
│   ├── Preferences
│   ├── Job Discovery
│   └── Applications
│
├── Recruiter
│   ├── Profile
│   ├── Company Context
│   ├── Jobs
│   ├── Candidates
│   └── Applications
│
├── Matching
│   ├── Skill Matching
│   ├── Compatibility
│   └── Recommendations
│
├── Administration
│   ├── Users
│   └── Skills
│
└── Platform
    ├── Dashboards
    ├── Languages
    ├── Security
    ├── Testing
    └── Documentation
```

---

# 3. Roadmap Priorities

Development priorities are grouped into four categories.

| Priority | Description |
|---|---|
| Critical | Required for a secure and reliable core platform |
| High | Essential functionality for the main user journeys |
| Medium | Important improvements and additional capabilities |
| Future | Longer-term product enhancements |

Priorities describe product importance, not implementation status.

---

# 4. Platform Foundation

**Priority:** Critical

The foundation provides the technical and functional capabilities required by all platform users.

Core areas include:

- application structure;
- authentication;
- registration;
- login;
- logout;
- role-based access;
- backend authorization;
- API communication;
- environment configuration;
- error handling;
- language support.

Supported roles:

```text
CANDIDATE
RECRUITER
ADMIN
```

Development focus:

- maintain reliable authentication flows;
- enforce role-based access on the backend;
- protect user and company resources;
- maintain consistent frontend and backend API contracts;
- validate configuration and environment variables;
- provide consistent error handling.

---

# 5. Candidate Experience

**Priority:** High

The candidate experience is centered on the professional profile, skills, job discovery, and applications.

```text
Candidate
   │
   ├── Profile
   ├── Skills
   ├── Preferences
   ├── Job Discovery
   ├── Matching
   └── Applications
```

Candidate functionality includes:

- registration;
- login;
- profile management;
- skill management;
- job search;
- job filtering;
- job sorting;
- job details;
- job applications;
- application status tracking;
- application withdrawal where supported.

Development focus:

- improve the candidate profile experience;
- maintain consistent skill information;
- provide reliable job discovery;
- improve application feedback;
- ensure candidates can only access their own protected information.

---

# 6. Candidate Profile

**Priority:** High

The candidate profile provides the professional information used throughout the platform.

Profile information may include:

- professional headline;
- professional summary;
- location;
- salary expectations;
- currency;
- availability;
- remote-work preference;
- skills;
- professional experience.

The profile is an important source of candidate information for job discovery and future matching functionality.

Potential improvements:

- profile completeness indicators;
- improved profile validation;
- structured experience information;
- CV upload where supported;
- CV information extraction as a future capability.

CV processing and extraction must be treated as separate implementation work and verified before being presented as available functionality.

---

# 7. Skills

**Priority:** High

Skills are a central platform component.

The skill functionality is used by:

- candidates;
- jobs;
- matching;
- administration.

Candidate skills may contain:

- skill;
- proficiency;
- years of experience;
- skill source or verification information where supported.

Development focus:

- maintain a consistent skill catalog;
- support skill selection in candidate profiles;
- support required and preferred job skills;
- improve skill search;
- preserve relationships between skills, candidates, and jobs;
- provide authorized skill administration.

Potential future improvements:

- expanded skill categorization;
- improved skill normalization;
- duplicate skill detection;
- skill verification workflows.

---

# 8. Job Discovery

**Priority:** High

Candidates must be able to discover relevant jobs through the job discovery experience.

The intended functionality includes:

- job listings;
- keyword search;
- location filtering;
- work-mode filtering;
- employment-type filtering;
- minimum salary filtering;
- maximum salary filtering;
- skill filtering;
- sorting;
- pagination.

Potential work modes include:

- remote;
- hybrid;
- onsite;
- flexible.

Development focus:

- maintain accurate job listings;
- provide consistent search behavior;
- ensure filters work with supported backend parameters;
- provide useful empty and error states;
- improve usability across desktop, tablet, and mobile.

---

# 9. Job Details

**Priority:** High

Candidates must be able to open individual jobs and review detailed information.

Job details may include:

- job title;
- description;
- company information;
- location;
- work mode;
- employment type;
- salary information;
- required skills;
- preferred skills.

The job detail experience should provide access to the application flow where applications are supported.

Development focus:

- present job information clearly;
- distinguish required and preferred skills;
- handle optional information consistently;
- provide clear application actions;
- prevent applications for jobs that are no longer eligible.

---

# 10. Applications

**Priority:** High

Applications connect candidates and recruiters through the job application process.

The intended flow is:

```text
Candidate
    ↓
   Job
    ↓
   Apply
    ↓
Application
    ↓
Application Review
    ↓
Application Status
```

Candidates should be able to:

- apply for eligible jobs;
- provide a cover letter where supported;
- view their applications;
- view application details;
- track application status;
- withdraw applications where permitted.

The application data model supports:

- `PENDING`;
- `REVIEWING`;
- `ACCEPTED`;
- `REJECTED`;
- `WITHDRAWN`.

Recruiters should be able to review applications associated with their authorized jobs.

Development focus:

- maintain application ownership;
- validate application creation;
- enforce permitted status transitions;
- improve application review workflows;
- provide clear status information to candidates.

---

# 11. Recruiter Experience

**Priority:** High

The recruiter experience focuses on managing recruitment information within the associated company context.

```text
Recruiter
   │
   ├── Profile
   ├── Company
   ├── Jobs
   ├── Candidates
   └── Applications
```

Recruiter functionality includes:

- managing recruiter information;
- working within a company context;
- creating jobs;
- editing jobs;
- defining required skills;
- defining preferred skills;
- publishing and managing jobs;
- reviewing authorized candidate information;
- reviewing applications.

Development focus:

- improve job management workflows;
- maintain company and recruiter relationships;
- provide clear job lifecycle actions;
- improve application review;
- enforce company-level authorization.

---

# 12. Company Context

**Priority:** High

Jobs are managed within a company context.

Company information is associated with recruiter and job functionality.

The company context supports:

- company information;
- recruiter association;
- job management;
- recruitment-related information;
- application ownership relationships.

Development focus:

- maintain correct company associations;
- enforce company-level resource access;
- prevent recruiters from managing unrelated company jobs;
- maintain consistent company information across the platform.

---

# 13. Job Management

**Priority:** High

Recruiters must be able to create and manage jobs they are authorized to access.

The intended workflow is:

```text
Create Job
    ↓
Enter Job Information
    ↓
Add Required / Preferred Skills
    ↓
Save Job
    ↓
Publish Job
    ↓
Manage Job Status
    ↓
Review Candidates
    ↓
Review Applications
```

Job information may include:

- title;
- description;
- location;
- work mode;
- employment type;
- salary information;
- required skills;
- preferred skills.

Development focus:

- validate job information;
- maintain job ownership;
- support permitted job status transitions;
- ensure published jobs are available for discovery;
- ensure closed or unavailable jobs are handled correctly.

---

# 14. Recruiter Candidate Experience

**Priority:** Medium

The recruiter experience should support reviewing relevant candidate information.

The intended workflow is:

```text
Job
  ↓
Relevant Candidates
  ↓
Candidate Information
  ↓
Matching Information
  ↓
Application Information
```

Potential capabilities include:

- viewing candidate profiles;
- reviewing candidate skills;
- reviewing relevant experience;
- understanding candidate/job compatibility;
- accessing related application information.

Development focus:

- respect candidate data permissions;
- limit information to authorized recruiters;
- provide meaningful candidate information;
- avoid exposing sensitive information unnecessarily.

Advanced candidate discovery and matching depend on the availability of the corresponding backend functionality.

---

# 15. Matching

**Priority:** Medium

Matching connects candidate profiles with job requirements.

Potential matching inputs include:

```text
Candidate
│
├── Skills
├── Experience
├── Location
├── Salary Expectations
├── Availability
└── Preferences
        │
        ▼
     Matching
        │
        ▼
       Job
```

Matching functionality may support:

- identifying relevant jobs;
- identifying relevant candidates;
- comparing candidate and job skills;
- presenting compatibility information;
- supporting recruiter candidate review;
- supporting job recommendations.

Potential future improvements:

- defined matching criteria;
- compatibility scoring;
- required versus preferred skill weighting;
- explanations of matching results;
- candidate and job ranking;
- personalized recommendations.

Development must first establish and document the actual matching logic.

A matching-related database model does not automatically mean that a complete matching workflow or public API is available.

---

# 16. Dashboards

**Priority:** Medium

The platform aims to provide role-specific dashboards.

## Candidate Dashboard

Potential dashboard information:

- profile completion;
- applications;
- available jobs;
- recommended jobs where supported;
- skills;
- recent applications;
- recent jobs.

## Recruiter Dashboard

Potential dashboard information:

- company information;
- jobs by status;
- applications;
- recent candidates;
- recent jobs.

## Admin Dashboard

The administration experience may provide access to:

- user management;
- skill management;
- relevant platform information.

Development focus:

- provide relevant information for each role;
- ensure dashboard data is accurate;
- respect user and company authorization;
- maintain consistent loading, empty, and error states.

---

# 17. Administration

**Priority:** Medium

Administration provides management functionality for authorized platform administrators.

Main areas:

```text
Administration
│
├── Users
└── Skills
```

Potential administrative capabilities include:

- viewing users;
- searching users;
- creating users where permitted;
- updating user information;
- deleting users where permitted;
- managing roles;
- managing skills.

Development focus:

- protect administrative operations;
- enforce backend authorization;
- validate role changes;
- prevent unauthorized privilege escalation;
- preserve data integrity when users or skills are changed.

The availability of individual administrative actions must be verified against the implementation.

---

# 18. Multilingual Platform

**Priority:** Medium

The platform supports multiple interface languages.

Current languages:

- English;
- Dutch.

English is the default language.

The application uses a shared translation system for interface content.

Development focus:

- maintain translation consistency;
- provide translations for new functionality;
- use English fallback behavior where appropriate;
- maintain language selection behavior;
- avoid untranslated user-facing content.

---

# 19. Platform Quality and Security

**Priority:** Critical

Platform quality and security are continuous development responsibilities.

Important areas include:

## Security

- authentication protection;
- backend authorization;
- resource ownership checks;
- secure password handling;
- input validation;
- rate limiting;
- safe file handling where uploads are supported;
- protection of sensitive information.

## Testing

- frontend component tests;
- backend unit tests;
- API integration tests;
- authentication and authorization tests;
- core user-flow tests;
- validation and error-handling tests.

## Maintainability

- consistent frontend and backend structure;
- clear API contracts;
- maintainable database models;
- environment configuration;
- technical documentation.

## User Experience

- responsive layouts;
- accessible interactions;
- consistent navigation;
- clear feedback;
- useful loading and empty states.

Quality and security work should continue throughout every development phase rather than being postponed until the end.

---

# 20. AI-Assisted Functionality

**Priority:** Future

AI-assisted functionality is a potential extension of the IT Talent platform.

Potential capabilities include:

- CV information extraction;
- structured skill identification;
- experience extraction;
- job requirement analysis;
- matching assistance;
- explanations of matching results.

Before implementation, each capability should have:

- a defined product requirement;
- an appropriate technical design;
- privacy and security considerations;
- validation and testing criteria;
- a clear user review process.

AI-generated information must not automatically be treated as verified candidate information.

AI functionality should support human decision-making and must not replace appropriate recruiter judgment.

---

# 21. Functional Development Phases

The following phases describe a proposed development sequence. They are not release dates and do not indicate that the functionality has already been completed.

## Phase 1 — Foundation and Security

**Priority:** Critical

Focus:

- application structure;
- authentication;
- registration and login;
- role-based authorization;
- API communication;
- environment configuration;
- input validation;
- security controls;
- automated tests.

**Expected outcome:** A reliable and secure foundation for the main platform workflows.

## Phase 2 — Candidate and Recruiter Core

**Priority:** High

Focus:

- candidate profiles;
- recruiter profiles;
- company context;
- candidate skills;
- skill catalog;
- job creation;
- job editing;
- job publishing and lifecycle management.

**Expected outcome:** Candidates and recruiters can maintain the essential information required by the platform.

## Phase 3 — Job Discovery and Applications

**Priority:** High

Focus:

- job listings;
- job search;
- filtering;
- sorting;
- pagination;
- job details;
- application submission;
- application overview;
- application status handling.

**Expected outcome:** Candidates can discover opportunities and apply, while recruiters can review associated applications.

## Phase 4 — Dashboards and Administration

**Priority:** Medium

Focus:

- candidate dashboard;
- recruiter dashboard;
- administrative functionality;
- user management;
- skill management;
- improved platform navigation.

**Expected outcome:** Each role receives a more complete overview of its relevant platform information and actions.

## Phase 5 — Matching Improvements

**Priority:** Medium

Focus:

- documented matching criteria;
- candidate/job skill comparison;
- compatibility scoring;
- matching explanations;
- candidate and job recommendations.

**Expected outcome:** Matching functionality becomes more transparent and useful for candidates and recruiters.

This phase depends on the availability of the required matching logic and data.

## Phase 6 — Advanced Features

**Priority:** Future

Potential focus:

- AI-assisted CV processing;
- advanced candidate discovery;
- improved recommendation logic;
- advanced matching;
- additional reporting;
- further platform enhancements.

**Expected outcome:** The platform can expand beyond its core recruitment workflows.

---

# 22. Development Order

The overall development direction can be summarized as follows:

```text
Foundation
    ↓
Authentication and Authorization
    ↓
Candidate / Recruiter Profiles
    ↓
Skills and Companies
    ↓
Job Management
    ↓
Job Discovery
    ↓
Applications
    ↓
Dashboards and Administration
    ↓
Matching Improvements
    ↓
Advanced Features
```

Security, testing, accessibility, and documentation apply throughout all phases.

The development order may change when technical dependencies, implementation findings, or product priorities require it.

---

# 23. Product Development Principles

## 23.1 User-Centered

Development should support the main Candidate, Recruiter, and Admin experiences.

## 23.2 Skill-Oriented

Skills remain a central component of candidate profiles, job requirements, and matching.

## 23.3 Structured

Candidate, recruiter, company, job, skill, matching, and application information should remain structured and consistent.

## 23.4 Consistent

New functionality should follow the existing application structure, navigation, and interaction patterns.

## 23.5 Multilingual

New user-facing functionality should support English and Dutch.

## 23.6 Role-Based

Functionality must remain aligned with the permissions and responsibilities of each platform role.

## 23.7 Secure by Design

Security and data protection must be considered during design and implementation, not only after functionality has been developed.

## 23.8 Incremental Development

Development should deliver functionality in manageable increments, with appropriate testing and documentation.

---

# 24. Roadmap Status and Tracking

Roadmap items must have an explicit implementation status.

| Status | Meaning |
|---|---|
| Completed | Implemented and verified |
| In Progress | Currently being developed |
| Planned | Accepted for future development |
| Proposed | Under consideration |
| Blocked | Waiting for a dependency or decision |
| Needs Verification | Implementation status is unknown |

The presence of an item in this roadmap does not mean it is completed.

A roadmap item should only be marked as completed after its relevant functionality has been verified.

Verification may include:

- frontend implementation;
- backend controllers and services;
- API behavior;
- database schema;
- automated tests;
- relevant user flows.

---

# 25. Roadmap Overview

| Area | Main Functionality | Priority |
|---|---|---|
| Foundation | Application structure, configuration, API communication | Critical |
| Authentication | Registration, login, logout | Critical |
| Authorization | Roles, permissions, resource ownership | Critical |
| Candidate | Profile, skills, preferences | High |
| Recruiter | Profile, company context | High |
| Jobs | Job creation, management, publishing | High |
| Job Discovery | Search, filters, sorting, pagination | High |
| Applications | Apply, review, status, withdrawal | High |
| Skills | Skill selection and administration | High |
| Dashboards | Candidate, recruiter, and admin views | Medium |
| Administration | User and skill management | Medium |
| Matching | Candidate/job compatibility | Medium |
| Languages | English and Dutch | Medium |
| AI-Assisted Features | CV processing and advanced matching assistance | Future |
| Platform Quality | Security, testing, accessibility, maintainability | Continuous |

---

# 26. Current Product Direction

Development of IT Talent should continue by strengthening the core candidate and recruiter experiences.

The primary candidate journey remains:

```text
Candidate
    ↓
Profile
    ↓
Skills
    ↓
Job Discovery
    ↓
Job Details
    ↓
Application
    ↓
Application Tracking
```

The primary recruiter journey remains:

```text
Recruiter
    ↓
Company Context
    ↓
Job Creation
    ↓
Job Management
    ↓
Candidate Review
    ↓
Application Review
```

Matching and AI-assisted functionality can extend these journeys when their requirements, implementation, and validation are sufficiently defined.

---

# 27. Related Documentation

The roadmap is aligned with the following product and technical documents:

**Product documentation**

- `vision.md`
- `requirements.md`

**Architecture documentation**

- `../architecture/architecture.md`
- `../architecture/api.md`
- `../architecture/database.md`
- `../architecture/security.md`

Product vision defines the direction of IT Talent.

Product requirements define the expected functional behavior.

This roadmap describes the proposed development priorities and sequence.

Technical documentation describes the architecture and implementation.

---

# 28. Document Status

**Document:** `roadmap.md`  
**Version:** `0.3.0`  
**Status:** Product Roadmap  
**Last updated:** 2026-11-09

This document defines the development direction and priorities of the IT Talent Platform.

Roadmap priorities and phases may change as implementation progresses and product requirements evolve.