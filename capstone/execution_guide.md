# Capstone Execution Guide — Harness Engineering

This guide provides step-by-step instructions for executing all four capstone systems and capturing evidence for the reflection brief. Each system is located within this repository under its corresponding project folder.

## Repository Structure

All four systems are present in this repository:

```
MUTHU-KUMARANM/Harness_Engineering/
├── Build a Claims Intake Agent with a stop_reason-Driven Loop/
│   └── exercises/03-dynamic-decomposition/solution/          ← System 1
├── Engineer a Long-Conversation Context Strategy for a Retail Support Copilot/
│   └── 04-assemble-and-locate/solution/                      ← System 2
├── Configure Claude Code for a Multi-Surface Monorepo Team/
│   └── 04-plan-mode-and-explore-decision-doc/solution/       ← System 3
└── Build a Multi-Shift Quality Monitoring System with Claude Orchestration/
    └── 04-fork-scratchpad/solution/                          ← System 4
```

## Prerequisites

- **Python 3.11+**
- **ANTHROPIC_API_KEY** environment variable (available via secrets in GitHub Actions or local export)
- **git** and terminal access
- Separate Python virtual environments per system

**Cost**: ~$1–$5 total for all four systems running against Claude API (Haiku/Sonnet defaults).

## Environment Setup

Ensure the API key is available:

```bash
# Check if ANTHROPIC_API_KEY is set
echo "API Key status: ${ANTHROPIC_API_KEY:+configured}"
```

## System Execution

### System 1: Insurance Claims Intake Agent

**Location**: `Build a Claims Intake Agent with a stop_reason-Driven Loop/exercises/03-dynamic-decomposition/solution`

**What it tests**: Stop_reason-driven agentic loop, tool execution, dynamic decomposition

**Setup & Run**:
```bash
cd "Build a Claims Intake Agent with a stop_reason-Driven Loop/exercises/03-dynamic-decomposition/solution"
python3 -m venv .venv
source .venv/bin/activate
pip install -e ".[dev]"

# Run tests (expect 29 passed)
pytest tests/ -v 2>&1 | tee system1_tests.log

# Run all claims (8 fixtures)
python -m claims_intake.run --all 2>&1 | tee system1_run.log
```

**Expected artifacts**:
- Test log: 29 tests pass
- Summary: `runs/<timestamp>/summary.md` (table of claim outcomes)
- Traces: `runs/<timestamp>/traces/*.jsonl` (stop_reason progression per claim)
- Queues: `runs/<timestamp>/queues/` (routing decisions)

**Evidence to capture**:
- Test output showing 29/29 pass
- `summary.md` showing processed claims with outcomes
- One complete trace file showing stop_reason transitions

---

### System 2: Retail Support Context Strategy

**Location**: `Engineer a Long-Conversation Context Strategy for a Retail Support Copilot/04-assemble-and-locate/solution`

**What it tests**: Context assembly, token budgeting, evaluation under constraints

**Setup & Run**:
```bash
cd "Engineer a Long-Conversation Context Strategy for a Retail Support Copilot/04-assemble-and-locate/solution"
python3 -m venv .venv
source .venv/bin/activate
pip install -e ".[dev]"

# Run tests (expect 17 passed)
pytest tests/ -v 2>&1 | tee system2_tests.log

# Run all evaluations + control
python -m retail_context.run --all 2>&1 | tee system2_run.log
```

**Expected artifacts**:
- Test log: 17 tests pass
- Context: `runs/<run_id>/context.md` (assembled context)
- Budget: `runs/<run_id>/budget.json` (token accounting showing ≥50% reduction)
- Evaluations: `runs/<run_id>/eval.jsonl` (6 evaluation results)
- Control: `runs/<run_id>/control.jsonl` (control variant without persistent facts)

**Evidence to capture**:
- Test output showing 17/17 pass
- `budget.json` showing token reduction percentage
- Eval pass count
- Example of control variant regression

---

### System 3: E-Commerce Team Claude Code Config

**Location**: `Configure Claude Code for a Multi-Surface Monorepo Team/04-plan-mode-and-explore-decision-doc/solution`

**What it tests**: Claude Code configuration hierarchy, path-scoped rules, commands, skills

**Setup & Run**:
```bash
cd "Configure Claude Code for a Multi-Surface Monorepo Team/04-plan-mode-and-explore-decision-doc/solution"
python3 -m venv .venv
source .venv/bin/activate
pip install -e ".[dev]"

# Run tests (expect 35 passed)
pytest tests/ -v 2>&1 | tee system3_tests.log

# Validate configuration (should print OK and exit 0)
python -m ecommerce_team_config . 2>&1 | tee system3_validator.log
echo "Exit code: $?" >> system3_validator.log
```

**Expected artifacts**:
- Test log: 35 tests pass
- Validator output: "OK" and exit code 0
- `.claude/` hierarchy with:
  - `CLAUDE.md` (with @import directives)
  - `.claude/standards/*.md` (always-loaded standards)
  - `.claude/rules/api.md`, `tests.md`, `react.md` (path-scoped rules with YAML frontmatter)
  - `.claude/commands/review.md` (project-scoped command)
  - `.claude/skills/deploy-check/SKILL.md` (forked skill with context: fork)

**Evidence to capture**:
- Test output showing 35/35 pass
- Validator output confirming OK
- Hierarchy diagram showing modular composition
- Example of path-scoped rule with glob patterns

---

### System 4: Multi-Shift Quality Monitoring

**Location**: `Build a Multi-Shift Quality Monitoring System with Claude Orchestration/04-fork-scratchpad/solution`

**What it tests**: Tiered state management, shift invocation pipeline, crash recovery, forked scratchpads

**Setup & Run**:
```bash
cd "Build a Multi-Shift Quality Monitoring System with Claude Orchestration/04-fork-scratchpad/solution"
python3 -m venv .venv
source .venv/bin/activate
pip install -e ".[dev]"

# Run tests (expect 28 passed)
pytest tests/ -v 2>&1 | tee system4_tests.log

# Initialize warm store (one-time)
python -c "
import json
from pathlib import Path
from shift_monitor.warm import WarmStore

w = WarmStore(Path('data/warm.sqlite'))
w.initialize()
w.insert_many(json.load(open('fixtures/defects.json')))
print('Warm store initialized')
" 2>&1 | tee system4_warm_init.log

# Run shift offline with recorded response (no API spend)
python -m shift_monitor run-shift \
  --shift C \
  --warm-db data/warm.sqlite \
  --recorded-response fixtures/recorded_responses/shift_C_2026-04-30.json \
  2>&1 | tee system4_run.log

# Check hot-state file size
ls -lh data/hot_state.json | tee -a system4_run.log
wc -c data/hot_state.json | tee -a system4_run.log
```

**Expected artifacts**:
- Test log: 28 tests pass
- Shift output: shift summary and processing log
- Hot state: `data/hot_state.json` (must be < 5 KB)
- Scratchpad: `data/shift_scratchpad.jsonl` (appended lines)

**Evidence to capture**:
- Test output showing 28/28 pass
- Shift summary output
- Hot state file size (< 5 KB)
- Scratchpad showing session isolation if fork was exercised

---

## Verification Checklist

After running all four systems, verify:

```
System 1 — Claims Intake:
  [ ] 29 tests pass
  [ ] summary.md exists with 8 claims
  [ ] At least one trace file shows stop_reason progression
  [ ] Loop terminates on end_turn or escalation

System 2 — Context Strategy:
  [ ] 17 tests pass
  [ ] budget.json shows ≥50% token reduction
  [ ] eval.jsonl shows ≥5 of 6 questions answered
  [ ] control.jsonl shows regression without persistent facts

System 3 — Claude Code Config:
  [ ] 35 tests pass
  [ ] Validator prints OK and exits 0
  [ ] CLAUDE.md contains @import blocks
  [ ] .claude/rules/*.md have YAML frontmatter with paths: globs
  [ ] /review command exists
  [ ] /deploy-check skill has context: fork

System 4 — Multi-Shift Orchestration:
  [ ] 28 tests pass
  [ ] hot_state.json < 5 KB
  [ ] Shift summary printed successfully
  [ ] shift_scratchpad.jsonl updated
```

---

## Saving Evidence to Repository

After running each system, save key artifacts to `capstone/evidence/`:

```bash
# System 1
cp "Build a Claims Intake Agent with a stop_reason-Driven Loop/exercises/03-dynamic-decomposition/solution/system1_tests.log" capstone/evidence/
cp "Build a Claims Intake Agent with a stop_reason-Driven Loop/exercises/03-dynamic-decomposition/solution/system1_run.log" capstone/evidence/
cp "Build a Claims Intake Agent with a stop_reason-Driven Loop/exercises/03-dynamic-decomposition/solution/runs/$(ls -t 'Build a Claims Intake Agent with a stop_reason-Driven Loop/exercises/03-dynamic-decomposition/solution/runs' | head -1)/summary.md" capstone/evidence/system1_summary.md

# System 2
cp "Engineer a Long-Conversation Context Strategy for a Retail Support Copilot/04-assemble-and-locate/solution/system2_tests.log" capstone/evidence/
cp "Engineer a Long-Conversation Context Strategy for a Retail Support Copilot/04-assemble-and-locate/solution/system2_run.log" capstone/evidence/
cp "Engineer a Long-Conversation Context Strategy for a Retail Support Copilot/04-assemble-and-locate/solution/runs/*/budget.json" capstone/evidence/system2_budget.json

# System 3
cp "Configure Claude Code for a Multi-Surface Monorepo Team/04-plan-mode-and-explore-decision-doc/solution/system3_tests.log" capstone/evidence/
cp "Configure Claude Code for a Multi-Surface Monorepo Team/04-plan-mode-and-explore-decision-doc/solution/system3_validator.log" capstone/evidence/

# System 4
cp "Build a Multi-Shift Quality Monitoring System with Claude Orchestration/04-fork-scratchpad/solution/system4_tests.log" capstone/evidence/
cp "Build a Multi-Shift Quality Monitoring System with Claude Orchestration/04-fork-scratchpad/solution/system4_run.log" capstone/evidence/
cp "Build a Multi-Shift Quality Monitoring System with Claude Orchestration/04-fork-scratchpad/solution/data/hot_state.json" capstone/evidence/system4_hot_state.json
```

---

## Next Steps

1. Execute each system following the instructions above
2. Capture all evidence files
3. Review the rubric requirements against actual run output
4. Fill `capstone/reflection_brief_template.md` with evidence-backed answers
5. Commit all changes to the repository

For detailed rubric requirements and reflection guidance, see `capstone/reflection_brief_template.md`.
