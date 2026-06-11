# Asset — Earnout / Post-Close Obligations Tracker (Google Sheet schema)

Optional — draft when the deal has an earnout, holdback, or material post-close obligations. The skill
**drafts content against this schema and asks the human to approve creating the sheet** — it does not
create it in Drive on its own. DRI: **Finance** (with Corp Dev).

## Tab 1: `Earnout`
| Column | Type | Notes |
|---|---|---|
| Milestone | text | The earnout condition (e.g., "ARR ≥ $X by M12", "product GA by M9") |
| Measurement basis | text | How it's measured + source of truth |
| Period | text | Earnout period / measurement date |
| Status | select | On track · At risk · Achieved · Missed |
| Payout | currency | Amount if achieved |
| Payout date | date | When it pays |
| DRI | person | Finance |
| Notes | text | Disputes, definitions |

## Tab 2: `Post-Close Obligations`
| Item | Type | Notes |
|---|---|---|
| **NWC true-up** | one-time | Final NWC vs. peg; true-up amount; window (typically 60–90 days post-close) |
| **Escrow releases** | scheduled | Indemnity/adjustment escrow release dates + amounts |
| **Indemnity survival** | period | Survival periods for reps/warranties; claim deadlines |
| **Post-close tax** | tasks | Elections, final returns, transfer-tax filings |
| **Holdbacks** | scheduled | Any holdback release conditions/dates |

Columns: item · DRI · due date · amount · status · notes.

## Tab 3: `Summary`
- Upcoming obligations (next 90 days); earnout exposure (max payout); open disputes.

## Conventions
- This is the **post-close tail** handed over from the closing plan — continuity so nothing falls
  through the gap between close and the integration team.
- Finance owns; surfaced by `integration-tracker` on the integration cadence.
