# Where to Put Improvements You Discover

As you work with the agent team, you'll notice things that need fixing — workflow gaps, agent mistakes, missing rules, better approaches. This guide tells you where to put each type of finding so it actually gets addressed.

---

## Quick Reference

| What you noticed | Where to put it | Example |
|---|---|---|
| "Next session should do X" | **Session prompt** | "Start migrating old badges to new components" |
| "The agents keep doing X wrong" | **AGENT-IMPROVEMENTS.md** | "Reviewer scores dimensions it didn't actually test" |
| "This rule should always apply" | **CLAUDE.md** | "Tester must run at least once per session" |
| "This agent's definition needs changing" | **AGENT-IMPROVEMENTS.md** + note which `.md` file | "Tester should include dev server check in pre-flight" |
| "The framework templates should be better" | **contribute-back.md** process | "Bootstrap should ask about dev server setup" |

---

## Detailed Guide

### Session Prompt (`eval/results/orchestrator-prompt-sessionN.md`)

**Use for:** Things the orchestrator should prioritize next session.

These are ephemeral — they apply to one session and get replaced by the next session's prompt. Good for:
- Redirecting focus ("switch from building components to migrating old code")
- One-time tasks ("capture baseline screenshots before making changes")
- Incorporating findings from the last session ("tester wasn't used last time — fix that")
- Setting constraints for a specific session ("only work on the dashboard page this session")

**Example:**
> The tester was never launched in session 1. This session, the tester MUST capture baseline screenshots before any code changes, and comparison screenshots after each migration batch.

---

### AGENT-IMPROVEMENTS.md

**Use for:** Systemic workflow problems that affect how agents operate across sessions.

These persist until the user reviews and decides (approve/defer/reject). Good for:
- Agent behavior that's consistently wrong ("reviewer fabricates scores for dimensions it didn't test")
- Delegation flow issues ("orchestrator fixes reviewer conditions itself instead of sending back to the engineer")
- Missing capabilities ("tester should know how to start the dev server")
- Process gaps ("no visual testing is happening because nobody starts the app")

**Format:**
```markdown
### AI-XX: [Agent] — [Short description]
- **Suggested by:** [you or which agent], session N
- **Proposed change:** [What should change in the agent's .md file or workflow]
- **Why:** [What went wrong because this wasn't in place]
```

**Who reviews:** You, periodically (every 3-5 sessions). The orchestrator will also present accumulated suggestions.

---

### CLAUDE.md

**Use for:** Permanent rules and constraints that every agent must follow, every session.

Changes to CLAUDE.md go through the reviewer (it's the single source of truth). Good for:
- Safety rules ("never modify `apps/core-api/` without explicit approval")
- Environmental requirements ("dev server must be running before visual testing")
- Scope definitions ("frontend-engineer reads `core-api/` for data shapes but doesn't modify it")
- Design decisions that are settled ("we use CVA for component variants, not conditional classes")

**Requires:** Reviewer approval for factual updates. User approval for rule/constraint changes.

---

### Agent Definition Files (`.claude/agents/*.md`)

**Use for:** Changing how a specific agent behaves.

Don't edit these directly during a session. Instead:
1. Write the suggestion in AGENT-IMPROVEMENTS.md
2. Note which agent file needs changing
3. The user reviews and approves
4. The change is made (by you or the orchestrator) and takes effect next session

**Example finding:** "The tester never starts the dev server before trying to take screenshots."
**Goes in AGENT-IMPROVEMENTS.md as:** "AI-XX: Tester — Add dev server startup verification to pre-flight checks. The tester should verify `localhost:3000` is responding before attempting Playwright screenshots."

---

### Framework Templates (agent-team-framework repo)

**Use for:** Improvements that would benefit ANY project using the framework, not just this one.

Don't modify the framework during a project session. Instead, after a project has been through 10+ cycles, run the `contribute-back.md` process to extract transferable learnings.

**Example:** "Every app project needs dev server startup as a pre-session requirement. The bootstrap should ask about this during setup and add it to the WORKFLOW-GUIDE."

---

## What NOT to Use

### NOT the Bootstrap

The bootstrap is for setup and workstream switching. Don't use it for:
- Fixing agent behavior → use AGENT-IMPROVEMENTS.md
- Adding session priorities → use session prompts
- Adding project rules → use CLAUDE.md
- Tweaking individual agent definitions → propose in AGENT-IMPROVEMENTS.md

### NOT Report Footers or Memory Files

Agents sometimes put "Self-Improvement Suggestion" in their report footers or agent memory files. These get lost. Everything goes to AGENT-IMPROVEMENTS.md — that's the one place that gets reviewed.

---

## Real Example: Session 1 Findings → Where They Go

After session 1 of a UI project, these issues were found:

| Finding | Where it goes | Why |
|---|---|---|
| Reviewer scored accessibility at 5.5 without testing it | AGENT-IMPROVEMENTS.md | Systemic — reviewer needs a rule change |
| Tester was never launched | Session 2 prompt + CLAUDE.md | Session 2 prompt fixes it immediately; CLAUDE.md makes it permanent |
| No migration of old code happened | Session 2 prompt | Next session's priority |
| Orchestrator fixed reviewer conditions itself | AGENT-IMPROVEMENTS.md | Delegation rule violation — systemic |
| Components have too many comments | Session 2 prompt | Cleanup task for next session |
| Dev server needs to be running for visual testing | CLAUDE.md + WORKFLOW-GUIDE | Permanent pre-session requirement |
| Frontend-engineer needs read access to core-api | CLAUDE.md | Scope definition — permanent |
