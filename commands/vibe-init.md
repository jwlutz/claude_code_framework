---
description: Analyze this codebase and build persistent understanding
---

Analyze this codebase and create a `.vibe/understanding.md` file that persists across sessions.

## Steps

1. **Map the structure** - Use Glob to find all source files. Ignore: node_modules, .git, __pycache__, .venv, venv, build, dist, .pytest_cache

2. **Identify the architecture**:
   - What language(s) and framework(s)?
   - What are the entry points?
   - How is code organized?

3. **Understand each component**:
   - Read the key files
   - What does each module/class do?
   - How do they connect?

4. **Find patterns and conventions**:
   - Naming conventions
   - Error handling approach
   - Testing patterns

5. **Identify risks**:
   - TODO/FIXME/HACK comments
   - Missing error handling
   - Hardcoded values

## Output

Create `.vibe/understanding.md`:
```markdown
# Codebase Understanding

Generated: [date]

## Summary
[1-2 sentences: what this codebase does]

## Architecture
- **Type**: [e.g., Python backtesting framework]
- **Languages**: [list]
- **Entry points**: [main files with paths]
- **Organization**: [how code is structured]

## Components
[For each major component:]
### [Name]
- **Path**: [location]
- **Purpose**: [what it does]
- **Key classes/functions**: [list]
- **Depends on**: [other components]

## Data Flow
[How data moves through the system]

## Patterns
- [Convention 1]
- [Convention 2]

## Risks
- 🔴 **Critical**: [if any]
- 🟡 **Warning**: [issues]
- 📝 **TODOs**: [count and notable ones]

## Glossary
- **[term]**: [meaning]
```

After creating, print a brief summary of what you found.
