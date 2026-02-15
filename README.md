# TeamGenesis

A Claude Code **plugin** that generates enterprise-grade **Agent Team Prompts** with governance architecture, anti-drift boundaries, approval gates, interface contracts, and agent persistence.

When you spin up a multi-agent team, agents drift, guess, overbuild, and lack clear boundaries. TeamGenesis generates a structured prompt that prevents this — every agent gets explicit scope, a Definition of Done, Stop Conditions, and interface contracts with other agents. When the session ends, agents are saved to `TEAM_AGENTS.json` so you can reload the same team later.

## What It Generates

A complete Agent Team Prompt following the **8-Point Governance Architecture**:

1. **Project Context** — Mission boundary, stack, source of truth
2. **Source of Truth Rule** — Authority hierarchy (the spec overrides all assumptions)
3. **Team Roster** — Agents with responsibilities and "Must NOT" boundaries
4. **Responsibility Blocks** — Per-agent scope, deliverables, Definition of Done, Stop Conditions
5. **Approval Protocol** — No code before plan approval
6. **Interface Contracts** — Exact I/O schemas between agents with concrete examples
7. **Operational Constraints** — Security invariants, dependency policy, simplicity rules
8. **Agent Persistence** — Save agents to `TEAM_AGENTS.json` (roles, prompts, goals, completed work) for reuse

Plus: Change Control (RFC process), Documentation Sync, Budget Constraints, and Two-Layer Prompting architecture.

See [`skills/team-genesis/examples/example-output.md`](skills/team-genesis/examples/example-output.md) for a complete generated example.

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
- **Project Type** (optional — auto-detected from stack)
- **Custom Roles** (optional — override default agent roles)

The plugin writes the generated prompt to `./AGENT_TEAM_PROMPT.md` and saves agents to `./TEAM_AGENTS.json`.

## Agent Reuse

TeamGenesis saves your agents after each session. When you run `/team-genesis` again, it detects `TEAM_AGENTS.json` and asks:

- **Continue** — Reload the same agents, pick up where they left off
- **Update** — Same agents, new goals (e.g., add a feature), prior work preserved
- **New team** — Generate from scratch, archive existing team

Each agent's JSON snapshot includes their role, purpose, scope, boundaries, goals, interface contracts, completed work, files touched, and blockers — everything needed to resume without context loss.

## Supported Project Types

| Type | Default Agents |
|------|---------------|
| **web-app** | Architect, Implementer, Critic, Frontend Specialist, DevOps |
| **api-service** | Architect, Implementer, Critic, API Designer, DevOps |
| **cli-tool** | Architect, Implementer, Critic, CLI/DX Specialist |
| **library** | Architect, Implementer, Critic, API Designer, DX/Documentation |
| **data-pipeline** | Architect, Implementer, Critic, Data Engineer, DevOps |

You can also define **custom roles** — TeamGenesis applies the same governance structure (DoD, Stop Conditions, interface contracts, boundaries) to any role you specify.

## Plugin Structure

```
TeamGenesis/
├── .claude-plugin/
│   └── plugin.json                       # Plugin manifest
├── skills/
│   └── team-genesis/
│       ├── SKILL.md                      # Core skill (frontmatter + instructions)
│       ├── references/
│       │   ├── governance-architecture.md  # 8-Point Architecture + Quality Multipliers
│       │   ├── prompt-template.md          # Output template with placeholders
│       │   └── project-type-profiles.md    # Role configs per project type
│       └── examples/
│           └── example-output.md           # Complete generated example (TaskFlow API)
├── README.md
└── LICENSE
```

## License

MIT — see [LICENSE](LICENSE).
