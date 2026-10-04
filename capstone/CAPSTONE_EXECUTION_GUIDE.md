# Capstone Execution Guide — Harness Engineering

## Overview

This guide provides step-by-step instructions for building, running, and verifying all four systems for the Harness Engineering capstone project. Each system is self-contained but builds upon the architectural patterns of the others.

---

## Prerequisites

### System Requirements
- **Python 3.11+**
- **git** and a terminal (macOS, Linux, or WSL on Windows)
- **ANTHROPIC_API_KEY** exported in environment (Systems 1, 2, and 4 require API access)

### Cost Estimate
Running all four systems against the live Claude API costs approximately **$1–$5 total**:
- System 1: ~$0.50 (8 claims processing)
- System 2: ~$1.50 (6 evaluations + control variant)
- System 3: ~$0.00 (validator only, no API calls)
- System 4: ~$1.50–$2.00 (shift processing, can run offline with `--recorded-response`)

### API Setup
```bash
# Export your Anthropic API key
export ANTHROPIC_API_KEY="your-key-here"

# Verify it's set
echo $ANTHROPIC_API_KEY
```

---

## Path to Course Repository

The four systems live in the course repo (cd15315 Claude AI Engineer Harness Engineering). Point a variable at your local clone:

```bash
# Adjust this path to your actual clone location:
SYSTEMS="../../path/to/cd15315 Claude AI Engineer Harness Engineering"
```

The systems are located at:
1. `"$SYSTEMS/Build a Claims Intake Agent with a stop_reason-Driven Loop/exercises/03-dynamic-decomposition/solution"`
2. `"$SYSTEMS/Engineer a Long-Conversation Context Strategy for a Retail Support Copilot/04-assemble-and-locate/solution"`
3. `"$SYSTEMS/Configure Claude Code for a Multi-Surface Monorepo Team/04-plan-mode-and-explore-decision-doc/solution"`
4. `"$SYSTEMS/Build a Multi-Shift Quality Monitoring System with Claude Orchestration/04-fork-scratchpad/solution"`

---

## System 1: Insurance Claims Intake Agent

**Exercises**: Stop_reason-driven loop, tool execution, dynamic decomposition
**Skills**: Agentic loops, tool design, structured output
**Runtime**: ~2–3 minutes
**Cost**: ~$0.50

### Setup & Run

```bash
cd "$SYSTEMS/Build a Claims Intake Agent with a stop_reason-Driven Loop/exercises/03-dynamic-decomposition/solution"

# Create virtual environment
python3 -m venv .venv
source .venv/bin/activate

# Install dependencies
pip install -e ".[dev]"

# Run test suite (expect 29 passed)
pytest tests/ -v > ../../../../../../capstone/evidence/system1_tests.log 2>&1

# Process all claims (8 fixtures)
python -m claims_intake.run --all > ../../../../../../capstone/evidence/system1_run.log 2>&1

# The run creates: runs/<timestamp>/
# Key artifacts:
# - runs/<timestamp>/summary.md          (claim outcomes table)
# - runs/<timestamp>/traces/*.jsonl      (turn-by-turn stop_reason)
# - runs/<timestamp>/queues/            (routing decisions)
```

### Evidence to Capture

```bash
# From the test run
cat capstone/evidence/system1_tests.log

# From the run output
cd "Build a Claims Intake Agent with a stop_reason-Driven Loop/exercises/03-dynamic-decomposition/solution"
ls -la runs/
# Copy the most recent run:
cp runs/<timestamp>/summary.md ../../../../../../capstone/evidence/system1_summary.md
cp runs/<timestamp>/traces/*.jsonl ../../../../../../capstone/evidence/system1_trace_sample.jsonl
```

### What Success Looks Like

- ✅ 29 tests pass
- ✅ `summary.md` shows 8 claims processed (with routing/escalation outcomes)
- ✅ At least one trace file shows turn-by-turn `stop_reason` progression
- ✅ Traces show loop terminating on `end_turn` or escalation

---

## System 2: Retail Support Context Strategy

**Exercises**: Prune tool output, case-facts block, compress with budget, assemble & locate
**Skills**: Context management, token budgeting, prompt assembly
**Runtime**: ~3–5 minutes
**Cost**: ~$1.50

### Setup & Run

```bash
cd "$SYSTEMS/Engineer a Long-Conversation Context Strategy for a Retail Support Copilot/04-assemble-and-locate/solution"

# Create virtual environment
python3 -m venv .venv
source .venv/bin/activate

# Install dependencies
pip install -e ".[dev]"

# Run test suite (expect 17 passed)
pytest tests/ -v > ../../../../../../capstone/evidence/system2_tests.log 2>&1

# Run all evaluations (6 evals + control variant)
python -m retail_context.run --all > ../../../../../../capstone/evidence/system2_run.log 2>&1

# The run creates: runs/<run_id>/
# Key artifacts:
# - runs/<run_id>/context.md           (assembled context)
# - runs/<run_id>/budget.json          (token accounting)
# - runs/<run_id>/eval.jsonl           (6 evaluation results)
# - runs/<run_id>/control.jsonl        (control variant)
```

### Evidence to Capture

```bash
# From the test run
cat capstone/evidence/system2_tests.log

# From the run output
cd "Engineer a Long-Conversation Context Strategy for a Retail Support Copilot/04-assemble-and-locate/solution"
ls -la runs/
# Copy the most recent run:
cp runs/<run_id>/budget.json ../../../../../../capstone/evidence/system2_budget.json
cp runs/<run_id>/eval.jsonl ../../../../../../capstone/evidence/system2_eval.jsonl
cp runs/<run_id>/context.md ../../../../../../capstone/evidence/system2_context.md
```

### What Success Looks Like

- ✅ Tests pass (17 tests)
- ✅ `budget.json` shows ≥50% reduction from ~47k-token baseline
- ✅ Evaluation results show ≥5 of 6 questions answered correctly
- ✅ Control variant (without persistent facts block) shows regression on at least one question

---

## System 3: E-Commerce Team Claude Code Config

**Exercises**: Modular CLAUDE.md, path-scoped rules, `/review` command, `/deploy-check` skill
**Skills**: Claude Code configuration, YAML frontmatter, command/skill design
**Runtime**: ~1 minute
**Cost**: ~$0.00 (validator only)

### Setup & Run

```bash
cd "$SYSTEMS/Configure Claude Code for a Multi-Surface Monorepo Team/04-plan-mode-and-explore-decision-doc/solution"

# Create virtual environment
python3 -m venv .venv
source .venv/bin/activate

# Install dependencies
pip install -e ".[dev]"

# Run test suite (expect 35 passed)
pytest tests/ -v > ../../../../../../capstone/evidence/system3_tests.log 2>&1

# Validate the configuration (should print OK and exit 0)
python -m ecommerce_team_config . > ../../../../../../capstone/evidence/system3_validator.log 2>&1
echo "Exit code: $?" >> ../../../../../../capstone/evidence/system3_validator.log
```

### Evidence to Capture

```bash
# From the test run
cat capstone/evidence/system3_tests.log

# From the validator
cat capstone/evidence/system3_validator.log

# File structure evidence
cd "Configure Claude Code for a Multi-Surface Monorepo Team/04-plan-mode-and-explore-decision-doc/solution"
find .claude -type f -name "*.md" | head -20

# Show the CLAUDE.md file
cat CLAUDE.md > ../../../../../../capstone/evidence/system3_CLAUDE.md

# Show path-scoped rules
cat .claude/rules/api.md > ../../../../../../capstone/evidence/system3_rules_api.md
cat .claude/rules/tests.md > ../../../../../../capstone/evidence/system3_rules_tests.md

# Show the command
cat .claude/commands/review.md > ../../../../../../capstone/evidence/system3_command_review.md

# Show the skill
cat .claude/skills/deploy-check/SKILL.md > ../../../../../../capstone/evidence/system3_skill_deploy.md
```

### What Success Looks Like

- ✅ Validator prints `OK` and exits with code 0
- ✅ Tests pass (35 tests)
- ✅ `CLAUDE.md` contains `@import` directives
- ✅ `.claude/rules/*.md` files have YAML frontmatter with `paths:` globs
- ✅ `/review` command exists and is properly configured
- ✅ `/deploy-check` skill has `context: fork` and read-only allowed-tools

---

## System 4: Multi-Shift Quality Monitoring

**Exercises**: Tiered state, invocation pipeline, crash recovery, forked scratchpads
**Skills**: Layer 3 orchestration, state management, session isolation
**Runtime**: ~1–2 minutes (offline with recorded response)
**Cost**: ~$0.00 (using `--recorded-response`)

### Setup & Run

```bash
cd "$SYSTEMS/Build a Multi-Shift Quality Monitoring System with Claude Orchestration/04-fork-scratchpad/solution"

# Create virtual environment
python3 -m venv .venv
source .venv/bin/activate

# Install dependencies
pip install -e ".[dev]"

# Run test suite (expect 28 passed)
pytest tests/ -v > ../../../../../../capstone/evidence/system4_tests.log 2>&1

# Initialize the warm store (one-time setup)
python -c "
import json
from pathlib import Path
from shift_monitor.warm import WarmStore

w = WarmStore(Path('data/warm.sqlite'))
w.initialize()
w.insert_many(json.load(open('fixtures/defects.json')))
print('Warm store initialized')
" > ../../../../../../capstone/evidence/system4_warm_init.log 2>&1

# Run a shift offline (no API spend)
python -m shift_monitor run-shift \
  --shift C \
  --warm-db data/warm.sqlite \
  --recorded-response fixtures/recorded_responses/shift_C_2026-04-30.json \
  > ../../../../../../capstone/evidence/system4_run.log 2>&1

# Capture the hot-state file size
ls -lh data/hot_state.json >> ../../../../../../capstone/evidence/system4_run.log
```

### Evidence to Capture

```bash
# From the test run
cat capstone/evidence/system4_tests.log

# From the shift run
cat capstone/evidence/system4_run.log

# Hot state file
cd "Build a Multi-Shift Quality Monitoring System with Claude Orchestration/04-fork-scratchpad/solution"
cp data/hot_state.json ../../../../../../capstone/evidence/system4_hot_state.json

# File size check
ls -lh data/hot_state.json
wc -c data/hot_state.json

# If forking: show isolated scratchpads
cat data/shift_scratchpad.jsonl >> ../../../../../../capstone/evidence/system4_scratchpads.jsonl
```

### What Success Looks Like

- ✅ Tests pass (28 tests)
- ✅ Shift output prints to stdout with summary
- ✅ `hot_state.json` is < 5 KB
- ✅ A line is appended to `shift_scratchpad.jsonl`
- ✅ Forked investigation (if executed) stays isolated from main state

---

## Verification Checklist

Before writing the reflection brief, verify all four systems:

```bash
# System 1: Claims Intake
[ ] 29 tests pass
[ ] summary.md captured
[ ] trace file captured with stop_reason progression
[ ] Loop termination logic identified

# System 2: Retail Support Context
[ ] 17 tests pass
[ ] budget.json shows ≥50% token reduction
[ ] ≥5 of 6 eval questions passed
[ ] Control variant regression evident

# System 3: E-Commerce Team Config
[ ] 35 tests pass
[ ] Validator exits 0 with OK
[ ] CLAUDE.md with @import captured
[ ] Path-scoped rules with YAML frontmatter captured
[ ] /review command configured
[ ] /deploy-check skill with context: fork captured

# System 4: Multi-Shift Quality
[ ] 28 tests pass
[ ] hot_state.json < 5 KB
[ ] Shift output captured
[ ] Recovery/fork logic demonstrated
```

---

## Reflection Brief

Use the template at `capstone/reflection-brief-template.md` to write your defense of architectural trade-offs. Every answer must cite specific artifacts from your runs:

- Run IDs, timestamps, and file paths
- Token counts from budget.json
- Test counts and pass rates
- Claim outcomes and routing decisions
- Query/response examples

---

## Optional: Advanced Runs

### Run System 1 with Claude Sonnet 4.6
Compare against Haiku baseline to see how model strength affects routing:

```bash
cd "$SYSTEMS/Build a Claims Intake Agent with a stop_reason-Driven Loop/exercises/03-dynamic-decomposition/solution"
export ANTHROPIC_MODEL="claude-sonnet-4-6"
python -m claims_intake.run --all
```

**Compare**: Turn counts, escalation rates, cost, and routing decisions.

### Break System 2 on Purpose
Remove the case-facts block to understand degradation:

```bash
cd "$SYSTEMS/Engineer a Long-Conversation Context Strategy for a Retail Support Copilot/04-assemble-and-locate/solution"
# Edit retail_context/assemble.py to skip case-facts
python -m retail_context.run --all
# Compare eval pass rate vs. baseline
```

### Exercise System 4's Fork Path End-to-End
Modify the shift runner to fork and investigate two competing hypotheses:

```bash
cd "$SYSTEMS/Build a Multi-Shift Quality Monitoring System with Claude Orchestration/04-fork-scratchpad/solution"
# Edit shift_monitor/run_shift.py to call fork_investigation() twice
python -m shift_monitor run-shift --shift C --warm-db data/warm.sqlite
# Verify both forks' scratchpads stay isolated
```

---

## Troubleshooting

### ImportError: No module named 'anthropic'
```bash
# Ensure you're in the correct venv and have installed dev extras
pip install -e ".[dev]"
```

### ANTHROPIC_API_KEY not found
```bash
# Export it before running
export ANTHROPIC_API_KEY="sk-..."
# Verify
echo $ANTHROPIC_API_KEY
```

### Tests fail with "fixture not found"
```bash
# Ensure you're in the solution/ directory, not a parent
pwd  # should end with /solution
ls -la fixtures/
```

### System 4: Cannot initialize warm store
```bash
# Ensure the directory exists
mkdir -p data
# Then run the init command again
python -c "from shift_monitor.warm import WarmStore; ..."
```

---

## Next Steps

1. **Execute all four systems** following the sections above
2. **Capture all evidence** in `capstone/evidence/`
3. **Document file paths and artifact locations**
4. **Write the reflection brief** citing your actual run results
5. **Commit everything** to the repository

Good luck! 🚀
