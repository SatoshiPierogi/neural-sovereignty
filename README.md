# Neural Sovereignty

A multi-agent AI operating system that runs on your machine. Five specialized agents — Scout, Forge, Builder, Trader, and Ops — coordinate through structured workflows to turn market signals into content, code, and trades. One human stays in control. Every agent earns its seat.

## What This Does

Neural Sovereignty gives you a team of AI agents, each with a defined role, persistent memory, and strict operating rules. You issue a task once. The agents decompose it, execute it, verify each other's work, and deliver a result — without losing context between sessions.

The system sits on top of [OpenClaw](https://docs.openclaw.ai) and uses [Antfarm](antfarm/) as its workflow engine.

## Architecture

```
┌──────────────────────────────────────────────────┐
│                    OPS (Router)                   │
│  Routes tasks · Tracks costs · Monitors health   │
├──────────┬──────────┬──────────┬─────────────────┤
│  SCOUT   │  FORGE   │  TRADER  │     BUILDER     │
│  Market  │  Content │  Trade   │     Code &      │
│  Intel   │  Engine  │  Desk    │     Deploy      │
└──────────┴──────────┴──────────┴─────────────────┘
        ↕               ↕              ↕
   ┌─────────┐    ┌──────────┐   ┌──────────┐
   │ Antfarm │    │  Memory  │   │  Skills  │
   │ Engine  │    │  System  │   │  Layer   │
   └─────────┘    └──────────┘   └──────────┘
```

### The Agents

| Agent | Role | What It Does |
|-------|------|-------------|
| **Scout** | Market intelligence | Monitors AIXBT, Nansen, Elfa AI, and Blockchair. Extracts signals. Scores them 1–10. Routes high-confidence signals to Forge and Trader. |
| **Forge** | Content production | Takes Scout signals and turns them into posts, threads, articles, and video scripts. Follows your brand voice rules. |
| **Trader** | Trade execution | Receives Scout signals. Structures trade proposals with entry, stop, target, and thesis. Executes only after human approval. Never trades autonomously. |
| **Builder** | Code and deployment | Builds onchain applications, deploys smart contracts, develops OpenClaw skills. Runs Antfarm workflows for feature development, bug fixes, and security audits. |
| **Ops** | Task routing and oversight | Routes requests to the right agent. Tracks token costs per agent per day. Escalates failures. Maintains audit logs. |

### How Ops Routes Work

- Market intelligence → Scout
- Content production → Forge
- Trade signals and execution → Trader
- Code, product, and deployment → Builder
- System health, scheduling, analytics → Ops handles directly

### Escalation Rules

- Scout signal strength ≥ 8 → auto-routes to both Forge and Trader
- Trader confidence ≥ 7 → flags for human review (no auto-execution)
- Builder PR fails tests → triggers bug-fix workflow
- Any agent errors 3x consecutively → alerts human and pauses that agent

## Workflow Engine (Antfarm)

[Antfarm](antfarm/) orchestrates multi-agent workflows using YAML definitions, SQLite state tracking, and cron-based polling. Three workflows ship out of the box:

| Workflow | Agents | Pipeline |
|----------|--------|----------|
| **feature-dev** | 7 | plan → setup → implement → verify → test → PR → review |
| **security-audit** | 7 | scan → prioritize → setup → fix → verify → test → PR |
| **bug-fix** | 6 | triage → investigate → setup → fix → verify → PR |

Each step runs in a fresh agent session with clean context. Agents verify each other's output. Failed steps retry automatically and escalate to the human if retries exhaust.

**Core design decisions:**
- YAML + SQLite + cron. No Redis, Kafka, or container orchestrator.
- Each agent runs in isolation with its own workspace.
- Story-based execution: large tasks decompose into ordered user stories, each implemented and verified independently.
- Built on the [Ralph loop](https://github.com/snarktank/ralph) pattern for autonomous agent execution.

## Memory System

Three-layer architecture gives agents persistent recall across sessions:

| Layer | Location | Purpose |
|-------|----------|---------|
| **Knowledge Graph** | `~/life/` | Entity-based storage using PARA. Each entity has `summary.md` (quick context) and `items.json` (atomic facts). |
| **Daily Notes** | `memory/YYYY-MM-DD.md` | Raw chronological timeline. Written continuously during conversations. |
| **Tacit Knowledge** | `MEMORY.md` | Facts about the user, not the world. Communication preferences, workflow habits, hard rules. |

**Key rules:**
- Facts never get deleted — they get superseded.
- `summary.md` is disposable (rewritten weekly). `items.json` is the source of truth.
- Memory decays: hot (7 days) → warm (8–30 days) → cold (30+ days). Accessing a cold fact reheats it.
- Heartbeat polls run memory extraction and maintenance automatically.

## Skills

Skills extend agent capabilities with tools and scripts.

| Skill | Description |
|-------|-------------|
| **exa-search** | Neural/semantic web search via Exa.ai. Better than keyword search for conceptual queries and research. |

Add new skills to `skills/` with a `SKILL.md` and supporting scripts.

## Directory Structure

```
neural-sovereignty/
├── AGENTS.md          # Workspace operating manual
├── SOUL.md            # Agent identity and behavioral rules
├── IDENTITY.md        # Agent name, vibe, emoji (filled per instance)
├── BOOTSTRAP.md       # First-run onboarding script
├── HEARTBEAT.md       # Periodic task checklist
├── MEMORY.md          # Long-term user knowledge
├── USER.md            # Human profile and preferences
├── TOOLS.md           # Environment-specific notes
│
├── antfarm/           # Workflow engine (TypeScript CLI, v0.5.1)
│   ├── src/           # CLI, database, installer, dashboard server
│   ├── workflows/     # feature-dev, bug-fix, security-audit
│   └── landing/       # Marketing site
│
├── builder/           # Builder agent workspace
├── forge/             # Forge agent workspace
├── ops/               # Ops agent workspace
├── scout/             # Scout agent workspace
├── trader/            # Trader agent workspace
│
├── skills/            # Skill definitions
│   └── exa-search/    # Semantic web search
│
└── memory/            # Daily notes
```

## Requirements

- [OpenClaw](https://github.com/openclaw/openclaw) v2026.2.9+
- Node.js ≥ 22
- `gh` CLI (for PR creation workflows)

## Quick Start

1. Install OpenClaw and configure your workspace.
2. Clone this repository into your OpenClaw workspace directory.
3. Install Antfarm:
   ```bash
   cd antfarm && npm install && npm run build && npm link
   antfarm install
   ```
4. Fill in `IDENTITY.md`, `USER.md`, and `MEMORY.md` with your details.
5. Customize agent `CONTEXT.md` files in each agent workspace directory.
6. Run a workflow:
   ```bash
   antfarm workflow run feature-dev "Add user authentication with OAuth"
   ```

## Security

- Agents **never** execute `sudo` or escalate privileges.
- Agents **never** share API keys, tokens, or credentials.
- Agents **never** send messages without explicit human approval.
- Agents **never** make purchases or financial transactions.
- Trader **never** executes trades without human approval.
- All code passes through security review before deployment.
- All workflows are plain YAML and Markdown — fully auditable.

## License

[MIT](antfarm/LICENSE) (Antfarm engine)
