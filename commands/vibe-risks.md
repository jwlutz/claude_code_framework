---
description: Show all identified risks in this codebase
---

Scan this codebase for risks and display them clearly.

## Risk Categories

Scan for these issues:

**🔴 Critical (security/correctness)**
- Hardcoded secrets (API keys, passwords, tokens)
- SQL injection patterns (string concatenation in queries)
- Missing input validation on public functions
- Bare except clauses that swallow errors

**🟡 Warning (maintainability)**
- TODO/FIXME/HACK/XXX comments
- Functions over 100 lines
- Files over 500 lines
- Deeply nested code (4+ levels)
- Duplicate code blocks

**💡 Info (minor)**
- Missing docstrings on public functions
- Unused imports
- Print statements (debug leftovers)

## Process

1. Read `.vibe/understanding.md` for existing known risks
2. Run fresh scan using Grep/Search for patterns above
3. Compare with previous scan in `.vibe/risks.md` if exists
4. Update `.vibe/risks.md` with current state

## Output Format
═══════════════════════════════════════════════════════════
RISK SCAN
═══════════════════════════════════════════════════════════
🔴 CRITICAL (0)
None found ✓
🟡 WARNING (3)
backtester/positionsizer.py:387 - TODO comment
backtester/calculation.py:89-130 - Unusual whitespace
backtester/backtester.py:245 - Function 120 lines
💡 INFO (2)
backtester/utils.py:12 - Missing docstring
tests/conftest.py:1 - Unused import
═══════════════════════════════════════════════════════════
Last scan: [timestamp]
Changes since last: +1 warning, -0 resolved
═══════════════════════════════════════════════════════════

Save results to `.vibe/risks.md` for tracking over time.
