# Agent Team Prompt Template

This is the output template Claude fills in when generating an Agent Team Prompt. Replace all `{{PLACEHOLDER}}` values with the user's inputs. Adapt conditional sections based on project type and selected roles.

---

## Template Start

```markdown
# {{PROJECT_NAME}} — Agent Team Prompt

## 1. Project Context

- **Project:** {{PROJECT_NAME}}
- **Goal:** {{PRIMARY_GOAL}}
- **Tech Stack:** {{TECH_STACK}}
- **Source of Truth:** `{{SOURCE_OF_TRUTH}}`

All architecture, features, and implementation decisions must comply with `{{SOURCE_OF_TRUTH}}`.

---

## 2. Source of Truth Rule

`{{SOURCE_OF_TRUTH}}` is the single source of truth for this project. It overrides all assumptions, prior knowledge, and agent opinions.

**Authority hierarchy:**
1. `{{SOURCE_OF_TRUTH}}` (highest authority)
2. Approved plans (approved by Critic)
3. Team Charter (this document)
4. Agent judgment (lowest — must defer to above)

**Conflict resolution:** If `{{SOURCE_OF_TRUTH}}` is silent or ambiguous on a requirement, **STOP and ask for clarification** — do not infer, assume, or invent.

---

## 3. Team Roster

Create an Agent Team with the following teammates:

{{FOR_EACH_AGENT}}
### {{AGENT_ROLE_NAME}}
**Purpose:** {{AGENT_PURPOSE}}

**Responsibilities:**
- {{RESPONSIBILITY_1}}
- {{RESPONSIBILITY_2}}
- {{RESPONSIBILITY_3}}

**Boundaries (Must NOT):**
- Must NOT {{BOUNDARY_1}}
- Must NOT {{BOUNDARY_2}}
{{END_FOR_EACH}}

---

## 4. Agent Responsibility Blocks

{{FOR_EACH_AGENT}}
### {{AGENT_ROLE_NAME}}

**Scope:** {{OWNED_FILES_AND_MODULES}}

**Deliverables:**
- {{DELIVERABLE_1}} (`{{FILE_PATH_1}}`)
- {{DELIVERABLE_2}} (`{{FILE_PATH_2}}`)

**Definition of Done:**
- [ ] {{DOD_ITEM_1}}
- [ ] {{DOD_ITEM_2}}
- [ ] {{DOD_ITEM_3}}
- [ ] {{DOD_ITEM_4}}
- [ ] No TODO comments without linked issues

**Stop Conditions — HALT and escalate if:**
- `{{SOURCE_OF_TRUTH}}` is ambiguous on a requirement
- A change touches another agent's owned files
- A new dependency is needed that's not in the approved list
- Security invariants would be affected
- {{AGENT_SPECIFIC_STOP_CONDITION}}

**Test Requirements:**
- Unit tests for: {{UNIT_TEST_TARGETS}}
- Integration tests for: {{INTEGRATION_TEST_TARGETS}}

---
{{END_FOR_EACH}}

## 5. Interface Contracts

{{FOR_EACH_CONTRACT}}
### {{AGENT_A}} -> {{AGENT_B}}
- **Input:** {{INPUT_FORMAT_AND_SCHEMA}}
- **Output:** {{OUTPUT_FORMAT_AND_SCHEMA}}
- **Example:**
```{{FORMAT}}
{{CONCRETE_EXAMPLE}}
```
{{END_FOR_EACH}}

---

## 6. Approval Protocol

**No code may be written before plan approval.**

Before implementing any feature, the responsible agent must submit a plan containing:
1. **Steps** — Ordered sequence of what will be built
2. **Files touched/created** — Exact paths
3. **Data formats** — Schemas with concrete examples (not just descriptions)
4. **Edge cases** — What could go wrong + how it's handled
5. **Test approach** — What tests, what they cover, what they mock
6. **Rollback strategy** — How to undo if something breaks

**Approval flow:**
1. Implementer (or assigned agent) writes the plan
2. Critic reviews the plan (NOT the code) for: completeness, architectural consistency, security compliance, testability, edge case coverage
3. Critic approves (pass) or rejects (fail + specific feedback)
4. If rejected: revise and resubmit — no partial approvals
5. Only after approval: implementation begins

---

## 7. Stop Conditions (Global)

**ALL agents must STOP immediately if:**
- `{{SOURCE_OF_TRUTH}}` is missing or ambiguous on a requirement
- A change touches security constraints ({{SECURITY_AREAS}})
- Any agent proposes a new dependency not in `{{SOURCE_OF_TRUTH}}`
- A proposed change would break an existing interface contract
- The implementation would require modifying another agent's owned files

**Action when stopped:** Ask a clarifying question OR write an RFC — **do not guess, assume, or work around**.

---

## 8. Change Control (RFC Process)

Any feature, dependency, or architectural change NOT in `{{SOURCE_OF_TRUTH}}` requires an RFC before implementation.

**RFC format:**
```markdown
## RFC: [Title]
- **Why needed:** [What problem this solves]
- **Impacted files/commands:** [What changes]
- **Risk/complexity:** [Low / Medium / High + explanation]
- **Approval required from:** [Critic / Architect]
```

RFCs must be approved by the Critic before any implementation begins.

---

## 9. Operational Constraints

- **Runtime:** {{TECH_STACK}}
- **Module system:** {{MODULE_SYSTEM}}
- **Sandboxed write paths:** {{ALLOWED_WRITE_PATHS}}
- **Dependency policy:** No external dependencies without RFC approval
- **Simplicity rule:** Prefer simplest implementation. No new frameworks without approval. If it can be done in 20 lines without a library, do it in 20 lines.

---

## 10. Security Invariants (Unchangeable Rules)

These rules **must never be broken** regardless of convenience or efficiency:

{{FOR_EACH_INVARIANT}}
- {{SECURITY_INVARIANT}}
{{END_FOR_EACH}}

If an implementation would violate any security invariant, the agent must **STOP and escalate** — not work around it.

---

## 11. Documentation Sync Rule

All agents must update `CLAUDE.md` when making architectural decisions, technology choices, or trade-offs.

**Required format:**
```markdown
## Decision: [Short title]
- **Context:** [Why this decision was needed]
- **Alternatives considered:** [What other options existed]
- **Chosen approach:** [What was selected and why]
- **Consequences:** [What this enables, what it constrains]
- **Owner:** [Agent role] | **Date:** [YYYY-MM-DD]
```

**Rule:** Every decision that affects architecture, technology choices, or trade-offs must be logged. An unlogged decision is an incomplete decision.

---

## 12. Budget and Simplicity Constraints

- Prefer the **simplest implementation** that meets requirements
- No new frameworks or libraries without RFC approval
- Reuse existing project infrastructure before building new
- Phase 2/3 features must not block Phase 1 delivery
- No premature abstractions — three similar lines > one premature helper

---

## 13. Session Completion Log

At the end of each work session, every agent must submit a completion log summarizing their work.

**Format:** {{SESSION_LOG_FORMAT}} (JSON | Markdown | Both)

**Log location:** `SESSION_LOGS/{{SESSION_DATE}}/{{AGENT_ROLE}}.{{LOG_EXT}}`

{{IF SESSION_LOG_FORMAT == "JSON" OR SESSION_LOG_FORMAT == "Both"}}
**JSON schema per agent:**
```json
{
  "agent_role": "{{AGENT_ROLE_NAME}}",
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
{{END_IF}}

{{IF SESSION_LOG_FORMAT == "Markdown" OR SESSION_LOG_FORMAT == "Both"}}
**Markdown format per agent:**
```markdown
### Agent: {{AGENT_ROLE_NAME}}
- **Status:** completed | blocked | escalated
- **Scope:** One-liner of assigned task

#### Work Completed
- [x] Task description → `deliverable/file/path`

#### Files Touched
- **Created:** list of file paths
- **Modified:** list of file paths
- **Deleted:** list of file paths

#### Summary
**Achievements:** list of key accomplishments
**Blockers:** list of blockers (or "None")
**Escalations:** list of escalations (or "None")

#### Test Coverage
- Tests written: N
- Tests passing: Yes/No

#### Approvals
- Plans submitted: N | Approved: N | Rejected: N
```
{{END_IF}}

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

- Treat `{{SOURCE_OF_TRUTH}}` as law; never invent requirements.
- If unclear, ask a question OR write an RFC — **do not guess**.
- Output schemas first, then logic.
- Prefer deterministic code; LLM/AI used only where specified.
- Every change must preserve security invariants.
- Test strategy is a deliverable, not an afterthought.

---

## Prompting Architecture

**Layer 1 — Team Charter (this document):** Defines global rules, boundaries, approval gates, and constraints. Immutable during a session.

**Layer 2 — Per-Agent Micro-Prompts:** Specific task instructions generated per session. Each micro-prompt references this Team Charter and adds the specific task, context, and acceptance criteria for that agent's current work.

Micro-prompts must not contradict the Team Charter.

---

End of {{PROJECT_NAME}} Agent Team Prompt.
```

## Template End

---

## Generation Notes for Claude

When filling in this template:

1. **Replace all `{{PLACEHOLDER}}` values** with the user's actual inputs.
2. **Generate `{{FOR_EACH}}` blocks** dynamically based on the selected roles and project type.
3. **Be specific** — file paths, schema examples, and test targets should reference real project structure, not generic placeholders.
4. **Security invariants:** If the user didn't provide any, use these defaults:
   - No secrets (API keys, passwords, tokens) in source code or logs
   - All user input validated at system boundaries
   - Parameterized queries for all database operations
   - HTML output escapes user-derived content
5. **Interface contract examples** must include concrete data, not just format descriptions. Show a real JSON object, a real CLI command, a real file path.
6. **Definition of Done items** must be verifiable (yes/no checkable). Not "code is clean" — instead "no TODO comments without linked issues."
7. **Write the output to the file** specified by the user (default: `./AGENT_TEAM_PROMPT.md`). Do not print the entire prompt to chat — write to file, then show a summary.
