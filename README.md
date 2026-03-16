# Online Directory Create Expert Skill for Claude and Codex 🚀

![Hero](assets/main.png)

![MIT License](https://img.shields.io/badge/License-MIT-green.svg)
![Release](https://img.shields.io/badge/Release-v1.0.0-blue.svg)
![Downloads](https://img.shields.io/badge/Downloads-Package-yellow.svg)

Build an online directory in any niche and any country or region. This AI‑agent‑optimized skill takes you from niche discovery to a clean, verified, enriched dataset that’s ready for website creation — no guesswork, no fluff.

## Why This Exists
Most directory projects fail at the data stage. This skill focuses on the hardest part: acquiring, validating, and enriching listings into a dataset that is good enough to ship.

## Who This Is For 🎯
- Founders and builders creating a niche directory or marketplace.
- Analysts who need a repeatable directory data pipeline.
- Teams that want a geo‑agnostic and niche‑agnostic workflow.

## What You Get ✅
- A full workflow from niche discovery to a handoff package that actually ships.
- Evidence‑based verification (registry, website, phone, marketplace).
- Clean schema, batch protocols, and context‑health safeguards.
- Optional Crawl4AI guidance (not required).

## Use Cases
- Build a verified business listings dataset for a niche directory.
- Create a geo‑agnostic directory data pipeline for multiple regions.
- Compile a registry‑verified marketplace dataset for SEO landing pages.

## How It Works (High Level) ⚙️
1. Discover and validate a niche using deep research.
2. Collect data if none is provided.
3. Clean, verify, and enrich listings into a structured dataset.
4. Produce a handoff package for website creation (website build is out of scope).

![Workflow](assets/workflow.png)

## Example Output (Tiny Sample)
```csv
business_id,name,region,country,verified_match,confidence
abc123,Example Co,Ontario,CA,yes,0.91
def456,Sample Services,London,UK,yes,0.83
```

## Quick Start (Minimal Input) ⚡
If you only know the country or region, the agent will:
1. Propose niches using deep research.
2. Build a Niche Brief (keywords, trust signals, data sources).
3. Collect a sample dataset.
4. Run the validation workflow.

## Install Packages 📦
Download the prebuilt packages from this repo.

### Claude
1. Go to https://claude.ai/skills
2. Click "Upload Skill"
3. Upload `packages/online-directory-create-expert-claude.zip`

### Codex
1. Unzip `packages/online-directory-create-expert-codex.zip`
2. Copy `SKILL.md` into your Codex skills directory:
`%USERPROFILE%\.codex\skills\online-directory-create-expert\SKILL.md`

## Outputs (Handoff Package) 🧳
- `cleaned.csv`
- `verified_candidates.csv`
- `inventory.csv`, `images.csv`, `features.csv`, `service_areas.csv` (or explicit skip notes)
- `manual_review.csv`
- Batch logs and changelog notes

## Skill File 🧠
- `SKILL.md` contains the complete workflow and prompt pack.

## FAQ
**Does this work in my country or region?**  
Yes. The workflow is geo‑agnostic and supports registry, phone, and low‑web evidence.

**Do I need a dataset first?**  
No. If you provide only a country or region, the agent will collect a starter dataset.

## SEO Keywords
AI directory builder, online directory skill, niche discovery workflow, geo‑agnostic data enrichment, Claude skill, Codex skill, directory data pipeline, registry‑based verification, business listings dataset, directory website data, AI marketplace dataset, directory research workflow.
