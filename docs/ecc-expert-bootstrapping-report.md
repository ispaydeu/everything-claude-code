# ECC Expert Bootstrapping Report

> **Document version:** 2026-03-08  
> **Scope:** Claude Code terminal / CLI usage  
> **Source of truth:** The forked repository at `ispaydeu/everything-claude-code`  
> **Upstream:** `affaan-m/everything-claude-code`  
> **This document** was synthesized from four independent AI-generated quickstart analyses of the same repository. Where those analyses disagreed, a fact-check was performed against the actual repository sources and the resolution is clearly marked with a **⚠️ Correction** callout so future AI sessions do not repeat the error.

---

## 1. What ECC Appears to Be

Everything Claude Code (ECC) is a **performance optimization system for AI agent harnesses**. Concretely, it is a curated, battle-tested collection of:

- **16 specialized sub-agents** for delegation (planning, code review, TDD, security, etc.)
- **40 slash commands** for quick workflows (`/plan`, `/tdd`, `/code-review`, etc.)
- **65 skill directories** containing reusable workflow knowledge and domain-specific patterns
- **Lifecycle hooks** (PreToolUse, PostToolUse, SessionStart, PreCompact, Stop, SessionEnd) for automatic quality checks, formatting, session persistence, and runtime gating
- **Rules** (common + language-specific overlays) that act as always-on policy layers
- **MCP server configurations** for optional external tool wiring (GitHub, Supabase, Vercel, etc.)
- **Cross-platform Node.js scripts** implementing hook behavior, CI validation, and utilities
- **Three primary user-facing guides** (shortform, longform, security), plus additional specialized guides and docs such as `the-openclaw-guide.md` and the `docs/` directory, and plugin packaging for easy installation

ECC is packaged as a **Claude Code plugin** (via `.claude-plugin/plugin.json`) so it can be installed from the plugin marketplace, and it also supports **manual installation** via `install.sh` for maximum control. It works across Claude Code, Codex, Cursor, Cowork, and other AI agent harnesses, but this report focuses exclusively on **Claude Code terminal / CLI**.

The project is authored by Affaan Mustafa, is MIT-licensed, and at version **1.8.0** ("Harness Performance System").

> **Source:** `README.md`, `CLAUDE.md`, `package.json`, `.claude-plugin/plugin.json`

> **⚠️ Correction — Component counts:** Two of the four source analyses stated there were "41 commands" and "67 skills." The actual counts verified against the repository are **40 command files** in `commands/` and **65 skill directories** in `skills/`. The discrepancy likely arose from counting README or index files, or from slightly different directory listing methods. Future AI sessions should count with `ls commands/*.md | wc -l` and `ls -d skills/*/ | wc -l` to get accurate numbers.

---

## 2. Most Important Sources First

| Priority | Source | Why It Matters | What It Teaches | Read Now or Later |
|----------|--------|----------------|-----------------|-------------------|
| **P0** | `README.md` | Primary entry point; installation, architecture overview, full component inventory | Install paths (plugin + manual), command list, agent list, what's inside each directory | **Now** |
| **P0** | `CLAUDE.md` | Repository architecture guidance for Claude Code itself | How to run tests, development notes, component formats, package manager detection | **Now** |
| **P0** | `the-shortform-guide.md` | Practical operational patterns — the "how to actually use this" doc | Skills vs commands, hook types, subagent usage, MCP hygiene, editor tips, keyboard shortcuts | **Now** |
| **P0** | `install.sh` | The actual installer; what it does, what it supports, what it installs where | Targets (claude/cursor/antigravity), languages (typescript/python/golang/swift), directory structure preservation | **Now** |
| **P1** | `.claude-plugin/plugin.json` | Plugin manifest metadata used by the marketplace (name/version/keywords); does **not** enumerate installed agents/commands/skills | Version, metadata, what is NOT in the manifest (hooks — see correction below) | **Now** |
| **P1** | `.claude-plugin/PLUGIN_SCHEMA_NOTES.md` | Undocumented validator constraints that have caused repeated installation failures | Array requirements, explicit agent paths, version field, the hooks flip-flop history | **Now** |
| **P1** | `hooks/hooks.json` | Every automatic behavior ECC performs; 230+ lines of lifecycle automation | PreToolUse guards, PostToolUse quality checks, session persistence, strategic compaction | **Now** |
| **P1** | `hooks/README.md` | Hook execution model, exit codes, input schema, runtime controls | How to tune hook behavior with `ECC_HOOK_PROFILE` and `ECC_DISABLED_HOOKS` | **Now** |
| **P1** | `rules/README.md` | How the rules layer is organized and why directory structure matters | Common vs language-specific rules, rule priority (language-specific overrides common), rules vs skills distinction | **Now** |
| **P1** | `the-longform-guide.md` | Advanced patterns for power users — context management, token optimization, parallelization | Strategic compaction, model routing (Haiku/Sonnet/Opus), continuous learning, verification loops, fork-based parallelism | **Later** |
| **P1** | `the-security-guide.md` | Threat model and hardening guide for agent-based workflows | Attack vectors (prompt injection, supply chain, credential theft), sandboxing levels, AgentShield scanning, reverse prompt injection guardrails | **Later** |
| **P2** | `.claude-plugin/README.md` | Plugin packaging notes and known validator issues | Same content as PLUGIN_SCHEMA_NOTES.md but shorter | Later |
| **P2** | `mcp-configs/mcp-servers.json` | MCP server templates for external integrations | Available integrations (GitHub, Supabase, Vercel, Railway, CloudFlare, ClickHouse) | Later |
| **P2** | `commands/` directory | All 40 slash commands with their prompts | Each `.md` file is a command definition with description frontmatter | Reference |
| **P2** | `agents/` directory | All 16 agent definitions | Each `.md` file has YAML frontmatter (name, description, tools, model) | Reference |
| **P2** | `skills/` directory | All 65 skill directories | Each contains workflow knowledge, patterns, optional scripts | Reference |
| **P2** | `scripts/` directory | Hook implementations, CI validators, utilities | Cross-platform Node.js code that hooks call | Reference |
| **P2** | `tests/` directory | Test suite (`node tests/run-all.js`) | Validates agents, commands, rules, skills, hooks, and CI scripts; CI expects a clean pass | Reference |

> **Source:** Repository file tree, each file's content

---

## 3. Core Mental Model

ECC is a **layered harness** where each layer serves a distinct purpose. Here is how the pieces fit together:

```
┌─────────────────────────────────────────────────────────────┐
│                     USER (Claude Code CLI)                   │
│  Types: /plan, /tdd, /code-review, etc.                     │
├─────────────────────────────────────────────────────────────┤
│                     COMMANDS (40 files)                       │
│  Slash-triggered entry points that orchestrate workflows     │
│  Stored in: commands/*.md                                    │
├─────────────────────────────────────────────────────────────┤
│                     AGENTS (16 specialists)                   │
│  Delegated roles that commands dispatch work to              │
│  planner, architect, tdd-guide, code-reviewer,               │
│  security-reviewer, build-error-resolver, e2e-runner,        │
│  refactor-cleaner, doc-updater, go-reviewer,                 │
│  go-build-resolver, python-reviewer, database-reviewer,      │
│  chief-of-staff, harness-optimizer, loop-operator            │
│  Stored in: agents/*.md (Markdown + YAML frontmatter)        │
├─────────────────────────────────────────────────────────────┤
│                     SKILLS (65 directories)                   │
│  Deep, reusable workflow knowledge and domain patterns        │
│  Referenced by agents and commands for HOW to do things       │
│  Stored in: skills/<name>/SKILL.md (+ optional scripts)      │
├─────────────────────────────────────────────────────────────┤
│                     HOOKS (lifecycle automation)              │
│  Automatic behaviors triggered at tool/session lifecycle      │
│  PreToolUse → PostToolUse → Stop → SessionStart/End/etc.     │
│  Defined in: hooks/hooks.json                                │
│  Implemented by: scripts/hooks/*.js (Node.js)                │
│  Gated by: ECC_HOOK_PROFILE + ECC_DISABLED_HOOKS             │
├─────────────────────────────────────────────────────────────┤
│                     RULES (always-on policy)                  │
│  Coding standards, security, git workflow, testing reqs       │
│  common/ applies to all; language/ overrides where needed     │
│  Installed to: ~/.claude/rules/ (via install.sh)             │
│  Language-specific rules take precedence over common rules    │
├─────────────────────────────────────────────────────────────┤
│                     MCP CONFIGS (optional wiring)             │
│  Templates for connecting external services                   │
│  GitHub, Supabase, Vercel, Railway, CloudFlare, ClickHouse   │
│  Stored in: mcp-configs/mcp-servers.json                     │
├─────────────────────────────────────────────────────────────┤
│                     SCRIPTS (runtime glue)                    │
│  Cross-platform Node.js implementations for hooks + CI       │
│  scripts/hooks/*.js — hook logic                             │
│  scripts/ci/*.js — validation scripts                        │
│  scripts/lib/*.js — shared utilities                         │
├─────────────────────────────────────────────────────────────┤
│                     GUIDES (user education)                   │
│  the-shortform-guide.md — practical daily operation           │
│  the-longform-guide.md — advanced patterns + optimization     │
│  the-security-guide.md — threat model + hardening             │
├─────────────────────────────────────────────────────────────┤
│                     PLUGIN / INSTALL LAYER                    │
│  .claude-plugin/plugin.json — marketplace packaging           │
│  install.sh — manual installation with target+language args   │
│  package.json — npm packaging as ecc-universal                │
└─────────────────────────────────────────────────────────────┘
```

### Key Relationships

- **Commands** are the user-facing entry points. They invoke **agents** for specialized work.
- **Agents** are delegated specialist roles. They reference **skills** for deep domain knowledge.
- **Skills** tell you *how* to do things. **Rules** tell you *what standards to maintain*.
- **Hooks** run automatically at lifecycle events — they don't require user invocation.
- **Scripts** are the actual Node.js code that hooks execute. Hooks are declarations; scripts are implementations.
- **MCP configs** are optional — they wire external services but consume context window. The shortform guide warns about this explicitly.
- **Rules** are the only component that **must be installed separately** from the plugin (the plugin cannot auto-install rules).
- **Guides** are documentation — they don't execute, but they contain critical operational knowledge.

### What Is Required vs Optional vs Advanced

| Component | Status | Notes |
|-----------|--------|-------|
| Rules (common/) | **Required** | Must install via `install.sh`; plugin cannot do this |
| Rules (language/) | **Required** for that language | Language-specific rules override common where they conflict |
| Commands | Included with plugin | Core workflow entry points |
| Agents | Included with plugin | Delegated to by commands |
| Skills | Included with plugin | Referenced by agents/commands |
| Hooks | Auto-loaded with plugin | Gated by profile; can be disabled individually |
| Scripts | Bundled | Used internally by hooks |
| MCP configs | **Optional** | Copy only what you need; too many consume context |
| Guides | **Optional reading** | But highly recommended for effective use |

> **Source:** `README.md`, `CLAUDE.md`, `hooks/hooks.json`, `rules/README.md`, `the-shortform-guide.md`, `.claude-plugin/plugin.json`

> **⚠️ Correction — "Plugins cannot auto-install rules":** All four source analyses correctly identified this. However, one analysis phrased it as "Plugins cannot auto-install rules" without explaining why. The reason is that Claude Code rules live in `~/.claude/rules/` and are loaded from the user's file system at session start. The plugin system distributes agents, commands, skills, and hooks through the plugin registry, but rules must be placed in the user's rules directory. This is a fundamental architectural constraint of Claude Code, not an ECC limitation.

---

## 4. Claude Code Terminal Quick-Start Path

### Prerequisites

- **Claude Code CLI** installed and authenticated
- **Node.js 18+** (required for hook scripts)
- **Git** (for manual installation path)

### Option A: Plugin Installation (Recommended)

```bash
# Step 1: Add the marketplace source
/plugin marketplace add affaan-m/everything-claude-code

# Step 2: Install the plugin
/plugin install everything-claude-code@everything-claude-code

# Step 3: Verify installation
/plugin list everything-claude-code@everything-claude-code
```

This installs agents, commands, skills, and hooks. But **rules are NOT installed** — you must do Step 4.

### Step 4: Install Rules (REQUIRED — Plugin Cannot Do This)

```bash
# Clone the repo (if you haven't already)
git clone https://github.com/affaan-m/everything-claude-code.git
cd everything-claude-code

# Install common rules + your language(s)
./install.sh typescript              # Single language
./install.sh typescript python golang  # Multiple languages
./install.sh --target claude typescript  # Explicit target (default)
```

**What `install.sh` does:**
1. Always installs `rules/common/` first (coding style, git workflow, testing, performance, patterns, hooks, agents, security)
2. Then installs `rules/<language>/` for each language you specify
3. Preserves directory structure — does NOT flatten (this matters because common/ and language-specific/ may have files with identical names)
4. Target directory: `~/.claude/rules/` (for Claude Code)
5. Validates language names (alphanumeric, dash, underscore only) for path traversal protection

**Supported languages:** `typescript`, `python`, `golang`, `swift` (extensible by adding `rules/<language>/` directories)

**Supported targets:** `claude` (default), `cursor`, `antigravity`

### Option B: Manual Installation (Full Control)

```bash
git clone https://github.com/affaan-m/everything-claude-code.git
cd everything-claude-code

# Install rules (REQUIRED — preserve directory structure)
./install.sh typescript python golang

# Copy agents (optional if not using plugin)
cp agents/*.md ~/.claude/agents/

# Copy commands (optional if not using plugin)
cp commands/*.md ~/.claude/commands/

# Copy skills (optional if not using plugin)
cp -r skills/* ~/.claude/skills/

# Copy hooks to settings.json (merge hooks/hooks.json into ~/.claude/settings.json)
# ⚠️ Manual merge needed — don't just overwrite settings.json

# Copy MCP configs (optional — only what you need)
# Merge desired entries from mcp-configs/mcp-servers.json into ~/.claude.json
```

> **⚠️ Correction — Rules installation path:** One source analysis suggested `cp -r rules/common ~/.claude/rules/common` for manual rule installation. This works but **`install.sh` is strongly preferred** because it handles directory structure preservation, prevents accidental flattening (which would cause filename collisions between common/ and language-specific/ rules), and validates inputs. Use `install.sh` even for manual installations.

### Step 5: Start Using Core Workflows

```bash
# Plan first
/plan "describe your feature or task"
# OR with plugin namespace:
/everything-claude-code:plan "describe your feature or task"

# Implement with TDD
/tdd "implement the planned feature"

# Review
/code-review "review the changes"

# Quality gate
/quality-gate
```

**Command invocation note:** When installed via plugin, commands can be invoked with the namespace prefix (`/everything-claude-code:plan`) or without (`/plan`). The non-namespaced form works when there are no conflicts with other plugins.

### Step 6: Understand Hook Controls

Hooks run automatically. You control them via environment variables:

```bash
# Hook strictness profile (default: standard)
export ECC_HOOK_PROFILE=minimal    # Essential lifecycle + safety only
export ECC_HOOK_PROFILE=standard   # Balanced quality + safety (default)
export ECC_HOOK_PROFILE=strict     # Additional reminders + stricter guardrails

# Disable specific hooks by ID (comma-separated)
export ECC_DISABLED_HOOKS="pre:bash:tmux-reminder,post:edit:typecheck"
```

### Step 7: Validate Your Setup

```bash
# Run the test suite to confirm everything works
node tests/run-all.js
```

Expected result: the suite should complete successfully with **all tests passing** (that is, `node tests/run-all.js` should exit with status code `0`).

> **⚠️ Correction — Test expectations:** Earlier analyses described "2–4 failing tests" as expected and pre-existing. That is misleading. In this repository, `tests/run-all.js` exits non-zero when any tests fail, and CI runs `node tests/run-all.js`, so **any failure should be treated as a real problem to investigate**. Test totals may change over time as the suite evolves, but the operational expectation is a clean pass.

> **Source:** `README.md` (Installation), `install.sh` (installer behavior), `hooks/README.md` (runtime controls), `rules/README.md` (rules installation)

---

## 5. Highest-Value ECC Pieces to Learn First

### Commands (Top 10)

| Command | What It Does | Why Learn It First |
|---------|-------------|-------------------|
| `/plan` | Implementation planning with phased approach | Start every non-trivial task with a plan |
| `/tdd` | Test-driven development workflow (RED → GREEN → REFACTOR) | Core development loop |
| `/code-review` | Quality and security review | Run after every implementation |
| `/build-fix` | Fix build/type errors automatically | Common need during development |
| `/quality-gate` | Run comprehensive quality checks | Gate before committing |
| `/e2e` | Generate and run E2E tests (Playwright) | Critical user flow validation |
| `/verify` | Verification workflow | Confirm changes work |
| `/learn` | Extract patterns from session for future use | Build institutional memory |
| `/skill-create` | Generate skills from git history | Create reusable knowledge |
| `/model-route` | Route to appropriate model (Haiku/Sonnet/Opus) | Token/cost optimization |

### Skills (Top 10)

| Skill | What It Provides | Why Learn It First |
|-------|-----------------|-------------------|
| `tdd-workflow` | TDD patterns, coverage requirements, test-first methodology | Foundation of ECC development style |
| `coding-standards` | Universal coding standards for TypeScript/JavaScript/React/Node | Baseline quality expectations |
| `verification-loop` | Comprehensive verification system | Ensures changes actually work |
| `strategic-compact` | Manual compaction at logical intervals | Critical for long sessions |
| `continuous-learning-v2` | Automated pattern extraction and memory persistence | Builds knowledge over time |
| `eval-harness` | Formal evaluation framework (EDD principles) | Measure improvement objectively |
| `security-review` | Security checklist and patterns | Required before sensitive changes |
| `search-first` | Research-first development approach | Avoid reinventing solutions |
| `configure-ecc` | How to configure ECC itself | Self-referential but important |
| `autonomous-loops` | Autonomous agent loop management | Advanced but high-leverage |

### Hooks (Most Important to Understand)

| Hook | Type | What It Does | Why It Matters |
|------|------|-------------|---------------|
| Session start loader | SessionStart | Loads previous context + detects package manager | Your session starts with context from previous work |
| Strategic compact | PreToolUse | Suggests `/compact` at ~50 tool calls | Prevents context overflow |
| Quality gate | PostToolUse | Runs quality checks after edits (async) | Continuous quality feedback |
| TypeScript check | PostToolUse | Runs `tsc --noEmit` after `.ts`/`.tsx` edits | Catches type errors immediately |
| Code formatter | PostToolUse | Auto-formats JS/TS files using Biome or Prettier (auto-detected) | Consistent code style |
| Console.log warning | PostToolUse + Stop | Warns about console.log statements | Prevents debug code in commits |
| Pre-compact saver | PreCompact | Saves state before context compaction | Preserves knowledge through compaction |
| Session persistence | Stop | Persists session state for next session | Continuity across sessions |
| Git push reminder | PreToolUse | Reminds to review changes before `git push` | Prevents accidental pushes |
| Continuous learning | PreToolUse + PostToolUse | Captures tool use patterns for learning | Builds knowledge automatically |

### Rules (Most Important)

| Rule File | What It Enforces |
|-----------|-----------------|
| `common/coding-style.md` | File size limits, naming conventions, no deep nesting |
| `common/testing.md` | 80% minimum coverage, unit + integration + E2E required |
| `common/security.md` | No hardcoded secrets, input validation, XSS/CSRF/SQLi prevention |
| `common/git-workflow.md` | Conventional commits, comprehensive PR summaries |
| `common/patterns.md` | Repository pattern, API response envelope, immutability |
| `common/agents.md` | When and how to delegate to sub-agents |
| `common/hooks.md` | Hook behavior rules |
| `<language>/*.md` | Language-specific overrides (language takes precedence over common) |

### Guides (Reading Priority)

| Guide | What It Teaches | When to Read |
|-------|----------------|-------------|
| `the-shortform-guide.md` | Daily operational patterns, keyboard shortcuts, MCP hygiene | **Immediately** after setup |
| `the-longform-guide.md` | Token optimization, parallelization, continuous learning, model routing | After you're comfortable with basics |
| `the-security-guide.md` | Threat model, sandboxing, AgentShield, attack patterns | Before working on anything security-sensitive |

> **Source:** `commands/`, `skills/`, `hooks/hooks.json`, `rules/`, `the-shortform-guide.md`, `the-longform-guide.md`, `the-security-guide.md`

---

## 6. Recommended Learning Order

### Stage 1: Get Productive Fast (30 minutes)

**Read:**
1. `README.md` — Focus on installation section and component overview
2. `the-shortform-guide.md` — Focus on commands, hook types, keyboard shortcuts, and MCP advice

**Configure:**
1. Install plugin via marketplace OR clone + manual install
2. Run `./install.sh <your-language>` to install rules (REQUIRED)
3. Verify with `/plan "hello world test"` — you should see the planner agent engage

**You should be able to:**
- Invoke `/plan`, `/tdd`, `/code-review` successfully
- Understand what hooks are running automatically
- Know where to look when something goes wrong
- Use keyboard shortcuts (`Ctrl+U`, `@`, `/`, `Shift+Enter`, `Tab`, `Esc Esc`)

### Stage 2: Understand the Harness Deeply (1-2 hours)

**Read:**
1. `CLAUDE.md` — Architecture, testing commands, development notes
2. `hooks/hooks.json` — Read every hook entry; understand what runs when
3. `hooks/README.md` — Hook execution model, exit codes, input schema
4. `rules/README.md` — Rules organization, priority, rules-vs-skills distinction
5. `.claude-plugin/PLUGIN_SCHEMA_NOTES.md` — Validator constraints and the hooks flip-flop history
6. `the-longform-guide.md` — Focus on context management, model routing, and verification loops

**Configure:**
1. Tune hook profile: `export ECC_HOOK_PROFILE=strict` for maximum guardrails (try for a session, then decide)
2. Explore disabling specific hooks: `export ECC_DISABLED_HOOKS="post:edit:typecheck"` if TypeScript checking is too aggressive
3. Review MCP configs: Copy only needed entries from `mcp-configs/mcp-servers.json` to `~/.claude.json`

**You should be able to:**
- Explain how commands → agents → skills → hooks → rules interact
- Tune hook behavior for your workflow
- Choose appropriate model routing (Haiku for exploration, Sonnet for implementation, Opus for architecture)
- Use `/fork` for parallel research conversations
- Use `/compact` strategically instead of relying on auto-compaction
- Run `node tests/run-all.js` to validate your installation

### Stage 3: Advanced / Optional (Ongoing)

**Read:**
1. `the-security-guide.md` — Full threat model and hardening
2. `skills/continuous-learning-v2/` — How ECC learns from your sessions
3. `skills/autonomous-loops/` — How to set up autonomous agent loops
4. `skills/eval-harness/` — Formal evaluation and benchmarking
5. `skills/strategic-compact/` — Advanced compaction strategies
6. Individual agent files in `agents/` — Understand each specialist's capabilities
7. `docs/token-optimization.md` — Token cost management

**Configure:**
1. Set up continuous learning hooks for automatic pattern extraction
2. Create custom skills with `/skill-create` based on your project's patterns
3. Configure AgentShield for security scanning: `npx ecc-agentshield scan`
4. Set up aliases for common workflows:
   ```bash
   alias claude-dev='claude --system-prompt "$(cat ~/.claude/contexts/dev.md)"'
   ```
5. Experiment with the two-instance kickoff pattern (scaffolding agent + research agent)
6. Try git worktrees for parallel Claude instances without conflicts

**You should be able to:**
- Create custom skills and commands for your project
- Run autonomous loops safely
- Benchmark workflow improvements with evals
- Harden your setup against prompt injection and supply chain attacks
- Optimize token costs across model tiers
- Use the orchestrator pattern (RESEARCH → PLAN → IMPLEMENT → REVIEW → VERIFY)

> **Source:** `README.md`, `CLAUDE.md`, `the-shortform-guide.md`, `the-longform-guide.md`, `the-security-guide.md`, `hooks/README.md`, `rules/README.md`

---

## 7. Practical Usage Patterns

### Pattern 1: Standard Feature Development

```
/plan "add user authentication with JWT"
→ planner agent creates phased implementation plan

/tdd "implement JWT auth per the plan"
→ tdd-guide agent: write tests first (RED), implement (GREEN), refactor

/code-review "review the JWT implementation"
→ code-reviewer agent: quality, security, maintainability checks

/quality-gate
→ runs comprehensive checks via post-tool hooks

/verify
→ verification workflow confirms everything works
```

### Pattern 2: Bug Fix with TDD

```
/tdd "fix the login timeout bug"
→ Write a failing test that reproduces the bug
→ Fix the implementation until test passes
→ Verify no regressions
```

### Pattern 3: Parallel Research + Implementation

```
# Fork conversation for research
/fork
→ Use forked conversation to explore codebase, read docs, research APIs

# Main conversation for code changes
/plan "implement the feature based on research findings"
/tdd "implement it"
```

### Pattern 4: Long Session Management

```
# Strategic compaction (hook will remind you at ~50 tool calls)
/compact

# Or use dynamic system prompt for persistent context
claude --system-prompt "$(cat project-context.md)"
```

### Pattern 5: Security Audit

```
# Full AgentShield scan
npx ecc-agentshield scan

# Deep scan with Opus agents
npx ecc-agentshield scan --opus

# Auto-fix safe issues
npx ecc-agentshield scan --fix
```

### Pattern 6: Multi-Agent Orchestration

```
/multi-plan "design and implement new payment system"
→ chief-of-staff coordinates multiple agents

/orchestrate "complex cross-cutting refactoring"
→ Sequential phases: RESEARCH → PLAN → IMPLEMENT → REVIEW → VERIFY
```

### Pattern 7: Build Error Recovery

```
/build-fix
→ build-error-resolver agent analyzes errors and fixes incrementally
→ Verifies after each fix
```

### Pattern 8: Model Routing for Cost Optimization

```
/model-route
→ Routes to appropriate model:
  - Haiku: exploration, simple edits, docs (cheapest)
  - Sonnet: multi-file implementation, PR reviews (90% of work)
  - Opus: complex architecture, debugging, security (most expensive)
```

### Pattern 9: Continuous Learning

```
# After a productive session, extract patterns
/learn
→ Saves discovered patterns, debugging techniques, workarounds as skills

# Create a reusable skill from recent work
/skill-create
→ Generates a skill directory from git history
```

### Pattern 10: MCP-Lean Workflow

The shortform guide explicitly warns: **MCP servers consume context window.** With a 200k context window, too many MCP tools loaded at once may reduce effective context to ~70k after compaction.

**Best practice:** Start with zero MCPs. Add only what you need. Prefer CLI + skills over MCPs where possible. For example, instead of a GitHub MCP, create a `/gh-pr` command wrapping `gh pr create`.

> **Source:** `the-shortform-guide.md`, `the-longform-guide.md`, `the-security-guide.md`, `commands/`, `agents/`

> **⚠️ Correction — MCP server count limit:** One source analysis stated "Keep enabled MCP servers below roughly 10." The repository does in fact include similar guidance — `the-shortform-guide.md` says to "keep under 10 enabled / under 80 tools active," and `mcp-configs/mcp-servers.json` includes the comment `"Keep under 10 MCPs enabled to preserve context window"`. This should be treated as a **rule of thumb for context-window management**, not a strict enforced cap. Future AI sessions should preserve the under-10 guidance while making clear it is heuristic rather than a validator or runtime limit.

---

## 8. Fork-Specific Differences

This section compares the `ispaydeu/everything-claude-code` fork against the `affaan-m/everything-claude-code` upstream.

**Finding:** As of this analysis, the fork appears to be a **direct clone of upstream** with no meaningful divergence in:
- Plugin manifest
- Hook definitions
- Rules structure
- Command inventory
- Agent inventory
- Skill inventory
- Script implementations
- Guide content

The fork's `plugin.json` and `README.md` still reference the upstream `affaan-m/everything-claude-code` repository in their URLs, confirming this is a fresh fork without customization.

**Operational implication:** All guidance in this report is upstream-compatible. If the fork diverges in the future, this section should be updated to document the differences.

**Install path note for fork users:** The plugin marketplace commands reference the upstream repo (`affaan-m/everything-claude-code`). If installing from the fork directly, clone the fork URL instead:
```bash
git clone https://github.com/ispaydeu/everything-claude-code.git
```

> **Source:** `.claude-plugin/plugin.json` (homepage/repository fields point to upstream), `README.md` (installation commands reference upstream)

---

## 9. Risks, Gotchas, and Unknowns

### Likely Confusion Points

1. **Rules are NOT installed by the plugin.** This is the #1 setup mistake. After plugin install, you MUST also run `install.sh` or manually copy rules. Without rules, ECC operates without its policy layer.

2. **Do NOT add `"hooks"` to plugin.json.** Claude Code v2.1+ auto-loads `hooks/hooks.json` by convention. Adding it to the manifest causes a "duplicate hooks" error. This has been a recurring issue (documented in `.claude-plugin/PLUGIN_SCHEMA_NOTES.md` with commit history showing 4 flip-flop cycles).

3. **Rules directory structure matters.** Do NOT flatten rules with `cp rules/*/*.md ~/.claude/rules/`. Common and language-specific directories contain files with the same names. Flattening causes one to overwrite the other. Always preserve the directory structure.

4. **Language-specific rules override common rules.** When they conflict, language-specific takes precedence. This is by design but can confuse users who expect common rules to always apply.

5. **Hook profiles default to `standard`.** If hooks feel too aggressive, try `minimal`. If not strict enough, try `strict`. This is controlled via `ECC_HOOK_PROFILE` environment variable.

6. **MCP context consumption.** Each enabled MCP server adds its tool definitions to your context window. Too many servers can significantly reduce available context for actual work. Start lean.

7. **`/security-scan` is a skill, not a command file.** The README lists `/security-scan` as a key command, but there is no `commands/security-scan.md` file. The security scanning capability is provided by the `skills/security-scan/` skill directory. This may cause confusion if you search for the command file.

8. **Node.js 18+ required.** All hook scripts are implemented in Node.js (cross-platform rewrite in v1.8). If your Node version is below 18, hooks will fail silently or with errors.

9. **The `agents` field in plugin.json must use explicit file paths, not directories.** Using `"agents": ["./agents/"]` will fail validation. You must enumerate each agent file. This is an undocumented validator constraint.

10. **The test suite is expected to pass cleanly.** If `node tests/run-all.js` reports failures, investigate them rather than assuming they are normal, because CI also runs that command and treats failures as real breakages.

### Source Conflicts Between the Four Analyses

| Topic | Conflict | Resolution |
|-------|----------|------------|
| Command count | Some said 41, some said 40+ | **40 commands** verified by `ls commands/*.md \| wc -l` |
| Skill count | Some said 67, some said 65+ | **65 skill directories** verified by `ls -d skills/*/ \| wc -l` |
| Test results | All said 1010/4 | **The suite is expected to pass cleanly**; totals may change over time, but failures should be investigated |
| MCP limit | One said "below ~10" | **Repo guidance says keep under ~10 enabled**, but as a rule of thumb for context-window management |
| `/security-scan` listed as command | README lists it as key command | **It's a skill (`skills/security-scan/`), not a command (`commands/security-scan.md` does not exist)** |

### What Is Still Unknown or Needs Verification

- **Plugin marketplace availability:** Whether `/plugin marketplace add affaan-m/everything-claude-code` works depends on the upstream author having published to the Claude plugin marketplace. If it fails, use manual installation.
- **Claude Code version compatibility:** The hooks auto-loading behavior changed in v2.1. If you're on an older Claude Code version, hook behavior may differ.
- **Continuous learning v2 stability:** The `skills/continuous-learning-v2/` is documented in `docs/continuous-learning-v2-spec.md` but its maturity level is not clear from repo sources alone.
- **AgentShield availability:** `npx ecc-agentshield scan` is documented in the security guide, but whether the npm package is published and maintained is not verified from repo sources.
- **The `the-openclaw-guide.md`** exists in the repo root but was not referenced by any of the four source analyses or the main README. Its relationship to the other guides needs investigation.

> **Source:** `.claude-plugin/PLUGIN_SCHEMA_NOTES.md`, `hooks/README.md`, `rules/README.md`, `the-shortform-guide.md`, direct repository verification

---

## 10. Ready-for-Questions Status

**Status: Ready.** This report is grounded in direct repository source verification. The mental model, install path, operational patterns, and gotchas are confirmed against actual file contents.

### Example Follow-Up Questions You Can Ask

1. **"How do I create a custom skill for my project?"** — Walkthrough using `/skill-create` and the skill format spec.
2. **"How do I set up autonomous loops safely?"** — Based on `skills/autonomous-loops/` and the longform guide.
3. **"What does the session persistence hook actually save and restore?"** — Deep dive into `scripts/hooks/session-start.js` and `scripts/hooks/pre-compact.js`.
4. **"How do I add a new language to install.sh?"** — Create a `rules/<language>/` directory with rule files.
5. **"What's the difference between /plan and /multi-plan?"** — Single vs multi-agent orchestration comparison.
6. **"How do I harden my ECC installation against prompt injection?"** — Security guide walkthrough with AgentShield.
7. **"How do I optimize my token costs when using ECC?"** — Model routing, MCP hygiene, strategic compaction, subagent architecture.
8. **"How do I debug a hook that isn't firing?"** — Hook profile checks, `ECC_DISABLED_HOOKS` audit, exit code interpretation.
9. **"What's the best way to onboard a team member to ECC?"** — Staged learning path with configuration checklist.
10. **"How do continuous learning hooks work under the hood?"** — Deep dive into `skills/continuous-learning-v2/` and the Stop hook implementation.

---

## 11. Source Notes

| Section | Primary Sources |
|---------|----------------|
| §1 What ECC Is | `README.md`, `CLAUDE.md`, `package.json`, `.claude-plugin/plugin.json` |
| §2 Priority Sources | Repository file tree, content of each referenced file |
| §3 Core Mental Model | `README.md`, `CLAUDE.md`, `hooks/hooks.json`, `rules/README.md`, `the-shortform-guide.md`, `.claude-plugin/plugin.json` |
| §4 Quick-Start | `README.md` (Installation), `install.sh` (behavior), `hooks/README.md` (controls), `rules/README.md` (rules installation) |
| §5 Highest-Value Pieces | `commands/*.md`, `skills/*/`, `hooks/hooks.json`, `rules/common/*.md`, `the-shortform-guide.md`, `the-longform-guide.md`, `the-security-guide.md` |
| §6 Learning Order | All guides, `CLAUDE.md`, `hooks/README.md`, `rules/README.md`, `.claude-plugin/PLUGIN_SCHEMA_NOTES.md` |
| §7 Usage Patterns | `the-shortform-guide.md`, `the-longform-guide.md`, `the-security-guide.md`, `commands/`, `agents/` |
| §8 Fork Differences | `.claude-plugin/plugin.json` (upstream URLs), `README.md` (upstream references), fork commit history |
| §9 Risks & Gotchas | `.claude-plugin/PLUGIN_SCHEMA_NOTES.md`, `hooks/README.md`, `rules/README.md`, `the-shortform-guide.md`, direct `ls` / `wc` verification |
| §10 Ready Status | All of the above |

### Correction Log (For Future AI Sessions)

| Correction | What Was Wrong | What Is Correct | How to Verify |
|------------|---------------|-----------------|---------------|
| Command count | "41 commands" | **40 command files** in `commands/` | `ls commands/*.md \| wc -l` |
| Skill count | "67 skills" | **65 skill directories** in `skills/` | `ls -d skills/*/ \| wc -l` |
| Test results | "1010 passed, 4 failed" | **The suite is expected to pass cleanly**; totals may change over time | `tests/run-all.js`, `.github/workflows/reusable-test.yml` |
| MCP server limit | "below ~10 servers" | **Repo guidance says keep under ~10 enabled**, as a context-window heuristic | `the-shortform-guide.md`, `mcp-configs/mcp-servers.json` |
| `/security-scan` | Listed as a command | **It's a skill** (`skills/security-scan/`), no `commands/security-scan.md` exists | `ls commands/security-scan.md` (will 404) |
| Rules auto-install | Ambiguous phrasing | Rules are **never** installed by the plugin; `install.sh` or manual copy **always** required | `install.sh` source, `.claude-plugin/plugin.json` (no rules field) |
| Hook duplicate error | Insufficiently explained | Claude Code v2.1+ auto-loads `hooks/hooks.json`; adding to manifest causes duplicate error; documented flip-flop history in `PLUGIN_SCHEMA_NOTES.md` | `.claude-plugin/PLUGIN_SCHEMA_NOTES.md` |

---

## Appendix A: Complete Component Inventory

### All 40 Commands

| Command | File |
|---------|------|
| `/build-fix` | `commands/build-fix.md` |
| `/checkpoint` | `commands/checkpoint.md` |
| `/claw` | `commands/claw.md` |
| `/code-review` | `commands/code-review.md` |
| `/e2e` | `commands/e2e.md` |
| `/eval` | `commands/eval.md` |
| `/evolve` | `commands/evolve.md` |
| `/go-build` | `commands/go-build.md` |
| `/go-review` | `commands/go-review.md` |
| `/go-test` | `commands/go-test.md` |
| `/harness-audit` | `commands/harness-audit.md` |
| `/instinct-export` | `commands/instinct-export.md` |
| `/instinct-import` | `commands/instinct-import.md` |
| `/instinct-status` | `commands/instinct-status.md` |
| `/learn` | `commands/learn.md` |
| `/learn-eval` | `commands/learn-eval.md` |
| `/loop-start` | `commands/loop-start.md` |
| `/loop-status` | `commands/loop-status.md` |
| `/model-route` | `commands/model-route.md` |
| `/multi-backend` | `commands/multi-backend.md` |
| `/multi-execute` | `commands/multi-execute.md` |
| `/multi-frontend` | `commands/multi-frontend.md` |
| `/multi-plan` | `commands/multi-plan.md` |
| `/multi-workflow` | `commands/multi-workflow.md` |
| `/orchestrate` | `commands/orchestrate.md` |
| `/plan` | `commands/plan.md` |
| `/pm2` | `commands/pm2.md` |
| `/projects` | `commands/projects.md` |
| `/promote` | `commands/promote.md` |
| `/python-review` | `commands/python-review.md` |
| `/quality-gate` | `commands/quality-gate.md` |
| `/refactor-clean` | `commands/refactor-clean.md` |
| `/sessions` | `commands/sessions.md` |
| `/setup-pm` | `commands/setup-pm.md` |
| `/skill-create` | `commands/skill-create.md` |
| `/tdd` | `commands/tdd.md` |
| `/test-coverage` | `commands/test-coverage.md` |
| `/update-codemaps` | `commands/update-codemaps.md` |
| `/update-docs` | `commands/update-docs.md` |
| `/verify` | `commands/verify.md` |

### All 16 Agents

| Agent | File | Specialty |
|-------|------|-----------|
| Planner | `agents/planner.md` | Implementation planning |
| Architect | `agents/architect.md` | System design decisions |
| TDD Guide | `agents/tdd-guide.md` | Test-driven development |
| Code Reviewer | `agents/code-reviewer.md` | Quality/security review |
| Security Reviewer | `agents/security-reviewer.md` | Vulnerability analysis |
| Build Error Resolver | `agents/build-error-resolver.md` | Fix build errors |
| E2E Runner | `agents/e2e-runner.md` | End-to-end testing (Playwright) |
| Refactor Cleaner | `agents/refactor-cleaner.md` | Dead code removal |
| Doc Updater | `agents/doc-updater.md` | Documentation sync |
| Go Reviewer | `agents/go-reviewer.md` | Go code review |
| Go Build Resolver | `agents/go-build-resolver.md` | Go build errors |
| Python Reviewer | `agents/python-reviewer.md` | Python code review |
| Database Reviewer | `agents/database-reviewer.md` | Database/Supabase review |
| Chief of Staff | `agents/chief-of-staff.md` | Multi-agent coordination |
| Harness Optimizer | `agents/harness-optimizer.md` | Optimize agent harness |
| Loop Operator | `agents/loop-operator.md` | Autonomous loop management |

### All 65 Skills (Grouped)

**Core Workflow:**
`tdd-workflow`, `coding-standards`, `verification-loop`, `eval-harness`, `strategic-compact`, `search-first`, `continuous-learning`, `continuous-learning-v2`, `configure-ecc`, `skill-stocktake`

**Agent Architecture:**
`agent-harness-construction`, `agentic-engineering`, `ai-first-engineering`, `autonomous-loops`, `continuous-agent-loop`, `enterprise-agent-ops`, `iterative-retrieval`, `cost-aware-llm-pipeline`

**Web/API/Backend:**
`api-design`, `backend-patterns`, `frontend-patterns`, `frontend-slides`, `e2e-testing`, `liquid-glass-design`

**Databases:**
`postgres-patterns`, `database-migrations`, `clickhouse-io`

**DevOps/Deployment:**
`deployment-patterns`, `docker-patterns`

**Security:**
`security-review`, `security-scan`

**Languages:**
`python-patterns`, `python-testing`, `golang-patterns`, `golang-testing`, `java-coding-standards`, `jpa-patterns`, `cpp-coding-standards`, `cpp-testing`, `swift-actor-persistence`, `swift-concurrency-6-2`, `swift-protocol-di-testing`, `swiftui-patterns`

**Frameworks:**
`django-patterns`, `django-security`, `django-tdd`, `django-verification`, `springboot-patterns`, `springboot-security`, `springboot-tdd`, `springboot-verification`

**Business/Content:**
`article-writing`, `content-engine`, `investor-materials`, `investor-outreach`, `market-research`

**Specialized:**
`nanoclaw-repl`, `plankton-code-quality`, `ralphinho-rfc-pipeline`, `nutrient-document-processing`, `foundation-models-on-device`, `regex-vs-llm-structured-text`, `visa-doc-translate`, `content-hash-cache-pattern`, `project-guidelines-example`

### Hook Lifecycle Summary

```
SessionStart → [session-start.js: load context + detect package manager]
     │
     ▼
User sends prompt → Claude picks tool
     │
     ▼
PreToolUse → [auto-tmux-dev, tmux-reminder, git-push-reminder,
              doc-file-warning, strategic-compact, continuous-learning]
     │
     ▼
Tool executes
     │
     ▼
PostToolUse → [pr-logger, build-analysis, quality-gate, prettier-format,
               typecheck, console-log-warning, continuous-learning]
     │
     ▼
Claude finishes responding
     │
     ▼
Stop → [check-console-log, session-persistence, evaluate-session, cost-tracker]
     │
     ▼
PreCompact → [pre-compact.js: save state before compaction]
     │
     ▼
SessionEnd → [session-end.js: lifecycle marker + cleanup]
```

> **Prompt version used:** v2026-03-07  
> **Repository state:** `ispaydeu/everything-claude-code` main branch  
> **Generated:** 2026-03-08
