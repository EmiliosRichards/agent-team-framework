# Orchestrator Session 2 Prompt — Example
# EXAMPLE: Shows how to incorporate findings from session 1 into the next session.
# This is from the manuav client-portal project.

## Context

Session 1 completed 5 cycles: font fix (Geist replaces Arial), component library foundation (cn utility, status-config.ts), and three core UI primitives (Badge, Button, Card). All approved by reviewer. Read `eval/results/session-state.md` and `eval/results/debrief.md` for full details.

### Issues found during session 1 review

1. **Tester was never launched.** No visual testing happened. Scorecard scores for error/loading states (5.0) and accessibility (~5.5) are fabricated — reviewer scored dimensions it didn't actually verify. This must not happen again.

2. **No migration happened.** Five cycles built foundations but zero existing code was changed. The old duplicated badge/button/card patterns are still in the codebase. The real test is migration — that's where breakage risk lives.

3. **Orchestrator fixed reviewer conditions itself.** When the reviewer said "APPROVED WITH CONDITIONS," the orchestrator fixed the issues inline instead of sending them back to the frontend-engineer. For small fixes this is OK, but it violates the delegation rules and should be sent back to the agent who owns those files.

4. **Too many comments in components.** The new Button component has a 20-line docblock. Components should have brief comments (3-5 lines max). Migration notes go in CHANGELOG, not in code.

5. **Dev server was never started.** The app needs both core-api and client-portal running for visual testing. This is now a pre-session requirement.

---

## Pre-Session Setup (user does this before starting the orchestrator)

```bash
# Terminal 1: Start core API
npm run dev:core-api

# Terminal 2: Start client portal  
npm run dev:client-portal

# Terminal 3: Start orchestrator
claude --agent orchestrator
```

Verify both are running before proceeding. The tester needs `localhost:3000` (or whatever port) accessible for Playwright screenshots.

---

## Session 2 Priorities

### 1. Baseline screenshots (BEFORE any changes)

Before touching any code, have the tester capture screenshots of every main page using Playwright. These are the "before" images for comparison after migration.

Pages to capture:
- Dashboard (both superuser and org user views)
- Lead detail page
- Settings
- Any other main routes

Save to `eval/results/screenshots/baseline/`. This is the visual reference for the rest of the session.

### 2. Migrate existing code to new components (this is the main work)

Start replacing duplicated inline patterns with the new Badge, Button, and Card components. Prioritize by blast radius:

- **Badges first** — duplicated across 5+ files, lowest risk (visual only)
- **Buttons second** — 102 inline instances, moderate risk (event handlers)
- **Cards third** — 137 inline containers, higher risk (layout structure)

For each migration batch:
- Frontend-engineer makes the changes
- Tester captures "after" screenshots of affected pages
- Reviewer compares before/after screenshots + code diff
- Only approve if visual output is identical (migration should be invisible to users)

### 3. Trim component comments

After migration stabilizes, have the frontend-engineer trim docblocks to 3-5 lines max. Move migration notes and pattern documentation to CHANGELOG.md or a docs/ file.

### 4. Reviewer must only score what was tested

**New rule for this session:** The reviewer must mark any quality dimension as "N/A — not tested" if it wasn't actually verified. If the tester didn't run Playwright, visual dimensions are N/A. If no accessibility audit was done, accessibility is N/A. No more fabricated 5.0 scores.

---

## Rules for this session

- **Tester runs at least once per session** — baseline screenshots before changes, comparison screenshots after
- **Dev server must be running** before tester launches Playwright
- **Orchestrator does NOT fix reviewer conditions itself** — send them back to the frontend-engineer
- **Frontend-engineer can READ `apps/core-api/` for understanding data shapes** but must NOT modify it. If a UI fix requires an API change, flag it.
- **Migration changes should be visually invisible** — the user shouldn't notice anything changed. Same layout, same colors, same behavior. Just cleaner code underneath.
- Delegate everything. Don't edit code yourself.
