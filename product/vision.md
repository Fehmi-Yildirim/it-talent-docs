# IT Talent Platform — Product Vision

**Document:** `vision.md`
**Version:** `0.2.0`
**Status:** Product Vision
**Last updated:** 2026-09-10

---

# 1. Product Vision

IT Talent is a digital platform for the IT labor market that connects candidates and recruiters through professional profiles, skills, jobs, applications, and matching.

The platform organizes information around:

* professional profiles;
* skills;
* experience;
* job requirements;
* location;
* work mode;
* employment type;
* salary;
* availability;
* preferences;
* applications.

IT Talent helps candidates discover relevant IT jobs and helps recruiters manage jobs, discover candidates, and review applications.

---

# 2. Product Concept

The core of IT Talent connects candidate profiles with available jobs.

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
```

The platform uses structured candidate and job information to provide relevant matching information.

---

# 3. User Roles

IT Talent supports three user roles:

```text
CANDIDATE
RECRUITER
ADMIN
```

## 3.1 Candidates

Candidates can:

* create an account;
* manage a professional profile;
* add and manage skills;
* manage professional information;
* define salary expectations;
* define availability;
* define work preferences;
* search for jobs;
* filter and sort jobs;
* view job details;
* apply for jobs;
* submit a cover letter;
* view applications;
* view application status;
* withdraw applications where supported.

## 3.2 Recruiters

Recruiters can:

* manage a recruiter profile;
* work within a company context;
* create jobs;
* manage jobs;
* define job information;
* define required and preferred skills;
* publish and manage vacancies;
* view candidates;
* view candidate-related information;
* view applications.

## 3.3 Administrators

Administrators can:

* manage users;
* manage user information;
* manage roles;
* manage skills.

---

# 4. Candidate Profiles

The candidate profile contains the professional information used throughout the platform.

Profile information includes:

* professional headline;
* summary;
* location;
* salary expectations;
* currency;
* availability;
* remote-work preference;
* skills;
* professional experience;
* CV information where available.

Candidates can maintain their profile information through the profile experience.

---

# 5. Skills

Skills are a central part of IT Talent.

The platform supports structured skills for candidates and jobs.

Candidate skill information can include:

* skill;
* proficiency;
* years of experience.

Jobs can contain:

* required skills;
* preferred skills.

Skills can also be managed through the administration functionality.

---

# 6. Jobs

Recruiters can create and manage jobs.

A job can contain:

* title;
* description;
* company;
* location;
* work mode;
* employment type;
* salary;
* required skills;
* preferred skills;
* job status.

Supported work modes include:

* remote;
* hybrid;
* onsite;
* flexible.

---

# 7. Job Discovery

Candidates can discover available jobs through the job discovery experience.

Job discovery supports:

* job listings;
* keyword search;
* location filtering;
* work-mode filtering;
* employment-type filtering;
* minimum salary filtering;
* maximum salary filtering;
* skill filtering;
* sorting;
* pagination;
* job details.

Candidates can open a job to view its detailed information, including:

* description;
* company information;
* location;
* work mode;
* employment type;
* salary;
* required skills;
* preferred skills.

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
   ├── Salary
   └── Preferences
          │
          ▼
      Matching
          │
          ▼
         Job
```

Matching helps identify relevant relationships between candidate profiles and jobs.

Match information can be used to understand how candidate skills, experience, and preferences relate to job requirements.

---

# 9. Applications

Candidates can apply for jobs through the platform.

```text
Candidate
    ↓
   Job
    ↓
Application
```

An application contains information such as:

* candidate;
* job;
* application date;
* cover letter;
* application status.

Application statuses include:

* pending;
* reviewing;
* accepted;
* rejected;
* withdrawn.

Candidates can view their applications and their current status.

Recruiters can view applications associated with their jobs.

---

# 10. Recruiter Job Management

The recruiter experience provides job management within the company context.

The workflow includes:

```text
Create Job
    ↓
Add Job Information
    ↓
Add Skills
    ↓
Publish / Manage Job
    ↓
View Candidates
    ↓
View Applications
```

Recruiters can manage their jobs and review candidates and applications related to their recruitment activities.

---

# 11. Company Context

Jobs are associated with a company.

The company context provides organizational information for recruiter and job management.

Recruiters can manage jobs within their company context.

---

# 12. Dashboards

IT Talent provides role-specific dashboard experiences.

## Candidate Dashboard

The candidate dashboard provides information such as:

* profile completion;
* applications;
* available jobs;
* recommended jobs;
* skills;
* recent applications;
* recent jobs.

## Recruiter Dashboard

The recruiter dashboard provides information such as:

* company information;
* jobs by status;
* applications;
* recent candidates;
* recent jobs.

## Admin Dashboard

The admin experience provides access to platform management functionality, including:

* user management;
* skill management.

---

# 13. Authentication & Access

IT Talent provides authentication and role-based access.

The platform supports:

* candidate authentication;
* recruiter authentication;
* administrator access;
* role-specific navigation;
* protected functionality.

Each role receives access to the functionality associated with that role.

---

# 14. Multilingual Platform

IT Talent supports multiple languages in the user interface.

Current languages are:

* **English — default**
* **Dutch**

The application uses a shared translation system for interface text.

Users can select the application language through the language functionality.

---

# 15. AI-Assisted Functionality

AI-assisted functionality supports the processing of candidate and job information.

The platform architecture supports AI-assisted processing for structured information such as:

* CV information;
* skills;
* job information;
* matching information.

AI-assisted functionality supports the platform data and user experience.

---

# 16. Administration

Administrators can manage platform data through the administration experience.

Administration includes:

* user management;
* user search;
* user creation;
* user updates;
* user deletion;
* role management;
* skill management.

Administrative functionality is protected by role-based access control.

---

# 17. Platform Structure

The main IT Talent platform domains are:

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

---

# 18. Product Principles

## 18.1 Skill-oriented

Skills are a central part of candidate profiles, jobs, and matching.

## 18.2 Structured

Candidate, recruiter, company, skill, job, and application information is structured within the platform.

## 18.3 Understandable

Job, candidate, application, and matching information is presented in a clear and structured way.

## 18.4 Role-based

Platform functionality is organized around the Candidate, Recruiter, and Admin roles.

## 18.5 Candidate-focused

Candidates can manage their professional profile, discover jobs, apply, and track their applications.

## 18.6 Recruiter-focused

Recruiters can manage jobs, view candidates, and manage applications within their company context.

## 18.7 Multilingual

The user interface supports English and Dutch.

---

# 19. Product Value

For candidates, IT Talent provides:

* a professional IT profile;
* skill management;
* job discovery;
* job search and filtering;
* matching information;
* job applications;
* application tracking.

For recruiters, IT Talent provides:

* recruiter and company context;
* job management;
* structured job requirements;
* candidate discovery;
* matching information;
* application management.

For administrators, IT Talent provides:

* user management;
* role management;
* skill management.

---

# 20. Current Product Definition

The current IT Talent product is centered on:

* authentication;
* candidate profiles;
* recruiter profiles;
* skills;
* companies;
* jobs;
* job search;
* job filtering;
* job details;
* matching;
* applications;
* role-specific dashboards;
* user administration;
* skill administration;
* English and Dutch language support.

These capabilities define the current IT Talent product experience.

---

# 21. Vision Statement

**IT Talent connects IT professionals and recruiters through professional profiles, skills, jobs, applications, and relevant matching information.**

---

# 22. Document Status

**Version:** 0.2.0
**Status:** Product Vision
**Last updated:** 2026-09-10

This document defines the current product vision and core capabilities of IT Talent.

Detailed functional requirements are defined in:

`product/requirements.md`
