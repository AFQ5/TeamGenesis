# Role Profiles Reference

This file defines the **core roles** (always included) and provides **reference examples** showing how specialist roles are structured for different project domains. Claude reads these examples to understand the expected depth and governance rigor, then dynamically generates specialist roles appropriate to the user's specific project.

**These examples are not an exhaustive list.** Any project domain can be supported — Claude determines the right specialist roles based on the user's project description, tech stack, and goals, applying the same governance structure demonstrated below.

---

## Core Roles (Always Included)

These three roles are present in every generated prompt regardless of project domain.

### Architect
- **Purpose:** System design, schemas, interface contracts, and architectural consistency.
- **Typical scope:** Data models, API surface design, system boundaries, technology decisions.
- **Must NOT:** Write implementation code. Approve plans without checking security invariants.
- **DoD template:** Schema files created with examples. Interface contracts documented between all agents. Architectural risks identified and logged in CLAUDE.md.
- **Common failure modes:**
  - Designs schemas in isolation without checking Implementer's constraints (e.g., ORM limitations)
  - Over-architects with abstractions that no current feature requires
  - Defines interface contracts at the wrong granularity — too abstract for Implementer to code against, or too rigid to accommodate edge cases
  - Silently changes schemas after Implementer has started work, breaking the contract

### Implementer
- **Purpose:** Write production code per the approved plan.
- **Typical scope:** Source files, business logic, data access layer, external integrations.
- **Must NOT:** Change architecture without Architect approval. Add dependencies without RFC. Skip tests.
- **DoD template:** All planned files created/updated. Unit tests written and passing. Edge cases handled or documented. No TODO comments without linked issues.
- **Common failure modes:**
  - Starts coding before the plan is fully approved — "I'll just get a head start"
  - Adds helper utilities or abstractions not in the plan ("while I'm here...")
  - Writes tests that only cover the happy path, skipping edge cases identified in the plan
  - Modifies files owned by another agent without noticing the boundary

### Critic
- **Purpose:** Review plans (not code) for completeness, security, testability, and edge case coverage. Perform periodic drift checks against the Source of Truth.
- **Typical scope:** Plan documents, schemas, data flows, test strategies, spec compliance.
- **Must NOT:** Write implementation code. Approve plans that violate security invariants. Rubber-stamp approvals.
- **DoD template:** Plan approved with pass/fail per section. Test strategy validated. Known risks escalated. Rejection feedback is specific and actionable.
- **Drift detection:** After every 2-3 completed tasks, re-read the Source of Truth and compare against current work. Flag scope creep, spec gaps, and constraint violations.
- **Common failure modes:**
  - Produces vague feedback ("improve error handling") without referencing specific contracts or files
  - Rubber-stamps plans to avoid blocking progress — the most dangerous failure mode
  - Over-indexes on edge cases before core flows are validated
  - Flags issues already handled by governance rules — wastes tokens, dilutes real signals

---

## Generating Specialist Roles

For every project, determine 1-3 additional specialist roles beyond the core three. Use the user's project description, tech stack, and goals to decide which specialists are needed.

**Every specialist role must have the full governance structure:**
- Purpose (one-line)
- Scope (what files/modules they own)
- Responsibilities (3-5 items)
- "Must NOT" boundaries (2-3 items)
- Definition of Done checklist (3-5 measurable items)
- Stop Conditions (2-3 specific situations where they halt and escalate)

**If the user provides custom role names:** Use them, applying the same governance structure. Ask clarifying questions if a role name is ambiguous (e.g., "Helper" — helper for what?). Always include the three core roles unless the user explicitly says to replace them.

The reference examples below show how this governance structure is applied across different project domains. Study them for depth and rigor, then apply the same standard to any domain.

---

## Reference Examples

These examples demonstrate how specialist roles are structured for common project domains. They serve as calibration points — showing the expected level of specificity in purpose, scope, boundaries, and DoD.

### Example: Web Application

**Typical specialist roles:** Frontend Specialist, DevOps

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

### Example: API Service

**Typical specialist roles:** API Designer, DevOps

**API Designer:**
- **Purpose:** Define API endpoints, request/response schemas, error formats, versioning strategy.
- **Typical scope:** OpenAPI/Swagger specs, route definitions, validation schemas, error handling standards.
- **Must NOT:** Write business logic implementation. Change database schemas without Architect approval.
- **DoD template:** API spec complete with all endpoints documented. Request/response examples for each endpoint. Error codes and messages defined. Versioning strategy documented.

**DevOps:** (Same structure as Web Application example above.)

---

### Example: CLI Tool

**Typical specialist role:** CLI/DX Specialist

**CLI/DX Specialist:**
- **Purpose:** Build CLI commands, argument parsing, help text, output formatting, and developer experience.
- **Typical scope:** Command definitions, argument/flag parsing, interactive prompts, output formatters (JSON, table, plain text).
- **Must NOT:** Modify core business logic. Bypass sandboxed write paths. Add interactive prompts without fallback for non-interactive mode.
- **DoD template:** All commands work as specified. Help text complete and accurate. Output formats consistent. Non-interactive mode supported (for scripting). Integration tests pass for primary flows.

---

### Example: Library / SDK

**Typical specialist roles:** API Designer, DX/Documentation Specialist

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

### Example: Data Pipeline

**Typical specialist roles:** Data Engineer, DevOps

**Data Engineer:**
- **Purpose:** Design data flows, transformations, validation rules, and storage schemas.
- **Typical scope:** ETL/ELT logic, data validation, schema definitions, pipeline orchestration, data quality checks.
- **Must NOT:** Modify API endpoints. Change authentication logic. Skip data validation steps.
- **DoD template:** Data schemas defined with examples. Transformation logic tested with edge cases (nulls, empty sets, malformed input). Pipeline idempotent (re-runnable without side effects). Data quality checks in place.

**DevOps:** (Same structure as Web Application example above.)

---

## Interface Contract Patterns

These patterns show how interface contracts are structured for different project domains. When generating contracts for any project, follow the same format: explicit input/output specs with concrete examples.

### Pattern: Web Application / API Service
- Architect -> Implementer: Data schemas (JSON/TypeScript types with examples)
- Implementer -> Critic: Plan document (markdown with steps, files, schemas, edge cases)
- Critic -> Implementer: Approval (pass/fail + specific feedback per section)
- API Designer -> Frontend Specialist: API spec (OpenAPI or typed endpoint definitions)

### Pattern: CLI Tool
- Architect -> Implementer: Command spec (name, flags, args, output format)
- Implementer -> CLI/DX Specialist: Core functions (typed interfaces for CLI to call)
- CLI/DX Specialist -> Critic: Integration test results + help text review

### Pattern: Library / SDK
- API Designer -> Implementer: Public API surface (type signatures + method contracts)
- Implementer -> DX/Documentation: Implementation notes (what changed, what to document)
- DX/Documentation -> Critic: Documentation review (completeness, accuracy, examples)

### Pattern: Data Pipeline
- Architect -> Data Engineer: Pipeline spec (sources, transformations, destinations, schedules)
- Data Engineer -> Implementer: Data schemas + transformation rules
- Implementer -> Critic: Plan with data flow diagrams + edge case analysis

**For other domains:** Define contracts following the same format — explicit input artifact, explicit output artifact, and a concrete example. Every agent pair that exchanges work must have a contract.
