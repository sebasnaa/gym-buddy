# LOVABLE.md

# GymBuddy Development Instructions

This document defines how Lovable must work on this project.

It is not the product specification.

The product specification is contained in the documentation under `/docs`.

---

# Mission

Build GymBuddy following the project documentation exactly.

Do not invent features.

Do not simplify requirements.

Do not replace documented decisions with assumptions.

When documentation conflicts with implementation, the documentation is the source of truth.

---

# Documentation Order

Before making ANY change, read these documents in this exact order.

1. docs/product.md
2. docs/architecture.md
3. docs/data-model.md
4. docs/api.md
5. docs/security.md
6. docs/ux.md
7. docs/analytics.md

Do not skip documents.

---

# Source of Truth

The documentation is the authoritative definition of the project.

Never contradict it.

If documentation appears inconsistent:

* stop
* explain the inconsistency
* propose possible solutions
* wait for approval

Do not silently choose one interpretation.

---

# Technology Stack

Frontend

* React
* TypeScript
* TailwindCSS

Backend

* Node.js
* TypeScript
* REST API

Database

* PostgreSQL

ORM

* Prisma

Authentication

* Server-managed secure sessions

Analytics

* PostgreSQL-backed events
* Follow docs/analytics.md

---

# Technologies That Must NOT Be Used

Never introduce:

* Supabase
* Firebase
* MongoDB
* GraphQL
* NextAuth
* Clerk
* Auth0
* LocalStorage authentication
* Direct database access from the frontend

Only introduce additional technologies after explicit approval.

---

# General Development Principles

Always prefer:

* simplicity
* readability
* maintainability
* modularity
* explicit code
* strong typing
* reusable components

Avoid:

* duplicated logic
* large components
* hidden side effects
* unnecessary abstractions
* premature optimization

---

# Architecture Rules

Follow docs/architecture.md.

Specifically:

* UI contains no business logic.
* Controllers contain no business rules.
* Services implement business logic.
* Repositories own database access.
* Prisma is the only database access layer.
* React never communicates directly with PostgreSQL.

---

# Database Rules

Follow docs/data-model.md.

Never:

* modify schema without updating migrations
* create undocumented tables
* remove constraints
* replace UUIDs with numeric IDs

Always:

* create migrations
* use foreign keys
* create indexes when documented
* preserve data integrity

---

# API Rules

Follow docs/api.md.

Never:

* invent endpoints
* change response contracts
* rename fields
* expose undocumented information

All endpoints must:

* validate input
* check authorization
* return documented responses
* return documented error codes

---

# Security Rules

Follow docs/security.md.

Never:

* expose passwords
* expose contact information
* expose tokens
* expose internal errors
* trust client-side authorization
* bypass validation

Always:

* validate on the server
* use secure sessions
* hash passwords
* protect private data
* respect authorization rules

---

# UX Rules

Follow docs/ux.md.

Do not redesign the product.

Maintain:

* documented navigation
* documented screens
* documented flows
* documented terminology

Do not transform GymBuddy into:

* a dating app
* a social network
* a chat application

---

# Analytics Rules

Follow docs/analytics.md.

Critical business events must originate from the backend.

Do not:

* log sensitive data
* invent event names
* modify event schemas

---

# Code Quality

Prefer:

* small files
* reusable hooks
* reusable components
* typed APIs
* explicit interfaces

Avoid:

* any
* duplicated types
* magic strings
* deeply nested conditionals

---

# Naming Conventions

Use English for:

* variables
* functions
* classes
* database tables
* API routes
* enums

Use Spanish only for user-facing text.

---

# Comments

Do not write unnecessary comments.

Write comments only when they explain:

* business rules
* non-obvious decisions
* security constraints

Do not comment obvious code.

---

# Error Handling

Never ignore errors.

Handle errors explicitly.

Use documented API responses.

Do not leak internal implementation details.

---

# Performance

Prefer:

* pagination
* lazy loading where appropriate
* memoization only when justified
* optimized database queries

Do not optimize prematurely.

Correctness is more important than micro-optimizations.

---

# Accessibility

Follow docs/ux.md.

Every UI must include:

* labels
* keyboard support
* visible focus
* accessible buttons
* sufficient contrast

---

# Responsive Design

Mobile first.

Desktop compatible.

Do not implement desktop-only layouts.

---

# Testing

Each implemented feature should include tests whenever practical.

Priority:

1. Business logic
2. API
3. Security
4. Critical UI flows

---

# Git Discipline

Keep changes focused.

Avoid unrelated modifications.

Do not reformat unrelated files.

Do not rename files without justification.

---

# Before Writing Code

For every implementation request:

1. Read the relevant documentation.
2. Explain the implementation plan.
3. List assumptions.
4. Identify missing requirements.
5. Wait for approval if requirements are ambiguous.

Do not immediately generate code for large features.

---

# Implementation Order

Build the application incrementally.

Recommended order:

1. Project setup
2. Prisma schema
3. PostgreSQL migrations
4. Authentication
5. User profile
6. Gyms
7. Availability
8. Matching
9. Match requests
10. Connections
11. Workout feedback
12. Administration
13. Analytics
14. Final polish

Each phase must leave the application in a working state.

---

# Modification Rules

When changing existing code:

* preserve documented behavior
* preserve API compatibility unless documentation changes
* avoid unnecessary refactoring
* explain breaking changes before implementing them

---

# Forbidden Actions

Never:

* invent requirements
* remove documented functionality
* ignore documentation
* replace PostgreSQL
* replace Prisma
* expose private contact information
* bypass authentication
* bypass authorization
* introduce undocumented dependencies
* silently change business rules

---

# When Requirements Are Missing

If documentation is incomplete:

1. Stop.
2. Explain exactly what is missing.
3. Suggest alternatives.
4. Wait for approval.

Do not guess.

---

# Definition of Done

A task is complete only when:

* It follows all documentation.
* It compiles successfully.
* Types are correct.
* Existing functionality is preserved.
* Security rules are respected.
* API contracts are maintained.
* Database integrity is preserved.
* UI matches the UX specification.
* No undocumented functionality has been introduced.

---

# Final Principle

The documentation defines the product.

The implementation must adapt to the documentation.

Never adapt the documentation to justify the implementation.
