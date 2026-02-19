---
description: Adversarial review with optional Playwright testing and code simplification
argument-hint: [optional: specific files or area to review]
---

Review recent changes through three lenses with subagent orchestration: adversarial code review, browser-level verification (if applicable), and code simplification.

## Pre-compute Context

```bash
echo "=== GIT STATUS ===" && git status --short 2>/dev/null || echo "Not a git repo"
echo "=== CHANGED FILES ===" && git diff --name-only HEAD~1 2>/dev/null || git diff --cached --name-only 2>/dev/null || echo "No recent changes"
echo "=== DIFF ===" && git diff HEAD~1 2>/dev/null || git diff --cached 2>/dev/null || git diff 2>/dev/null || echo "No diff available"
echo "=== PLAYWRIGHT ===" && (test -f playwright.config.ts && echo "playwright.config.ts found" || test -f playwright.config.js && echo "playwright.config.js found" || (test -f package.json && grep -q playwright package.json && echo "Playwright in package.json") || echo "No Playwright detected")
echo "=== DEV SERVER ===" && (test -f package.json && node -e "const p=require('./package.json'); const s=p.scripts||{}; console.log(s.dev||s.start||'none')" 2>/dev/null || echo "No dev server detected")
echo "=== VIBE CONTEXT ===" && (test -f .vibe/understanding.md && echo "Understanding: exists" && head -20 .vibe/understanding.md || echo "Understanding: none")
echo "=== VIBE RISKS ===" && (test -f .vibe/risks.md && echo "Risks baseline: exists" || echo "Risks baseline: none")
```

## Phase 1: Adversarial Review + Playwright (parallel)

Spawn **reviewer** and **tester** subagents **in parallel** via the Task tool (both in a single message):

**Reviewer subagent:**
```
Use the Task tool with subagent_type="vibe:reviewer" (or "general-purpose" if unavailable) to:
- Review the changed files as a hostile critic — try to break the code
- Read .vibe/understanding.md for project patterns
- Verify library APIs are used correctly (look up docs via Context7 if available)

Check:
- Correctness: trace code paths, edge cases (null, undefined, empty, 0, negative), off-by-one, race conditions
- Security: unsanitized input, injection vectors (SQL, XSS, command, path traversal), secrets in logs, auth checks
- Reliability: external service failures, unhandled exceptions, deadlocks, memory leaks
- Design: coupling, implicit assumptions, readability

Output format:
  CRITICAL: [file:line] — [issue] — [how to exploit/trigger]
  WARNING: [file:line] — [issue] — [why it matters]
  SUGGESTION: [file:line] — [issue] — [improvement]
  Verdict: [PASS / NEEDS FIXES]
```

**Playwright subagent (if applicable — skip if no Playwright detected or changes are backend/config only):**
```
Use the Task tool with subagent_type="vibe:tester" (or "general-purpose" if unavailable) to:
- Start the dev server if needed
- Run existing Playwright tests: npx playwright test --reporter=list
- Use browser tools to navigate affected pages, take snapshots, check for console errors, verify network requests
- Test edge cases identified in the review
- Report: existing tests pass/fail, pages checked, console errors, network failures, visual issues, verdict
```

**If CRITICAL issues found, STOP and ask user whether to fix now or continue.**

## Phase 2: Simplification

This phase runs in the **main conversation** (needs user approval per change).

Review the changed code for opportunities to simplify **without changing behavior**.

**Rules:**
- Every simplification must preserve exact functionality
- Only simplify code that was recently modified (from the diff)
- Do not refactor code that wasn't touched
- Do not add features, abstractions, or "improvements"

### What to Simplify
- Redundant conditionals or branches that can never be reached
- Variables assigned and used only once with no clarity benefit
- Overly verbose patterns that have simpler idiomatic equivalents
- Dead code introduced by the changes
- Duplicated logic within the changed code

### What NOT to Simplify
- Working code outside the diff
- Code that is verbose for a good reason (clarity, debugging)
- Patterns that match the rest of the codebase even if not your preference

Show proposed simplifications with before/after snippets.

**STOP and wait for user to approve which simplifications to apply.**

Apply only the approved simplifications.

## Phase 3: Summary

```
REVIEW COMPLETE

Adversarial review:  [PASS / NEEDS FIXES] — [N critical, N warning, N suggestion]
Playwright:          [PASS / NEEDS FIXES / SKIPPED] — [details]
Simplification:      [CLEAN / N changes applied]

Overall: [READY TO SHIP / NEEDS ATTENTION]
```

Update `.vibe/risks.md` with any new findings.
Update `.vibe/decisions.md` if simplifications were applied.
