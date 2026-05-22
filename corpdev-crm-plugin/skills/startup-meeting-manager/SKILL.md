---
name: crm-startup-meeting-manager
description: Use this skill to automate the full lifecycle of startup meetings for corp dev workflows. Triggers whenever the user mentions an upcoming startup meeting, wants to prep for a pitch/intro/diligence call, needs to process notes or a transcript from a startup conversation, or wants to log a startup interaction to CRM. Also use proactively when the user mentions deal flow, corp dev, founders, startup outreach, Google Meet recap, or "log this meeting." Handles calendar detection, Notion CRM lookup, Google Drive note creation in Corp Dev Spaces, Google Meet transcript retrieval, Slack DM recap, and Notion CorpDev CRM touchpoint logging end-to-end.
---
 
# Startup Meeting Manager
 
Automates the end-to-end workflow for startup interactions: detect meetings from calendar, check the Notion CRM to determine if the company is already known, retrieve transcripts, generate a summary, send a Slack recap, and log to the Notion CorpDev CRM.
 
For **known companies** (already in the CRM): use the linked Drive doc and skip web research entirely — go straight to transcript retrieval, summary, and CRM update.
 
For **new companies** (not in the CRM): do web research to classify, create a Corp Dev Spaces doc, then proceed with transcript, summary, and Slack — and flag to James to add the company via `crm-add-enrich`.
 
This skill has two phases you can run independently or together:
- **Pre-meeting**: Detect the meeting → CRM lookup → (if new: classify + Drive doc) → Slack prep message
- **Post-meeting**: CRM lookup → retrieve transcript → summarize → Slack recap → log to Notion CRM
---
 
## Phase 1: Detect the Meeting
 
### Step 1: Scan the Calendar
 
Use the Google Calendar MCP to look at upcoming events (next 7 days) or recent events (past 48 hours for post-meeting runs).
 
**Flag an event as a candidate startup meeting** when at least one attendee has an email domain that is NOT `@toasttab.com`.
 
**Extracting the company name:**
- Take the external attendee's email domain (e.g., `sprout.ai`) → strip the TLD → title-case the result (e.g., `Sprout`)
- If multiple external domains are present, list each as a separate candidate
- Prefer the domain that most closely matches the event title if there's any overlap
**Always exclude — do not process these meetings:**
- Banks, investment banks, and financial advisors (e.g., William Blair, Goldman Sachs, JPMorgan)
- Executive search and recruiting firms (e.g., Heidrick & Struggles, Spencer Stuart, Korn Ferry)
- Meetings originating from personal email accounts (e.g., james.jw.han@gmail.com) — only process work calendar meetings (james.han@toasttab.com)
---
 
## Phase 2: CRM Lookup — Known vs. New Company
 
### Step 2: Check the Notion CorpDev CRM
 
**CorpDev CRM data source ID:** `3b8058a353a98326b290013effbd3bf9`
**Touchpoint Log data source ID:** `c6d058a353a982c697800119ddda8be3`
 
Search the CorpDev CRM for a record matching the company name (case-insensitive, fuzzy match).
 
### If the company IS in the CRM → Known Company path
 
- Retrieve the existing CRM record: Status, Contact, Contact Role, Docs Linked, Priority, Construct
- Use the `Docs Linked` URL as the notes doc — **do not search Drive for a doc or create a new one**
- Skip web research entirely — the CRM record already provides the context you need
- Proceed directly to **Phase 4: Transcript Retrieval**
### If the company is NOT in the CRM → New Company path
 
- Proceed to Step 3 (web research) and Step 4 (Drive doc creation)
- After the Slack DM is sent, flag to James:
  > "[Company Name] isn't in the CorpDev CRM yet. Want me to add it? If so, tell me the Pillar > Category > Sub-category and Construct, and I'll add it via `crm-add-enrich`."
- Do not create the CRM company record automatically — that requires James to confirm taxonomy
---
 
## Phase 3: New Companies Only — Classify & Create Drive Doc
 
*Skip this phase entirely if the company was found in the CRM.*
 
### Step 3: Classify via Web Research
 
Use WebSearch to learn about the company. Search for: `"[Company Name]" company`
 
From the results, extract:
- **What they do** (1–2 sentences)
- **Industry / vertical** (e.g., Restaurant Tech, FinTech, SaaS, Marketplace, Healthcare, Logistics)
- **Funding stage or company maturity** (Seed / Series A/B/C / established / public)
- **Notable investors or customers** — if findable
- **Relationship type** — classify as one of:
  - **New Intro**: First or early meeting with a startup or growth-stage company
  - **Existing Relationship**: Board seat, active investment, or ongoing engagement
  - **Partner / Integration**: Established company meeting for a product, integration, or commercial discussion
  - **Other External**: Anything that doesn't fit the above (e.g., advisor, recruiter, consultant)
**Exception:** If the company is a large enterprise with no plausible corp dev relevance (e.g., a Salesforce AE, a law firm, a benefits vendor), flag it to James and ask whether to include it before proceeding.
 
### Step 4: Create the Corp Dev Spaces Notes Doc
 
Access the **Corp Dev Spaces** folder in Google Drive.
 
**Subfolder routing:** Based on the company's industry/vertical from Step 3, find a matching subfolder inside Corp Dev Spaces (e.g., "Restaurant Tech", "FinTech", "SaaS", "Marketplace", "Healthcare", "Partners", "Other").
- If a matching subfolder exists → use it
- If no clear match → suggest the most appropriate name to James, confirm, then create it
- For **Partner / Integration** meetings, prefer a "Partners" subfolder if one exists
**Company folder:** Within the vertical subfolder, look for an existing folder named `[Company Name]`. If not found, create it.
 
**Before creating a new doc:** search Drive for any existing doc with `[Company Name]` and "Notes" in the title. If found, treat it as an existing doc (Case A) — don't create a new one.
 
**Case A: Doc already exists**
- Do not create or modify the existing doc
- **Pre-meeting**: surface the doc link in the Slack prep message
- **Post-meeting**: send a Slack DM with a ready-to-paste notes block populated from the transcript, with a direct link to the doc
**Case B: No doc exists**
Create a new Google Doc named `[Company Name] — Corp Dev Notes` in the appropriate subfolder:
 
```
[Company] — Corp Dev Notes
First contact: [Date]
Relationship type: [New Intro / Existing Relationship / Partner / Other External]
 
About [Company]
[2–3 sentences from web research]
Notable wins, funding rounds, product updates, news. Total raised, est. valuation, notable investors. Founder backgrounds.
 
========================================
[Date] — [Meeting Type]
========================================
 
Attendees
  Toast: ...
  [Company]: ...
 
Meeting Notes
  [From transcript]
 
Key Themes & Questions
  [Main topics]
 
Next Steps
  [Who does what by when]
 
Investment / Partnership Thesis
  [Your take — fill after the call]
```
 
---
 
## Phase 4: Transcript Retrieval
 
### Step 5: Retrieve the Transcript
 
The transcript is the primary source for meeting notes — it takes priority over Drive doc notes, which are supplementary context only.
 
**Source priority order:**
1. **Gmail transcript emails** — always search using `in:anywhere` to catch emails routed to Promotions, Updates, or other labels. Search for `[Company Name] in:anywhere` combined with known senders:
   - Loom / Atlassian Loom: `from:loom.com` or `from:e.atlassian.com`
   - Granola: `from:granola.ai`
   - Zoom: `from:zoom.us` with "Meeting assets" or "transcript" in subject
   - Otter.ai: `from:otter.ai`
   - Fireflies: `from:fireflies.ai`
   - Google Meet: transcript saved to Drive (see below)
2. **Google Drive** — search for files where:
   - Filename contains `[Company Name]` OR the calendar event title
   - Modified within the last 48 hours
   - File type: Google Doc or .docx
   - Primary location for Meet transcripts: `My Drive > Meet Recordings > [Meeting title] — [Date]`
**If a transcript is found:**
- Use it as the primary source for the Slack summary
- Extract and clean the content — strip timestamps, system messages, and crosstalk
- Supplement with any additional context from the Drive doc notes
**If no transcript is found:**
- Fall back to the Drive doc notes (for known companies) or any other available context
- If neither exists, note `[Transcript not found — add meeting notes manually]` in the Slack DM
- Continue with CRM logging using whatever is available
**Transcript storage** (post-meeting, when a transcript is found):
1. Raw transcript stays in its source location — do not move or delete it
2. Extract and clean the substantive content
3. Append the cleaned content into the **Meeting Notes** section of the current dated block in the company's notes doc
4. Add a reference line at the top of that block: `Raw transcript: [link to source]`
---
 
## Phase 5: Summary & Slack Notification
 
### Step 6: Generate a Summary
 
Write in the voice of a senior analyst briefing an exec. Lead with the strategic "so-what" for Toast, synthesize into key themes rather than mirroring the meeting agenda, and cut procedural detail that doesn't change the picture. Be opinionated — draw conclusions, not just observations. If a number was mentioned, include it; if it wasn't, don't speculate.
 
**Style principles:**
- Lead each theme with the insight, not the fact — "Hardware dependency is their moat, not friction" not "Solink requires hardware at every deployment"
- Organize into 2–4 key themes synthesized from the call, not by agenda topic
- Write next steps as owner → action, not as bullet observations
- The key notes section preserves the factual record; the themes section should make it unnecessary to read the notes to understand what matters
**Format:**
 
```
**📋 [Company] — [Meeting Type] | [Date]**
 
[3-sentence bottom-line: verdict or key development → key supporting reason → what's next. For new intros: Toast's strategic interest and urgency. For existing relationships: what happened, what moved, what's next.]
 
**[Theme 1 headline — insight-first, not topic-first]**
[2–4 sentences: the so-what for Toast, supporting facts, open question or implication]
 
**[Theme 2 headline]**
[2–4 sentences]
 
**[Theme 3 headline]** ← omit if not needed
[2–4 sentences]
 
─────────────────────
**Next steps**
→ [Owner]: [action]
→ [Owner]: [action]
 
─────────────────────
**Key notes from the call**
• [Most important factual bullet]
• [... include financials, pricing, metrics, competitive details — the raw record]
 
Notes: [doc link] · Transcript: [link or "unavailable"]
```
 
**Formatting rules for Slack:**
- Use `**text**` for bold (not `*text*`, which renders as italic in Slack)
- Use `─────────────────────` as a divider before Next steps and Key notes sections
- No horizontal rules or markdown headers (`###`) — Slack does not render these
### Step 7: Send Slack DM
 
Only send **after the meeting has occurred** and content (transcript or notes) is available. Do not send anything pre-meeting.
 
Send a **direct message to james.han@toasttab.com**.
 
---
 
## Phase 6: CRM Update
 
### Step 8: Log to the Notion CorpDev CRM
 
*Applies to all companies — known (immediately) and new (once James confirms taxonomy).*
 
#### 8a. Create a Touchpoint Log entry
 
| Field | Value |
|---|---|
| Entry | `YYYY-MM-DD — [Company Name]` (today's date, LA timezone) |
| Company | Relation to the CorpDev CRM company record |
| Date | Meeting date |
| Note | Structured CRM log in the following format — write as a top business analyst briefing a senior exec: terse, direct, no em dashes, no AI voice, no padding.<br><br>**Objective:** [One sentence on the purpose of the meeting]<br>- [Key insight or takeaway — 2 bullets max; omit entirely if no clear signal; do not force bullets]<br>- [Second insight only if genuinely warranted]<br><br>**Next step:** [Only if explicitly agreed in the meeting — omit if not]<br>*Summarized by Claude*<br><br>Signal quality guidelines: lead with strategic signal, not stats. Do not speculate or infer next steps. Do not mistake data points for insights. If only one strong takeaway exists, write one bullet. If none, write none. |
 
#### 8b. Update the company record
 
Update the company's `Last Updated` date to today (America/Los_Angeles).
 
If the meeting revealed new signals about Location, Contact, or Contact Role that differ from what's in the CRM, surface them to James: "The CRM has [X] for Contact — the call was with [Y]. Want me to update it?" Do not overwrite without confirmation.
 
If the company's `Docs Linked` field is blank and a Drive doc was found or created, update `Docs Linked` with the doc URL.
 
#### 8c. Confirm via Slack DM
 
Append to the post-meeting Slack DM:
 
```
─────────────────────
✅ Touchpoint logged to CorpDev CRM
[Link to Notion record]
```
 
If the company wasn't in the CRM:
```
⚠️ [Company Name] not in CRM — reply with taxonomy to add it.
```
 
---
 
## Error Handling
 
If any step fails, send a Slack DM with:
- ❌ Which step failed and why
- What to do manually to recover
- Links to anything successfully created
**Common failure scenarios:**
- *No transcript found* → "No transcript found for [Company Name] on [Date]. Notes doc is at [link] — add notes manually, then ask me to generate the summary and CRM entry."
- *Company not in CRM* → flag and ask James for taxonomy to add via `crm-add-enrich`
- *Corp Dev Spaces subfolder ambiguous* → "Found multiple possible subfolders for [vertical]. Which should I use: [list]?"
- *Large non-corp-dev-relevant enterprise* → "The external attendee is from [domain], which appears to be [e.g., a law firm / benefits vendor]. Want me to include it anyway?"
- *No external-attendee meetings detected* → "No external-attendee events found. Want to run this for a specific company? Just give me the name."
---
 
## How to Run This Skill
 
| What you say | What happens |
|---|---|
| "Prep for my call with Acme tomorrow" | Pre-meeting: CRM lookup → (if new: classify + Drive doc) → Slack prep |
| "Process my meeting with Acme from today" | Post-meeting: CRM lookup → transcript → summary → Slack → Notion CRM |
| "Check my calendar for external meetings this week" | Scans calendar, CRM lookup for each, flags all external-attendee events |
| "Log today's Solink meeting to CRM" | CRM lookup → CRM touchpoint using notes already in Drive |
| "Take notes on my Karma board meeting" | Full workflow — known company path since Karma is in CRM |
 
---
 
## Setting Up a Daily Schedule (Optional)
 
> "Schedule the startup meeting skill to run every weekday at 12pm."
 
Claude will use the `schedule` skill to configure this.
 
---
 
## Note on Google Meet Transcription
 
This skill retrieves transcripts but does not enable them. To enable:
 
1. In Google Meet, open the three-dot menu → "Record meeting" (transcript is generated alongside the recording)
2. Or ask your Google Workspace admin to enable automatic transcription
3. After the meeting ends, Google saves the transcript to `My Drive > Meet Recordings` — usually within 10–20 minutes
If transcription is not enabled, the skill will proceed without a transcript and prompt you to add notes manually.