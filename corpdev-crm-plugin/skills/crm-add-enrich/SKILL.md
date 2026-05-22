---
name: crm-add-enrich
description: >
  Use this skill when James wants to add a new company to the CorpDev CRM, or says things like
  "add [company]", "new company [name]", "log [company] to the CRM", "enrich [company]", or
  "score [company]". Parses natural-language input, validates taxonomy and duplicates, enriches
  from PitchBook + web search + Google Docs, proposes Traction/Product-Tech/Team scores with
  rationale, and writes the complete record to the Notion CRM.
---

# CRM Add & Enrich

The repeatable core of the CorpDev CRM workflow. James provides minimal input to add a company; AI enriches from three sources, proposes quality scores, and writes a complete record to Notion.

## Inputs

James's natural-language input. At minimum: company name + Pillar > Category > Sub-category + Construct. Optionally: priority, contact name, contact role, location, Google Doc link, source (how the company came to attention).

**Example inputs:**
- "add Acme, ARPU > Voice AI > Phone Ordering, M&A, P1"
- "new company FoodBot, ARPU > BOH > Inventory, Investment, contact is Jane Lee (CEO), SF, sourced via conference"
- "add Maple, ARPU > Voice AI > Phone Ordering, M&A, P1, here's the doc: [link]"
- "enrich HiAuto" (re-enrich an existing company)

## Outputs

A complete Notion company record with:
- All fields populated from James's input + enrichment
- AI-proposed scores with rationale
- Initial Touchpoint Log entry
- Confirmation message summarizing what was written

## Workflow

### Phase 1 — Parse & Validate (Step 1)

1. **Parse input.** Extract: company name, Pillar, Category, Sub-category, Construct, and any optional fields (Priority, Contact, Contact Role, Location, Doc link, Source). If Priority is not specified, default to P2. If Status is not specified, default to Active.

2. **Validate taxonomy.** Query the Notion CRM database for current select options. If any provided Pillar, Category, or Sub-category value doesn't match existing options:
   - **Flag for James's review:** "The Sub-category 'Kitchen Automation' doesn't exist in the CRM. Should I create it, or did you mean one of these: [closest matches]?"
   - Do NOT auto-create new taxonomy values.
   - Wait for James's confirmation before proceeding.

3. **Check for duplicates.** Search the Notion CRM by company name (case-insensitive, fuzzy).
   - If exact or near match found: "A company called '[name]' already exists in the CRM with Priority [X] under [Pillar > Category]. Here's the existing record: [link]. Would you like to update it with new enrichment, or is this a different company?"
   - Wait for James's decision. Do not merge or overwrite automatically.

4. **If re-enrich request** (e.g., "enrich HiAuto"): skip to Phase 2 using the existing record.

### Phase 2 — Enrich (Step 2)

Run all three sources in a single pass. Each source is independent — if one fails, continue with the others.

#### 2a. PitchBook Query

Search PitchBook MCP for the company name. Pull:
- Estimated revenue
- Total funding raised
- Last valuation
- Key investors (top 5)
- Board members

**If no PitchBook data found:** Note "No PitchBook data available" and continue. Many early-stage companies won't have PitchBook coverage — this is expected, not an error.

**If multiple PitchBook matches:** Pick the closest match by company name + sector alignment. Note the ambiguity: "PitchBook returned multiple matches for '[name]'. Using [selected match] based on sector alignment. Other matches: [list]."

#### 2b. Web Search

Run 2–3 targeted searches:
- "[company name] funding round revenue"
- "[company name] product overview"
- "[company name] founders team"

Extract:
- HQ location (if not provided by James)
- CEO/founder name + title (if not provided)
- One-line company description
- Recent revenue estimates (cross-reference with PitchBook; note source)
- Notable recent news (funding, partnerships, product launches)
- Product positioning and differentiation signals
- Team/founder background signals from LinkedIn results

**Revenue source priority:** Internal Google Docs > PitchBook > web search. If sources conflict, note all values with attribution and let the most recent/authoritative win.

#### 2c. Google Docs Search

**If James provided a doc link:** Read that specific doc via Google Drive MCP.

**If no link provided:** Ask James: "Do you have an internal doc for [company]? If so, share the link and I'll pull signals from it." Do NOT proactively search Google Drive — wait for James to provide a link or explicitly authorize a search.

**From each doc, extract:**
- Traction signals: revenue figures, growth rates, customer counts, location counts, ARR mentions
- Product/Tech signals: product differentiation, technical moat, competitive positioning
- Team signals: founder impressions, team quality notes, backgrounds
- Deal context: construct discussions, partnership terms, investment terms
- Recent touchpoint context

**If no doc provided and James declines to share one:** Skip gracefully. Note "No internal docs provided for [company]."

### Phase 3 — Score & Write (Step 3)

#### 3a. Propose Scores

Using all signals from Phase 2, propose three scores with rationale:

**Traction Score (1–5):**

| Score | Criteria |
|---|---|
| 5 | >$100M ARR, OR >Rule of 40 (growth % + margin %), OR >100% YoY growth |
| 4 | Strong traction: $30M–$100M ARR, 50–100% growth, clear market pull |
| 3 | Moderate traction: $5M–$30M ARR, growing but not standout |
| 2 | Early traction: <$5M ARR, limited revenue/customer data, some pilots |
| 1 | Pre-revenue, negligible traction, or significant churn that has reset run-rate to near-zero |

**Traction scoring notes:**
- Always anchor to **current run-rate ARR**, not historical deployments or signed pipeline. A company that previously operated at scale but has since churned its anchor customer scores on its current state, not its peak.
- Historical deployments and pipeline are useful context for the rationale but must not inflate the score.
- If ARR is undisclosed, use funding stage + investor quality + customer count to triangulate a range, and note the uncertainty.

**Product/Tech Score (1–5):**

| Score | Criteria |
|---|---|
| 5 | Clear market leader with unique, differentiated product; strong technical moat |
| 4 | Strong product with meaningful differentiation; recognized in market |
| 3 | Solid product but not clearly differentiated; competitive market |
| 2 | Product exists but unclear differentiation or early-stage |
| 1 | Minimal product signal or concerns about product quality |

**Team Score (1–5):**

| Score | Criteria |
|---|---|
| 5 | Exceptional founders/execs with directly relevant experience, strong network, proven track record |
| 4 | Strong team with relevant backgrounds; good impressions from calls |
| 3 | Competent team; some relevant experience but nothing standout |
| 2 | Team concerns: gaps in key roles, limited relevant experience |
| 1 | Significant team concerns flagged in calls or public signals |

**Team scoring notes:**
- Direct call impressions are a **strong and legitimate signal** for Team scores — weight them heavily. A founder who comes across as exceptional in person should move the score to 5 even if their CV alone would suggest 4.
- When a call impression is the deciding factor, note it explicitly in the rationale: e.g., "Cambridge PhD + prior exit; Botty assessed as exceptional in Apr 2026 R&D call (Toast R&D team)."
- Conversely, if a call left a muted impression despite a strong CV, that can hold a score at 3 — on-paper credentials don't override direct assessment.
- If no calls have been held yet, default conservative (3 or below) and note "No direct call — score based on public signals only."

**For each score, show:**
- The proposed score (1–5)
- A one-line rationale citing specific data points
- The source(s) the rationale came from (PitchBook, web, internal doc)
- If insufficient data: "Insufficient data — [what's missing]. Set manually or skip?"

**Present all three scores to James for confirmation.** Wait for his response before writing. He may:
- Confirm all → proceed to write
- Override one or more → apply overrides, write
- Skip a score → leave that field blank

#### 3b. Write to Notion

Write the complete record to the CorpDev CRM Notion database:

| Field | Value |
|---|---|
| Company | From James's input |
| Status | From James's input or default "Active" |
| Pillar | From James's input |
| Category | From James's input |
| Sub-category | From James's input |
| Construct | From James's input (multi-select) |
| Priority | From James's input or default P2 |
| Source | From James's input or blank |
| Contact | From James's input or web enrichment |
| Contact Role | From James's input or web enrichment |
| Location | From James's input or web enrichment |
| Toast Integration | From James's input or blank |
| Docs Linked | From James's input or blank |
| Est. Revenue | From enrichment (with source attribution, e.g., "$30M ARR (PitchBook, Jan 2026)") |
| Total Funding | From PitchBook |
| Last Valuation | From PitchBook |
| Key Investors | From PitchBook |
| Board Members | From PitchBook |
| Traction Score | Confirmed score |
| Product/Tech Score | Confirmed score |
| Team Score | Confirmed score |
| Description | AI-generated one-liner from web enrichment |
| Last Updated | Today's date (America/Los_Angeles) |

#### 3c. Add Initial Touchpoint Log Entry

Create a Touchpoint Log entry:
- **Entry title:** `YYYY-MM-DD — [Company Name]` (today's date, LA timezone)
- **Date:** Today
- **Note:** "Added to CRM. Enriched from [sources used]. [Any notable context from James's input or docs]. AI scores — Traction [X]: [one-line rationale + source]. Product/Tech [X]: [one-line rationale + source]. Team [X]: [one-line rationale + source]."

#### 3d. Confirmation

Present a concise confirmation (optimized for mobile reading):

```
✓ Added [Company] to CorpDev CRM
  [Pillar] > [Category] > [Sub-category] | [Construct] | [Priority] | [Status]
  Revenue: [X] | Funding: [X] | Valuation: [X]
  Scores: Traction [X], Product/Tech [X], Team [X]
  Enriched from: [sources]
  [Link to Notion record]
```

## Re-Enrich Mode

When James says "enrich [company]" or "re-score [company]" for an existing record:

1. Look up the existing record in Notion.
2. Run Phase 2 (Enrich) fresh — all three sources.
3. Compare new data with existing record. Present a diff: "Here's what changed since the last enrichment: [changes]."
4. Propose updated scores with rationale.
5. James confirms → overwrite the record with updated data. Update Last Updated date.
6. Add a Touchpoint Log entry: `YYYY-MM-DD — [Company Name]` with Note: "Re-enriched. [summary of changes]."

## Failure Modes

- **PitchBook MCP auth fails** → continue with web + docs only. Note the gap.
- **PitchBook returns no data** → expected for early-stage companies. Note and continue.
- **No internal doc provided** → ask James once. If he declines, skip gracefully — do not search Drive proactively.
- **Notion write fails** → surface clear error. Offer to retry or output the record as formatted text so James can paste manually.
- **James overrides a score** → write the override. Preserve AI rationale in the Touchpoint Log Note field on the initial entry for future reference.
- **Ambiguous company name in PitchBook** → present options, let James pick.
- **New taxonomy value** → block and flag. Never auto-create.
- **Duplicate company** → block and flag. Never auto-merge.
- **All three enrichment sources return nothing** → create the stub record with James's input only. Note "No enrichment data found from any source."

## Notes

- James's timezone is America/Los_Angeles. Use his local date for Last Updated and Touchpoint Log entries.
- Revenue estimates should always include source and date: "$30M ARR (PitchBook, Jan 2026)" or "$2M ARR (internal doc, Dec 2025 call)". Never present an unsourced revenue figure.
- The scoring rubric will evolve. After the first 10–20 companies, James will likely tighten Product/Tech and Team criteria. The rubric in this skill is the starting point.
- When enriching, prefer specificity over comprehensiveness. Three strong, sourced data points beat ten vague ones.
- Keep the confirmation message concise. James will read this on mobile after meetings.
- "Contact Role" in Notion corresponds to what was previously called "Title" in the original schema. The rename was intentional to avoid Notion's reserved property name.
