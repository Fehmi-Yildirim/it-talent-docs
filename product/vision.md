# IT Talent Platform — Product Vision

**Document:** `vision.md`
**Version:** `0.2.0`
**Status:** Product Vision
**Last updated:** 2026-09-10

---

# 1. Product Vision

IT Talent is a digital platform for the IT labor market that connects IT professionals, job seekers, recruiters, and technology companies.

The platform organizes talent and job information around:

* skills;
* experience;
* professional profiles;
* job requirements;
* preferences;
* availability;
* location;
* employment conditions;
* transparent matching.

IT Talent helps candidates discover relevant opportunities and helps recruiters and companies find and evaluate suitable IT professionals.

---

# 2. Product Concept

The core of IT Talent consists of three elements:

```text
Candidate Profile
       +
Skills & Experience
       +
Job Requirements
       ↓
    Matching
       ↓
Candidate ↔ Job
```

The platform combines structured candidate and job information to determine relevant matches.

A match can consider:

* matching skills;
* relevant experience;
* location;
* work mode;
* salary;
* availability;
* preferences.

---

# 3. Target Users

IT Talent supports three primary user groups.

## 3.1 Candidates

Candidates can:

* create an account;
* manage a professional profile;
* add skills;
* record experience;
* manage preferences;
* discover jobs;
* view relevant jobs;
* view matches;
* express interest in jobs;
* manage applications.

A candidate can be actively looking for work or open to relevant opportunities.

## 3.2 Recruiters

Recruiters can:

* manage a recruiter profile;
* belong to a company;
* create jobs;
* manage jobs;
* define job requirements;
* search candidates;
* view candidates;
* view matches;
* shortlist candidates;
* manage recruitment activities.

## 3.3 Companies

Companies can:

* manage a company profile;
* manage recruiters;
* publish jobs;
* discover candidates;
* evaluate suitable candidates;
* support recruitment activities.

---

# 4. Candidate Profiles

The candidate profile forms the foundation of the talent side of the platform.

A profile can contain:

* personal information;
* professional title;
* summary;
* skills;
* experience level;
* work experience;
* education;
* location;
* availability;
* salary expectations;
* work preferences;
* CV;
* visibility settings.

Skills are an important part of the professional profile.

Candidates can record information such as proficiency and relevant experience for their skills.

---

# 5. Skills

IT Talent uses a structured skill catalog.

A skill can contain:

* name;
* slug;
* category;
* description.

Skill categories can include:

* Programming;
* Frontend;
* Backend;
* Cloud;
* DevOps;
* Data;
* Security;
* Testing;
* Architecture;
* Management.

Candidate skills can contain additional information such as:

* proficiency;
* years of experience;
* source.

Candidates can add self-reported skills.

The source of skill information is explicitly recorded.

---

# 6. Jobs

Recruiters can create jobs on behalf of a company.

A job can contain:

* title;
* description;
* location;
* work mode;
* employment type;
* salary;
* required experience;
* required skills;
* preferred skills;
* status;
* publication information.

Skills can be divided into:

**Required skills**

Skills that are essential for the position.

**Preferred skills**

Skills that are valuable but not essential.

---

# 7. Job Discovery

Candidates can discover and view available jobs.

Job discovery supports:

* job listings;
* search;
* filtering;
* job details;
* relevant jobs;
* match information.

Candidates can see how their profile relates to a job.

---

# 8. Matching

Matching is a core function of IT Talent.

The platform compares candidate and job information.

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

Matching can produce:

* overall match;
* skill match;
* experience match;
* location match;
* salary match;
* availability match;
* preference match.

Matching results are supported by understandable information about strengths and gaps.

---

# 9. Match Explanation

IT Talent makes matching understandable.

A match can show:

```text
Match: 91%

Strong matches
+ React
+ TypeScript
+ AWS
+ Relevant experience

Potential gaps
- Limited Kubernetes experience
```

This gives users insight into why a candidate and job match.

---

# 10. Applications

Candidates can participate in the application process through the platform.

Applications connect:

```text
Candidate
    ↓
Job
    ↓
Application
```

An application can contain:

* candidate;
* job;
* status;
* application date;
* cover letter;
* recruitment information.

Recruiters can manage applications throughout the recruitment process.

---

# 11. Recruiter Workflow

The recruiter workflow supports:

```text
Create Job
    ↓
Define Requirements
    ↓
Discover Candidates
    ↓
View Matches
    ↓
Understand Match
    ↓
Shortlist
    ↓
Contact
    ↓
Manage Application
```

---

# 12. Company Management

Companies provide the organizational context for recruiters and jobs.

A company profile can contain:

* company name;
* company information;
* location;
* website;
* recruiters;
* jobs.

Recruiters can manage jobs and candidate discovery within the company context.

---

# 13. Authentication & Roles

IT Talent supports the following user roles:

```text
CANDIDATE
RECRUITER
ADMIN
```

Each role has access to functions appropriate to that role.

Candidates manage their professional information.

Recruiters manage recruitment activities within their company context.

Administrators manage platform users and platform data.

---

# 14. Administration

Administrators can manage platform data and users.

Administration supports:

* viewing users;
* searching users;
* creating users;
* updating users;
* deleting users;
* managing roles;
* managing skills.

Administrative functions are protected by role-based access control.

---

# 15. Multilingual Platform

IT Talent supports multiple languages in the user interface.

Current language support:

* **English — default**
* **Dutch — secondary**

The multilingual structure allows additional languages to be introduced without changing the core platform functionality.

---

# 16. AI-Assisted Capabilities

AI can support IT Talent in processing and interpreting information.

Potential applications include:

* CV skill extraction;
* job description skill extraction;
* skill normalization;
* text interpretation;
* match explanations;
* profile assistance.

AI supports the structured data within the platform.

Final candidate and recruitment decisions remain with users.

---

# 17. Transparency

IT Talent provides transparency around profile information and matching.

Users should be able to understand:

* registered skills;
* matched skills;
* missing skills;
* relevant experience;
* match factors;
* profile information used for matching.

A match score is supporting information and is not an automatic hiring decision.

---

# 18. Candidate Control

Candidates maintain control over their professional profile.

The platform supports control over:

* profile information;
* visibility;
* availability;
* job-seeking status;
* CV;
* recruiter discoverability;
* contact preferences.

---

# 19. Recruitment Intelligence

The structured data within IT Talent provides a foundation for recruitment intelligence.

The platform can provide insights into:

* talent availability;
* skill demand;
* skill gaps;
* candidate quality;
* job quality;
* matching quality;
* recruitment activity.

---

# 20. Product Principles

## 20.1 Skill-first

Skills are a central part of candidate and job matching.

## 20.2 Explainable

Matching results should be understandable.

## 20.3 Candidate Control

Candidates maintain control over their professional information.

## 20.4 Human Decision

The platform supports human decision-making.

## 20.5 Structured Data

Candidates, skills, companies, jobs, and applications are structured platform entities.

## 20.6 Secure

Access to data and functionality is controlled through authentication and user roles.

## 20.7 International

The platform supports multiple languages and can be expanded internationally.

---

# 21. Platform Structure

The main IT Talent domains are:

```text
IT Talent
│
├── Authentication
├── Users
├── Candidates
│   ├── Profile
│   └── Skills
├── Skills
├── Recruiters
├── Companies
├── Jobs
├── Job Discovery
├── Matching
├── Applications
├── Dashboard
└── Administration
```

---

# 22. Product Value

For candidates, IT Talent provides:

* a professional IT profile;
* structured skill management;
* job discovery;
* relevant matches;
* match insights;
* application support.

For recruiters, IT Talent provides:

* job management;
* candidate discovery;
* skill-based matching;
* match insights;
* shortlist functionality;
* application management.

For companies, IT Talent provides:

* company management;
* recruiter management;
* job management;
* candidate discovery;
* recruitment support;
* recruitment insights.

---

# 23. Long-Term Product Direction

IT Talent can expand with capabilities such as:

* advanced matching;
* talent discovery;
* skills graph;
* skill-gap analysis;
* career recommendations;
* learning recommendations;
* salary intelligence;
* talent pools;
* recruitment analytics;
* internal mobility;
* AI-assisted recruiting.

These capabilities build on the platform's candidates, skills, companies, jobs, matching, and applications.

---

# 24. Vision Statement

**IT Talent connects IT professionals and organizations through skills, experience, and professional preferences to make relevant opportunities and recruitment matches more transparent and accessible.**

---

# 25. Document Status

**Version:** 0.2.0
**Status:** Product Vision

This document defines the product vision, core capabilities, and future direction of IT Talent.

Detailed functional requirements are defined in:

`product/requirements.md`
