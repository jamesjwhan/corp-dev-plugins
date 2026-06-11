# Module — Closing Mechanics

**Always in scope.** Owner DRI: **Corp Dev** (with Legal). This is the spine of every sign-to-close
plan — the CP tracker, the money movement, and the close event itself.

## A. Conditions-precedent (CP) tracker

The master artifact. One row per condition to close, with owner and status. Built from
`assets/cp-tracker-schema.md`. Every other module's P0 items roll up here. This is what
`integration-tracker` runs to closure.

## B. Closing set & certificates — Owner DRI: **Legal**

- **Bring-down of reps & warranties** — confirm representations remain true as of close
- **Officer's / secretary's / good-standing certificates**
- **Signature pages / closing set** assembled and held in escrow pending close

## C. Flow of funds — Owner DRI: **Finance** (mechanics) + **Corp Dev** (coordination)

- **Flow-of-funds memo** — who gets paid what, from where, in what order; ties to the payment
  spreadsheet and the estimated closing statement
- **Wire instructions** verified (callback verification — fraud control); escrow account funded
- **NEVER executed by the skill or the agent** — wires are drafted/reconciled and routed to the
  named human (Finance/Treasury) for authorization. This is the highest-risk action in the deal.

## D. The close gate — **`◆` exec sign-off (CFO/CLO/CEO)**

The close event:
1. Confirm all CPs satisfied or waived (CP tracker fully green)
2. Deliver certificates; release signature pages from escrow
3. **Authorize** funds flow → wires sent; equity issued (human approval required)
4. Flip the switches: access on, payroll live, announcement fires (hand to `deal-comms-runbook`)
5. Confirm close; hand off to Stage 7 `deal-integration-plan` / `integration-tracker`

**Same-day close:** this gate merges into signing — CPs are satisfied at or before signing, and the
close is simultaneous. The plan still lists the items; they just collapse onto one date.

## Post-close tail (flag for continuity, executed in Stage 7)
NWC true-up (60–90 days); escrow-release schedule; indemnification survival; earnout administration
(if any); post-close tax elections. Hand these to the `deal-integration-plan` earnout/obligations tracker.
