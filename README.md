# claude-code-orchestra

Multi-Agent AI Development Environment

```
Claude Code (Orchestrator) ─┬─ Codex CLI (Deep Reasoning)
                            ├─ Gemini CLI (Research)
                            └─ Subagents (Parallel Tasks)
```

## Quick Start

既存プロジェクトのルートで実行:

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

## License

MIT
