
---

# Support Ticket Router (Webhook → OpenAI → Merge → Switch → Gmail/Sheets)

## Prereqs

* n8n running
* OpenAI credentials (for the **Message a model** node)
* Gmail OAuth (for the **Send a message** nodes)
* (Optional) Google Sheets OAuth if you want to log tickets

---

## 1) Webhook (capture the request)

* **Node:** Webhook
* **HTTP Method:** `POST`
* **Path:** `support-ticket`
* **Test payload (JSON):**

  ```json
  {
    "name": "Sara Ahmed",
    "email": "sara@example.com",
    "subject": "Pricing",
    "message": "Need details"
  }
  ```

> Tip: Use the “Test URL” while building, then switch to “Production URL” before activating.

---

## 2) Edit Fields (normalize to a common shape)

* **Node:** Edit Fields (Set)
* **Mode:** Manual Mapping
* **Fields to Set (String):**

  * `fromName` = `{{$json.body.name}}`
  * `fromEmail` = `{{$json.body.email}}`
  * `subject` = `{{$json.body.subject}}`
  * `body` = `{{$json.body.message}}`
* Leave **Include Other Input Fields** = Off (we’ll pass the original body separately if needed)

This gives you consistent keys for the rest of the flow.

---

## 3) Message a model (classify & summarize)

* **Node:** Message a model
* **Model:** GPT-4 (or your preferred)
* **Messages → Prompt (single message):**

  ```
  You are a support triage classifier. Categorize and summarize the ticket.

  Rules for priority:
  - Pricing or billing related inquiries = "high"
  - Technical support issues = "medium"
  - General or casual inquiries = "low"

  Return ONLY valid JSON, nothing else:
  {
    "category": "billing|tech_support|general",
    "priority": "low|medium|high",
    "summary": "<one line>"
  }

  Subject: "{{$json.subject}}"
  Body: "{{$json.body}}"
  ```
* Result will come back as a JSON **string** in `message.content`.

---

## 4) Code (parse the AI JSON)

* **Node:** Code
* **Language:** JavaScript
* **Code:**

  ```javascript
  const ai = JSON.parse($json.message.content);

  return [
    {
      json: {
        category: ai.category,
        priority: ai.priority,
        summary: ai.summary,
      }
    }
  ];
  ```

This produces clean fields: `category`, `priority`, `summary`.

---

## 5) Merge (combine AI + contact fields)

* **Node:** Merge
* **Mode:** Combine
* **Combine By:** Position
* **Number of Inputs:** 2
* **Wire it like this:**

  * **Input 1** → from the **Code** node (AI fields)
  * **Input 2** → from **Edit Fields** (fromName, fromEmail, subject, body)

**Expected output (one item):**

```json
{
  "category": "billing",
  "priority": "high",
  "summary": "Customer requests for pricing details",
  "fromName": "Sara Ahmed",
  "fromEmail": "sara@example.com",
  "subject": "Pricing",
  "body": "Need details"
}
```

---

## 6) Switch (route by category)

* **Node:** Switch
* **Settings → Number of Outputs:** `3`
* **Parameters → Routing Rules:**

  * **Output 0:** `{{$json.category}}` is equal to `billing`
  * **Output 1:** `{{$json.category}}` is equal to `tech_support`
  * **Output 2:** *(leave without a rule → this becomes the default “general” branch)*

> You now have three connectors: 0=Billing, 1=Tech Support, 2=General (else).

---

## 7) Email / log actions (one per branch)

### 7a) Billing branch (Output 0)

* **Node:** Gmail → Send a message
* **To:** your billing team email (e.g., `billing@yourdomain.com`)
* **Subject:** `={{ $json.fromName }} - Billing inquiry: {{ $json.subject }}`
* **Email Type:** HTML
* **Message (HTML):**

  ```html
  <p><b>From:</b> {{ $json.fromName }} ({{ $json.fromEmail }})</p>
  <p><b>Priority:</b> {{ $json.priority }}</p>
  <p><b>Summary:</b> {{ $json.summary }}</p>
  <p><b>Message:</b><br>{{ $json.body }}</p>
  ```

### 7b) Tech Support branch (Output 1)

* **Node:** Gmail → Send a message
* **To:** your support team (e.g., `support@yourdomain.com`)
* **Subject:** `=Support - {{ $json.subject }} ({{ $json.priority }})`
* **Message:** similar template as above.

### 7c) General branch (Output 2)

* Either send to a **general inbox** or **log to Google Sheets**:

  * **Sheets → Append row** with columns: Name, Email, Subject, Body, Category, Priority, Summary.

*(In the blueprint I provided, Output 0 & 1 send Gmail; Output 2 logs to Sheets.)*&#x20;

---

## 8) Test quickly

Use curl/Postman against your Webhook **Test URL**:

```bash
curl -X POST "<your-test-webhook-url>" \
  -H "Content-Type: application/json" \
  -d '{"name":"Sara Ahmed","email":"sara@example.com","subject":"Pricing","message":"Need details"}'
```

You should see:

* Switch → **billing** branch (priority **high**)
* Email to Billing (or a new row in Sheets if routed there)

---

## 9) Common fixes

* **“Invalid output format (index)” in Code node:** always return `[ { json: {...} } ]`.
* **Only two outputs on Switch:** increase **Number of Outputs** (Settings) to `3`.
* **AI returns non-JSON text:** tighten the prompt (as above) or wrap `JSON.parse` in try/catch and default.

---

## 10) Nice upgrades (optional)

* Auto-reply to the customer (Gmail node after Switch) using a friendly HTML template.
* Add a **Delay** + **Retry** if Gmail/Sheets rate-limits.
* Add a **Slack** notify node per branch.
* Store the whole original payload for audit (`Edit Fields` → Keep other inputs On, or write to Sheets/DB).

---
