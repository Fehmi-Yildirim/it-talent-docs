# IT Talent Platform — Security Architecture

Document: security.md
Version: 0.3.0
Status: Accepted — MVP Security Baseline
Last updated: 2026-08-18

## 1. Purpose

This document defines the security architecture and security baseline
for the IT Talent Platform.

The platform processes personal, professional and potentially sensitive
recruitment information.

Security and privacy are therefore architectural requirements from the
beginning of development.

This document describes:

- implemented security controls;
- required security boundaries;
- authentication;
- authorization;
- validation;
- API protection;
- database protection;
- secret management;
- frontend/backend security boundaries;
- future security requirements.

The document distinguishes between controls that are currently
implemented and controls that are required but not yet implemented.

---

## 2. Security Objectives

The platform must protect:

- Confidentiality
- Integrity
- Availability
- Privacy
- Account security
- Authorization boundaries
- API security
- Data integrity
- Traceability where required

The MVP security approach follows the principle:

> Security must be enforced by the backend and must never depend
> exclusively on frontend restrictions.

---

## 3. Current Security Architecture

The current implementation consists of:

```text
                    Internet
                       │
                     HTTPS
                       │
                       ▼
              ┌─────────────────┐
              │ React + Vite    │
              │ Frontend        │
              └────────┬────────┘
                       │
                       │ HTTP/HTTPS
                       ▼
              ┌─────────────────┐
              │ NestJS Backend  │
              │                 │
              │ Authentication  │
              │ Authorization   │
              │ Validation      │
              │ Business Logic  │
              │                 │
              └───────┬───┬─────┘
                      │   │
                      ▼   ▼
               PostgreSQL  External
                           Services

The frontend does not access PostgreSQL directly.

The backend is the security boundary between the frontend and
the database.

4. Frontend Security Boundary

The current frontend uses:

React
Vite
TypeScript

The frontend is responsible for:

presentation;
navigation;
user interaction;
client-side state;
form handling;
API communication.

The frontend must not be treated as a trusted security boundary.

Client-side validation may improve user experience, but all security
validation must also be performed by the backend.

The frontend must never determine:

user role;
resource ownership;
company ownership;
candidate ownership;
permissions;
authorization decisions.
5. Backend Security Boundary

The backend uses NestJS.

The backend is responsible for:

authentication;
authorization;
input validation;
business rules;
database access;
resource ownership;
security-sensitive operations;
AI orchestration;
matching authorization.

The current backend already configures:

global API prefix;
CORS;
global validation;
whitelist validation;
transformation;
rejection of unexpected properties.
6. API Prefix

The backend currently exposes the API under:

/api/v1

This is configured through the NestJS global prefix.

Example:

GET /api/v1/health

API versioning therefore forms part of the security and compatibility
boundary.

Breaking API changes should use a new API version.

7. Authentication

Authentication is handled by the backend.

The current backend includes:

NestJS JWT support;
Passport;
Passport JWT;
Argon2 password hashing.

Passwords must never be stored in plaintext.

The database stores a password hash:

User
 └── passwordHash

The authentication system must never return password hashes through
API responses.

Authentication decisions must be performed by the backend.

8. Password Security

Passwords are hashed using Argon2.

The backend must:

never store plaintext passwords;
never log plaintext passwords;
never return password hashes to clients;
validate password input;
use secure password hashing parameters;
protect authentication endpoints against abuse.

Password reset functionality will be defined when implemented.

9. Roles

The current user model defines:

CANDIDATE
RECRUITER
ADMIN

Role information is stored in the backend database.

Roles must never be trusted when supplied by the client.

For example, the backend must not accept:

{
  "role": "ADMIN"
}

as an authorization decision.

The authenticated backend context determines the effective role.

10. Authorization

Authentication answers:

Who are you?

Authorization answers:

What are you allowed to access?

Authorization must be enforced by the backend.

Expected ownership boundaries include:

Candidate
 └── own profile
 └── own skills
 └── own documents


Recruiter
 └── company resources
 └── company jobs
 └── permitted candidate information


Admin
 └── administrative resources

Frontend visibility restrictions are not sufficient authorization.

11. Input Validation

The backend currently uses NestJS ValidationPipe.

The global configuration includes:

whitelist: true
transform: true
forbidNonWhitelisted: true

This means incoming DTO data is validated and unexpected properties
are rejected.

Validation must occur before business logic is executed.

All future endpoints should use explicit DTOs.

Examples:

RegisterDto
LoginDto
CreateJobDto
UpdateCandidateDto
CreateSkillDto
CreateJobRequirementDto
12. CORS

The backend currently enables CORS.

The allowed origin is configured through:

CORS_ORIGIN

with a local development fallback.

Credentials are currently enabled.

Production environments must use an explicit trusted frontend origin.

Wildcard origins must not be used together with credentialed requests.

13. Database Security

PostgreSQL is the primary database.

Prisma is used as the database access layer.

The backend communicates with PostgreSQL.

The frontend must never connect directly to PostgreSQL.

Database credentials must remain server-side.

They must never be:

committed to Git;
exposed through frontend environment variables;
returned through API responses;
logged.

14. Database Integrity

The database uses UUID primary keys.

Core relationships are enforced through foreign keys.

Examples include:

CandidateSkill
 ├── candidateId → Candidate
 └── skillId     → Skill


JobRequirement
 ├── jobId       → Job
 └── skillId     → Skill


Match
 ├── candidateId → Candidate
 └── jobId       → Job

Unique constraints prevent duplicate relationships where required.

This provides an additional layer of data integrity.

15. Sensitive Data

Potentially sensitive information includes:

email addresses;
candidate profiles;
employment information;
salary information;
location;
CVs;
professional skills;
recruitment activity.

API responses must expose only information that the authenticated
user is authorized to receive.

Database entities must not automatically become API response objects.

DTOs or response models should be used.

16. API Response Security

The backend must avoid returning internal database fields.

Especially:

passwordHash
internal authorization metadata
private candidate information
internal security information

The API should use dedicated response DTOs.

Conceptually:

Prisma Entity
      ↓
Response DTO
      ↓
JSON Response
17. Authentication Data Transport

The exact production token transport strategy remains an implementation
decision.

The architecture must ensure that authentication credentials are
protected against unnecessary exposure to browser-side JavaScript.

The preferred production approach should minimize token exposure and
protect against:

token theft;
XSS;
CSRF where applicable;
session fixation;
unauthorized reuse.

The final mechanism must be documented when authentication is finalized.

18. Rate Limiting

Rate limiting is a required security control.

Particularly sensitive endpoints include:

POST /api/v1/auth/login
POST /api/v1/auth/register
POST /api/v1/jobs/:id/match
POST /api/v1/candidates/me/analyze-cv
POST /api/v1/jobs/:id/analyze

Rate limiting is part of the security baseline but is not yet considered
complete until explicitly implemented and tested.

19. File Upload Security

CV uploads are not yet part of the currently implemented database model.

When implemented, uploaded files must be treated as untrusted input.

The backend must validate:

file size;
MIME type;
file extension;
upload authorization;
storage location;
access permissions.

Files should not be stored directly in PostgreSQL.

The intended architecture is:

Frontend
   │
   ▼
Backend
   │
   ▼
Object Storage
   │
   ▼
CV Metadata
20. AI Security Boundary

The frontend must never communicate directly with an AI provider.

The intended architecture is:

Frontend
   │
   ▼
NestJS Backend
   │
   ▼
AI Service
   │
   ▼
Structured Result
   │
   ▼
Backend Validation
   │
   ▼
Database

AI API credentials must remain exclusively on the backend.

AI-generated information must be validated before being persisted.

21. AI Data Integrity

AI output is not automatically trusted as authoritative data.

For example:

AI_EXTRACTED
confidence = 0.91

is different from:

VERIFIED

AI must not silently overwrite verified information without an
explicit business rule.

22. Logging and Secrets

Secrets must never be written to application logs.

This includes:

passwords;
JWT secrets;
database credentials;
AI API keys;
authentication tokens;
private file URLs.

Environment variables or the deployment platform's secret-management
system must be used for secrets.

23. Environment Security

Environment-specific configuration must be separated.

Local development may use:

.env
.env.local

These files must not be committed to Git.

Repositories should provide:

.env.example

containing variable names but no real credentials.

24. HTTPS

Production API traffic must use HTTPS.

Plain HTTP may be used only for controlled local development.

Production deployments must not expose authentication credentials
over unencrypted HTTP.

25. Security Headers

Production deployment should include appropriate HTTP security headers.

At minimum, the production configuration should evaluate:

Content-Security-Policy;
X-Content-Type-Options;
Referrer-Policy;
frame protection;
Strict-Transport-Security.

The exact implementation depends on the final deployment architecture.

26. Error Handling

Security-sensitive errors should not expose internal implementation
details.

The API should return predictable error responses without exposing:

stack traces;
database queries;
internal file paths;
secret configuration;
authentication internals.

Development environments may provide additional diagnostics.

Production responses must remain appropriately generic.

27. Resource Ownership

The backend must derive ownership from authenticated context wherever
possible.

The client must not be trusted to determine:

userId
candidateId
companyId
recruiterId
role
permissions

For example, updating:

PATCH /api/v1/candidates/me

should resolve the candidate through the authenticated user rather than
trusting a client-provided userId.

28. GDPR / Privacy Security

Security controls must support GDPR principles.

The system should support:

data minimization;
controlled access;
deletion;
export where required;
appropriate retention;
secure storage;
access control;
auditability.

The exact legal implementation is outside the technical security
architecture and must be validated before production launch.

29. Current Implementation Status
Implemented

The current backend already contains:

NestJS backend;
JWT/Passport dependencies;
Argon2 password hashing;
PostgreSQL/Prisma;
UUID identifiers;
role model;
account status model;
global API prefix /api/v1;
CORS configuration;
global DTO validation;
whitelist validation;
rejection of non-whitelisted properties;
database relations and constraints.
Partially implemented / requires verification
complete authorization model;
production token transport;
rate limiting;
security headers;
production secret management;
audit logging;
file upload security;
AI security controls;
complete resource ownership enforcement.
Not yet part of the current implementation
CV object storage;
AI processing pipeline;
matching security boundaries;
advanced audit logging;
production-grade security monitoring.

These should not be documented as already implemented.

30. Security Definition of Done

A security-sensitive feature is considered complete when:

authentication requirements are defined;
authorization requirements are defined;
ownership rules are defined;
input validation exists;
sensitive fields are protected;
error handling is safe;
secrets are not exposed;
automated tests exist;
relevant security behavior is documented.
31. Security Development Principle

The project follows:

Secure by default, validate on the backend, trust as little as possible.

Security must not be postponed until the production deployment phase.

Security controls should be introduced together with the functionality
they protect.

32. Relationship with Other Architecture Documents

This document must remain consistent with:

architecture.md
database.md
api.md

If implementation differs from the documented security architecture,
the documentation must be updated.

Significant architectural security decisions should receive an ADR.

33. Security Status

Security Architecture:

ACCEPTED — MVP BASELINE

This document describes the security baseline for the currently
implemented architecture.

It does not claim that all production security controls have already
been implemented.