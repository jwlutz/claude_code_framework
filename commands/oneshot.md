---
description: End-to-end workflow — clarify, implement, review, and present
argument-hint: <what you want to build or fix>
---

Full lifecycle in one command with subagent orchestration: clarify intent with the user, implement the task, then adversarially review and present the finished work.

## Pre-compute Context

```bash
echo "=== GIT STATUS ===" && git status --short 2>/dev/null || echo "Not a git repo"
echo "=== TEST RUNNER ===" && (test -f pytest.ini && echo "pytest" || test -f pyproject.toml && grep -q pytest pyproject.toml && echo "pytest" || test -f package.json && node -e "const p=require('./package.json'); p.scripts?.test ? console.log('npm test') : process.exit(1)" 2>/dev/null || test -f Makefile && grep -q "^test:" Makefile && echo "make test" || echo "No test runner detected")
echo "=== PLAYWRIGHT ===" && (test -f playwright.config.ts && echo "playwright.config.ts found" || test -f playwright.config.js && echo "playwright.config.js found" || (test -f package.json && grep -q playwright package.json && echo "Playwright in package.json") || echo "No Playwright detected")
echo "=== DEV SERVER ===" && (test -f package.json && node -e "const p=require('./package.json'); const s=p.scripts||{}; console.log(s.dev||s.start||'none')" 2>/dev/null || echo "No dev server detected")
echo "=== VIBE CONTEXT ===" && (test -f .vibe/understanding.md && echo "Understanding: exists" && head -20 .vibe/understanding.md || echo "Understanding: none — consider running /vibe:init")
echo "=== VIBE RISKS ===" && (test -f .vibe/risks.md && echo "Risks baseline: exists" || echo "Risks baseline: none")
echo "=== VIBE LEARNINGS ===" && (test -f .vibe/learnings.md && cat .vibe/learnings.md || echo "No learnings yet")
```

---

# STAGE 1: CLARIFY

Before writing a single line of code, make sure you understand exactly what the user wants.

## Phase 1: Explore & Question

Spawn an **explorer** subagent via the Task tool:

```
Use the Task tool with subagent_type="vibe:explorer" (or "general-purpose" if unavailable) to:
- Read .vibe/understanding.md and .vibe/learnings.md for project context
- Explore the files relevant to [TASK]
- Identify the current architecture and patterns
- Note ambiguities and decisions the user needs to make
- If Context7 is available, look up unfamiliar library/framework APIs
```

Then, in the **main conversation**, present understanding back to the user:

```
TASK UNDERSTANDING

What I think you want:
  [1-2 sentence summary of the task]

What this involves:
  - [key change 1]
  - [key change 2]
  - [key change N]

Questions before I proceed:
  1. [Ambiguity or decision the user needs to make]
  2. [Another question, if any]

Assumptions I'm making (correct me if wrong):
  - [assumption 1]
  - [assumption 2]
```

**STOP and wait for the user to confirm understanding, answer questions, and correct assumptions.**

Do not proceed until the user says the understanding is correct.

---

# STAGE 2: IMPLEMENT

Now execute the task using the full development workflow.

## Phase 2: Plan

This phase runs in the **main conversation** (needs user interaction).

Present 1-3 approaches:

```
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

Define what "done" looks like:
- [ ] [Specific measurable outcome 1]
- [ ] [Specific measurable outcome 2]
- [ ] [Test that should pass]
- [ ] [Behavior user should observe]

**STOP and wait for user approval before writing any code.**

## Phase 3: Implement

Spawn an **engineer** subagent via the Task tool:

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

## Phase 4: Self-check + Test

Spawn a **tester** subagent via the Task tool:

```
Use the Task tool with subagent_type="vibe:tester" (or "general-purpose" if unavailable) to:
- Run the test suite: [test command from pre-compute]
- Run linters if available
- Report pass/fail with counts
```

**Iterate until tests pass.** Do not move on with failing tests.

---

# STAGE 3: REVIEW & PRESENT

With implementation complete, perform a thorough review and present the finished work.

## Phase 5: Adversarial Review + Playwright (parallel)

Spawn **reviewer** and **tester** subagents **in parallel** via the Task tool (both in a single message):

**Reviewer subagent:**
```
Use the Task tool with subagent_type="vibe:reviewer" (or "general-purpose" if unavailable) to:
- Review the changes as a hostile critic — try to break the code
- Check correctness: trace code paths, edge cases, error handling
- Check security: injection, secrets, auth
- Check reliability: external service failures, unhandled exceptions
- Verify library APIs are used correctly (look up docs via Context7 if available)
- Look for simplification opportunities in changed code (without changing behavior)
- Output: issues with severity, simplification proposals, verdict
```

**Playwright subagent (if applicable — skip if no Playwright detected or changes are backend/config only):**
```
Use the Task tool with subagent_type="vibe:tester" (or "general-purpose" if unavailable) to:
- Start the dev server if needed
- Run existing Playwright tests: npx playwright test --reporter=list
- Use browser tools to navigate affected pages, check for console errors, verify network requests
- Report: pages checked, console errors, network failures, visual issues, verdict
```

Fix anything found. If a fix is non-trivial, note it for the user.

## Phase 6: Simplification

This phase runs in the **main conversation** (needs user approval per change).

From the reviewer's simplification proposals, show before/after snippets.

**STOP and wait for user to approve which simplifications to apply (if any).**

Apply only the approved simplifications.

## Phase 7: Verify & Present

Check off each success criterion from Phase 2.

If any criteria are unmet, go back and fix before presenting.

```
ONESHOT COMPLETE

Task: [summary]

Changes made:
  - [file] — [what changed]

Decisions:
  - [key decision and why]

Review results:
  Adversarial review:  [PASS / fixed N issues] — [N critical, N warning, N suggestion]
  Playwright:          [PASS / NEEDS FIXES / SKIPPED] — [details]
  Simplification:      [CLEAN / N changes applied]
  Success criteria:    [all met / N of M met]

Tests: passing
Risks: [no new risks / N new warnings]

Overall: [READY TO SHIP / NEEDS ATTENTION]

Ready to commit? [yes / show diff / make changes]
```

Update `.vibe/decisions.md` with key decisions.
Update `.vibe/risks.md` with current state.
