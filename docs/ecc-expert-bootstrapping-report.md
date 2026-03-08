# ECC Expert Bootstrapping Notes

This document captures a source-grounded first-pass map of Everything Claude Code (ECC) for Claude Code terminal users.

## High-signal reading order
1. `README.md`
2. `CLAUDE.md`
3. `the-shortform-guide.md`
4. `the-longform-guide.md`
5. `install.sh`
6. `.claude-plugin/README.md` and `.claude-plugin/plugin.json`
7. `hooks/hooks.json`
8. `commands/`
9. `agents/`
10. `skills/`
11. `rules/README.md` and language rules
12. `mcp-configs/mcp-servers.json`
13. `tests/`

## Claude terminal-oriented model
- **Plugin layer** distributes agents, commands, skills, hooks metadata.
- **Rules layer** is installed manually to `~/.claude/rules` (required for Claude use).
- **Commands** are user-invoked slash workflows.
- **Agents** are delegated specialists selected by those workflows.
- **Skills** are reusable deep references/workflows.
- **Hooks** are lifecycle automations with runtime profile gating.
- **Scripts** implement cross-platform behavior used by hooks and utility commands.
- **MCP configs** provide optional external context connectors.

## Notable operational details
- Recommended install path: plugin install + `./install.sh <language...>` for rules.
- Hook runtime controls:
  - `ECC_HOOK_PROFILE=minimal|standard|strict`
  - `ECC_DISABLED_HOOKS=<comma-separated-hook-ids>`
- Session bootstrap behavior includes package-manager detection and recent-session summary injection.

## Verification snapshot
- `node tests/run-all.js` currently reports 1010 passing / 4 failing tests in this environment.
- Existing failing area observed in integration hook tests around blocking-hook behavior.
