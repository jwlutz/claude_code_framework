---
description: Start a task with the full agent workflow
argument-hint: <what you want to build or fix>
---

Execute a development task using the explore → plan → implement → review → risk check → test workflow.

## Pre-compute Context

```bash
# Gather state so Claude doesn't waste turns discovering it
echo "=== GIT STATUS ===" && git status --short 2>/dev/null || echo "Not a git repo"
echo "=== TEST RUNNER ===" && (test -f pytest.ini && echo "pytest" || test -f pyproject.toml && grep -q pytest pyproject.toml && echo "pytest" || test -f package.json && node -e "const p=require('./package.json'); p.scripts?.test ? console.log('npm test') : process.exit(1)" 2>/dev/null || test -f Makefile && grep -q "^test:" Makefile && echo "make test" || echo "No test runner detected")
echo "=== VIBE CONTEXT ===" && (test -f .vibe/understanding.md && echo "Understanding: exists" && head -20 .vibe/understanding.md || echo "Understanding: none — consider running /vibe:init")
echo "=== VIBE RISKS ===" && (test -f .vibe/risks.md && echo "Risks baseline: exists" || echo "Risks baseline: none")
```

## Phase 1: Understand

Using the pre-computed context above, explore what needs to change:
- Which files are involved?
- What patterns should we follow?
- Any risks or gotchas?

Summarize findings before proceeding.

## Phase 2: Plan

Present 1-3 approaches to the user:
To implement [TASK], I see these approaches:
Option A: [Name]

What: [description]
Files: [list]
Effort: [estimate]
Trade-off: [pro/con]

Option B: [Name] (if applicable)

What: [description]
Files: [list]
Effort: [estimate]
Trade-off: [pro/con]

Recommendation: [A or B] because [reason]

### Success Criteria

Before writing any code, define what "done" looks like:
- [ ] [Specific measurable outcome 1]
- [ ] [Specific measurable outcome 2]
- [ ] [Test that should pass]
- [ ] [Behavior user should observe]

Proceed with recommendation? [yes / tell me more / different approach]

**STOP and wait for user approval before writing any code.**

## Phase 3: Implement

After user approves:

1. Snapshot current risks: read `.vibe/risks.md` if exists, or note "no baseline"
2. Create/update `.vibe/work/current.md` to track this task, including the success criteria checklist
3. Make the changes following project patterns
4. Update current.md with each file changed and decisions made

## Phase 4: Review

Review your own changes for:
- Logic errors or bugs
- Missing error handling
- Deviation from project patterns
- Security issues

Minor issues: fix them. Significant issues: ask user.

## Phase 5: Risk Check

Scan modified files for new risks:

**Check for:**
- New TODO/FIXME/HACK comments added
- Hardcoded values that look like secrets
- Missing error handling in new code
- Functions that became too long
- Bare except clauses

**Compare to baseline:**
- What risks existed before?
- What risks exist now?
- Did this task ADD any risks?

**If new critical risks found:**
⚠️  New risks detected:
🔴 CRITICAL:



This task introduced critical risks. Options:
[fix now] [acknowledge and continue] [abort]

**STOP and wait for user decision if critical risks found.**

If only warnings/info or no new risks, continue automatically.

## Phase 6: Test

Run the detected test runner from pre-computed context. If none was detected, look harder:
```bash
# Try common test runners
pytest 2>/dev/null || npm test 2>/dev/null || make test 2>/dev/null || echo "No tests found"
```

Run linters if available. Report pass/fail with counts.

**Iterate until tests pass.** Do not move on with failing tests.

## Phase 7: Verify Success Criteria

Check off each success criterion from Phase 2:
- Did each specific outcome happen?
- Do tests pass?
- Does the behavior match what was defined?

If any criteria are unmet, go back and fix before completing.

## Phase 8: Complete

When everything passes:
✓ Task complete
Changes made:



Decisions recorded:



Risks: [no new risks / N new warnings acknowledged]
Tests: ✓ passing
Success criteria: ✓ all met
Ready to commit? [yes / show diff / make changes]

Update `.vibe/decisions.md` with key decisions.
Update `.vibe/risks.md` with current state.
