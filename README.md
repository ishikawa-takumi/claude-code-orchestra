# claude-code-orchestra

![Claude Code Orchestra](./summary.png)

Multi-Agent AI Development Environment

```
Claude Code (Orchestrator) ─┬─ Codex CLI (Deep Reasoning)
                            ├─ Gemini CLI (Research)
                            └─ Subagents (Parallel Tasks)
```

## Quick Start

Run at the root of an existing project:

```bash
git clone --depth 1 https://github.com/DeL-TaiseiOzaki/claude-code-orchestra.git .starter && cp -r .starter/.claude .starter/.codex .starter/.gemini .starter/CLAUDE.md . && rm -rf .starter && claude
```

## Prerequisites

### Claude Code

```bash
npm install -g @anthropic-ai/claude-code
claude login
```

### Codex CLI

```bash
npm install -g @openai/codex
codex login
```

### Gemini CLI

```bash
npm install -g @google/gemini-cli
gemini login
```

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│           Claude Code (Orchestrator)                        │
│           → Context conservation is the top priority        │
│           → Handles user interaction, coordination, execution│
│                      ↓                                      │
│  ┌───────────────────────────────────────────────────────┐  │
│  │              Subagent (general-purpose)               │  │
│  │              → Has its own isolated context           │  │
│  │              → Can call Codex/Gemini                  │  │
│  │              → Summarizes results back to main        │  │
│  │                                                       │  │
│  │   ┌──────────────┐        ┌──────────────┐           │  │
│  │   │  Codex CLI   │        │  Gemini CLI  │           │  │
│  │   │  design/reasoning │   │  research    │           │  │
│  │   │  debugging   │        │  multimodal  │           │  │
│  │   └──────────────┘        └──────────────┘           │  │
│  └───────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

### Context Management (Important)

To conserve the main orchestrator's context, tasks expected to produce large output should run via subagents.

| Situation | Recommended Approach |
|------|----------|
| Large output expected | via subagent |
| Short questions/short answers | direct call OK |
| Codex/Gemini consultation | via subagent |
| Detailed analysis needed | via subagent → save to file |

## Directory Structure

```
.
├── CLAUDE.md                    # main system documentation
├── README.md
├── pyproject.toml               # Python project settings
├── uv.lock                      # dependency lockfile
│
├── .claude/
│   ├── agents/
│   │   └── general-purpose.md   # subagent configuration
│   │
│   ├── skills/                  # reusable workflows
│   │   ├── startproject/        # project kickoff
│   │   ├── plan/                # implementation planning
│   │   ├── tdd/                 # test-driven development
│   │   ├── checkpointing/       # session persistence
│   │   ├── codex-system/        # Codex CLI integration
│   │   ├── gemini-system/       # Gemini CLI integration
│   │   └── ...
│   │
│   ├── hooks/                   # automation hooks
│   │   ├── agent-router.py      # agent routing
│   │   ├── lint-on-save.py      # auto-lint on save
│   │   └── ...
│   │
│   ├── rules/                   # development guidelines
│   │   ├── coding-principles.md
│   │   ├── testing.md
│   │   └── ...
│   │
│   ├── docs/
│   │   ├── DESIGN.md            # design decision log
│   │   ├── research/            # Gemini research results
│   │   └── libraries/           # library constraints
│   │
│   └── logs/
│       └── cli-tools.jsonl      # Codex/Gemini I/O logs
│
├── .codex/                      # Codex CLI settings
│   ├── AGENTS.md
│   └── config.toml
│
└── .gemini/                     # Gemini CLI settings
    ├── GEMINI.md
    └── settings.json
```

## Skills

### `/startproject` — Project Kickoff

Start a project with multi-agent collaboration.

```
/startproject user-authentication
```

**Workflow:**
1. **Gemini** → repository analysis and pre-research
2. **Claude** → requirements discovery and plan creation
3. **Codex** → plan review and risk analysis
4. **Claude** → task list creation

### `/plan` — Implementation Plan

Break requirements into concrete steps.

```
/plan add-api-endpoint
```

**Output:**
- implementation steps (files, changes, verification)
- dependencies and risks
- acceptance criteria

### `/tdd` — Test-Driven Development

Implement using the Red-Green-Refactor cycle.

```
/tdd user-registration
```

**Workflow:**
1. test case design
2. write failing tests (Red)
3. minimal implementation (Green)
4. refactor (Refactor)

### `/checkpointing` — Session Persistence

Save the session state.

```bash
/checkpointing              # basic: history log
/checkpointing --full       # full: includes git history and file changes
/checkpointing --analyze    # analysis: discover reusable skill patterns
```

### `/codex-system` — Codex CLI Integration

Use for design decisions, debugging, and trade-off analysis.

**Trigger examples:**
- "How should I design this?" "How should I implement this?"
- "Why isn't it working?" "I'm getting an error"
- "Which is better?" "Compare these"

### `/gemini-system` — Gemini CLI Integration

Use for research, large-scale analysis, and multimodal processing.

**Trigger examples:**
- "Look this up" "Do research"
- "Review this PDF/video"
- "Understand the entire codebase"

### `/simplify` — Code Refactoring

Simplify code and improve readability.

### `/design-tracker` — Design Decision Tracking

Automatically record architecture and implementation decisions.

## Development

### Tech Stack

| Tool | Purpose |
|--------|------|
| **uv** | package management (pip not allowed) |
| **ruff** | linting/formatting |
| **mypy** | type checking |
| **pytest** | testing |
| **poethepoet** | task runner |

### Commands

```bash
# dependencies
uv add <package>           # add package
uv add --dev <package>     # add dev dependency
uv sync                    # sync dependencies

# quality checks
poe lint                   # ruff check + format
poe typecheck              # mypy
poe test                   # pytest
poe all                    # run all checks

# direct runs
uv run pytest -v
uv run ruff check .
```

## Hooks

Automation hooks suggest agent collaboration at the right time.

| Hook | Trigger | Action |
|--------|----------|------|
| `agent-router.py` | user input | suggest routing to Codex/Gemini |
| `lint-on-save.py` | file save | run auto-lint |
| `check-codex-before-write.py` | before file write | suggest Codex consultation |
| `log-cli-tools.py` | Codex/Gemini run | record I/O logs |

## Language Rules

- **Code/Thinking/Reasoning**: English
- **User responses**: English
- **Technical documentation**: English
- **User-facing documentation (README, etc.)**: English
