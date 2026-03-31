# Dexter Pipeline — Project Context
# EXAMPLE: This is a sanitized snapshot of a real project's CLAUDE.md after 30+ cycles.
# Use this as a reference for what a mature CLAUDE.md looks like.

## What This Project Does
This pipeline processes B2B sales contacts through several stages:
1. **Export**: Pulls call journeys from Postgres, groups by phone number
2. **Classification**: Uses an LLM to classify call outcomes and extract strategy fields from transcripts
3. **Reference Matching**: Finds nearest existing customer facility by geographic distance
4. **Template Pitch**: Fills pitch templates with placeholders (fallback)
5. **Generative Pitch**: Uses an LLM to generate fully original pitches from strategy fields
6. **Final Export**: Combines everything into a single CSV for sales agents

## Key Directories
- `prompts/` — LLM prompts (classification, pitch generation). Prompt-engineer edits these.
- `config/` — Pipeline configuration (config.yml, taxonomy.json, referenzen.csv). Do not modify without user approval.
- `config/product.md` — Product context: what we sell, USPs, competitor positioning, objection handling.
- `scripts/` — Pipeline scripts (01-05 numbered steps). Code-engineer edits these.
- `eval/results/` — Output from test runs and agent reports (timestamped)
- `eval/regression_phones.csv` — 20 curated test contacts for consistent before/after comparison
- `eval/quality_rubric.md` — Evolving quality criteria
- `CHANGELOG.md` — Record of all changes
- `IMPROVEMENTS.md` — Prioritised backlog
- `AGENT-IMPROVEMENTS.md` — Self-improvement suggestions from agents

## The Agent Team

| Agent | Responsibility | Modifies |
|-------|---------------|----------|
| **orchestrator** | Strategy, research, coordination, delegation | CHANGELOG.md, IMPROVEMENTS.md |
| **prompt-engineer** | LLM prompt improvements, sales copy | `prompts/` only |
| **code-engineer** | Pipeline code, data formatting, logic | `scripts/` only |
| **tester** | Runs pipeline on samples, writes observation reports | `eval/results/` only |
| **analyst** | Deep-dives into outputs, finds patterns | `eval/results/` only |
| **reviewer** | Quality gate — compares before/after, approves or rejects | Nothing (read-only) |
| **scrutinizer** | Red team, innovation, challenges assumptions | `eval/adversarial/` and `eval/results/` |
| **auditor** | Meta-improvement — audits agent effectiveness | `eval/results/` only |

## Rules for All Agents
- Always read this file at the start of a task
- Never modify files outside your designated directories
- All changes require reviewer approval before being considered done
- Always run tests before AND after making changes
- Log every change in CHANGELOG.md
- Git checkpoint before every change, commit after every approved change
- One focused change per cycle
- Agent improvement suggestions go to AGENT-IMPROVEMENTS.md under "Pending"

## How to Run
```bash
python scripts/01_export.py --run eval/results/run-<timestamp> --phones "+49XXXXX,+49YYYYY"
python scripts/02_classify.py --run eval/results/run-<timestamp>
python scripts/03_match_reference.py --run eval/results/run-<timestamp>
python scripts/04_generate_pitches.py --run eval/results/run-<timestamp>
python scripts/04b_enhance_pitches.py --run eval/results/run-<timestamp>
python scripts/05_export_final.py --run eval/results/run-<timestamp>
python scripts/_build_review.py eval/results/run-<timestamp>
```

## How to Test
Observation-driven approach — no pre-defined expected outputs. Agents run the pipeline on sample contacts and write detailed observations about what worked and what didn't.

Regression test set: `eval/regression_phones.csv` — 20 curated contacts. Always test against these.

## Key Design Decisions
- Appointment contacts are excluded entirely — pipeline targets missed opportunities
- Do-not-call requires specific evidence patterns (not just "no interest")
- Always generate a pitch regardless of call count — sales agents decide whether to use it
- Reference proximity <=50km uses "near you" framing, >50km uses neutral framing
- Two separate pitch generation prompts that evolve independently (psychology-driven vs conventional)

## Agent Safety Constraints

### Hard Boundaries (NEVER violate)
- **Database**: READ ONLY. No writes.
- **Production data**: Never write to `runs/` directories. All test outputs go to `eval/results/` only.
- **Cost control**: Never change the LLM model. Never add new LLM API calls. Never run on more than 20 contacts per test. These require explicit user approval.
- **Config**: Never modify taxonomy or reference data without user approval.
- **Agent definitions**: Never modify `.claude/agents/` without user approval.
- **Git safety**: Always checkpoint before changes. Always revert on rejection.

### Escalation Protocol
Agents ask the user before: architectural changes, cost-impacting changes, structural changes, anything uncertain.
Agents use own judgment for: prompt wording, code formatting, reports, small test batches, error handling.
