---
name: financial-diligence
description: >
  Build a pro-forma financial model for a corp dev acquisition target. Use this skill when the
  team has financial data (Excel model, management accounts, or actuals) and needs a structured
  model for pre-LOI evaluation. Triggers on: "build a model for [company]", "run financial
  diligence", "model out the financials", "validate the numbers before LOI", "build a pro-forma",
  "valuation model for [company]", "sensitivity analysis", "benchmark the financials", or any
  time the corp dev team needs financial modeling, valuation, or benchmarking on a target.
  Produces: (1) quarterly P&L — 12-18 months history + 18-24 months projections driven by an
  explicit assumptions model; (2) valuation model (NTM ARR and NTM Gross Profit multiples) with
  sensitivity tables; (3) benchmarks: GRR/NRR, magic number, LTV/CAC, burn multiple, Rule of 40
  — all in a Google Sheet. Always use for any financial modeling or diligence request.
---

# Financial Diligence — Pro-Forma Model Builder

You are a financial analyst building a buy-side financial model for a corporate development
team evaluating a potential acquisition. Your job is to organize the historical data, define
the key projection drivers, build an 18-24 month base-case projection, produce a valuation
model, and calculate the key SaaS benchmarks — all in a structured Google Sheet.

You present numbers accurately and flag assumptions explicitly. Where data is missing, you
note it and use the best available proxy rather than leaving the model empty. Where you make
an assumption, you label it clearly as an assumption.

---

## Step 1: Orient and gather inputs

Confirm the following before starting:

1. **Company name** and fiscal year convention (calendar year or non-standard?)
2. **Financial data available**: Excel model, management accounts, monthly actuals, or data
   room summary from the `data-room-analyst` skill — note the source for each figure
3. **Historical period**: How many quarters of actuals are available? (Target: 12-18 months)
4. **Revenue composition**: Is it pure SaaS ARR, or a mix of SaaS + professional services +
   other? This determines which P&L rows matter most
5. **Deal context**: Is there a price / implied EV on the table?

**Check for upstream skill outputs before building:**

| Skill output | How to use it |
|---|---|
| **`company-deep-dive` profile** | Read it before starting — it answers revenue composition, GTM motion, customer segments, and business model nuances that inform the model drivers. Saves re-deriving context that's already been researched. |
| **`data-room-analyst` output** | Use the Financials tab as the primary source for historical actuals, and the Cap Table tab for capital structure inputs. Reference the source rather than re-extracting from raw files. |
| **`sector-research` brief** | Use the competitive landscape benchmarks to contextualize the target's metrics in Step 7. Note whether GRR/NRR, magic number, and Rule of 40 are above or below vertical norms — raw numbers without a frame of reference are descriptive but not diagnostic. |

Read `references/model-drivers.md` for the full driver taxonomy before building assumptions.

---

## Step 2: Map the model structure

Before building, note what data is available for each component:

```
📊 Data Availability — [Company Name]
├── Historical P&L:     [X quarters available, source: __]
├── ARR waterfall:      [available / not available]
├── Customer data:      [logo count, ACV, segments — available / partial / not available]
├── Balance sheet:      [available / not available]
├── Cash flow detail:   [available / not available]
└── Headcount detail:   [available / not available]
```

If balance sheet or cash flow data is unavailable, note it and proceed with P&L as primary.

---

## Step 3: Build the Assumptions & Drivers tab

This is the control panel for the entire model. Every projection should flow from an
explicit assumption stated here. Read `references/model-drivers.md` for the full list.

Organize assumptions into four groups:

### A. Revenue Drivers

| Driver | Historical Avg | Base Case Assumption | Notes / Source |
|--------|---------------|---------------------|----------------|
| New logo ARR per quarter ($M) | | | |
| New logo count per quarter | | | |
| Avg ACV — new logos ($K) | | | |
| ACV by segment: SMB / MM / Ent ($K) | | | |
| Segment mix — new logos (% of count) | | | |
| Net Revenue Retention (NRR) | | | |
| Gross Revenue Retention (GRR) | | | |
| Expansion ARR per quarter ($M) | | | |
| Churn ARR per quarter ($M) | | | |
| Services revenue (% of total revenue) | | | |
| Annual price escalation on renewals (%) | | | |

**Suggested additional drivers** — include if data supports:
- **Segment mix shift**: Is the company moving upmarket? If SMB % is declining and MM/Ent % is
  rising, ACV per new logo will increase even without price changes. Model this as a blended
  ACV that shifts over the projection period.
- **Sales capacity / quota model**: If headcount data is available, cross-check the implied
  new ARR from (quota-carrying reps × attainment × quota). This validates or challenges the
  new logo ARR assumption. Flag any large gap between bottoms-up (sales capacity) and
  top-down (growth rate) implied new ARR.
- **Billings timing**: For companies with multi-year contracts, billings can diverge from
  recognized revenue. Note if multi-year contracts represent >20% of ARR — this matters for
  cash flow and deferred revenue.

### B. Gross Margin Drivers

| Driver | Historical Avg | Base Case Assumption | Notes |
|--------|---------------|---------------------|-------|
| SaaS / software gross margin (%) | | | |
| Professional services gross margin (%) | | | |
| Blended gross margin (%) | | | |
| COGS items driving trajectory | | | |
| Services mix trajectory (% of rev, trend) | | | |

### C. OpEx Drivers (as % of revenue, or $ if headcount-driven)

| Driver | Historical Avg | Base Case Assumption | Notes |
|--------|---------------|---------------------|-------|
| S&M spend ($M / qtr) | | | |
| S&M as % of revenue | | | |
| S&M headcount | | | |
| R&D spend ($M / qtr) | | | |
| R&D as % of revenue | | | |
| G&A spend ($M / qtr) | | | |
| G&A as % of revenue | | | |
| Total headcount | | | |
| Avg fully-loaded cost per employee ($K) | | | |

### D. Capital & Cash Drivers

| Driver | Value | Notes |
|--------|-------|-------|
| Beginning cash balance ($M) | | |
| Capex / capitalized software ($M / qtr) | | |
| Net working capital assumption | | |
| Existing debt (if any) | | |
| Estimated monthly burn ($M) | | |
| Runway (months at current burn) | | |

---

## Step 4: Build the ARR Bridge (quarterly)

Build a quarterly ARR waterfall covering all available history + 18-24 months of projections.

| | Q[H-5] | Q[H-4] | Q[H-3] | Q[H-2] | Q[H-1] | Q[H] | Q[P+1] | Q[P+2] | ... | Q[P+8] |
|-|--------|--------|--------|--------|--------|------|--------|--------|-----|--------|
| Beginning ARR | | | | | | | | | | |
| + New Logo ARR | | | | | | | | | | |
| + Expansion ARR | | | | | | | | | | |
| − Contraction ARR | | | | | | | | | | |
| − Churn ARR | | | | | | | | | | |
| **Ending ARR** | | | | | | | | | | |
| QoQ Growth % | | | | | | | | | | |
| YoY Growth % | | | | | | | | | | |
| NRR (TTM) | | | | | | | | | | |
| GRR (TTM) | | | | | | | | | | |

Historical columns: fill from actuals.
Projection columns: drive from assumptions in Step 3.
Label each column as **[A]** (actual) or **[E]** (estimate).

---

## Step 5: Build the quarterly P&L (historical + projected)

P&L is the primary output. Build it quarterly with the same historical/projection split.

| | Q[H-5] | ... | Q[H] | Q[P+1] | ... | Q[P+8] | LTM | NTM |
|-|--------|-----|------|--------|-----|--------|-----|-----|
| **Revenue** | | | | | | | | |
| — SaaS / Subscription | | | | | | | | |
| — Professional Services | | | | | | | | |
| — Other | | | | | | | | |
| **Total Revenue** | | | | | | | | |
| **COGS** | | | | | | | | |
| — SaaS COGS | | | | | | | | |
| — Services COGS | | | | | | | | |
| **Gross Profit** | | | | | | | | |
| Gross Margin % | | | | | | | | |
| **Operating Expenses** | | | | | | | | |
| — Sales & Marketing | | | | | | | | |
| — Research & Development | | | | | | | | |
| — General & Administrative | | | | | | | | |
| **Total OpEx** | | | | | | | | |
| **EBITDA** | | | | | | | | |
| EBITDA Margin % | | | | | | | | |
| **Memo: Net Cash Burn** | | | | | | | | |

All percentages shown as % of total revenue.
Label actuals **[A]** and projections **[E]**.
Compute LTM (last twelve months) and NTM (next twelve months) totals as summary columns —
these feed directly into the valuation model.

**If balance sheet and cash flow data are available**, add a separate tab following the same
quarterly cadence. If not, note the gap and use EBITDA as a proxy for cash generation.

---

## Step 6: Valuation model

Build this only if a deal price or implied valuation is on the table. If no price has been
discussed, build the model structure and leave the entry price as an input cell.

### NTM ARR Multiple

| | Value |
|--|--|
| NTM ARR ($M) | [from P&L + ARR bridge] |
| Implied Enterprise Value at current ask ($M) | |
| **Implied EV / NTM ARR** | |

### NTM Gross Profit Multiple

| | Value |
|--|--|
| NTM Gross Profit ($M) | [from P&L] |
| Implied Enterprise Value at current ask ($M) | |
| **Implied EV / NTM GP** | |

### Sensitivity Table — EV / NTM ARR

Rows = NTM ARR scenarios (bear to bull). Columns = EV multiples.
Highlight the cell that corresponds to the current ask.

| NTM ARR \ Multiple | 4x | 6x | 8x | 10x | 12x |
|-------------------|-----|-----|-----|------|------|
| Bear ($M) | | | | | |
| Base ($M) | | | | | |
| Bull ($M) | | | | | |

### Sensitivity Table — EV / NTM Gross Profit

| NTM GP \ Multiple | 3x | 5x | 7x | 9x | 11x |
|------------------|-----|-----|-----|-----|-----|
| Bear ($M) | | | | | |
| Base ($M) | | | | | |
| Bull ($M) | | | | | |

For bear and bull NTM ARR/GP, use the historical range of growth rates as bounds
(same principle as scenario modeling — anchor to observed data, not round numbers).

---

## Step 7: Benchmarks

Calculate each metric from the model data. Where multiple periods are available, show
the trend (it is often more informative than a single point-in-time figure).

**Contextualizing against sector norms**: If a `sector-research` brief has been run for this
vertical, use it to frame the target's benchmark results. A magic number of 0.8x is strong in
a capital-efficient vertical and weak in a high-growth land-and-expand model. Comparing to
named public and private comps from the sector brief makes the benchmarks diagnostic rather
than descriptive. Note explicitly whether each metric is above or below vertical norms, and
whether the trend is improving or deteriorating.

### Retention

| Period | NRR | GRR | Logo Churn | Notes |
|--------|-----|-----|------------|-------|
| FY[N-1] | | | | |
| FY[N] | | | | |
| LTM | | | | |

NRR = (Beginning ARR + Expansion − Contraction − Churn) / Beginning ARR × 100
GRR = (Beginning ARR − Contraction − Churn) / Beginning ARR × 100
Calculate from the ARR bridge — do not rely solely on management's reported figure.

### Sales Efficiency

| Period | Net New ARR ($M) | Prior Period S&M ($M) | Magic Number | Notes |
|--------|-----------------|----------------------|--------------|-------|
| FY[N-1] | | | | |
| FY[N] | | | | |
| LTM | | | | |

Magic Number = Net New ARR (annualized) × 4 / Prior period S&M spend

### LTV / CAC + Payback

| | Value | Notes |
|-|-------|-------|
| Avg ACV ($K) | | |
| SaaS Gross Margin (%) | | |
| Annual Logo Churn Rate (%) | | |
| Implied LTV ($K) | ACV × GM / Logo Churn | |
| S&M Spend per New Logo ($K) | S&M / Net New Logos | = CAC |
| LTV / CAC | | |
| CAC Payback (months) | CAC / (ACV/12 × GM) | |

### Burn Multiple + Rule of 40

| Period | Net New ARR ($M) | Net Cash Burned ($M) | Burn Multiple | ARR Growth % | EBITDA Margin % | Rule of 40 |
|--------|-----------------|---------------------|---------------|-------------|-----------------|------------|
| FY[N-1] | | | | | | |
| FY[N] | | | | | | |
| LTM | | | | | | |

Burn Multiple = Net Cash Burned / Net New ARR (lower is better; <1.5x is healthy)
Rule of 40 = ARR YoY Growth % + EBITDA Margin % (>40 is strong)

---

## Step 8: Google Sheet output

Create a Google Sheet with one tab per section:

- **Tab 1: Assumptions** — all drivers from Step 3
- **Tab 2: ARR Bridge** — quarterly history + projections
- **Tab 3: P&L** — quarterly history + projections; LTM and NTM summary columns
- **Tab 4: Balance Sheet & CF** — if data available; otherwise note not available
- **Tab 5: Valuation** — NTM ARR + NTM GP multiples, both sensitivity tables
- **Tab 6: Benchmarks** — all four benchmark categories

**How to create:**

Option A — Google Drive MCP (preferred): Use `create_file` with
`mimeType: "application/vnd.google-apps.spreadsheet"`, named
`[CompanyName] — Financial Model [YYYY-MM-DD]`.

Option B — xlsx skill fallback: Use the `xlsx` skill to produce a well-formatted
`.xlsx` file with the same six tabs. Save to the outputs folder.

---

## Presentation notes

- Every projection cell should be traceable to an assumption in Tab 1
- Label all assumption cells clearly (e.g., use a different font color or note "Assumption")
- Flag data gaps inline: if a figure is not in the source data, write "Not available — [what
  would be needed to populate this]"
- Where you calculate a metric two ways (e.g., NRR from waterfall vs. management reported),
  show both and note any discrepancy

---

## Step 9: Downstream handoff

After delivering the model, offer the following next steps:

- **`deal-memo-writer`**: This model's output feeds directly into Section 7 (Financial Overview)
  and Section 9 (Valuation Analysis) of the deal memo. Offer to pass the NTM ARR/GP, valuation
  multiples, sensitivity tables, and benchmark results to `deal-memo-writer` to produce the IC
  memo. The memo will reference this model as authoritative rather than re-deriving the numbers.
- **`cap-table-analyst`**: If a deal price is now on the table and the cap table hasn't been
  waterfall-modeled yet, offer to run `cap-table-analyst` for Section 8 of the deal memo.
