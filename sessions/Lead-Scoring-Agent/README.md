
---

# 📌 Workflow: Lead Qualifier Agent Webhook → Code → OpenAI → Gmail

---

## 1. **Webhook Node – Capture Data**

* Drag in a **Webhook** node.
* Set **HTTP Method** = `POST`.
* Path = `capture-leads`.
* This is where you’ll POST lead data like:

  ```json
  {
    "name": "Sara",
    "email": "sara@example.com",
    "message": "I want to know more about pricing"
  }
  ```

---

## 2. **Edit Fields – Normalize Input**

* Add a **Set (Edit Fields)** node after the Webhook.
* Map fields:

  * `name = {{$json.body.name}}`
  * `email = {{$json.body.email}}`
  * `message = {{$json.body.message}}`
  * `status = new-lead`

This cleans the payload for downstream nodes.

---

## 3. **Code Node – Simple Lead Scoring**

* Add a **Code** node.
* Use this JavaScript:

  ```javascript
  let score = $json.message.toLowerCase().includes("pricing") ? 90 : 60;
  return [{
    ...$json,
    score
  }];
  ```
* This assigns `score = 90` if the lead asks about pricing, else `60`.

---

## 4. **Message a Model – OpenAI Email Draft**

* Add **Message a Model** (OpenAI) node.
* Model = `gpt-4` (or any you’ve connected).
* Prompt:

  ```
  You are a sales assistant. Based on the following customer message, write a short professional email reply.  
  - Format the email using <p> tags for paragraphs.  
  - Keep it polite and professional.  
  - End with: <p>Best regards,<br>BotCampus Support</p>  

  Customer message: "{{$json.message}}"  
  Customer name: "{{$json.name}}"
  ```
* Output → gives you an HTML reply.

---

## 5. **Edit Fields – Rename AI Output**

* Add another **Set (Edit Fields)** node after the OpenAI node.
* Add field:

  * `ai_reply = {{$json.message.content}}`
* This avoids overwriting your original `message` field.

---

## 6. **Merge Node – Combine Data + AI Reply**

* Add a **Merge** node.
* Connect:

  * Input 1 → Code node (scored lead)
  * Input 2 → Set (ai\_reply) node
* Mode = **Combine by Position**.
* Result = one item containing name, email, score, original message, and `ai_reply`.

---

## 7. **Gmail Node – Send Email**

* Add a **Gmail** node.
* Operation = **Send a Message**.
* Fields:

  * **To** → `={{$json.email}}`
  * **Subject** → `"Regarding your inquiry"`
  * **Email Type** → HTML
  * **Message** → `={{$json.ai_reply}}`

Now Gmail will send the AI-crafted email directly to the customer.

---

## 8. **(Optional) Webhook Response**

* Add **Respond to Webhook** at the end if you want to confirm back to the API caller.
* Response body example:

  ```json
  {
    "status": "email_sent",
    "to": "={{$json.email}}",
    "score": "={{$json.score}}"
  }
  ```

---

# ✅ End Result

* **Input** → Customer submits name, email, and message.
* **Processing** → Workflow scores the lead, drafts a professional HTML email with GPT.
* **Output** → Email is sent via Gmail, optionally returns a confirmation JSON.

---
