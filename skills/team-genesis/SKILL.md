---
name: team-genesis
description: "Generate compact Agent Team governance into CLAUDE.md — auto-loaded every session, zero setup. Agents update their state in the same file. Use when creating a new multi-agent team, setting up agent roles, or starting a project that needs structured AI agent collaboration. Trigger phrases: create agent team, set up team prompt, generate team charter, new agent team, team genesis, spin up agents, agent coordination."
tags: [agents, team, governance, architecture, multi-agent, coordination]
---

# TeamGenesis: Agent Team Architecture Generator

You generate compact Agent Team governance directly into `CLAUDE.md` and `CLAUDE.local.md`. Claude Code auto-loads both at session start — zero setup, zero manual referencing.

- **`CLAUDE.md`** — Committed to repo. Team roster, contracts, governance rules, agent state. < 80 lines.
- **`CLAUDE.local.md`** — Private, never committed (Claude Code auto-gitignores it). Session notes, draft decisions, mid-thought work.

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
1. Read Agent State table — check for blockers and completed work
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

Generate governance into two files. `CLAUDE.md` must stay **under 80 lines** — it loads every session.

### CLAUDE.md — Team Governance Block

Must contain exactly these sections in order:

```markdown
## Team Governance
<!-- TeamGenesis v1.0.0 | {DATE} | {PROJECT_NAME} -->

### Team Roster
| Agent | Role |
|---|---|
| Architect | System design, schemas, interface contracts |
| Implementer | Production code per approved plans |
| Critic | Plan review, quality gates, drift checks |
| {Specialist} | {one-line role description} |

### Handoff Contracts
| From | To | Input | Output |
|---|---|---|---|
| Architect | Implementer | Prisma schema + TS types (`src/types/`) | Implementation follows schema; deviation requires RFC |
| Implementer | Critic | Plan doc (steps, files, schemas, edge cases) | Pass/fail per section + specific feedback |
| {Agent} | {Agent} | {typed input artifact} | {typed output artifact} |

### Governance Rules
`{SOURCE_OF_TRUTH}` is law. Authority: spec > approved plans > this charter > agent judgment.

1. **Plan before code.** Plans: steps, files, schemas, edge cases, tests, rollback. Critic approves. _(Without this gate, agents write code from their interpretation, then discover conflicts at integration — 10x fix cost.)_
2. **Stay in scope.** Touch only your owned files. Cross-boundary changes require RFC. _(Without boundaries, agents silently write the same files — the #1 cause of agent team corruption.)_
3. **Contracts are binding.** Typed interfaces between agent pairs. _(Without contracts, two agents assume different schemas — invisible until integration fails.)_
4. **Security: {invariants}.** Violation = halt and escalate.
5. **Drift check.** Critic re-reads spec every 2-3 tasks. Flag scope creep, gaps, violations.
6. **Log decisions** in this file: context, alternatives, chosen approach, consequences. _(Unlogged decisions get undone by later agents who don't know why things were built that way.)_
7. **Escalation → user.** Format: decision needed, options, recommendation, blocked work.
8. **After completing any task, update your row in the Agent State table before handing off.**

### Agent State
<!-- Agents: update this table after every completed task -->
| Agent | Current Task | Last Output | Status |
|---|---|---|---|
| Architect | - | - | idle |
| Implementer | - | - | idle |
| Critic | - | - | idle |
| {Specialist} | - | - | idle |
```

### CLAUDE.local.md — Private Session State

Generate a starter template:

```markdown
# Session Notes (private — not committed)

Use CLAUDE.local.md when:
- A decision is still in draft (not yet final)
- Context is personal or session-specific
- Thinking is mid-thought and not ready to commit

Promote to CLAUDE.md only when decided.

## Draft Decisions

## Scratch Space
```

### Quality Floors

Even in compact format, every element must meet these minimums:
- **Boundaries:** Concrete actions (`Must NOT write implementation code`), not vague (`Must NOT overstep`)
- **DoD items:** Binary checks (`All unit tests passing`), not subjective (`code is clean`)
- **Contracts:** Real artifact with format (`Prisma schema + TS types in src/types/`), not abstract (`data exchange`)
- **Stop conditions:** Explicit trigger + action (`if spec ambiguous on data relationship, escalate to user`)

### Governance Scaling

| Scope | Agents | Contracts | CLAUDE.md approach |
|---|---|---|---|
| **Small** (1-2 features) | 3-4 | 2-4 | Everything in CLAUDE.md (< 60 lines) |
| **Medium** (3-5 features) | 4-5 | 5-8 | Everything in CLAUDE.md (< 80 lines) |
| **Large** (5+ features) | 5-6 | 8-12 | CLAUDE.md stays < 80 lines; push detailed contract specs to `.claude/rules/` if needed |

For large projects: if contract detail would push CLAUDE.md over 80 lines, move the full contract definitions into `.claude/rules/contracts.md` and keep only the summary table in CLAUDE.md. If the user flags contracts as private, use `.claude/rules/` (never committed) instead of committed files.

---

## Step 3.5: Self-Validate

Before writing, verify:
- [ ] CLAUDE.md has exactly: Team Roster → Handoff Contracts → Governance Rules → Agent State (in order)
- [ ] Every agent has DoD with ≥3 binary items in their definition
- [ ] Every agent has ≥2 stop conditions
- [ ] Every contract row specifies typed input AND typed output
- [ ] Security invariants in both Rules AND relevant agent boundaries
- [ ] Rule 8 (update Agent State after task completion) is present
- [ ] Source of Truth referenced in governance rules
- [ ] Total CLAUDE.md governance block ≤ 80 lines

---

## Step 4: Output

1. **Read existing `CLAUDE.md`** if present. Preserve all non-TeamGenesis content.
2. **Append** (or replace) the `## Team Governance` section.
3. **Create `CLAUDE.local.md`** with the private session template (only if it doesn't exist — never overwrite).
4. **Show summary:** team composition, lines added to CLAUDE.md, reminder that agents update the State table during work.

No other files are generated. No JSON. No separate prompt files.

---

## Error Handling

- **Missing required inputs:** Ask specifically for what's missing
- **Missing optional inputs:** Use defaults
- **Ambiguous roles:** Ask to clarify
- **Conflicting roles:** Flag overlap, ask resolution
- **No Source of Truth file:** Offer to create template
- **Existing governance section in CLAUDE.md:** Ask continue/update/new
- **CLAUDE.md would exceed 80 lines:** Move contract detail to `.claude/rules/`

---

## Examples

### Minimal input
"Create an agent team for my Node.js CLI tool deploy-cli"
→ Ask for goal, spec file, stack. Generate: Architect, Implementer, Critic, CLI Specialist. Write to CLAUDE.md + CLAUDE.local.md.

### Full input
"Team for TaskFlow, Node 20/TS/Express/PostgreSQL, goal: task management with auth, spec: PROJECT_BRIEF.md"
→ Skip questions. Generate immediately. Append to CLAUDE.md.

### Reuse
"Add notifications to TaskFlow"
→ Detect `## Team Governance` in CLAUDE.md. Ask continue/update/new. Preserve agent state.

### Large project overflow
"Team for a microservices platform with 8 services, shared auth, event bus"
→ CLAUDE.md gets the summary tables (< 80 lines). Full contract specs go to `.claude/rules/contracts.md`.

---

## Reference Floor

Minimum quality bar for a contract row:

```
| Architect | Implementer | Prisma schema + TS types | `prisma/schema.prisma` + `src/types/*.ts` — Implementer consumes directly, deviation requires RFC |
```

Minimum quality bar for a governance rule:

```
2. **Stay in scope.** Touch only your owned files. Cross-boundary changes require RFC. _(Without boundaries, agents silently write the same files — the #1 cause of agent team corruption.)_
```

Every element in the generated output should meet or exceed this specificity.
