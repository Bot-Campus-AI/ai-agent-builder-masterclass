
---

## 🔄 Loop Over Items – Gmail Version ✉️

👉 **Send a personalized email to each lead, one by one.**

---

### 🎯 Goal

Use **Code → Loop Over Items → Gmail** so each lead gets an individual email (safe for rate limits; easy to personalize).

---

### 🪜 Step-by-Step Demo

1. **Manual Trigger**

   * Start with a Manual Trigger.

---

2. **Code Node – Seed Demo Leads**

   * Add a Code node named `Seed Leads`.

   * Paste:

     ```javascript
     return [
       { json: { name: "Rahul",  email: "rahul@deloitte.com"   } },
       { json: { name: "Priya",  email: "priya@accenture.com"  } },
       { json: { name: "Aamir",  email: "aamir@cognizant.com"  } },
       { json: { name: "Fatima", email: "fatima@tcs.com"       } }
     ];
     ```

   * Execute once → confirm **4 items**.

---

3. **Loop Over Items**

   * Add **Loop Over Items** after `Seed Leads`.
   * Leave defaults.
   * You’ll use **loop** (per-item) for the next step and **done** for an optional summary.

---

4. **Gmail Node – Send per lead**

   * Replace the placeholder with **Gmail**.

   * **Operation**: *Send*

   * **To**: `={{$json.email}}`

   * **Subject**: `Welcome to BotCampus AI, {{$json.name}}`

   * **Text**:

     ```
     Hi {{$json.name}},
     Thanks for your interest in BotCampus AI.
     We'll follow up with next steps shortly.
     ```

   * Connect **Loop Over Items (loop)** → **Gmail** → back into **Loop Over Items** (input).

   > If you don’t have Gmail credentials yet, skip to the HTTP version below for a no-auth demo.

---

5. **(Optional) Wait inside the loop**

   * Between **Loop (loop)** and **Gmail**, insert a **Wait** node:

     * After Time Interval → **5 seconds**
   * This spaces out emails to avoid throttling.

---

6. **Done Path (optional)**

   * From **Loop Over Items (done)** → **Set**:

     ```json
     { "summary": "All leads emailed successfully" }
     ```

---

7. **Run Workflow**

   * Execute → watch Gmail fire once per lead in order: Rahul → Priya → Aamir → Fatima.

---

## 💡 Gmail Version – Real-Time Use Cases

* **Onboarding/Nurture** → one-by-one welcome emails after sign-up.
* **Event reminders** → loop registrants; send personalized reminders.
* **Follow-ups** → drip messages with a **Wait** between touches.

---

## ⚠️ Gotchas (Gmail)

* Use **loop**, not **done**, for the per-item path.
* Add a **Wait** if you see rate-limit errors.
* If your action node returns no item, add a tiny **Set** after it to pass the current item onward.

---
