# Advance Brainstorming (with Challengers)

A Claude Code plugin that enhances brainstorming with a customizable **challenger team** — parallel agents that stress-test your ideas before you commit to building them.

Whether you're designing a product feature, planning a marketing campaign, or architecting a system, the challenger team acts as your virtual review board — catching blind spots, questioning assumptions, and strengthening your approach.

## Prerequisites

- [Claude Code](https://claude.ai/claude-code)
- [Superpowers plugin](https://github.com/obra/superpowers) (required for `writing-plans` handoff)

## Installation

**Install via VS Code (recommended):**

1. Open the Claude Code panel in VS Code
2. Type `/plugins` in the chat
3. Go to **Marketplaces** tab → enter `leoawesome/brainstorming-with-challenger` → add it
4. Go to **Plugins** tab → find **advance-brainstorming** → click **Install**
5. Done — works in every session

**Install via CLI:**

```
/plugin marketplace add leoawesome/brainstorming-with-challenger
/plugin install advance-brainstorming@advance-brainstorming-marketplace
/reload-plugins
```

**Private repos:** This works with private repos too. As long as you have `git clone` access (e.g., added as a collaborator), Claude Code uses your existing git credentials. For auto-updates on private repos, set `GITHUB_TOKEN` in your shell config.

**For local testing:**

```bash
claude --plugin-dir /path/to/brainstorming-with-challenger/plugin
```

## Usage

Say **"brainstorm with challengers"** to start. You can also use phrases like:
- "challenge this idea"
- "stress-test this"

## How It Works

The skill follows the standard brainstorming flow (explore context, ask questions, propose approaches), but adds **smart challenger suggestions** and two **challenge points**:

1. **After clarifying questions** — the skill scans the challenger gallery, picks relevant ones for your task, and asks you to confirm. Devil's Advocate is always recommended.
2. **After proposing approaches** — approved challengers stress-test for flaws, gaps, and counter-arguments
3. **After user approves the design** — challengers check for completeness before writing the spec

Challengers are **silent by default** — if they find nothing wrong, you won't even know they ran.

After brainstorming, the skill automatically hands off to `superpowers:writing-plans`.

## Example Session

Here's what a typical session looks like:

```
You: brainstorm with challengers — I want to build a customer feedback portal

Claude: [explores project context, asks clarifying questions one at a time]

Claude: For this session, I'd recommend these challengers:
  - **Devil's Advocate** — always recommended to challenge assumptions
  - **Technical Challenger** — building a web portal with backend
  - **Product Challenger** — user-facing feature with workflows
  - **Security Challenger** — handles user-submitted data
  - **Data Privacy Challenger** — collecting user information
  Want to include them? You can also add or remove any.

You: yes

Claude: Here are 3 approaches:
  A. Standalone portal with its own auth (recommended)
  B. Embedded widget in existing app
  C. Third-party tool integration

  # Challengers dispatch silently in parallel:
  # - Technical Challenger: "Approach A duplicates auth — use SSO instead"
  # - Devil's Advocate: "Approach B is simpler and avoids a separate deploy"
  # - Security Challenger: "Approach C sends data to third party — privacy risk"
  # - Product Challenger: NO_ISSUES
  # - Data Privacy Challenger: NO_ISSUES

Claude: After stress-testing, I've refined the approaches:
  A. Standalone portal with SSO (strengthened — avoids auth duplication)
  B. Embedded widget (elevated — simpler deploy story)
  C. Third-party integration (flagged — data leaves your control)

  [presents design sections, gets your approval]

  # Completeness check runs silently — all return COMPLETE

Claude: Design doc written to docs/plans/2026-03-25-feedback-portal-design.md
  Invoking writing-plans to create implementation plan...
```

## Token Usage Note

Each brainstorming session dispatches challenger agents at two checkpoints. The number of agents depends on how many challengers you approve — typically 3-5 per checkpoint. Each agent explores the codebase independently. This provides thorough review but uses more tokens than standard brainstorming. For small changes, only the Devil's Advocate runs (1 agent per checkpoint).

## Challenger Gallery

All challengers live in `plugin/skills/brainstorming/gallery/`. The skill suggests relevant ones each session based on your task — you approve, remove, or add before the challenge runs.

| Challenger | Suggested When |
|---|---|
| **Devil's Advocate** | Always (recommended every session) |
| **Technical** | Software architecture, APIs, databases, system design |
| **Product** | User-facing features, workflows, dashboards |
| **Design** | UI layout, visual design, styling, animations |
| **Security** | Auth, user input, APIs, payments, file uploads |
| **Compliance** | User data, consent, regulated industries |
| **Business Viability** | New products, revenue impact, pricing |
| **Cost Optimization** | Infrastructure, cloud, scaling, third-party APIs |
| **Accessibility** | User-facing UI, forms, navigation, media |
| **Data Privacy** | Data collection, analytics, tracking, third-party sharing |

## Customization

Each challenger is a standalone `.md` file in `plugin/skills/brainstorming/gallery/`. To customize:

- **Edit** existing files to change focus areas
- **Add** new `.md` files for new roles
- **Remove** files to disable roles

### Example: adding a custom challenger

A product lead might add `plugin/skills/brainstorming/gallery/market-fit.md`:

```markdown
---
name: Market Fit Challenger
when: always
suggest-when: "The task involves a new product, feature launch, or user acquisition strategy"
---

## Focus — Stress-Test
- Is there evidence users want this?
- What's the smallest version that tests the core assumption?
- Who are the first 10 users and how do we reach them?

## Focus — Completeness
- Are success metrics defined?
- Is there a feedback loop to validate after launch?

## Rules
- Be specific and constructive.
- Respond "NO_ISSUES" or "COMPLETE" if nothing to flag.
```

### Challenger file format

- `name` — display name shown during suggestion
- `when` — `always` (runs for every challenge) or `ui-only` (only for UI/UX changes)
- `suggest-when` — describes when the skill should suggest this challenger. Use `always` to always suggest (like Devil's Advocate)

## License

MIT
