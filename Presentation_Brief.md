# PRESENTATION BRIEF — Geo-Insight: The Overlooked Crises Response Center

## Instructions for GenAI Presentation Tool

**Output:** 7-slide deck (expandable to 8 with appendix).  
**Audience:** Humanitarian decision-makers (UN OCHA, donor strategy teams, NGO leadership). Non-technical. They care about: what crises are being missed, why this system is trustworthy, and what action it enables.  
**Tone:** Authoritative and urgent, but measured. No sensationalism. Let the data carry emotional weight.  
**Design guidance:** Minimal text per slide (max 5 bullet points, max 12 words each). Use visuals: maps, comparison tables, flow diagrams. Every slide should have one "anchor number" displayed prominently.  
**Narrative arc:** Problem → Insight → Method → Proof → Transparency → Action.

---

## SLIDE 1 — THE PROBLEM (Title Slide + Problem Statement)

**Anchor number:** 10+ disconnected data systems  
**Visual:** Split graphic — fragmented data icons on left, a single unanswered question on right  
**Narrative beat:** Open with the frustration humanitarian analysts face daily.

**Content:**

Title: "Finding the Crises No One Is Watching"

The international humanitarian system has no single tool that answers: *"Where is the biggest mismatch between documented human need and actual response?"*

- Funding data (FTS), severity assessments (INFORM), sector needs (HNO), and emergency allocations (CERF/CBPF) live in 10+ separate systems
- Joining them requires reconciling inconsistent schemas, conflicting naming conventions, and structural gaps across numerous Excel workbooks and CSVs
- This process can be manual, cumbersome, with no institutionalized memory and critical!
- Result: systematic blind spots where severe crises receive negligible funding for *years* without detection

**Speaker note:** "This isn't a technology problem. It's an attention problem. The data exists — but no one can see the full picture."

---

## SLIDE 2 — THE CORE INSIGHT (What "Overlooked" Actually Means)

**Anchor number:** Sudan (severity 4.7, gap 60%) ranks *below* Türkiye (severity 3.5, gap 94%)  
**Visual:** Side-by-side comparison — two crises, one "underfunded," one "overlooked." Make the distinction visceral.  
**Narrative beat:** This is the intellectual contribution. Redefine the problem before presenting the solution.

**Content:**

"Overlooked" ≠ "Most Severe"

- A severity-5.0 crisis funded at 40% is *underfunded* — but it's on the international radar, receiving meaningful response
- A severity-3.5 crisis funded at 6% is *overlooked* — the international system has almost entirely failed to respond despite documented need
- This system measures **response absence relative to need**, not crisis magnitude

The "Stable Catastrophe" problem:
- Sudan (severity 4.7), DRC (4.5), Afghanistan (4.5) — crises so severe for so long the world stopped watching them
- These rank high precisely *because* their severity persists while attention has moved on
- Any system that only promotes "worsening" crises will systematically miss the catastrophes that stabilised at catastrophic levels

**Speaker note:** "We're not replacing severity rankings. We're asking a different question: where has the international community's *response* most failed relative to what's documented?"

---

## SLIDE 3 — THE SCORING MECHANISM (How We Quantify "Overlooked")

**Anchor number:** 6 dimensions, all must be present simultaneously  
**Visual:** Multiplicative formula shown as interlocking gears or a Venn diagram — if any gear stops, score = zero  
**Narrative beat:** Establish rigour. This audience needs to trust the number before they act on it.

**Content:**

Formula: `overlooked_score = severity × funding_gap × scale × trend × persistence × allocation_neglect`


Scores cluster in the [0, 0.5] range for the majority of country-years. Overlooked crises typically score 0.3–0.6.

- 0.5–0.8 | Very High | Severely overlooked — large gap, meaningful scale, limited response |
- 0.8+ | Extreme | Maximum neglect

**Why multiplicative (not additive):** A fully-funded crisis scores ZERO regardless of severity. The formula enforces intersection — a crisis must be simultaneously severe, underfunded, large-scale, and persistently neglected.

| Dimension | What It Measures | Why It's Necessary |
|-----------|-----------------|-------------------|
| Severity | Is this a real crisis? | Prevents trivial $100K appeals from ranking above $2B crises |
| Funding Gap | How absent is the response? | Core signal — highest correlation with final score |
| Scale | How many people at stake? | A 95% gap on $200K ≠ a 70% gap on $3B |
| Trend | Is it getting worse? | Amplifies worsening + underfunded crises |
| Persistence | Chronic or one-time? | 4+ consecutive underfunded years = structural neglect |
| Allocation Neglect | Are last-resort funds also missing? | Signals failure even at emergency-response level |

**Key design choice:** Severity is linear, not amplified. Ecuador (severity 2.8, 92% unfunded, worsening, persistent) IS  overlooked — amplifying severity would incorrectly demote it to a lower rank.

**Speaker note:** "Each dimension answers a different question. The multiplicative design means you can't game the ranking — you can't be 'overlooked' if you're well-funded, and you can't rank high from severity alone."

---

## SLIDE 4 — VALIDATION & ROBUSTNESS (Why You Can Trust This)

**Anchor number:** 4 alternatives tested and rejected with documented evidence  
**Visual:** Comparison table (current vs. rejected approaches) with clear "Pass/Fail" indicators  
**Narrative beat:** Pre-empt skepticism. Show that the methodology survived adversarial testing.

**Content:**

The formula was stress-tested against 4 alternative scoring designs on 377 scored country-years (71 countries, 2020–2026):

| Alternative Tested | Why Rejected |
|---|---|
| Amplified severity (^1.5) | Conflates "most severe" with "most overlooked." Ecuador drops from #4 → #22 despite 92% unfunded. |
| Continuous trend (numerical delta) | INFORM's expert label and numerical delta are *uncorrelated* (r=0.052). Only 41% agreement. The number is a lagging indicator of a lagging indicator. |
| Two-stage approach (gap + priority sort) | 93.7% of countries collapse into tie groups. Philippines (147% funded) ranks #8. Sudan, Afghanistan, DRC drop to ranks 43–62. |
| Additive/weighted sum | A fully-funded, high-severity crisis could score the same as a neglected one. Violates the core concept. |

Validation results of chosen formula:
- **Spread:** Top-ranked crises score 6.5× higher than bottom (sufficient discrimination)
- **Face validity:** 7 of 8 known severe underfunded crises appear in the top 20 (Afghanistan, Yemen, South Sudan, DRC, Somalia, Syria, Myanmar)
- **Stability:** Removing any single component changes rankings by less than .4% — no fragile dependencies


---

## SLIDE 5 — SYSTEM ARCHITECTURE (From Raw Data to Conversational Answers)

**Anchor number:** 67 workbooks + 30 CSVs → 1 conversational interface  
**Visual:** Simplified left-to-right pipeline diagram: Raw Data → Bronze (363 tables) → Silver (23 tables) → Gold (5 tables) → RAG Agent  
**Narrative beat:** Keep this brief. The audience doesn't need to understand Medallion architecture — they need confidence the plumbing is sound.

**Content:**

High-level flow:

1. **Ingest** — 67 Excel workbooks + ~30 CSVs bulk-ingested (manifest-driven, no manual configuration needed for new data drops)
2. **Clean** — Multi-header Excel parsing, schema drift handling, deduplication (e.g., INFORM temporal data: 251,008 rows reduced to 6,889 — each monthly release contained full history)
3. **Synthesise** — 5 purpose-built analytical tables, each designed for a specific question type. Map-ready outputs with denormalized coordinates (100% coverage, zero NULLs)
4. **Answer** — Conversational RAG agent (Databricks Genie Space) translates natural-language questions into SQL, executes, returns answers with visualisations

Key architectural decisions:
- Separating parsing from analysis means the system can be rebuilt/updated without re-processing 4+ hours of Excel ingestion
- 5 gold tables (not 1 wide, not 10 narrow) — each question type maps to 1-2 tables, preventing the agent from making incorrect joins
- External taxonomy datasets (HDX country reference + HPC cluster taxonomy) resolved a critical join gap: sector matching improved from 38% → 95%

**Speaker note:** "You don't need to understand the engineering. The point is: messy, fragmented UN data goes in one end, and reliable answers come out the other — in natural language, with visualisations."

---

## SLIDE 6 — TRANSPARENCY: ASSUMPTIONS, LIMITATIONS & DATA GAPS

**Anchor number:** 43.2% of FTS requirements are currently unscored  
**Visual:** Honest coverage chart showing what's included vs. what's missing  
**Narrative beat:** Build trust through transparency. Acknowledging gaps *strengthens* credibility with this audience.

**Content:**

**What the score captures:**
- 377 scored country-years across 71 countries (57% of total dollar requirements)
- 6 years of data (2020–2026), most reliable from 2020 onward
- INFORM severity, FTS funding, HPC sector needs, CERF/CBPF allocations

**What it does NOT capture:**
- Bilateral and private aid (excluded from FTS — real funding coverage may be higher than reported)
- 17 countries have severity scores but lack crisis metadata (trend defaults to neutral)
- CERF/CBPF allocation data covers only 35% of scored rows — remainder defaults to maximum neglect assumption
- Static population figures (no year-over-year growth adjustment)
- 2026 data is provisional (partial year for both INFORM and FTS)

**Declared external datasets:**
- Countries & Territories Taxonomy (HDX) — canonical country names, regions, coordinates
- HPC Global Cluster Taxonomy (HPC API) — 22-row bridge for sector matching

**Agent constraints:**
- Single-turn only (no conversational memory between questions)
- SQL-only answers (cannot synthesise qualitative situation reports)
- Point-in-time snapshot (no live data refresh)

**Speaker note:** "We declare these limitations upfront because the score is designed for decision support, not autonomous decision-making. Every response from the agent includes relevant caveats automatically."

---

## SLIDE 7 — CALL TO ACTION (What This Enables)

**Anchor number:** Seconds, not weeks  
**Visual:** Before/after comparison — manual multi-system analysis vs. single natural-language query  
**Narrative beat:** End with forward momentum. What does the audience *do* with this?

**Content:**

**What this changes:**

Before: An analyst spends days joining FTS, INFORM, HNO, and CERF/CBPF data to answer a single question about funding gaps — then starts over for the next question, with no memory of prior work.

After: A natural-language question like "Which crises have been funded below 20% for 3+ years with worsening severity?" returns a ranked, map-ready answer in seconds — grounded in 6 years of synthesised data.

**Intended use cases:**
- **Advocacy:** Surface neglected crises for donor briefings and media engagement
- **Allocation:** Identify where pooled fund allocations (CERF/CBPF) would have highest marginal impact
- **Early warning:** Detect crises where funding gaps are widening as severity increases — before they become catastrophes
- **Accountability:** Track whether identified gaps persist year-over-year

**Immediate next steps:**
- Lakeview Dashboard for visual validation independent of the agent
- Streaming pipeline for monthly data refresh
- Vector search over OCHA situation reports for qualitative synthesis

**Speaker note:** "This is a tool for anticipatory action. The crises it surfaces are the ones where early intervention — a funding allocation, a media spotlight, an advocacy push — can change outcomes before the situation deteriorates further."

---

## OPTIONAL SLIDE 8 — APPENDIX (Technical Reference)

**For Q&A or technical stakeholders who ask "how exactly does the score work?"**

Include:
- Full formula with component ranges
- Calibration parameters (33% threshold from P25-P33 of funding distribution, 5-year lookback, log-scale rationale)
- Data source table (FTS, INFORM, HNO, CERF/CBPF, COD, HRP) with coverage dates
- Genie Space link for live demo

---

## KEY DIFFERENCES FROM V1 BRIEF (For Reference)

1. Reordered: Scoring insight (Slide 2-3) now precedes architecture (Slide 5) — lead with "what" and "why," not "how"
2. Added visual direction per slide — GenAI tools produce better output with layout guidance
3. Added speaker notes — frames narrative intent the tool can use for notes panels
4. Elevated "Stable Catastrophe Paradox" to Slide 2 — strongest story element for this audience
5. Removed "recommended for removal" language about allocation_neglect — reframed as stability proof
6. Translated statistical language (P90/P10 → "top-ranked score 6.5× higher than bottom")
7. Call to action rewritten around specific use cases (advocacy, allocation, early warning, accountability)
8. Added "Before/After" framing for the value proposition
