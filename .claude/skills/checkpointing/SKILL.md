---
name: checkpointing
description: |
  Save session context to agent configuration files or create full checkpoint files.
  Supports three modes: session history (default), full checkpoint (--full),
  and skill analysis (--full --analyze) for extracting reusable patterns.
metadata:
  short-description: Checkpoint session context with skill extraction support
---

# Checkpointing — Session Context Persistence

**Save work history during a session and discover reusable skill patterns.**

## Modes

### Mode 1: Session History (default)

Append CLI consultation history to each agent's configuration file.

```
┌─────────────────────────────────────────────────────────────┐
│  .claude/logs/cli-tools.jsonl                               │
│                      ↓                                      │
│  /checkpointing                                             │
│                      ↓                                      │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────────────┐ │
│  │  CLAUDE.md   │ │ AGENTS.md    │ │ GEMINI.md            │ │
│  │ ## Session   │ │ ## Session   │ │ ## Session           │ │
│  │ History      │ │ History      │ │ History              │ │
│  └──────────────┘ └──────────────┘ └──────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

### Mode 2: Full Checkpoint (--full)

Create a comprehensive snapshot of the entire work session.

```
┌─────────────────────────────────────────────────────────────┐
│  Data Sources:                                              │
│  ├─ git log (commits)                                       │
│  ├─ git diff (file changes)                                 │
│  └─ cli-tools.jsonl (Codex/Gemini logs)                     │
│                      ↓                                      │
│  /checkpointing --full                                      │
│                      ↓                                      │
│  .claude/checkpoints/2026-01-28-153000.md                   │
│  ├─ Summary (commits, files, consultations)                 │
│  ├─ Git History (commits list)                              │
│  ├─ File Changes (created, modified, deleted)               │
│  └─ CLI Consultations (Codex/Gemini)                        │
└─────────────────────────────────────────────────────────────┘
```

### Mode 3: Skill Analysis (--full --analyze)

Discover patterns that can be turned into skills from checkpoints.

```
┌─────────────────────────────────────────────────────────────┐
│  /checkpointing --full --analyze                            │
│                      ↓                                      │
│  1. Generate Full Checkpoint                                │
│  2. Generate analysis prompt                                │
│     → .claude/checkpoints/YYYY-MM-DD-HHMMSS.analyze-prompt.md│
│                      ↓                                      │
│  3. Run AI analysis via subagent                            │
│     → Discover work patterns                                │
│     → Propose skill candidates                              │
│                      ↓                                      │
│  4. Add new skills to .claude/skills/                       │
└─────────────────────────────────────────────────────────────┘
```

**Example patterns to discover:**
- test → implement loop (TDD workflow)
- research → design → implementation flow
- simultaneous changes to a specific file set
- CLI consultation → code change sequence

## Usage

```bash
# Session History mode (default)
/checkpointing

# Full Checkpoint mode
/checkpointing --full

# Skill Analysis mode (recommended)
/checkpointing --full --analyze

# Time range
/checkpointing --since "2026-01-26"
/checkpointing --full --analyze --since "2026-01-26"
```

### Skill Analysis Execution Flow

```bash
# Step 1: Generate checkpoint + analysis prompt
python checkpoint.py --full --analyze

# Step 2: Analyze with subagent (Claude runs automatically)
# → Read the analysis prompt
# → Propose skill candidates
# → Generate skills after user approval
```

## Processing Details

### Session History Mode

1. Parse `.claude/logs/cli-tools.jsonl`
2. Organize Codex/Gemini consultations by date
3. Append `## Session History` to each agent config file

### Full Checkpoint Mode

1. **Collect Git information**
   - `git log` for commit history
   - `git diff --name-status` for file changes
   - `git diff --numstat` for line changes

2. **Parse CLI consultation logs**
   - Codex consultation content and status
   - Gemini research content and status

3. **Generate checkpoint file**
   - `.claude/checkpoints/YYYY-MM-DD-HHMMSS.md`

## Full Checkpoint Format

```markdown
# Checkpoint: 2026-01-28 15:30:00 UTC

## Summary
- **Commits**: 5
- **Files changed**: 12 (8 modified, 3 created, 1 deleted)
- **Codex consultations**: 3
- **Gemini researches**: 2

## Git History

### Commits
- `abc1234` Add checkpointing enhancement
- `def5678` Update documentation

### File Changes

**Created:**
- `new_feature.py` (+120)

**Modified:**
- `checkpoint.py` (+80, -20)
- `SKILL.md` (+45, -10)

**Deleted:**
- `old_script.py`

## CLI Tool Consultations

### Codex (3 consultations)
- ✓ Design: checkpoint extension architecture
- ✓ Debugging: Git log parsing issue

### Gemini (2 researches)
- ✓ Research: Git integration best practices
```

## Session History Format

```markdown
## Session History

### 2026-01-26

**Codex consultations:**
- ✓ Recommended the subagent pattern for Codex/Gemini calls...

**Gemini research:**
- ✓ MCP vs CLI comparison research...
```

## Recommended Timing

| Timing | Recommended Mode |
|-----------|-----------|
| Before ending a session | `--full --analyze` |
| After major design decisions | `--full` |
| After completing large feature implementations | `--full --analyze` |
| At natural breakpoints in long work | `--full` |
| When you notice a recurring pattern | `--full --analyze` |
| Daily light logging | default |

## Notes

- If logs are empty, nothing is appended
- Existing `## Session History` sections are overwritten
- The log file itself is not modified (read-only)
- Full Checkpoints accumulate in `.claude/checkpoints/`
- CLI log processing works even if Git is not initialized
- Skill suggestions generated by `--analyze` must be reviewed by a human before adoption
- Skill analysis is designed to let AI discover patterns freely, not to force pre-defined ones
