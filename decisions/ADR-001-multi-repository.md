# ADR-001 — Multi-Repository Architecture

**Status:** Accepted  
**Date:** 2026-08-12

## Context

The IT Talent Platform consists of three clearly separated concerns:

- frontend application;
- backend/API application;
- project documentation.

The project is intended to be deployed and developed independently.

## Decision

We use three separate GitHub repositories:

- `it-talent-frontend`
- `it-talent-backend`
- `it-talent-docs`

The repositories are related to the same product but remain independently versioned.

## Rationale

This separation provides:

- independent deployments;
- clear ownership boundaries;
- smaller repositories;
- independent CI/CD pipelines;
- easier frontend/backend scaling;
- reduced coupling;
- clean separation between application code and documentation.

## Consequences

### Positive

Frontend and backend can evolve independently.

Documentation does not need to be included in application deployment artifacts.

Each repository can have its own:

- CI pipeline;
- deployment configuration;
- release process;
- permissions.

### Negative

Cross-repository changes require coordination.

For example, an API change may require changes in both:

`it-talent-backend`

and:

`it-talent-frontend`

This will be managed through API contracts and versioning.

## Rejected Alternative

A single monorepo containing:

```text
frontend/
backend/
docs/