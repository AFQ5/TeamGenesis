# Project Type Profiles

This file defines which agent roles to assign based on the project type. Claude reads this file to select the right team composition.

---

## Core Roles (Always Included)

These three roles are present in every generated prompt regardless of project type.

### Architect
- **Purpose:** System design, schemas, interface contracts, and architectural consistency.
- **Typical scope:** Data models, API surface design, system boundaries, technology decisions.
- **Must NOT:** Write implementation code. Approve plans without checking security invariants.
- **DoD template:** Schema files created with examples. Interface contracts documented between all agents. Architectural risks identified and logged in CLAUDE.md.

### Implementer
- **Purpose:** Write production code per the approved plan.
- **Typical scope:** Source files, business logic, data access layer, external integrations.
- **Must NOT:** Change architecture without Architect approval. Add dependencies without RFC. Skip tests.
- **DoD template:** All planned files created/updated. Unit tests written and passing. Edge cases handled or documented. No TODO comments without linked issues.

### Critic
- **Purpose:** Review plans (not code) for completeness, security, testability, and edge case coverage.
- **Typical scope:** Plan documents, schemas, data flows, test strategies.
- **Must NOT:** Write implementation code. Approve plans that violate security invariants. Rubber-stamp approvals.
- **DoD template:** Plan approved with pass/fail per section. Test strategy validated. Known risks escalated. Rejection feedback is specific and actionable.

---

## Project-Type-Specific Roles

Select additional roles based on the project type. If the user specifies custom roles, use those instead (or in addition) — but apply the same governance structure (DoD, stop conditions, boundaries) to every custom role.

### web-app

**Additional roles:** Frontend Specialist, DevOps

**Frontend Specialist:**
- **Purpose:** Build UI components, manage client-side state, handle routing and user interactions.
- **Typical scope:** Components, pages, styles, client-side routing, state management.
- **Must NOT:** Modify backend API contracts without Architect approval. Bypass authentication flows. Hardcode API URLs.
- **DoD template:** All UI components render correctly. Responsive behavior verified. Accessibility basics met (semantic HTML, ARIA labels). Client-side validation matches API schemas.

**DevOps:**
- **Purpose:** CI/CD pipelines, deployment configuration, environment setup, infrastructure as code.
- **Typical scope:** Dockerfiles, CI configs, deployment scripts, environment variables, monitoring.
- **Must NOT:** Modify application business logic. Change database schemas. Store secrets in config files.
- **DoD template:** CI pipeline passes. Deployment config tested in staging. Environment variables documented. Rollback procedure documented.

---

### api-service

**Additional roles:** API Designer, DevOps

**API Designer:**
- **Purpose:** Define API endpoints, request/response schemas, error formats, versioning strategy.
- **Typical scope:** OpenAPI/Swagger specs, route definitions, validation schemas, error handling standards.
- **Must NOT:** Write business logic implementation. Change database schemas without Architect approval.
- **DoD template:** API spec complete with all endpoints documented. Request/response examples for each endpoint. Error codes and messages defined. Versioning strategy documented.

**DevOps:** (Same as web-app profile above.)

---

### cli-tool

**Additional role:** CLI/DX Specialist

**CLI/DX Specialist:**
- **Purpose:** Build CLI commands, argument parsing, help text, output formatting, and developer experience.
- **Typical scope:** Command definitions, argument/flag parsing, interactive prompts, output formatters (JSON, table, plain text).
- **Must NOT:** Modify core business logic. Bypass sandboxed write paths. Add interactive prompts without fallback for non-interactive mode.
- **DoD template:** All commands work as specified. Help text complete and accurate. Output formats consistent. Non-interactive mode supported (for scripting). Integration tests pass for primary flows.

---

### library

**Additional roles:** API Designer, DX/Documentation Specialist

**API Designer:**
- **Purpose:** Define the public API surface, type signatures, method contracts, and extension points.
- **Typical scope:** Public interfaces, type definitions, method signatures, configuration options.
- **Must NOT:** Expose internal implementation details. Break backward compatibility without RFC. Add required dependencies without approval.
- **DoD template:** Public API fully typed. Method signatures documented with JSDoc/docstrings. Breaking changes flagged and documented. Configuration options have sensible defaults.

**DX/Documentation Specialist:**
- **Purpose:** Write usage examples, API reference docs, migration guides, and getting-started content.
- **Typical scope:** README, API docs, example code, changelog, migration guides.
- **Must NOT:** Modify library source code. Change public API without API Designer approval.
- **DoD template:** README includes quick-start example. All public methods documented. At least 2 usage examples per major feature. Changelog updated.

---

### data-pipeline

**Additional roles:** Data Engineer, DevOps

**Data Engineer:**
- **Purpose:** Design data flows, transformations, validation rules, and storage schemas.
- **Typical scope:** ETL/ELT logic, data validation, schema definitions, pipeline orchestration, data quality checks.
- **Must NOT:** Modify API endpoints. Change authentication logic. Skip data validation steps.
- **DoD template:** Data schemas defined with examples. Transformation logic tested with edge cases (nulls, empty sets, malformed input). Pipeline idempotent (re-runnable without side effects). Data quality checks in place.

**DevOps:** (Same as web-app profile above.)

---

## Handling User-Defined Custom Roles

When the user provides custom role names:

1. **Use the custom roles** instead of (or in addition to) the profile defaults above.
2. **Apply the governance structure** to each custom role. Every custom role must have:
   - Purpose (one-line)
   - Scope (what files/modules they own)
   - Responsibilities (3-5 items)
   - "Must NOT" boundaries (2-3 items)
   - Definition of Done checklist (3-5 measurable items)
   - Stop Conditions (2-3 specific situations where they halt and escalate)
3. **Ask clarifying questions** if a custom role name is ambiguous (e.g., "Helper" — helper for what?).
4. **Always include the three core roles** (Architect, Implementer, Critic) even when custom roles are provided, unless the user explicitly says to replace them.

---

## Interface Contracts by Project Type

Define these default interface contracts based on project type. Add more as needed.

### web-app / api-service
- Architect -> Implementer: Data schemas (JSON/TypeScript types with examples)
- Implementer -> Critic: Plan document (markdown with steps, files, schemas, edge cases)
- Critic -> Implementer: Approval (pass/fail + specific feedback per section)
- API Designer -> Frontend Specialist: API spec (OpenAPI or typed endpoint definitions)

### cli-tool
- Architect -> Implementer: Command spec (name, flags, args, output format)
- Implementer -> CLI/DX Specialist: Core functions (typed interfaces for CLI to call)
- CLI/DX Specialist -> Critic: Integration test results + help text review

### library
- API Designer -> Implementer: Public API surface (type signatures + method contracts)
- Implementer -> DX/Documentation: Implementation notes (what changed, what to document)
- DX/Documentation -> Critic: Documentation review (completeness, accuracy, examples)

### data-pipeline
- Architect -> Data Engineer: Pipeline spec (sources, transformations, destinations, schedules)
- Data Engineer -> Implementer: Data schemas + transformation rules
- Implementer -> Critic: Plan with data flow diagrams + edge case analysis
