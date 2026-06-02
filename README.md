# Marketing Campaign Agent

An n8n AI agent workflow that turns internal product documents into a full marketing campaign in minutes.

## What it does

Upload any internal files about a product feature (specs, meeting notes, customer feedback, roadmap tickets) and the workflow automatically generates:

- **LinkedIn post** — concise, high-impact, written for founders and executives
- **Marketing email** — subject line + full body with clear CTA
- **Blog post** — long-form, SEO-friendly article with structured sections
- **Promotional image** — generated via GPT Image
- **Quality report** — an AI judge scores all content and auto-improves anything that doesn't pass

The final output is delivered as a **formatted HTML email** sent directly to the submitter.

## Workflow Architecture

```
Form Submission (file upload)
        │
        ▼
  Convert Files to Text
        │
        ▼
  Context Builder Agent       ← extracts structured campaign_context from docs
        │
        ▼
  Prompt Engineer Agent       ← generates channel-specific prompts
        │
        ▼
  ┌─────┬──────┬──────┬───────┐
  │     │      │      │       │
LinkedIn Email  Blog  Image   │
  │     │      │      │       │
  └─────┴──────┴──────┘       │
        │                     │
        ▼                     ▼
   Merge (3 inputs)     Analyze Image
        │
        ▼
  Judge & Score Content
        │
   ┌────┴────┐
Pass?       Revise?
   │           │
   │    Auto-Improve Content
   │           │
   └─────┬─────┘
         ▼
   Build Final Report (HTML)
         │
         ▼
   Email Campaign Report
```

## Tech Stack

- **n8n** — workflow orchestration
- **OpenAI GPT-4o-mini** — content generation and judging
- **OpenAI GPT-Image-1** — promotional image generation
- **GPT-4.1** — image quality analysis

## Setup

### 1. Import the workflow
In n8n, go to **Workflows → Import** and upload `workflow.json`.

### 2. Add credentials
You need two credentials configured in n8n:

| Credential | Used by |
|---|---|
| OpenAI API key | All AI agent nodes |

### 3. Install the document converter community node
This workflow uses `@mazix/n8n-nodes-converter-documents` to extract text from uploaded files. Install it via **Settings → Community Nodes**.

### 4. Run it
Activate the workflow and open the form URL shown in the **On form submission** node. Upload your product documents, enter your email, and submit.

## Key Design Decisions

- **Context first** — a dedicated Context Builder Agent reads all documents and extracts a structured `campaign_context` object before any content is generated. This ensures all downstream agents work from the same grounded understanding.
- **Prompt engineering layer** — a Prompt Engineer Agent generates channel-specific instructions so each generator (LinkedIn, Email, Blog) gets a tailored brief rather than a generic prompt.
- **AI quality gate** — a Judge agent scores every piece of content and triggers an Auto-Improve pass if anything scores below threshold, without human intervention.
- **Clean HTML output** — the final report is rendered as a styled HTML email with score visualisations, not raw JSON.
