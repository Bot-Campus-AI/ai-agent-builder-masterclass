
---

## 🔧 Prerequisite – Gmail Setup in n8n

Before building the multi-channel workflow, configure Gmail in n8n:

1. **Enable Gmail API**

   * Go to [Google Cloud Console](https://console.cloud.google.com/).
   * Create/select a project.
   * Enable **Gmail API**.

2. **Create OAuth Credentials**

   * Navigate to **APIs & Services → Credentials**.
   * Click **Create Credentials → OAuth Client ID**.
   * Application type → **Web Application**.
   * Add `http://localhost:5678/rest/oauth2-credential/callback` as a redirect URI (adjust port if needed).

3. **Get Client ID & Secret**

   * Copy the generated **Client ID** and **Client Secret**.

4. **Configure in n8n**

   * In n8n, go to **Credentials → New → Google Gmail OAuth2 API**.
   * Paste the Client ID and Client Secret.
   * Authenticate → Gmail is ready to use.

---

## 📡 Multi-Channel Workflow (Slack + Gmail)

👉 **Send the same event into both Slack and Gmail at once.**

---

### 🎯 Goal

Show how a single event (like a new lead or form submission) can be **fanned out to multiple channels** for maximum visibility.

---

### 🪜 Step-by-Step Guide

1. **Trigger Node**

   * Use **Manual Trigger** (for demo).
   * In real use cases → could be **Webhook**, **Google Sheets**, or **MySQL**.

---

2. **Set Node – Fake Lead Data**

   ```json
   {
     "name": "Priya Nair",
     "email": "priya.nair@accenture.com",
     "company": "Accenture"
   }
   ```

---

3. **Slack Node – Send Message**

   * Add a **Slack Node**.
   * Configure message:

   ```
   🚀 New Lead Captured!  
   👤 Name: {{$json["name"]}}  
   🏢 Company: {{$json["company"]}}  
   📧 Email: {{$json["email"]}}
   ```

---

4. **Gmail Node – Send Email**

   * Add a **Gmail Node**.
   * Operation: **Send Email**.
   * To: `sales-team@yourcompany.com`
   * Subject: `New Lead Captured - {{$json["name"]}}`
   * Body:

   ```
   A new lead has been captured:

   Name: {{$json["name"]}}
   Email: {{$json["email"]}}
   Company: {{$json["company"]}}

   Sent automatically via n8n 🚀
   ```

---

5. **Run Workflow**

   * Manual Trigger → both Slack and Gmail fire simultaneously.

---

### ⚡ Real-Time Use Cases

* **Sales + Marketing Alignment** → New leads go to Slack (for speed) **and** Gmail (for record).
* **Incident Alerts** → API error posted to Slack + email to IT manager.
* **Event Registration** → New signup → Slack notification + confirmation email.

---

✅ **Takeaway:** *One event → Many channels*. This pattern is essential for alerting, sales, and support teams.

---
