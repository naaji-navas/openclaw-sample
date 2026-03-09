# OpenClaw — Autonomous Project Management

## Philosophy

**CEO Pattern**: The main session is the CEO. It sets strategy, spawns PMs, and reviews outcomes.
It does NOT execute work. All execution is delegated to subagents (PMs).

## Architecture

```
User ↔ Main Agent (Coordinator)
            │
            ├─→ PM-frontend  ──→ STATE.yaml
            ├─→ PM-backend   ──→ STATE.yaml
            ├─→ PM-content   ──→ STATE.yaml
            └─→ PM-devops    ──→ STATE.yaml
```

- **Main Agent**: Receives user intent, decomposes into projects, spawns/routes to PMs
- **PM Subagents**: Own a project scope, maintain their STATE.yaml, execute autonomously
- **STATE.yaml**: Single source of truth per project — all coordination happens through files

## PM Delegation Workflow

```
1. User sends task
2. Main agent checks PROJECT_REGISTRY.yaml
3. If PM exists for scope → route task to existing PM
4. If no PM exists → spawn new PM subagent
5. PM reads/creates STATE.yaml → breaks work into tasks
6. PM executes tasks autonomously, updating STATE.yaml
7. PM commits changes to git on meaningful progress
8. Main agent checks in periodically → reviews state, adjusts priorities
9. Main agent summarizes outcome to user
```

## Rules

### Main Agent (Coordinator)
- **Max 2-3 tool calls per user message** (spawn, send, read state)
- NEVER write code directly — delegate everything
- Read PROJECT_REGISTRY.yaml before spawning duplicates
- Summarize PM status to user in plain language
- Adjust priorities by editing STATE.yaml or messaging PMs

### PM Subagents
- Own their project's `projects/{project-name}/STATE.yaml`
- Break large tasks into subtasks with clear IDs
- Update STATE.yaml status after each meaningful step
- Commit to git after completing a task (not after every line)
- Can spawn sub-subagents for parallel subtasks
- Report blockers by setting `status: blocked` with `blocked_by` field
- Never work outside their assigned project scope

### STATE.yaml Protocol
- PMs **read** STATE.yaml at start to understand current state
- PMs **write** STATE.yaml after completing or starting tasks
- Tasks use lifecycle: `pending` → `in_progress` → `done` | `blocked` | `cancelled`
- `next_actions` section lists what should happen next (human-readable)
- `blocked_by` references other task IDs for dependency tracking

## Label Conventions

```
pm-{project}-{scope}

Examples:
  pm-auth-refactor      → Auth module refactor PM
  pm-website-frontend   → Website frontend PM
  pm-api-migration      → API migration PM
  pm-docs-update        → Documentation PM
```

## File Structure

```
openclaw/
├── AGENTS.md                    # This file — agent behavior guide
├── PROJECT_REGISTRY.yaml        # Active projects and their PMs
├── projects/
│   ├── .templates/
│   │   └── STATE.yaml           # Template for new projects
│   ├── {project-name}/
│   │   ├── STATE.yaml           # Project coordination state
│   │   └── ...                  # Project work artifacts
├── prompts/
│   ├── coordinator.md           # Main agent system prompt
│   ├── pm-general.md            # General PM subagent prompt
│   └── pm-specialized.md        # Domain-specific PM prompts
└── .cursor/
    └── rules/
        └── openclaw.mdc         # Cursor rule for agent behavior
```

## Quick Start

1. User describes a task or project
2. Coordinator reads `PROJECT_REGISTRY.yaml`
3. Coordinator spawns PM: `Task(prompt="...", description="pm-{name}")`
4. PM creates `projects/{name}/STATE.yaml`
5. PM works through tasks, committing progress
6. Coordinator checks state, reports to user

## Anti-Patterns

- **Don't**: Have main agent write code → spawn a PM instead
- **Don't**: Skip STATE.yaml updates → state drift causes confusion
- **Don't**: Spawn duplicate PMs → always check registry first
- **Don't**: Let PMs work across project boundaries → scope creep
- **Don't**: Forget git commits → lose audit trail
