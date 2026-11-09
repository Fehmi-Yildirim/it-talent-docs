# IT Talent Platform — Product Vision

**Document:** `vision.md`  
**Version:** `0.3.0`  
**Status:** Product Vision  
**Last updated:** 2026-11-09

---

# 1. Product Vision

IT Talent is a digital platform for the IT labor market that aims to connect IT professionals and recruiters through structured professional profiles, skills, job opportunities, applications, and relevant matching information.

The platform organizes recruitment-related information around:

- professional profiles;
- skills and experience;
- job requirements;
- location and work preferences;
- employment type;
- salary expectations;
- availability;
- applications;
- matching information.

IT Talent aims to help candidates discover relevant IT opportunities and enables recruiters to manage vacancies, review candidate information, and handle applications.

The long-term vision is to make the connection between IT talent and recruitment opportunities more transparent, structured, and efficient.

---

# 2. Product Concept

The core concept of IT Talent is connecting candidate profiles with job opportunities.

```text
Candidate Profile
       +
Skills & Experience
       +
Preferences
       │
       ▼
    Matching
       │
       ▼
Candidate ↔ Job
       │
       ▼
   Application
```

The platform uses structured candidate and job information to support the discovery of relevant opportunities.

Matching information can help candidates and recruiters understand how professional skills and preferences relate to job requirements.

The exact matching functionality depends on the implemented matching services and business logic.

---

# 3. User Roles

IT Talent supports three primary user roles:

```text
CANDIDATE
RECRUITER
ADMIN
```

## 3.1 Candidates

Candidates use IT Talent to manage their professional information and explore IT job opportunities.

The candidate experience includes:

- creating an account;
- managing a professional profile;
- adding and managing skills;
- maintaining professional information;
- defining salary expectations;
- defining availability;
- specifying work preferences;
- searching for jobs;
- filtering and sorting job listings;
- viewing job details;
- applying for jobs;
- submitting a cover letter where supported;
- viewing applications;
- tracking application status;
- withdrawing applications where supported.

## 3.2 Recruiters

Recruiters use IT Talent to manage recruitment activities within an associated company context.

The recruiter experience includes:

- managing recruiter profile information;
- working within a company context;
- creating jobs;
- managing job information;
- defining required and preferred skills;
- publishing and managing vacancies;
- viewing authorized candidate information;
- reviewing applications associated with their jobs.

## 3.3 Administrators

Administrators support platform management through authorized administrative functionality.

The administration experience may include:

- managing users;
- reviewing user information;
- managing permitted account roles;
- managing skills;
- maintaining relevant platform data.

Administrative actions must be protected by backend authorization.

---

# 4. Candidate Profiles

The candidate profile represents the professional information used throughout the platform.

Profile information may include:

- professional headline;
- professional summary;
- location;
- salary expectations;
- salary currency;
- availability;
- remote-work preference;
- skills;
- professional experience;
- CV information where supported.

Candidates should be able to maintain their professional information through the candidate profile experience.

The profile provides structured information that can support job discovery, applications, and future matching functionality.

CV upload, extraction, and AI-assisted processing are separate capabilities and should only be considered available when verified in the application implementation.

---

# 5. Skills

Skills are a central part of IT Talent.

The platform uses structured skills to describe candidate capabilities and job requirements.

Candidate skill information may include:

- skill;
- proficiency level;
- years of experience;
- skill source or verification information where supported.

Jobs can contain:

- required skills;
- preferred skills.

The skill catalog supports consistent skill selection across candidate profiles and job requirements.

Authorized administrators may manage the skill catalog through the administration functionality.

---

# 6. Jobs

Recruiters can create and manage job opportunities within their authorized company context.

A job may contain:

- title;
- description;
- company;
- location;
- work mode;
- employment type;
- salary information;
- required skills;
- preferred skills;
- job status.

Potential work modes include:

- remote;
- hybrid;
- onsite;
- flexible.

Job information is structured to support job discovery, candidate applications, and matching-related functionality.

---

# 7. Job Discovery

Candidates can discover available jobs through the job discovery experience.

The intended job discovery experience supports:

- job listings;
- keyword search;
- location filtering;
- work-mode filtering;
- employment-type filtering;
- minimum salary filtering;
- maximum salary filtering;
- skill filtering;
- sorting;
- pagination;
- job details.

Candidates can open a job to review information such as:

- description;
- company information;
- location;
- work mode;
- employment type;
- salary information;
- required skills;
- preferred skills.

The available search, filter, and sorting options depend on the implemented frontend and backend functionality.

---

# 8. Matching

Matching connects candidate information with job information.

```text
Candidate
   │
   ├── Skills
   ├── Experience
   ├── Location
   ├── Availability
   ├── Salary Expectations
   └── Preferences
          │
          ▼
       Matching
          │
          ▼
         Job
```

The product vision is to help identify relevant relationships between candidate profiles and job requirements.

Potential matching criteria include:

- skill compatibility;
- professional experience;
- location;
- availability;
- salary expectations;
- work preferences.

Matching information may help users understand how candidate capabilities relate to job requirements.

Future matching functionality may include:

- compatibility scores;
- explanations of matching results;
- candidate ranking;
- job recommendations.

These capabilities depend on the availability and implementation of the matching logic.

The existence of matching-related database models does not by itself mean that all matching functionality is available through the application.

---

# 9. Applications

IT Talent supports the connection between candidates and recruiters through job applications.

```text
Candidate
    ↓
   Job
    ↓
Application
    ↓
Application Review
```

An application may contain:

- candidate reference;
- job reference;
- application date;
- cover letter where supported;
- application status.

The application data model supports the following statuses:

- `PENDING`;
- `REVIEWING`;
- `ACCEPTED`;
- `REJECTED`;
- `WITHDRAWN`.

Candidates should be able to review their own applications and track their status.

Recruiters should be able to review applications associated with jobs they are authorized to manage.

Application actions and status transitions must follow the backend business rules.

---

# 10. Recruiter Job Management

The recruiter experience provides job management within the associated company context.

The intended workflow is:

```text
Create Job
    ↓
Add Job Information
    ↓
Add Required / Preferred Skills
    ↓
Publish / Manage Job
    ↓
Review Candidates
    ↓
Review Applications
```

Recruiters manage jobs and review recruitment information associated with their authorized company and job relationships.

The backend is responsible for enforcing ownership and authorization rules.

---

# 11. Company Context

Jobs are associated with a company.

The company context provides organizational information for recruiter and job management.

Recruiters operate within the company context permitted by their account and backend authorization.

Company relationships help maintain a clear separation between recruitment activities belonging to different organizations.

---

# 12. Dashboards

IT Talent aims to provide role-specific dashboard experiences.

## 12.1 Candidate Dashboard

The candidate dashboard may provide:

- profile completion information;
- application information;
- available jobs;
- recommended jobs where supported;
- skills;
- recent applications;
- recently published jobs.

## 12.2 Recruiter Dashboard

The recruiter dashboard may provide:

- company information;
- jobs grouped by status;
- application information;
- recent candidates;
- recent jobs.

## 12.3 Admin Dashboard

The administration experience may provide access to platform management functionality, including:

- user management;
- skill management;
- relevant administrative information.

Dashboard content depends on the available backend data and implemented frontend functionality.

---

# 13. Authentication and Access

IT Talent provides authentication and role-based access.

The platform supports three roles:

- `CANDIDATE`;
- `RECRUITER`;
- `ADMIN`.

The product experience includes:

- user registration;
- user login;
- logout;
- role-specific navigation;
- protected functionality.

Each role receives access to the functionality permitted for that role.

Authentication and authorization must be enforced by the backend. Frontend navigation and visibility controls are not sufficient security boundaries.

Public registration must not allow unauthorized assignment of administrative privileges.

---

# 14. Multilingual Platform

IT Talent supports multiple languages in the user interface.

The supported languages are:

- **English — default language**
- **Dutch**

The application uses a shared translation system for interface text.

The product experience supports language selection and language-specific interface content.

The translation system should provide English fallback behavior when a translation is unavailable.

New user-facing functionality should include translations for all supported languages.

---

# 15. AI-Assisted Functionality

AI-assisted functionality is a potential extension of the IT Talent platform.

The product direction allows for future AI-assisted processing of structured recruitment information, such as:

- CV information;
- candidate skills;
- professional experience;
- job information;
- matching information.

Potential future applications include:

- extracting structured information from CV documents;
- identifying candidate skills;
- supporting job requirement analysis;
- assisting candidate and job matching;
- providing explanations of matching results.

AI-assisted capabilities must be treated as planned or optional functionality until their implementation has been verified.

Any future AI functionality should:

- preserve the accuracy of source information;
- allow appropriate user review of extracted data;
- avoid presenting inferred information as verified fact;
- respect authorization and privacy requirements;
- provide understandable results.

AI-generated information should support human decision-making rather than automatically determine recruitment outcomes.

---

# 16. Administration

IT Talent includes an administrative role for authorized platform management.

The intended administration experience may include:

- user management;
- user search;
- user creation where permitted;
- user updates;
- user deletion where permitted;
- role management;
- skill management.

Administrative functionality must be protected through backend authorization.

Sensitive actions, particularly user deletion and role changes, must follow appropriate permission and validation rules.

The exact administrative capabilities depend on the implemented application functionality.

---

# 17. Platform Structure

The main IT Talent product domains are:

```text
IT Talent
│
├── Authentication
├── Users
├── Candidates
│   ├── Profile
│   └── Skills
├── Recruiters
├── Companies
├── Skills
├── Jobs
├── Job Discovery
├── Matching
├── Applications
├── Dashboard
└── Administration
```

These domains describe the product organization. They do not necessarily represent separate deployable services or public API endpoints.

Technical module boundaries are documented in:

`../architecture/architecture.md`

---

# 18. Product Principles

## 18.1 Skill-Oriented

Skills are a central part of candidate profiles, job requirements, and the platform's matching concept.

## 18.2 Structured

Candidate, recruiter, company, skill, job, and application information is organized in structured data models.

## 18.3 Understandable

Job, candidate, application, and matching information should be presented clearly and consistently.

## 18.4 Role-Based

Platform functionality is organized around the Candidate, Recruiter, and Admin roles.

## 18.5 Candidate-Focused

Candidates should be able to maintain their professional profiles, discover relevant jobs, apply, and track their applications.

## 18.6 Recruiter-Focused

Recruiters should be able to manage jobs, review candidate information, and handle applications within their authorized company context.

## 18.7 Multilingual

The user interface supports English and Dutch.

## 18.8 Secure and Privacy-Aware

Personal and recruitment-related information must be handled according to authorization, privacy, and data-protection requirements.

## 18.9 Explainable Matching

Matching information should be understandable and based on identifiable criteria wherever matching functionality is provided.

---

# 19. Product Value

## 19.1 Value for Candidates

IT Talent aims to provide candidates with:

- a structured professional IT profile;
- skill management;
- job discovery;
- job search and filtering;
- relevant matching information;
- job applications;
- application tracking.

## 19.2 Value for Recruiters

IT Talent aims to provide recruiters with:

- recruiter and company context;
- job management;
- structured job requirements;
- candidate discovery;
- matching information where supported;
- application review and management.

## 19.3 Value for Administrators

IT Talent aims to provide administrators with:

- user management;
- permitted role management;
- skill management;
- platform administration functionality.

---

# 20. Current Product Direction

The current IT Talent product is centered on the following domains:

- authentication;
- candidate profiles;
- recruiter profiles;
- company relationships;
- skills;
- jobs;
- job discovery;
- job search and filtering;
- job details;
- applications;
- application status tracking;
- role-specific dashboards;
- administration;
- English and Dutch language support.

Matching, AI-assisted processing, advanced recommendations, and ranking represent product capabilities that require separate implementation verification.

The current implementation status of individual features is maintained through the functional requirements and technical documentation.

---

# 21. Vision Statement

**IT Talent connects IT professionals and recruiters through structured professional profiles, skills, job opportunities, applications, and relevant matching information.**

The platform aims to make IT recruitment more structured, transparent, and accessible for both candidates and recruiters.

---

# 22. Document Status

**Document:** `vision.md`  
**Version:** `0.3.0`  
**Status:** Product Vision  
**Last updated:** 2026-11-09

Related documentation:

- `requirements.md`
- `../architecture/architecture.md`
- `../architecture/api.md`
- `../architecture/database.md`
- `../architecture/security.md`

This document defines the product vision and intended direction of IT Talent.

Functional requirements are described in `requirements.md`.

Technical implementation details are maintained in the corresponding architecture and API documentation.