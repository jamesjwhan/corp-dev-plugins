# Model Driver Taxonomy
 
*Reference for the financial-diligence skill, Step 3.*
*Use this to ensure all key assumptions are captured before building projections.*
 
---
 
## Revenue Drivers
 
### 1. ARR Bridge Components
 
These are the most important drivers — every ARR projection must flow from these.
 
**New Logo ARR**
- New logo count per quarter (by segment: SMB / Mid-Market / Enterprise)
- ACV per new logo by segment
- Segment mix shift over time (is the company moving upmarket?)
- Cross-check: implied new logo ARR vs. sales capacity model (see Headcount section)
**Expansion ARR**
- Expansion ARR per quarter (or: NRR − GRR as % of beginning ARR, applied quarterly)
- What drives expansion: seat-based, usage-based, module upsell, or geographic expansion?
- Is expansion concentrated in a few large customers or broad-based? (Affects reliability)
**Churn / Contraction ARR**
- Gross logo churn rate (annual, converted to quarterly)
- Contraction ARR from downgrades (if tracked separately from full churn)
- GRR = 1 − (Churn + Contraction) / Beginning ARR — use this as the base input
**NRR → Expansion check**
- NRR − GRR = net expansion contribution
- If NRR > 120% and GRR > 90%, expansion is the primary growth engine (strong)
- If NRR is only slightly above GRR, expansion is minimal (all growth comes from new logos)
### 2. Pricing
 
- Base ACV per segment (current period)
- Annual contractual price escalation (typically 3–8% for SaaS)
- Renewal rate vs. new logo rate (are renewals being done at discount? At premium?)
- Multi-year contract prevalence: what % of ARR is on multi-year deals?
### 3. Revenue Recognition / Billings
 
- Billing cadence: monthly, quarterly, or annual upfront?
- Multi-year upfront billing: if >20% of ARR is billed annually/multi-year upfront, deferred
  revenue builds matter for cash flow even if P&L looks clean
- Revenue recognition: straight-line (most common for SaaS) or milestone-based (services)?
- Services revenue recognition: on delivery vs. over contract term?
### 4. Revenue Mix
 
- SaaS / subscription as % of total revenue (current + trend)
- Professional services as % of total revenue (current + trend)
- Other (hardware, one-time, partner fees) as % of total revenue
- Note: services % trending up often indicates a product-market fit challenge;
  services % trending down (toward pure SaaS) is a positive margin signal
---
 
## Gross Margin Drivers
 
- **SaaS COGS**: hosting/infrastructure, customer success, implementation support
  amortized over the customer base. Scales with ARR but has leverage (% should fall over time)
- **Services COGS**: direct labor for professional services delivery; typically 60–70% COGS
  (30–40% margin) — well below SaaS margin and a blended-margin headwind
- **Gross margin trajectory**: Is there a clear path from current blended GM to a higher
  steady-state? (e.g., services mix declining + infrastructure cost leverage)
- **Key question**: what is the SaaS-only gross margin? This is the long-run margin profile
  of the business stripped of services drag.
---
 
## OpEx Drivers
 
### Sales & Marketing
 
- S&M headcount (quota-carrying reps + support staff)
- Average quota per rep and attainment rate
- Sales cycle length (months): affects when S&M spend translates to new ARR
- Marketing spend as % of total S&M
- CAC by channel (inbound vs. outbound vs. partner): blended vs. channel-specific
- **Sales capacity cross-check**:
  Implied New ARR = (# quota-carrying reps) × (avg quota) × (attainment %)
  Compare this to the new logo ARR assumption from the ARR bridge.
  If they diverge by >20%, investigate: pipeline coverage, ramp time, new rep hiring pace.
### Research & Development
 
- R&D headcount and % offshore vs. onshore
- Capitalized software development costs (if any — reduces reported R&D expense but
  cash goes out the door; adds to capex line)
- R&D % of revenue trajectory: should decrease as company scales, but watch for under-investment
- Key hires / initiatives that drive step-changes in R&D spend
### General & Administrative
 
- G&A headcount (finance, legal, HR, admin)
- G&A % of revenue: should compress from ~15-20% at growth stage toward ~8-10% at scale
- One-time items: legal expenses, audit fees, M&A costs — exclude from run-rate
- Public company readiness costs (if applicable): D&O insurance, SOX compliance
---
 
## Capital & Cash Drivers
 
- **Starting cash balance**: as of the most recent balance sheet date
- **Capex / capitalized software**: hardware, infrastructure buildout, capitalized development
- **Deferred revenue**: growing deferred revenue = healthy billings ahead of recognition;
  shrinking deferred revenue = bookings slowdown (leading indicator)
- **Accounts receivable**: DSO (days sales outstanding) — high or rising AR is a collection risk
- **Debt service**: any outstanding loans, convertible notes, or credit facility draws
---
 
## Suggested Additional Drivers (flag if data supports)
 
### Segment Mix Shift
If a company is transitioning upmarket (SMB → MM → Enterprise):
- Average new logo ACV rises over time even without explicit price increases
- Growth rate may moderate (fewer total logos, larger ACVs)
- NRR typically improves (enterprise customers expand more and churn less)
- Model this as a blended ACV that shifts across the projection period
How to model: project segment % of new logos over time, apply segment-specific ACV,
derive weighted average new logo ACV per quarter.
 
### Payback Period Trend
CAC payback is not static — it improves as:
- Brand awareness builds (lower CAC for inbound vs. outbound)
- ACV increases (same CAC, more revenue per logo)
- Gross margin improves (higher margin on the same ACV)
Project payback period improvement (if any) explicitly rather than holding it constant.
### Services Mix Trajectory
If professional services is a meaningful revenue stream:
- What is management's plan for services as % of revenue over the projection period?
- Is services growing in absolute $ even if shrinking as a % of total? (Important for GM)
- Does services growth correlate with new logo growth (implementation) or is it ongoing?
### Billings / Collections Model
For companies with annual upfront or multi-year billing:
- Build a quarterly billings schedule: New ARR billed + Renewals billed
- Deferred revenue ending balance = beginning deferred + billings − recognized revenue
- This bridges the gap between ARR, P&L revenue, and actual cash collections
---
 
## Common Pitfalls
 
| Pitfall | What to watch for |
|---------|------------------|
| Using ARR ≈ Revenue without checking | For multi-year or services-mixed companies, these diverge significantly |
| Holding gross margin flat | SaaS GM typically improves 1-3pp/year as infrastructure costs scale; services mix changes can move it 5-10pp |
| Linear S&M extrapolation | S&M is often lumpy (hiring classes, marketing campaigns) — validate against headcount plan |
| Ignoring deferred revenue | Prepaid contracts make P&L revenue look lower than cash collected; important for cash flow |
| NRR based on single cohort | Management sometimes reports NRR for best-performing cohort — always calculate blended NRR |
 