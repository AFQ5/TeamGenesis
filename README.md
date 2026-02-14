# TeamGenesis

A Claude Code skill that generates enterprise-grade **Agent Team Prompts** with governance architecture, anti-drift boundaries, approval gates, and interface contracts.

When you spin up a multi-agent team, agents drift, guess, overbuild, and lack clear boundaries. TeamGenesis generates a structured prompt that prevents this — every agent gets explicit scope, a Definition of Done, Stop Conditions, and interface contracts with other agents.

## What It Generates

A complete Agent Team Prompt following the **8-Point Governance Architecture**:

1. **Project Context** — Mission boundary, stack, source of truth
2. **Source of Truth Rule** — Authority hierarchy (the spec overrides all assumptions)
3. **Team Roster** — Agents with responsibilities and "Must NOT" boundaries
4. **Responsibility Blocks** — Per-agent scope, deliverables, Definition of Done, Stop Conditions
5. **Approval Protocol** — No code before plan approval
6. **Interface Contracts** — Exact I/O schemas between agents with concrete examples
7. **Operational Constraints** — Security invariants, dependency policy, simplicity rules
8. **Session Completion Log** — Per-agent work summaries (JSON/Markdown) so the team remembers what was done

Plus: Change Control (RFC process), Documentation Sync, Budget Constraints, and Two-Layer Prompting architecture.

See [`examples/example-output.md`](examples/example-output.md) for a complete generated example.

## Installation

Clone this repo into your Claude Code skills directory:

```bash
git clone https://github.com/AFQ5/TeamGenesis.git ~/.claude/skills/team-genesis
```

Or for project-level skills:

```bash
git clone https://github.com/AFQ5/TeamGenesis.git .claude/skills/team-genesis
```

## Usage

In Claude Code, run:

```
/team-genesis
```

Then provide:
- **Project Name** and **Primary Goal** (required)
- **Tech Stack** (required)
- **Source of Truth file** (required — e.g., `PROJECT_BRIEF.md`)
- **Security Invariants** (optional)
- **Project Type** (optional — auto-detected from stack)
- **Custom Roles** (optional — override default agent roles)
- **Session Log Format** (optional — `JSON`, `Markdown`, or `Both`; default: `JSON`)

The skill writes the generated prompt to `./AGENT_TEAM_PROMPT.md` (configurable).

## Supported Project Types

| Type | Default Agents |
|------|---------------|
| **web-app** | Architect, Implementer, Critic, Frontend Specialist, DevOps |
| **api-service** | Architect, Implementer, Critic, API Designer, DevOps |
| **cli-tool** | Architect, Implementer, Critic, CLI/DX Specialist |
| **library** | Architect, Implementer, Critic, API Designer, DX/Documentation |
| **data-pipeline** | Architect, Implementer, Critic, Data Engineer, DevOps |

You can also define **custom roles** — TeamGenesis applies the same governance structure (DoD, Stop Conditions, interface contracts, boundaries) to any role you specify.

## Project Structure

```
team-genesis/
├── SKILL.md                          # Core skill (frontmatter + instructions)
├── references/
│   ├── governance-architecture.md    # 8-Point Architecture + Quality Multipliers
│   ├── prompt-template.md            # Output template with placeholders
│   └── project-type-profiles.md      # Role configs per project type
└── examples/
    └── example-output.md             # Complete generated example (TaskFlow API)
```

## License

MIT — see [LICENSE](LICENSE).
