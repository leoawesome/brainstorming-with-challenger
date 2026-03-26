# Lazy-Loading Challengers via Catalog Index

**Date:** 2026-03-27
**Status:** Implemented

## Problem

When the brainstorming skill is invoked, the AI reads all 12 challenger `.md` files during the suggest-and-confirm step just to decide which 3-4 challengers to suggest. This wastes tokens and dilutes context quality -- Anthropic's own data shows tool selection accuracy drops from 88% to 79.5% when too much is in context.

## Solution

Two-tier lazy-loading pattern (mirrors Claude ToolSearch, ChatGPT plugins, VS Code manifests):

1. **Tier 1 -- Lightweight index (`gallery/index.yaml`):** Contains `name`, `file`, `when`, `suggest-when`, and a one-line `description` for each challenger. Read once during suggest-and-confirm.
2. **Tier 2 -- Full definitions (`gallery/*.md`):** Only loaded for approved challengers, right before dispatch at challenge points.

## Changes

- **New file: `gallery/index.yaml`** -- Manually maintained catalog of all 12 challengers with lightweight metadata.
- **Updated: `challenge-protocol.md`** -- Suggest-and-confirm now reads `index.yaml` instead of all `.md` files. Loading Challengers section clarifies that full files are read only at dispatch time.
- **Updated: `SKILL.md`** -- Checklist step 3 references `gallery/index.yaml` and explicitly says not to read individual files yet.

## Maintenance

When adding a new challenger:
1. Create the `.md` file in `gallery/`
2. Add a corresponding entry in `gallery/index.yaml`

The index is manually maintained. The gallery is small (12 challengers) and changes infrequently, so auto-generation would add unnecessary complexity.
