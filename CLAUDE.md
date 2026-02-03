# Claude Code Orchestra

**Multi-agent Collaboration Framework**

Claude Code integrates Codex CLI (deep reasoning) and Gemini CLI (large-scale research) to accelerate development by leveraging each agent's strengths.

---

## Why This Exists

| Agent | Strength | Use For |
|-------|----------|---------|
| **Claude Code** | orchestration, user interaction | overall coordination, task management |
| **Codex CLI** | deep reasoning, design decisions, debugging | design consultation, error analysis, trade-off evaluation |
| **Gemini CLI** | 1M tokens, multimodal, web search | full codebase analysis, library research, PDF/video processing |

**IMPORTANT**: Even tasks that are difficult for a single agent can be solved through collaboration among the three agents.

---

## Context Management (CRITICAL)

Claude Code's context window is **200k tokens**, but tool definitions and other overhead reduce the effective usable context to **about 70-100k**.

**YOU MUST** call Codex/Gemini via subagents (when output is 10 lines or more).

| Output Size | Method | Reason |
|-----------|------|------|
| 1-2 sentences | direct call OK | no overhead |
| 10+ lines | **via subagent** | protect main context |
| analysis report | subagent → save to file | details are persisted in `.claude/docs/` |

```
# MUST: via subagent (large output)
Task(subagent_type="general-purpose", prompt="Consult Codex on the design and return a summary")

# OK: direct call (small output only)
Bash("codex exec ... 'Answer in one sentence'")
```

---

## Quick Reference

### When to Use Codex

- design decisions ("How should I implement?" "Which pattern?")
- debugging ("Why isn't it working?" "What's causing the error?")
- comparisons ("Which is better, A or B?")

→ Details: `.claude/rules/codex-delegation.md`

### When to Use Gemini

- research ("Look this up" "What's the latest info?")
- large-scale analysis ("Understand the entire codebase")
- multimodal ("Review this PDF/video")

→ Details: `.claude/rules/gemini-delegation.md`

---

## Workflow

```
/startproject <feature-name>
```

1. Gemini analyzes the repository (via subagent)
2. Claude conducts requirements discovery and creates a plan
3. Codex reviews the plan (via subagent)
4. Claude creates the task list
5. **Post-implementation review in a separate session** (recommended)

→ Details: `/startproject`, `/plan`, `/tdd` skills

---

## Tech Stack

- **Python** / **uv** (pip not allowed)
- **ruff** (lint/format) / **ty** (type check) / **pytest**
- `poe lint` / `poe test` / `poe all`

→ Details: `.claude/rules/dev-environment.md`

---

## Documentation

| Location | Content |
|----------|---------|
| `.claude/rules/` | coding, security, and language rules |
| `.claude/docs/DESIGN.md` | record of design decisions |
| `.claude/docs/research/` | Gemini research results |
| `.claude/logs/cli-tools.jsonl` | Codex/Gemini I/O logs |

---

## CLAUDE.md Updates

If the same mistake, misunderstanding, or omission happens more than once, update this file with a clear, actionable rule to prevent repeat issues.

- Keep rules short and specific.
- Prefer concrete do/don't guidance over general advice.
- Add examples only when they clarify ambiguity.
- Remove or consolidate outdated rules to keep this file concise.

---

## Working Practices

### Rich Context Usage

- Use `@` file references to point Claude to specific files.
- Paste images directly when visuals are relevant.
- Pipe data into Claude (logs, outputs, specs) instead of summarizing manually.

### Session Management

- Use `/clear` to reset context when switching tasks.
- Use `/compact` to reduce context size while preserving key information.

### CLI Tools / MCP / Plugins

- Prefer CLI tools for deterministic actions (lint, tests, format, build).
- Connect MCP servers or plugins when they provide reliable external context or tools.

---

## Language Protocol

- **Thinking/Code**: English
- **User communication**: English
