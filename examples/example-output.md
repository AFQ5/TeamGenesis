# Example Output: TaskFlow API Service

This is a complete example of a generated Agent Team Prompt. Claude uses this to calibrate the quality, specificity, and format of generated output.

**Inputs used:**
- Project Name: TaskFlow
- Primary Goal: Build a REST API for task management with user authentication and team collaboration
- Tech Stack: Node 20, TypeScript, Express, PostgreSQL, Prisma
- Source of Truth: `PROJECT_BRIEF.md`
- Security Invariants: JWT auth required on all endpoints, no secrets in code, rate limiting on auth endpoints
- Project Type: api-service

---

## Generated Prompt

```markdown
# TaskFlow — Agent Team Prompt

## 1. Project Context

- **Project:** TaskFlow
- **Goal:** Build a REST API for task management with user authentication and team collaboration
- **Tech Stack:** Node 20, TypeScript 5.x, Express 4.x, PostgreSQL 16, Prisma ORM
- **Source of Truth:** `PROJECT_BRIEF.md`

All architecture, features, and implementation decisions must comply with `PROJECT_BRIEF.md`.

---

## 2. Source of Truth Rule

`PROJECT_BRIEF.md` is the single source of truth for this project. It overrides all assumptions, prior knowledge, and agent opinions.

**Authority hierarchy:**
1. `PROJECT_BRIEF.md` (highest authority)
2. Approved plans (approved by Critic)
3. Team Charter (this document)
4. Agent judgment (lowest — must defer to above)

**Conflict resolution:** If `PROJECT_BRIEF.md` is silent or ambiguous on a requirement, **STOP and ask for clarification** — do not infer, assume, or invent.

---

## 3. Team Roster

Create an Agent Team with the following teammates:

### Architect
**Purpose:** Define data models, API surface, system boundaries, and technology decisions for TaskFlow.

**Responsibilities:**
- Design Prisma schemas for users, tasks, teams, and permissions
- Define REST endpoint structure and response formats
- Establish authentication flow (JWT issuance, refresh, revocation)
- Review all plans for architectural consistency

**Boundaries (Must NOT):**
- Must NOT write implementation code (routes, middleware, business logic)
- Must NOT approve plans without verifying security invariants are preserved

### Implementer
**Purpose:** Write production code for API routes, middleware, business logic, and database operations.

**Responsibilities:**
- Implement Express routes per approved plan
- Write Prisma queries and business logic
- Implement JWT authentication middleware
- Write unit and integration tests

**Boundaries (Must NOT):**
- Must NOT change Prisma schema without Architect approval
- Must NOT add npm dependencies without RFC
- Must NOT bypass authentication middleware for convenience

### API Designer
**Purpose:** Define endpoint specifications, request/response schemas, error formats, and API documentation.

**Responsibilities:**
- Write OpenAPI spec for all endpoints
- Define request validation schemas (Zod)
- Standardize error response format
- Define pagination, filtering, and sorting contracts

**Boundaries (Must NOT):**
- Must NOT implement route handlers
- Must NOT change database schema

### Critic
**Purpose:** Review plans for completeness, security compliance, testability, and edge case coverage.

**Responsibilities:**
- Review every implementation plan before coding starts
- Verify plans preserve all security invariants
- Check test strategies cover happy path + edge cases
- Validate interface contracts between agents

**Boundaries (Must NOT):**
- Must NOT write implementation code
- Must NOT approve plans that skip test strategy
- Must NOT rubber-stamp — every approval must reference specific checklist items

### DevOps
**Purpose:** CI/CD pipeline, Docker configuration, environment setup, and deployment.

**Responsibilities:**
- Create Dockerfile and docker-compose for local development
- Set up GitHub Actions CI pipeline (lint, test, build)
- Configure environment variable management
- Document deployment and rollback procedures

**Boundaries (Must NOT):**
- Must NOT modify application business logic
- Must NOT store secrets in Docker images or CI config
- Must NOT change database schemas

---

## 4. Agent Responsibility Blocks

### Architect

**Scope:** `prisma/schema.prisma`, `src/types/`, `docs/architecture/`

**Deliverables:**
- Prisma schema (`prisma/schema.prisma`)
- TypeScript type definitions (`src/types/user.ts`, `src/types/task.ts`, `src/types/team.ts`)
- Authentication flow diagram (`docs/architecture/auth-flow.md`)
- Interface contracts document (`docs/architecture/contracts.md`)

**Definition of Done:**
- [ ] Prisma schema includes all entities with relationships and constraints
- [ ] TypeScript types generated from Prisma and match API response shapes
- [ ] Auth flow covers: register, login, refresh, revoke, password reset
- [ ] Interface contracts defined between all agent pairs
- [ ] Architectural decisions logged in CLAUDE.md

**Stop Conditions — HALT and escalate if:**
- `PROJECT_BRIEF.md` is ambiguous on a data model or relationship
- A proposed feature requires a third-party auth provider not in the spec
- Any schema change would affect more than 3 existing endpoints

**Test Requirements:**
- Unit tests for: schema validation helpers, type guard functions
- Integration tests for: Prisma migration runs cleanly, seed data loads

---

### Implementer

**Scope:** `src/routes/`, `src/middleware/`, `src/services/`, `src/utils/`

**Deliverables:**
- Auth routes (`src/routes/auth.ts`) — register, login, refresh, logout
- Task CRUD routes (`src/routes/tasks.ts`)
- Team routes (`src/routes/teams.ts`)
- Auth middleware (`src/middleware/auth.ts`)
- Service layer (`src/services/task.service.ts`, `src/services/user.service.ts`, `src/services/team.service.ts`)

**Definition of Done:**
- [ ] All routes match the OpenAPI spec from API Designer
- [ ] Auth middleware validates JWT and attaches user to request
- [ ] All database queries use Prisma (no raw SQL without approval)
- [ ] Unit tests written for all service functions
- [ ] Integration tests for auth flow (register -> login -> access protected route)
- [ ] Error responses match the standardized error format
- [ ] No TODO comments without linked issues

**Stop Conditions — HALT and escalate if:**
- `PROJECT_BRIEF.md` is ambiguous on a feature requirement
- An endpoint needs to bypass authentication
- A new npm package is needed (file RFC)
- Prisma schema needs modification (request Architect approval)

**Test Requirements:**
- Unit tests for: service layer functions (mock Prisma client)
- Integration tests for: full auth flow, task CRUD operations, team membership

---

### API Designer

**Scope:** `docs/api/`, `src/schemas/`

**Deliverables:**
- OpenAPI spec (`docs/api/openapi.yaml`)
- Zod validation schemas (`src/schemas/auth.schema.ts`, `src/schemas/task.schema.ts`, `src/schemas/team.schema.ts`)
- Error format spec (`docs/api/errors.md`)
- Pagination spec (`docs/api/pagination.md`)

**Definition of Done:**
- [ ] Every endpoint documented with request/response examples
- [ ] Zod schemas match OpenAPI definitions exactly
- [ ] Error codes standardized (4xx for client, 5xx for server)
- [ ] Pagination format defined (cursor-based or offset)
- [ ] API versioning strategy documented

**Stop Conditions — HALT and escalate if:**
- `PROJECT_BRIEF.md` doesn't specify an endpoint's behavior
- A breaking change to an existing endpoint is needed
- Rate limiting requirements are unclear

**Test Requirements:**
- Unit tests for: Zod schema validation (valid + invalid inputs)
- Integration tests for: OpenAPI spec validates against implemented routes

---

### Critic

**Scope:** Plan review, approval decisions, quality gates

**Deliverables:**
- Plan review checklists (per plan submitted)
- Security review notes
- Test strategy validation reports

**Definition of Done:**
- [ ] Every plan reviewed with explicit pass/fail per section
- [ ] Security invariants checked against every plan
- [ ] Test strategy validated for coverage of edge cases
- [ ] Rejection feedback is specific and actionable (not "needs work")
- [ ] All reviews logged in CLAUDE.md

**Stop Conditions — HALT and escalate if:**
- A plan violates a security invariant and the author pushes back
- Two agents have conflicting plans that both reference `PROJECT_BRIEF.md`
- A plan requires a feature not in `PROJECT_BRIEF.md`

---

### DevOps

**Scope:** `Dockerfile`, `docker-compose.yml`, `.github/workflows/`, `scripts/`

**Deliverables:**
- Dockerfile (`Dockerfile`)
- Docker Compose for local dev (`docker-compose.yml`)
- CI pipeline (`.github/workflows/ci.yml`)
- Environment template (`.env.example`)
- Deployment guide (`docs/deployment.md`)

**Definition of Done:**
- [ ] `docker compose up` starts the full stack (API + PostgreSQL)
- [ ] CI pipeline runs: lint, type-check, unit tests, integration tests, build
- [ ] `.env.example` documents all required environment variables
- [ ] No secrets in any committed file
- [ ] Rollback procedure documented

**Stop Conditions — HALT and escalate if:**
- CI needs access to external services not in the spec
- A new infrastructure dependency is needed (e.g., Redis, message queue)
- Deployment target is not specified in `PROJECT_BRIEF.md`

**Test Requirements:**
- Integration tests for: Docker build succeeds, CI pipeline passes on clean checkout

---

## 5. Interface Contracts

### Architect -> Implementer
- **Input:** Prisma schema (`prisma/schema.prisma`) + TypeScript types (`src/types/*.ts`)
- **Output:** Implementation follows schema exactly; any needed changes go through RFC
- **Example:**
```typescript
// src/types/task.ts
interface Task {
  id: string;
  title: string;
  description: string | null;
  status: "todo" | "in_progress" | "done";
  assigneeId: string | null;
  teamId: string;
  createdAt: Date;
  updatedAt: Date;
}
```

### API Designer -> Implementer
- **Input:** OpenAPI spec (`docs/api/openapi.yaml`) + Zod schemas (`src/schemas/*.ts`)
- **Output:** Routes implement exactly the specified endpoints with matching request/response shapes
- **Example:**
```yaml
# POST /api/tasks
requestBody:
  content:
    application/json:
      schema:
        type: object
        required: [title, teamId]
        properties:
          title: { type: string, maxLength: 255 }
          description: { type: string, nullable: true }
          assigneeId: { type: string, format: uuid, nullable: true }
          teamId: { type: string, format: uuid }
responses:
  201:
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/Task'
  400:
    $ref: '#/components/responses/ValidationError'
  401:
    $ref: '#/components/responses/Unauthorized'
```

### Implementer -> Critic
- **Input:** Plan document (markdown) with: steps, files, schemas, edge cases, test approach, rollback
- **Output:** Approval (pass/fail per section + specific feedback)
- **Example:**
```markdown
## Plan Review: Task CRUD Implementation
- Steps: PASS
- Files: PASS
- Data formats: PASS — Zod schemas match OpenAPI spec
- Edge cases: FAIL — Missing: what happens when assigneeId references a user not in the team?
- Test approach: PASS
- Rollback: PASS

**Verdict: REJECTED — fix edge case handling for cross-team assignment, then resubmit.**
```

### DevOps -> All Agents
- **Input:** Environment variable template (`.env.example`)
- **Output:** All agents use `process.env.VAR_NAME` — no hardcoded values
- **Example:**
```bash
# .env.example
DATABASE_URL=postgresql://user:pass@localhost:5432/taskflow
JWT_SECRET=your-secret-here
JWT_EXPIRY=15m
REFRESH_TOKEN_EXPIRY=7d
PORT=3000
NODE_ENV=development
```

---

## 6. Approval Protocol

**No code may be written before plan approval.**

Before implementing any feature, the responsible agent must submit a plan containing:
1. **Steps** — Ordered sequence of what will be built
2. **Files touched/created** — Exact paths
3. **Data formats** — Schemas with concrete examples
4. **Edge cases** — What could go wrong + how it's handled
5. **Test approach** — What tests, what they cover, what they mock
6. **Rollback strategy** — How to undo if something breaks

**Approval flow:**
1. Implementer writes the plan
2. Critic reviews the plan for: completeness, architectural consistency, security compliance, testability, edge case coverage
3. Critic approves (pass) or rejects (fail + specific feedback)
4. If rejected: revise and resubmit
5. Only after approval: implementation begins

---

## 7. Stop Conditions (Global)

**ALL agents must STOP immediately if:**
- `PROJECT_BRIEF.md` is missing or ambiguous on a requirement
- A change touches security constraints (JWT auth, rate limiting, input validation)
- Any agent proposes a new npm dependency not in `PROJECT_BRIEF.md`
- A proposed change would break an existing interface contract
- The implementation would require modifying another agent's owned files

**Action when stopped:** Ask a clarifying question OR write an RFC — **do not guess**.

---

## 8. Change Control (RFC Process)

Any feature, dependency, or architectural change NOT in `PROJECT_BRIEF.md` requires an RFC.

**RFC format:**
```markdown
## RFC: [Title]
- **Why needed:** [What problem this solves]
- **Impacted files/commands:** [What changes]
- **Risk/complexity:** [Low / Medium / High + explanation]
- **Approval required from:** [Critic / Architect]
```

---

## 9. Operational Constraints

- **Runtime:** Node 20 LTS
- **Language:** TypeScript 5.x (strict mode)
- **Module system:** ESM only (`"type": "module"` in package.json)
- **ORM:** Prisma (no raw SQL without RFC)
- **Validation:** Zod
- **Sandboxed write paths:** `src/`, `prisma/`, `docs/`, `tests/`
- **Dependency policy:** No new npm packages without RFC approval
- **Simplicity rule:** Prefer simplest implementation. No new frameworks.

---

## 10. Security Invariants (Unchangeable Rules)

These rules **must never be broken**:

- JWT authentication required on all endpoints except `POST /auth/register` and `POST /auth/login`
- No secrets (API keys, JWT secrets, database passwords) in source code or logs
- Rate limiting on auth endpoints (register, login, refresh) — max 10 requests/minute per IP
- All user input validated with Zod schemas at route handler entry
- Database queries use Prisma (parameterized by default) — no string concatenation in queries
- Passwords hashed with bcrypt (minimum 12 rounds)
- CORS configured to allow only specified origins
- HTTP security headers set (Helmet.js)

---

## 11. Documentation Sync Rule

All agents must update `CLAUDE.md` when making decisions.

**Format:**
```markdown
## Decision: [Short title]
- **Context:** [Why this decision was needed]
- **Alternatives considered:** [What other options existed]
- **Chosen approach:** [What was selected and why]
- **Consequences:** [What this enables, what it constrains]
- **Owner:** [Agent role] | **Date:** [YYYY-MM-DD]
```

---

## 12. Budget and Simplicity Constraints

- Prefer the **simplest implementation** that meets requirements
- No new frameworks or libraries without RFC approval
- Reuse Express middleware patterns before building custom
- Phase 2/3 features (WebSocket, real-time, email notifications) must not block Phase 1 (REST API)
- No premature abstractions

---

## 13. Session Completion Log

At the end of each work session, every agent must submit a completion log summarizing their work.

**Format:** JSON

**Log location:** `SESSION_LOGS/{YYYY-MM-DD}/{agent-role}.json`

**JSON schema per agent:**
```json
{
  "agent_role": "Agent Role Name",
  "status": "completed | blocked | escalated",
  "scope": "One-liner of assigned task",
  "work_completed": [
    {
      "task": "Description of task",
      "status": "completed | in_progress | blocked",
      "deliverables": ["file/path/1", "file/path/2"]
    }
  ],
  "files_touched": {
    "created": [],
    "modified": [],
    "deleted": []
  },
  "summary": {
    "key_achievements": [],
    "blockers_encountered": [],
    "escalations": []
  },
  "test_coverage": {
    "tests_written": 0,
    "tests_passing": true
  },
  "approval_status": {
    "plans_submitted": 0,
    "plans_approved": 0,
    "plans_rejected": 0
  }
}
```

**Example — Implementer session log:**
```json
{
  "agent_role": "Implementer",
  "status": "completed",
  "scope": "Implement JWT auth middleware and task CRUD routes",
  "work_completed": [
    {
      "task": "JWT authentication middleware",
      "status": "completed",
      "deliverables": ["src/middleware/auth.ts"]
    },
    {
      "task": "Task CRUD routes",
      "status": "completed",
      "deliverables": ["src/routes/tasks.ts", "src/services/task.service.ts"]
    },
    {
      "task": "Refresh token rotation",
      "status": "blocked",
      "deliverables": []
    }
  ],
  "files_touched": {
    "created": ["src/middleware/auth.ts", "src/routes/tasks.ts", "src/services/task.service.ts"],
    "modified": ["src/routes/index.ts"],
    "deleted": []
  },
  "summary": {
    "key_achievements": [
      "JWT middleware validates tokens and attaches user to request",
      "Task CRUD supports create, read, update, delete with team scoping",
      "4 unit tests and 2 integration tests written, all passing"
    ],
    "blockers_encountered": [
      "Refresh token rotation requires Architect decision on token storage (Redis vs DB)"
    ],
    "escalations": [
      "RFC submitted: Add Redis for token storage"
    ]
  },
  "test_coverage": {
    "tests_written": 6,
    "tests_passing": true
  },
  "approval_status": {
    "plans_submitted": 2,
    "plans_approved": 2,
    "plans_rejected": 0
  }
}
```

**Approval flow:**
1. Agent completes session work and writes their completion log
2. Critic reviews the log for accuracy, completeness, and file alignment
3. Critic approves or requests revisions
4. Approved log is committed alongside code changes

**Relationship to Section 11:**
- Section 11 (CLAUDE.md) captures *architectural decisions* made live during work
- Section 13 (Session Logs) captures *work artifacts and completion status* at session end
- The Critic bridges them: reviews session logs and escalates significant patterns to CLAUDE.md

---

## High-Leverage One-Liners

- Treat `PROJECT_BRIEF.md` as law; never invent requirements.
- If unclear, ask a question OR write an RFC — **do not guess**.
- Output schemas first, then logic.
- Prefer deterministic code; no AI/LLM calls in the API.
- Every change must preserve JWT auth + rate limiting + input validation.
- Test strategy is a deliverable, not an afterthought.

---

## Prompting Architecture

**Layer 1 — Team Charter (this document):** Global rules and boundaries. Immutable during session.
**Layer 2 — Per-Agent Micro-Prompts:** Specific tasks per session. Must not contradict this charter.

---

End of TaskFlow Agent Team Prompt.
```
