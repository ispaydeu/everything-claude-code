# ECC Expert Bootstrapping Notes

This file captures a repository-grounded orientation for Everything Claude Code (ECC), with emphasis on Claude Code terminal usage.

## High-signal files

1. `README.md` — install path, release highlights, architecture framing
2. `CLAUDE.md` — repository architecture and test commands
3. `the-shortform-guide.md` — operational workflows (commands, hooks, MCP hygiene)
4. `the-longform-guide.md` — advanced token/memory/eval/parallelization patterns
5. `install.sh` — concrete installer behavior and target layouts
6. `.claude-plugin/README.md` + `.claude-plugin/plugin.json` — plugin manifest and validation caveats
7. `hooks/hooks.json` + `hooks/README.md` — active hook lifecycle and runtime controls
8. `commands/`, `agents/`, `skills/`, `rules/`, `mcp-configs/`, `scripts/`, `tests/`

## Core model

ECC is a harness package:
- agents: specialized delegated roles
- commands: slash-triggered entry points
- skills: reusable workflow knowledge + optional scripts
- hooks: automatic event-driven checks and persistence
- rules: always-on policy layer (common + language overlays)
- mcp-configs: optional external tool wiring templates
- scripts: cross-platform execution and runtime glue
- guides: user education from quick setup to advanced operation

## Claude Code-first path

1. Install plugin via marketplace commands from README.
2. Install rules manually with `./install.sh <language...>`.
3. Run commands (`/plan`, `/tdd`, `/code-review`, `/quality-gate`, etc.).
4. Keep MCP and hook profiles lean for context/token efficiency.
5. Use loop and NanoClaw features after baseline workflow is stable.

## Validation commands

- `node tests/run-all.js`

