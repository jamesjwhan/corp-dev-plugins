# Asset — Diligence Status Tracker (Google Sheet schema)

The `deal-diligence-planner` **drafts content against this schema and asks the human to approve
creating the sheet** — it does not create it in Corp Dev Spaces / Drive on its own. Once approved and
created, `diligence-coordinator` runs it. Mirrors the closing-planner's CP tracker for consistency.

## Tab 1: `Diligence Tracker`

| Column | Type | Notes |
|---|---|---|
| ID | text | e.g., LEG-03, TECH-01, FIN-02, PPL-04, CUST-01 |
| Workstream | select | Legal · Technical · Financial · People · Tax/Acct · Customer |
| Request item | text | The specific request or question |
| Tests assertion | text | Which Deal Memo assertion this validates (blank if confirmatory) |
| Priority | select | P0 (assertion/deal-breaker) · P1 (material) · P2 (confirmatory) |
| DRI | person | Single named owner |
| Status | select | Not requested · Requested · Received · In review · Blocked · Complete |
| Due (relative) | text | e.g., "Week 1", "Pre-final approval" |
| Long-lead? | checkbox | TRUE for full security review, all-employee interviews on large teams |
| Finding / notes | text | Result, flag, or blocker; link to analysis |
| Human approval needed? | checkbox | TRUE for any outbound request — agent drafts, human sends |

## Tab 2: `Assertions Coverage`
- One row per Deal Memo assertion → which P0 items test it → status. Surfaces **uncovered
  assertions** (a hole in the plan).

## Tab 3: `Summary`
- % complete by Priority; **open P0 count** (the gate to final approval); blocked count; approval queue.

## Conventions
- **P0 maps to assertions** — confirmatory diligence isn't done until every key assertion is tested.
- Outbound requests (Human approval needed = TRUE) are drafted, never auto-sent.
- Keep inside the MNPI workspace; restrict to the tent roster.
