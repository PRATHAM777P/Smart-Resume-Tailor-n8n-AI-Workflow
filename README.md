# 🤖 Smart Resume Tailor — n8n AI Workflow

<div align="center">

![n8n](https://img.shields.io/badge/n8n-workflow-orange?style=for-the-badge&logo=n8n)
![AI Powered](https://img.shields.io/badge/AI-Powered-blue?style=for-the-badge&logo=openai)
![Google Gemini](https://img.shields.io/badge/Gemini-API-red?style=for-the-badge&logo=google)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)

**Automatically tailor your resume to any job description in seconds — powered by LLMs and n8n automation.**

[Features](#-features) · [Architecture](#-architecture) · [Setup](#-setup) · [Usage](#-usage) · [Security](#-security) · [Customization](#-customization)

</div>

---

## 🚀 What This Does

Traditional resume tailoring takes **15+ minutes per application**. This workflow brings that down to near zero manual effort.

You paste a job link. The workflow:

1. **Scrapes** the full job description using Firecrawl
2. **Analyzes** requirements, skills, and keywords using LLMs
3. **Rewrites** your resume bullet points to match the role
4. **Outputs** a tailored resume, saved back to Google Sheets — ready to export

All of it automated. You only need to find the job posting and submit the final resume.

---

## ✨ Features

- ⚡ **Parallel Processing** — Run multiple job tailoring tasks simultaneously
- 🧠 **Dual LLM Support** — Uses both Google Gemini and OpenAI for specialized tasks
- 📊 **Google Sheets Integration** — Input jobs, get results back in a structured sheet
- 🔗 **Firecrawl Web Scraping** — Extracts clean job descriptions from any URL
- 🎯 **Keyword Alignment** — Automatically matches ATS-friendly keywords from JD to resume
- 📄 **LaTeX Resume Output** — Generates a professionally formatted resume
- 🔧 **Fully Customizable** — Swap models, prompts, data sources, or output formats

---

## 🏗️ Architecture

```
Google Sheets (Job URL Input)
        │
        ▼
Firecrawl (Scrape Job Description)
        │
        ▼
LLM Chain — Job Analysis (Gemini / OpenAI)
        │
        ├──► Skills Extraction
        ├──► Keyword Matching
        ├──► Bullet Point Rewriting
        └──► Resume Assembly
                │
                ▼
        Google Sheets (Tailored Resume Output)
```

The workflow contains **44 nodes** including LLM chains, merge operations, field editors, and sheet triggers — all orchestrated via n8n.

---

## 🧩 Tech Stack

| Tool | Purpose |
|------|---------|
| **n8n** | Workflow orchestration |
| **Google Sheets** | Input/output data layer |
| **Google Gemini API** | Primary LLM (analysis & rewriting) |
| **OpenAI API** | Secondary LLM (structured output tasks) |
| **Firecrawl** | Job description web scraping |

---

## ⚙️ Setup

### Prerequisites

- [n8n account](https://n8n.io) (self-hosted or cloud)
- API keys for all integrations (see below)
- A Google Sheets file with a "Job descriptions" sheet

### Step 1 — Get API Keys

| Service | Where to Get It |
|---------|----------------|
| Google Sheets | [Google Cloud Console](https://console.cloud.google.com) → Enable Sheets API |
| Gemini | [Google AI Studio](https://aistudio.google.com) |
| OpenAI | [platform.openai.com](https://platform.openai.com) |
| Firecrawl | [firecrawl.dev](https://firecrawl.dev) |

### Step 2 — Import the Workflow

1. Open your n8n instance
2. Click **Import from File**
3. Select `Resume-workflow-sanitized.json`
4. The workflow will appear in your canvas

### Step 3 — Connect Credentials

In n8n, go to **Credentials** and add:
- Google Sheets OAuth2 (or Service Account)
- Gemini API Key
- OpenAI API Key
- Firecrawl API Key

Then update each node that uses credentials to point to your newly added credentials.

### Step 4 — Set Up Your Google Sheet

Create a Google Sheet with these tabs:
- **Job descriptions** — where you paste job URLs
- **Job-content** — auto-populated with scraped content
- **final-job-resume** — where your tailored resumes are saved

Update the Google Sheets nodes in n8n to point to **your** sheet ID (visible in the URL: `docs.google.com/spreadsheets/d/YOUR_SHEET_ID/`).

### Step 5 — Upload Your Resume Template

In the relevant n8n node, paste your base resume content (in plain text or LaTeX). The workflow uses this as the starting point for tailoring.

---

## 🎯 Usage

1. Open your Google Sheet → **"Job descriptions"** tab
2. Paste a job posting URL into the designated column
3. The n8n trigger fires automatically (every minute by default)
4. Wait ~60–90 seconds for the workflow to complete
5. Check the **"final-job-resume"** tab for your tailored resume

That's it. No further action needed until you're ready to apply.

---

## 🔒 Security

This repository is safe to share publicly. Here's why:

- ✅ **No API keys in the workflow JSON** — n8n stores credentials separately, never in exported files
- ✅ **No hardcoded personal data** — all user-specific IDs and sheet references have been removed
- ✅ **No execution history** — pinData is empty; no previous run output is embedded
- ⚠️ **You must supply your own credentials** — nothing works out of the box without your API keys connected in n8n

> **Never commit `.n8n/` config folders, `.env` files, or n8n credential exports to GitHub.** Add these to `.gitignore` (provided in this repo).

---

## 🛠️ Customization

The workflow is designed to be adapted:

**Change the LLM model**
→ Open any Gemini or OpenAI node → switch the model version

**Customize system prompts**
→ Edit the "Basic LLM Chain" nodes to reflect your experience, tone, or industry

**Change the data source**
→ Replace Google Sheets nodes with Airtable, Notion, or CSV inputs

**Change the output format**
→ Currently outputs LaTeX resume source; modify the final assembly prompt to output plain text, JSON, or cover letter format instead

---

## 📁 Repository Structure

```
├── README.md # Project documentation
├── Resume-workflow-sanitized.json # n8n workflow (safe to share)
├── .gitignore # Prevents committing sensitive files
├── SECURITY.md # Security policy and reporting guidelines
└── requirements.txt # Python dependencies
```

---

## 🤝 Contributing

Pull requests welcome. For major changes, open an issue first to discuss what you'd like to change.

---

## 📄 License

MIT — feel free to use, modify, and share.

---

<div align="center">
Built with n8n · Gemini · OpenAI · Firecrawl
</div>
