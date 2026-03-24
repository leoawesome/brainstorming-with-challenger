# Advance Brainstorming (with Challengers)

A Claude Code plugin that enhances brainstorming with a customizable **challenger team** — parallel agents that stress-test your ideas before you commit to building them.

Whether you're designing a product feature, planning a marketing campaign, or architecting a system, the challenger team acts as your virtual review board — catching blind spots, questioning assumptions, and strengthening your approach.

## Prerequisites

- [Claude Code](https://claude.ai/claude-code)
- [Superpowers plugin](https://github.com/obra/superpowers) (required for `writing-plans` handoff)

## Installation

Test locally:

```bash
claude --plugin-dir /path/to/brainstorming-with-challenger
```

Or install from a marketplace if available. After installing, run `/reload-plugins` to load the skill.

## Usage

Say **"brainstorm with challengers"** to start. You can also use phrases like:
- "challenge this idea"
- "stress-test this"

## How It Works

The skill follows the standard brainstorming flow (explore context, ask questions, propose approaches), but adds two **challenge points** where parallel agents stress-test your work:

1. **After proposing approaches** — challengers look for flaws, gaps, and counter-arguments
2. **After user approves the design** — challengers check for completeness before writing the spec

Challengers are **silent by default** — if they find nothing wrong, you won't even know they ran.

After brainstorming, the skill automatically hands off to `superpowers:writing-plans`.

## Example Session

Here's what a typical session looks like:

```
You: brainstorm with challengers — I want to build a customer feedback portal

Claude: [explores project context, asks clarifying questions one at a time]

Claude: Here are 3 approaches:
  A. Standalone portal with its own auth (recommended)
  B. Embedded widget in existing app
  C. Third-party tool integration

  # At this point, challengers are dispatched silently in parallel:
  # - Technical Challenger finds: "Approach A duplicates auth — use SSO instead"
  # - Devil's Advocate argues: "Approach B is simpler and avoids a separate deploy"
  # - Product Challenger: NO_ISSUES
  #
  # Claude synthesizes the feedback and strengthens the approaches:

Claude: After stress-testing, I've refined the approaches:
  A. Standalone portal with SSO (strengthened — avoids auth duplication)
  B. Embedded widget (elevated — simpler deploy story)
  C. Third-party integration

  [presents design sections, gets your approval]

  # Completeness check runs silently — all return COMPLETE

Claude: Design doc written to docs/plans/2026-03-25-feedback-portal-design.md
  Invoking writing-plans to create implementation plan...
```

## Token Usage Note

Each brainstorming session dispatches 3-4 challenger agents at two checkpoints (6-8 agent calls total). Each agent explores the codebase independently. This provides thorough review but uses more tokens than standard brainstorming. For small changes, only the Devil's Advocate runs (1 agent per checkpoint).

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

### Example: adding a custom challenger

A product lead might add `challengers/business-viability.md`:

```markdown
---
name: Business Viability Challenger
when: always
---

## Focus — Stress-Test
- Does this align with our revenue model?
- What's the cost to build vs expected return?
- Are we solving a problem customers are willing to pay for?

## Focus — Completeness
- Is the go-to-market strategy addressed?
- Are pricing implications considered?

## Rules
- Be specific and constructive.
- Respond "NO_ISSUES" or "COMPLETE" if nothing to flag.
```

### Challenger file format

The `when` field accepts:
- `always` — runs for every challenge
- `ui-only` — runs only when the change involves UI/UX

## License

MIT
