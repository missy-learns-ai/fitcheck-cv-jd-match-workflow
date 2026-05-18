# Setup Guide

## Prerequisites

- n8n instance (self-hosted or [n8n.cloud](https://n8n.io/cloud))
- OpenAI API account with access to `gpt-4.1-mini` and `gpt-4o`
- Google account with Sheets and Gmail access

---

## 1. Import the workflow

1. Open your n8n instance
2. Go to **Workflows → Import from file**
3. Upload `workflow/FitCheck-CV-JD-Match-Workflow.json`

---

## 2. Set up credentials

Copy `.env.example` to `.env` and fill in your values.

In n8n, create the following credentials under **Settings → Credentials**:

### OpenAI
- Credential type: **OpenAI API**
- Add your OpenAI API key

### Google Sheets
- Credential type: **Google Sheets OAuth2 API**
- Sign in with your Google account and grant Sheets access

### Gmail
- Credential type: **Gmail OAuth2 API**
- Sign in with your Google account and grant Gmail send access

---

## 3. Update node references after importing

The exported workflow uses placeholders only (no real API keys or n8n credential IDs). After importing, open these nodes and reconnect credentials:

| Node | Placeholder in export | What to update |
|---|---|---|
| JD Parser | `YOUR_OPENAI_CREDENTIAL_ID` | Select your OpenAI credential |
| CV Parser | `YOUR_OPENAI_CREDENTIAL_ID` | Select your OpenAI credential |
| CV Matcher | `YOUR_OPENAI_CREDENTIAL_ID` | Select your OpenAI credential |
| Cover Letter Generator | `YOUR_OPENAI_CREDENTIAL_ID` | Select your OpenAI credential |
| Store Results | `YOUR_GOOGLE_SHEETS_CREDENTIAL_ID` + `YOUR_GOOGLE_SHEET_ID` | Select your Google Sheets credential and spreadsheet |
| Email Candidate Results | `YOUR_GMAIL_CREDENTIAL_ID` | Select your Gmail credential |
| Workflow settings | `YOUR_ERROR_WORKFLOW_ID` | Set your Error Handler workflow (optional) |

---

## 4. Create your Google Sheet

Create a new Google Sheet with these exact column headers in row 1:

```
submitted_at
candidate_name
email
company
job_title
job_family
seniority_level
match_score
experience_fit
recommendation
matched_skills
missing_must_have
missing_nice_to_have
skills_to_surface
matched_domain_flags
unmatched_domain_flags
matched_competencies
unmatched_competencies
strengths
gaps
cv_summary_rewrite
bullet_rewrites
```

In the **Store Results** node, update the spreadsheet ID to match your sheet.

---

## 5. Set up the Error Handler workflow (optional but recommended)

1. Create a new workflow in n8n called `FitCheck Error Handler`
2. Add an **Error Trigger** node
3. Add a **Gmail** node to send alert emails to your address
4. In your main workflow settings, set **Error Workflow** to `FitCheck Error Handler`

---

## 6. Activate the workflow

Toggle the workflow to **Active** in n8n. The form trigger URL will be generated automatically.

---

## Environment variables reference

| Variable | Description |
|---|---|
| `OPENAI_API_KEY` | Your OpenAI API key |
| `GOOGLE_CLIENT_ID` | Google OAuth client ID |
| `GOOGLE_CLIENT_SECRET` | Google OAuth client secret |
| `GMAIL_SENDER_ADDRESS` | Gmail address used to send results |
| `GOOGLE_SHEET_ID` | ID of your Google Sheet (from the URL) |

---

## Estimated API costs per submission

| Model | Node | Approx. tokens | Approx. cost |
|---|---|---|---|
| gpt-4.1-mini | JD Parser | ~1,000 | ~$0.001 |
| gpt-4.1-mini | CV Parser | ~1,500 | ~$0.002 |
| gpt-4.1-mini | CV Matcher | ~2,500 | ~$0.003 |
| gpt-4o | Cover Letter (if ≥ 70%) | ~1,500 | ~$0.015 |

**Total per submission: ~$0.02–0.03**
