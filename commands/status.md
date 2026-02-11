---
description: Show vibe status for this codebase
---

Check if `.vibe/` exists and show its state.

## Output

If `.vibe/understanding.md` exists:
```
✓ Vibe initialized
  Last analyzed: [date from file]
  Components: [count]
  Risks: [count by severity]
```

If not:
```
✗ No vibe understanding found
  Run /vibe:init to analyze this codebase
```
