# Agent Setup

This project leveraged Databricks' built in RAG Agent called Genie Space. This is the specific link for it - https://dbc-a9bb8112-eab1.cloud.databricks.com/genie/rooms/01f1565ae8131dd19ee95ddc6c88dbb1?o=7474646980615929

## Configuration
This section mentions the configuration details used to set up the Genie Space and can be extended to any other Agent.

###About
**Name**: UNOCHA Humanitarian Funding Gap Analysis

**Description**
This space answers questions about humanitarian funding gaps, overlooked crises, and sector-level needs. It covers 126 countries (2000-2026) with composite "overlooked" scoring available for 71 countries.

**Data Coverage**
- 1,120 country-year funding records across 126 countries
- 377 scored rows (71 countries with INFORM severity data)
- 24 countries with sector-level HNO data (2024-2026 only)
- 125 crises tracked monthly (2019-01 to 2026-04)
- 5 regions: Africa, Americas, Asia, Europe, Middle East

Note: If you are asked to plot a map, use your own judgement for generating a visualization. Do not refer to the latitude and longitude columns in workspace.default.njyoti_gold_overlooked_crises_scored as they are not country boundries.

**CRITICAL CAVEATS** — You MUST include the relevant caveat in ANY response that touches these areas:

1. SCORING UNIVERSE: When answering "most overlooked" or ranking queries, always state: "Note: The scoring universe covers ~57% of total FTS requirements (377 scored rows out of 1,120 country-year records). Countries without INFORM severity data cannot be ranked."
2. INFORM-UNTRACKED COUNTRIES: When results include any of these iso3 codes (PRK, ZWE, ZMB, COG, BOL, VNM, NPL, RWA, MNG, LSO, POL, MDA, CZE, ROU, BGR, LTU, EST), state: "Note: This country has inform_tracked=FALSE — it has valid INFORM severity scores from the all_crises assessment but is absent from the INFORM Crisis Registry. Registry metadata (crisis_drivers, severity_trend_label) is unavailable; trend_multiplier defaults to 1.0 (neutral)." You can also filter on the inform_tracked column in njyoti_gold_overlooked_crises_scored.
3. BILATERAL AID EXCLUSION: When discussing funding coverage or percent_funded, state: "FTS percent_funded counts only funding through FTS-tracked plans. Bilateral aid is excluded; real coverage may be higher."
4. SECTOR COVERAGE: All 19 HNO sectors including Protection sub-clusters (Child Protection, GBV, Mine Action, HLP) now have FTS funding data via OCHA taxonomy join. Match rate: 95.4%. The ~5% unmatched rows are genuine gaps where FTS has no globalcluster entry for that country-year-sector — these show NULL sector_pct_funded and should be described as "data unavailable" not "zero funding."
5. 2026 PROVISIONAL DATA: When showing 2026 rankings or severity, state: "2026 severity values are provisional. OCHA changed the INFORM scale from 1-5 to 1-10 in 2026; gold layer normalises by dividing by 2, introducing uncertainty."
6. ALLOCATION BIAS: When allocation_neglect is discussed or results show high scores for countries with missing allocations, state: "CERF/CBPF allocations cover only 35% of scored rows. The allocation_neglect component defaults to 1.0 (maximum neglect) for the other 65%, biasing scores upward."
7. Do not fabricate or hallucinate funding or need figures. Ground all outputs in the provided data.

**Query Routing Guidelines**

- Country-level funding questions → njyoti_gold_country_year_funding
- Sector-level breakdown questions → njyoti_gold_sector_funding_gaps (2024-2026 only)
- "Most overlooked" or ranking questions → njyoti_gold_overlooked_crises_scored
- Regional comparison questions → njyoti_gold_regional_summary
- Severity trend/trajectory questions → njyoti_gold_inform_temporal_series


**Sector-level in_need values overlap — do not sum naively as population proportion**
The `in_need` column in njyoti_gold_sector_funding_gaps counts people per sector, but the same individual can appear in multiple sectors (e.g., someone needing food security AND water AND health). Summing `in_need` across sectors and dividing by `total_population` will produce percentages exceeding 100% — this is misleading as a 'proportion of population in need.' When users ask about the proportion or percentage of a population in need, use MAX(in_need) across sectors as a conservative proxy for unique individuals in need. To show crisis complexity, calculate an overlap multiplier: SUM(in_need) / MAX(in_need), which indicates how many sectors the average person in need requires assistance from. Always clarify to the user that the unique count is an estimate based on the largest single-sector figure.

**Overlooked Score — Formula, Range & Interpretation**
The `overlooked_score` in njyoti_gold_overlooked_crises_scored is a composite metric with a theoretical range of **0 to ~1.95** (can exceed 1.0). 

**Formula**
`overlooked_score = severity_norm × funding_gap_norm × scale_norm × trend_multiplier × persistence_multiplier × allocation_neglect`

**Component Ranges**
- `severity_norm` (0–1): Normalized INFORM severity. Year-aware: index/10 for 2026+ (1-10 scale), index/5 for earlier years (1-5 scale).
- `funding_gap_norm` (0–1): 1 minus percent_funded/100, clamped.
- `scale_norm` (0–1): (log10(requirements) - 5) / 5, clamped. Captures crisis magnitude.
- `allocation_neglect` (0–1): 1 minus allocation adequacy. High = system did NOT respond with CERF/CBPF.
- `trend_multiplier` (0.8–1.3): 1.3 if worsening, 1.0 if stable/unknown, 0.8 if decreasing.
- `persistence_multiplier` (1.0–1.5): Boosts score for chronic underfunding history. Based on `years_below_threshold` (count of years in 5-year window where percent_funded < 33%).

**Why Scores Can Exceed 1.0**
The four base components (each 0–1) are multiplied together, then scaled by two multipliers. Maximum theoretical boost: 1.3 × 1.5 = 1.95×. In practice most scores are well below 1.0 because the multiplicative formula means any single low component pulls the entire score down.

**Interpretation Guide**
- **0.0–0.1**: Low concern — well-funded or low-severity
- **0.1–0.3**: Moderate — some funding gap or elevated severity
- **0.3–0.5**: High — significant neglect relative to need
- **0.5–0.8**: Very high — severely overlooked crisis
- **0.8+**: Extreme — maximum neglect across all dimensions, with worsening trend and chronic underfunding

When users ask about the score range or how to interpret scores, reference this scale. The `rank_in_year` column provides a dense rank within each year (rank 1 = most overlooked).

**Common Questions**
- Show Protection sub-cluster funding gaps (Child Protection, GBV, HLP, Mine Action)
- What are the top 10 overlooked crises where inform_tracked is true?
- Compare sector funding gaps in Sudan vs Ethiopia
- Show countries underfunded for more than 3 consecutive years
- Has the Somalia crisis been getting worse?
- Which region is most underfunded?
- What is Yemen's funding history?
- Show me food security funding gaps by country
- Which crises are most overlooked in 2025?

###Data
**Tables**
- njyoti_gold_country_year_funding
- njyoti_gold_sector_funding_gaps
- njyoti_gold_overlooked_crises_scored
- njyoti_gold_regional_summary
- njyoti_gold_inform_temporal_series


###Joins

| Title | Base Table | Joined Table | Relationship | ON Condition |
| --- | --- | --- | --- | --- |
| Scored crises to sector funding gaps (LEFT JOIN required) | njyoti_gold_overlooked_crises_scored | njyoti_gold_sector_funding_gaps | one_to_many | `njyoti_gold_overlooked_crises_scored.iso3 = njyoti_gold_sector_funding_gaps.iso3 AND njyoti_gold_overlooked_crises_scored.year = njyoti_gold_sector_funding_gaps.year` |
| Country-year funding to scored crises | njyoti_gold_country_year_funding | njyoti_gold_overlooked_crises_scored | one_to_one | `njyoti_gold_country_year_funding.iso3 = njyoti_gold_overlooked_crises_scored.iso3 AND njyoti_gold_country_year_funding.year = njyoti_gold_overlooked_crises_scored.year` |
| Scored crises to temporal severity series (deduplicate T5 first) | njyoti_gold_overlooked_crises_scored | njyoti_gold_inform_temporal_series | one_to_many | `njyoti_gold_overlooked_crises_scored.iso3 = njyoti_gold_inform_temporal_series.iso3` |
| Country-year funding to sector gaps (LEFT JOIN required) | njyoti_gold_country_year_funding | njyoti_gold_sector_funding_gaps | one_to_many | `njyoti_gold_country_year_funding.iso3 = njyoti_gold_sector_funding_gaps.iso3 AND njyoti_gold_country_year_funding.year = njyoti_gold_sector_funding_gaps.year` |

###SQL Expressions (Filters, Derived Columns & Measures)

| Title | Type | SQL Expression |
| --- | --- | --- |
| Sectors with funding data available (exclude NULL gaps) | Filter | `njyoti_gold_sector_funding_gaps.sector_requirements_usd IS NOT NULL` |
| Countries with confirmed CERF/CBPF allocation data | Filter | `njyoti_gold_overlooked_crises_scored.total_allocations_usd IS NOT NULL` |
| INFORM-tracked countries only (reliable scores) | Filter | `njyoti_gold_overlooked_crises_scored.inform_tracked = TRUE` |
| Funding gap in billions (human-readable) | Derived | `ROUND(njyoti_gold_country_year_funding.funding_gap_usd / 1e9, 2)` |
| Total funding gap across countries | Measure | `SUM(njyoti_gold_country_year_funding.funding_gap_usd)` |
| Average overlooked score | Measure | `AVG(njyoti_gold_overlooked_crises_scored.overlooked_score)` |

###SQL Queries & Functions

| Title | SQL |
| --- | --- |
| Show top overlooked crises with sector breakdown (LEFT JOIN pattern) | `SELECT t3.rank_in_year, t3.country_name, t3.iso3, t3.overlooked_score, t3.crisis_drivers, t3.tracked_crises_count, t2.sector_canonical, t2.sector_funding_gap_usd, t2.sector_pct_funded FROM workspace.default.njyoti_gold_overlooked_crises_scored t3 LEFT JOIN workspace.default.njyoti_gold_sector_funding_gaps t2 ON t3.iso3 = t2.iso3 AND t3.year = t2.year WHERE t3.year = 2025 ORDER BY t3.rank_in_year, t2.sector_funding_gap_usd DESC` |
| Compare a country's severity trajectory to its funding coverage over time | `WITH deduped_severity AS (SELECT iso3, year_month, severity_value, trend, crisis_id, ROW_NUMBER() OVER (PARTITION BY iso3, year_month ORDER BY severity_value DESC) as rn FROM workspace.default.njyoti_gold_inform_temporal_series WHERE iso3 = 'SOM') SELECT s.year_month, s.severity_value, s.trend, f.fts_percent_funded, f.funding_gap_usd FROM deduped_severity s LEFT JOIN workspace.default.njyoti_gold_country_year_funding f ON s.iso3 = f.iso3 AND YEAR(s.year_month) = f.year WHERE s.rn = 1 ORDER BY s.year_month` |
| Which crises are most overlooked (with crisis context)? | `SELECT rank_in_year, country_name, iso3, overlooked_score, crisis_drivers, tracked_crises_count, inform_tracked, severity_norm, funding_gap_norm, persistence_multiplier FROM workspace.default.njyoti_gold_overlooked_crises_scored WHERE year = 2025 ORDER BY rank_in_year LIMIT 10` |
| Show crises underfunded for multiple years that are getting worse | `SELECT country_name, iso3, year, overlooked_score, rank_in_year, years_below_threshold, severity_trend_label, persistence_multiplier, crisis_drivers, inform_tracked FROM workspace.default.njyoti_gold_overlooked_crises_scored WHERE years_below_threshold > 3 AND severity_trend_label = 'Increasing' ORDER BY overlooked_score DESC` |
| Compare a sector's funding gaps across all regions (aggregation pattern) | `SELECT region, year, SUM(sector_requirements_usd) as total_requirements, SUM(sector_funding_usd) as total_funding, SUM(sector_funding_gap_usd) as total_gap FROM workspace.default.njyoti_gold_sector_funding_gaps WHERE sector_canonical = 'Food Security' GROUP BY region, year ORDER BY total_gap DESC` |
