---
name: team-genesis
description: "Generate compact Agent Team governance that goes directly into CLAUDE.md — auto-loaded, no manual referencing needed. Agents update their state in the same file. Use when creating a new multi-agent team, setting up agent roles, or starting a project that needs structured AI agent collaboration. Trigger phrases: create agent team, set up team prompt, generate team charter, new agent team, team genesis, spin up agents, agent coordination."
tags: [agents, team, governance, architecture, multi-agent, coordination]
---

# TeamGenesis: Agent Team Architecture Generator

You generate compact Agent Team governance that appends to `CLAUDE.md`. Claude Code auto-loads `CLAUDE.md` at session start — no `@` referencing, no extra token cost. Agents read it, follow it, and update their state in it.

**Do NOT use this skill for:** General coding tasks, single-agent work, code review, debugging, or documentation.

---

## Step 1: Gather Inputs

Ask for all inputs in a **single message**.

**Required:**
1. **Project Name** — e.g., "TaskFlow"
2. **Primary Goal** — One sentence
3. **Tech Stack** — Languages, frameworks, runtime
4. **Source of Truth** — The canonical spec file (e.g., `PROJECT_BRIEF.md`). Offer to create a template if it doesn't exist.

**Optional (defaults if skipped):**
5. **Security Invariants** — Default: no secrets in code, validate input, parameterized queries, escape HTML
6. **Custom Roles** — Override auto-selected specialists

If inputs provided upfront, skip questions and generate.

**Before asking:** Check if `CLAUDE.md` contains a `## Team Governance` section. If yes, ask:
- **Continue** — Same agents, same goals, resume where they left off
- **Update** — Same agents, new goals, state preserved
- **New** — Regenerate (old section archived as HTML comment)

If continuing/updating:
1. Read existing Agent State table — check for blockers and completed work
2. Read decision log entries in CLAUDE.md — understand prior context
3. Read Source of Truth — check for changes since last session
4. Resolve blockers before starting new work

---

## Step 2: Select Agent Roles

Read `references/project-type-profiles.md` for core roles and specialist examples.

**Always include:**
- **Architect** — System design, schemas, contracts
- **Implementer** — Code per approved plans
- **Critic** — Plan review, quality gates, drift checks

**Add 1-3 specialists** based on the project. Every role gets: scope, boundaries ("Must NOT"), DoD, stop conditions. _(Without negative boundaries, agents expand scope — an Architect who can code will start coding.)_

---

## Step 3: Generate

Generate a compact governance block. The **entire block must fit under 80 lines** — it lives in CLAUDE.md which loads every session. Every line must earn its place.

### Output Format

The generated block follows this structure:

```markdown
## Team Governance
<!-- TeamGenesis v1.0.0 | {DATE} | {PROJECT_NAME} -->

### Source of Truth
`{SOURCE_OF_TRUTH}` is law. Authority: spec > approved plans > this charter > agent judgment.
If ambiguous, STOP and ask — do not infer, assume, or invent.

### Team
| Agent | Scope | Must NOT |
|---|---|---|
| {Role} | {owned files/dirs} | {concrete boundaries} |

### Agent Definitions

#### {Role}
- **DoD:** {3-5 binary checkable items}
- **Stop if:** {2-3 specific halt conditions}
- **Tests:** {unit + integration targets}

### Contracts
| From | To | Artifact | Format |
|---|---|---|---|
| Architect | Implementer | Data schemas | TypeScript types in `src/types/` |

### Rules
1. **Plan before code.** Plans: steps, files, schemas, edge cases, tests, rollback. Critic approves.
2. **Stay in scope.** Cross-boundary changes require RFC.
3. **Contracts are binding.** Typed interfaces between agent pairs.
4. **Security: {invariants}.** Violation = halt and escalate.
5. **Drift check.** Critic re-reads spec every 2-3 tasks.
6. **Log decisions** in this file: context, alternatives, chosen approach, consequences.
7. **Escalation → user.** Format: decision needed, options, recommendation, blocked work.

### Agent State
<!-- Agents: update this table as you work -->
| Agent | Status | Current Task | Completed | Blockers |
|---|---|---|---|---|
| {Role} | idle | - | - | - |
```

### Quality Floors

Even in compact format, every element must meet these minimums:
- **Boundaries:** Concrete actions (`Must NOT write implementation code`), not vague (`Must NOT overstep`)
- **DoD items:** Binary checks (`All unit tests passing`), not subjective (`code is clean`)
- **Contracts:** Real artifact with format (`TypeScript types in src/types/`), not abstract (`data exchange`)
- **Stop conditions:** Explicit trigger + action (`if spec ambiguous on data relationship, escalate to user`)

### Governance Scaling

| Scope | Agents | Contracts | DoD depth |
|---|---|---|---|
| **Small** (1-2 features) | 3-4 | 2-4 | 2-line DoD, 1-line stop |
| **Medium** (3-5 features) | 4-5 | 5-8 | 3-line DoD, 2-line stop |
| **Large** (5+ features) | 5-6 | 8-12 | 5-line DoD, 3-line stop |

---

## Step 3.5: Self-Validate

Before writing, verify:
- [ ] Every agent has DoD with ≥3 binary items
- [ ] Every agent has ≥2 stop conditions
- [ ] Every contract specifies artifact + format
- [ ] Security invariants in both Rules AND relevant agent boundaries
- [ ] Source of Truth referenced in stop conditions
- [ ] Total governance block ≤ 80 lines

---

## Step 4: Output

1. **Read existing `CLAUDE.md`** if present. Preserve all non-TeamGenesis content.
2. **Append** the `## Team Governance` section (or replace existing one).
3. **Write `TEAM_AGENTS.json`** — minimal identity file for reload detection:
```json
{
  "team": "{PROJECT_NAME}",
  "source_of_truth": "{SOURCE_OF_TRUTH}",
  "governance_version": "1.0.0",
  "created": "{DATE}",
  "agents": ["{Role1}", "{Role2}"]
}
```
4. **Show summary:** team composition, lines added to CLAUDE.md.

### Why this model works

| Concern | Solution |
|---|---|
| Token cost | < 80 lines, auto-loaded — no extra `@` reference cost |
| Agent state | State table in CLAUDE.md — agents edit it naturally |
| Persistence | CLAUDE.md survives between sessions, auto-loaded next time |
| Reload detection | TEAM_AGENTS.json (~5 lines) lets the skill detect existing teams |
| Decision history | Agents log decisions in CLAUDE.md per Rule 6 |

---

## Error Handling

- **Missing required inputs:** Ask specifically for what's missing
- **Missing optional inputs:** Use defaults
- **Ambiguous roles:** Ask to clarify
- **Conflicting roles:** Flag overlap, ask resolution
- **No Source of Truth file:** Offer to create template
- **Existing CLAUDE.md TeamGenesis section:** Ask continue/update/new

---

## Examples

### Minimal input
"Create an agent team for my Node.js CLI tool deploy-cli"
→ Ask for goal, spec file, stack. Generate: Architect, Implementer, Critic, CLI Specialist. Append to CLAUDE.md.

### Full input
"Team for TaskFlow, Node 20/TS/Express/PostgreSQL, goal: task management with auth, spec: PROJECT_BRIEF.md"
→ Skip questions. Generate immediately. Append to CLAUDE.md.

### Reuse
"Add notifications to TaskFlow"
→ Detect `## Team Governance` in CLAUDE.md. Ask continue/update/new. Preserve agent state.

---

## Reference Floor

Minimum quality bar for a compact agent definition:

```markdown
#### Architect
- **DoD:** Prisma schema has all models with types, relations, indexes · TypeScript types importable without modification · No `any` in shared types · Every model has example JSON in comments
- **Stop if:** Spec ambiguous on data relationship · Schema change would break approved code · Security affected by schema decision
- **Tests:** Unit: type guards in `src/types/` · Integration: Prisma migration + seed
```

Minimum quality bar for a contract row:

```
| Architect | Implementer | Prisma schema + TS types | `prisma/schema.prisma` + `src/types/*.ts` — Implementer consumes directly, deviation requires RFC |
```

Every agent definition and contract in the output should meet or exceed this specificity.
