# 🤖 Agent 1 - CV Filter & Ranker

> Part of the [n8n HR Agents](/) project - a collection of AI-powered automation workflows for HR teams.

An n8n workflow that automatically monitors a Gmail inbox, extracts text from incoming CV attachments (PDF & DOCX), scores candidates using GPT-4o-mini, logs results to Google Sheets, and sends a Slack notification - all without writing a single line of application code.

---

## 📸 Demo

### Workflow overview
![n8n workflow canvas](docs/workflow.png)

### Slack notifications
![Slack DM with candidate summary](docs/slack.png)

### Google Sheets ranking
![Google Sheets with candidate data](docs/sheets.png)

---

## ✨ Features

- **Automatic email monitoring** - polls Gmail inbox every minute for new emails containing "CV" in subject or body
- **Multi-format support** - extracts text from both PDF and DOCX attachments
- **AI-powered scoring** - GPT-4o-mini analyzes each CV and returns structured JSON with candidate data, a match score (1–10), summary, and 3 interview questions
- **Google Sheets logging** - appends every candidate as a new row with full details
- **Slack notifications** - sends a DM with candidate summary and a direct link to the sheet
- **Error handling** - automatically replies to senders who forget to attach a CV, then stops gracefully

---

## 🔧 Tech stack

| Tool | Purpose | Cost |
|------|---------|------|
| [n8n Cloud](https://n8n.io) | Workflow automation | Free trial / from €20/mo |
| Gmail API | Email trigger & reply | Free |
| OpenAI GPT-4o-mini | CV analysis & scoring | ~$0.01 per CV |
| Google Sheets API | Candidate database | Free |
| Slack API | Recruiter notifications | Free |

---

## 🗂️ Workflow nodes

```
Gmail Trigger
    └── Get a Message (with attachment download)
        └── IF (attachment exists?)
            ├── FALSE → Reply to sender → Stop and Error
            └── TRUE → Switch (PDF or DOCX?)
                ├── PDF → Extract from File
                └── DOCX → Code in JavaScript (custom parser)
                    └── OpenAI Chat Model (GPT-4o-mini)
                        └── Code in JavaScript (JSON parser)
                            └── Append row in Google Sheets
                                └── Send Slack message
```
---

## ⚠️ Known limitations

- **Gmail Trigger polling** - the trigger polls every minute. Emails arriving within the same polling window may occasionally be processed out of order. In real-world use (where CVs arrive minutes or hours apart) this is not an issue.
- **DOCX parsing** - uses a custom XML regex parser since n8n Cloud blocks external Node.js modules. Works well for standard DOCX files; heavily formatted documents may lose some styling.
- **Single attachment** - currently processes only the first attachment (`attachment_0`). If a candidate sends multiple files, only the first is analyzed.

## By

*Agata Horodyska*
