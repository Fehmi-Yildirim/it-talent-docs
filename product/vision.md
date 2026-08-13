# IT Talent Platform — Product Vision

**Document:** `vision.md`
**Version:** `0.1.0`
**Status:** Draft / MVP Product Vision
**Last updated:** 2026-08-12

---

# 1. Product Vision

IT Talent is a digital platform that connects:

- IT professionals;
- IT job seekers;
- recruiters;
- technology companies.

The platform focuses on **skills, experience and compatibility** rather than relying exclusively on traditional CV-based recruitment.

The long-term goal is to make the recruitment process:

- faster;
- more transparent;
- more relevant;
- more data-driven;
- easier for candidates;
- more efficient for recruiters.

---

# 2. Problem

Traditional IT recruitment often relies heavily on:

- CV screening;
- keyword searches;
- LinkedIn profiles;
- manual recruiter interpretation;
- disconnected recruitment systems;
- subjective candidate evaluation.

This creates several problems.

## Candidates

Candidates may be rejected because:

- their CV does not contain the exact keyword;
- their experience is described differently from the job description;
- recruiters do not discover them;
- their actual skills are difficult to evaluate.

## Recruiters

Recruiters often need to:

- read many CVs;
- compare candidates manually;
- interpret technical skills;
- determine whether experience is relevant;
- contact candidates who turn out not to be a good fit.

## Companies

Companies want:

- better candidates;
- faster hiring;
- lower recruitment costs;
- higher quality matches.

---

# 3. Product Opportunity

The IT labor market contains a large amount of structured and semi-structured information:

```text
Candidate
    │
    ├── Skills
    ├── Experience
    ├── Location
    ├── Availability
    ├── Salary expectations
    └── Preferences

Job
    │
    ├── Required skills
    ├── Preferred skills
    ├── Experience
    ├── Location
    ├── Salary
    ├── Work mode
    └── Employment type

    4. Core Product Concept

The core product concept is:

Skills + Experience + Preferences
                    │
                    ▼
              Matching Engine
                    │
                    ▼
           Candidate ↔ Job

The platform calculates a compatibility score and explains the result.

Example:

Senior React Developer
        │
        ▼
Candidate A

Overall Match: 91%

Skills          95%
Experience      90%
Location        100%
Salary          85%
Availability    100%
Preferences      80%

The score should always be accompanied by understandable reasons.

5. Target Users

The platform has three primary user groups.

5.1 IT Professionals

People who already work in IT.

Examples:

software developers;
DevOps engineers;
cloud engineers;
data engineers;
security specialists;
QA engineers;
IT project managers;
architects.

Their objective is to discover relevant career opportunities without repeatedly tailoring their CV to every job.

6. Job Seekers

The platform also targets people actively looking for work.

They may:

create a profile;
upload a CV;
specify skills;
specify experience;
define preferences;
discover relevant jobs;
receive match recommendations.

The distinction between "IT professional" and "job seeker" is primarily behavioral.

A professional may be:

actively looking

or:

passively open to opportunities
7. Recruiters

Recruiters use the platform to:

create vacancies;
define required skills;
search candidates;
view candidate matches;
understand match quality;
shortlist candidates.

The platform should reduce manual candidate screening.

8. Companies

Companies use the platform to:

manage their company profile;
create jobs;
manage recruiters;
find suitable candidates;
eventually manage the recruitment pipeline.

Company functionality will initially be centered around recruiters.

9. Primary Value Proposition
For candidates

Find opportunities that match your real skills, not just your CV keywords.

For recruiters

Find the most relevant IT professionals faster.

For companies

Reduce recruitment time and improve candidate quality.

10. Product Differentiation

The platform should not attempt to become another generic job board.

The primary differentiator is:

Skill-first matching

rather than:

Job listing + keyword search

The platform should eventually understand relationships such as:

React
TypeScript
Next.js
Node.js
AWS

and recognize that these skills form a meaningful technical profile.

11. MVP Product

The MVP should answer one fundamental question:

Can we create meaningful matches between IT candidates and IT jobs?

The MVP therefore focuses on:

Candidate Profile
        +
Skills
        +
Job Requirements
        ↓
    Matching
        ↓
Match Score

Everything that does not directly contribute to validating this proposition should have lower priority.

12. MVP Candidate Experience

A candidate should be able to:

create an account;
create a professional profile;
enter skills;
specify experience;
specify preferences;
upload a CV;
receive relevant job matches;
inspect why a job matches their profile.
13. MVP Recruiter Experience

A recruiter should be able to:

create an account;
create/join a company;
create a job;
specify required skills;
specify preferred skills;
publish the job;
see matching candidates;
inspect match explanations.
14. MVP Matching Experience

The matching engine should compare:

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
             JOB

The output:

Overall Score
+
Component Scores
+
Strengths
+
Potential Gaps
15. AI Role

AI is an important enabling technology but should not be the product itself.

AI may initially be used for:

CV skill extraction;
job-description skill extraction;
skill normalization;
natural-language explanations.

Example:

CV
 ↓
AI extraction
 ↓
"React", "TypeScript", "AWS"
 ↓
Skill normalization
 ↓
CandidateSkill records

The core product remains the structured matching system.

16. Why This Matters

This architecture prevents the product from becoming:

"ChatGPT with a recruitment UI."

Instead, the platform becomes:

A structured IT talent marketplace with AI-assisted data processing and matching.

This is an important strategic distinction.

17. Product Principles

The product follows these principles.

17.1 Candidate First

Candidates should understand:

how their profile is used;
why they match a job;
what information recruiters can see.
17.2 Explainability

A match score should not be a mysterious number.

Instead:

91% match

Why?

+ Strong React experience
+ Strong TypeScript experience
+ Relevant AWS experience
+ Salary range overlaps

Potential gap:

- Limited Kubernetes experience
17.3 Human-in-the-Loop

AI assists humans.

It does not replace:

candidate judgment;
recruiter judgment;
hiring decisions.
18. No Black Box Hiring Decisions

The platform should not make irreversible employment decisions automatically.

For example, the system should not simply say:

Candidate rejected.

based exclusively on an AI score.

Instead, the system should provide decision-support information.

19. Transparency

Users should eventually be able to understand:

which skills were detected;
which skills were matched;
which requirements were missing;
how the score was calculated;
which information is AI-generated.
20. Candidate Control

Candidates should eventually control:

profile visibility;
job-seeking status;
recruiter discoverability;
CV visibility;
contact preferences.
21. Recruiter Efficiency

The recruiter experience should optimize for:

Discover
    ↓
Filter
    ↓
Match
    ↓
Understand
    ↓
Shortlist
    ↓
Contact

rather than:

Open CV
Read CV
Open LinkedIn
Search skills
Compare
Repeat...
22. Company Value

The ultimate business value for companies is not the number of CVs in the system.

It is:

Qualified candidates
        ×
Relevant opportunities
        ×
Speed

The platform should therefore optimize for match quality, not simply candidate volume.

23. Marketplace Model

The long-term platform is a two-sided marketplace.

        IT Talent Platform

Candidates  ←────────────→  Companies
IT Pros                      Recruiters
Job Seekers                  Hiring Teams

The platform becomes more valuable as both sides grow.

This creates a marketplace challenge:

The product needs enough relevant candidates and jobs to produce useful matches.

24. Initial Market Strategy

The initial product should avoid trying to cover:

all jobs
all industries
all countries
all professions

Instead, start narrow.

Possible initial niche:

IT professionals
+
software/technology companies
+
Netherlands

This makes the matching problem more manageable.

25. Initial Geographic Focus

The architecture should remain internationalizable.

However, the MVP may initially focus on the Dutch market.

Potential future expansion:

Netherlands
   ↓
Benelux
   ↓
Europe
   ↓
International

The product should avoid hard-coding Dutch-specific assumptions into the core architecture.

26. Business Model — Initial Hypothesis

Potential revenue models include:

Recruiter subscription
Free
Professional
Business
Enterprise
Employer subscription

Companies pay for:

more jobs;
more candidate searches;
advanced matching;
analytics.
Recruitment credits

Recruiters purchase:

candidate contact credits
Premium matching

Advanced matching capabilities could become a paid feature.

27. Free Tier

The platform should have a useful free tier during early adoption.

Potential free functionality:

Candidates
profile;
skills;
job discovery;
basic matching.
Recruiters
limited jobs;
limited candidate searches;
basic matching.

The objective is product adoption rather than immediate monetization.

28. Monetization Principle

The product should monetize efficiency and access, not basic candidate identity.

We should avoid creating a model where candidates must pay simply to be discoverable.

29. Key Product Metrics

The MVP should measure:

Candidate metrics
Registered candidates
Completed profiles
CV uploads
Profiles with skills
Job matches viewed
Jobs saved
Recruiter metrics
Registered recruiters
Jobs created
Jobs published
Candidates viewed
Matches viewed
Candidates shortlisted
Marketplace metrics
Candidate ↔ Job matches
High-quality matches
Match-to-contact rate
Contact-to-interview rate
Interview-to-hire rate
30. North Star Metric

A potential North Star Metric is:

Qualified candidate-job interactions per active month.

A qualified interaction could mean:

Recruiter views a relevant candidate

or:

Candidate meaningfully engages with a relevant job

The exact metric should be validated after MVP usage data becomes available.

31. Quality Over Quantity

The platform should avoid optimizing purely for:

number of matches

Instead:

relevant matches

are more valuable.

100 poor matches are worse than:

10 highly relevant matches
32. MVP Success Criteria

The MVP should be considered promising if early users demonstrate that:

candidates complete profiles;
recruiters create real jobs;
the matching engine produces believable results;
recruiters prefer ranked candidates over manual searching;
candidates understand why jobs are recommended;
users return to the platform.
33. Product Risks

Important risks include:

Cold start

Without candidates:

Recruiters have little value.

Without jobs:

Candidates have little value.
Matching quality

Poor matches destroy trust quickly.

AI trust

Incorrect AI-generated skills can damage candidate profiles.

Data quality

Incomplete profiles produce weak matches.

Privacy

Candidate data must be handled carefully.

Competition

The recruitment market contains established platforms and ATS providers.

The product therefore needs a clear differentiator.

34. Competitive Positioning

The platform should not initially attempt to compete with every recruitment platform.

Instead, position around:

IT-specialized
+
skills-first
+
explainable matching
+
AI-assisted

This creates a more focused proposition.

35. Long-Term Vision

The long-term platform could evolve from:

Job Matching

into:

IT Talent Intelligence Platform

Potential capabilities:

Skills graph
Talent discovery
Career recommendations
Skill-gap analysis
Learning recommendations
Salary intelligence
Recruitment analytics
Talent pools
Internal mobility
AI-assisted recruiting

These are future possibilities, not MVP requirements.

36. Product Evolution

Possible evolution:

Phase 1
Job ↔ Candidate Matching

        ↓

Phase 2
Talent Discovery

        ↓

Phase 3
Recruitment Workflow

        ↓

Phase 4
Talent Intelligence

        ↓

Phase 5
IT Talent Marketplace
37. Product Philosophy

The platform should ultimately answer three questions extremely well:

Candidate

"Which jobs are genuinely relevant to me?"

Recruiter

"Which candidates are genuinely relevant to this job?"

Company

"How can we hire the right IT professional faster?"

38. Vision Statement

IT Talent makes IT recruitment more intelligent by connecting people and opportunities through skills, experience and transparent matching.

39. Product Vision Status

Version 0.1.0

This document defines the long-term product direction and MVP philosophy.

Detailed functional requirements are defined separately in:

product/requirements.md
Hierna

Onze documentatie begint nu echt een samenhangend geheel te vormen:

it-talent-docs/
│
├── README.md
│
├── architecture/
│   ├── architecture.md       ✅
│   ├── database.md           ✅
│   ├── api.md                ✅
│   └── security.md           ✅
│
├── product/
│   ├── vision.md             ✅
│   ├── requirements.md       ← VOLGENDE
│   └── roadmap.md
│
└── decisions/
    └── README.md