# Claude Code Terminal Quickstart for Everything Claude Code (ECC)

This quickstart is for users who mainly run Claude Code from terminal and want ECC working fast.

## 1) Install the plugin

```bash
/plugin marketplace add affaan-m/everything-claude-code
/plugin install everything-claude-code@everything-claude-code
```

## 2) Install rules (required)

Plugins cannot auto-install rules, so install them manually from the repo:

```bash
git clone https://github.com/affaan-m/everything-claude-code.git
cd everything-claude-code
./install.sh typescript
```

Use one or more languages:

```bash
./install.sh typescript python golang
```

## 3) Start with the core workflow

- Plan first: `/everything-claude-code:plan "..."`
- Implement with TDD: `/everything-claude-code:tdd "..."`
- Review: `/everything-claude-code:code-review "..."`

For manual install command paths, use non-namespaced versions like `/plan`.

## 4) Understand hook controls

ECC hook execution is profile-controlled:

- `ECC_HOOK_PROFILE=minimal|standard|strict` (default: `standard`)
- `ECC_DISABLED_HOOKS=comma,separated,hook,ids`

## 5) Keep MCP usage lean

ECC includes an MCP starter config in `mcp-configs/mcp-servers.json`. Copy only what you need and keep enabled MCP servers low to preserve context window.

## 6) High-value files to read next

1. `README.md` (installation + command/agent/skill entry point)
2. `hooks/hooks.json` (what runs automatically)
3. `rules/README.md` (how common/language rules layer)
4. `the-shortform-guide.md` (practical operating patterns)
5. `the-longform-guide.md` (advanced optimization strategies)
