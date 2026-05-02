# CLAUDE.md — Project 6: Lead Generation and Enrichment Pipeline

## What You Are Building

An n8n workflow JSON file that automates lead enrichment and outreach for AI workflow automation consulting. The output is a single importable n8n workflow JSON file named `P6_Leads_V2_claude.json`.

---

## Environment

- n8n Cloud version 2.17.5
- Output must be a valid importable n8n workflow JSON file
- Use free tier LLM models where possible
- active: false

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
- Input sheet: Companies -- contains Company, Website, Contact Name, Role, Email
- Log sheet: Summary -- contains Company, Orig Text, Summary, Rating, Recency

---

## What the Workflow Must Do

1. Trigger when a new row is added to the Companies sheet
2. Check the Summary log to avoid reprocessing companies contacted within 90 days
3. Enrich each new company with live website content and recent news
4. Generate a 2-3 sentence company summary using an LLM
5. Score each company 1-10 for fit as a consulting target -- companies scoring 7+ receive outreach
6. Generate a personalized cold outreach email for high scorers and send via Gmail
7. Log every company to the Summary sheet with a timestamp regardless of score

---

## Consulting Value Proposition

AI workflow automation consulting that helps companies deploy internal productivity tools using platforms like n8n, Claude, and Google Workspace. Target customers are small to mid-size SaaS and tech companies with operational complexity.

---

## Key Constraints

- Use the Requirements document as the source of truth for scoring criteria, data schema, and success criteria
- Log sheet Recency column must use $now for timestamps
- Gmail body must convert newlines to HTML br tags
- All company data must be pulled from the trigger node using cross-node references throughout the workflow
