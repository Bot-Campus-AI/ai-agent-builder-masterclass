
---

## 🧑‍💼 Customer Onboarding Workflow

👉 **Automate the journey from signup form → database → welcome email → Slack team alert → Wait for 24 hrs → Send a Followup Email → Slack alert for Followup.**

---

### 🎯 Goal

Show how n8n can take new customer signups from a form, validate the data, store it, send a welcome email, and alert the internal team.

---

## 🔧 Prerequisites

1. **MySQL Database Ready**

   * Table for storing customers:

     ```sql
     CREATE TABLE customers (
         id INT AUTO_INCREMENT PRIMARY KEY,
         name VARCHAR(100),
         email VARCHAR(100),
         company VARCHAR(100),
         created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
     );
     ```

2. **Slack Credentials**

   * Slack App with `chat:write` scope.
   * Bot invited to your channel (e.g., `#alerts`).

3. **Gmail Credentials**

   * Gmail OAuth2 credential configured in n8n.

4. **Form/Webhook Source**

   * Google Form or any form builder that can send a **Webhook POST** to n8n.
   * Fields expected: `name`, `email`, `company`.

---

## 🪜 Step-by-Step Demo

1. **Webhook Trigger – Capture Form Submission**

   * Add a **Webhook Node**.
   * Method: `POST`
   * Path: `/customer-signup`
   * Fields captured:

     ```json
     {
       "name": "Rahul Sharma",
       "email": "rahul.sharma@accenture.com",
       "company": "Accenture"
     }
     ```

---

2. **IF Node – Validate Email Format**

   * Condition: `email` contains `"@"`.
   * **True Path** → continue.
   * **False Path** → Slack alert:

     ```
     🚨 Invalid signup received.
     Name: {{$json.name}}
     Email: {{$json.email}}
     Company: {{$json.company}}
     ```

---

3. **MySQL Node – Insert Customer**

   * Operation: `Insert`.
   * Table: `customers`.
   * Fields: `name`, `email`, `company`.
   * SQL executed:

     ```sql
     INSERT INTO customers (name, email, company)
     VALUES ({{$json.name}}, {{$json.email}}, {{$json.company}});
     ```

---

4. **Gmail Node – Send Welcome Email**

   * Operation: `Send Email`.
   * To: `{{$json.email}}`.
   * Subject: `Welcome to BotCampus AI, {{$json.name}}!`
   * Body:

     ```
     Hi {{$json.name}},

     Welcome to BotCampus AI 🎉
     We’re excited to have {{$json.company}} on board.

     Stay tuned for more updates.

     – BotCampus AI Team
     ```

---

5. **Slack Node – Team Alert**

   * Operation: `Send Message`.
   * Channel: `#alerts`.
   * Message:

     ```
     🎉 New Customer Onboarded!
     👤 Name: {{$json.name}}
     🏢 Company: {{$json.company}}
     📧 Email: {{$json.email}}
     ```
---

6. **Wait Node – Pause for 24 Hours** 🕒

   * Add a **Wait Node** after Slack.
   * Configure: **Wait → Relative → 24 hours**.
   * This pauses the workflow per customer.

---

7. **Gmail Node – Send Follow-Up Email**

   * Subject: `Checking In – How’s it going, {{$json.name}}?`
   * Body:

     ```
     Hi {{$json.name}},

     It’s been a day since you joined BotCampus AI 🚀  
     Just checking in to see how you’re finding things.

     If you have any questions, reply directly — we’d love to help.  

     – BotCampus AI Team
     ```

---

8. **Slack Node – Follow-Up Sent Alert** ✅

   * Message to `#alerts`:

     ```
     ✅ Follow-Up Email Sent!
     👤 Name: {{$json.name}}
     🏢 Company: {{$json.company}}
     📧 Email: {{$json.email}}
     Time: {{$now}}
     ```

---
### 📌 Real-Time Use Cases

* **Sales Onboarding** → Instantly notify sales team when a new customer signs up.
* **Training Enrollment** → Auto-welcome students, store their info, and notify instructors.
* **Freemium SaaS Signup** → Save user details in DB + send welcome email + alert CS team.

---

✅ **Takeaway:** This workflow shows n8n as the glue across **Forms → Validation → Database → Communication Channels**.

---

