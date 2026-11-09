# ADR-001 — Multi-Repository Architecture

**Status:** Accepted  
**Date:** 2026-08-12  
**Updated:** 2026-11-09

## Context

The IT Talent Platform consists of three separate parts:

- Frontend application
- Backend and REST API
- Project documentation

Each part has its own responsibilities and can be developed, maintained, and versioned independently.

The project therefore needs a repository structure that separates these concerns while keeping them coordinated as parts of one platform.

## Decision

The IT Talent Platform uses three separate GitHub repositories:

- `it-talent-frontend`
- `it-talent-backend`
- `it-talent-docs`

All three repositories belong to the same IT Talent Platform but are maintained and versioned independently.

The frontend communicates with the backend through the REST API. The documentation repository contains the platform's technical documentation, architecture decisions, and product documentation.

## Rationale

The multi-repository architecture provides a clear separation of responsibilities:

- Frontend development and user interface.
- Backend development, business logic, and API functionality.
- Technical and product documentation.

This separation allows each repository to maintain its own source structure, dependencies, configuration, and development workflow.

It also makes repository ownership and changes easier to manage independently.

## Consequences

### Positive

- Frontend and backend code remain clearly separated.
- Documentation is maintained independently from application code.
- Each repository can be versioned and released independently.
- Each application maintains its own dependencies and configuration.
- The REST API provides a defined communication boundary between frontend and backend.
- Changes within one repository do not automatically require changes in the others.

### Negative

Changes affecting multiple parts of the platform may require coordinated updates across multiple repositories.

For example, an API contract change may require corresponding changes in:

- `it-talent-backend`
- `it-talent-frontend`
- `it-talent-docs`

The API documentation must remain aligned with the implemented API contract.

Independent versioning also means that compatibility between frontend and backend versions must be considered when making changes to the API.

## Rejected Alternative

A monorepo containing the frontend, backend, and documentation was considered but not selected.

Separate repositories were chosen to maintain clear boundaries between frontend development, backend development, and documentation.

## Result

The IT Talent Platform is maintained through three coordinated but independently versioned repositories:

```text
IT Talent Platform
│
├── it-talent-frontend
├── it-talent-backend
└── it-talent-docs
```

The repositories remain separate while contributing to the same platform. Changes affecting shared functionality, particularly the REST API contract, must be coordinated across the relevant repositories.