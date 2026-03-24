# Challenge Protocol

At two key points in the brainstorming process, dispatch a team of 3-4 challenger agents **in parallel** to stress-test the work (4 when the change involves UI/UX — include the Design Challenger). Challengers operate **silently by default** — they only surface findings when they identify genuine improvements or gaps.

<IMPORTANT>
The purpose of challenges is quality, not ceremony. If all challengers find nothing wrong, proceed without mentioning the challenge ran. Only surface findings that would actually change the design.
</IMPORTANT>

## Shared Context for All Challengers

Every challenger agent MUST receive the following context in its prompt so it can make informed judgments:

- **Original user request** — the feature/change the user asked for
- **Project conventions** — read the project's CLAUDE.md (and any sub-project CLAUDE.md files) and include relevant architecture patterns, coding rules, and conventions
- **Codebase findings** — relevant files, existing patterns, and code discovered during exploration
- **Q&A collected** — key clarifications from the user during brainstorming
- **The proposal being challenged** — the full approaches or design being reviewed

## The Challenger Team

**1. Technical Challenger** — Is the approach sound and scalable?
- Is there a simpler architecture that achieves the same result?
- Will this scale? What breaks under heavy load?
- Is the data model correct? Unnecessary complexity? Missing relationships?
- Are there performance pitfalls (N+1 queries, missing indexes, unnecessary joins)?
- Does the API design follow the project's established patterns?

**2. Product Challenger** — Is the UX good and complete?
- Is there a better UI/UX pattern for this interaction?
- Are edge states handled (empty, loading, error, offline)?
- Does the layout/flow feel natural for the user's goal?
- Are accessibility and i18n accounted for?
- Is the component structure clean — no unnecessary abstraction, no missing reuse?

**3. Devil's Advocate** — What's the strongest argument against the recommendation?
- Actively argue against the recommended approach
- Find the single biggest risk or weakness
- Propose the strongest counter-approach — if one exists, describe it with enough detail to compare
- Challenge assumptions that everyone seems to take for granted
- If the recommendation truly is the best option, say so — do not manufacture objections

**4. Design Challenger** *(UI/UX changes only)* — Does the visual design actually improve the experience?
- **Only dispatched when the change involves UI/UX** (layout, styling, visual hierarchy, animations, component design). Skip entirely for backend, API, data model, or logic-only changes.
- Would a real user perceive this as an improvement, or just "different"?
- Does the design maintain visual hierarchy — is the most important information the most prominent?
- Are colors, spacing, and typography working together or fighting each other?
- Does the design hold up in edge states (empty data, max values, long text, small screens)?
- Compare against polished reference apps in the same category — does this feel competitive or amateur?
- Would this look good in a screenshot? First impressions matter.
- Flag when proposed changes add visual noise without clear user value (e.g., gradients that reduce contrast, decorations that distract from data)
- If available, review the actual screenshot/mockup, not just the code description

## Challenge Point 1: Stress-Test Approaches

**When:** After you propose 2-3 approaches, BEFORE presenting to the user.

Dispatch all challengers in parallel using the Agent tool (3 for non-UI changes, 4 for UI/UX changes — include Design Challenger). Each agent should receive the shared context (see above) plus the proposed approaches.

**Agent prompt template (adapt per challenger role):**
```
You are a [Technical Challenger / Product Challenger / Devil's Advocate / Design Challenger] reviewing proposed approaches.

## Context
- Feature: [brief description of what we're building]
- Project conventions: [paste relevant CLAUDE.md patterns and rules]
- Codebase context: [paste relevant existing code patterns and files found]
- User clarifications: [paste key Q&A points]

## Proposed Approaches
[paste the 2-3 approaches with trade-offs and recommendation]

## Your Role
[Role-specific instructions from the challenger descriptions above]

## Rules
- Be specific and constructive. If you find a flaw, explain the flaw AND propose the better alternative.
- Do NOT nitpick or manufacture issues. Only flag things that would meaningfully change the design.
- If everything is solid from your perspective, respond with exactly: "NO_ISSUES"
```

**After all challengers respond:**
- If all return `NO_ISSUES` → proceed silently to "Present design sections." Do not mention the challenge.
- If any challenger found issues → synthesize the findings, revise the approaches incorporating valid feedback, then present the improved version to the user. Briefly note which approaches were strengthened and why.

## Challenge Point 2: Completeness Check

**When:** After the user approves the design sections, BEFORE writing the design doc.

Dispatch all challengers in parallel (3 for non-UI, 4 for UI/UX changes), but now focused on **completeness** rather than quality.

**Agent prompt template (adapt per challenger role):**
```
You are a [Technical Challenger / Product Challenger / Devil's Advocate / Design Challenger] checking a design for completeness.

## Context
- Original user request: [paste the original request]
- User clarifications: [paste key Q&A points]
- Project conventions: [paste relevant CLAUDE.md patterns and rules]

## Approved Design
[paste the full approved design]

## Your Role (completeness focus)
- [Technical]: Are all technical requirements addressed? Missing error handling, auth, migrations, backward compatibility?
- [Product]: Are all UX requirements addressed? Missing empty states, loading states, i18n, accessibility, responsive behavior?
- [Devil's Advocate]: What did everyone overlook? What small detail will cause problems later? Is there scope creep beyond what was asked?
- [Design] *(UI/UX only)*: Does the visual design hold up across all states (empty, full, overflow)? Are colors, spacing, and typography consistent? Would this look polished in a real screenshot?

## Rules
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
| **Small** | Config change, copy update, simple bugfix | Skip challenge — proceed directly |
| **Medium** | New component, API endpoint, single-screen feature | Run all 3 challengers |
| **Large** | New system, architectural change, multi-screen feature | Run all 3 challengers |

Use your judgment. When in doubt, run the challenge — the silent-by-default rule ensures it adds no noise if everything is fine.
