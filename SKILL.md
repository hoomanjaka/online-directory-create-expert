---
name: online-directory-create-expert
description: "Build a defensible online directory in any niche using a 7-step, data-first pipeline optimized for agent speed and context health."
---

# online-directory-create-expert

## Outcome
Turn raw listings into a verified, enriched, and deployable directory dataset using a repeatable pipeline that is fast, scalable, and safe for agent context limits.

## Context Health Rules (Non-Negotiable)
- Never paste full CSVs or full crawl dumps into the model. Use samples and aggregates.
- Always work in passes: sample first, scale later.
- Keep a stable schema and only add columns, never rename mid-pipeline.
- Store artifacts on disk; feed only the delta to the model.
- Use explicit batch sizes and record which batch was processed.
- Prefer deterministic scripts (pandas, regex) for cleaning; reserve LLMs for classification and extraction.

## Input Packet Standard (What to Send the Model)
Use this structure for every step to keep context consistent.

- Sample rows: 20-50 rows max, chosen for ambiguity or edge cases.
- Aggregate stats: counts, top categories, missing-field rates, duplicate rate.
- Errors from last batch: top 5 failure modes with examples.
- Required output schema: exact fields and allowed values.

## When to Use This Skill
Use this skill when you need a directory that wins on data quality, not just listings volume.

- You have raw listings (maps providers, vendor exports, industry lists, registries).
- You need to verify niche relevance at scale.
- You want structured enrichment (inventory, images, amenities, service areas).

## Inputs You Need
Required:
- Niche definition and decision drivers (price, features, availability, service radius).
- Keyword lists for verification (positive and negative).
Data sources (choose one or more):
- Raw CSVs of listings (multiple files OK).
- Registry or licensing exports.
- Marketplace or association lists.
If the user does not provide data, you must collect it using available sources before proceeding.

Optional:
- Any crawling/extraction tool that can fetch and parse websites at scale.
- Image scraping pipeline or existing image datasets.

## Minimal Input Mode (User-Friendly)
Minimum user input can be just a country and/or region.
If country/region is not provided, infer from available context or choose a reasonable default and state the assumption.

If other inputs are missing, do not block. Generate them yourself using deep research:
- Propose a short list of niches suitable for the region.
- Select one niche and generate the Niche Brief.
- Identify likely data sources (registries, associations, marketplaces, maps).
- Generate keywords, negatives, and trust signals.

## Optional Tooling: Crawl4AI (Suggested, Not Required)
If you want a robust crawler, Crawl4AI is a good default. Use this only if it fits your stack.

Install:
- `pip install crawl4ai`

Smoke test (minimal):
- Run a single known URL and confirm it returns main content and links.
- Verify rate limiting and retry behavior on a small batch.

Tuning checklist:
- Start with low concurrency (1-3) and increase gradually.
- Set timeouts and backoff to avoid bans.
- Keep crawl depth at 1-2 until verification accuracy is stable.

Proceed to data gathering only after the smoke test passes.

## Niche Discovery (Pre-step -1)
If you are starting without a niche, do this before Niche Setup.

- Generate 10-20 candidate niches.
- Score each by data availability, decision drivers, data moat potential, and monetization.
- Pick the top 1-3 for quick validation (Quick mode).

Output: a one-page niche brief (definition, decision drivers, trust signals, data sources, keywords).

Deep research requirement:
- Use first-principles research to justify niche choice and data sources.
- Prefer authoritative sources (official registries, government data, industry associations).

## Niche Setup (Agnostic, Minimum Viable)
Define these before any scraping or verification. Keep them short and testable.

- Niche definition: one sentence describing what qualifies and what does not.
- Decision drivers: 3-5 factors users care about (price, quality, speed, coverage, features).
- Positive keywords: 5-15 phrases that must appear in a provider description (website or registry text).
- Negative keywords: 5-15 phrases that indicate non-matches.
- Trust signals: reviews, certifications, licenses, registry presence, or verified contact details.

Trust signals operational order:
1. Official registries or licensing proof.
2. Verified website with clear service pages.
3. Verified phone or email tied to the business name.
4. Reviews or third-party marketplace profiles.

Success check: run verification on a 50-200 row sample and reach >80 percent precision on matches.

## Placeholder Fill (Auto)
Do not edit this skill manually. Before running any prompt, fill placeholders from the Niche Brief:
- {niche}: use the niche definition.
- {pos_1..pos_3}: top 3 positive keywords from the brief.
- {neg_1..neg_2}: top 2 negative keywords from the brief.
If the brief is missing, generate these from the candidate data and trust signals, then proceed.

## Execution Modes (Choose One)
Quick (fast validation):
- 1 sample CSV or registry extract, 50-200 rows.
- Run Steps 2-3 only.
- Goal: confirm niche viability and verification accuracy.
If no dataset is provided, collect a small sample first, then proceed.

Standard (default build):
- Full dataset, all 7 steps.
- Enrich only top verified candidates if budget-limited.

Deep (best quality):
- All 7 steps plus manual review and confidence scoring.
- Add a second verification pass and spot-checks.

Quick mode exit criteria:
- At least 50 verified candidates (or 10 percent of sample, whichever is smaller).
- Precision on a 20-row audit > 80 percent (or 10 percent sample if smaller).
- Cleaned schema matches the contract below.

## Core Workflow (7 Steps)
Before starting, pick a niche and list decision drivers users care about.

1. Collect raw data from sources (scraping, registry exports, marketplace lists).
2. Clean and consolidate CSVs, removing junk and irrelevant businesses.
3. Verify sources at scale using a crawler or registry plus LLM prompts.
4. Enrich inventory data (what the business offers, counts, types).
5. Enrich images with strict filtering rules and edge-case handling.
6. Extract amenities and features to power filters and comparison.
7. Extract service areas with location-aware accuracy rules.

Pre-step 0: Deduplicate and normalize identifiers.
- Create a stable business_id (hash of name + address + phone).
- Merge duplicates across sources and keep a provenance column.
- Normalize name, address, and phone before hashing (lowercase, strip punctuation, standardize suite/unit).
- Tie-break rule: keep the record with the most complete fields; merge the rest into provenance.
- If address/phone are missing, use registry_id or name + region + country as the hash inputs.

## Prompt Pack (Cleaned Templates)
Replace placeholders using the Niche Brief.

### Step 2: Clean and Consolidate CSVs
```text
You are cleaning and consolidating multiple CSVs of {niche} listings into one clean dataset.

Task:
- Merge all CSVs into a single consolidated CSV.
- Remove any row that matches ANY removal criteria below.
- Normalize columns and values (trim whitespace, consistent casing, standardize phone and URLs).
- Preserve registry_id and parent_id if present.

Removal criteria (remove if ANY applies):
- Missing business name or missing street-level address where applicable (administrative area alone is not enough).
- Permanently closed or temporarily closed.
- Not actually a {niche} business based on name or category.
- Clearly unrelated website with no supporting registry/phone evidence.
- Trust signals exist and indicate low quality or non-operational status.

Output:
- cleaned.csv (stable schema)
- removed_rows.csv (include reason)
```
Schema:
- cleaned.csv: business_id, name, address, city, region, postal_code, country, phone, website, category, source, parent_id (optional), registry_id (optional)
- removed_rows.csv: business_id, name, reason, source

### Step 3: Tiered Verification (Crawler + LLM)
```text
Goal: verify that each candidate is a real {niche} provider.

Tier 1: fast keyword filter (website sources only)
- Crawl homepage + top pages when a website exists.
- Positive keywords: {pos_1}, {pos_2}, {pos_3}
- Negative keywords: {neg_1}, {neg_2}
- If any reasonable chance it is a {niche} provider, keep it for Tier 2.

Tier 1 (offline/low-web alternative):
- Use registry or licensing lookup when websites are missing.
- Validate via phone or official contact channels where applicable.
- Record evidence_type as registry or phone.

Tier 2: LLM verification
For each candidate source, answer:
- Is this business actually a {niche} provider? (yes/no)
- What products/services indicate this?
- Extract a 1-2 sentence proof summary.
Apply trust signal order (registry > website > verified contact > reviews).

Output:
- verified_candidates.csv with columns: is_match, proof, confidence, signals_found, evidence_type, source_url, registry_id (optional)
```
Schema:
- verified_candidates.csv: business_id, is_match (yes/no), confidence (0-1), signals_found, proof, evidence_type, source_url, registry_id (optional)

Routing:
- confidence >= 0.7 -> accept
- confidence 0.5-0.69 -> manual_review.csv
- confidence < 0.5 -> reject

Trust signal override:
- If registry evidence exists, it overrides conflicting website evidence.

Populate parent_id:
- If a brand has multiple locations, set parent_id to the brand entity id.

### Step 4: Offerings/Inventory Enrichment
```text
Extract offerings/inventory data from verified sources.

For each business:
- Identify product types and counts.
- Capture any pricing or availability signals.
- Record evidence text and source page URL.

Output:
- inventory.csv with columns: business_id, inventory_items, inventory_counts, price_signals, evidence, source_url
```
Schema:
- inventory.csv: business_id, inventory_items, inventory_counts, price_signals, evidence, source_url
Note:
- Use inventory.csv even for service-based niches; treat inventory_items as offerings.

### Step 5: Image Enrichment
```text
Collect only relevant product images.

Rules:
- Use images from pages already scraped.
- Or use provided datasets when pages do not exist.
- Exclude logos, favicons, and unrelated imagery.
- Only include images that clearly show the product type and relevant features.
- Quality check each image before inclusion.
If images are not relevant for the niche, skip this step and note it in logs.

Output:
- images.csv with columns: business_id, image_url, product_type, confidence, source_url
```
Schema:
- images.csv: business_id, image_url, product_type, confidence (0-1), source_url

### Step 6: Amenities and Features
```text
Extract amenities and features into structured columns.

Rules:
- One column per feature family (lighting, climate control, ADA, generators, etc.).
- Separate "core" vs "specialty" features if the niche mixes products.
- Normalize inconsistent wording (e.g., "A/C" -> "air_conditioning").
If features are not meaningful for the niche, skip this step and note it in logs.

Output:
- features.csv with standardized boolean or categorical fields.
```
Schema:
- features.csv: business_id, feature_family, feature_value, evidence, source_url

### Step 7: Service Area Extraction
```text
Extract service areas with location-aware accuracy.

Rules:
- Prefer explicit service area pages.
- If a business HQ is in a specific locality/region, do not assign service areas outside that area unless explicitly stated.
- For multi-location businesses, map service areas to each location separately.

Output:
- service_areas.csv with columns: business_id, service_area, scope, evidence, evidence_type, source_url
```
Schema:
- service_areas.csv: business_id, service_area, scope (city/district/region/state/province/country), evidence, evidence_type, source_url

## Confidence Rubric (Use for All LLM Decisions)
- 0.9-1.0: Explicit evidence on the site matching the niche, with product/service detail.
- 0.7-0.89: Strong signals but missing one key detail.
- 0.5-0.69: Ambiguous; keep only if niche requires broad inclusion.
- < 0.5: Exclude or send to manual review.

## Batch Protocol (Resumable)
- Split work into numbered batches: batch_001.csv, batch_002.csv.
- Each batch produces its own output file with the same suffix.
- Maintain a pending.csv with unprocessed business_ids.
- After each batch, append results to the master file and log stats.
Status values:
- pending, processing, done, error

## Schema Contracts (Explicit)
cleaned.csv
- business_id, name, address, city, region, postal_code, country, phone, website, category, source, parent_id (optional), registry_id (optional)

removed_rows.csv
- business_id, name, reason, source

verified_candidates.csv
- business_id, is_match, confidence, signals_found, proof, evidence_type, source_url, registry_id (optional)

inventory.csv
- business_id, inventory_items, inventory_counts, price_signals, evidence, source_url

images.csv
- business_id, image_url, product_type, confidence, source_url

features.csv
- business_id, feature_family, feature_value, evidence, source_url

service_areas.csv
- business_id, service_area, scope, evidence, evidence_type, source_url

manual_review.csv
- business_id, reason, suggested_action, evidence, evidence_type, source_url, registry_id (optional)

pending.csv
- business_id, batch_id, status

## Field Types and Constraints (Geo-Agnostic)
- business_id: stable hash string.
- parent_id: optional brand/entity id for multi-location businesses.
- registry_id: optional id from official registries/licensing sources.
- name: string, required.
- address: string, required if available; allow free-form for non-structured regions.
- city/region/postal_code/country: optional; use region/country fields where applicable.
- phone: string, preserve original formatting; optionally add normalized_phone.
- website/source_url: valid URL or registry/phone reference string, or empty.
- confidence: float 0-1.
- scope: one of locality, city, district, region, state, province, country.
- evidence_type: one of website, registry, phone, marketplace, manual

## Compound Field Format
- inventory_items: pipe-separated list (item1|item2|item3).
- inventory_counts: JSON object string ({"item":"count"}).
- signals_found: pipe-separated list of keyword hits.
- feature_value: use normalized tokens (snake_case).

## Website Quality Rules
- If a website is parked, empty, or redirects to an unrelated domain, mark as no_working_site.
- If only social profiles exist, keep only if the niche allows social-only presence.
- Flag marketplace/aggregator pages and require explicit proof of ownership before accept.
- If no website exists, keep only if the region supports phone-only or registry-only businesses.
Heuristics:
- Parked/empty: "domain for sale", "coming soon", no contact/about/service pages.
- Aggregator: lists many unrelated businesses with no operator branding.
- Redirects: if primary domain redirects to another niche, mark as unrelated.

## Crawl Ethics and Politeness
- Respect robots.txt where required.
- Rate-limit crawling and retry with backoff.
- Avoid scraping personal emails or sensitive personal data.

## Offline/Low-Web Regions
- If web presence is sparse, accept registry or phone validation as primary evidence.
- Use evidence_type to record registry/phone/website sources.

## Data Privacy Note
- Follow local data protection laws; avoid storing personal data unless explicitly permitted.

## Handoff Package (Website Creation Starts After This)
Deliver the following package before moving to website creation:
- Niche brief and decision drivers
- Cleaned dataset (cleaned.csv)
- Verified dataset with evidence (verified_candidates.csv)
- Enrichment files (inventory.csv, images.csv, features.csv, service_areas.csv) or explicit skip notes
- Manual review queue (manual_review.csv)
- Batch logs and changelog notes

## Stop Conditions (Per Step)
- Step 2: < 5 percent duplicates remain and removal reasons stabilize.
- Step 3: Verification precision > 80 percent on audit sample.
- Step 4-7: New information rate drops below 10 percent per batch.

Define "new information rate":
- (new non-empty fields added in batch) / (total fields expected in batch)

Define "removal reasons stabilize":
- Top 3 removal reasons change by less than 5 percent across 2 consecutive batches.

## Multi-Location Handling
- Split multi-location businesses into separate records per location before Step 7.
- Do not apply service areas across locations without explicit evidence.
- For franchise networks, keep a parent_id and child location rows.

## Agent Performance Tuning
Use these constraints to keep runs fast and stable.

- Batch size: 50-200 businesses per prompt for verification and enrichment.
- Crawl depth: start at 1-2 pages, expand only when confidence < 0.7.
- If not crawling websites, skip crawl depth and rely on registry/phone evidence.
- Always write outputs to disk between passes; never keep full data in memory.
- Use a stable business_id as the join key across all files.
- Maintain a "pending.csv" queue for unprocessed rows to allow resumable runs.

## Token Budget Guide (Context Health)
- Per call target: <= 2,500 tokens input, <= 1,000 tokens output.
- If larger context is needed, split into smaller batches and summarize results.

Batch summary template:
- Summary stats (counts, missing rates, duplicates).
- Top 10 errors and examples.
- 20 edge rows with expected outputs.

## Minimal Schema (Recommended)
- business_id (stable key)
- parent_id (optional)
- registry_id (optional)
- name
- address
- city
- region
- postal_code
- country
- phone
- website
- category
- verified_match (yes/no)
- confidence

## Quality Gates
- Sample cleaned rows to verify removal criteria correctness.
- Sample verified rows to check classification accuracy.
- Manually review image sets for relevance and rights risk.
- Review features columns for junk tokens or malformed entries.
- Validate service areas against the listing's HQ locality/region.
- Track false positives and rerun passes with edge-case fixes.

Verification audit protocol:
- Sample 20 rows (or 10 percent if smaller): 10 accepted, 10 rejected.
- Count false accepts and false rejects.
- If > 20 percent errors, revise keywords or thresholds.

## Common Failure Modes and Fixes
- Too many false positives in verification: tighten negative keywords and add Tier 1 thresholds.
- Missing inventory details: expand crawl depth only for low-confidence candidates.
- Noisy features columns: enforce strict vocab mapping and drop rare tokens.
- Service areas spanning outside declared locality/region: split multi-location businesses first.
- Duplicate listings across sources: hash normalized name+address+phone and merge before Step 2.
- Marketplace listings misclassified: require direct operator identifiers (about page, contact email domain).

## Multilingual and Regional Variants
- Maintain keyword lists in local languages and common transliterations.
- If the region lacks street addresses, accept landmark-style addresses and use region/country fields.

## Dataset Versioning
- Version outputs per run: v1_cleaned.csv, v2_cleaned.csv, etc.
- Never overwrite previous master files; append a changelog note.

## Website Build Output (Optional)
If you are building the directory site:
- Define required filters from features.csv and service_areas.csv.
- Generate SEO pages by region and feature.
- Ensure listings link to verified websites only.
- Localize language, currency, and phone formatting to the target region.

## Artifacts and File Layout (Suggested)
- `raw/` raw CSVs
- `cleaned/cleaned.csv`
- `cleaned/removed_rows.csv`
- `verify/verified_candidates.csv`
- `verify/manual_review.csv`
- `enrich/inventory.csv`
- `enrich/images.csv`
- `enrich/features.csv`
- `enrich/service_areas.csv`
- `logs/pending.csv`
- `logs/` batch logs and error reports

## Notes and Cautions
- Image scraping carries rights risk; consider stock images or explicit permission.
- SEO timelines are long; this is not a fast-revenue strategy.
- Data quality drives trust; prioritize accuracy over speed.
