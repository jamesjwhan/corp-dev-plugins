# Module — Financial Diligence

DRI: **Finance** (with Tax/Accounting). In scope when revenue is material. **Hand the modeling to the
`financial-diligence` skill** — this module scopes the request list and the questions; that skill
builds the QoE, PF model, and benchmarks.

## Request list (trim to the deal)

**Quality of earnings (QoE)** *(P0 when revenue/margins are a key assertion)*
- Monthly P&L 12–18 months history; revenue recognition policy; one-time vs. recurring
- Normalized EBITDA bridge (add-backs scrutinized); gross margin by product/segment
- Cash burn and runway

**Revenue & retention** *(P0 when growth/retention is the thesis)*
- ARR/MRR bridge; new/expansion/contraction/churn; GRR and NRR (ties to customer-dd)
- Cohort retention; logo vs. dollar retention
- Pipeline and bookings quality

**Unit economics & efficiency**
- LTV/CAC, CAC payback, magic number, burn multiple, Rule of 40
- Sales efficiency by channel/segment

**Balance sheet / working capital**
- **Net-working-capital trend** (sets the peg for the closing NWC true-up); cash bridge
- Debt and cash schedules; off-balance-sheet items; deferred revenue treatment
- AR aging; significant accruals/liabilities

## Priorities
- **P0:** QoE integrity, retention metrics validating the thesis, NWC trend, any restatement risk
- **P1:** unit economics, margin trajectory
- **P2:** cohort detail, channel-level efficiency

## Output → `financial-diligence`
This module produces the *request list and questions*. The `financial-diligence` skill consumes them
to build: quarterly P&L (history + projections from an explicit assumptions model), valuation (NTM
ARR / GP multiples + sensitivities), and the benchmark set (GRR/NRR, magic number, LTV/CAC, burn
multiple, Rule of 40).

## Out / light when
Light/none for pre-revenue acqui-hires (no QoE; confirm burn/runway only). Deep for platform deals.
