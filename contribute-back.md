# Contribute Back — Extract Transferable Improvements

Run this prompt on a project that has been through 10+ improvement cycles. It identifies what the project's agents learned that should be folded back into the framework templates.

Hand this to an agent with access to BOTH the project repo AND the framework repo.

---

## Your Task

Compare this project's current agent definitions, CLAUDE.md, and workflow against the framework templates at `C:/Users/EmiliosRichards/Projects/agent-team-framework`. Identify improvements that are **transferable** (would help ANY project) vs **project-specific** (only matter here).

## Step 0: Read Field Notes (if any exist)

Check `C:/Users/EmiliosRichards/Projects/agent-team-framework/contributions/` for any `field-notes-{{PROJECT_NAME}}*.md` files. These contain raw findings captured during sessions — friction points, discoveries, ideas. Use them as a starting checklist of things to investigate in the project's actual files.

## Step 1: Read Both Sets

**Project (battle-tested):**
- Read all files in `.claude/agents/`
- Read `CLAUDE.md`
- Read `WORKFLOW-GUIDE.md` (if it exists)
- Read `AGENT-IMPROVEMENTS.md` — look at approved items, they represent validated workflow fixes
- Read `CHANGELOG.md` — look for entries that changed agent behavior or workflow (not just code/prompt edits)
- Read `eval/quality_rubric.md` — look for criteria that evolved beyond the starter set
- Read `.claude/settings.json` and `.claude/settings.local.json` — look for permission patterns that evolved beyond the template

**Framework (templates):**
- Read all files in `C:/Users/EmiliosRichards/Projects/agent-team-framework/agents/`
- Read `C:/Users/EmiliosRichards/Projects/agent-team-framework/.claude/agents/bootstrap.md`
- Read all files in `C:/Users/EmiliosRichards/Projects/agent-team-framework/templates/`

## Step 2: Diff Agent by Agent

For each agent that exists in both the project and the framework, compare and list:

### Additions (in project, not in framework)
- New sections, rules, guardrails, report formats, checklists
- For each: Is this transferable or project-specific?
- Transferable = would help any project using this agent type (e.g., "timeout protection for subagent calls")
- Project-specific = only matters for this domain (e.g., "always check German naturalness in pitches")

### Modifications (exists in both but project version is more detailed)
- Sections that started generic but evolved to be more specific
- For each: What was the original? What did it become? Why? Is the improvement transferable?

### Removals (in framework but project removed it)
- Anything the project found unhelpful and dropped
- For each: Why was it removed? Should the framework remove it too?

## Step 3: Check Workflow Patterns

Look beyond individual agents at the overall workflow:

- Did the project develop any **new session patterns** not in the framework's WORKFLOW-GUIDE?
- Did the project develop any **new tracking mechanisms** (scorecards, comparison docs, A/B testing)?
- Did AGENT-IMPROVEMENTS.md surface any **workflow fixes** that should be baked into the framework?
- Did the project discover any **anti-patterns** worth warning about in the framework?
- Did the quality rubric evolve in ways that suggest the **starter rubric template** is missing dimensions?

## Step 4: Check Bootstrap Completeness

Based on what the project needed that the bootstrap didn't provide:
- Were there files the team had to create manually that the bootstrap should have generated?
- Were there questions the bootstrap should have asked but didn't?
- Were there archetype-level patterns (agent rosters, boundaries, quality dimensions) that should be updated?

## Step 5: Write the Report

Save to `C:/Users/EmiliosRichards/Projects/agent-team-framework/contributions/{{PROJECT_NAME}}-{{DATE}}.md`:

```markdown
# Contribute Back Report: {{PROJECT_NAME}}
## Date: {{DATE}}
## Cycles completed: {{N}}

## Transferable Improvements (recommended for framework)

### 1. [Short title]
- **What:** [What was added/changed in the project]
- **Where:** [Which framework file(s) should be updated]
- **Why it's transferable:** [Why any project would benefit]
- **Example from project:** [Concrete example of the improvement in action]

### 2. ...

## Project-Specific Patterns (NOT for framework)

### 1. [Short title]
- **What:** [What was added/changed]
- **Why it's project-specific:** [Why this only matters here]

## Anti-Patterns Discovered

### 1. [Short title]
- **What happened:** [What went wrong]
- **How it was fixed:** [What the project did]
- **Framework action:** [Should the framework warn about this?]

## Bootstrap Gaps

### 1. [Short title]
- **What was missing:** [What the team had to create manually]
- **Framework action:** [Should bootstrap generate this?]

## Summary

- **Transferable items:** {{N}} (recommended for framework update)
- **Project-specific items:** {{N}} (stay in project only)
- **Anti-patterns:** {{N}} (consider adding warnings)
- **Bootstrap gaps:** {{N}} (consider adding to bootstrap)
```

## Important

- Be honest about what's transferable vs project-specific. Not every improvement belongs in the framework — that's how frameworks get bloated.
- Focus on PATTERNS, not content. "Add a post-delegation checklist" is transferable. "Add a check for German umlauts" is not.
- When in doubt, flag it as "borderline" and let the user decide.
- **Include source file paths** for every transferable item — the apply step needs to read the actual implementation, not just your summary of it.

## What Happens Next

1. The user reads this report
2. For each transferable item, the user marks it as **APPROVED**, **DEFERRED**, or **REJECTED** (edit the report directly)
3. The user runs `apply-contributions.md` with the marked-up report — an agent reads the approved items, extracts the transferable patterns from the project's source files, and applies them to the framework templates
4. The apply agent verifies consistency across all framework files

This is the full loop: **project learns → contribute-back extracts → user approves → apply implements → framework improves.**
