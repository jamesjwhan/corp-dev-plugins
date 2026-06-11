# Asset — Integration Performance Dashboard (Google Sheet schema)

The `deal-integration-plan` **drafts content against this schema and asks the human to approve creating
the sheet** — it does not create it in Corp Dev Spaces / Drive on its own. Once approved and created,
`integration-tracker` runs the cadence on it. Share with the IWG + Exec Sponsor.

## Tab 1: `OKRs`
| Column | Type | Notes |
|---|---|---|
| Objective | text | Outcome that proves a thesis assertion |
| Ties to assertion | text | Which Deal Memo assertion |
| Key result | text | Measurable, time-bound |
| DRI | person | Roadmap DRI (Product) or Engagement DRI (Eng) |
| Target | text | The number + date (e.g., "GA by M6", "≥90% retention M6") |
| Current | text | Latest reading |
| RAG | select | Green · Amber · Red |
| Milestone | select | M2 · M4 · M6 |
| Notes / blockers | text | |

## Tab 2: `Metrics`
Deal-specific financial/business metrics tracked over time (weekly rows):
- Acquired-team **retention %** (always)
- Product adoption (acquired product usage in Toast base)
- Attached/cross-sell ARR
- Cost/synergy run-rate
- Any thesis-specific metric

## Tab 3: `Milestones`
- The 2/4/6-month roadmap milestones with owner, due, status, RAG.

## Tab 4: `Summary`
- OKR RAG rollup; retention trend; milestone hit-rate; open Reds for the Exec Sponsor.

## Conventions
- **RAG is honest** — Red surfaces fast; the dashboard exists to catch misses early, not to look green.
- **Retention is always on the dashboard** — it's the leading indicator of integration health.
- Cadence stamped on the sheet: 2–3×/week first 30 days → weekly.
