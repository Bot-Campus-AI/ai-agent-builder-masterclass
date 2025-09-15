
---

## Merge Node – Combining Data from Multiple Paths 🔗

👉 **Take two branches (e.g., name + email) and merge into one.**

### 🎯 Goal

 **Merge Node** brings together two streams of data into a single output.

---

### 🪜 Step-by-Step Demo

1. **Start with a Manual Trigger**

   * Add a **Manual Trigger** node as the entry point.

2. **Branch 1 – Name Data**

   * Add a **Set Node** after the trigger.
   * Configure it to return:

     ```json
     {
       "name": "John Doe"
     }
     ```

3. **Branch 2 – Email Data**

   * From the Manual Trigger, branch off into another **Set Node**.
   * Configure it to return:

     ```json
     {
       "email": "john@example.com"
     }
     ```

4. **Merge Node**

   * Add a **Merge Node** and connect **both Set Nodes** into it.
   * In the Merge Node options:

     * Mode: **Combine** (keeps fields from both inputs together).

5. **Run Workflow**

   * Execute the workflow → Output should look like:

     ```json
     {
       "name": "John Doe",
       "email": "john@example.com"
     }
     ```

---

## 🔗 Merge Node – Real-Time Use Cases

* **Lead Consolidation** → Merge user’s form submission (name/email) with CRM lookup data.
* **Customer Records** → Merge order info with shipping details before saving to database.
* **Analytics** → Merge two API responses (e.g., marketing + sales data) into one record.

---
