
---

## 🔄 Loop Over Items – Process Leads One by One 👤

👉 **Handle multiple leads sequentially**, so you can personalize messages or push them safely into external systems without hitting rate limits.

---

### 🎯 Goal

Show how the **Loop Over Items** node lets you **process each lead individually** (send email, push to CRM, update sheet) instead of dumping them all at once.

---

### 🪜 Step-by-Step Demo

1. **Manual Trigger**

   * Start with a Manual Trigger.

---

2. **Code Node – Seed Demo Leads**

   * Add a Code Node called `Seed Leads`.

   * Paste this code:

     ```javascript
     return [
       { json: { name: "Rahul",  email: "rahul@deloitte.com" } },
       { json: { name: "Priya",  email: "priya@accenture.com" } },
       { json: { name: "Aamir",  email: "aamir@cognizant.com" } },
       { json: { name: "Fatima", email: "fatima@tcs.com" } }
     ];
     ```

   * Execute once → you should see **4 separate items** in the output.

---

3. **Loop Over Items**

   * Add a **Loop Over Items** node after the Code Node.
   * Leave defaults as is.
   * Important: Connect the **loop output** (not done) to the next step, so each lead is handled **individually**.

---

4. **Replace Me – Real Action Node**

   * Add a placeholder node (it might say `Replace Me`).

   * Replace this with a **real action**, depending on your demo:

   - **Option A: Gmail Node** → send personalized emails:

     ```
     To: {{$json.email}}
     Subject: Welcome to BotCampus AI
     Body:
     Hi {{$json.name}},
     Thanks for joining us. Let’s get started!
     ```

   - **Option B: HTTP Request Node** → push each lead to an API (e.g., HubSpot, Zoho):

     ```json
     {
       "name": "={{$json.name}}",
       "email": "={{$json.email}}",
       "source": "BotCampus AI Workshop"
     }
     ```

   - **Option C: Google Sheets Node** → append each lead into a sheet (columns: Name, Email, Source).

   * After this node, connect it back into the **Loop Over Items** node so the workflow continues to the next lead.

---

5. **Done Path (Optional)**

   * Use the **done** output of Loop Over Items to add a final Set Node:

     ```json
     {
       "summary": "All leads processed successfully"
     }
     ```

---

## 💡 Loop Over Items – Real-Time Use Cases

* **Email Outreach** → Send personalized onboarding emails to 100+ leads, one by one.
* **CRM Updates** → Push each record into Salesforce/HubSpot without bulk API errors.
* **Event Reminders** → Loop through registrants and send reminders individually.
* **Batch Processing** → Process rows in Google Sheets sequentially instead of dumping them at once.

---

⚡ **Key Tip for Participants**:

* The `Replace Me` node is just a placeholder. Swap it with *whatever real action you want* — Gmail, Slack, HTTP API, Google Sheets.
* This pattern is one of the most **powerful in n8n** — once you get “loop” vs “done,” you can automate almost anything.

---
