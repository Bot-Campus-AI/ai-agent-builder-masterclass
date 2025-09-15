
---

## 5️⃣ Wait Node – Pause Until a Condition is Met 🛑

👉 **Stop the workflow mid-way and resume later.**

---

### 🎯 Goal

Show how the Wait Node lets you **pause execution** until a certain time or external event, and then continue. This is more powerful than a simple delay.

---

### 🪜 Step-by-Step Demo

1. **Manual Trigger**

   * Start with a Manual Trigger.

2. **Set Node – Before Wait**

   * Add a Set Node called `Start Message`.
   * Add a field:

     ```json
     {
       "text": "Workflow started"
     }
     ```

3. **Wait Node**

   * Add a Wait Node after `Start Message`.
   * Configure it:

     * **Wait Till Time** → pick a time (e.g., 1–2 minutes from now).
     * Or **Wait Indefinitely** → manually resume later.

4. **Set Node – After Wait**

   * Add another Set Node called `After Wait Message`.
   * Add a field:

     ```json
     {
       "text": "Workflow resumed after wait"
     }
     ```

5. **Run Workflow**

   * Execute the workflow.
   * Show that it **pauses at the Wait Node**.
   * Once the chosen time arrives (or you manually resume), the workflow continues and you see the `After Wait Message`.

---

## ⏳ Wait Node – Real-Time Use Cases

* **Lead Nurturing** → Capture a lead, then *wait 24 hours* before sending a follow-up email.
* **Cart Abandonment** → User adds to cart, *wait 2 hours*, then send reminder.
* **Payment Processing** → Wait until the payment provider sends confirmation webhook before activating a subscription.
* **Approval Workflows** → Pause workflow until manager clicks an approval link, then continue.
* **Scheduled Messages** → Capture event signup → wait until 1 day before event → send reminder.


---
