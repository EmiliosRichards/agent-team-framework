# Orchestrator Session Prompt — Example
# EXAMPLE: Shows what a real session prompt looks like. This was Session 11.

## Context
Read CLAUDE.md for full project context. This session follows a housekeeping session where agent definitions were updated and scrutinizer/auditor ran.

### What happened last session
- Reference handling fix (both versions)
- Chase v3.0 Brücke rearchitecture
- mobile_app counter for DM10 technical barriers
- AP name formality rule
- A/B test confirmed improvements

### Items left incomplete
1. Chase CTA/autonomy integration — autonomy still a separate sentence, should be built into CTA
2. DM example rewrites — few-shot examples still show old stacked pattern
3. Chase Wert-Signal dropout — some pitches go Problem→CTA with no product mention

---

## Session priorities

### 1. Production Validation Pass — Do this FIRST
Before more feature work, validate production safety:
- **1a.** DNC consistency — run classification 3x on same contacts, flags must be identical
- **1b.** Appointment detection — pull known appointment contacts, verify all get excluded
- **1c.** Scale test (50 contacts) — scan for systemic issues
- **1d.** Final output review — show user sample rows

### 2. GK Follow-Through Pitch (both versions)
When a GK bypass succeeds, the agent needs a prepared DM pitch. Generate two pitches for GK contacts.

### 3. Fix Chase Wert-Signal dropout + CTA integration (Chase only)
Every pitch must mention what the product does BEFORE the CTA. Problem→Product→CTA is non-negotiable.

### 4. Rewrite Chase DM examples (Chase only)
Few-shot examples drive LLM style more than rules. Rewrite to demonstrate integrated v3.0 style.

### 5. A/B test on all changes

### 6. Self-directed improvement cycle (after 1-5)
2-3 cycles using own judgment. Produce comparison docs for user review.

## Business rules
- Validation comes FIRST — no feature work until production safety confirmed
- Scale test requires user cost approval
- Delegate everything
