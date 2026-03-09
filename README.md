# OpenClaw

Decentralized autonomous project management for AI coding agents.

OpenClaw implements a **CEO pattern** where the main agent acts as a coordinator — it sets strategy and delegates all execution to PM (Project Manager) subagents. Agents coordinate through shared `STATE.yaml` files instead of a central orchestrator, enabling parallel autonomous work with zero bottlenecks.

## How It Works

```
User ↔ Coordinator (main agent)
            │
            ├─→ PM-frontend  ──→ STATE.yaml
            ├─→ PM-backend   ──→ STATE.yaml
            └─→ PM-devops    ──→ STATE.yaml
```

1. You describe a task
2. Coordinator checks `PROJECT_REGISTRY.yaml` for existing PMs
3. Spawns a scoped PM subagent (or routes to an existing one)
4. PM creates `projects/{name}/STATE.yaml`, breaks work into tasks
5. PM works autonomously — updating state, committing to git
6. You check in anytime — coordinator reads state and summarizes

## Core Idea

**STATE.yaml > orchestrator.** File-based coordination scales better than message-passing. Every state change is git-committed, giving you a full audit trail for free.

## Project Structure

```
openclaw/
├── AGENTS.md                    # Architecture & agent behavior rules
├── PROJECT_REGISTRY.yaml        # Active projects & their PMs
├── projects/
│   ├── .templates/
│   │   └── STATE.yaml           # Template for new projects
│   └── {project-name}/
│       └── STATE.yaml           # Per-project coordination state
├── prompts/
│   ├── coordinator.md           # Main agent (CEO) prompt
│   ├── pm-general.md            # Base PM subagent prompt
│   └── pm-specialized.md        # Domain-specific PM extensions
└── .cursor/
    └── rules/
        └── openclaw.mdc         # Auto-applied Cursor rule
```

## STATE.yaml

The single source of truth per project:

```yaml
project: "auth-refactor"
pm_label: "pm-auth-refactor"

tasks:
  - id: extract-middleware
    status: done
    output: "src/middleware/auth.ts"

  - id: update-routes
    status: in_progress
    notes: "Migrating 12 route handlers"

  - id: write-tests
    status: blocked
    blocked_by: update-routes

next_actions:
  - "pm-auth-refactor: Finish route migration, then unblock tests"
```

Tasks follow a simple lifecycle: `pending` → `in_progress` → `done` | `blocked` | `cancelled`

## Key Principles

- **Thin coordinator** — 2-3 tool calls max per interaction, all execution delegated
- **File-based coordination** — STATE.yaml is the only communication channel
- **Git as audit log** — every state change is committed
- **Scoped ownership** — PMs stay within their project boundary
- **No orchestrator bottleneck** — agents self-organize through shared state

## Inspired By

[Nicholas Carlini's approach](https://nicholas.carlini.com/) to autonomous coding agents — let agents self-organize rather than micromanaging them.

## License

MIT
