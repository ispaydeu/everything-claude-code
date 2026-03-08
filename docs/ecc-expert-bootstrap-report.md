# ECC Expert Bootstrapping Notes (Claude Code CLI Focus)

This document captures a high-signal, source-grounded orientation for operating Everything Claude Code (ECC) in Claude Code terminal workflows.

## Key findings

- ECC is structured as a Claude Code plugin with installable agents, commands, skills, hooks, and external MCP templates.
- Plugin install does not distribute rules; rules must be installed manually or via `install.sh`.
- Hook auto-loading behavior in Claude Code v2.1+ requires avoiding a `hooks` entry in plugin manifest.
- Hook runtime can be tuned using `ECC_HOOK_PROFILE` and `ECC_DISABLED_HOOKS`.
- The practical command flow is plan -> tdd -> code-review -> quality/security checks.

## Priority files to study first

1. `README.md`
2. `CLAUDE.md`
3. `the-shortform-guide.md`
4. `the-longform-guide.md`
5. `install.sh`
6. `.claude-plugin/plugin.json` and `.claude-plugin/README.md`
7. `hooks/hooks.json`
8. `rules/README.md`
9. `mcp-configs/mcp-servers.json`
10. high-value commands/skills (plan, tdd, quality-gate, loop-start, model-route, strategic-compact, continuous-learning-v2)

## Operational quick-start (Claude Code)

```bash
/plugin marketplace add affaan-m/everything-claude-code
/plugin install everything-claude-code@everything-claude-code

# required: install rules separately
./install.sh typescript   # or python/golang/swift

# verify
/plugin list everything-claude-code@everything-claude-code
/everything-claude-code:plan "your task"
```

## Notes

- Default to Sonnet; escalate to Opus for architecture/security ambiguity.
- Keep enabled MCP servers below roughly 10 to protect context window.
- Use strategic compaction and memory persistence hooks for long sessions.
- For security hardening, run AgentShield and review CLAUDE/settings/hooks/MCP definitions.
