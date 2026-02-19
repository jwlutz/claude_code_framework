---
description: Test-driven development workflow
argument-hint: <feature or fix to implement>
---

Implement a feature using test-driven development with subagent orchestration. Write tests first, then make them pass.

## Pre-compute Context

```bash
echo "=== TEST RUNNER ===" && (test -f pytest.ini && echo "pytest" || test -f pyproject.toml && grep -q pytest pyproject.toml && echo "pytest" || test -f package.json && node -e "const p=require('./package.json'); p.scripts?.test ? console.log('npm test') : process.exit(1)" 2>/dev/null || test -f Makefile && grep -q "^test:" Makefile && echo "make test" || echo "No test runner detected")
echo "=== TEST DIRS ===" && (ls -d tests/ test/ spec/ __tests__/ 2>/dev/null || echo "No test directory found")
echo "=== VIBE CONTEXT ===" && (test -f .vibe/understanding.md && head -20 .vibe/understanding.md || echo "No understanding — consider /vibe:init")
echo "=== VIBE LEARNINGS ===" && (test -f .vibe/learnings.md && cat .vibe/learnings.md || echo "No learnings yet")
```

## Phase 1: Understand

Spawn an **explorer** subagent via the Task tool:

```
Use the Task tool with subagent_type="vibe:explorer" (or "general-purpose" if unavailable) to:
- Read .vibe/understanding.md and .vibe/learnings.md for project context
- Understand what we're building or fixing: [TASK]
- Find existing test files and read them to understand test patterns/conventions
- Identify the test framework in use and look up its API via Context7 if available
- Note which files will need tests and which will need implementation
```

Review the explorer's findings. Present understanding to user.

## Phase 2: Write Tests

Spawn an **engineer** subagent via the Task tool to write tests:

```
Use the Task tool with subagent_type="vibe:engineer" (or "general-purpose" if unavailable) to:
- Write test cases for: [TASK]
- Follow the project's existing test patterns from explorer findings
- If Context7 is available, look up the test framework API before writing tests
- Cover: happy path, edge cases, error cases
- DO NOT write any implementation code
- DO NOT run the tests — they should fail (implementation doesn't exist yet)
- Output: list of test files created and what each test verifies
```

Show the user the tests and explain what each test verifies.

**STOP and wait for user approval of the tests.**

## Phase 3: Commit Tests

This phase runs in the **main conversation** (needs git interaction).

After approval, commit the tests:
```bash
git add [test files] && git commit -m "test: add tests for [feature]"
```

## Phase 4: Implement

Spawn an **engineer** subagent via the Task tool:

```
Use the Task tool with subagent_type="vibe:engineer" (or "general-purpose" if unavailable) to:
- Write the minimum code to make the tests pass
- DO NOT edit the test files
- Follow project patterns from .vibe/understanding.md
- If Context7 is available, look up library APIs before using them
- Keep it simple — only enough code to pass tests
- Output: list of files changed
```

## Phase 5: Iterate

Spawn a **tester** subagent via the Task tool:

```
Use the Task tool with subagent_type="vibe:tester" (or "general-purpose" if unavailable) to:
- Run the test suite: [test command]
- Report pass/fail with counts and failure details
```

If tests fail:
- Read the failure output carefully
- Fix the implementation (NOT the tests) — spawn another engineer subagent if needed
- Run tester again
- **Keep iterating until all tests pass. Do not give up.**

## Phase 6: Complete

This phase runs in the **main conversation**.

When all tests pass:

```
TDD cycle complete

Tests written: [count]
Tests passing: [count]
Files changed: [list]

Ready to commit? [yes / show diff / refactor first]
```

Commit the implementation:
```bash
git add [implementation files] && git commit -m "feat: implement [feature]"
```
