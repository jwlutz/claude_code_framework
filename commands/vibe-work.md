---
description: Start a task with the full agent workflow
argument-hint: <what you want to build or fix>
---

Execute a development task using the explore → plan → implement → review → risk check → test workflow.

## Phase 1: Understand

Read `.vibe/understanding.md` for codebase context.

Explore what needs to change:
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
Proceed with recommendation? [yes / tell me more / different approach]

**STOP and wait for user approval before writing any code.**

## Phase 3: Implement

After user approves:

1. Snapshot current risks: read `.vibe/risks.md` if exists, or note "no baseline"
2. Create/update `.vibe/work/current.md` to track this task
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

Run available tests:
- pytest, npm test, make test, etc.
- Run linters if available

Report pass/fail with counts.

## Phase 7: Complete

When everything passes:
✓ Task complete
Changes made:



Decisions recorded:



Risks: [no new risks / N new warnings acknowledged]
Tests: ✓ passing
Ready to commit? [yes / show diff / make changes]

Update `.vibe/decisions.md` with key decisions.
Update `.vibe/risks.md` with current state.
