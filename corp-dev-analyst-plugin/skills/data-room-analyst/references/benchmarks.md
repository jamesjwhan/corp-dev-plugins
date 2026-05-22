# SaaS Benchmarks & Comp Transaction Reference

*Reference for data-room-analyst skill. Updated to reflect 2024–2025 market conditions.*

---

## SaaS Operating Benchmarks

### Growth Rate (ARR YoY)
| Stage | Excellent | Good | Watch | Red Flag |
|-------|-----------|------|-------|----------|
| Early ($1–10M ARR) | >100% | >60% | 30–60% | <30% |
| Growth ($10–50M ARR) | >60% | >35% | 20–35% | <20% |
| Scale ($50–100M ARR) | >40% | >25% | 15–25% | <15% |
| Late ($100M+ ARR) | >25% | >15% | 10–15% | <10% |

*Context: in a post-ZIRP environment, investors have reset expectations. "Good" now requires
capital efficiency alongside growth. A company growing 40% with a 2x burn multiple is less
attractive than one growing 30% with a 0.8x burn multiple.*

### Revenue Retention
| Metric | Best-in-Class | Healthy | Watch | Red Flag |
|--------|---------------|---------|-------|----------|
| NRR (Net Revenue Retention) | >130% | >110% | 95–110% | <95% |
| GRR (Gross Revenue Retention) | >95% | >90% | 85–90% | <85% |
| Logo Churn (annual) | <5% | <10% | 10–15% | >20% |

**Hard floor**: GRR < 90% means the existing customer base is shrinking. No amount of new
logo growth can sustainably overcome a leaky bucket. This is a material risk and must be
prominently discussed, not footnoted.

**NRR vs. GRR relationship**: NRR > GRR indicates expansion revenue from existing customers
(upsell/cross-sell). NRR significantly > GRR (e.g., NRR 120% / GRR 88%) means retention is
poor but a few customers are expanding heavily — this hides a fragile retention story.

### Gross Margins
| Model | Best-in-Class | Healthy | Watch | Red Flag |
|-------|---------------|---------|-------|----------|
| Pure SaaS | >85% | >75% | 65–75% | <65% |
| SaaS + Services | >70% | >60% | 50–60% | <50% |
| Marketplace / Transactional | >60% | >45% | 35–45% | <35% |
| Hardware-attached SaaS | >55% | >45% | 35–45% | <35% |

*Toast context: hardware-attached and transactional SaaS will have lower blended margins.
Adjust expectations accordingly. Focus on software-only gross margin when available.*

### Rule of 40

Rule of 40 = ARR Growth % + FCF Margin %
*(Use EBITDA margin as proxy if FCF not available)*

| Score | Assessment |
|-------|------------|
| >60 | Exceptional — elite efficiency |
| 40–60 | Strong — well-balanced growth and profitability |
| 20–40 | Acceptable — standard for growth-stage companies |
| <20 | Concerning — burning heavily relative to growth |
| <0 | Red flag — growing slowly and burning capital |

*Caveat*: Rule of 40 is most meaningful above $20–30M ARR. Below that, burn is often a
deliberate investment decision and the ratio can be misleading.

### Unit Economics
| Metric | Best-in-Class | Good | Acceptable | Watch |
|--------|---------------|------|------------|-------|
| CAC Payback (months) | <9 | 9–15 | 15–24 | >24 |
| LTV / CAC | >8x | 5–8x | 3–5x | <3x |
| Sales Efficiency / Magic Number | >1.5 | 1–1.5 | 0.7–1.0 | <0.7 |

*Magic number = (Net New ARR × 4) / Prior Quarter S&M Spend*

### Burn Efficiency
| Burn Multiple | Assessment |
|---------------|------------|
| <0.5x | Exceptional |
| 0.5–1.0x | Strong |
| 1.0–1.5x | Acceptable |
| 1.5–2.0x | Watch |
| >2.0x | Red flag |

*Burn Multiple = Net Cash Burned / Net New ARR*

### Customer Concentration
| Top 10 Customers as % of ARR | Assessment |
|-------------------------------|------------|
| <20% | Healthy — diversified base |
| 20–30% | Acceptable — monitor for single-customer risk |
| 30–50% | Watch — loss of one customer is material |
| >50% | Red flag — existential customer concentration |

---

## Comparable Transaction Multiples (EV / NTM ARR)

*Private company discount vs. public comps: ~20–35%. Use this table for private targets.*

### By Growth Profile (2023–2025 M&A activity)
| Growth Rate | NRR Profile | Implied EV/NTM ARR Range |
|-------------|-------------|--------------------------|
| >50% ARR growth | >120% NRR | 8–15x |
| 30–50% growth | >110% NRR | 6–10x |
| 30–50% growth | 95–110% NRR | 4–7x |
| 15–30% growth | >110% NRR | 4–7x |
| 15–30% growth | 95–110% NRR | 3–5x |
| <15% growth | Profitable | 2–4x (on ARR) or 1–3x on Revenue |

*Context: these ranges compress significantly for companies with GRR < 90%, high burn,
or significant customer concentration. Apply a 20–40% discount to the midpoint for
companies with one or more red flags.*

### Comparable Strategic Acquisitions (Restaurant / SMB Tech / Vertical SaaS)
| Acquirer | Target | Year | EV/NTM ARR | Notes |
|----------|--------|------|------------|-------|
| Toast | xtraCHEF | 2021 | ~6–8x est. | Restaurant back-office SaaS |
| Lightspeed | Upserve | 2020 | ~5x est. | Restaurant POS / analytics |
| NCR | Aloha (legacy) | various | — | Legacy POS, not comparable |
| Vista Equity | various VSaaS | 2022–24 | 4–8x | Profitable vertical SaaS |
| Thoma Bravo | various | 2022–24 | 5–10x | Depends heavily on growth profile |

*Note: strategic acquirers often pay a 20–40% premium over financial sponsors for assets
with clear synergies (revenue cross-sell, platform integration). Toast should anchor its
walk/no-walk price to the synergy-adjusted return, not just the stand-alone valuation.*

---

## Cohort Analysis Interpretation Guide

**What healthy cohort data looks like:**
- Each vintage expands over time (net expansion visible in cohort curves)
- Earlier cohorts are larger than later ones (shows duration-adjusted value)
- Churn cohorts are small and stable, not growing as % of cohorts
- No single cohort "cliff" suggesting a product or pricing change caused mass churn

**Red flags in cohort data:**
- Cohorts that plateau or decline after 12–18 months (limited expansion ceiling)
- Recent cohorts underperforming older ones at the same age (suggests product/market fit
  is weakening or competition intensifying)
- Missing cohort data itself — companies with strong retention don't hide the data

**GRR calculation check**: GRR should be calculated on beginning-of-period ARR,
net of churn and contraction only (excluding expansion). Verify that the company
is not including expansion in its GRR figure (a common mistake or obfuscation tactic).

---

## Red Flag Index

Flags that individually warrant significant discussion, and combinations that should
shift the recommendation to PASS or MONITOR:

| Flag | Standalone Impact | Combined with 2+ other flags |
|------|-------------------|-------------------------------|
| GRR < 90% | 🟡 Significant — probe deeply | 🔴 PASS unless clear explanation |
| NRR < 95% | 🟡 Significant | 🔴 PASS |
| Top-10 > 50% ARR | 🟡 Significant | 🔴 PASS |
| Burn multiple > 3x | 🟡 Significant | 🔴 PASS |
| Missed plan 2+ years in a row | 🟡 Significant | 🔴 PASS |
| Cap table complexity (>15 investors, complex preferences) | 🟢 Monitor | 🟡 Raises deal risk |
| No audited financials | 🟡 Requires QoE | 🟡 |
| Founder departures in last 12 mo | 🟡 Probe | 🟡 |
