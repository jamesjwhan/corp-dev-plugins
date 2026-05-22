---
name: crm-migration-toast
description: >
  Use this skill when James wants to migrate his existing companies from Google Sheets to the
  Notion CRM, or says things like "migrate the spreadsheet", "import companies", "bulk import",
  "move the sheet to Notion", or "load the CRM from the spreadsheet". One-time operation that
  reads the existing Google Sheet, maps columns to the Notion schema, flattens touchpoint
  history, and creates records in the CorpDev CRM Notion database.
---

# CRM Bulk Migration (Toast)

One-time operation: import ~80 companies from James's existing Google Sheet into the CorpDev CRM Notion database. Maps columns, flattens touchpoint history into chronological Touchpoint Log entries, and excludes Ecosystem Partners.

## Inputs

- Google Sheet ID: `1zlUSGACXQrXPOqN42r3u12SR2j3f6JAgzY1hZBmcGu8`
- The CorpDev CRM Notion database must already exist (created by `crm-setup-toast`).

## Outputs

- ~80 Notion company records with mapped data
- Chronological Touchpoint Log entries for each company (flattened from the Sheet's 4 fixed touchpoint columns)
- A summary report: companies imported, rows skipped (and why), new taxonomy values flagged

## Workflow

### Step 1 — Read the Google Sheet

Read the full Sheet via Google Drive MCP. Parse all rows.

### Step 2 — Filter and Validate

- **Exclude** rows where Pillar (column 1) = "Ecosystem Partners"
- **Exclude** empty rows (no Company name)
- **Count** remaining companies for James's confirmation

Present: "Found [N] companies to migrate (excluded [M] Ecosystem Partners and [K] empty rows). Proceed?"

### Step 3 — Detect Taxonomy Issues

Before writing any records, scan all rows for taxonomy values. Flag any values that don't match the select options created during Schema Bootstrap:

- New Pillar values
- New Category values
- New Sub-category values
- Non-standard Priority values

Present all flags at once: "Found [N] new taxonomy values across the dataset: [list]. Should I create these, or do any need renaming?"

Wait for James's confirmation before proceeding.

### Step 4 — Map and Write Records

For each row (in order), map columns to the Notion schema:

| Sheet Column | Notion Field | Mapping Notes |
|---|---|---|
| Pillar (col 1) | Pillar | Direct map |
| Pillar (col 2) | Category | Direct map |
| Sub-pillar (col 3) | Sub-category | Direct map |
| Construct (col 4) | Construct | Normalize to multi-select: "Investment / product partnership" → [Investment, Partnership]; "M&A, Partnership" → [M&A, Partnership] |
| Company (col 5) | Company | Direct map (Title property) |
| Priority (col 6) | Priority | Map "P3" → "P3 (passed)". Map explicit "P3 (passed)" → "P3 (passed)". Do NOT remap P0, P1, or P2 — if a row has "P2 (passed)" flag it for James's review rather than auto-converting. |
| Contact (col 7) | Contact | Direct map |
| Title (col 8) | Contact Role | Direct map (note: Sheet column is "Title"; maps to Notion field "Contact Role") |
| Location (col 9) | Location | Direct map |
| Toast integration (col 16) | Toast Integration | Direct map |
| Docs Linked (col 17) | Docs Linked | Direct map as URL |
| Notes (col 15) | — | Migrate as a Touchpoint Log entry with Entry title `[context] — [Company Name]` and Date = blank |

**Status field:** Default all migrated companies to **"Active"** unless Priority is "P3 (passed)", in which case set Status to **"Passed"**.

**Source field:** Leave blank for all migrated records (historical sourcing is unknown). James can fill in later.

**Fields left blank after migration** (to be populated by enrichment later):
- Est. Revenue, Total Funding, Last Valuation, Key Investors, Board Members
- Traction Score, Product/Tech Score, Team Score
- Description
- Last Updated → set to today's date

### Step 5 — Flatten Touchpoint History

For each company, create Touchpoint Log entries from the Sheet's touchpoint columns. Work from oldest to newest:

1. **Prev TP Notes (col 14)** → oldest entry (if not empty)
2. **Prev TP Notes (col 13)** → next entry
3. **Prev TP Notes (col 12)** → next entry
4. **Last TP Notes (col 11)** → most recent entry

For each non-empty touchpoint:
- **Entry title:** Format as `YYYY-MM-DD — [Company Name]` (e.g., "2026-04-29 — HiAuto"). If the exact date can't be parsed, use `[unparsed] — [Company Name]`.
- **Date:** Extract from the note text if it starts with a date pattern (e.g., "11/24/25:", "Jan 2026:", "Oct '25:"). If the Last Touch Point column (col 10) has a date, use it for the most recent entry.
- **Note:** The full text of the touchpoint entry
- **Company:** Relation to the parent company record

Also migrate the Notes column (col 15) as a separate Touchpoint Log entry with Date = blank (general context, not a specific touchpoint). Entry title: `[context] — [Company Name]`.

### Step 6 — Post-migration Enrichment Pass

After all records are written, run a lightweight enrichment pass on every company record:

**For each company:**
1. **Location** (if blank in the Sheet): Run a web search for `"[Company Name]" headquarters` to find HQ city/region. If found, update the Location field. If not found, leave blank.
2. **Description**: Run a web search for `"[Company Name]" company` to generate a one-line description of what the company does. Always populate this field for all companies.
3. **Docs Linked** (if blank in the Sheet): Search Google Drive **scoped strictly to the Corp Dev Spaces folder** (folder ID: `1ayQfU7WpkvQV4xzsUAPpAkkLjOcdxr_i`) for any Google Docs matching the company name. After finding a file, always verify its parent folder path contains "Corp Dev Spaces" before linking — Drive search can return files from other shared folders that look like matches but aren't Corp Dev docs.

   **Search strategy — try all of these for each company:**
   - Exact company name: `"HiAuto"`, `"Maple"`
   - No-space variant: `"LomanAI"` for Loman AI, `"LoopAI"` for Loop AI (common in this folder)
   - Name + descriptor: `"[Company] notes"`, `"[Company] AI"`, `"[Company] Corp Dev"`
   - Search must reach **all subfolder depths** — docs may be nested 2–3 levels deep (e.g., `Corp Dev Spaces / Voice AI / Targets / LomanAI` or `Corp Dev Spaces / Consumer Guest FOH / Loyalty / Thanx`). Do not stop at the first subfolder level.

   **Folder priority if multiple docs found:** Deal Memo > Corp Dev Notes > other Google Doc. Exclude PDFs and spreadsheets — link to Google Docs only.

   If none found inside Corp Dev Spaces, leave blank. Do not link docs from other Drive folders (partner folders, BD folders, SLT shared drives, etc.).

Run in batches of 10 to respect API rate limits. If web search returns no usable data for a company, leave the field blank rather than guessing.

**Note on PitchBook:** PitchBook enrichment (revenue, funding, valuation, investors, scores) is NOT part of the migration enrichment pass — that runs separately via `crm-add-enrich`. This step only populates lightweight public fields (Location, Description, Docs Linked).

### Step 7 — Present Summary

After all records are written, present a summary:

```
Migration complete:
  ✓ [N] companies imported
  ✓ [M] touchpoint entries created
  ✗ [K] rows skipped: [reasons]
  ⚠ [J] new taxonomy values created: [list]
  ⚠ [L] rows with ambiguous priority (e.g. "P2 (passed)") flagged for review: [list]

Next steps:
  - Browse the CRM: [Notion database link]
  - To enrich companies with PitchBook data and scores, say "enrich [company name]"
  - To batch-enrich, we can run enrichment on companies in groups of 10-15
```

## Batching Strategy

Write records in batches of 10 to manage Notion API rate limits:
- Write 10 records
- Brief pause
- Write next 10
- Continue until complete

If a batch fails partway through:
- Note which companies were successfully written
- Report the failure point
- Offer to resume from the last successful record

## Failure Modes

- **Google Sheet read fails** → surface error. Cannot proceed without the source data.
- **Notion MCP auth fails** → surface error. Cannot write records.
- **Sheet schema changed** → validate column headers against expected schema before processing. If columns are in a different order, attempt to match by header name. If headers are unrecognizable, stop and ask James.
- **Partial migration failure** → report progress (e.g., "42 of 78 companies written successfully. Failed at row 43: [error]. Resume?"). Allow restart from the failure point.
- **Near-duplicate companies in the Sheet** → flag but don't block. Some companies may appear similar but are distinct (e.g., different entities with similar names). Report potential duplicates at the end.
- **Touchpoint date parsing fails** → write the entry without a date, use `[unparsed] — [Company Name]` as the Entry title. Note which entries have unparsed dates in the summary.
- **Notion rate limits** → increase batch pause interval and retry.
- **"P2 (passed)" or other unexpected priority variants** → do NOT auto-convert. Flag for James's review at the end of migration.

## Notes

- This is a one-time operation. After migration, new companies are added via `crm-add-enrich`.
- Migration does NOT run enrichment (PitchBook, web search, scoring). That's a separate step James can run per-company or in batches after migration.
- The Google Sheet remains as-is after migration — it's not modified or deleted. James can keep it as a backup.
- If James wants to re-run migration (e.g., after a failed first attempt), check for existing records and skip companies already in Notion to avoid duplicates.
- "Contact Role" in Notion corresponds to the "Title" column in the Sheet. The rename was intentional to avoid Notion's reserved property name.