
---

# 🤖 Guide: WhatsApp Auto-Responder Agent with n8n

### 🎯 Goal

Build an **AI-powered WhatsApp agent** that listens to incoming messages, responds using **OpenAI GPT**, and remembers context during the conversation.

---

## 🔧 Prerequisites

1. **Meta for Developers account**

   * Create a Business App.
   * Add **WhatsApp** as a product.
   * Get **Access Token, Phone Number ID, and WhatsApp Business Account ID**.
   * Add your personal number as a test recipient.

2. **n8n Cloud Trail Subscription**

   * Free Trail Account of n8n Cloud.

3. **OpenAI API Key**

   * Create an API key at [https://platform.openai.com](https://platform.openai.com).
   * Add it in n8n under **Credentials → OpenAI**.

---

## 🛠️ Step 1: Add WhatsApp Trigger Node

* Node: **WhatsApp Trigger**
* Purpose: Captures **incoming WhatsApp messages** from Meta Cloud API.
* Config:

  * Link to your **WhatsApp OAuth/Token credentials**.
  * Meta will call the **Production Webhook URL** to deliver messages.

---

## 🛠️ Step 2: Configure AI Agent

* Node: **AI Agent**
* Purpose: Takes incoming text and generates a reply.
* Setup:

  * **Prompt Source**: `Define below`
  * **Prompt Text**:

    ```n8n
    {{$json.messages[0].text.body}}
    ```
  * **System Message**:

    ```
    You are a helpful assistant called Max.
    Respond in a friendly tone.

    You are currently speaking to {{ $json.contacts[0].profile.name }}

    The current date and time is {{ $now.toISO() }}
    ```

---

## 🛠️ Step 3: Connect OpenAI Model

* Node: **OpenAI Chat Model**
* Model: `gpt-4.1-mini` (or `gpt-4o-mini` for faster/cheaper).
* Credentials: Select your **OpenAI API Key**.

Connect this to the **AI Agent** node via the `Language Model` input.

---

## 🛠️ Step 4: Add Conversation Memory

* Node: **Simple Memory**
* Purpose: Store context across multiple user messages.
* Config:

  * **Session Key**:

    ```n8n
    {{$('WhatsApp Trigger').item.json.contacts[0].wa_id}}
    ```

    (Ensures each user’s chat history is isolated.)
  * **Context Window**: `10` (last 10 messages remembered).

Connect this to the **AI Agent** node via the `Memory` input.

---

## 🛠️ Step 5: Send AI Reply to WhatsApp

* Node: **Send Message (WhatsApp)**
* Purpose: Sends the AI’s response back to the user.
* Config:

  * **Phone Number ID**: your WhatsApp Business Phone Number ID.
  * **Recipient**:

    ```n8n
    {{$('WhatsApp Trigger').item.json.messages[0].from}}
    ```
  * **Text Body**:

    ```n8n
    {{$json.output}}
    ```

---

## 🛠️ Step 6: Connect Nodes

1. **WhatsApp Trigger** → **AI Agent**
2. **AI Agent** → **Send Message**
3. **OpenAI Chat Model** → **AI Agent (Language Model)**
4. **Simple Memory** → **AI Agent (Memory)**

---

## ▶️ Step 7: Run & Test

1. **Activate** the workflow (green toggle in n8n).
2. In Meta Developer Console → WhatsApp → **Configuration**, paste the **Production Webhook URL** from the WhatsApp Trigger node.
3. Click **Verify & Save** with your chosen Verify Token.
4. Send a message from your test WhatsApp number.
5. The AI Agent replies automatically in WhatsApp, with context remembered across messages.

---

## 💡 Real-World Uses

* **Customer Support Agent** → Auto-answer FAQs with memory.
* **Lead Qualification Bot** → Collects info and asks follow-up questions.
* **Interactive Learning Bot** → Personalized tutoring sessions over WhatsApp.

---
