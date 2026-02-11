---
description: Test-driven development workflow
argument-hint: <feature or fix to implement>
---

Implement a feature using test-driven development. Write tests first, then make them pass.

## Pre-compute Context

```bash
echo "=== TEST RUNNER ===" && (test -f pytest.ini && echo "pytest" || test -f pyproject.toml && grep -q pytest pyproject.toml && echo "pytest" || test -f package.json && node -e "const p=require('./package.json'); p.scripts?.test ? console.log('npm test') : process.exit(1)" 2>/dev/null || test -f Makefile && grep -q "^test:" Makefile && echo "make test" || echo "No test runner detected")
echo "=== TEST DIRS ===" && (ls -d tests/ test/ spec/ __tests__/ 2>/dev/null || echo "No test directory found")
echo "=== VIBE CONTEXT ===" && (test -f .vibe/understanding.md && head -20 .vibe/understanding.md || echo "No understanding — consider /vibe:init")
```

## Phase 1: Understand the Target

Read `.vibe/understanding.md` if available. Understand:
- What are we building or fixing?
- What existing tests look like (find an example test file, read it)
- What patterns and conventions the test suite uses

## Phase 2: Write Tests

Write test cases that describe the desired behavior:
- Cover the happy path
- Cover edge cases
- Cover error cases
- Use the project's existing test patterns and conventions

**DO NOT run the tests yet.** They should fail — the implementation doesn't exist.

**DO NOT write any implementation code yet.**

Show the user the tests and explain what each test verifies.

**STOP and wait for user approval of the tests.**

## Phase 3: Commit Tests

After approval, commit the tests:
```bash
git add [test files] && git commit -m "test: add tests for [feature]"
```

## Phase 4: Implement

Now write the minimum code to make the tests pass.

**Rules:**
- DO NOT edit the test files
- Write only enough code to pass the tests
- Follow project patterns from `.vibe/understanding.md`
- Keep it simple

## Phase 5: Iterate

Run the tests:
```bash
# Use the detected test runner
pytest [test file] 2>/dev/null || npm test 2>/dev/null || make test 2>/dev/null
```

If tests fail:
- Read the failure output carefully
- Fix the implementation (NOT the tests)
- Run again
- Repeat until all tests pass

**Keep iterating until all tests pass. Do not give up.**

## Phase 6: Complete

When all tests pass:
✓ TDD cycle complete
Tests written: [count]
Tests passing: [count]
Files changed: [list]

Ready to commit? [yes / show diff / refactor first]

Commit the implementation:
```bash
git add [implementation files] && git commit -m "feat: implement [feature]"
```
