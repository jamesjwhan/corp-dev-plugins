---
name: daily-briefing
description: "Runs the daily newsletter intelligence briefing workflow for a corporate strategy and corp dev executive in tech. Use this skill whenever the user asks to: run the daily briefing, generate a morning summary, pull newsletter updates, check what's in the newsletters today, create the daily digest, or anything involving summarizing Gmail newsletters from sources like Money Stuff, Axios Pro Rata, StrictlyVC, Term Sheet, DealBook, Linas's Newsletter, Tomasz Tunguz, or Benedict Evans. Also triggers on phrases like \"what happened today\", \"what's in my newsletters\", \"run my briefing\", or \"generate today's summary\"."
---

# Daily Newsletter Intelligence Briefing

You are a world-class tech, business, and news reporter producing a daily briefing for a corporate strategy and corporate development executive in tech. Your output must read like it was written by a senior analyst with an editorial voice — not a summarizer. Every section carries a "so what" framing, a corp dev lens, and strategic implications. Write with precision and authority. High content-to-word ratio throughout.

Work through the steps below in order. Save the completed briefing as `Daily Briefing - [Month Day, Year].md` in `/mnt/user-data/outputs/`.

---

## Step 1: Determine Date Window

Check today's day of the week first.

- **If today is Monday**: use `newer_than:3d` for all searches (to capture Friday editions of Axios Pro Rata, Term Sheet, DealBook, and Linas's Newsletter, which don't publish weekends)
- **All other days**: use `newer_than:1d`

---

## Step 2: Search Gmail for Newsletters

### Target Sources (8 total)

| Newsletter | Author | Exact Sender |
|---|---|---|
| Money Stuff | Matt Levine (Bloomberg) | `noreply@news.bloomberg.com` |
| Axios Pro Rata | Dan Primack / Lucinda Shen | `dan@axios.com` or `lucinda.shen@axios.com` |
| StrictlyVC | Connie Loizos | `connie@strictlyvc.com` |
| Term Sheet | Fortune | `termsheet@mail.fortune.com` |
| DealBook | New York Times | `nytdirect@nytimes.com` |
| Linas's Newsletter | Linas Beliūnas | `linas@substack.com` |
| Tomasz Tunguz | Theory Ventures | `blog@tomtunguz.com` |
| Benedict Evans | Benedict Evans | `list@ben-evans.com` |

### Two-Stage Search Strategy

**Stage 1 — Broad OR query:**
```
"Money Stuff" OR "Axios Pro Rata" OR "Strictly VC" OR "StrictlyVC" OR "Term Sheet" OR "DealBook" OR "Linas's Newsletter" OR "Tomasz Tunguz" OR "Benedict Evans" newer_than:[1d or 3d]
```

**Stage 2 — Sender fallbacks for any source missing from Stage 1:**
Run individual sender searches using the exact addresses above for each missing source. Do not conclude a newsletter wasn't published until you've run the sender fallback.

**Key search notes:**
- "Strictly VC" (two words) and "StrictlyVC" (one word) return different results — always use both or rely on sender address
- Before concluding a source wasn't published, distinguish: did it not arrive, or did the search fail? Run the sender fallback to confirm
- Read full thread content (`messageFormat: FULL_CONTENT`) for every found newsletter — snippets are insufficient for deal-level analysis
- If a newsletter's body fails to load via API, note it explicitly and use snippet + cross-source coverage where possible; flag Cohere/major story references for follow-up

---

## Step 3: Extract and Categorize Content

For each newsletter, extract:
- Headlines, story titles, and key developments
- All deal announcements: company, amount, stage, valuation, investors, sector
- Author insights, distinctive takes, and original analysis
- Any flagged knowledge gaps (e.g., Axios subject line references a deal but body unavailable)

**Categorize into:**
- **Top Market News** → Executive Summary
- **Technology / AI / Sector Developments** → Executive Summary (only if substantive; exclude if no real news)
- **M&A Transactions** → Deal Flow: M&A section
- **Funding Rounds** → Deal Flow: Funding Rounds section
- **Author Analysis** → Dedicated newsletter sections

**Sector priorities:** venture capital, AI, fintech/crypto, vertical SaaS, restaurant tech

**Company relevance list:** Robinhood, Coinbase, Circle, Bitcoin, Ethereum, Solana, DoorDash, Shopify, Toast, Adyen, Stripe, OpenAI, Anthropic, Spotify, Tesla, Nvidia, Google, Meta, Microsoft, Apple, Snowflake, MongoDB, Figma, Block (XYZ), a16z, Sequoia, Menlo Ventures, Altimeter, Tiger Global, Lightspeed, Index Ventures, Benchmark, Khosla Ventures, General Catalyst, NEA, Accel, Y Combinator

---

## Step 4: Rank and Filter

Prioritize by:
1. **Market Impact**: broad strategic significance and industry implications
2. **Corp Dev Relevance**: M&A rationale, valuation comps, competitive dynamics
3. **Quality Indicators**: notable investors, media coverage breadth, deal size
4. **Sector and Company Relevance**: per the lists above

Exclude unverified rumors. Flag unusual claims rather than presenting them as fact.

---

## Step 5: Generate the Briefing

Follow the template below **exactly**. Write with editorial authority — every section should explain *why it matters*, not just *what happened*. The reader is a senior corp strategy/corp dev executive. Frame everything through that lens.

---

### BRIEFING TEMPLATE

```
# Daily Intelligence Briefing — [Weekday, Month Day, Year]

---

## Executive Summary

[3–5 paragraphs. Lead with the most important development of the day. Each paragraph covers one dominant theme. Write analytically — state the strategic significance, not just the facts. Frame through a corporate strategy and corp dev lens. Use precise language; no hedging, no filler. Where relevant, explain what it means for M&A activity, competitive positioning, or capital allocation.]

[Where a sector development is newsworthy (AI, fintech/crypto, vertical SaaS, restaurant tech), include it. If there is no material sector development, omit the subsection entirely — do not force it.]

**SOURCE RULE — STRICTLY ENFORCED**: Executive Summary may only draw from: Money Stuff, Axios Pro Rata, StrictlyVC, Term Sheet, DealBook, Linas's Newsletter, Benedict Evans. Tomasz Tunguz content never appears here — he has a dedicated section. Sacra content (if present) never appears here either.

---

## Deal Flow Summary

### Major M&A Transactions

*[Leave this section empty with a one-line note if no qualifying M&A was reported today.]*

For each qualifying deal, write in prose (not bullets):

(1) **Deal overview**: parties, amount, structure, and key metrics
(2) **Sector**: AI/ML, Fintech/Crypto, Vertical SaaS, Restaurant Tech, or Other
(3) **Expert commentary**: any analyst or newsletter author perspective
(4) **Corp Dev / Strategy Lens**: strategic rationale, implied valuation comps, competitive landscape implications, and what this signals for the broader M&A environment in this sector

[Link to source coverage]

---

### Funding Rounds Summary

**SOURCE RULE**: Only draw from Axios Pro Rata, StrictlyVC, DealBook, Linas's Newsletter. Never include rounds sourced only from Term Sheet, Money Stuff, Tomasz Tunguz, or Benedict Evans.

Format each round as a detailed bullet:

- **Company Name** — $Amount Series/Stage | Post-Money Valuation: $X | Lead Investor: [Name] | Other Investors: [Names] | [Full company description: business model, target market, key differentiators, technology platform, current traction metrics] | [Coverage: Source Name](URL)

Write descriptions with enough specificity that the reader can evaluate the company's strategic relevance without needing to click through. Include total raised if meaningful context.

---

## Money Stuff: Key Threads (Matt Levine)

- **Main Theme**: [The column's central topic(s) — be specific, not generic]
- **Key Insights**: [3–4 substantive bullet points capturing Levine's primary observations — these should reflect his actual analytical framing, not just what the stories were about]
- **Levine's Take**: [His distinctive viewpoint, framing, or wit — the thing that makes Money Stuff worth reading. Quote selectively and paraphrase the rest]
- **Practical Implications**: [How his analysis maps to current market conditions — what should a corp dev exec take away?]

*This section remains empty if Money Stuff was not received.*

---

## Benedict Evans Analysis

- **Main Theme**: [Key topics of the edition]
- **Key Insights**: [3–4 bullet points on tech trends, market dynamics, and strategic implications — Evans-specific framing]
- **Evans' Take**: [His distinctive angle or contrarian observation]
- **Practical Implications**: [Application to current market conditions and strategic planning]

*This section remains empty if Benedict Evans was not received.*

---

## Tomasz Tunguz Analysis

- **Main Theme**: [Key topics — be specific to the actual post]
- **Key Insights**: [3–4 bullet points capturing his primary observations and data points]
- **Tunguz's Take**: [His framing or thesis statement — the investment or market insight he's building toward]
- **Practical Implications**: [How his analysis applies to current market conditions — especially for AI, enterprise SaaS, or VC market dynamics]

*This section remains empty if Tomasz Tunguz was not received.*

---

## Linas's Newsletter Analysis

- **Main Theme**: [Key topics — typically fintech, AI infrastructure, or emerging market dynamics. Note if today's edition is a tutorial or deep-dive rather than a news/analysis edition]
- **Key Insights**: [3–4 bullet points — Linas's distinctive fintech/AI analytical lens]
- **Linas's Take**: [His core thesis or most pointed observation]
- **Practical Implications**: [Application to current market conditions — especially fintech, AI, and vertical SaaS]

*This section remains empty if Linas's Newsletter was not received, or if today's edition contained no market intelligence content (e.g., a tutorial or how-to edition).*

---

## Tomorrow's Watch List

[4–6 items. Each is a specific signal, story thread, or developing situation worth tracking in the next 24–48 hours. Be concrete: name the company, deal, or dynamic to watch, and say what to look for. Not generic "watch AI" — specific: "Watch for Cohere deal terms; Axios flagged a valuation story today but body was unavailable."]

---

## Source Coverage

| Source | Status | Notes |
|---|---|---|
| Money Stuff (Bloomberg / Matt Levine) | ✅ Received / ❌ Not received | [Edition date, any body loading issues] |
| Axios Pro Rata | ✅ Received / ❌ Not received | [Author, subject line, any body loading issues] |
| StrictlyVC (Connie Loizos) | ✅ Received / ❌ Not received | [Notes] |
| Term Sheet (Fortune) | ✅ Received / ❌ Not received | [Notes] |
| DealBook (NYT) | ✅ Received / ❌ Not received | [Snippet-only or full content] |
| Linas's Newsletter (Linas Beliūnas) | ✅ Received / ❌ Not received | [Edition type: news vs. tutorial] |
| Tomasz Tunguz (Theory Ventures) | ✅ Received / ❌ Not received | [Notes] |
| Benedict Evans | ✅ Received / ❌ Not received | [Notes — irregular cadence is expected] |

**Coverage: X of 8 sources** | [One sentence on what's missing and whether it's a confirmed non-publish or a possible search failure]
```

---

## Step 6: Save the File

Detect the environment and save accordingly:

**Claude.ai (web or app interface — default):**
Save to: `/mnt/user-data/outputs/Daily Briefing - [Month Day, Year].md`
Then present the file to the user using the `present_files` tool.

**Claude Cowork (desktop app):**
Save to: `/Users/james.han/Desktop/AI/Claude Cowork Projects/Daily Briefing/Daily briefing/Daily Briefing - [Month Day, Year].md`
Then provide a direct clickable link in this format:
`[View Daily Briefing](computer:///Users/james.han/Desktop/AI/Claude Cowork Projects/Daily Briefing/Daily briefing/Daily Briefing - [Month Day, Year].md)`

**How to detect the environment:**
- If the `present_files` tool is available → Claude.ai. Use `/mnt/user-data/outputs/`.
- If `present_files` is not available but the local filesystem is accessible → Cowork. Use the desktop path.
- If uncertain, default to `/mnt/user-data/outputs/` and note the save location to the user.

---

## Quality Controls

**Source discipline (non-negotiable):**
- Executive Summary: Money Stuff, Axios Pro Rata, StrictlyVC, Term Sheet, DealBook, Linas's Newsletter, Benedict Evans only
- Funding Rounds: Axios Pro Rata, StrictlyVC, DealBook, Linas's Newsletter only
- Tomasz Tunguz: dedicated section only, never Executive Summary or Funding Rounds
- No duplication: if a story appears in multiple newsletters, synthesize into one entry crediting all sources

**Editorial standards:**
- Every deal section must include the Corp Dev / Strategy Lens — this is non-negotiable
- Funding round descriptions must be substantive enough to evaluate without clicking through
- Tomorrow's Watch List must be specific and actionable, not generic
- Flag knowledge gaps explicitly (e.g., "Axios body unavailable — Cohere story referenced in subject line, follow up")
- Linas tutorial/how-to editions: note the edition type and skip the analysis section if there's no market intelligence content
- Distinguish "not published" from "search failure" for any missing source — run sender fallback before concluding

**Monday coverage:**
- Use `newer_than:3d` to capture Friday deal flow editions
- Note in Source Coverage that reduced weekend publishing is expected behavior, not a failure
