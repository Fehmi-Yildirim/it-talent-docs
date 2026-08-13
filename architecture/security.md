# IT Talent Platform — Security Architecture

Document: security.md
Version: 0.2.0
Status: Accepted
Last updated: 2026-08-13

## 1. Purpose

This document defines the security architecture for the IT Talent Platform.

The platform processes sensitive professional and personal information. Security and privacy are therefore architectural requirements from the beginning.

## 2. Security Objectives

The platform must protect:

- Confidentiality
- Integrity
- Availability
- Privacy
- Account security
- Traceability

## 3. Security Architecture

```text
                    Internet
                       │
                     HTTPS
                       │
                       ▼
              ┌─────────────────┐
              │ React + Vite     │
              │ Frontend         │
              └────────┬────────┘
                       │
                     HTTPS
                       │
                       ▼
              ┌─────────────────┐
              │ NestJS Backend   │
              │                 │
              │ Authentication  │
              │ Authorization   │
              │ Validation      │
              │ Business Logic  │
              │ Matching        │
              │ AI Orchestration│
              └───────┬───┬─────┘
                      │   │
                      ▼   ▼
               PostgreSQL AI Provider