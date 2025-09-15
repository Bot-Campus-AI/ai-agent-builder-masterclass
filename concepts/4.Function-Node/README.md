
---

## Function Node – Adding Custom Logic 🧑‍💻

👉 **Use simple JavaScript inside n8n to manipulate or generate data.**

---

### 🎯 Goal

Function Node can transform data in ways that Set / Merge cannot — for example, calculating a new field.

---

### 🪜 Step-by-Step Demo

1. **Start with Manual Trigger**

   * Begin the workflow with a **Manual Trigger**.

2. **Add a Set Node for Input**

   * Create a Set node with fields:

     ```json
     {
       "num1": 5,
       "num2": 10
     }
     ```

3. **Add a Function Node**

   * Drag in a **Function Node**.
   * Replace the default code with:

     ```javascript
     return [{
       num1: $json.num1,
       num2: $json.num2,
       sum: $json.num1 + $json.num2,
       product: $json.num1 * $json.num2
     }];
     ```

4. **Run Workflow**

   * Execute the workflow → You’ll see:

     ```json
     {
       "num1": 5,
       "num2": 10,
       "sum": 15,
       "product": 50
     }
     ```

---

## 🧑‍💻 Function Node – Real-Time Use Cases

* **Custom Calculations** → Calculate discounts, taxes, or commissions dynamically.
* **Data Transformation** → Reformat date fields (`2025-09-15 → 15/09/2025`).
* **Custom Logic** → Add conditional fields (e.g., “status = VIP” if spend > \$5000).

---
