---
name: crm-setup-toast
description: >
  Use this skill when James wants to set up the CorpDev CRM Notion database for the first time,
  or says things like "set up the CRM", "create the CRM database", "bootstrap the CRM", or
  "initialize the Notion CRM". Creates the full schema including the Touchpoint Log sub-database
  and populates taxonomy select options from the existing Google Sheet. This is a one-time operation.
---

# CRM Setup (Toast)

One-time setup: create the CorpDev CRM Notion database in James's workspace with the full schema, Touchpoint Log sub-database, and pre-populated taxonomy select options extracted from his existing Google Sheet.

## When to Use

Only run this once. If the database already exists, confirm with James before proceeding — do not create a duplicate.

## Workflow

### Step 1 — Read Existing Taxonomy

Read the existing Google Sheet (ID: `1zlUSGACXQrXPOqN42r3u12SR2j3f6JAgzY1hZBmcGu8`) via Google Drive MCP. Extract unique values for:

- **Pillar** (column 1): ARPU, Consumer, Enterprise, INTL, New Verticals, Consolidation
- **Category** (column 2): Voice AI, Guest, BOH, Fintech, Vision AI, Demand Gen, Modern Dining, Enterprise, INTL, New Customers, Retail Customers, US Public Co, INTL Co
- **Sub-category** (column 3): Phone Ordering, Drive-thru, Loyalty, Online Ordering, Tableside, Camera AI, Tip management, Hiring/HR, Accounting, Middleware, Inventory, All-in-one, Capital, Banking, Tables, Membership, Payments, Partnerships, POS, Hotel PMS, Beauty and Spa, Auto repair, Gas/c-stores, Supplier (Retail), Supplier (Food), Suppliers (Cafes), Service/repairs, Nordics, Germany, Europe, Australia, E Asia, LATAM, Other
- **Construct**: M&A, Investment, Partnership, JV, Investment / product partnership, Investment / M&A, M&A Partnership, Partnership JV
- **Priority**: P0, P1, P2, P3 (passed)
- **Toast Integration**: Y, N, PortCo

Filter out "Ecosystem Partners" rows — they are excluded from the CRM.

Deduplicate and clean:
- Normalize Construct values: "Investment / product partnership" → multi-select [Investment, Partnership]; "Investment / M&A" → multi-select [Investment, M&A]. The canonical set is: M&A, Investment, Partnership, JV.
- Map both "P3" and "P3 (passed)" → "P3 (passed)".

Present the extracted taxonomy to James for confirmation before creating the database.

### Step 2 — Create the Notion Database

Create a new Notion database titled **"CorpDev CRM"** with these fields:

| Field | Notion Property Type | Configuration |
|---|---|---|
| Company | Title | Primary property |
| Status | Select | Active, On Hold, Passed, Monitoring |
| Pillar | Select | Options from Step 1 |
| Category | Select | Options from Step 1 |
| Sub-category | Select | Options from Step 1 |
| Construct | Multi-select | M&A, Investment, Partnership, JV |
| Priority | Select | P0, P1, P2, P3 (passed) |
| Source | Select | Inbound, Outreach, Conference, Referral, Other |
| Contact | Rich text | — |
| Contact Role | Rich text | Role/position of primary contact |
| Location | Rich text | HQ city/region |
| Toast Integration | Select | Y, N, PortCo |
| Docs Linked | URL | Link to Google Docs |
| Est. Revenue | Rich text | Estimate with source attribution (e.g., "$30M ARR (PitchBook, Jan 2026)") |
| Total Funding | Number (USD) | — |
| Last Valuation | Number (USD) | — |
| Key Investors | Rich text | Comma-separated |
| Board Members | Rich text | Comma-separated |
| Traction Score | Select | 1, 2, 3, 4, 5 |
| Product/Tech Score | Select | 1, 2, 3, 4, 5 |
| Team Score | Select | 1, 2, 3, 4, 5 |
| Description | Rich text | AI-generated one-liner |
| Last Updated | Date | — |

> **Note on "Contact Role":** This was previously named "Title" which conflicted with Notion's reserved Title property type. It is now "Contact Role" to avoid write errors.

> **Note on "Status" vs "Priority":** Priority indicates strategic importance (P0–P3). Status indicates deal stage (Active, On Hold, etc.). They serve different filtering needs.

### Step 3 — Create the Touchpoint Log Sub-Database

Create a second Notion database titled **"Touchpoint Log"** with:

| Field | Notion Property Type | Configuration |
|---|---|---|
| Entry | Title | Short label — format: `YYYY-MM-DD — [Company Name]` (e.g., "2026-04-29 — HiAuto"); use `[context] — [Company Name]` for general notes without a specific date |
| Company | Relation | → CorpDev CRM database |
| Date | Date | When the touchpoint occurred |
| Note | Rich text | What happened |

After creating the Touchpoint Log, add a **Rollup** to the CorpDev CRM database:

| Field | Notion Property Type | Configuration |
|---|---|---|
| Last Touchpoint Date | Rollup | Relation: Touchpoint Log → Company; Rollup property: Date; Function: Latest date |

This gives James a sortable "last touched" column without manual updates.

### Step 4 — Confirm

After creation, present James with:
- Database name and URL
- Field count and types
- Taxonomy values loaded
- Touchpoint Log sub-database URL
- Last Touchpoint Date rollup confirmed working

Tell James: "CorpDev CRM database created. You can now run the bulk migration to import your existing companies, or start adding companies one at a time."

Store both Notion database IDs (CorpDev CRM and Touchpoint Log) and present them to James so he can add them to his project config if needed. Downstream skills (`crm-migration-toast` and `crm-add-enrich`) reference these IDs.

## Failure Modes

- **Notion MCP auth fails** → surface clear error; do not retry silently.
- **Google Sheet read fails** → fall back to the hardcoded taxonomy values listed in Step 1 above. Tell James the Sheet couldn't be read and show which values were used.
- **Database with same name already exists** → ask James: "A Notion database called 'CorpDev CRM' already exists. Should I use the existing one, or create a new one?"
- **Rollup creation fails** → note the failure, continue. James can add the Last Touchpoint Date rollup manually via Notion UI.
- **Notion rate limits** → retry with backoff; this is a small operation so unlikely.

## Notes

- This skill runs once. After the database exists, the `crm-add-enrich` and `crm-migration-toast` skills take over.
- Store the Notion database ID so downstream skills can reference it. Present it to James so he can add it to his project config if needed.
