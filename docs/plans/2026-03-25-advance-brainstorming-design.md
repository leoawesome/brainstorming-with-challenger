# Advance Brainstorming Skill — Design Spec

## Overview

A Claude Code plugin that enhances the brainstorming step of the superpowers workflow with a customizable **challenger team** — parallel agents that independently explore the codebase and stress-test approaches before finalizing designs.

**Prerequisite:** superpowers plugin (for `writing-plans` handoff).

**Trigger:** "brainstorm with challengers" (or variations like "challenge this idea", "stress-test this").

**Skill name:** `advance-brainstorming:brainstorming`

## Plugin Structure

```
brainstorming-with-challenger/
├── .claude-plugin/
│   └── plugin.json
├── skills/
│   └── brainstorming/
│       ├── SKILL.md
│       ├── challenge-protocol.md
│       └── gallery/
│           ├── devils-advocate.md
│           ├── technical.md
│           ├── product.md
│           ├── design.md
│           ├── security.md
│           ├── compliance.md
│           ├── business-viability.md
│           ├── cost-optimization.md
│           ├── accessibility.md
│           └── data-privacy.md
```

## Flow

1. Explore project context
2. Ask clarifying questions (one at a time)
3. **Suggest gallery challengers** — scan gallery, match against task context, present relevant challengers for user approval
4. Propose 2-3 approaches with trade-offs
5. **Challenge Point 1 (stress-test)** — dispatch approved challenger agents in parallel
6. Present design sections, get user approval
7. **Challenge Point 2 (completeness)** — dispatch challengers again
8. Write design doc to `docs/plans/YYYY-MM-DD-<topic>-design.md`
9. Auto-invoke `superpowers:writing-plans`

## Challenger System

### Gallery-based suggest-and-confirm

All challengers live in `gallery/`. None are active by default. The skill scans gallery files, matches their `suggest-when` field against task context, and presents relevant ones to the user. User approves, adds, or removes before any challenge runs. Selection is session-only.

Each `.md` in `gallery/` defines one role with frontmatter:

```yaml
---
name: Technical Challenger
when: always  # or "ui-only"
suggest-when: "The task involves software architecture, APIs, databases, or system design"
---
```

Devil's Advocate uses `suggest-when: always` — it is always recommended.

### Available challengers (10)

| Challenger | when | suggest-when |
|---|---|---|
| **Devil's Advocate** | always | always |
| **Technical** | always | software, APIs, databases, system design |
| **Product** | always | user-facing features, workflows, dashboards |
| **Design** | ui-only | UI layout, visual design, styling |
| **Security** | always | auth, user input, APIs, payments |
| **Compliance** | always | user data, consent, regulated industries |
| **Business Viability** | always | new products, revenue impact, pricing |
| **Cost Optimization** | always | infrastructure, cloud, scaling |
| **Accessibility** | always | user-facing UI, forms, navigation |
| **Data Privacy** | always | data collection, analytics, tracking |

### Challenger agents have tool access

Agents are dispatched via the Agent tool with access to Read, Grep, Glob. They receive file paths as starting points and explore the codebase independently — not limited to pasted context.

### Silent by default

- `NO_ISSUES` / `COMPLETE` → proceed without mentioning the challenge
- Issues found → synthesize findings, revise, present to user

### Proportional challenge

| Complexity | Challenge Level |
|---|---|
| Small (config, copy, bugfix) | Devil's Advocate only |
| Medium/Large | All approved challengers |

### Re-challenge on substantial revisions

If Challenge Point 1 causes substantial revisions (not just wording), re-run challengers on the revised version.

### Edge cases

- **User declines all challengers:** proceed, note "No challengers active — skipping stress-test steps."
- **No gallery files exist:** skip suggestion and challenge steps, note "No challengers available in gallery — skipping challenge steps."
- **All return NO_ISSUES / COMPLETE:** proceed silently, don't mention the challenge ran.

## Customization

Users fork the repo and edit files in `gallery/`:
- Edit existing files to change focus areas
- Add new files for new roles (e.g., `market-fit.md`, `legal-review.md`)
- Remove files to disable roles

## Prerequisite Check

At transition to `writing-plans`, verify the skill is available. If not, inform user to install superpowers.
