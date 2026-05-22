# Diligence Extraction Checklist

*Document-by-document guide for the data-room-analyst skill.*
*Use this to ensure you pull the right data points from each document type.*

---

## Investor / Board Deck (usually most info-dense — read first)

**Extract:**
- Company overview: what they do, target customer, value proposition
- ARR and ARR growth (current + 1–2 year history)
- Key metrics snapshot (NRR, GRR, gross margin, customer count)
- Market size claims and source
- Competitive positioning map
- Product roadmap (near-term priorities)
- Team bios: founders, key executives, investors

**Watch for:**
- Metrics presented in favorable but non-standard ways (e.g., "total contract value" instead
  of ARR, "platform revenue" that bundles non-recurring items)
- TAM figures without methodology (treat these as aspirational, not analytical)
- "Customer count" that mixes active and churned/trial users
- Missing cohort charts or retention data (omission is informative)

---

## Financial Model / Data Room Financials

**Extract:**
- P&L by year: revenue, COGS, gross profit, S&M, R&D, G&A, EBITDA/operating income
- Revenue breakdown: recurring (ARR/MRR), professional services, one-time
- ARR bridge/waterfall: beginning ARR, new logo ARR, expansion, churn, ending ARR
- Cash and cash equivalents (latest balance sheet date)
- Burn rate (monthly and annual) and runway
- FY budget/plan vs. actuals (each year — are they a company that hits its numbers?)

**Calculate:**
- YoY ARR growth (actuals only — don't rely solely on management's reported figure)
- NRR = (Beginning ARR + Expansion – Churn) / Beginning ARR × 100
- GRR = (Beginning ARR – Churn – Contraction) / Beginning ARR × 100
- Gross margin = Gross Profit / Revenue × 100
- Rule of 40 = ARR Growth % + EBITDA Margin %
- Burn multiple = Trailing 12-month net burn / Net New ARR

**Watch for:**
- Revenue recognition policies — is ARR being recognized upfront or ratably?
- Deferred revenue balance on balance sheet (large deferred revenue = healthy collection;
  declining deferred revenue = slowing bookings)
- "Adjusted" metrics that exclude recurring COGS or stock-based comp without justification
- Plan/budget credibility: companies that consistently miss plan warrant a larger risk discount
- Accounts receivable aging — large past-due balances signal collection problems

---

## Customer / Cohort Data

**Extract:**
- Total active customer count (by segment if available)
- ACV by segment: SMB, mid-market, enterprise (define thresholds used)
- Customer concentration: top 5 and top 10 customer ARR as % of total
- Cohort retention table or chart: by vintage year/quarter
- Logo churn rate (annual): customers lost / beginning customer count
- NRR and GRR by segment (if available)

**Calculate from cohort data:**
- Time to first expansion (average months before a customer upsells)
- Cohort health: are recent cohorts expanding faster or slower than older ones at the same age?

**Watch for:**
- Cohort data that starts from a non-standard date or excludes early vintages
- NRR significantly higher than GRR — dig into which customers are expanding and why;
  if it's 2–3 outlier customers, the NRR is not representative of the base
- Customer count definitions that include free/trial users or churned accounts on payment plans
- Missing cohort data entirely (always a yellow flag — probe in management Q&A)

---

## Cap Table

**Extract:**
- Founder ownership %
- Lead investor(s), ownership %, and round history (Series A, B, C, amounts raised)
- Liquidation preference stack (1x, 2x, participating?)
- Option pool size (% of fully diluted)
- Any warrants, convertible notes, SAFEs outstanding

**Watch for:**
- Heavily diluted founders (<15% ownership) — can reduce retention incentive post-close
- Large liquidation preference overhang (2x+ participating preferred) — can create a
  misalignment between investor and founder economics at exit
- Many small investors (>15 institutional investors) — can complicate transaction approvals
  and create holdout risk
- Down round history — probe why, and assess impact on team morale and culture

---

## Customer Contracts (sample review)

**Extract (from a representative sample):**
- Standard contract length (annual, multi-year?)
- Auto-renew provisions
- Termination rights (for convenience? notice period?)
- Price escalation clauses
- Change of control provisions — do contracts require customer consent at acquisition?
  (CRITICAL for Corp Dev — a large % of ARR subject to change-of-control consent = deal risk)
- IP ownership language (especially for custom development done for key customers)

**Watch for:**
- Change-of-control clauses that allow customers to terminate or reduce fees at acquisition —
  this directly impacts the revenue multiple you should pay
- Non-standard SLAs or support commitments in key customer contracts
- Revenue share or royalty obligations to third parties embedded in contracts
- Unusual limitation-of-liability caps that might expose acquirer

---

## Legal / Corporate Documents

**Extract:**
- Jurisdiction of incorporation
- Outstanding litigation or regulatory matters
- Known IP disputes or open-source compliance issues
- Employment agreements for key executives (change-of-control triggers, non-competes)
- Material agreements: reseller agreements, technology licenses, partnership agreements

**Watch for:**
- IP not cleanly owned by the company (especially if founders did early development while
  employed elsewhere — "prior employer" IP risk)
- Open-source licenses in the codebase that could restrict commercialization (GPL exposure)
- Non-compete and NDA gaps with key employees
- Outstanding employee claims (harassment, wrongful termination) — ask directly

---

## Market / Competitive Materials

**Extract:**
- TAM claim, SAM, SOM with methodology (adjust credibility accordingly)
- Named competitors with relative positioning
- Win/loss data (if provided — this is rare and very valuable)
- Customer use cases and ICP definition

**Watch for:**
- TAMs from third-party research firms without checking methodology (Gartner/IDC TAMs
  are often aspirational and not bottoms-up verified)
- Competitive analysis that conveniently omits well-funded competitors
- "We don't have direct competitors" (this is never true — probe what customers use today
  and what the displacement cost is)

---

## Management Team Materials

**Extract:**
- Founder backgrounds and tenure
- Key executive team: VP Sales, VP Eng, CFO (or equivalent)
- Recent departures (ask if not in the data room)
- Headcount by function and location

**Watch for:**
- Thin finance function for the company's scale (suggests data quality risk)
- Recent departure of VP Sales or CTO (usually a signal of something — probe)
- Headcount concentrated in high-cost geographies without clear justification
- Missing org chart or team bios (suggests something to hide)

---

## What to Do When Key Documents Are Missing

| Missing Document | What It Signals | What to Ask |
|-----------------|-----------------|-------------|
| Cohort / retention data | Company may be hiding poor retention | "Can you share the monthly ARR waterfall and cohort retention curves for all vintages?" |
| Cap table | Deal complexity or sensitivity | "Can you share the fully diluted cap table including preferences and option pool?" |
| Audited financials | Immature finance function; potential data quality issues | "Do you have audited financials? If not, what is the audit readiness?" |
| Budget vs. actuals | Possibly missing or unfavorable | "Can you provide the last 2–3 years of budget vs. actuals?" |
| Customer contracts (sample) | Change-of-control risk may be material | "Can we review a sample of your top 10 customer contracts, specifically regarding change-of-control provisions?" |
| Win/loss analysis | Competitive position may be weaker than presented | "Do you have win/loss data by segment? What is your typical displacement scenario?" |
