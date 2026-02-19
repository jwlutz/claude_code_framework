---
description: Start a task with the full agent workflow
argument-hint: <what you want to build or fix>
---

Execute a development task using subagent orchestration: explore → plan → implement → review + test → risk check → verify.

## Pre-compute Context

```bash
# Gather state so Claude doesn't waste turns discovering it
echo "=== GIT STATUS ===" && git status --short 2>/dev/null || echo "Not a git repo"
echo "=== TEST RUNNER ===" && (test -f pytest.ini && echo "pytest" || test -f pyproject.toml && grep -q pytest pyproject.toml && echo "pytest" || test -f package.json && node -e "const p=require('./package.json'); p.scripts?.test ? console.log('npm test') : process.exit(1)" 2>/dev/null || test -f Makefile && grep -q "^test:" Makefile && echo "make test" || echo "No test runner detected")
echo "=== VIBE CONTEXT ===" && (test -f .vibe/understanding.md && echo "Understanding: exists" && head -20 .vibe/understanding.md || echo "Understanding: none — consider running /vibe:init")
echo "=== VIBE RISKS ===" && (test -f .vibe/risks.md && echo "Risks baseline: exists" || echo "Risks baseline: none")
echo "=== VIBE LEARNINGS ===" && (test -f .vibe/learnings.md && cat .vibe/learnings.md || echo "No learnings yet")
```

## Phase 1: Understand

Spawn an **explorer** subagent via the Task tool to analyze the relevant code:

```
Use the Task tool with subagent_type="vibe:explorer" (or "general-purpose" if unavailable) to:
- Read .vibe/understanding.md and .vibe/learnings.md for project context
- Explore the files relevant to [TASK]
- Identify which files need to change
- Note patterns to follow
- Flag any risks or gotchas
- If Context7 is available, look up unfamiliar library/framework APIs before describing them
```

Review the explorer's findings. Summarize for the user before proceeding.

## Phase 2: Plan

This phase runs in the **main conversation** (needs user interaction).

Present 1-3 approaches to the user:

```
To implement [TASK], I see these approaches:

Option A: [Name]
  What: [description]
  Files: [list]
  Trade-off: [pro/con]

Option B: [Name] (if applicable)
  What: [description]
  Files: [list]
  Trade-off: [pro/con]

Recommendation: [A or B] because [reason]
```

### Success Criteria

Before writing any code, define what "done" looks like:
- [ ] [Specific measurable outcome 1]
- [ ] [Specific measurable outcome 2]
- [ ] [Test that should pass]
- [ ] [Behavior user should observe]

**STOP and wait for user approval before writing any code.**

## Phase 3: Implement

After user approves, spawn an **engineer** subagent via the Task tool:

```
Use the Task tool with subagent_type="vibe:engineer" (or "general-purpose" if unavailable) to:
- Implement the approved plan: [plan details]
- Files to change: [list from plan]
- Patterns to follow: [from explorer findings]
- Read .vibe/understanding.md for project conventions
- If Context7 is available, look up library APIs before using them
- Snapshot current risks from .vibe/risks.md
- Create/update .vibe/work/current.md to track the task
```

## Phase 4: Review + Test (parallel)

Spawn **reviewer** and **tester** subagents **in parallel** via the Task tool (both in a single message):

**Reviewer subagent:**
```
Use the Task tool with subagent_type="vibe:reviewer" (or "general-purpose" if unavailable) to:
- Adversarially review the changes just made
- Check for logic errors, security issues, pattern deviations
- Verify library APIs are used correctly (look up docs via Context7 if available)
- Read .vibe/understanding.md for project patterns
- Output: issues found with severity, verdict (APPROVE / NEEDS FIXES)
```

**Tester subagent (in parallel):**
```
Use the Task tool with subagent_type="vibe:tester" (or "general-purpose" if unavailable) to:
- Run the test suite: [test command from pre-compute]
- Run linters if available
- Report pass/fail with counts
- Output: test results, lint results, verdict (READY / NEEDS FIXES)
```

If either finds issues, fix them and re-run. **Iterate until both pass.**

## Phase 5: Risk Check

This phase runs in the **main conversation**.

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

**If new critical risks found, STOP and ask user.**

## Phase 6: Verify & Complete

This phase runs in the **main conversation**.

Check off each success criterion from Phase 2:
- Did each specific outcome happen?
- Do tests pass?
- Does the behavior match what was defined?

If any criteria are unmet, go back and fix before completing.

When everything passes:

```
Task complete

Changes made:
  - [file] — [what changed]

Decisions recorded:
  - [key decision and why]

Risks: [no new risks / N new warnings acknowledged]
Tests: passing
Success criteria: all met

Ready to commit? [yes / show diff / make changes]
```

Update `.vibe/decisions.md` with key decisions.
Update `.vibe/risks.md` with current state.

To record learnings from this task, the user can run `/vibe:learn`.
