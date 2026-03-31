# Apply Contributions — Implement Approved Framework Improvements

Run this AFTER reviewing a contribute-back report. You mark which items are approved, then hand this prompt to an agent with write access to the framework repo. It implements the approved changes across all relevant framework files.

---

## Your Task

Read the contribute-back report at `contributions/{{REPORT_FILE}}`. The user has marked which items are approved for framework inclusion. Implement ONLY the approved items.

## Step 1: Read the Report + Source Material

1. Read the contribute-back report — identify all items marked as approved/transferable
2. For each approved item, read the **source file** in the project repo where the improvement exists. You need to see the actual implementation, not just the description. The report should reference which project files contain the improvement.
3. Read the **target framework file** that needs updating

## Step 2: Plan the Changes

Before editing anything, write a change plan listing:

```markdown
## Change Plan

### Change 1: [Title from report]
- **Source:** [project file + relevant lines/sections]
- **Target:** [framework file]
- **What to add/modify:** [Specific description — not "update the orchestrator" but "add a Post-Delegation Checklist section after the Delegation Rules section with these 4 items: ..."]
- **What to leave out:** [Any project-specific content from the source that should NOT be copied]

### Change 2: ...
```

Present this plan and get user confirmation before proceeding.

## Step 3: Implement

For each approved change:

1. Read the target framework file in full
2. Read the source from the project repo
3. **Extract the transferable pattern** — strip out project-specific references (file paths, domain terms, tech stack details) and keep the structural pattern. Replace specifics with `[BOOTSTRAP FILLS]` or generic descriptions.
4. Add the new content to the framework file in the appropriate location
5. Ensure it doesn't duplicate existing content — if the framework already has a weaker version of the same concept, strengthen it rather than adding a second copy

### Rules for Implementation

- **Add, don't replace** — Preserve everything already in the framework file. Add new sections, expand existing ones.
- **Keep it generic** — No project-specific file paths, domain terms, tech stack references. Use `[BOOTSTRAP FILLS]` for anything the bootstrap should customize per project.
- **Maintain consistency** — Match the existing formatting, heading levels, and style of the framework file.
- **Preserve comments** — Keep `<!-- Bootstrap: ... -->` annotations where they help.
- **One pattern per section** — Don't stuff multiple unrelated improvements into one section.

## Step 4: Verify

After implementing all changes:

1. List every framework file that was modified
2. For each file: show a before/after summary (what sections were added or expanded)
3. Run a quick consistency check:
   - Do all agent templates still have frontmatter?
   - Do all agent templates still have the "Mandatory at Every Task Start" section?
   - Do all agent templates still have the "Agent Improvement Suggestions" section?
   - Does the bootstrap reference any new template sections it should know about?
   - Does the README mention any new files or processes?

## Step 5: Update Tracking

1. Add a note to the contribute-back report: "Applied on {{DATE}}. Changes implemented in: [list of files]."
2. If any items from the report were NOT implemented (user deferred or you encountered issues), note why.

## Output

Write a summary:
```markdown
## Applied Contributions Summary

**Source:** {{REPORT_FILE}}
**Date applied:** {{DATE}}

### Changes Made
1. [Framework file]: [What was added — one line]
2. ...

### Items Deferred
1. [Item]: [Why]

### Verification
- All agent templates have frontmatter: YES/NO
- All agent templates have mandatory sections: YES/NO
- Bootstrap updated for new sections: YES/NO
- README updated: YES/NO
```
