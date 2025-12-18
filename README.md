# Gmail Department Automation (n8n Workflows)

This repository contains **3 n8n workflow exports** used to automate incoming Gmail messages for different departments (Info, Sales, HR).  
Core idea: **Receive email → analyze/classify → respond/label/route → log to Google Sheets**.

## Files

- `Sales final (3).json` — Sales routing + attachment handling + logging. :contentReference[oaicite:3]{index=3}
- `infofinal (3).json` — Info inbox automation (auto-reply/labels/routing + Sheets logging). :contentReference[oaicite:4]{index=4}
- `HR3final (3).json` — HR pipeline for job applications/CVs (Drive upload, extract text, AI extraction, routing, logging). :contentReference[oaicite:5]{index=5}

---

## What these workflows do (High-level)

### 1) Info Workflow (`infofinal (3).json`)
- Listens to the **Info** inbox using **Gmail Trigger**.
- Normalizes sender, subject, body, date, and message ID.
- Uses AI to produce a **summary/classification**.
- Optionally:
  - Sends an **automatic reply**.
  - **Adds Gmail labels** for tracking (e.g., Auto-Routed/...).
  - Appends a new row into **Google Sheets** for logging and reporting.

### 2) Sales Workflow (`Sales final (3).json`)
- Processes incoming emails and attachments (PDF/XLS/CSV/image).
- Extracts content from attachments (example nodes exist for PDF/XLS/CSV and image analysis).
- Uses **OpenAI** to analyze content and generate structured output.
- Routes/forwards messages to internal addresses (Sales/Finance/Projects/Contracts patterns exist).
- Marks messages as read, adds labels, and logs results to **Google Sheets**.

### 3) HR Workflow (`HR3final (3).json`)
- Focuses on **job applications** and CV attachments.
- Supports CV files (PDF/DOCX patterns exist):
  - Uploads CV to **Google Drive**
  - Extracts resume content
  - Uses **OpenAI** to extract candidate fields such as:
    - Full name, email, phone
    - Education, years of experience
    - Field of expertise, skills
- Uses a **Switch** to route candidates by detected domain/role (e.g., Marketing, AI, Cybersecurity, HR, etc.).
- Logs candidate data and CV link to **Google Sheets**.

---

## Requirements

### n8n
- n8n installed and running (self-hosted or cloud).
- Workflows imported into your n8n instance.

### Accounts / Integrations
These workflows rely on credentials configured inside n8n:
- **Gmail OAuth2** (read emails, reply, label, mark-as-read, forward)
- **OpenAI** credentials (used by AI nodes)
- **Google Sheets OAuth2** (append logging rows)
- **Google Drive OAuth2** (upload CVs / store attachments) — used heavily in HR workflow

---

## Setup

1. **Import workflows**
   - In n8n: `Workflows → Import from File`
   - Import:
     - `Sales final (3).json`
     - `infofinal (3).json`
     - `HR3final (3).json`

2. **Create / map credentials**
   - Go to: `Credentials`
   - Ensure these exist and are selected in nodes:
     - Gmail OAuth2
     - OpenAI API
     - Google Sheets OAuth2
     - Google Drive OAuth2

3. **Update configuration fields**
   In each workflow, check and update:
   - Department target emails (forwarding)
   - Gmail label names (optional)
   - Google Sheets:
     - Spreadsheet ID
     - Sheet name/tab
   - Google Drive:
     - Folder ID for uploads
   - Any hard-coded node fields like “message” templates

4. **Enable workflows**
   - Start with “Test” using a sample email.
   - Then activate each workflow.

---

## Logging (Google Sheets)

Each workflow appends structured records into Google Sheets to support:
- Audit trail
- Performance tracking
- Daily/weekly reporting

Recommended columns (example):
- `from_email`
- `subject`
- `date_time`
- `classification`
- `summary`
- `forward_to`
- `message_id`
- `attachment_link` (HR / Drive)

---

## Customization

### AI prompts / outputs
You can tune:
- Classification labels (Info vs Sales vs HR vs Finance vs Projects vs Contracts)
- Output schema (JSON fields)
- Confidence scoring / strict formatting

### Auto-replies
Update reply content per department to match your brand voice and SLA.

### Labels
Use a consistent labeling convention like:
- `Auto-Routed/Info`
- `Auto-Routed/Sales`
- `Auto-Routed/HR`
- `Auto-Routed/Finance`
- `Auto-Routed/Contracts`

---

## Security Notes

- Use least-privilege OAuth scopes where possible.
- Store secrets only in **n8n Credentials** (avoid hard-coding API keys).
- Consider adding:
  - Rate limits / retries
  - Error handling (fallback route to “Manual Review” sheet)
  - PII masking for logs (especially HR)

---

## Troubleshooting

- **No emails triggering**
  - Confirm Gmail Trigger is enabled and connected
  - Check inbox filters / label filters
- **Sheets append fails**
  - Spreadsheet ID / Sheet name mismatch
  - OAuth permissions
- **Drive upload fails**
  - Folder ID invalid or access denied
- **OpenAI node errors**
  - Invalid API key, model name, token limits, or prompt formatting

---

## License
Internal use (adjust as needed).
