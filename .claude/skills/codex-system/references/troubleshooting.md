# Troubleshooting

## Codex CLI Not Found

```bash
# Check
which codex
codex --version

# Install
npm install -g @openai/codex
```

## Authentication Error

```bash
# Re-authenticate
codex login

# Check status
codex login status
```

## Timeout

| reasoning_effort | Recommended Timeout |
|-----------------|---------------------|
| low             | 60s                 |
| medium          | 180s                |
| high            | 600s                |
| xhigh           | 900s                |

Configure in `config.toml`:
```toml
[mcp_servers.codex]
tool_timeout_sec = 600
```

## Git Repository Error

```bash
# When running outside a Git repo
codex exec --skip-git-repo-check ...
```

## Too Much reasoning Output

```bash
# Suppress stderr
codex exec ... 2>/dev/null

# Or set in config.toml
hide_agent_reasoning = true
```

## Cannot Continue Session

```bash
# Recent sessions list
codex sessions list

# Details for a specific session
codex sessions show {SESSION_ID}
```

## Sandbox Permission Error

| Error | Cause | Solution |
|--------|------|--------|
| Permission denied | writing in read-only | switch to workspace-write |
| Network blocked | sandbox restriction | danger-full-access (use with caution) |

## Out of Memory

When analyzing a large codebase:
1. Narrow the target files
2. Analyze in stages
3. Tune with `--config context_limit=...`
