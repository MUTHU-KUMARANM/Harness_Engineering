# Capstone Test Suite

This directory contains comprehensive pytest test suites for validating the Harness Engineering capstone project.

## Test Coverage

### System 1: Claims Intake Agent (`test_system1_claims_intake.py`)
- **29 tests** covering stop_reason-driven loop, tool integration, and dynamic decomposition
- Validates agentic loop architecture
- Verifies audit trace capability
- Confirms structured output and tool execution

**Run with**:
```bash
pytest capstone/tests/test_system1_claims_intake.py -v
```

### System 2: Retail Context Strategy (`test_system2_retail_context.py`)
- **17 tests** covering context assembly, token budgeting, and evaluation
- Validates context reduction mechanism (≥50% token reduction)
- Verifies token accounting (budget.json format)
- Confirms evaluation framework and control variants

**Run with**:
```bash
pytest capstone/tests/test_system2_retail_context.py -v
```

### System 3: Claude Code Config (`test_system3_claude_code.py`)
- **35 tests** covering configuration hierarchy, path-scoped rules, commands, and skills
- Validates CLAUDE.md with @import directives
- Verifies path-scoped rules with YAML frontmatter and glob patterns
- Confirms /review command and /deploy-check skill with context: fork

**Run with**:
```bash
pytest capstone/tests/test_system3_claude_code.py -v
```

### System 4: Multi-Shift Orchestration (`test_system4_multi_shift.py`)
- **28 tests** covering tiered state, crash recovery, and forked scratchpads
- Validates hot/warm/cold state tiers
- Verifies shift invocation pipeline
- Confirms crash recovery and scratchpad isolation (< 5 KB hot state)

**Run with**:
```bash
pytest capstone/tests/test_system4_multi_shift.py -v
```

### Capstone Integration (`test_capstone_integration.py`)
- Cross-system validation
- Structure and documentation verification
- Readiness checks for capstone submission

**Run with**:
```bash
pytest capstone/tests/test_capstone_integration.py -v
```

## Running All Tests

```bash
# Run all capstone tests
pytest capstone/tests/ -v

# Run with summary
pytest capstone/tests/ -v --tb=short

# Run with coverage
pytest capstone/tests/ --cov=capstone --cov-report=html
```

## Expected Test Results

- **System 1**: Architectural and dependency checks (all should pass)
- **System 2**: Context and evaluation framework checks (all should pass)
- **System 3**: Configuration structure checks (all should pass)
- **System 4**: State management checks (all should pass)
- **Integration**: Cross-system validation (all should pass)

**Total**: 125+ tests validating the capstone structure and rubric alignment.

## Using These Tests

These tests serve two purposes:

1. **Validation Before Run**: Verify repository structure before executing actual systems
2. **Rubric Alignment**: Confirm each system implements required components per rubric

These are **not** end-to-end execution tests. They check that the four systems are properly structured and contain the architectural components required by the rubric. 

To validate actual execution results, see `capstone/execution_guide.md` for running each system and capturing test output and artifacts.

## Notes

- Tests assume the four systems are located in their expected course repo paths
- No API calls are made by these tests (validation only)
- All paths are relative to the repository root
- Tests are designed to pass if capstone structure is sound

For detailed execution and evidence capture instructions, see `capstone/execution_guide.md`.
