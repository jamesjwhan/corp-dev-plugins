# Module — Day-1 Readiness

Day-1 readiness is treated as an **internal condition to close**: Toast doesn't close until it can
operate the acquired team and product on Day-1. Three tracks. Each item is typically **P1** (needed
for a clean Day-1) unless it gates close.

> Principle: everything is **staged but not switched on** until close. Access, announcements, and
> payroll flip at the close gate — not before. If HSR-reportable, gun-jumping rules bar premature
> integration; stage only.

## A. People & HR — Owner DRI: **People Ops**

- Offer letters / employment agreements for all transferring employees; leveling + comp mapping locked
- Key-employee **retention agreements signed**, effective at close
- Benefits transition (health, 401(k) blackout/rollover, COBRA for non-transferring, PTO/accrual mapping)
- **Payroll cutover** so the first post-close paycheck is correct; multi-state registration as needed
- **Immigration** transfers (H-1B amendments, green-card continuity) — **long-lead, start at signing**
- International employees/contractors — entity or employer-of-record/vendor setup — **long-lead**
- WARN Act analysis if any roles are eliminated
- Day-1 org: reporting lines, manager assignments, onboarding/equipment plan

## B. Systems, IT & Security — Owner DRI: **IT / Security** (with Eng)

- **Day-1 access runbook**: email, SSO, Slack, HRIS, identity — provisioned but **switched at close**
- Security-review remediation items closed; endpoint management enrollment
- Secrets/credentials handover; decommission plan for the target's redundant tooling
- **Data migration plan** with privacy/DPA compliance for customer-data transfer
- Product/eng: repo and environment access expands **from clean-team to full team at close**;
  operationalize the keep/rebuild/discard integration milestones from tech diligence

## C. Product, Customer & Commercial — Owner DRI: **Product + GTM**

- **Customer communication plan** (timed to close); retention plays for at-risk accounts
- Contract assignment/novation where consent is required (coordinate with the consents module)
- **Support transition** — who owns acquired customers Day-1
- Legacy-product **sunset / migration timeline**
- Partner/channel notifications

## In/out summary for the plan
- People track in **if** employees transfer (almost always). International items gated by facts.
- IT/Security track **always in**.
- Product/Commercial track in **if** customers/product transfer (out for a pure talent acqui-hire
  with no product to assume).
