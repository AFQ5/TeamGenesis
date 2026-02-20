# TeamGenesis

A Claude Code **plugin** that generates compact **Agent Team governance** directly into `CLAUDE.md` — auto-loaded every session, zero setup.

When you spin up a multi-agent team, agents drift, guess, overbuild, and lack clear boundaries. TeamGenesis generates a compact governance block (< 80 lines) with roles, contracts, rules, and a state table that agents update as they work.

## How It Works

1. Run `/team-genesis` and provide your project details
2. TeamGenesis appends a `## Team Governance` section to your `CLAUDE.md`
3. Claude Code auto-loads it — agents see the rules immediately
4. Agents update the **Agent State** table during work
5. Next session: everything persists, no setup needed

## What It Generates

**`CLAUDE.md`** (committed, auto-loaded) — compact governance block containing:
- **Team Roster** — agents + one-line role each
- **Handoff Contracts** — typed input/output per agent pair
- **Governance Rules** — compact, with inline anti-pattern rationale
- **Agent State** — live table agents update after every task

**`CLAUDE.local.md`** (private, never committed) — session scratch space:
- Draft decisions not ready to share
- Personal session notes
- Mid-thought work

## Installation

### As a plugin (recommended)

```bash
/plugin marketplace add AFQ5/TeamGenesis
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

## Usage

```
/team-genesis
```

Provide: **Project Name**, **Goal**, **Tech Stack**, and **Source of Truth file**.

Optional: Security invariants, custom roles.

### Agent Reuse

Run `/team-genesis` again — it detects the existing `## Team Governance` section in `CLAUDE.md` and asks:
- **Continue** — same agents, resume work
- **Update** — same agents, new goals, state preserved
- **New** — regenerate from scratch

## Plugin Structure

```
TeamGenesis/
├── .claude-plugin/
│   └── plugin.json
├── skills/
│   └── team-genesis/
│       ├── SKILL.md                       # Skill instructions
│       └── references/
│           └── project-type-profiles.md   # Core roles + specialist examples
├── README.md
└── LICENSE
```

## License

MIT — see [LICENSE](LICENSE).
