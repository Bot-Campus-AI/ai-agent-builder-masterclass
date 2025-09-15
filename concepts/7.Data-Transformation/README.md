
---

## 🔧 Prerequisite – Configure Slack in n8n

1. **Create a Slack Account (Free)**

   * Go to slack.com and sign up using your **Gmail**.
   * Create a **new workspace** (or join your company workspace).
   * Create a channel for alerts (e.g., `#alerts`).

2. **Create a Slack App**

   * Visit [https://api.slack.com/apps](https://api.slack.com/apps)
   * Click **Create New App → From Scratch**.
   * Name it (e.g., `BotCampus Alerts`) and select the **workspace** you’ll use with n8n.

3. **Add Bot Permissions**

   * In your app → **OAuth & Permissions** → **Scopes** → **Bot Token Scopes**.
   * Add:

     * `chat:write` (send messages)
     * `chat:write.public` *(post to public channels without being a member)*
     * `channels:read` *(helps n8n list channels in a dropdown)*
     * `groups:read` *(helps n8n list channels in a dropdown)*


4. **Install the App to the Workspace**

   * Still under **OAuth & Permissions**, click **Install to Workspace** → **Allow**.
   * Copy the **Bot User OAuth Token** (starts with `xoxb-…`).
     *This is the token you’ll paste into n8n.*

5. **Invite the Bot to Your Channel**

   * In Slack, open your alerts channel (e.g., `#alerts`).
   * Type: `/invite @BotCampus Alerts`
     *(If you added `chat:write.public`, the bot can post to public channels even if not invited — but inviting is still recommended.)*

6. **Create Slack Credentials in n8n**

   * In n8n → **Credentials → New → Slack API**.
   * Paste the `xoxb-…` **Bot User OAuth Token**.
   * Save the credential (e.g., name it `Slack – BotCampus`).

7. **Quick Smoke Test (Optional but Recommended)**

   * Build a tiny test: **Manual Trigger → Slack (Post Message)**.
   * Channel: `#alerts` (or the Channel ID).
   * Message: `Hello from n8n 👋`
   * Execute → confirm the message appears in Slack.
---

# 🔄  Data Transformation Workflow (with Slack Integration)

👉 **Why:** Before sending data to APIs (like Slack, HubSpot, CRMs), you often need to clean, reformat, or rename fields. This workflow demonstrates that.

---

## 🎯 Goal

Capture a fake lead, transform the data, and send a nicely formatted Slack message with execution details.

---

## 🪜 Step-by-Step Demo

1. **Manual Trigger**

   * Start the workflow manually.

2. **Date & Time (Get Current Date)**

   * Operation: **Get Current Date**
   * Output Field: `currentDate`

3. **Date & Time (Format Date)**

   * Operation: **Format a Date**
   * Input: `{{$json.currentDate}}`
   * Format: `MM/DD/YYYY`
   * Output Field: `formattedDate`

4. **Code Node – Build Message**
   Add a small JS snippet to create a Slack-friendly message:

   ```javascript
   // Grab the formatted date
   const formattedDate = $input.first().json.formattedDate;

   // Build message
   const message = `🔔 Workflow executed successfully at ${formattedDate}`;

   // Return for next nodes
   return [
     {
       json: { message }
     }
   ];
   ```

5. **Fake Lead (Set Node)**
   Add dummy data to simulate a new lead:

   ```json
   {
     "first_name": "Rahul",
     "last_name": "Sharma",
     "email_id": "rahul.sharma@accenture.com"
   }
   ```
* **Toggle ON** → *Include Other Input Fields*
* Set *Input Fields to Include* → **All**

6. **Rename Keys**

   * Rename `first_name → name`
   * Rename `email_id → email`
     This prepares the payload for cleaner API use.

7. **Slack – Send a Message**

   * Channel: `#alerts`
   * Message Template:

   ```
   {{$json.message}}
   🎯 New Lead Captured:
   Name: {{$json.name}} {{$json.last_name}}
   Email: {{$json.email}}
   ```

---

### 📌 Takeaways

* **Data transformation is essential.** Most APIs expect clean, standardized field names.
* **Rename Keys saves time.** Instead of fixing data later, adjust it mid-workflow.
* **Slack integration becomes more meaningful.** Rather than raw data, you now send formatted, human-friendly alerts.

---
