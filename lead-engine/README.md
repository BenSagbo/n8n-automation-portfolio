# Lead Engine: Qualify and Route

An n8n workflow that qualifies inbound leads with AI and routes each one automatically, so no lead sits waiting for a reply.

**Demo video (3 min):** https://drive.google.com/file/d/1kf6YI2EL6wnd5ZZgwLiL1MJMBRGcjvx3/view?usp=drive_link

![Workflow](Screenshot%202026-11-24%20195608.png)

## How it works

1. **Webhook** receives a new form submission (Tally).
2. **Clean Lead** (JavaScript) normalizes the name, email, business type, budget, and need.
3. **Score with Claude** rates the lead on fit, clarity, urgency and budget, and returns structured JSON: hot, warm, or cold, a one-line reason, and a needs_review flag for spam or unclear input.
4. **Parse Score** (JavaScript) validates the AI output. If it can't be parsed, the lead defaults to human review instead of being misrouted.
5. **Upsert Contact** writes the lead and its score to HubSpot, keyed on email, so a repeat submission updates the contact instead of duplicating it.
6. **Switch** routes the lead:
   - **Review:** Slack alert to a person with the lead details and the AI's note
   - **Hot:** email with a booking link
   - **Warm:** email asking a couple of qualifying questions, plus the booking link
   - **Cold:** stays in HubSpot, tagged cold for later nurture
7. **Log to Supabase** records every lead and its score for an audit trail.

If any step fails, a separate error workflow sends a Slack alert so nothing fails silently.

## Tools

n8n, Claude API (Anthropic), HubSpot API, Gmail, Slack, Supabase, Tally

## Using this workflow

Import `lead-engine-qualify-and-route.json` into n8n, then connect your own credentials for Anthropic, HubSpot, Gmail, Slack, and Supabase, and replace the placeholder values (`YOUR_LINK`, `YOUR_SLACK_CHANNEL_ID`).
