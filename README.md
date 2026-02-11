# Claude Code Framework

Structured workflows for Claude Code. Understand your codebase, track decisions, and catch risks before they become problems.

Inspired by [Boris Cherny's Claude Code workflow](https://howborisusesclaudecode.com/) — plan before coding, verify your work, and build persistent context.

## Why Use This?

AI coding assistants are powerful, but without structure they can:
- Make changes without understanding the broader system
- Introduce bugs by ignoring existing patterns
- Lose context between sessions
- Miss critical risks in new code

Claude Code Framework adds **discipline** to AI-assisted development. It creates persistent context that survives across sessions, enforces review checkpoints, and tracks every decision.

## Installation

Add the marketplace and install the plugin:

```bash
# Add the marketplace
/plugin marketplace add jwlutz/claude_code_framework

# Install the vibe plugin
/plugin install vibe@vibe-marketplace
```

Or from the CLI:

```bash
claude plugin install vibe@vibe-marketplace
```

That's it. The commands are now available in any project.

### Team Installation

Add this to your project's `.claude/settings.json` to auto-prompt teammates:

```json
{
  "extraKnownMarketplaces": {
    "vibe-marketplace": {
      "source": {
        "source": "github",
        "repo": "jwlutz/claude_code_framework"
      }
    }
  },
  "enabledPlugins": {
    "vibe@vibe-marketplace": true
  }
}
```

## Quick Start

```bash
# In your project directory
claude

# Initialize understanding of your codebase
/vibe:init

# Now Claude knows your architecture, patterns, and risks
/vibe:ask "How does authentication work?"

# Start a task with the full workflow
/vibe:go Add rate limiting to the API

# Or go TDD style
/vibe:tdd Add input validation to the signup form
```

## Commands

| Command | Description |
|---------|-------------|
| `/vibe:init` | Analyze codebase and create persistent understanding |
| `/vibe:ask <question>` | Ask questions using stored context |
| `/vibe:go <task>` | Full workflow: explore, plan, implement, review, test |
| `/vibe:tdd <task>` | Test-driven development: write tests first, then implement |
| `/vibe:commit` | Smart commit, push, and optionally create a PR |
| `/vibe:learn <lesson>` | Record a learning for future sessions |
| `/vibe:status` | Check if understanding exists and its state |
| `/vibe:risks` | Scan for security issues, TODOs, and code smells |
| `/vibe:log` | View the decision history for this project |

## What Gets Created

After running `/vibe:init`, a `.vibe/` folder appears in your project:

```
.vibe/
  understanding.md   # Architecture, components, patterns
  decisions.md       # Why things were done (builds over time)
  risks.md           # Known issues and code smells
  learnings.md       # Lessons learned (don't repeat mistakes)
  work/
    current.md       # Active task tracking
```

**Commit this folder.** It gives Claude (and your team) persistent context.

## The `/vibe:go` Workflow

When you run `/vibe:go <task>`, the framework enforces this process:

1. **Pre-compute** - Inline bash gathers git status, test runner, and existing context
2. **Understand** - Read existing context, explore relevant code
3. **Plan** - Present options with tradeoffs, define success criteria, wait for approval
4. **Implement** - Make changes following project patterns
5. **Review** - Self-check for bugs and style issues
6. **Risk Check** - Scan for new issues introduced
7. **Test** - Run the test suite, iterate until passing
8. **Verify** - Check success criteria are met
9. **Complete** - Record decisions, update context

No code is written until you approve the plan.

## The `/vibe:tdd` Workflow

Test-driven development following Boris Cherny's approach:

1. **Understand** - What are we building?
2. **Write Tests** - Define desired behavior as tests (don't run them yet)
3. **Commit Tests** - Lock in the test contract
4. **Implement** - Write minimum code to pass tests (don't edit tests)
5. **Iterate** - Run tests, fix implementation, repeat until green
6. **Complete** - Commit the passing implementation

## For Consultants

Share this with clients who are experimenting with AI coding. It provides:

- **Guardrails** - Prevents AI from making unreviewed changes
- **Visibility** - Every decision is recorded with rationale
- **Consistency** - Same workflow regardless of who's prompting
- **Safety Net** - Risk scanning catches issues before commit
- **Memory** - Learnings persist across sessions

Install it in client repos before they start using Claude Code.

## Requirements

- [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code) installed and authenticated

## License

MIT - see [LICENSE](LICENSE)

---

Built by [Jack Lutz](https://github.com/jwlutz)
