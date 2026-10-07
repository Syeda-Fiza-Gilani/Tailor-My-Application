# Tailor My Application

Turn any job posting link into a tailored resume and cover letter automatically.

---

## What this is

A two-part project:

1. **Landing page** (`tailor-my-application.html`): a styled form where someone pastes a job posting URL and an optional note. Built with plain HTML, CSS, and JavaScript — no frameworks, no build step.
2. **n8n workflow**: the automation engine behind it. It fetches the job posting, extracts the real requirements using AI, rewrites the candidate's resume and cover letter to match, and saves both into a new Google Doc.

The landing page is the front door. The n8n workflow is what actually does the work.

<img width="1224" height="845" alt="image" src="https://github.com/user-attachments/assets/52b010ee-0b38-4848-a447-e2d21143faee" />

<img width="1050" height="664" alt="image" src="https://github.com/user-attachments/assets/2affea95-70c9-4904-b229-39c1ec590942" />


---

## How it works, end to end

1. Someone opens the landing page and pastes in a job posting link (LinkedIn, Indeed, a company careers page, etc.), optionally adding a note like "emphasize my leadership experience."
2. The form sends that data to an n8n webhook (or n8n Form Trigger).
3. n8n fetches the job posting page and extracts its visible text.
4. An AI step reads that text and pulls out structured details: job title, company, required skills, preferred skills, key responsibilities, and ATS keywords.
5. Two AI writing steps run:
   - One rewrites the candidate's base resume to lead with the most relevant experience and naturally work in the job's own language and keywords — without inventing anything.
   - One drafts a short, specific cover letter tied to that company and role, avoiding generic opening lines.
6. Both outputs are merged, written into a new Google Doc titled after the job and company, and the doc is moved into a chosen Google Drive folder.

---

## Files

| File | Purpose |
|---|---|
| `index.html` | The public-facing landing page with the intake form |
| `Tailor-My-Job-Application.json` | The combined n8n workflow export, credential-free, ready to import — includes the resume-tailoring nodes described here |

---

## Setup

### 1. Import the n8n workflow
Import the JSON into your n8n instance. Nothing will run until credentials are added (see below).

### 2. Add your credentials
This part of the workflow needs:
- **OpenRouter API** — powers the AI extraction and writing steps (model used by default: `anthropic/claude-sonnet-5`)
- **Google Docs API** (OAuth2) — creates and writes into the output document
- **Google Drive API** (OAuth2) — moves the finished document into a folder

### 3. Fill in your details
Open the **Prepare Candidate Profile** node and replace the placeholder values with the real candidate's:
- Full name, email, phone, portfolio URL
- Preferred tone (e.g. "confident, concise, results-driven")
- Writing language
- Full base resume text (paste the whole resume as plain text)

This is the only node that needs editing to keep the candidate's info current.

### 4. Pick a Google Drive destination
Open the **Generate Application Document** and **Move to Drive Folder** nodes and set the folder you want finished documents saved into. By default it targets the root of My Drive.

### 5. Connect the landing page to the workflow
In `index.html`, find this line near the top of the `<script>` tag:

```javascript
var ENDPOINT_URL = "";
```

Paste in your n8n webhook or Form Trigger production URL. Until this is set, the page runs safely in demo mode and won't send anything anywhere.

### 6. Test it
Submit a real job posting link through the landing page and confirm a new Google Doc appears with a tailored resume and cover letter.

---

## Good to know

- **LinkedIn often blocks anonymous scraping.** If a LinkedIn job link comes back empty, use a company's own careers page or Indeed instead — these usually work without a login wall.
- **Nothing is invented.** The AI prompts are explicitly instructed to never invent employers, titles, dates, or accomplishments — it only reorders and rewords what's actually in the base resume.
- **One resume profile per run, as built.** This workflow is set up around a single candidate's hardcoded profile. For multiple users submitting through the same form, the candidate's details would need to come from the form itself instead of a fixed Set node.
- **Model names drift.** Double-check that `anthropic/claude-sonnet-5` (or whichever model you choose) is still available in your OpenRouter account before relying on this in production.

---

## Short description

> Tailor My Application turns a single job posting link into a fully tailored resume and cover letter built with n8n, AI writing agents, and a lightweight custom landing page. Paste a link, get a ready-to-send application in under a minute. No invented experience, no generic templates — just your real work, re-presented for the job in front of you.
