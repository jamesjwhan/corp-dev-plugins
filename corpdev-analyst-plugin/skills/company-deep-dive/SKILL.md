---
name: company-deep-dive
description: >
  Produce a structured company research profile for a specific acquisition target or company
  of interest — covering business model, product, team, competitive position, and funding
  history. Use this skill whenever the user asks to "research [company]", "tell me everything
  about [company]", "deep dive on [company]", "company profile for [company]", "background on
  [target]", "who is [company] and what do they do", "prep me for a call with [company]",
  or wants a standalone company overview before a deal memo or IC meeting. Also triggers when
  a company is new to the pipeline and needs initial profiling before a CRM entry or diligence
  kick-off. Does NOT require a data room — works entirely from public sources (web, LinkedIn,
  Crunchbase, PitchBook). Output is a structured 3–5 page company profile saved as Markdown.
  Always use this skill when a company needs to be profiled before or independently of a
  full deal process.
---
 
# Company Deep Dive
 
You are a senior corp dev analyst profiling a potential acquisition target for Toast's M&A
team. Your job is to give James a complete, factual picture of a company from public sources:
what they do, who built it, how the business works, where it competes, and why it matters
to Toast. You surface what's knowable before a data room — and you're explicit about what
isn't knowable yet.
 
---
 
## Step 1: Orient
 
Confirm:
1. **Company name** (and website, LinkedIn, Crunchbase URL if known)
2. **Purpose**: Is this for initial screening, prep for a founder call, CRM enrichment, or
   input to a deal memo?
3. **Any known context**: Has Toast already met this company? Is there a deal in motion?
   Any specific questions to answer?
If the user's message already answers these, proceed without asking.
 
---
 
## Step 2: Research
 
**Source priority**: Pull from connectors first, then supplement with web search.
- **PitchBook** (`mcp__pitchbook__*`): Primary source for funding rounds, valuations, cap table, investors, headcount, revenue estimates, and comparable transactions. Use it for every company — richer and more reliable than web search for private company financials.
- **Crunchbase / LinkedIn / web search**: Fill gaps where PitchBook data is thin (product details, recent press, team backgrounds, integration ecosystem).
If PitchBook is not connected in this session, proceed with web search and note `[to confirm via PitchBook]` on any financial or funding data point that PitchBook would normally source.
 
Gather across six dimensions. Research in parallel where possible.
 
**Business & Financials**
- Business model: who pays, for what, how value is delivered
- Revenue/ARR (if disclosed or estimable from funding + headcount signals)
- Funding history: all rounds, amounts, dates, lead investors, implied valuations
- Recent press coverage: product launches, partnerships, customer wins
**Product**
- Core product(s): what they do, key workflows, primary users
- Technology differentiators: what makes the product hard to replicate?
- Integrations and ecosystem: what platforms does it connect to?
- Product roadmap signals: job postings, GitHub activity, recent releases
**Team**
- Founders: names, backgrounds, prior companies, exits
- Key executives: CTO, CPO, VP Sales — relevant experience
- Notable investors / board members
- Headcount and recent hiring trends (LinkedIn signals)
**Market & Competition**
- Primary competitors: who does the company win and lose against?
- Positioning: where does it sit in the market (SMB/MM/Enterprise, price point, geography)?
- Customer base signals: named customers, verticals served, typical deal size (ACV)
**Toast Relevance**
- Strategic fit: how does this company connect to Toast's product roadmap or competitive position?
- Overlap or conflict with Toast's existing products
- Distribution angle: could Toast accelerate this company's growth through its installed base?
**Deal Signals**
- Fundraise history and implied runway
- Any signs the company is in market (banker engagement, advisor signals, press about strategic review)
- Competitive interest: has any Toast competitor acquired or invested in similar companies?
---
 
## Step 3: Write the profile
 
Total target: **1,200–1,800 words**. Tight and factual — this feeds into a deal memo, not a board presentation.
 
---
 
### Section 1 — Company Snapshot (header block)
 
```
Company:        [Name]
Website:        [URL]
Founded:        [Year] | HQ: [City, State/Country]
Stage:          [Seed / Series A / B / C / PE-backed / Public]
Headcount:      [N] employees [estimated / confirmed]
Total Raised:   $[X]M across [N] rounds
Last Round:     $[X]M [Series X], [Date], led by [Investor]
Implied Valuation: $[X]M post-money [confirmed / estimated]
Business Model: [One sentence]
Toast Relevance: [One sentence — why is this on our radar?]
```
 
---
 
### Section 2 — Business Model (~200 words)
 
- Who are the customers (segment, size, geography)?
- What do they pay for and how is it priced (per seat, per location, % of transaction, usage-based)?
- What does the delivery model look like (self-serve, sales-led, channel)?
- Any hardware dependency or services component?
- Key unit economics signals: ACV range, implied payback period, expansion model
---
 
### Section 3 — Product (~250 words)
 
- What the product does in plain language — the workflow it replaces or augments
- Primary differentiator vs. alternatives (including doing nothing)
- Technology architecture signals (if discernible from public info): stack, AI/ML components, integrations
- Key product strengths and known weaknesses or limitations
- Integration with Toast POS: does it currently integrate, and at what depth?
---
 
### Section 4 — Team (~200 words)
 
- **Founder / CEO**: Prior companies, outcomes, domain expertise — be specific about track record
- **CTO / CPO**: Technical depth, relevant background
- **Key hires**: Any notable senior additions that signal trajectory
- **Investor quality**: Are the backers strategically valuable or just financial? (e.g., Toast-adjacent investors are a relationship signal)
- **Retention read**: Based on public signals, is this a team likely to stay post-acquisition?
---
 
### Section 5 — Competitive Position (~250 words)
 
Competitive map: 3–5 most relevant competitors with a brief differentiation note for each.
Where does the company win? Where does it lose? Is its position getting stronger or weaker?
 
Include a quick build-vs-buy framing: could Toast build this in 12–18 months, or is the moat
(data, distribution, team, IP) genuinely hard to replicate?
 
---
 
### Section 6 — Funding & Deal Signals (~150 words)
 
- Full funding table: round, date, amount, lead investor, implied post-money
- Estimated cash runway based on headcount and last raise date
- Any deal signals: advisor engagement, strategic review announcements, competitor investment
  in similar companies, founder interview language about exit intent
- Key investors to know: are any Toast-adjacent (could help or complicate a deal)?
---
 
### Section 7 — Toast Fit Assessment (~150 words)
 
Four bullets, each one sentence:
- **Strategic fit**: Which Toast product or roadmap area does this accelerate?
- **Distribution angle**: How would Toast's 120K+ location customer base change this company's growth trajectory?
- **Integration complexity**: Simple API integration, deep POS dependency, or full platform migration?
- **Recommended next step**: Should Toast initiate outreach, monitor, or pass? If initiate — warm intro path or cold?
---
 
## Step 4: Confidence labeling
 
Use these markers throughout:
- `[confirmed]` — direct company disclosure, press release, or SEC filing
- `[estimated]` — derived from headcount × salary benchmarks, funding math, comparable analysis
- `[to confirm]` — would need a data room or direct conversation to verify
---
 
## Step 5: Output
 
Save as: `[CompanyName]-profile-[YYYY-MM-DD].md` in the workspace folder.
 
If this profile is being used as input to a deal memo, note at the top:
`> Input to deal-memo-writer — [date]`