# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What You Are Building

An n8n workflow JSON file that automates lead enrichment and outreach for AI workflow automation consulting. The output is a single importable n8n workflow JSON file named `P6_Leads_V2_claude.json`.

---

## Environment

- n8n Cloud version 2.17.5
- Output must be a valid importable n8n workflow JSON file
- Use free tier LLM models where possible
- `active: false` in the exported JSON

---

## Credentials

Use these exact credential names so n8n wires them automatically on import:

| Service | Credential Name |
|---|---|
| Google Sheets Trigger | Google Sheets Trigger OAuth2 API |
| Google Sheets (read/append) | Google Sheets OAuth2 API |
| Gmail | Gmail OAuth2 API |

Use placeholder values for all Sheet IDs, API keys, and email addresses.

---

## Google Sheet Structure

- Document name: P6: Leads
- Input sheet: **Companies** — columns: Company, Website, Contact Name, Role, Email
- Log sheet: **Summary** — columns: Company, Orig Text, Summary, Rating, Recency

---

## Workflow Architecture (3-Phase Build)

Build and deliver in three distinct phases, stopping after each to confirm before continuing.

### Phase 1 — Trigger through first LLM (Summarizer)
1. **Google Sheets Trigger** — fires when a new row is added to Companies
2. **Google Sheets Read** — query Summary sheet to check if company was processed within 90 days (use Recency column); skip if found
3. **HTTP Request** — fetch raw website content from the company's Website field
4. **HTTP Request** — fetch recent news (NewsAPI or similar free-tier search)
5. **LLM Node (Summarizer)** — produce a 2–3 sentence company summary from scraped content

### Phase 2 — Scoring LLM
6. **LLM Node (Scorer)** — score company 1–10 for fit as an AI workflow automation consulting target; output score + one-sentence rationale
7. **IF Node** — branch on score ≥ 7

### Phase 3 — Outreach, logging, and dedup
8. **LLM Node (Email Writer)** — generate personalized cold outreach email (2–3 paragraphs, 3–4 sentences each) for score ≥ 7 branch
9. **Gmail Node** — send email; body must convert `\n` to `<br>` HTML tags
10. **Google Sheets Append** — log every company (both branches) to Summary sheet with `$now` as Recency timestamp

---

## Key Constraints

- `P6_Requirements.md` is the source of truth for scoring criteria, target persona, and success criteria
- Deduplication window: 90 days (check Recency column in Summary sheet before processing)
- Log sheet Recency column must use `$now` for timestamps
- Gmail body must convert newlines to HTML `<br>` tags
- All company data (name, website, contact, email) must flow from the trigger node via cross-node references — do not hardcode
- Target companies: small to mid-size SaaS and tech companies with operational complexity; exclude large enterprises
- Value proposition: deploy internal productivity tools using n8n, Claude, and Google Workspace

---

## n8n JSON Output Rules

- The JSON must be importable via n8n's workflow import UI without modification
- Each node requires: `id`, `name`, `type`, `typeVersion`, `position`, `parameters`, and `credentials` (where applicable)
- Use `n8n-nodes-base.googleSheetsTrigger`, `n8n-nodes-base.googleSheets`, `n8n-nodes-base.httpRequest`, `n8n-nodes-base.gmail`, `n8n-nodes-base.if`, and `@n8n/n8n-nodes-langchain.lmChatOpenAi` (or equivalent free-tier LLM node)
- Connections are defined in the top-level `"connections"` object keyed by source node name
- Validate JSON is syntactically correct before writing the file
