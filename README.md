# Online Directory Create Expert Skill (Claude + Codex)

Build a defensible online directory in any niche and any country or region. This skill is AI-agent optimized, context-healthy, and designed to move you from niche discovery to a handoff package that is ready for website creation.

## What This Skill Does
- Finds and validates a niche using deep research when the user provides only a country and/or region.
- Collects data if none is provided.
- Cleans, verifies, and enriches listings into a structured dataset.
- Produces a handoff package for website creation (website build is intentionally out of scope).

## Key Features
- Niche-agnostic and geo-agnostic.
- Evidence-based verification with registry, website, phone, and marketplace support.
- Context-health safeguards and batch protocols.
- Optional Crawl4AI guidance (not required).

## Quick Start (Minimal Input)
If you only know the country or region, the agent will:
1. Propose niches using deep research.
2. Build a Niche Brief (keywords, trust signals, data sources).
3. Collect a sample dataset.
4. Run the validation workflow.

## Install Packages
Download the prebuilt packages from this repo.

### Claude
1. Go to https://claude.ai/skills
2. Click "Upload Skill"
3. Upload `packages/online-directory-create-expert-claude.zip`

### Codex
1. Unzip `packages/online-directory-create-expert-codex.zip`
2. Copy `SKILL.md` into your Codex skills directory:
`%USERPROFILE%\.codex\skills\online-directory-create-expert\SKILL.md`

## Skill File
- `SKILL.md` contains the complete workflow and prompt pack.

## Outputs (Handoff Package)
- `cleaned.csv`
- `verified_candidates.csv`
- `inventory.csv`, `images.csv`, `features.csv`, `service_areas.csv` (or explicit skip notes)
- `manual_review.csv`
- Batch logs and changelog notes

## SEO Keywords
AI directory builder, online directory skill, niche research workflow, geo-agnostic data enrichment, Claude skill, Codex skill, directory data pipeline, registry-based verification.
