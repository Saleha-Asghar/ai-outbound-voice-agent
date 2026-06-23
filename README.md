# 🤖 AI Outbound Voice Agent — Real Estate Lead Qualifier

An end-to-end AI voice agent system that automatically calls real estate leads, qualifies them through natural conversation, and logs every call outcome to Google Sheets — with zero human involvement.

Built with VAPI, n8n, Twilio, and Google Sheets.

---

## 📽️ Demo

> [Insert your demo video link here — YouTube or LinkedIn]

---

## 🏗️ System Architecture

```
New Lead Data
      ↓
n8n HTTP Request → VAPI API
      ↓
Alex (AI Voice Agent) calls the lead via Twilio
      ↓
Conversation: identity → interest → qualify → book callback
      ↓
Call ends → VAPI fires end-of-call-report webhook
      ↓
n8n catches webhook → extracts transcript + outcome
      ↓
Google Sheets → new row logged automatically
```

---

## ✨ Features

- **Instant outbound calling** — triggers a call the moment a lead is added
- **Natural conversation flow** — confirms identity, qualifies budget/timeline/area, handles objections
- **Smart qualification detection** — expanded keyword matching across natural speech patterns, not just exact phrases
- **Auto call logging** — transcript, duration, qualification status, sentiment, summary all logged automatically
- **Duplicate-safe webhook handling** — VAPI server message filtering ensures only the final call report is processed
- **Null-safe data extraction** — missing timestamps, empty transcripts, failed recordings all handled gracefully
- **Human-in-the-loop** — specialist callback booking built into the conversation flow

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| VAPI | AI voice infrastructure + call management |
| n8n | Workflow orchestration + webhook handling |
| Twilio | Telephony / phone number |
| Google Sheets | Lead tracking + call log |
| GPT-4.1 | Conversation intelligence (via VAPI) |
| Cloudflare Tunnel | Local n8n webhook exposure |

---

## 📁 Repository Structure

```
ai-voice-agent/
│
├── n8n/
│   ├── alex_call_trigger.json        # n8n workflow: triggers outbound call
│   └── alex_call_logger.json         # n8n workflow: logs call to Sheets
│
├── vapi/
│   └── alex_system_prompt.md         # Full VAPI assistant system prompt
│
├── docs/
│   └── architecture.png              # System architecture diagram
│
└── README.md
```

---

## 🚀 Setup Guide

### Prerequisites
- [VAPI account](https://vapi.ai) — free $10 credit
- [n8n](https://n8n.io) — self-hosted or cloud
- [Twilio account](https://twilio.com) — trial or paid
- Google account (for Sheets)
- [Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps) — for local webhook exposure

---

### Step 1 — Set up VAPI Assistant

1. Log in to [vapi.ai](https://vapi.ai)
2. Create a new Assistant → name it **Alex**
3. Paste the contents of `vapi/alex_system_prompt.md` into the System Prompt field
4. Set First Message to:
   ```
   Hey, this is Alex calling from Premier Realty. Who am I speaking with today?
   ```
5. Go to **Advanced** tab → **Server Messages** → enable only:
   - ✅ `end-of-call-report`
   - ✅ `hang`
6. Set **Server URL** to your n8n webhook URL (see Step 3)
7. Connect your Twilio number under **Phone Numbers**

---

### Step 2 — Set up Google Sheets

Create a new Google Sheet with these exact column headers in Row 1:

```
Timestamp | Customer Name | Call Duration | Ended Reason | 
Qualification Status | Callback Booked | Full Transcript | Recording URL
```

---

### Step 3 — Import n8n Workflows

**Workflow 1 — Call Trigger:**
1. In n8n → New Workflow → Import from File
2. Upload `n8n/alex_call_trigger.json`
3. Update the HTTP Request node:
   - Add your VAPI API key as Bearer token
   - Replace `assistantId` with your VAPI assistant ID
   - Replace `phoneNumberId` with your VAPI phone number ID

**Workflow 2 — Call Logger:**
1. Import `n8n/alex_call_logger.json`
2. Set webhook path to `vapi-callback`
3. Connect your Google Sheets credential
4. Replace the Sheet URL with your own
5. Toggle workflow to **Active**

---

### Step 4 — Expose n8n Webhook Publicly

Run Cloudflare Tunnel:
```bash
cloudflared tunnel --url http://localhost:5678
```

Copy the generated URL and paste into VAPI Advanced → Server URL:
```
https://your-tunnel-url.trycloudflare.com/webhook/vapi-callback
```

---

### Step 5 — Test

Trigger a test call from the Call Trigger workflow. When the call ends, verify:
- ✅ Call appears in VAPI Logs
- ✅ n8n Call Logger workflow executed green
- ✅ New row appears in Google Sheet with correct data

---

## 📊 Google Sheet Output Example

| Timestamp | Customer Name | Duration | Ended Reason | Status | Callback | Summary |
|---|---|---|---|---|---|---|
| 23/06/2026, 3:41 PM | John Smith | 2m 34s | customer | Qualified | Yes | Lead interested in downtown, budget $300k, callback booked for tomorrow 3pm |
| 23/06/2026, 4:12 PM | Sarah K | 1m 10s | customer | Not Interested | No | Lead asked to be removed from list |

---

## 🔧 Customization

**Change the industry:**
Edit `vapi/alex_system_prompt.md` — replace Premier Realty with your client's business, and update the qualification questions to match their use case.

**Change qualification criteria:**
In the n8n Code node, update the `qualifiedKeywords` and `partialKeywords` arrays to match your industry's terminology.

**Add more follow-up actions:**
After the Google Sheets node, you can chain additional nodes — send an email, trigger a WhatsApp message, create a CRM record, etc.

---

## 💰 Cost Breakdown

| Item | Cost |
|---|---|
| VAPI free credit | $0 (included) |
| VAPI browser calls (demo) | $0 |
| Twilio trial | $0 |
| n8n self-hosted | $0 |
| Google Sheets | $0 |
| Cloudflare Tunnel | $0 |
| **Total for demo** | **$0** |

Production costs (per call): ~$0.10–0.15/min

---

## 📬 Contact

Built by **Saleha** — automation & AI systems freelancer.

- LinkedIn: www.linkedin.com/in/saleha-asghar-842435355
- Fiverr: [your Fiverr gig URL]
- Email: salehaasghar46@gmail.com

---
