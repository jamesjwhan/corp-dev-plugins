---
name: cap-table-analyst
description: >
  Builds a liquidation waterfall analysis from a startup cap table — computes tiered payouts
  (convertibles → preferred → common), produces a formatted Excel model with individual
  stakeholder breakouts, and uploads to Google Drive. Use this skill whenever the user shares
  a cap table file and asks about exit proceeds, liquidation scenarios, who gets paid what,
  waterfall analysis, payout modeling, M&A proceeds distribution, or anything involving how
  exit value flows through a company's capital structure. Trigger even when the user says
  things like "how much would X get if we sold for $Y", "model out a $Z exit", "run a
  waterfall on this cap table", or provides a Drive link to a cap table spreadsheet. This
  skill covers the full workflow: reading the cap table → extracting structured data →
  clarifying key assumptions → building the Excel waterfall model → verifying zero formula
  errors → uploading the result to Drive.
---

# Cap Table Analyst

Builds a liquidation waterfall Excel model from a startup cap table, showing how exit proceeds
flow through each tier and what each stakeholder receives.

---

## Step 0 — Gather Inputs

Before building anything, confirm you have:

1. **The cap table data** — via Google Drive link, uploaded file, local path, or structured
   extract from a prior `data-room-analyst` run (see note below)
2. **Exit proceeds** — total dollar amount(s) being modeled. If this waterfall will feed a
   deal memo, prompt for three scenarios: low / mid / high EV (e.g., "$20M / $35M / $50M").
   A single-scenario model is fine for a standalone analysis; for deal memo Section 8, three
   scenarios are needed.
3. **Liquidation preference multiple** — almost always 1x; ask if not stated
4. **Named individuals to break out** — specific people the user wants individual payout rows for
5. **Drive destination** — the folder where the output should be saved (often the same folder as the source)

If any of these are missing, ask before proceeding. A waterfall without a stated exit amount or preference multiple is meaningless.

**Using data-room-analyst output as the starting point:**
If `data-room-analyst` has been run for this company, its Section 3 (Cap Table Analysis) already
contains the ownership summary, round detail table, and a basic liquidation waterfall at several
exit values. Use that extract as your starting point rather than re-parsing the raw cap table file:
- Pull the ownership summary → use as the basis for share counts and class structure
- Pull the round detail → use for capital invested, liquidation preferences, and participation flags
- Note any fields marked `[to confirm]` in the data-room output — those are gaps to resolve before building

If the data-room extract has missing or uncertain fields (common for participation structure,
per-class multiples, or seniority), go back to the raw cap table or term sheets to fill them.

---

## Step 1 — Parse the Cap Table

Read the cap table file with `extract-text` or pandas. Look for these standard sheets:
- **Summary Cap Table** — grouped by share class (use this as the primary source for groupings and totals)
- **Detailed Cap Table** — individual holder-level data (use for named individual breakouts)

### Extracting Key Data

From the cap table, collect:

| Data Point | Where to Find It |
|---|---|
| Convertible instruments (Notes, SAFEs) | Rows labeled as "CN Notes", "SAFE", "Convertible Note" — capture **face value / principal** |
| Preferred share classes | Rows with "Series", "Preferred", "PS" — capture **shares outstanding** and **cash raised / capital invested** |
| Common shares | Rows labeled "Common", "CS", "Founders" — capture **shares outstanding** |
| Options / RSUs | Rows labeled "Options", "EIP", "RSU", "Stock Options" — capture **shares / units** |
| Warrants | Rows labeled "Warrant", "CSW" — capture **shares** and **exercise price** if available |

For named individual breakouts, find each person's row(s) across all share classes in the detailed tab.

---

## Step 2 — Clarify Key Assumptions (Important)

Before computing anything, clarify these assumptions. State all of them in the model header. When information is missing from the cap table, flag and ask before building.

### A. Preferred Liquidation Basis

Use **capital invested** (cash raised) as the basis for the 1x liquidation preference. This is the verifiable, conservative number — found in the cap table as "Cash Raised", "Capital Invested", or "Amount Invested".

Add **cumulative dividends** on top when applicable: `liq_pref_claim = capital_invested × pref_multiple + cumulative_dividends`

If the cap table shows an OIP-derived value that diverges from cash raised, flag both numbers and ask which to use.

### B. Seniority and Pari Passu Structure

**Default: all preferred classes are pari passu (seniority = 1 for all classes).** This means if there aren't enough proceeds to cover all preferred, each class is paid pro-rata by claim — not sequentially.

Override this only when the term sheets explicitly establish a seniority ordering (e.g. "Series B is senior to Series A"):

| Structure | How to model |
|---|---|
| All pari passu (default) | Leave `seniority` at default (1 for all) |
| Series B senior to Series A | `'seniority': 1` for B, `'seniority': 2` for A |
| B senior; A and seed pari passu | `'seniority': 1` for B; `'seniority': 2` for both A and seed |

Within a seniority group, all classes with the same seniority number share proceeds pro-rata by claim amount.

### C. Liquidation Preference Multiple (Per Class)

Each preferred class can have its own multiple. Common configurations:

| Scenario | How to specify |
|---|---|
| All classes 1x | Pass `pref_multiple=1.0` globally; use old tuple format |
| Series B 2x, Series A 1x | Use dict format with per-class `pref_multiple` |
| Any non-standard multiple | Always use dict format with explicit `pref_multiple` |

**Default**: 1x. If the user doesn't mention multiples, assume 1x and note the assumption.

### D. Conversion Check (Always On for Non-Participating Preferred)

**Non-participating preferred always takes MAX(liquidation preference, as-converted value).** This is not optional — it is the correct financial behavior in every deal. Do not model preferred investors as taking only their 1x pref when conversion gives them more.

The conversion check runs automatically in `compute_waterfall()`:
1. Compute each class's liq pref payout (Tier 2)
2. Compute common PPS assuming all non-participating preferred takes their pref
3. For any class where `pref_shares × PPS > liq_pref_payout`: the class converts — it joins the common pool and gives back its T2 payout, raising the PPS for everyone
4. Iterate until stable (converting a class changes the PPS, which may trigger more conversions)

Classes that convert appear greyed out in Tier 2 with a note, and as separate rows in Tier 3.

### E. Participation Structure (Per Class)

| Type | What it means | How to model |
|---|---|---|
| **Non-participating** (default for most deals) | Takes liq pref, subject to conversion check | `'participation': 'non-participating'` |
| **Participating** | Takes liq pref AND participates pro-rata in common residual | `'participation': 'participating'` |
| **Capped participating** | Participates up to a total return multiple, then common gets the rest | `'participation': 'capped', 'participation_cap': 3.0` |

Participating preferred is **not** subject to the conversion check — they already get pref + participation, which is always at least as good as just converting.

### F. Convertible Notes and SAFEs

Each convertible instrument supports:
- `principal`: face value (always required)
- `accrued_interest`: pre-computed dollar amount of accrued interest to add to the T1 claim
- `coc_multiple`: contractual M&A return multiplier applied to principal (e.g. `2.0` for a 2x return provision). Claim = `principal × coc_multiple + accrued_interest`
- `implied_shares`: if provided, the instrument gets MAX(claim, implied_shares × common_pps). Use when a SAFE has a valuation cap that implies a share count at exit.

**T1 is always pari passu** — if total claims exceed remaining proceeds, each instrument is paid pro-rata by claim.

### G. Warrants and Options

Warrants: exclude if exercise prices are unavailable. Add footnote.

Options/RSUs: show as **"Theoretical Gross Payout"** = options × common PPS. Net payout = gross minus exercise cost. Do not include options in the common pool denominator.

---

## Step 3 — Compute the Waterfall

**Always use `compute_waterfall()` from `scripts/waterfall_builder.py`.** Do not compute the waterfall manually. The function handles all mechanics correctly: pari passu, seniority, conversion check, participation, capped participation.

```python
from scripts.waterfall_builder import compute_waterfall, _normalize_conv, _normalize_pref

cn = [_normalize_conv(c) for c in conv_instruments]
pn = [_normalize_pref(c, default_multiple) for c in pref_classes]
wf = compute_waterfall(total_exit, cn, pn, common_shares)

# Key outputs:
# wf['tier1_payout']       — total Tier 1 payout
# wf['tier2_pref_total']   — total Tier 2 preference payout (converters excluded)
# wf['remaining2']         — pool available for Tier 3
# wf['pps']                — common per-share value (after all mechanics)
# wf['common_total']       — common stock + converted preferred payout
# wf['converting_names']   — set of preferred class names that elected to convert
# wf['tier2_prefs']        — list of per-class dicts with pref_payout, participation_payout, total_payout, converted
# wf['tier1_instruments']  — list of per-instrument dicts with claim, payout
```

**Summary of mechanics:**

```
Tier 1 — Convertibles / SAFEs (always pari passu):
  claim_i = principal_i × coc_multiple_i + accrued_interest_i
  All instruments paid pro-rata if total claims > remaining

Tier 2 — Preferred (seniority groups; pari passu within group):
  liq_pref_claim_i = capital_invested_i × pref_multiple_i + cumulative_dividends_i
  Groups processed most-senior-first (lowest seniority number)
  Within group: pro-rata by claim if insufficient funds

Conversion check — non-participating preferred (iterative):
  Each class takes MAX(liq_pref_payout, pref_shares × common_PPS)
  Converters join common pool and give back their T2 payout → raises PPS → may trigger more
  Iterate until stable

Tier 3 — Common pool:
  Denominator = common_shares + converted_pref_shares + participating_pref_shares
  PPS = remaining2 / denominator
  Participating preferred also gets: pref_shares × PPS (capped if applicable)
```

**Verify totals before building the Excel model:**
```python
t1 = wf['tier1_payout']
t2 = wf['tier2_pref_total']
t3_common = wf['common_total']
t3_part = sum(p['participation_payout'] for p in wf['tier2_prefs'])
assert abs(t1 + t2 + t3_common + t3_part - total_exit) < 1, "Totals don't balance!"
```

**If running multiple scenarios** (e.g., low / mid / high for a deal memo), run
`compute_waterfall()` once per exit amount and collect the results. Build a Scenarios tab in
the Excel model (see Step 4) that shows each stakeholder's payout across all three scenarios
side by side. The Excel assumption cell approach (blue input for exit proceeds) means a user
can also manually scenario-test by changing that one cell, but the Scenarios tab gives the
IC the at-a-glance comparison they need without manual toggling.

---

## Step 4 — Build the Excel Model

Use **openpyxl** for all formatting. Use the template in `scripts/waterfall_builder.py` as your starting point — it provides the color palette, the `h()` cell-styling helper, and the standard sheet layout.

### Instrument and Class Formats

**`conv_instruments` — old format (backward compatible):**
```python
conv_instruments = [
    ('2022 SAFE', 500_000),   # (name, principal)
]
```

**`conv_instruments` — dict format (full flexibility):**
```python
conv_instruments = [
    {'name': '2022 SAFE', 'principal': 500_000},                           # minimal
    {'name': '2x Bridge Note', 'principal': 1_000_000, 'coc_multiple': 2.0},  # CoC multiplier
    {'name': 'Note w/ Interest', 'principal': 750_000, 'accrued_interest': 45_000},  # accrued int
    {'name': 'SAFE w/ Cap', 'principal': 500_000, 'implied_shares': 150_000},  # conversion check
]
```

**`pref_classes` — old format (backward compatible):**
```python
pref_classes = [
    ('Series A', 1_000_000, 2_000_000),      # (name, shares, capital_invested)
    ('Series B', 500_000, 5_000_000, 1.5),   # with explicit multiple
]
```

**`pref_classes` — dict format (full flexibility):**
```python
pref_classes = [
    {
        'name': 'Series B',
        'shares': 500_000,
        'capital_invested': 10_000_000,
        'pref_multiple': 2.0,               # 2x pref
        'cumulative_dividends': 400_000,    # accrued divs add to liq pref claim
        'seniority': 1,                     # most senior
        'participation': 'capped',
        'participation_cap': 3.0,           # max 3x total return
    },
    {
        'name': 'Series A',
        'shares': 1_000_000,
        'capital_invested': 2_000_000,
        'pref_multiple': 1.0,
        'seniority': 2,                     # junior to Series B; pari passu with seed
        'participation': 'participating',   # fully participating, no cap
    },
    {
        'name': 'Seed Preferred',
        'shares': 800_000,
        'capital_invested': 1_000_000,
        'pref_multiple': 1.0,
        'seniority': 2,                     # pari passu with Series A (same seniority number)
        'participation': 'non-participating',
    },
]
```

### Sheet 1: Waterfall Analysis

Structure:
1. **Title bar** — company name + "Liquidation Waterfall Analysis" (dark navy background)
2. **Subtitle** — exit amount | preference description | cap table date (light gray)
3. **Assumptions block** — exit proceeds (blue input), default preference multiple (blue input), preferred basis description, participation structure (when applicable)
4. **Tier 1 table** — one row per convertible instrument, total row, remaining row
5. **Tier 2 table** — preferred class with shares / capital invested / per-class multiple / pref payout / participation type
6. **Tier 3 table** — common shares / per-share value / total; for participating preferred, adds a denominator helper row and per-class participation rows
7. **Waterfall Summary** — all tiers in one compact table; includes a "Participation Payout" column when applicable
8. **Footnotes** — warrant exclusion, option gross treatment, and capped participation note (³) when applicable

### Sheet 2: Individual Payouts

Structure:
1. **Title bar + subtitle** (same as Sheet 1 but says "Payout Breakdown by Stakeholder")
2. **Key Assumptions block** — common PPS and preferred PPS as cross-sheet formula references to Sheet 1
3. **Named Individual Summary** — one row per named person with: common payout | preferred payout | option payout (gross) | TOTAL | % of exit
4. **Full breakdown by tier** — all holders listed under their respective tier section (Tier 1 convertibles → Tier 2 preferred → Tier 3 common → Warrants → Options)

Highlight named individuals in the full breakdown using the orange/salmon highlight color so they stand out.

### Sheet 3: Scenarios (include when 2+ exit amounts are modeled)

Build this sheet whenever multiple exit scenarios have been requested (e.g., for a deal memo).

Structure:
1. **Title bar** — "[Company] — Waterfall Scenarios"
2. **Exit scenario inputs** — one column per scenario (Low / Mid / High), exit amounts as blue input cells
3. **Stakeholder payout summary** — rows for each key stakeholder group (founders, each investor class, option pool); columns for each scenario; dollar payout and % of exit
4. **Key metrics per scenario** — common PPS, founder net proceeds, total investor proceeds, option pool gross value

The Scenarios sheet references the same `compute_waterfall()` logic — run once per scenario and populate. This gives the IC the at-a-glance view they need for deal memo Section 8 without manual toggling of the assumption cell.

### Color Coding (Industry Standard)

```python
# Blue text  = hardcoded inputs the user will change for scenarios
# Black text  = formulas and calculations
# Green text  = cross-sheet references (links to other worksheets)
# Gray text   = notes, excluded items, non-participating instruments
```

```python
# Key palette (use these exact hex values):
COLORS = {
    'nav':         '1F4E79',   # dark navy — title bars
    'blue_hdr':    '2E74B5',   # medium blue — Tier 2 headers
    'green_hdr':   '375623',   # dark green — Tier 1 headers
    'gold_hdr':    'BF8F00',   # gold — Tier 3 headers
    'gray_hdr':    '595959',   # gray — warrants/options headers
    'green_bg':    'E2EFDA',   # light green — Tier 1 data rows
    'green_sub':   'C6EFCE',   # medium green — Tier 1 subtotals
    'blue_bg':     'DDEEFF',   # light blue — Tier 2 data rows
    'blue_sub':    'BDD7EE',   # medium blue — Tier 2 subtotals
    'gold_bg':     'FFF2CC',   # light gold — Tier 3 data rows
    'gold_sub':    'FFE699',   # medium gold — Tier 3 subtotals
    'gray_bg':     'F2F2F2',   # light gray — warrants/options rows
    'highlight':   'FCE4D6',   # salmon — named individual rows
    'input_bg':    'EBF3FB',   # pale blue — assumption input cells
    'total_bg':    'D6DCE4',   # gray-blue — grand total rows
    'subtitle':    'D9E1F2',   # pale lavender — subtitle bar
    'blue_input':  '0000FF',   # blue text — hardcoded inputs
    'black':       '000000',   # black text — formulas
    'gray_text':   '7F7F7F',   # gray text — notes/excluded items
    'white':       'FFFFFF',   # white text — on dark backgrounds
}
```

### Number Formatting

```python
CURRENCY  = '$#,##0;($#,##0);"-"'       # dollar amounts, zeros as dash
CURRENCY2 = '$#,##0.00;($#,##0.00);"-"' # per-share values (2 decimals)
NUMFMT    = '#,##0'                      # share counts
PCT       = '0.0%'                       # percentages
MULT      = '0.0"x"'                     # preference multiples
```

### Formula Rules

- **Assumption cells** are hardcoded values with blue text on pale blue background
- **All calculations** use Excel formulas referencing assumption cells — never hardcode computed values
- **Cross-sheet references** use `='Sheet Name'!CellRef` syntax and get green text
- Never hardcode the exit amount or preference multiple into tier calculation formulas — always reference the assumption cells

Example: If exit proceeds are in B5 and preference multiple in B6, the Tier 1 payout formula for a row where face value is in C12 should be `=C12*$B$6`, not `=C12*1.0`.

---

## Step 5 — Recalculate and Verify

After saving the .xlsx file, always run:

```bash
python scripts/recalc.py <output_file.xlsx>
```

Check the JSON output:
- `"status": "success"` — you're done
- `"status": "errors_found"` — fix every error listed in `error_summary` before delivering

Common errors and fixes:
- `#VALUE!` in a cell — often caused by a text value being treated as a formula. Check for cells starting with `=` in text strings (e.g. `'= $1,849,998...'`). Prefix with `'1x = ...'` to prevent Excel parsing it.
- `#REF!` — a cell reference points to a deleted or nonexistent cell. Check row/column offsets.
- `#DIV/0!` — a denominator is zero. Add an `IFERROR` wrapper or check that share counts are non-zero.

---

## Step 6 — Upload to Google Drive

If the user wants the file in Drive, use the Google Drive MCP tool:

```python
import base64

with open(output_path, 'rb') as f:
    b64 = base64.b64encode(f.read()).decode('utf-8')

# Write to a temp file to ensure clean single-line base64
with open('/tmp/b64_content.txt', 'w') as f:
    f.write(b64)
```

Then read the content from the temp file and call `create_file` with:
- `title`: the filename (e.g. `CompanyName_Waterfall_$20M.xlsx`)
- `parentId`: the Google Drive folder ID (extract from the Drive URL: `...folders/<folderID>` or from the spreadsheet's parent)
- `contentMimeType`: `application/vnd.openxmlformats-officedocument.spreadsheetml.sheet`
- `disableConversionToGoogleType`: `true` (critical — prevents Drive from converting to Google Sheets)
- `base64Content`: the base64 string read from the temp file

**Common upload failure**: If the base64 string contains spaces or newlines (from `base64` CLI command output), the upload will fail silently with a corrupted file. Always use Python's `base64.b64encode(...).decode('utf-8')` to generate a clean single-line string.

---

## Naming Convention for Output File

```
<CompanyName>_Waterfall_<ExitAmount>.xlsx
```

For multi-scenario models: `<CompanyName>_Waterfall_Scenarios.xlsx`

Examples: `NomadGo_Waterfall_20M.xlsx`, `Acme_Waterfall_Scenarios.xlsx`

---

## When to Raise a Question Before Building

The skill handles most waterfall structures automatically. Surface these to the user before proceeding:

- **Participation structure not stated** — if the cap table or term sheet doesn't specify, ask. Participating vs. non-participating is a significant difference at any exit size.
- **Multiple classes with different multiples** — confirm which multiple applies to which class before building.
- **Anti-dilution provisions** — weighted-average or full-ratchet adjustments that reprice the OIP require pre-computed adjusted share counts. Flag and ask the user to provide corrected numbers.
- **Pay-to-play mechanics** — preferred shares that convert to common in a down round need manual classification before running the waterfall.
- **PIK accrued interest** — if convertible notes have been accruing interest that adds to principal at conversion, confirm the accrued amount before modeling.

---

## Checklist Before Delivering

- [ ] All tier payouts sum to total exit proceeds (for each scenario if multi-scenario)
- [ ] Participation structure confirmed and modeled correctly (non-participating / participating / capped)
- [ ] Per-class preference multiples written as blue input cells in Tier 2 (not hardcoded into formulas)
- [ ] `compute_waterfall()` result verified against Excel model totals before saving
- [ ] Zero formula errors after recalc.py
- [ ] Named individuals are highlighted (salmon) in Sheet 2
- [ ] Scenarios tab included if 2+ exit amounts were requested
- [ ] Warrants and options have footnotes explaining their treatment
- [ ] Capped participation amounts have footnote ³ explaining the iterative solve
- [ ] File uploaded to correct Drive folder (if requested)
- [ ] File opens and displays cleanly (columns not truncated)

---

## Downstream handoff

After delivering the model, offer to pass the waterfall results to `deal-memo-writer` for
Section 8 (Cap Table & Waterfall) of the IC memo. Specifically, summarize:
- Founder net proceeds at each exit scenario
- Total investor proceeds and implied returns vs. capital invested
- Option pool gross value
- Any blocking rights or governance provisions that affect deal execution

The deal-memo-writer will reference this model as authoritative and summarize rather than
rebuild the waterfall. If a `financial-diligence` model has also been run, both outputs
can be fed into the memo together.
