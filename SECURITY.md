# 🔒 Security Policy

This document covers the security model of this project, what is and isn't safe to share publicly, how to handle credentials, and how to report vulnerabilities.

---

## ✅ What Is Safe to Share (Already in This Repo)

| File | Why It's Safe |
|------|--------------|
| `Resume-workflow-sanitized.json` | n8n exports **never** include credentials. All personal IDs have been removed and replaced with placeholders. |
| `README.md` | Documentation only — no secrets. |
| `.gitignore` | Prevents secrets from being committed. |
| `requirements.txt` | Package list only — no keys or tokens. |
| `SECURITY.md` | This file. |

---

## ❌ Never Commit These

These files must **never** be pushed to GitHub. The `.gitignore` already blocks them — but understand why:

| File / Folder | Risk |
|--------------|------|
| `.env` or `.env.*` | Contains API keys in plaintext |
| `.n8n/` folder | Stores **all** your n8n credentials (encrypted locally, but raw JSON if exported) |
| `n8n-credentials-*.json` | Direct export of credentials — instant account compromise if leaked |
| `google-credentials.json` / `service-account.json` | Full Google API access |
| `token.json` | OAuth refresh token — grants long-lived Google access |
| Any file with `sk-`, `AIza`, `Bearer ` patterns | OpenAI / Gemini / generic API keys |

---

## 🔑 Credential Security Model

This project uses **4 external APIs**. Here's how to store each securely:

### Google Sheets API
- Use a **Service Account** with least-privilege access (Viewer + Editor on your specific sheet only — not all of Drive)
- Store the `service-account.json` locally only; never commit it
- In n8n: add as a "Google Sheets" credential; n8n encrypts it at rest

### Google Gemini API
- Key format: starts with `AIza...`
- Generate a key scoped to Gemini API only (not all Google Cloud APIs)
- Rotate immediately if accidentally exposed
- In n8n: add as a "Google Gemini" credential

### OpenAI API
- Key format: starts with `sk-...`
- Set a **monthly spend limit** in your OpenAI dashboard before use
- Use project-scoped keys (not organization master keys)
- In n8n: add as an "OpenAI" credential

### Firecrawl API
- Key format: `fc-...`
- Set domain allowlists in Firecrawl dashboard if possible
- In n8n: add as an "HTTP Header Auth" or "Firecrawl" credential

---

## 🛡️ Pre-Commit Secret Scanning (Recommended)

Install `detect-secrets` to automatically block accidental key commits:

```bash
pip install detect-secrets pre-commit
detect-secrets scan > .secrets.baseline
```

Create `.pre-commit-config.yaml`:

```yaml
repos:
  - repo: https://github.com/Yelp/detect-secrets
    rev: v1.5.0
    hooks:
      - id: detect-secrets
        args: ['--baseline', '.secrets.baseline']
```

Then activate:

```bash
pre-commit install
```

Now every `git commit` will automatically scan for secrets before they can be pushed.

---


## 🔄 If You Accidentally Expose a Secret

1. **Revoke the key immediately** — don't just delete the commit. Git history retains it.
   - OpenAI: [platform.openai.com/api-keys](https://platform.openai.com/api-keys) → Delete key
   - Google: [console.cloud.google.com](https://console.cloud.google.com) → API Credentials → Delete
   - Gemini: [aistudio.google.com](https://aistudio.google.com) → API Keys → Revoke
   - Firecrawl: Dashboard → API Keys → Revoke

2. **Clean git history** using BFG Repo Cleaner or `git filter-repo`:
   ```bash
   git filter-repo --replace-text <(echo 'YOUR_LEAKED_KEY==>REMOVED')
   ```

3. **Force-push** the cleaned history and notify GitHub support if the repo is public.

4. **Generate a new key** only after the old one is confirmed revoked.

---

## 🚨 Reporting a Vulnerability

If you discover a security issue with this project:

1. **Do not open a public GitHub issue.**
2. Email the repository owner directly (see GitHub profile).
3. Include: description of the issue, steps to reproduce, and potential impact.
4. Allow reasonable time to respond before public disclosure.

---

## 📋 Security Checklist for New Contributors

Before opening a pull request:

- [ ] No API keys, tokens, or passwords in any file
- [ ] No personal email addresses in workflow JSON
- [ ] No hardcoded Google Sheet/Doc IDs pointing to real user data
- [ ] `.gitignore` is present and includes all secret file patterns
- [ ] `detect-secrets` pre-commit hook is installed and passing
- [ ] Workflow JSON tested via n8n import — credentials connected separately
