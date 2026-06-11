# Asset — CP Tracker (Google Sheet schema)

The `deal-closing-planner` generates this sheet; `integration-tracker` runs it. Create it in the
deal's private Corp Dev Spaces folder. One row per condition / readiness item.

## Tab 1: `CP Tracker`

| Column | Type | Notes |
|---|---|---|
| ID | text | e.g., REG-01, CONSENT-03, FIN-02, DAY1-PPL-04 |
| Workstream | select | Regulatory · Consents · Stockholder · Financial/Tax/Acct · Day1-People · Day1-IT · Day1-Product · Closing-Mechanics |
| Item | text | The condition or readiness item |
| Type | select | CP (gates close) · Consent · Regulatory · Day-1 · Mechanics |
| Priority | select | P0 (blocks close) · P1 (clean Day-1) · P2 (fast-follow) |
| DRI | person | Single named owner |
| Status | select | Not started · In progress · Blocked · Drafted–awaiting approval · Complete |
| Due (relative) | text | e.g., "At signing", "Sign + 10d", "By close", "Close + 30d" |
| Long-lead? | checkbox | TRUE for PPA valuation, immigration, 280G, HSR waiting period |
| Blocker / notes | text | Current blocker; link to draft/doc |
| Human approval needed? | checkbox | TRUE for anything sent/filed/wired — agent drafts, human approves |

## Tab 2: `Summary` (formulas)

- Count and % complete by Priority (highlight: **P0 not complete = cannot close**)
- Count Blocked
- Count "Drafted–awaiting approval" (the human-approval queue)
- List of long-lead items not yet started

## Conventions
- **P0 is the close-blocking set** — the Summary tab should make "are we clear to close?" answerable
  at a glance (all P0 = Complete).
- Any row with **Human approval needed = TRUE** never auto-advances; `integration-tracker` surfaces
  it to the DRI and waits.
- Keep the sheet inside the deal's MNPI workspace; restrict sharing to the tent roster.
