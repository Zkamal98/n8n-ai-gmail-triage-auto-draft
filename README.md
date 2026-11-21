# AI Gmail Inbox Triage, Labeling & Auto-Draft Assistant (n8n)

An end-to-end automated inbox management workflow built in **n8n**. It monitors incoming Gmail messages, classifies intent using an LLM (**Groq / Llama 3.3**), dynamically applies category labels (`Sales`, `Support`, `Billing`, `Other`), and creates threaded draft replies whenever a response is required—preserving **human-in-the-loop** review before sending.

## Architecture & Workflow Logic

`Gmail Trigger` → `Filter (If)` → `LLM Classification (Groq / Llama 3.3)` → `JSON Parser (JavaScript)` → `Dynamic Gmail Labeling` → `Reply Check (If)` → `Create Threaded Gmail Draft`

1. **Email Ingestion:** Polls Gmail for incoming messages and filters out automated/self-sent noise.
2. **AI Intent Classification:** Sends the email body and subject to an LLM with a structured prompt to determine:
   - `category`: `Sales`, `Support`, `Billing`, or `Other`
   - `needs_reply`: `true` or `false`
   - `draft_reply`: A context-aware, polite response tailored to the sender's inquiry
3. **Structured Output Parsing:** A custom JavaScript node parses and validates the LLM's JSON response and maps the category to its corresponding Gmail Label ID.
4. **Automated Labeling:** Applies the matching `AI:<Category>` label directly to the message in Gmail.
5. **Human-in-the-Loop Drafting:** If `needs_reply` evaluates to `true`, creates a properly threaded reply in Gmail **Drafts** (`threadId`, `Subject`, and `To` mapped) for final human review.

## Setup & Prerequisites

1. **n8n Instance:** Self-hosted or n8n Cloud.
2. **Credentials:**
   - **Gmail OAuth2** credential connected to your target inbox.
   - **LLM API Key** (Groq / OpenAI-compatible endpoint).
3. **Gmail Labels:** Create four labels in your Gmail account (`AI: Sales`, `AI: Support`, `AI: Billing`, `AI: Other`) and update the Label IDs in the JavaScript node to match your account's IDs.
4. **Import & Activate:** Import `workflow.json` into n8n, attach your credentials, and publish the workflow.

## Tech Stack
* **Orchestration:** n8n
* **AI / LLM:** Groq API (Llama 3.3)
* **Scripting:** JavaScript (n8n Code Node)
* **Integrations:** Gmail API (OAuth2)