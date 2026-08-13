IT Talent Platform — Security Architecture

Document: security.md
Version: 0.1.0
Status: Draft / MVP Security Architecture
Last updated: 2026-08-12

1. Purpose

This document defines the security architecture for the IT Talent Platform.

The platform processes potentially sensitive professional and personal information, including:

user accounts;
names and contact information;
CVs;
professional experience;
skills;
salary expectations;
location;
employment preferences;
recruiter/company information;
AI-generated candidate/job information.

Security and privacy therefore form part of the architecture from the beginning.

2. Security Objectives

The platform must protect:

Confidentiality — unauthorized users must not access private data.
Integrity — users must not manipulate data they do not own.
Availability — the platform should remain usable and recoverable.
Privacy — personal data must be processed appropriately.
Account security — authentication credentials must be protected.
Traceability — important security-sensitive actions should be auditable.
3. Security Architecture

The basic security boundary is:

┌─────────────────────────────────────────────┐
│                  Internet                   │
└──────────────────────┬──────────────────────┘
                       │
                     HTTPS
                       │
                       ▼
┌─────────────────────────────────────────────┐
│              Next.js Frontend               │
│                                             │
│  UI / Forms / Client State / API Client     │
└──────────────────────┬──────────────────────┘
                       │
                     HTTPS
                       │
                       ▼
┌─────────────────────────────────────────────┐
│               NestJS Backend                │
│                                             │
│ Authentication                              │
│ Authorization                               │
│ Validation                                  │
│ Business Logic                              │
│ Matching                                    │
│ AI Orchestration                            │
└──────────────┬───────────────────┬──────────┘
               │                   │
               ▼                   ▼
       ┌───────────────┐    ┌───────────────┐
       │  PostgreSQL   │    │  AI Provider  │
       └───────────────┘    └───────────────┘

The frontend is considered untrusted.

All important security decisions happen on the backend.

4. Zero Trust Principle

The backend must not trust information merely because it originated from the frontend.

For example, the frontend must never be allowed to determine:

userId
companyId
role
permissions
candidate ownership

The backend derives these from authenticated context and database relationships.

5. Authentication

Authentication verifies the identity of the user.

Initial roles:

CANDIDATE
RECRUITER
ADMIN

The authentication system must support:

registration;
login;
logout;
authenticated sessions;
password reset;
account status;
session/token expiration.

The exact implementation will be finalized during backend development.

6. Password Security

Passwords must never be stored in plaintext.

The database stores only:

passwordHash

The backend will use a modern password hashing algorithm such as:

Argon2id; or
bcrypt with an appropriately strong configuration.

The selected implementation must use a deliberately configured work factor.

7. Password Requirements

The platform should enforce reasonable password requirements.

The goal should be:

strong authentication without unnecessarily hostile password rules.

Passwords should never be logged.

Passwords must never appear in:

API responses;
error messages;
analytics;
application logs;
database exports.
8. Session Security

Authentication sessions/tokens must:

expire;
be revocable;
be protected against theft;
not expose unnecessary information.

If browser cookies are used, they should be configured appropriately with:

Secure
HttpOnly
SameSite

The exact configuration depends on the final deployment architecture.

9. Authorization

Authentication is not authorization.

The backend must verify whether a user is allowed to perform a requested action.

Example:

Candidate A
    │
    └── can edit → Candidate A profile

Candidate A
    │
    └── cannot edit → Candidate B profile

Similarly:

Recruiter A
    │
    └── Company A
          │
          ├── Job 1
          └── Job 2

Recruiter A should not automatically be able to modify:

Company B
Job belonging to Company B
10. Role-Based Access Control

The MVP uses RBAC.

Conceptually:

                    User
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
      Candidate    Recruiter     Admin

Permissions will be enforced through backend guards/policies.

11. Resource-Level Authorization

Role-based authorization alone is insufficient.

Example:

Recruiter

may have permission to edit jobs.

But the backend must additionally check:

Does this job belong to the recruiter's company?

Therefore authorization operates at two levels:

Role
 +
Resource ownership
12. Candidate Privacy

Candidate data requires special attention.

Candidate information should have visibility rules.

Conceptually:

PRIVATE
RECRUITER_VISIBLE
PUBLIC

Not all candidate information should be visible to recruiters.

For example, a recruiter may see:

Name
Professional title
Skills
Experience
Location
Availability

while other information may remain private until the candidate explicitly permits disclosure.

The exact visibility matrix will be defined before candidate discovery is implemented.

13. Company Isolation

Company data must be isolated logically.

Example:

Company A
├── Recruiter A1
├── Recruiter A2
└── Jobs

Company B
├── Recruiter B1
└── Jobs

A recruiter belonging to Company A must not access private Company B resources merely by changing an ID in an API request.

This must be enforced server-side.

14. IDOR Protection

The platform must protect against Insecure Direct Object Reference (IDOR) vulnerabilities.

Unsafe pattern:

GET /api/v1/candidates/123

and assuming that knowing 123 grants access.

The backend must instead evaluate:

Authenticated User
        │
        ▼
Authorization Policy
        │
        ▼
Candidate 123

before returning the resource.

15. Input Validation

All external input must be validated.

This includes:

JSON bodies;
query parameters;
route parameters;
uploaded files;
headers where relevant.

NestJS DTO validation will be used.

Example:

CreateJobDto
UpdateCandidateDto
CreateSkillDto
16. Input Sanitization

The backend must treat external text as untrusted.

This includes:

job descriptions;
candidate summaries;
CV content;
company descriptions;
AI-generated content.

The application must protect against:

SQL injection;
XSS;
malformed input;
injection attacks;
unsafe HTML.

Prisma parameterization provides protection against typical SQL injection when used correctly.

17. XSS Protection

User-generated text must not automatically be rendered as executable HTML.

For example:

Candidate summary
Job description
Company description

should normally be rendered as text or safely sanitized rich text.

The frontend must not use unsafe HTML rendering unless explicitly required and sanitized.

18. CSRF

If cookie-based authentication is used, CSRF protections must be configured appropriately.

The implementation will depend on the final authentication mechanism.

Possible protections include:

SameSite cookies;
CSRF tokens;
origin validation;
strict request policies.
19. CORS

The backend must use an explicit CORS allowlist.

Development may allow:

http://localhost:3000

Production should allow only the official frontend origin.

We should avoid:

Access-Control-Allow-Origin: *

for authenticated production APIs.

20. Rate Limiting

Rate limiting is required to reduce:

brute-force attacks;
credential stuffing;
API abuse;
excessive AI requests;
denial-of-service risk.

Sensitive endpoints should have stricter limits.

Examples:

/auth/login
/auth/register
/jobs/:id/match
/candidates/me/analyze-cv
/jobs/:id/analyze
21. Account Protection

Authentication endpoints should include protections against repeated login attempts.

Possible mechanisms:

rate limiting;
progressive delays;
temporary lockouts where appropriate;
suspicious activity monitoring.

The system should avoid leaking whether an email address is registered.

22. Secrets Management

Secrets must never be committed to GitHub.

Examples:

DATABASE_URL
JWT_SECRET
AI_API_KEY

must be supplied through environment configuration.

Repositories contain:

.env.example

but never real secret values.

23. GitHub Security

The repositories should enable:

branch protection;
pull requests;
secret scanning where available;
dependency alerts;
Dependabot or equivalent dependency updates;
required CI checks.

The main branch should be protected.

Direct production changes should not be made through unreviewed commits.

24. Dependency Security

Dependencies must be kept reasonably current.

The project should regularly run:

npm audit

and automated dependency scanning where available.

High-risk vulnerabilities should be investigated promptly.

We should avoid unnecessary dependencies.

25. Database Security

The PostgreSQL database must:

use encrypted connections where supported;
use strong credentials;
not expose credentials to frontend code;
use least-privilege access;
use backups in production;
restrict network access where possible.
26. Database Credentials

Only the backend should have access to:

DATABASE_URL

The frontend must never receive it.

Never use:

NEXT_PUBLIC_DATABASE_URL

or equivalent.

Anything prefixed with NEXT_PUBLIC_ should be treated as browser-visible.

27. CV Security

CVs require additional protection because they may contain substantial personal information.

CV files must:

have controlled access;
have validated MIME/type;
have size limits;
not be executable;
not be served directly from the application server without authorization;
be stored in appropriate object storage when implemented.
28. CV Upload Threats

The system must protect against malicious uploads.

Potential threats include:

Executable files
Malicious PDFs
Oversized files
Polyglot files
Malformed documents
Content-type spoofing

The upload pipeline should validate both:

declared MIME type;
actual file characteristics.
29. CV Access

A CV should not be publicly accessible simply because someone knows its URL.

Preferred architecture:

Candidate
    │
    ▼
Backend authorization
    │
    ▼
Signed temporary URL
    │
    ▼
Object Storage

Signed URLs should have short expiration times.

30. AI Security

AI integration creates a separate security boundary.

The architecture is:

Backend
   │
   ▼
AI Provider

not:

Browser
   │
   ▼
AI Provider

AI API keys remain server-side.

31. Prompt Injection

CVs and job descriptions are untrusted input.

A CV could theoretically contain text such as:

Ignore all previous instructions...

The AI processing system must treat document content as data, not as trusted instructions.

AI prompts should clearly separate:

System instructions
+
Untrusted document content
32. AI Data Minimization

Only information necessary for the AI operation should be sent to an AI provider.

For example, when extracting skills from a CV, the platform should avoid sending unrelated data if it is not required.

The exact data-processing policy depends on the selected AI provider and production privacy requirements.

33. AI Output Validation

AI output must never automatically become trusted application state.

Pipeline:

AI
 ↓
Structured output
 ↓
Schema validation
 ↓
Business validation
 ↓
Database

Example:

AI says:
"React proficiency = 17"

Backend:
REJECT

because the permitted range may be:

1–5
34. AI Hallucination Protection

The matching engine must not rely blindly on AI-generated facts.

For example, if the candidate's CV does not demonstrate Kubernetes experience, the AI must not invent it.

Important matching decisions should be based primarily on structured domain data.

AI may help interpret information, but should not silently manufacture candidate qualifications.

35. Logging

The backend should use structured logging.

Logs may include:

timestamp
requestId
userId where appropriate
endpoint
HTTP status
duration
error code

Logs must not contain:

password
passwordHash
session tokens
API keys
full CV contents
unnecessary personal information
36. Request Correlation

Requests should eventually have a request/correlation ID.

Example:

X-Request-ID

This makes debugging easier:

Frontend
   │ requestId=abc123
   ▼
Backend
   │
   ├── Database
   └── AI

All related logs can then be associated with the same request.

37. Audit Logging

Security-sensitive actions should eventually be auditable.

Potential events:

LOGIN
LOGOUT
PASSWORD_CHANGED
PROFILE_UPDATED
JOB_CREATED
JOB_PUBLISHED
CANDIDATE_VIEWED
CV_ACCESSED
MATCH_VIEWED
ADMIN_ACTION

A dedicated audit_logs table may be introduced.

For MVP, only the most important security events need to be implemented.

38. Personal Data in Logs

The logging system should follow data minimization.

Avoid:

email=...
phone=...
full CV=...
salary=...

unless genuinely required for troubleshooting.

Prefer:

userId=UUID
event=LOGIN_FAILED
39. Encryption in Transit

Production communication must use HTTPS.

Architecture:

Browser
   │
 HTTPS
   ▼
Frontend
   │
 HTTPS
   ▼
Backend
   │
 encrypted connection where supported
   ▼
Database
40. Encryption at Rest

Managed infrastructure should provide encryption at rest where available.

This should apply to:

database;
object storage;
backups.

The final infrastructure provider must be evaluated before production launch.

41. Backups

Production data must have backups.

Backup strategy should include:

automated database backups;
retention policy;
recovery testing;
disaster recovery procedure.

A backup that has never been restored is not considered a fully tested backup strategy.

42. Disaster Recovery

The platform should eventually define:

RPO — Recovery Point Objective
RTO — Recovery Time Objective

These values can remain relatively modest for the MVP.

Example future target:

RPO: 24 hours
RTO: 4 hours

These are placeholders, not final commitments.

43. Data Deletion

The platform must eventually support appropriate deletion workflows.

For example:

Candidate requests account deletion
        │
        ▼
Identify associated personal data
        │
        ▼
Delete/anonymize according to policy
        │
        ▼
Remove files
        │
        ▼
Invalidate sessions

The exact workflow must comply with applicable legal requirements.

44. Data Export

The platform should eventually support candidate data export where required.

Potential export:

Profile
Skills
Experience
Preferences
Documents metadata

Actual legal requirements should be validated before production launch.

45. Privacy by Design

Privacy should be considered during feature design rather than added afterward.

Every new feature should ask:

What personal data does this feature require?

Who can see it?

Why do we need it?

How long should we retain it?

Can we reduce the amount of data?
46. Security Headers

The production frontend/backend infrastructure should use appropriate security headers.

Potential headers include:

Content-Security-Policy
X-Content-Type-Options
Referrer-Policy
Strict-Transport-Security
Permissions-Policy

Exact configuration should be tested against application functionality.

47. Content Security Policy

A Content Security Policy should be introduced once the frontend architecture is stable.

The policy must account for:

Next.js;
authentication;
API calls;
analytics if introduced;
AI-related functionality if applicable.

Avoid unnecessarily broad policies such as unrestricted:

script-src *
48. Frontend Security

The frontend must:

avoid storing secrets;
avoid trusting client-side authorization;
safely render user content;
validate forms for usability;
rely on backend validation for security;
avoid exposing private API responses;
handle authentication state securely.

Client-side validation improves UX but is not a security boundary.

49. Backend Security Layers

The NestJS backend should use multiple security layers:

Request
   │
   ▼
Rate Limit
   │
   ▼
Authentication
   │
   ▼
Authorization
   │
   ▼
Validation
   │
   ▼
Business Logic
   │
   ▼
Database / External Services
50. Security Testing

Security testing will eventually include:

Automated
dependency scanning;
authentication tests;
authorization tests;
input validation tests;
API tests;
upload validation tests.
Manual
authorization bypass attempts;
IDOR testing;
authentication testing;
file upload testing;
API abuse testing.
51. OWASP Alignment

The backend should use the OWASP Top 10 as a baseline security reference.

Particular attention should be given to:

Broken Access Control;
Cryptographic Failures;
Injection;
Insecure Design;
Security Misconfiguration;
Vulnerable Components;
Identification and Authentication Failures;
Logging and Monitoring Failures.

The platform does not claim formal OWASP certification.

52. Security Definition of Done

A security-sensitive feature is considered complete only when:

authentication requirements are defined;
authorization requirements are defined;
input validation exists;
sensitive data exposure has been considered;
error handling is safe;
logs do not expose secrets;
automated security-relevant tests exist;
dependencies have been checked;
documentation has been updated.
53. MVP Security Priorities

For MVP, priority should be:

P0 — Mandatory
Secure authentication
Password hashing
Authorization
Company isolation
Candidate privacy
Input validation
Secrets management
HTTPS
Database security
Basic rate limiting
CV upload protection
AI key protection
P1 — Important
Audit logging
Request IDs
Security headers
Dependency scanning
Automated security tests
Backup verification
P2 — Later
Advanced anomaly detection
Enterprise SSO
2FA/MFA
Advanced SIEM
Dedicated security monitoring
Formal penetration testing
54. Security Architecture Summary

The security model can be summarized as:

                 UNTRUSTED INTERNET
                        │
                        ▼
                  ┌───────────┐
                  │ Frontend  │
                  └─────┬─────┘
                        │
                      HTTPS
                        │
                        ▼
               ┌─────────────────┐
               │ Authentication  │
               └────────┬────────┘
                        ▼
               ┌─────────────────┐
               │ Authorization   │
               └────────┬────────┘
                        ▼
               ┌─────────────────┐
               │ Input Validation│
               └────────┬────────┘
                        ▼
               ┌─────────────────┐
               │ Business Logic  │
               └──────┬─────┬────┘
                      │     │
                      ▼     ▼
                 PostgreSQL AI

The central rule is:

Never trust the client. Verify everything at the backend boundary.

55. Status

Security Architecture:

v0.1.0 — Complete for MVP planning

The architecture is intentionally designed so that stronger security controls can be added without redesigning the entire platform.

Documentenstatus

We hebben nu vier belangrijke architectuurdocumenten:

it-talent-docs/
│
├── architecture/
│   ├── architecture.md      ✅
│   ├── database.md          ✅
│   ├── api.md               ✅
│   └── security.md          ✅
│
├── product/
│   ├── vision.md            ⏭️
│   ├── requirements.md      ⏭️
│   └── roadmap.md           ⏭️
│
└── decisions/
    └── README.md            ⏭️