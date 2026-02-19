# TeamGenesis

A Claude Code **plugin** that generates **Agent Team Prompts** — the governance documents that coordinate multiple AI agents working on a single project.

When you spin up a multi-agent team, agents drift, guess, overbuild, and lack clear boundaries. TeamGenesis generates a structured prompt that prevents this — every agent gets explicit scope, a Definition of Done, Stop Conditions, and interface contracts with other agents.

## What It Generates

Two files with distinct purposes:

| File | Purpose | Used when |
|---|---|---|
| `AGENT_TEAM_PROMPT.md` | **Sync file** — governance rules agents follow during work | Every session |
| `TEAM_AGENTS.json` | **Memory file** — team identity + history for reload | Between sessions |

The sync file contains:
1. **Project Context** — Mission boundary, stack, source of truth
2. **Source of Truth Rule** — Authority hierarchy (the spec overrides all assumptions)
3. **Team Roster** — Agents with responsibilities and "Must NOT" boundaries
4. **Responsibility Blocks** — Per-agent scope, deliverables, Definition of Done, Stop Conditions
5. **Interface Contracts** — Exact I/O schemas between agents with concrete examples
6. **Approval Protocol** — No code before plan approval
7. **Escalation Protocol** — What happens after an agent halts, including arbitration rules
8. **Operational Constraints** — Security invariants, dependency policy, simplicity rules
9. **Agent Persistence** — Save/reload instructions for `TEAM_AGENTS.json`

Plus: Change Control (RFC process), Documentation Sync, Budget Constraints, Drift Detection, and Two-Layer Prompting architecture.

## Installation

### As a plugin (recommended)

```bash
# Add the marketplace (one-time)
/plugin marketplace add AFQ5/TeamGenesis

# Install the plugin
/plugin install team-genesis
```

Or from the CLI:

```bash
claude plugin install team-genesis@AFQ5/TeamGenesis
```

### Local development / testing

```bash
git clone https://github.com/AFQ5/TeamGenesis.git
claude --plugin-dir ./TeamGenesis
```

### Legacy: as a standalone skill

```bash
# User-level (all projects)
git clone https://github.com/AFQ5/TeamGenesis.git ~/.claude/skills/team-genesis

# Project-level
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
- **Custom Roles** (optional — override dynamically selected specialist roles)

## Agent Reuse

TeamGenesis saves your agents after each session. When you run `/team-genesis` again, it detects `TEAM_AGENTS.json` and asks:

- **Continue** — Reload the same agents, pick up where they left off
- **Update** — Same agents, new goals (e.g., add a feature), prior work preserved
- **New team** — Generate from scratch, archive existing team

## Any Project Domain

TeamGenesis works with **any project type**. The 3 core roles (Architect, Implementer, Critic) are always included, and 1-3 specialist roles are dynamically generated based on your project's description, tech stack, and goals.

You can also define **custom roles** — TeamGenesis applies the same governance structure (DoD, Stop Conditions, interface contracts, boundaries) to any role you specify.

## Plugin Structure

```
TeamGenesis/
├── .claude-plugin/
│   └── plugin.json                        # Plugin manifest
├── skills/
│   └── team-genesis/
│       ├── SKILL.md                       # Skill instructions + template + governance
│       └── references/
│           └── project-type-profiles.md   # Core roles + specialist role examples
├── README.md
└── LICENSE
```

## License

MIT — see [LICENSE](LICENSE).
