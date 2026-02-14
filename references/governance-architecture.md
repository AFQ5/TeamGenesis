# Governance Architecture Reference

This document defines the **7-Point Governance Architecture** and **6 Quality Multipliers** that form the foundation of every TeamGenesis-generated Agent Team Prompt.

Claude: Read this file to understand the principles behind each section you generate. Do not copy this text verbatim into the output — use it to inform the depth and rigor of what you write.

---

## The 7-Point Governance Architecture

### Point 1: Project Context

**What it is:** The opening block that defines the mission boundary for the entire team.

**Must include:**
- Project name and one-line goal
- Runtime and tech stack (language version, framework, module system)
- Binary/package name (if applicable)
- Source of Truth file path

**Why it matters:** Without an explicit boundary, agents invent requirements. The Project Context block forces every agent to anchor decisions to a concrete scope.

**Anti-pattern:** Vague context like "build a web app." Always specify: build what, with what, constrained by what.

---

### Point 2: Source of Truth Rule

**What it is:** The authority hierarchy that resolves all conflicts and ambiguities.

**Must include:**
- Which file is the single source of truth (e.g., `PROJECT_BRIEF.md`, `PRD.md`)
- The explicit rule: this file overrides all assumptions, prior knowledge, and agent opinions
- The conflict resolution protocol: **STOP and ask for clarification — do not guess**

**Why it matters:** Agents hallucinate when they encounter ambiguity. The Source of Truth Rule gives them a clear escalation path instead of guessing.

**Key phrase to embed:** "If {{SOURCE_OF_TRUTH}} is silent or ambiguous on a requirement, STOP and ask — do not infer, assume, or invent."

---

### Point 3: Team Definition

**What it is:** The roster of agents with their roles and explicit negative boundaries.

**Must include per agent:**
- Role name and one-line purpose
- 3-5 core responsibilities (what they DO)
- 2-3 explicit "Must NOT" boundaries (what they must NEVER do)

**Why it matters:** Without negative boundaries, agents expand their scope. An Architect who can also write code will start writing code. A QA agent who can also implement will skip review and just fix things.

**Rule:** Every "Must NOT" boundary must be specific and testable. Not "Must NOT overstep" — instead "Must NOT write implementation code" or "Must NOT approve plans without security review."

---

### Point 4: Responsibility Blocks

**What it is:** The detailed per-agent contract that defines scope, deliverables, Definition of Done, and Stop Conditions.

**Must include per agent:**

**Scope:** What files, modules, or system areas this agent owns.

**Deliverables:** Exact files or artifacts this agent produces. File-level specificity — not "create the API" but "create `src/routes/tasks.ts`, `src/models/task.ts`, `src/schemas/task.schema.ts`."

**Definition of Done (DoD):** A checklist of measurable criteria. Each item must be verifiable (yes/no). Examples:
- All planned files created/updated
- Unit tests written and passing
- Edge cases handled or documented as known limitations
- Interface contracts validated against schema
- No TODO comments left without linked issue

**Stop Conditions:** Specific situations where this agent must halt and escalate. Examples:
- Source of Truth is ambiguous on a requirement
- A change would touch another agent's owned files
- A new dependency is needed that's not in the approved list
- Security invariants would be affected

**Test Strategy:** What tests this agent must write (unit tests for pure logic, integration tests for workflows). This is a first-class deliverable, not an afterthought.

---

### Point 5: Approval Protocol

**What it is:** The safety gate that prevents code from being written before a plan is reviewed.

**The rule: No code before plan approval.**

**A plan must include:**
1. Steps (ordered sequence of what will be built)
2. Files touched/created (exact paths)
3. Data formats (schemas with examples, not just descriptions)
4. Edge cases and failure modes (what could go wrong)
5. Test approach (what tests, what they cover, what they mock)
6. Rollback strategy (how to undo if something breaks)

**Who approves:** The Critic/Reviewer agent reviews and approves the **plan** (not the code). They check for: completeness, architectural consistency, security compliance, testability, and edge case coverage.

**Escalation:** If the Critic rejects a plan, the rejection must include specific feedback and the plan must be revised before any implementation begins.

---

### Point 6: Operational Constraints

**What it is:** The hard limits that no agent may violate under any circumstances.

**Must include:**
- Runtime constraints (e.g., "Node 20 ESM only", "Python 3.11+, no 2.x compat")
- Module system (ESM, CommonJS, etc.)
- Allowed/forbidden dependencies
- Sandboxed write paths (which directories agents may write to)
- Security invariants (the unchangeable rules)

**Security Invariants format:** Each invariant is a rule that **must never be broken**, regardless of convenience or efficiency. Examples:
- All user input must be validated at system boundaries
- No secrets in source code or logs
- All endpoints require authentication unless explicitly marked public
- HTML output escapes all user-derived content (XSS prevention)
- Database queries use parameterized statements (SQL injection prevention)

**Rule:** If an agent's implementation would violate any operational constraint, they must STOP and escalate — not work around it.

---

### Point 7: Documentation Sync Rule

**What it is:** The protocol for keeping project documentation current as agents make decisions.

**All agents must update CLAUDE.md using this format:**

```
## Decision: [Short title]
- **Context:** Why this decision was needed
- **Alternatives considered:** What other options existed
- **Chosen approach:** What was selected and why
- **Consequences:** What this enables, what it constrains
- **Owner:** [Agent role] | **Date:** [YYYY-MM-DD]
```

**Why it matters:** Without structured decision logging, later agents (or future sessions) lose context on why things were built a certain way. This prevents "churn" where agents undo each other's decisions.

**Rule:** Every architectural decision, technology choice, or trade-off must be logged. If an agent makes a decision and doesn't log it, the decision is incomplete.

---

## Quality Multipliers

These six discipline layers complement the 7-Point Architecture. They are embedded throughout the generated prompt, not isolated in their own sections.

### QM-1: Interface Contracts

**What it is:** Explicit input/output specifications between agents.

**Format per contract:**
```
Between [Agent A] -> [Agent B]:
- Input: [exact format — JSON schema, file path, CLI flags]
- Output: [exact format — response schema, file artifact, approval status]
- Example: [one concrete example with real data]
```

**Rule:** No agent may pass data to another agent without a defined contract. If the contract doesn't exist, create it before proceeding.

### QM-2: Change Control (RFC Process)

**What it is:** The protocol for proposing changes that aren't in the original Source of Truth.

**RFC format:**
```
## RFC: [Title]
- **Why needed:** What problem this solves
- **Impacted files/commands:** What changes
- **Risk/complexity:** Low / Medium / High + explanation
- **Approval required from:** [Agent role]
```

**Rule:** Any feature, dependency, or architectural change not in the Source of Truth file requires an RFC before implementation.

### QM-3: Decision Log Format

Already defined in Point 7 (Documentation Sync Rule). When generating the prompt, embed the decision log format in the Documentation Sync section rather than creating a separate section.

### QM-4: No Code Before Plan

Already enforced by Point 5 (Approval Protocol). When generating the prompt, emphasize this rule in both the Approval Protocol section and in each agent's Stop Conditions.

**Plan requirements (for reference):**
- Steps (ordered)
- Files touched/created
- Data formats (schemas with examples)
- Edge cases + failure modes
- Test approach (unit + integration)
- Rollback strategy

### QM-5: Two-Layer Prompting

**What it is:** Separation of global rules from per-agent task instructions.

- **Layer 1 — Team Charter:** The generated prompt itself. Defines global rules, boundaries, approval gates, and constraints that apply to ALL agents.
- **Layer 2 — Per-Agent Micro-Prompts:** Specific task instructions generated dynamically per session. Each micro-prompt references the Team Charter and adds the specific task, context, and acceptance criteria for that agent's current work.

**Rule:** The Team Charter is immutable during a session. Per-Agent micro-prompts must not contradict the Team Charter.

### QM-6: Budget Constraints

**What it is:** The simplicity enforcement layer.

**Rules:**
- Prefer the simplest implementation that meets requirements
- No new frameworks or libraries without RFC approval
- Reuse existing project infrastructure before building new
- Phase 2/3 features must not block Phase 1 delivery
- If a task can be done in 20 lines without a library, do it in 20 lines

**Why it matters:** Agents over-engineer by default. They add abstractions, utilities, and "nice to have" features that bloat the codebase. Budget constraints force focus.
