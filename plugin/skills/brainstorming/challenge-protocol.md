# Challenge Protocol

At two key points in the brainstorming process, dispatch challenger agents **in parallel** to stress-test the work. All challengers live in the `gallery/` directory (sibling to this file). They are selected per session based on task context.

Challengers operate **silently by default** — they only surface findings when they identify genuine improvements or gaps.

<IMPORTANT>
The purpose of challenges is quality, not ceremony. If all challengers find nothing wrong, proceed without mentioning the challenge ran. Only surface findings that would actually change the design.
</IMPORTANT>

## Suggest-and-Confirm

All challengers live in the `gallery/` directory. None are active by default — they are suggested based on context each session.

**When to suggest:** After clarifying questions are complete and before proposing approaches.

**How to suggest:**
1. Read all `.md` files in the `gallery/` directory
2. Each challenger has a `suggest-when` field in its frontmatter:
   - Most challengers describe the context where they're relevant (e.g., "The task involves authentication, APIs, or payment processing")
   - Devil's Advocate has `suggest-when: always` — it is **always suggested** regardless of context, because it's universally useful
3. Match the task context (user request + Q&A clarifications + project context) against each `suggest-when` description
4. Present all matched challengers to the user:

> "For this session, I'd recommend these challengers:
> - **Devil's Advocate** — always recommended to challenge assumptions
> - **Technical Challenger** — [one-line reason why it's relevant to this task]
> - **Security Challenger** — [one-line reason why it's relevant to this task]
>
> Want to include them? You can also add or remove any."

5. The user can approve all, remove some, or add others from the gallery
6. Approved challengers are active for all subsequent challenge dispatches in this session
7. If the user declines all challengers, proceed but note: "No challengers active — skipping stress-test steps."
8. If no `.md` files are found in the `gallery/` directory, skip the suggestion step and proceed without challengers. Note: "No challengers available in gallery — skipping challenge steps."

**Important:** Challenger selection is session-only. Each brainstorming session evaluates the gallery fresh.

## Loading Challengers

1. Start with the challengers the user approved during suggest-and-confirm
2. Each file has YAML frontmatter with `name` and `when` fields
3. The `when` field accepts exactly two values:
   - `always` — dispatched for every challenge
   - `ui-only` — dispatched only when the change involves UI/UX (layout, styling, visual hierarchy, animations, component design)
4. Dispatch all challengers where `when: always`, plus those with `when: ui-only` if the change involves UI/UX

## Shared Context for All Challengers

Every challenger agent MUST receive the following context in its prompt so it can make informed judgments:

- **Original user request** — the feature/change the user asked for
- **Project conventions** — read the project's CLAUDE.md (and any sub-project CLAUDE.md files) and include relevant architecture patterns, coding rules, and conventions
- **Key file paths** — provide paths to relevant files as starting points (the agent has tool access to explore further)
- **Q&A collected** — key clarifications from the user during brainstorming
- **The proposal being challenged** — the full approaches or design being reviewed

## Challenger Agent Dispatch

Each challenger agent is dispatched using the Agent tool with `subagent_type: "general-purpose"`. Agents have access to Read, Grep, Glob and can independently explore the codebase beyond the starting context you provide.

## Challenge Point 1: Stress-Test Approaches

**When:** After you propose 2-3 approaches, BEFORE presenting to the user.

Dispatch all approved challengers in parallel. Each agent should receive the shared context plus the proposed approaches.

**Agent prompt template (adapt per challenger role):**
```
You are a [CHALLENGER_NAME] reviewing proposed approaches.

## Context
- Feature: [brief description of what we're building]
- Project conventions: [paste relevant CLAUDE.md patterns and rules]
- Key files to start exploring: [list relevant file paths]
- User clarifications: [paste key Q&A points]

## Proposed Approaches
[paste the 2-3 approaches with trade-offs and recommendation]

## Your Role
[paste the role's focus areas and rules from its challenger file]

## Rules
- You have tool access — use Read, Grep, Glob to explore the codebase and verify your concerns.
- Be specific and constructive. If you find a flaw, explain the flaw AND propose the better alternative.
- Do NOT nitpick or manufacture issues. Only flag things that would meaningfully change the design.
- If everything is solid from your perspective, respond with exactly: "NO_ISSUES"
```

**After all challengers respond:**
- If all return `NO_ISSUES` → proceed silently to "Present design sections." Do not mention the challenge.
- If any challenger found issues → synthesize the findings, revise the approaches incorporating valid feedback, then present the improved version to the user. Briefly note which approaches were strengthened and why.
- If revisions are substantial, re-run the challengers on the revised version. If minor, proceed.

## Challenge Point 2: Completeness Check

**When:** After the user approves the design sections, BEFORE writing the design doc.

Dispatch all approved challengers in parallel, but now focused on **completeness** rather than quality.

**Agent prompt template (adapt per challenger role):**
```
You are a [CHALLENGER_NAME] checking a design for completeness.

## Context
- Original user request: [paste the original request]
- User clarifications: [paste key Q&A points]
- Project conventions: [paste relevant CLAUDE.md patterns and rules]
- Key files to start exploring: [list relevant file paths]

## Approved Design
[paste the full approved design]

## Your Role (completeness focus)
[paste the role's completeness-specific focus from its challenger file]

## Rules
- You have tool access — use Read, Grep, Glob to explore the codebase and verify completeness.
- Only flag genuinely missing pieces that the user asked for or that are necessary for the feature to work correctly.
- Do NOT suggest additions beyond the user's requirements.
- If everything is covered from your perspective, respond with exactly: "COMPLETE"
```

**After all challengers respond:**
- If all return `COMPLETE` → proceed silently to "Write design doc." Do not mention the check.
- If any challenger found gaps → present the gaps to the user: "Before finalizing, a few details we should address: [list gaps]." Resolve each gap with the user, then update the design.

## Proportional Challenge

Scale the challenge intensity to the feature's complexity:

| Complexity | Signal | Challenge Level |
|---|---|---|
| **Small** | Config change, copy update, simple bugfix | Devil's Advocate only |
| **Medium** | New component, API endpoint, single-screen feature | All approved challengers |
| **Large** | New system, architectural change, multi-screen feature | All approved challengers |

Use your judgment. When in doubt, run the full challenge — the silent-by-default rule ensures it adds no noise if everything is fine.
