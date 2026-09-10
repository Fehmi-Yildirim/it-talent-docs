# ADR-001 — Multi-Repository Architecture

**Status:** Accepted
**Date:** 2026-08-12
**Updated:** 2026-09-10

## Context

The IT Talent Platform consists of three separate concerns:

* frontend application;
* backend/API application;
* project documentation.

These concerns have different responsibilities and can be developed and versioned independently.

## Decision

The IT Talent Platform uses three separate GitHub repositories:

* `it-talent-frontend`
* `it-talent-backend`
* `it-talent-docs`

All three repositories belong to the same IT Talent Platform but remain independently versioned.

The frontend and backend communicate through the REST API. The documentation repository contains the technical and product documentation for the platform.

## Rationale

The multi-repository structure provides a clear separation between:

* frontend development;
* backend and API development;
* documentation.

Each repository can therefore maintain its own source structure, dependencies, configuration, and development workflow.

## Consequences

### Positive

* Frontend and backend code remain clearly separated.
* Documentation is maintained independently from application code.
* Changes can be versioned per repository.
* Each application has its own dependency and configuration structure.
* API communication provides a clear boundary between frontend and backend.

### Negative

Changes that affect both frontend and backend may require updates in multiple repositories.

For example, an API contract change can require corresponding changes in both:

```text
it-talent-backend
it-talent-frontend
```

The API documentation in `it-talent-docs` should remain aligned with the implemented API.

## Rejected Alternative

A single repository containing frontend, backend, and documentation was not selected.

The project uses separate repositories to maintain clear boundaries between application code and documentation, and between frontend and backend development.

## Result

The IT Talent Platform is maintained as three coordinated but independently versioned repositories:

```text
IT Talent Platform
│
├── it-talent-frontend
├── it-talent-backend
└── it-talent-docs
```
