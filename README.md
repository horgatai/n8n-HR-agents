# 🤖 n8n HR Agents

A collection of three AI-powered n8n workflow agents that automate the full recruitment cycle — from the first CV in the inbox to a new hire's 30-day check-in. Built without writing a single line of application code.

---

## 📦 Agents

| Agent | Trigger | What it does |
|-------|---------|-------------|
| [Agent 1 — CV Filter & Ranker](./agent-1-cv-filter/) | New email with CV attachment | Extracts text from PDF/DOCX, scores candidate 1–10 with GPT-4o-mini, logs to Google Sheets, notifies on Slack |
| [Agent 2 — Interview Planner](./agent-2-interview-planner/) | Status changed to `interview` | Generates AI interview plan, creates private recruiter brief in Google Docs, schedules Google Meet, notifies on Slack |
| [Agent 3 — Onboarding Agent](./agent-3-onboarding/) | Status changed to `active` | Sends welcome email, creates onboarding checklist in Google Docs, schedules first-day meeting, follows up on Day 7 and Day 30 |

---

## 🔗 How the agents connect

All three agents share a single **Google Sheet** (`CV Ranking`) as their central database. The recruiter never needs to touch n8n — they just update a `Status` column and the right agent fires automatically.

```
Candidate sends CV by email
        │
        ▼
┌─────────────────┐
│   Agent 1       │  Gmail → AI → Google Sheets → Slack
│  CV Filter      │  Status set to: "new"
└────────┬────────┘
         │  Recruiter reviews sheet, changes status to "interview"
         ▼
┌─────────────────┐
│   Agent 2       │  Sheets → AI → Google Docs → Calendar → Slack
│  Interview      │  Status stays: "interview"
│  Planner        │
└────────┬────────┘
         │  Recruiter changes status to "active" after offer accepted
         ▼
┌─────────────────┐
│   Agent 3       │  Sheets → Gmail → Slack → Google Docs → Calendar
│  Onboarding     │  + automated Day 7 and Day 30 follow-ups
│  Agent          │
└─────────────────┘
```

The only manual step in the entire pipeline is the recruiter changing a status field in Google Sheets. Everything else is automated.

---

## ⏱️ Time saved

Manual recruitment and onboarding is one of the most time-consuming HR functions. Here's what the research says — and how these agents address it:

### CV screening
- A hiring professional spends approximately **23 hours** screening candidates for a single hire
- Resume review alone accounts for **22% of a recruiter's time**
- Using AI saves recruiters about **4.5 hours per week** and reduces the average cost of hiring by 71%

**Agent 1 impact:** CV analysis that takes a recruiter 30–90 seconds per resume is reduced to ~3 seconds per CV. A batch of 50 CVs goes from 1–2 hours of manual work to under 3 minutes.

### Interview scheduling and preparation
- **67% of recruiters** say it takes between 30 minutes and 2 hours to schedule a single interview
- Recruiters spend **⅔ of their overall hiring time** on the interview process
- **60% of recruiters** say they regularly lose candidates before they're able to schedule an interview

**Agent 2 impact:** Interview scheduling + brief preparation that takes 1–2 hours is reduced to changing one cell in a spreadsheet. The Google Meet link, recruiter brief, and calendar invite are created in seconds.

### Onboarding
- Two in five HR managers who don't use digital tools **spend 3+ hours** onboarding each new employee manually
- Only **12% of employees** think their company has done a great job with onboarding — meaning 88% rate it as a failure
- Automating onboarding can produce up to a **65% increase in new hire productivity** and a **77% decrease in turnover** within the first 3 months

**Agent 3 impact:** The welcome email, checklist, calendar event, and Day 7/30 follow-ups — tasks that typically take 3+ hours spread across weeks — are triggered by a single status change and run automatically.

### Combined estimate
For a team hiring **10 people per month**, these three agents together can save an estimated **15–25 hours per month** of repetitive administrative work — time that HR professionals can redirect to building relationships, culture, and strategy.

---

## 🔧 Tech stack

| Tool | Purpose | Cost |
|------|---------|------|
| [n8n Cloud](https://n8n.io) | Workflow automation | Free trial / from €20/mo |
| Gmail API | Email trigger, CV parsing, follow-ups | Free |
| OpenAI GPT-4o-mini | CV scoring, interview plan generation | ~$0.01–0.02 per candidate |
| Google Sheets API | Central candidate database | Free |
| Google Calendar API | Interview and onboarding events + Meet | Free |
| Google Docs API | Recruiter briefs and onboarding checklists | Free |
| Slack API | Real-time notifications | Free |

**Estimated monthly cost for 10 hires: < $1 in OpenAI credits.**

## 📁 Repository structure

```
n8n-hr-agents/
├── README.md                          ← you are here
├── agent-1-cv-filter/
│   ├── README.md
│   ├── cv-filter-agent.json           ← n8n workflow export
│   └── docs/
│       ├── workflow.png
│       ├── slack.png
│       └── sheets.png
├── agent-2-interview-planner/
│   ├── README.md
│   ├── interview-planner.json
│   └── docs/
│       ├── workflow.png
│       ├── slack.png
│       ├── docs.png
│       └── calendar.png
└── agent-3-onboarding/
    ├── README.md
    ├── onboarding-agent.json
    └── docs/
        ├── workflow.png
        ├── welcome-mail.png
        ├── slack-day0.png
        ├── docs-checklist.png
        └── calendar.png
```

---

## ⚠️ Known limitations

- **n8n Cloud plan** — the free trial has execution limits. For production use with high CV volume, a paid plan is recommended.
- **Gmail polling** — the trigger polls every minute. Emails arriving within the same polling window may occasionally be processed out of order. In real-world use this is not an issue.
- **OpenAI dependency** — all AI features require an active OpenAI API key with available credits.
- **Single-language prompts** — prompts are optimized for English-language CVs. For other languages, update the system prompts in Agent 1 and Agent 2.

---
