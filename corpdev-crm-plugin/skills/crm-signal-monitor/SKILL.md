---
name: crm-signal-monitor
description: >
  Proactively scans for new signals about companies in the CorpDev CRM and surfaces a digest for James to review before writing anything to Notion. Use this skill when James says "run signal monitor", "scan for new signals", "check for CRM updates", "what's new with our pipeline", "any news on our companies", "run the monitor", or "update the CRM with latest signals". Also trigger after a daily briefing run (in case briefing news overlaps with CRM companies), after startup-meeting-manager logs a meeting (to check if related companies have fresh signals), or whenever James wants a periodic intelligence sweep without adding a specific company. Scans internal signals (Corp Dev Spaces, Slack, Gmail), PitchBook, curated newsletters/media, and top VC sources. Always presents a digest for James to approve before writing touchpoint entries or updating CRM fields.
---
 
# CRM Signal Monitor
 
Aggregates intelligence signals about CorpDev CRM companies, presents a digest for James to review, and — after approval — hands off structured updates to the crm-add-enrich skill, which owns all Notion writes.
 
**Signal-monitor aggregates. crm-add-enrich writes.** These two skills are complementary: signal-monitor scans sources and surfaces what's new; crm-add-enrich validates, enriches, and executes CRM updates.
 
**Nothing is written to Notion without James's approval.** This is a scan → review → hand-off workflow.
 
---
 
## Inputs
 
James triggers this with minimal input. Optional overrides:
- **Lookback window**: how far back to scan (default: 7 days; "last 30 days" extends it)
- **Priority filter**: default scans P0/P1 deeply + P2 lightly; "P1 only" narrows; "all" includes P3
- **Company filter**: "check signals for HiAuto" focuses on one company
---
 
## Phase 1: Load the Company Watch List (CRM reference only)
 
Query the CorpDev CRM Notion database to build the watch list. This is purely a reference step — the CRM is not a signal source.
 
For each active company, pull:
- Company name (exact string — used as search query across all signal sources)
- Primary contact name / CEO first name (loaded for disambiguation only — used to clarify which company a Slack message refers to when the company name isn't mentioned directly; not used as an independent search term)
- Priority (P0 / P1 / P2 / P3)
- Notion page ID (for writing approved updates later)
- Last Updated date (signals predating this are likely already logged — skip them; if Last Updated is null or missing, scan the full lookback window)
- Current Traction, Product/Tech, and Team scores (needed for score update proposals — load the score rubric from crm-add-enrich/SKILL.md before proposing any score changes so you know what each point level represents)
Build two lists:
- **P0/P1 list** → deep scan: all signal sources
- **P2 list** → light scan: internal signals + headlines only (no PitchBook cold query)
- P3 companies: skip unless James explicitly includes them
**Watch list completeness:** A single keyword search will miss companies with short or ambiguous names (e.g., "Qu", "Layer" → LayerFi, "Karma"). Run multiple diverse search passes — by category, sub-category, pillar, and status — and cross-check the total count against prior scans. If the count seems low, run additional passes before proceeding. Presenting a digest that silently omits P0 companies is worse than taking extra time to build the list correctly.
 
---
 
## Phase 2: Internal Signals
 
Internal signals are highest priority — they reflect direct deal engagement that no external source will have. Run all three sub-phases; each is independent.
 
**Relationship to startup-meeting-manager and the weekly meeting recap:** startup-meeting-manager owns the meeting lifecycle — it creates Drive notes, posts Slack recaps, and logs touchpoints to Notion. The weekly meeting recap task covers all direct interactions for the week. The CRM's Last Updated date reflects that work. The default assumption should be: if a direct meeting, call, or substantive interaction with a company occurred this week, it is already captured. Phase 2 looks for what happens in the gaps — between meetings, outside formal workflows, and in sources the recap doesn't touch. It catches: (a) between-meeting signals that never went through a formal meeting flow, (b) internal diligence docs that aren't meeting transcripts, and (c) signals newer than the CRM's Last Updated date that haven't yet been logged. Skip any signal whose date predates the Last Updated date for that company in the CRM.
 
### 2a. Corp Dev Spaces (Google Drive)
 
Search for recently modified docs in the Corp Dev Spaces folder whose content mentions CRM company names. Naming conventions vary — don't rely on an exact filename pattern. The goal is to find diligence notes, internal analyses, or meeting recaps created or updated within the lookback window that contain new signal not yet reflected in the CRM.
 
**Search strategy:**
- Search for docs modified within the lookback window in Corp Dev Spaces
- Cross-reference content or filename against CRM company names
- When in doubt, open the doc and skim it — a meeting note will have dates, attendees, and conversation content; a static doc will read like a tracker or summary report
**Read and extract from docs that look like meeting notes or call recaps:**
- Traction mentions (revenue, growth, customer counts)
- Product and tech impressions
- Team observations
- Next steps and deal context
- Source label: `Corp Dev Spaces — [filename] ([date modified])`
**Skip docs that are clearly static or aggregate in nature** — pipeline trackers, monthly/quarterly update spreadsheets, board decks, or any doc that reads as a manually maintained summary rather than notes from an actual conversation. The Corp Dev Monthly Updates doc is a specific example of what to skip; wait for meeting transcription notes instead.
 
If Drive is unavailable, skip and note the gap.
 
### 2b. Slack — Senior Leader DMs
 
Search Slack DMs for mentions of CRM company names, filtered to high-signal conversations only.
 
**Who to search:**
- **James's own DM (self-DM):** startup-meeting-manager automatically posts meeting notes here after every logged meeting. Check it as a backstop — if a note appeared after the CRM's Last Updated date for a company, that's a signal worth surfacing if not yet logged.
- **DMs and group DMs with colleagues who have "VP", "SVP", or "Chief" in their Slack title** — this is the primary net-new source vs. startup-meeting-manager. Exec endorsements ("Brewer seems very bullish"), strategic conversations about deal conviction, internal alignment discussions — none of these go through startup-meeting-manager. Use `slack_search_users` or `slack_read_user_profile` to identify these colleagues before searching, then search DMs with them specifically.
- **Relevant corp dev or strategy channels** if accessible.
**Search strategy:** Look up user profiles to identify VP/SVP/Chief colleagues first. Then batch 8–10 CRM company names per query scoped to those DMs. Use `slack_search_public_and_private`.
 
Contact names are loaded in Phase 1 but used only as a disambiguation aid when a message references a name without the company name and it's otherwise unclear which company it relates to — not as independent search terms. Searching by first name alone ("Amir", "Botty") will surface every internal Toast conversation mentioning that person, most of which won't be CRM-relevant signals.
 
**Extract:** executive assessments of a company (product, team, deal conviction), direct founder interactions outside a formal meeting, and deal developments surfaced in Slack that aren't already reflected in the CRM.
 
- Source label: `Slack — [channel or DM participants] ([date])`
**Exclude:** general product/ops conversations that mention a company name incidentally (e.g., MarketMan mentioned as a customer's current vendor in a support channel). New companies not in the CRM are handled by startup-meeting-manager — do not flag them here.
 
### 2c. Gmail — Deal-Related Threads
 
Search James's Gmail inbox for deal-relevant email threads mentioning CRM companies.
 
**What belongs here:** inbound outreach from founders of CRM companies, internal deal emails (shared briefings, cap table requests, diligence coordination), founder follow-ups, and deal-relevant correspondence from colleagues — for companies already in the CRM watch list only.
 
**Explicitly exclude:** Fireflies, Otter, or any other meeting-recorder delivery emails. Meeting capture is startup-meeting-manager's job — by the time signal-monitor runs, those meetings should already be reflected in Corp Dev Spaces (Phase 2a). Don't double-count them here.
 
**Search strategy:** Batch company names into groups of 10–12:
```
"Company A" OR "Company B" OR "Company C" ... newer_than:7d
```
Read full thread content (`messageFormat: FULL_CONTENT`) — snippets miss deal context.
 
- Source label: `Gmail — [sender domain] ([date])`
- Skip threads where the company name appears only in a footer, signature, newsletter ad, or unrelated product context
---
 
## Phase 3: PitchBook
 
For each **P0/P1 company**, query PitchBook for changes since the CRM's Last Updated date:
- Total funding raised (flag if increased)
- Most recent round: amount, stage, lead investors, close date
- New investors added to the cap table
- Last valuation (flag if changed)
Only surface a signal if something has **changed** vs. what's already in the CRM. Don't re-report known funding.
 
If PitchBook returns no data, note "No PitchBook data" and continue — expected for early-stage companies.
 
If PitchBook MCP is unavailable, skip this phase entirely and note the gap.
 
---
 
## Phase 4: Curated Newsletters & Media
 
Don't run a generic web search. Instead, search specifically within a curated list of high-signal sources that cover tech deals, venture, and restaurant/hospitality tech.
 
### 4a. Newsletter & Publication Sources
 
Search for CRM company name mentions across these sources (web search with `site:` filters or targeted queries):
 
**Deal/venture newsletters:**
- Axios Pro Rata (Dan Primack / Lucinda Shen) — axios.com/pro-rata
- Money Stuff (Matt Levine) — bloomberg.com
- StrictlyVC (Connie Loizos) — strictlyvc.com
- Term Sheet (Fortune) — fortune.com/term-sheet
- DealBook (NYT) — nytimes.com/dealbook
- Linas's Newsletter — linasfintech.substack.com or equivalent
- Tomasz Tunguz — tomtunguz.com
- Benedict Evans — ben-evans.com
**Tier-1 press:**
- WSJ — wsj.com
- Financial Times — ft.com
- TechCrunch — techcrunch.com
- The Information — theinformation.com
**Search strategy:** For each P0/P1 company, run:
```
"[Company Name]" site:axios.com OR site:fortune.com OR site:wsj.com OR site:ft.com OR site:techcrunch.com
```
And a separate pass for the newsletter sources. You don't need to run every source individually — batch them.
 
For P2 companies: only search if they already surfaced in Phase 2. Don't cold-search every P2 company.
 
### 4b. VC Portfolio & Blog Sources
 
Top-tier VCs announce portfolio news (new investments, rounds, exits) on their own sites and social feeds. Search for CRM company mentions across:
 
**Primary VC sources:**
a16z, Sequoia, Menlo Ventures, Altimeter, Tiger Global, Lightspeed, Index Ventures, Benchmark, Khosla Ventures, General Catalyst, NEA, Accel, Y Combinator, Lead Edge Capital, Bessemer Venture Partners
 
**What to look for:**
- A VC's portfolio page or blog post announcing a new investment in a CRM company
- A VC announcing a follow-on round for a company in the CRM
- A VC's sector thesis piece that references a CRM company as an example
**Search strategy:**
```
"[Company Name]" site:a16z.com OR site:sequoiacap.com OR site:khoslaventures.com ...
```
Or run a general search: `"[Company Name]" investment round [VC firm names]`
 
Source label: `VC — [firm name] ([publication/post type], [date])`
 
### 4c. General Web Search (catch-all, low priority)
 
After running the curated sources above, do a broad web search for any P0/P1 company that hasn't yet surfaced a signal. This is a backstop — not a primary filter — and should be weighted below curated sources.
 
```
"[Company Name]" funding OR acquisition OR partnership OR launch
```
 
Be selective: only surface results from credible outlets. Treat blog spam, SEO content farms, and low-authority press releases as noise — skip them. If a result appears in both a curated source and a general search, cite the curated source.
 
Source label: `Web — [publication] ([date])`
 
---
 
## Phase 4.5: Cross-Reference Against startup-meeting-manager
 
Before presenting the digest, check what startup-meeting-manager and the weekly meeting recap have already captured for the same lookback window. This is what makes the digest actionable — it shows James only what he doesn't already have.
 
**What to check (in parallel):**
- **Slack self-DM** — search for startup-meeting-manager recap posts in the lookback window. These are formatted as `:clipboard: [Company] — [Meeting Type] | [Date]`. Each one represents a meeting that was already logged to Corp Dev Spaces and Notion.
- **Notion Touchpoint Log** — for each company with signals, check whether a touchpoint already exists with a date inside the lookback window. If yes, the meeting is already logged.
- **Corp Dev Spaces (Drive)** — meeting notes docs created within the lookback window are likely startup-meeting-manager outputs. These are already known.
**Classify each signal as one of three tags:**
 
- `[ALREADY CAPTURED]` — startup-meeting-manager or the weekly recap already logged this. A self-DM recap was posted, a Notion touchpoint exists, or the Drive meeting note was created by that workflow. James already knows this.
- `[NET NEW]` — this signal comes from a source startup-meeting-manager doesn't cover: VP/Chief DMs, Gmail deal threads not tied to a logged meeting, external press/VC sources, Drive docs that aren't meeting transcripts, or PitchBook data. James does not already have this.
- `[SUPPLEMENTS]` — startup-meeting-manager captured the meeting itself, but this signal adds context that wasn't in the meeting note. Example: an exec endorsement Slack message posted after the meeting, or a funding signal discovered via web search for a company James met this week.
**If startup-meeting-manager has not run this week** (no self-DM posts found), note that and treat all signals as potentially net-new.
 
---
 
## Phase 5: Consolidate & Present Digest
 
After all sources are scanned and classified, present the digest with net-new signals leading. The default view prioritises what James doesn't already know from his meeting workflows.
 
**Signal bar — only surface signals that clear this threshold:**
 
A signal is worth surfacing if it represents one of:
- A **direct interaction with the company** — a call, meeting, or substantive email exchange with a founder or their team
- A **material external event** — new funding round, acquisition, or major product launch confirmed by a credible external source
- A **formal deal decision** — a company is passed, advanced to diligence, or a LOI/term sheet moment occurs
These three categories have different tests:
 
**For interaction-based signals (internal Slack, Gmail, Drive)** — both must be true:
1. Was there an actual meaningful new touchpoint with the target — a real interaction with the company, not just an internal discussion about them?
2. Would a future reader of the CRM log learn something meaningful from this entry that they couldn't infer from context?
If either answer is no, skip it.
 
**For external material events (PitchBook, press, VC sources)** — the bar is: does the event materially change what someone reading the CRM would know about this company's trajectory? A new funding round clears the bar. A name-drop in a newsletter think-piece does not.
 
**Drop everything else:**
- **Internal Toast discussions about a company** — if James and a colleague are discussing a company but the company/founder isn't in the conversation, that's internal context, not a CRM touchpoint. Even if it's high-signal internally (e.g. an exec alignment discussion), don't log it as an external touchpoint.
- **Administrative and legal steps** — MNDA execution, NDA signing, scheduling coordination, calendar logistics. These are process steps, not meaningful interactions worth logging on their own.
- **Future scheduled meetings** — a meeting that has been arranged but not yet taken place is not a touchpoint. Log it only after it occurs.
- **Name mentions without an interaction** — a founder's name appearing in a Slack thread doesn't mean a meeting occurred. Only surface if an actual interaction happened.
- **Already logged** — signals already in the Touchpoint Log since the CRM's Last Updated date
- **Low-credibility web sources** — content farms, SEO blogs, unattributed wire pickups
- **Meeting-recorder emails** — handled upstream by startup-meeting-manager
**For each company with actionable signals, present the proposed CRM write directly** — not raw signal bullets. The output should show exactly what will land in Notion if James approves, written as it would appear in the CRM. Include the NET NEW / SUPPLEMENTS / ALREADY CAPTURED classification only on the signals that are supplements or already captured, to give context; net-new signals need no tag since they're the default.
 
```
[Company Name] · [P0/P1]
Action: [new touchpoint / append to existing [date] touchpoint / field updates only / no write needed]
 
Touchpoint: [YYYY-MM-DD] — [Exact text as it would appear in the Notion Touchpoint Log note. Written as a senior analyst briefing an exec — terse, direct, insight-led. No em dashes. State what happened, key takeaway, next step if any.]
[Traction / Product/Tech / Team] score: [current] → [proposed]. Rationale: [one line citing specific signals.]
 
[Field name]: [current value] → [proposed value]
[Field name]: [current value] → [proposed value]
```
 
**Format rules:**
- Lead with the action type so James knows immediately what each entry requires
- Write the touchpoint note as it will appear in Notion — not as a description of what to write
- Score updates go directly under the touchpoint, not as a separate section
- Field updates (funding, investors, status) listed line by line with [current] → [proposed]
- If action is "append to existing touchpoint", name the original entry date so it's unambiguous
- If all signals for a company are ALREADY CAPTURED, show the company with "Action: no write needed — already logged by startup-meeting-manager" and skip the touchpoint block
- One entry per company — combine all signals into a single touchpoint note
**At the end, show the net-new summary then approval prompt:**
 
```
NET-NEW SUMMARY
  Already captured by weekly recap: [N] signals
  Net-new: [N] signals
  Supplements: [N] signals
 
  Key net-new value this scan:
  • [Most important signal James wouldn't have had otherwise]
  • [Second most important]
 
[N] actions total: [brief description, e.g. "2 new touchpoints, 1 append, 2 field enrichments"]
Sources scanned: [list] | Sources unavailable: [list if any]
 
Say "write all", "skip [company]", or edit anything above and I'll pass to crm-add-enrich.
```
 
If signal volume is high (>15 companies with signals), present a summary-only view first and ask whether to proceed with all or filter by priority.
 
---
 
## Phase 6: Hand Off to crm-add-enrich (after James's approval)
 
Signal-monitor aggregates and surfaces intelligence. crm-add-enrich owns all writes to Notion. After James approves the digest, signal-monitor's job is to package the approved signals into structured inputs for crm-add-enrich and trigger that skill.
 
James replies with one of:
- **"write all"** → hand off every proposed action to crm-add-enrich
- **"skip [company]"** → exclude that company, hand off the rest
- **"write [company] only"** → hand off only that company
- Inline edits → apply them to the signal summaries before handing off
**Handoff format — for each approved company, pass to crm-add-enrich:**
 
*For existing companies (touchpoint + field updates):*
```
Company: [Company Name]
Action: update existing record
Touchpoint: [YYYY-MM-DD] — Signal via [source]: [summary]
Field updates:
  - [Field name]: [current value] → [new value] (source: [attribution])
Score proposals:
  - [Traction/Product/Team]: [current] → [proposed] — [one-line rationale]
```
 
crm-add-enrich will handle taxonomy validation, duplicate checking, enrichment, and the actual Notion write. Signal-monitor does not write to Notion directly.
 
**After crm-add-enrich completes, confirm:**
```
✓ crm-add-enrich updated [N] existing records, added [N] new companies
  Companies updated: [names]
```
 
---
 
## Score Update Logic
 
If a signal contains meaningful traction, product, or team data, propose a score update alongside the touchpoint entry. Don't update scores silently — always show `[current score] → [proposed score]` with a one-line rationale for James to confirm. Current scores are loaded in Phase 1.
 
**Propose a Traction score update when:**
- A new funding round is announced
- A published revenue figure is materially above or below what the current score implies
- A growth rate is mentioned that would shift the score by ≥1 point
**Propose a Product/Tech score update when:**
- Internal meeting notes, demo recaps, or diligence docs contain strong qualitative assessments of the product or technology that differ materially from the current score (e.g. "the vision AI pipeline is significantly more mature than competitors" or "the core product is thin — it's essentially a wrapper")
- A major external product launch, architectural pivot, or significant acquisition that changes competitive positioning — but weight internal diligence observations more heavily than press coverage
- **Magnitude bar:** a single passing comment isn't enough. Look for a clear strong statement from a senior exec, or consistent assessments across multiple people or sources in the same scan cycle.
**Propose a Team score update when:**
- Internal meeting notes, Slack discussions, or diligence docs contain strong qualitative assessments of the founding or leadership team that differ from the current score (e.g. "the engineering team depth is exceptional" or "the CEO struggled to answer basic questions about unit economics")
- These internal impressions are the primary driver — external events like a C-suite hire/departure are secondary and only worth flagging if they're at the CEO or CTO level
- **Magnitude bar:** same as Product/Tech — require a clear strong statement from a senior exec, or consistent positive/negative assessments across multiple people or sources. A single exec saying "I liked them" in Slack is not sufficient on its own.
The score rubric lives in `crm-add-enrich/SKILL.md` for reference. Load it in Phase 1 before proposing any score changes.
 
---
 
## Scope: Existing CRM Companies Only
 
This skill monitors signals for companies already in the CRM watch list. It does not discover or flag new companies.
 
New company discovery is startup-meeting-manager's responsibility — when James has a meeting with a company not yet in the CRM, startup-meeting-manager logs it and triggers crm-add-enrich to create the record. Signal-monitor picks them up on the next scan cycle once they're in the CRM.
 
---
 
## Failure Modes
 
| Issue | Response |
|---|---|
| Surfacing meetings already covered by the weekly recap | This is the most common failure mode. The weekly meeting recap task covers all direct company interactions. Default to "already captured" for any signal tied to a meeting, call, or scheduled interaction. Only surface as net-new if you can confirm no startup-meeting-manager recap exists for that company in the lookback window AND the signal isn't already reflected in the CRM's Last Updated date. |
| Surfacing internal Toast discussions as touchpoints | If James and a colleague are discussing a company but no company representative is in the thread, it is internal context — not a CRM touchpoint. This applies even to high-signal discussions like exec alignment, acquihire framing, or deal conviction conversations. These are not external interactions and should not be proposed as Notion writes. |
| Slack search returns 0 results | Try narrower queries (individual company names). If still empty, note "No Slack signals found" and continue. |
| Drive unavailable | Skip Phase 2a, note the gap. |
| Gmail returns 0 results | Try smaller batches (5–6 names). If still empty, note and continue. |
| PitchBook unavailable | Skip Phase 3, note the gap. |
| VC/newsletter search returns no results for a company | Expected — most companies won't appear. Skip silently. |
| All sources empty for a company | Don't create an empty touchpoint. Skip silently. |
| crm-add-enrich handoff fails | Surface the error. Show the structured handoff text so James can trigger crm-add-enrich manually with it. |
| Signal volume > 15 companies | Show summary-only first. Ask whether to proceed with all or filter. |
| 0 signals found across all companies | Show a clean summary: "Signal scan complete — [date range]. No new signals found for [N] companies scanned. Sources checked: [list]." Do not present an empty digest. |
 
---
 
## Notes
 
- James's timezone is America/Los_Angeles. All dates in the digest and Notion entries use his local date.
- This skill monitors existing CRM companies only. It does not discover or flag new companies — that is startup-meeting-manager's job.
- Don't surface signals dated before the CRM's Last Updated date for that company — they've likely already been accounted for. If Last Updated is null, scan the full lookback window.
- The goal is signal efficiency, not signal volume. Three specific, sourced signals beat ten vague ones.
- Gmail (Phase 2c) covers deal-related threads: inbound outreach, legal notices, internal deal emails. Meeting-recorder emails (Fireflies, Otter) are explicitly excluded — those belong to startup-meeting-manager, which writes its output to Corp Dev Spaces (Phase 2a).
- General web search (Phase 4c) is a backstop only. Curated newsletter and VC sources (4a/4b) should be checked first and weighted higher. When in doubt about a web source's credibility, skip it.
 