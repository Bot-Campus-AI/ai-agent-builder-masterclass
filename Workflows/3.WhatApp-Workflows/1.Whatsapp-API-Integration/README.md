
---

# 🚀 Guide: WhatsApp Messaging Workflow with n8n

### 🎯 Goal

Send a **WhatsApp message** using Meta’s Cloud API and **log the status into Slack** automatically.

---

## 🔧 Prerequisites

1. **Meta for Developers account**

   * Create a Business App.
   * Add **WhatsApp** as a product.
   * Get **Access Token, Phone Number ID, and WhatsApp Business Account ID**.

2. **n8n Installed**

   * Running locally (e.g., `http://localhost:5678`).
   * Access to **HTTP Request Node** and **Slack Node**.

3. **Slack App or API Token**

   * A bot or app with permission to post messages into a channel.
   * Slack channel ID where messages will be posted.

---

## 🛠️ Step 1: Add Trigger Node

* Node: **When clicking ‘Execute workflow’**
* Purpose: Runs the workflow manually for testing.
* In production, this can be replaced with **Webhook Trigger**.

---

## 🛠️ Step 2: Configure WhatsApp HTTP Request

* Node: **HTTP Request**
* Method: `POST`
* URL:

  ```
  https://graph.facebook.com/v17.0/<Phone-Number-ID>/messages
  ```
* Authentication: **Bearer Token** (Meta’s Access Token).

**Body (JSON):**

```json
{
  "messaging_product": "whatsapp",
  "to": "917995808161",
  "type": "template",
  "template": {
    "name": "hello_world",
    "language": {
      "code": "en_US"
    }
  }
}
```

✅ This sends the standard **hello\_world** WhatsApp template message.

---

## 🛠️ Step 3: Forward Response to Slack

* Node: **Slack → Send a message**
* Target: Choose a Slack **channel** (e.g., `#alerts`).
* Message Text:

  ```
  The WhatsApp Message has been sent successfully. 
  Status: {{ $json.messages[0].message_status }}
  ```
* This dynamically displays whether the message was **sent/delivered/failed**.

---

## 🛠️ Step 4: Connect Nodes

1. `When clicking 'Execute workflow'` → **HTTP Request**
2. **HTTP Request** → **Send a message (Slack)**

---

## ▶️ Step 5: Run & Test

1. Click **Execute Workflow** in n8n.
2. Check WhatsApp inbox of the target number.
3. Verify Slack channel receives status update.

---
