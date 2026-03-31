# Changelog (Excerpt — Cycles 29-31)
# EXAMPLE: Shows what real changelog entries look like after 30+ cycles.

## Cycle 31 — 2026-03-31
**Change:** Self-reference bug fix, DNC two-layer hardening, ethical filter, batch variety, score recalibration
**Agent:** code-engineer (scripts), prompt-engineer (prompts)
**Files:** scripts/02_classify.py, scripts/03_match_reference.py, prompts/classify_journey.txt, prompts/generate_pitch.txt, eval/quality_rubric.md
**Reviewer:** APPROVED
**Impact:** Self-reference match eliminated (Brechtener Heide no longer matched to itself). DNC backstop catches non-deterministic LLM flags. GAMA ethical filter prevents reframing rejections as invitations. Honest quality baseline recalibrated from 9.0 to 7.5.
**Notes:** Largest single-cycle change set. Multiple independent fixes bundled because they were all data-correctness issues.

## Cycle 30 — 2026-03-30
**Change:** Fern reference variety + emotion line context-selection
**Agent:** prompt-engineer
**Files:** prompts/generate_pitch.txt
**Reviewer:** APPROVED
**Impact:** Fern reference monoculture broken — context-based selection produces different framings for different contact situations. Emotion line recurrence (St. Niklas getting same line 3 cycles in a row) resolved.

## Cycle 29 — 2026-03-30
**Change:** GK operational intelligence — Timing-Bypass + Routing-Bypass
**Agent:** prompt-engineer
**Files:** prompts/generate_pitch.txt, prompts/classify_journey.txt
**Reviewer:** APPROVED
**Impact:** GK pitches now use specific timing/routing information from transcripts instead of generic bypass patterns. Contact with "call back after 9am" now gets a pitch referencing that timing.
