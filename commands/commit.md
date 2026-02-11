---
description: Smart commit, push, and optionally create a PR
argument-hint: [optional: commit message hint]
---

Commit current changes with a well-crafted message, push, and optionally create a PR.

## Pre-compute Context

```bash
echo "=== STAGED ===" && git diff --cached --stat 2>/dev/null
echo "=== UNSTAGED ===" && git diff --stat 2>/dev/null
echo "=== UNTRACKED ===" && git ls-files --others --exclude-standard 2>/dev/null
echo "=== BRANCH ===" && git branch --show-current 2>/dev/null
echo "=== REMOTE ===" && git remote -v 2>/dev/null | head -2
echo "=== RECENT COMMITS ===" && git log --oneline -5 2>/dev/null
echo "=== DIFF ===" && git diff 2>/dev/null && git diff --cached 2>/dev/null
```

## Process

1. **Analyze changes**: Look at all staged, unstaged, and untracked files. Understand what changed and why.

2. **Stage relevant files**: Add files that belong to this logical change. Don't mix unrelated changes.
   ```bash
   git add [specific files]
   ```

3. **Draft commit message**: Follow the project's existing commit style (check recent commits above). If no clear style, use conventional commits:
   - `feat:` for new features
   - `fix:` for bug fixes
   - `refactor:` for restructuring
   - `test:` for test changes
   - `docs:` for documentation

   Keep the first line under 72 characters. Add a body if the "why" isn't obvious.

4. **Show the user**: Display the proposed commit message and files to be committed.

   **STOP and wait for user approval.**

5. **Commit and push**:
   ```bash
   git commit -m "[message]"
   git push
   ```

6. **Offer PR**: If on a feature branch (not main/master), ask:
   "Create a pull request? [yes / no]"

   If yes, create with `gh pr create`.

## If commit fails

- Pre-commit hook failure: fix the issue, re-stage, create a NEW commit (never amend)
- Push failure: check if remote is ahead, pull if needed
