# FitCheck — AI-Powered CV & JD Matching Workflow

An n8n automation workflow that parses a job description and CV, scores the match, and emails the candidate structured results including a tailored cover letter.

Built as a portfolio project demonstrating AI automation, prompt engineering, and workflow design using n8n and OpenAI.

---

## What it does

1. Candidate submits a form with Job Title, Company, JD text, CV (PDF), and cover letter preferences
2. JD and CV are parsed in parallel using OpenAI gpt-4.1-mini
3. Outputs are merged and a CV Matcher scores the match 0–100 with structured analysis
<img width="1392" height="571" alt="image" src="https://github.com/user-attachments/assets/03e3dfe3-abc6-4d33-ab57-c90850bce13a" />

4. If score ≥ 70, a tailored cover letter is generated using gpt-4o
5. Results + cover letter are emailed directly to the candidate
6. All submissions are logged to Google Sheets
<img width="1225" height="530" alt="image" src="https://github.com/user-attachments/assets/05351e58-3af4-467f-898f-b3e89ccf4aba" />

## Architecture
<img width="1399" height="610" alt="image" src="https://github.com/user-attachments/assets/84a814b5-bf8c-4a0b-b676-1a04e56d8e3f" />

---

## Stack

- [n8n](https://n8n.io) — workflow automation
- OpenAI API — `gpt-4.1-mini` for parsing and matching, `gpt-4o` for cover letter generation
- Google Sheets API — submission logging
- Gmail API — candidate email delivery

---

## Features

- **Domain-agnostic** — works for any role, any industry (not just data roles)
- **Dynamic domain flag detection** — model infers required capabilities from the JD, no hardcoded role types
- **Structured JSON output** — all AI nodes return validated, typed JSON
- **Tone and length controls** — candidate selects cover letter style at submission
- **Score-gated generation** — cover letter only generates for strong matches (≥ 70%), saving API costs
- **Error handling** — try/catch on all Code nodes, CV validation, admin error email alerts
- **HTML email output** — formatted sections with match score, strengths, gaps, CV rewrites, and cover letter

---

## Workflow Architecture

```
Form Trigger
    ├── Extract from File → Validate CV → CV Parser → Normalize CV ──┐
    └── JD Parser → Normalize JD ───────────────────────────────── Merge
                                                                       ↓
                                                                  CV Matcher
                                                                       ↓
                                                             Normalize Matcher
                                                                       ↓
                                                             Output Formatter
                                                                       ↓
                                                              Google Sheets
                                                                       ↓
                                                               IF score ≥ 70?
                                                              ↙             ↘
                                                    Cover Letter         Set Empty
                                                    Generator            Cover Letter
                                                    → Normalize              ↘
                                                              ↘              ↙
                                                          Email Candidate Results
```

---

## Setup

See [SETUP.md](SETUP.md) for full installation and configuration instructions.

---

## Prompts

All system prompts are documented in the [`/prompts`](prompts/) folder:

| File | Node |
|---|---|
| `jd_parser_system.md` | JD Parser |
| `cv_parser_system.md` | CV Parser |
| `cv_matcher_system.md` | CV Matcher |
| `cover_letter_system.md` | Cover Letter Generator |

---

## Output example

The candidate receives an HTML email containing:

- Match score (0–100) and fit level
- Recommendation summary
- Strengths and gaps specific to the role
- Skills to highlight from their CV
- Rewritten CV summary targeted to the role
- Improved CV bullet points
- Suggested cover letter (if score ≥ 70)
<img width="1235" height="636" alt="image" src="https://github.com/user-attachments/assets/25f40d0f-cb67-4f92-9811-28224c338a0c" />
<img width="1298" height="622" alt="image" src="https://github.com/user-attachments/assets/30c64288-2615-4c91-a5ba-1457b403e4b5" />
<img width="1308" height="630" alt="image" src="https://github.com/user-attachments/assets/8132e388-df74-4c7f-8726-e5f0fd061fe4" />


---

## Google Sheets columns

```
submitted_at | candidate_name | email | company | job_title | job_family
| seniority_level | match_score | experience_fit | recommendation
| matched_skills | missing_must_have | missing_nice_to_have | skills_to_surface
| matched_domain_flags | unmatched_domain_flags | matched_competencies
| unmatched_competencies | strengths | gaps | cv_summary_rewrite | bullet_rewrites
```

---

## Built by

Pratistha Thapa — [LinkedIn](https://linkedin.com/in/yourprofile) · [GitHub](https://github.com/yourusername)
