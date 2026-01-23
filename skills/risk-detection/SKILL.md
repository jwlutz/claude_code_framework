---
name: risk-detection
description: Detects code risks and security issues. Triggers when reviewing code, checking for problems, or scanning for issues.
---

When scanning for risks, look for these patterns:

## Critical (Security/Correctness)
Hardcoded secrets
pattern: (api_key|password|secret|token)\s*=\s*["'][^"']+["']
exclude: test files, example configs
SQL injection
pattern: execute(.%s|execute(.+|execute(f"|cursor.execute(.*format
Bare except
pattern: except\s*:
eval/exec with variables
pattern: eval(|exec(

## Warning (Maintainability)
TODOs
pattern: (TODO|FIXME|HACK|XXX):?
Long functions (check line count between def and next def/class/EOF)
Deep nesting (4+ levels of indentation)

## Info (Minor)
Print statements
pattern: print(
Unused imports (import X but X never used)

When reporting, always include file:line references.
