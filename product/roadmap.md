# IT Talent Platform — Product Roadmap

**Document:** `roadmap.md`
**Version:** `0.2.0`
**Status:** Product Roadmap
**Last updated:** 2026-09-10

---

# 1. Purpose

This roadmap describes the functional structure and development direction of the current IT Talent Platform.

The roadmap is based on the current product design and organizes the platform into logical development areas.

The platform is centered around:

* candidates;
* recruiters;
* companies;
* skills;
* jobs;
* matching;
* applications;
* dashboards;
* administration.

---

# 2. Development Structure

The platform is organized into the following areas:

```text
IT Talent Platform
│
├── Foundation
│   └── Authentication & Access
│
├── Candidate
│   ├── Profile
│   ├── Skills
│   ├── Job Discovery
│   └── Applications
│
├── Recruiter
│   ├── Profile
│   ├── Company
│   ├── Jobs
│   ├── Candidates
│   └── Applications
│
├── Matching
│
├── Administration
│   ├── Users
│   └── Skills
│
└── Platform
    ├── Dashboard
    └── Languages
```

---

# 3. Platform Foundation

The foundation provides the functionality required by all platform users.

Core areas:

* application structure;
* authentication;
* registration;
* login;
* logout;
* role-based access;
* API communication;
* user management;
* language support.

Supported roles:

```text
CANDIDATE
RECRUITER
ADMIN
```

---

# 4. Candidate Experience

The candidate experience is built around the professional profile and job discovery.

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

* registration;
* login;
* profile management;
* skill management;
* job search;
* job filtering;
* job sorting;
* job details;
* job matching;
* job applications;
* application status;
* application withdrawal.

---

# 5. Candidate Profile

The candidate profile provides the professional information used throughout the platform.

The profile includes:

* professional headline;
* summary;
* location;
* salary expectation;
* currency;
* availability;
* remote-work preference;
* skills;
* experience information.

The profile is the central source of candidate information for job discovery and matching.

---

# 6. Skills

Skills are a central platform component.

The skill functionality is used by:

* candidates;
* jobs;
* matching;
* administration.

Candidate skills can contain:

* skill;
* proficiency;
* years of experience.

Candidates can:

* add skills;
* edit skills;
* remove skills.

Administrators can manage the platform skill catalog.

---

# 7. Job Discovery

Candidates can discover jobs through the job discovery experience.

The job discovery functionality includes:

* job listings;
* search;
* location filtering;
* work-mode filtering;
* employment-type filtering;
* salary filtering;
* skill filtering;
* sorting;
* pagination.

Supported work modes include:

* remote;
* hybrid;
* onsite;
* flexible.

---

# 8. Job Details

Candidates can open individual jobs to view detailed information.

Job details include:

* job title;
* description;
* company information;
* location;
* work mode;
* employment type;
* salary;
* required skills;
* preferred skills.

The job detail experience also provides access to the application flow.

---

# 9. Matching

Matching connects candidate profiles with jobs.

The matching process uses structured information such as:

```text
Candidate
│
├── Skills
├── Experience
├── Location
├── Salary
├── Availability
└── Preferences
        │
        ▼
     Matching
        │
        ▼
       Job
```

Matching information can be used to:

* identify relevant jobs;
* identify relevant candidates;
* present compatibility;
* support candidate discovery;
* support recruiter candidate review.

---

# 10. Applications

Applications are part of the current platform experience.

The application flow is:

```text
Candidate
    ↓
Job
    ↓
Apply
    ↓
Application
    ↓
Application Status
```

Candidates can:

* apply for jobs;
* add a cover letter;
* view applications;
* view application details;
* track application status;
* withdraw applications where supported.

Supported application statuses include:

* pending;
* reviewing;
* accepted;
* rejected;
* withdrawn.

Recruiters can view applications associated with their jobs.

---

# 11. Recruiter Experience

The recruiter experience is focused on managing recruitment information.

```text
Recruiter
   │
   ├── Profile
   ├── Company
   ├── Jobs
   ├── Candidates
   └── Applications
```

Recruiters can:

* manage recruiter information;
* work within a company context;
* create jobs;
* manage jobs;
* define required skills;
* define preferred skills;
* publish jobs;
* manage job status;
* view candidates;
* review applications.

---

# 12. Company Context

Jobs are managed within a company context.

The company information is associated with recruiter and job functionality.

The company context supports:

* company information;
* recruiter association;
* job management;
* candidate and application context.

---

# 13. Job Management

Recruiters can create and manage jobs.

The job management flow is:

```text
Create Job
    ↓
Enter Job Information
    ↓
Add Required / Preferred Skills
    ↓
Manage Job
    ↓
Publish
    ↓
Review Candidates
    ↓
Review Applications
```

Job information includes:

* title;
* description;
* location;
* work mode;
* employment type;
* salary;
* required skills;
* preferred skills.

---

# 14. Recruiter Candidate Experience

Recruiters can view candidate-related information through the recruiter experience.

Candidate information can be used together with matching information to support candidate discovery and review.

The recruiter workflow is:

```text
Job
 ↓
Matching Candidates
 ↓
Candidate Information
 ↓
Application Information
```

---

# 15. Dashboards

The platform provides role-specific dashboards.

## Candidate Dashboard

The candidate dashboard provides:

* profile completion;
* applications;
* available jobs;
* recommended jobs;
* skills;
* recent applications;
* recent jobs.

## Recruiter Dashboard

The recruiter dashboard provides:

* company information;
* jobs by status;
* applications;
* recent candidates;
* recent jobs.

## Admin Dashboard

The admin experience provides access to platform management functionality.

---

# 16. Administration

Administration provides management functionality for platform data.

Current administration areas include:

```text
Administration
│
├── Users
└── Skills
```

Administrators can manage:

* users;
* user information;
* roles;
* skills.

---

# 17. Multilingual Platform

The platform supports multiple interface languages.

Current languages:

* English;
* Dutch.

English is the default language.

The application uses a shared translation system for interface content.

The language functionality is available through the application interface.

---

# 18. Platform Navigation

The main application navigation is organized around the user's role.

Common functionality includes:

```text
Dashboard
Profile
Jobs / Find Jobs
Applications
Language
Logout
```

Additional navigation and functionality are provided according to the authenticated role.

---

# 19. Functional Development Order

The platform functionality can be understood in the following order:

```text
Foundation
    ↓
Authentication
    ↓
User Roles
    ↓
Candidate / Recruiter Profiles
    ↓
Skills
    ↓
Companies
    ↓
Jobs
    ↓
Job Discovery
    ↓
Matching
    ↓
Applications
    ↓
Dashboards
    ↓
Administration
    ↓
Multilingual Interface
```

These areas form the main functional structure of IT Talent.

---

# 20. Current Product Flow

The complete platform experience connects the main functional areas:

```text
                    IT Talent
                       │
          ┌────────────┴────────────┐
          │                         │
      Candidate                  Recruiter
          │                         │
       Profile                   Company
          │                         │
        Skills                    Jobs
          │                         │
          └──────────┬──────────────┘
                     │
                  Matching
                     │
              ┌──────┴──────┐
              │             │
             Jobs       Candidates
              │             │
              └──────┬──────┘
                     │
                Applications
                     │
                  Dashboard
```

---

# 21. Current Product Areas

The current IT Talent roadmap consists of these functional areas:

| Area           | Main functionality                       |
| -------------- | ---------------------------------------- |
| Foundation     | Authentication, roles, API communication |
| Candidate      | Profile, skills, preferences             |
| Recruiter      | Profile, company context                 |
| Jobs           | Job creation, management, publishing     |
| Job Discovery  | Search, filters, sorting, pagination     |
| Skills         | Skill selection and administration       |
| Matching       | Candidate/job compatibility              |
| Applications   | Apply, status, withdrawal                |
| Dashboards     | Candidate, recruiter, admin              |
| Administration | Users and skills                         |
| Languages      | English and Dutch                        |

---

# 22. Product Development Direction

Development of IT Talent should continue by extending the existing platform areas while keeping the core user flows consistent.

The primary functional flow remains:

```text
Candidate
   ↓
Profile
   ↓
Skills
   ↓
Jobs
   ↓
Matching
   ↓
Application
```

For recruiters:

```text
Recruiter
   ↓
Company
   ↓
Job
   ↓
Skills
   ↓
Matching
   ↓
Candidates
   ↓
Applications
```

---

# 23. Roadmap Principles

## 23.1 User-Centered

Development should support the main Candidate, Recruiter, and Admin experiences.

## 23.2 Skill-Oriented

Skills remain a central component of profiles, jobs, and matching.

## 23.3 Structured

Candidate, recruiter, company, job, skill, matching, and application information should remain structured.

## 23.4 Consistent

New functionality should follow the existing application structure, navigation, and interaction patterns.

## 23.5 Multilingual

New user-facing functionality should support the platform's English and Dutch interface.

## 23.6 Role-Based

Functionality should remain aligned with the permissions and responsibilities of each platform role.

---

# 24. Current Roadmap Status

The product documentation defines the current functional structure of IT Talent:

```text
Product Vision       ✓
Product Requirements ✓
Product Roadmap      ✓

Candidate            ✓
Recruiter            ✓
Company              ✓
Skills               ✓
Jobs                 ✓
Job Discovery        ✓
Matching             ✓
Applications         ✓
Dashboards           ✓
Administration       ✓
English / Dutch      ✓
```

---

# 25. Next Development Focus

Future development should build on the existing platform structure and continue improving the core experiences:

```text
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
Applications
    ↓
Platform Management
```

The roadmap remains centered on the current IT Talent product experience.
