# Agent Team Framework

A reusable framework for setting up coordinated AI agent teams on any project. Based on patterns proven across 30+ improvement cycles on the Dexter pipeline.

## Quick Start

1. Copy this folder into your project repo (or keep it separate and reference it)
2. Run the bootstrap agent: `claude --agent bootstrap`
3. Answer the bootstrap's questions about your project
4. The bootstrap generates your tailored CLAUDE.md, agent definitions, workflow guide, and tracking files
5. Start your first session: `claude --agent orchestrator`

## What You Get

- **CLAUDE.md** — Single source of truth for your project context, scoped to your workstream
- **Agent team** — Orchestrator + domain-specific agents + quality agents, each with clear file boundaries
- **Workflow guide** — Session patterns, testing methods, git safety, adapted to your project type
- **Tracking files** — CHANGELOG.md, IMPROVEMENTS.md, AGENT-IMPROVEMENTS.md
- **Quality rubric** — Evaluation criteria tailored to your project type

## Architecture

### Core Agents (always included)
| Agent | Role |
|-------|------|
| **orchestrator** | Strategy, prioritization, delegation. Never implements directly. |
| **tester** | Runs the system, writes observations, validates changes |
| **reviewer** | Approval gate. Compares before/after. Read-only. |
| **scrutinizer** | Red team. Challenges assumptions, finds blind spots. |
| **auditor** | Meta-agent. Audits agent effectiveness and workflow. |

### Domain Agents (selected by bootstrap based on project type)
- **code-engineer** / **frontend-engineer** / **backend-engineer** — Implementation
- **architect** — Design decisions, repo structure, system design
- **prompt-engineer** — LLM prompt work (only for LLM projects)
- **analyst** — Pattern finding across outputs (only for data/LLM projects)
- **devops** — Infrastructure, CI/CD, deployment

### Monorepo Support

For monorepos, the framework supports **scoped workstreams**. Each workstream gets its own CLAUDE.md defining boundaries within the monorepo. Core agents are shared; domain agents are configured per workstream. Run `claude --agent bootstrap` with "reconfigure" to switch workstreams.

## When to Use This (and When Not To)

**Use this for:**
- Sustained improvement work on a codebase (multiple cycles over days/weeks)
- Projects where quality matters and changes should be reviewed
- Refactoring, UI overhauls, pipeline optimization, architecture improvements
- Any work where you want systematic progress tracking

**Don't use this for:**
- Quick one-off bug fixes (just fix it directly)
- Simple documentation updates
- Tasks that take less than 15 minutes
- Greenfield prototyping where you're still figuring out the structure

The framework adds coordination overhead. That overhead pays off when you're doing sustained, iterative work. For quick tasks, it's overkill.

## Examples

The `examples/dexter-snapshot/` directory contains sanitized files from a real project that used this framework for 30+ improvement cycles:
- A mature CLAUDE.md showing what fully filled-in project context looks like
- Real CHANGELOG entries showing the level of detail that works
- A real orchestrator session prompt showing how priorities are structured

## Improving the Framework Itself

The framework improves from every project that uses it. After a project has been through 10+ improvement cycles:

1. **Extract:** Run `contribute-back.md` on the project repo — an agent diffs the project's agents against the framework templates and produces a report separating **transferable** improvements from **project-specific** ones
2. **Review:** Read the report, mark each transferable item as APPROVED / DEFERRED / REJECTED
3. **Apply:** Run `apply-contributions.md` with the marked report — an agent reads the approved items from the project's source files, strips out project-specific content, and applies the patterns to the framework templates
4. **Verify:** The apply agent checks consistency across all framework files

Reports are stored in `contributions/` with the project name and date. This is how the Dexter project's 30+ cycles of learnings ended up in the framework's agent templates, and how your next project's learnings will improve it further.

```
Framework → Bootstrap → Project → Improvement cycles
    ↑                                      ↓
    ← contribute-back ← review ← apply ←──┘
```

## Key Principles

1. **Strict separation of concerns** — Each agent can only modify specific files/directories
2. **Reviewer approval gate** — No change is "done" until reviewed
3. **Session-based orchestration** — Fresh context each session with numbered priorities
4. **Self-improvement loop** — Agents suggest improvements via AGENT-IMPROVEMENTS.md
5. **Git safety** — Checkpoint before every change, revert on rejection
6. **Delegation enforcement** — Orchestrator delegates, never implements
