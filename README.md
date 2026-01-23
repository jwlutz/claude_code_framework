# Claude Code Framework

Structured workflows for Claude Code. Understand your codebase, track decisions, and catch risks before they become problems.

## Why Use This?

AI coding assistants are powerful, but without structure they can:
- Make changes without understanding the broader system
- Introduce bugs by ignoring existing patterns
- Lose context between sessions
- Miss critical risks in new code

Claude Code Framework adds **discipline** to AI-assisted development. It creates persistent context that survives across sessions, enforces review checkpoints, and tracks every decision.

## Installation

```bash
claude plugin add https://github.com/jwlutz/claude_code_framework
```

That's it. The commands are now available in any project.

## Quick Start

```bash
# In your project directory
claude

# Initialize understanding of your codebase
/vibe-init

# Now Claude knows your architecture, patterns, and risks
/vibe-ask "How does authentication work?"

# Start a task with the full workflow
/vibe-work Add rate limiting to the API
```

## Commands

| Command | Description |
|---------|-------------|
| `/vibe-init` | Analyze codebase and create persistent understanding |
| `/vibe-ask <question>` | Ask questions using stored context |
| `/vibe-work <task>` | Full workflow: explore, plan, implement, review, test |
| `/vibe-status` | Check if understanding exists and its state |
| `/vibe-risks` | Scan for security issues, TODOs, and code smells |
| `/vibe-decisions` | View the decision history for this project |

## What Gets Created

After running `/vibe-init`, a `.vibe/` folder appears in your project:

```
.vibe/
  understanding.md   # Architecture, components, patterns
  decisions.md       # Why things were done (builds over time)
  risks.md           # Known issues and code smells
  work/
    current.md       # Active task tracking
```

**Commit this folder.** It gives Claude (and your team) persistent context.

## The `/vibe-work` Workflow

When you run `/vibe-work <task>`, the framework enforces this process:

1. **Understand** - Read existing context, explore relevant code
2. **Plan** - Present options with tradeoffs, wait for approval
3. **Implement** - Make changes following project patterns
4. **Review** - Self-check for bugs and style issues
5. **Risk Check** - Scan for new issues introduced
6. **Test** - Run the test suite
7. **Complete** - Record decisions, update context

No code is written until you approve the plan.

## For Consultants

Share this with clients who are experimenting with AI coding. It provides:

- **Guardrails** - Prevents AI from making unreviewed changes
- **Visibility** - Every decision is recorded with rationale
- **Consistency** - Same workflow regardless of who's prompting
- **Safety Net** - Risk scanning catches issues before commit

Install it in client repos before they start using Claude Code.

## Requirements

- [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code) installed and authenticated

## License

MIT - see [LICENSE](LICENSE)

---

Built by [Jack Lutz](https://github.com/jwlutz)
