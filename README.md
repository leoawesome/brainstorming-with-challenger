# Advance Brainstorming (with Challengers)

A Claude Code plugin that enhances brainstorming with a customizable **challenger team** — parallel agents that stress-test your designs before you commit to building them.

## Prerequisites

- [Claude Code](https://claude.ai/claude-code)
- [Superpowers plugin](https://github.com/obra/superpowers) (required for `writing-plans` handoff)

## Installation

Install via Claude Code:

```
/install-plugin https://github.com/leoawesome/brainstorming-with-challenger
```

Or add manually to your Claude Code plugins configuration.

## Usage

Say **"brainstorm with challengers"** to start. You can also use phrases like:
- "challenge this idea"
- "stress-test this"

## How It Works

The skill follows the standard brainstorming flow (explore context, ask questions, propose approaches), but adds two **challenge points** where parallel agents stress-test your work:

1. **After proposing approaches** — challengers look for architectural flaws, UX gaps, and counter-arguments
2. **After user approves the design** — challengers check for completeness before writing the spec

Challengers are **silent by default** — if they find nothing wrong, you won't even know they ran.

After brainstorming, the skill automatically hands off to `superpowers:writing-plans`.

## Default Challengers

| Challenger | Runs | Focus |
|---|---|---|
| **Technical** | Always | Architecture, scalability, performance |
| **Product** | Always | UX, edge states, accessibility |
| **Devil's Advocate** | Always | Strongest counter-argument, hidden assumptions |
| **Design** | UI/UX only | Visual hierarchy, polish, responsive design |

## Customization

Each challenger is a standalone `.md` file in `skills/brainstorming/challengers/`. To customize:

- **Edit** existing files to change focus areas
- **Add** new `.md` files for new roles (e.g., `security.md`, `business-viability.md`)
- **Remove** files to disable roles

### Challenger file format

```markdown
---
name: Security Challenger
when: always
---

## Focus — Stress-Test
- Are there injection vectors?
- Is auth/authz handled correctly?

## Focus — Completeness
- Are all security requirements addressed?

## Rules
- Be specific and constructive.
- Respond "NO_ISSUES" or "COMPLETE" if nothing to flag.
```

The `when` field accepts:
- `always` — runs for every challenge
- `ui-only` — runs only when the change involves UI/UX

## License

MIT
