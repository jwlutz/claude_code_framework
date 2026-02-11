---
description: Record a learning for future sessions
argument-hint: <what you learned>
---

Append a learning to `.vibe/learnings.md` so Claude remembers it in future sessions.

## Process

1. If `.vibe/learnings.md` doesn't exist, create it with this header:
```markdown
# Learnings

Lessons learned during development. Claude reads this to avoid repeating mistakes.
```

2. Append the new learning:
```markdown

## [Date] — [Short title]
[The learning in 1-3 sentences. Be specific and actionable.]
```

3. Confirm what was recorded.

## Examples

Good learnings:
- "Always use `bun`, not `npm` in this project — package-lock.json is not tracked"
- "The auth middleware must be applied before the rate limiter or requests fail silently"
- "Tests that touch the database need the `@pytest.mark.db` decorator or they get skipped"

Bad learnings (too vague):
- "Be careful with tests"
- "The API is tricky"

## Auto-learn

If Claude notices it made a mistake during a session and had to correct itself, proactively suggest running `/vibe:learn` to record what went wrong.
