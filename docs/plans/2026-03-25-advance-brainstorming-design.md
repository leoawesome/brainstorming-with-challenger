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
├── .opencode/
│   └── plugins/
│       └── advance-brainstorming.js
├── package.json
├── skills/
│   └── brainstorming/
│       ├── SKILL.md
│       ├── challenge-protocol.md
│       └── challengers/
│           ├── technical.md
│           ├── product.md
│           ├── devils-advocate.md
│           └── design.md
```

## Flow

1. Explore project context
2. Ask clarifying questions (one at a time)
3. Propose 2-3 approaches with trade-offs
4. **Challenge Point 1 (stress-test)** — dispatch challenger agents in parallel
5. Present design sections, get user approval
6. **Challenge Point 2 (completeness)** — dispatch challengers again
7. Write design doc to `docs/plans/YYYY-MM-DD-<topic>-design.md`
8. Auto-invoke `superpowers:writing-plans`

## Challenger System

### File-per-challenger

Each `.md` in `challengers/` defines one role with frontmatter:

```yaml
---
name: Technical Challenger
when: always  # or "ui-only"
---
```

Body contains focus areas and rules. The skill globs `challengers/*.md` and dispatches one Agent per file.

### Default challengers

- **Technical** (`when: always`) — architecture, scalability, performance
- **Product** (`when: always`) — UX, edge states, accessibility
- **Devil's Advocate** (`when: always`) — strongest counter-argument
- **Design** (`when: ui-only`) — visual design quality (only for UI/UX changes)

### Challenger agents have tool access

Agents are dispatched via the Agent tool with access to Read, Grep, Glob. They receive file paths as starting points and explore the codebase independently — not limited to pasted context.

### Silent by default

- `NO_ISSUES` / `COMPLETE` → proceed without mentioning the challenge
- Issues found → synthesize findings, revise, present to user

### Proportional challenge

| Complexity | Challenge Level |
|---|---|
| Small (config, copy, bugfix) | Devil's Advocate only |
| Medium/Large | All challengers |

### Re-challenge on substantial revisions

If Challenge Point 1 causes substantial revisions (not just wording), re-run challengers on the revised version.

### Edge case: no challengers

If no `.md` files found in `challengers/`, skip challenge and proceed. Log: "No challengers configured — skipping challenge."

## Customization

Users fork the repo and edit files in `challengers/`:
- Edit existing files to change focus areas
- Add new files for new roles (e.g., `security.md`, `business-viability.md`)
- Remove files to disable roles

## Prerequisite Check

At transition to `writing-plans`, verify the skill is available. If not, inform user to install superpowers.
