# Plugin Guide — Researching, Evaluating, and Installing Claude Code Plugins

## What Plugins Are

Plugins are instruction/tool packages that extend Claude Code. They can contain:
- **Skills** (SKILL.md) — instructions injected into Claude's context for specific task types (e.g., frontend-design pushes better UI code)
- **Commands** — slash commands that trigger workflows (e.g., `/commit`)
- **Agents** — subagent definitions with their own system prompts
- **Hooks** — scripts that run on events (e.g., lint after every file edit)
- **MCP servers** — connections to external tools (e.g., Playwright controls a browser, GitHub manages PRs)
- **LSP servers** — language server protocol for real type-checking

Plugins are NOT compiled code — they're mostly markdown instructions and config files loaded into Claude's context.

## How to Discover Plugins

Inside a Claude session:
```
/plugin
```
Go to the **Discover** tab to browse the official marketplace (101+ plugins as of March 2026).

Or browse online:
- Official marketplace: https://claude.com/plugins
- GitHub source: https://github.com/anthropics/claude-plugins-official

## How to Install

Inside a Claude session:
```
/plugin install <name>@claude-plugins-official
```

**Scope matters.** Plugins can be installed at:
- **Project level** (recommended) — only active in this repo
- **User level** — active in ALL repos (avoid unless universally useful)

Install per-project to avoid irrelevant plugins activating. TypeScript LSP on a Python project is wasted overhead. Frontend-design on a data pipeline is nonsensical.

## How to Evaluate a Plugin

Before installing, ask these questions:

### 1. Does it conflict with our agent workflow?

Plugins with their own orchestration/agent systems **conflict** with our framework:
- **Superpowers** — has its own brainstorming, planning, TDD, review phases. Two competing management systems.
- **feature-dev** — has its own explore/architect/review agents. Overlaps with orchestrator.
- **ralph-loop** — autonomous loops conflict with orchestrator's controlled delegation.
- **pr-review-toolkit / code-review** — overlap with our reviewer agent.

**Rule:** If a plugin has its own agents, orchestration, or review flow, it probably conflicts. Use our framework OR the plugin, not both.

### 2. Is it a passive enhancer or an active tool?

**Passive** (install and forget — just makes things better):
- frontend-design, security-guidance, context7, pyright-lsp, typescript-lsp

**Active tools** (agents need to know they exist to use them):
- playwright (tester uses for visual checks), github (orchestrator uses for PRs), commit-commands (agents use for git)
- Active tools must be documented in CLAUDE.md so agents know they're available.

**Workflow replacers** (use outside agent sessions for quick tasks):
- feature-dev, code-review — useful standalone but conflict during orchestrator sessions

### 3. Is it relevant to this project type?

| Plugin | App (frontend) | App (backend) | LLM Pipeline | Data Pipeline |
|---|:---:|:---:|:---:|:---:|
| frontend-design | Yes | No | No | No |
| typescript-lsp | Yes (if TS) | Yes (if TS) | No | No |
| pyright-lsp | No | No | Yes (if Python) | Yes (if Python) |
| playwright | Yes | Maybe | No | No |
| security-guidance | Yes | Yes | Yes | Yes |
| commit-commands | Yes | Yes | Yes | Yes |
| github | Yes | Yes | Yes | Yes |
| context7 | Yes | Yes | Maybe | Maybe |
| code-simplifier | Yes | Yes | Yes | Yes |
| claude-md-management | Yes | Yes | Yes | Yes |
| figma | Only with Figma | No | No | No |

## Recommended Plugin Sets by Project Type

### App Development (Frontend-heavy)
```
/plugin install frontend-design@claude-plugins-official
/plugin install typescript-lsp@claude-plugins-official
/plugin install playwright@claude-plugins-official
/plugin install security-guidance@claude-plugins-official
/plugin install commit-commands@claude-plugins-official
/plugin install github@claude-plugins-official
/plugin install context7@claude-plugins-official
/plugin install code-simplifier@claude-plugins-official
/plugin install claude-md-management@claude-plugins-official
```

### App Development (Backend-heavy)
```
/plugin install typescript-lsp@claude-plugins-official    # or pyright-lsp for Python
/plugin install security-guidance@claude-plugins-official
/plugin install commit-commands@claude-plugins-official
/plugin install github@claude-plugins-official
/plugin install context7@claude-plugins-official
/plugin install code-simplifier@claude-plugins-official
/plugin install claude-md-management@claude-plugins-official
```

### LLM / Data Pipeline (Python)
```
/plugin install pyright-lsp@claude-plugins-official
/plugin install security-guidance@claude-plugins-official
/plugin install commit-commands@claude-plugins-official
/plugin install github@claude-plugins-official
/plugin install claude-md-management@claude-plugins-official
```

### Universal (every project)
```
/plugin install security-guidance@claude-plugins-official
/plugin install commit-commands@claude-plugins-official
/plugin install github@claude-plugins-official
/plugin install claude-md-management@claude-plugins-official
```

## After Installing — Document in CLAUDE.md

Add an "Installed Plugins" section to CLAUDE.md listing each plugin and how agents should interact with it:

```markdown
## Installed Plugins

- **[passive plugin]** — [what it does]. Activates automatically.
- **[active tool]** — [what it does]. [Which agent] should use this for [which tasks].
- **[workflow plugin]** — [what it does]. Use for [quick tasks] OUTSIDE orchestrator sessions.
```

The bootstrap agent handles this during setup (Step 4b). For existing projects, add the section manually.

## Plugins to Avoid When Using the Agent Framework

These are good plugins but conflict with our multi-agent orchestration:

| Plugin | Why Skip |
|---|---|
| **superpowers** | Own orchestration layer (brainstorm → plan → TDD → review). Fights with orchestrator. |
| **feature-dev** | Own explore/architect/review agents. Overlaps with orchestrator + reviewer. |
| **ralph-loop** | Autonomous unsupervised loops. Conflicts with controlled delegation. Native `/loop` command exists anyway. |
| **pr-review-toolkit** | Own multi-agent review. Overlaps with reviewer agent. |
| **code-review** | Own review agents. Overlaps with reviewer agent. |
| **claude-code-setup** | Analyzes codebase and recommends config. Our recon agent + bootstrap does this. |

These are fine if you're NOT using the agent framework. They're just redundant or conflicting when you are.
