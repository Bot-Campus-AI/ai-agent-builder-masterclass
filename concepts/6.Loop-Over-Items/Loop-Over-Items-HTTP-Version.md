
## 🔄 Loop Over Items – HTTP API Version 🌐

👉 **POST each lead to an API/CRM endpoint individually.**

---

### 🎯 Goal

Use **Code → Loop Over Items → HTTP Request** to push each lead to an external service (demo with httpbin.org; swap with any CRM later).

---

### 🪜 Step-by-Step Demo

1. **Manual Trigger**

   * Start with a Manual Trigger.

---

2. **Code Node – Seed Demo Leads**

   * Same `Seed Leads` code as above:

     ```javascript
     return [
       { json: { name: "Rahul",  email: "rahul@deloitte.co"   } },
       { json: { name: "Priya",  email: "priya@accenture.co"  } },
       { json: { name: "Aamir",  email: "aamir@cognizant.co"  } },
       { json: { name: "Fatima", email: "fatima@tcs.co"       } }
     ];
     ```

---

3. **Loop Over Items**

   * Add **Loop Over Items** after `Seed Leads`.
   * Use **loop** for the per-item path.

---

4. **HTTP Request – POST per lead**

   * Replace the placeholder with **HTTP Request**.
   * **Method**: *POST*
   * **URL**: `https://httpbin.org/post`  *(safe echo API for demos)*
   * **Authentication**: *None* (for httpbin).
     *(Swap to your CRM auth when ready.)*
   * **Body** → *JSON*:

     ```json
     {
       "name": "={{$json.name}}",
       "email": "={{$json.email}}",
       "source": "BotCampus AI Workshop"
     }
     ```
   * Connect **Loop Over Items (loop)** → **HTTP Request** → back into **Loop Over Items** (input).

---

5. **(Optional) Wait inside the loop**

   * Add **Wait (3–5s)** between **loop** and **HTTP** to respect API limits.

---

6. **Done Path (optional)**

   * From **Loop Over Items (done)** → **Set**:

     ```json
     { "summary": "All leads pushed to API" }
     ```

---

7. **Run Workflow**

   * Execute → see 4 POSTs in sequence.
   * In each HTTP node execution, look at:

     * `json.data` (the JSON you sent)
     * `json.headers` (metadata)
     * `json.url` (the endpoint called)

---

## 💡 HTTP Version – Real-Time Use Cases

* **CRM ingestion** → HubSpot/Salesforce/Zoho in a controlled stream.
* **Verification/enrichment** → call validation APIs per lead.
* **Webhooks-as-destination** → POST each lead to your own service.

---

## 🧠 Pattern recap (both versions)

* **Code** creates clean, multiple items.
* **Loop Over Items** guarantees **one-by-one** processing.
* The old **“Replace Me”** spot = your **real action** (Gmail, HTTP, Sheets, Slack).
* Add **Wait** inside the loop for rate-limited targets.
* Use **done** for a final summary or notification.

---
