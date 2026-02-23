<img width="1544" height="485" alt="image" src="https://github.com/user-attachments/assets/c017e87f-54f2-4d58-8160-64589a7ac7c1" />Below is a clean, professional **README.md** you can paste directly into your Git repo. It reflects your exact workflow and keeps the explanation simple and clear (as you prefer).

---

📄 Compliance Copilot — n8n Workflow

An automated AI-powered compliance checking pipeline built using **n8n + OpenAI**.

This workflow scans uploaded documents (PDF), extracts employee details, evaluates compliance risk, and sends alert emails when violations are detected.

---

🚀 Features

* 📥 Webhook-based document ingestion
* 📄 Automatic PDF text extraction
* 🔍 Regex-based employee data extraction
* 🤖 AI compliance risk analysis
* 🧱 Structured JSON parsing
* 🚨 Conditional risk routing
* 📧 Automated email alerts

---

🏗️ Workflow Architecture

```text
Webhook
 → Extract from File (PDF)
 → Edit Fields (prepare text)
 → Edit Fields (regex extraction)
 → Basic LLM Chain
 → Structured Output Parser
 → IF (risk check)
 → Gmail (alert)
```

---

<img width="1544" height="485" alt="image" src="https://github.com/user-attachments/assets/c3ee87d9-7979-49b7-986c-804a761b8776" />


🔄 How It Works

## Step 1 — Webhook Trigger

The workflow starts when a document is sent via HTTP POST to the n8n webhook.

**Input:**

* Binary PDF file

**Output:**

* File passed to extraction step

---

## Step 2 — Extract from File

Uses n8n’s PDF extractor.

**Purpose:**

* Convert PDF → raw text

**Key output field:**

```json
text
```

---

## Step 3 — Edit Fields (Prepare Text)

Creates normalized fields for downstream processing.

**Fields created:**

* `document_text`
* `policy_rules`

---

## Step 4 — Edit Fields (Regex Extraction)

Deterministically extracts employee metadata using regex.

**Extracted fields:**

* `person_name`
* `employee_id`

Example patterns:

```js
Employee Name:\s*([^\n]+)
Employee ID:\s*([^\n]+)
```

✅ This avoids LLM hallucinations.

---

## Step 5 — Basic LLM Chain

OpenAI evaluates the document against policy rules.

**Model tasks:**

* Compliance analysis
* Risk scoring
* Violation detection
* Summary generation

**Output format (strict JSON):**

```json
{
  "person_name": "string",
  "employee_id": "string",
  "risk_level": "Low | Medium | High",
  "violations": [],
  "confidence": 0,
  "summary": "string"
}
```

---

## Step 6 — Structured Output Parser

Ensures the LLM response matches the required JSON schema.

**Benefits:**

* Type safety
* Predictable structure
* Downstream reliability

---

## Step 7 — IF Node (Risk Routing)

Evaluates:

```js
risk_level == "High"
```

**Routing:**

* TRUE → send alert email
* FALSE → no action

---

## Step 8 — Gmail Alert

Sends formatted compliance alert.

**Email includes:**

* Employee Name
* Employee ID
* Risk Level
* Violations
* Confidence
* Summary

---

# 🧪 Sample Use Case

Input document:

> Employee Expense Reimbursement Report

Output:

```json
{
  "risk_level": "High",
  "violations": [
    "Personal bank info exposure",
    "Unauthorized payment method"
  ]
}
```

Result:

📧 Alert email sent automatically.

---

# ⚙️ Prerequisites

Before running:

* n8n (cloud or self-hosted)
* OpenAI API credentials
* Gmail OAuth configured
* PDF test document

---

# 🔧 Setup Instructions

## 1. Import Workflow

* Open n8n
* Import the workflow JSON
* Activate the workflow

---

## 2. Configure Credentials

Update:

* OpenAI Chat Model
* Gmail account
* Webhook URL (if needed)

---

## 3. Test the Flow

Send POST request with PDF:

Send a test request (very important)

You can test using:

Postman

curl

or browser form upload

```bash
Simple curl example:
curl -X POST <WEBHOOK_URL> \
  -F "data=@sample.pdf"
```

---

# 📌 Important Design Decisions

### ✅ Regex before LLM

Employee data is extracted deterministically to:

* reduce hallucinations
* improve reliability
* lower token usage

---

### ✅ Structured Output Parser

Used to guarantee schema compliance.

---

### ✅ Conditional Routing

Prevents alert fatigue by emailing only high-risk cases.

---

# 🚨 Known Limitations

* Regex assumes fixed document format
* Only PDF currently supported
* Single-document processing
* English documents only

---

# 🔮 Future Improvements

* [ ] Multi-format support (DOCX, images)
* [ ] Batch document processing
* [ ] Risk dashboard
* [ ] Database logging
* [ ] RAG-based policy engine
* [ ] Slack/Teams alerts
* [ ] Human-in-the-loop review



