# Marketing Challenger Design

**Date:** 2026-03-26
**Status:** Approved

## Summary

Add a single Marketing Challenger to the gallery that covers positioning, go-to-market strategy, growth channels, SEO, and messaging clarity. Triggers on user-facing features, launches, public content, pricing, onboarding, or growth strategy.

## Challenger Definition

**File:** `plugin/skills/brainstorming/gallery/marketing.md`

### Frontmatter

- `name`: Marketing Challenger
- `when`: always
- `suggest-when`: "The task involves a user-facing feature, product launch, landing page, public-facing content, pricing, onboarding flow, or growth strategy"

### Focus — Stress-Test

- Is the target audience clearly defined? Will this resonate with them?
- How will users discover this? What acquisition channels apply?
- Are there SEO implications — discoverability, keyword relevance, URL structure, meta content?
- Does the positioning differentiate from alternatives, or does it blend in?
- Is the value proposition immediately clear to a first-time visitor?
- Is the launch timing and sequencing realistic for the target market?

### Focus — Completeness

- Are success metrics defined for acquisition, activation, and retention?
- Is there a plan for how users find and reach this feature/product (organic, paid, referral)?
- Are SEO fundamentals covered — crawlability, semantic markup, page speed, structured data?
- Is messaging consistent across touchpoints (landing page, onboarding, in-product)?
- Is there a feedback loop to validate product-market fit after launch?

### Rules

Same as all gallery challengers:
- Be specific and constructive. If you find a flaw, explain the flaw AND propose the better alternative.
- Do NOT nitpick or manufacture issues. Only flag things that would meaningfully change the design.
- Respond "NO_ISSUES" (stress-test) or "COMPLETE" (completeness) if nothing to flag.

## Design Decisions

- **One broad challenger** rather than split (Go-to-Market + Growth/SEO) — matches gallery pattern, avoids fragmentation
- **`when: always`** with scoped `suggest-when` — marketing isn't relevant for internal tooling or refactoring, so it only gets suggested for user-facing/public-facing work
- **SEO integrated** into both stress-test and completeness sections rather than a separate challenger
