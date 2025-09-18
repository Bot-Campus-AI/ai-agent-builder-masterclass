
---

## 🔧 Prerequisite – OpenRouter Setup (Free)

1. **Create an OpenRouter account**

   * Go to [openrouter.ai](https://openrouter.ai).
   * Sign up with Google or GitHub (free).

2. **Generate an API Key**

   * Once logged in → go to **Dashboard → API Keys**.
   * Click **Create Key** → copy your API key (keep it safe).
   * You’ll use this as a **Bearer token** in n8n.

3. **Pick a Free Model**

   * For this POC, use **`mistralai/mistral-7b-instruct:free`**.
   * It’s currently offered as a **free-tier** model.

4. **n8n Credentials**

   * No native “OpenRouter” credential needed → just use the **HTTP Request node**.
   * You’ll paste your API key into the `Authorization` header.

---

Perfect—here’s a **participant-ready guide** you can hand over. It gives them two working builds and finishes with **clear reasons to prefer OpenAI for tool workflows**.

---

# 🧰 Prereqs (do once)

1. **Slack**

   * Create a channel (e.g., `#alerts`) and add your bot/app.
   * In n8n: Credentials → **Slack API** → Bot token (`xoxb-…`).

2. **OpenRouter**

   * Create API key at openrouter.ai.
   * In n8n: Credentials → **OpenRouter** → paste API key.

3. **OpenAI**

   * Create API key.
   * In n8n: Credentials → **OpenAI** → paste key.

---

# A) Mistral-Tiny (Free) – “Welcome Bot → Slack” with Structured JSON

This version teaches how to make **non-tool models** cooperate with tools by forcing JSON, parsing, and routing.

### Build

1. **When chat message received** (trigger)

2. **AI Agent**

   * **Source for Prompt**: Connected Chat Trigger Node
   * **Options → System Message** (paste):

    ```
    You are a helpful assistant for BotCampus AI.
    
    Always reply ONLY with JSON in this exact shape:
    {"action":"send_to_slack","content":"<message>"}
    
    Rules:
    - Start the content with "Welcome".
    - Do NOT include Slack user IDs, channel IDs, or placeholders like <@USERID>, <#SlackChannelID>, @USERID.
    - Output plain human-readable text only.
    - Never add extra metadata, mentions, or IDs.
    - No text outside JSON.
    ```
   * (If your UI supports it) **Require Specific Output Format** and use this schema:

     ```json
     {
       "type": "object",
       "properties": {
         "action": { "type": "string", "enum": ["send_to_slack", "respond"] },
         "content": { "type": "string" }
       },
       "required": ["action","content"],
       "additionalProperties": false
     }
     ```

3. **OpenRouter Chat Model**

   * Credential: your OpenRouter key
   * **Model**: `mistralai/mistral-tiny`
   * Options: Temperature **0.4–0.7** (prefer **0.4** for JSON fidelity)

4. **Code** (to parse the JSON) → paste:

   ```js
   const raw = $json.output ?? '';
   let action = 'respond';
   let content = raw;
   try {
     const o = JSON.parse(raw);
     action = o.action || action;
     content = o.content || content;
   } catch (e) {}
   return [{ action, content }];
   ```

5. **IF**

   * Condition: `action` **equals** `send_to_slack`
   * **True** → Slack
   * **False** → (optional) reply back in chat

6. **Send a message in Slack** (True branch)

   * Channel: `#alerts` (or your channel)
   * Message: `{{$json.content}}`

### Try it

Type in chat:

> “Hi, I’m Rahul, joining as a Data Engineer. Create a good intro of me and **send it to Slack**.”

You’ll see JSON → Code parses → IF routes → Slack posts.

---

# B) OpenAI Chat – “Welcome Bot → Slack” with Native Tool Calling

Here the agent recognizes and calls the Slack tool **without** JSON scaffolding.

### Build

1. **When chat message received** (trigger)

2. **AI Agent**

   * **System Message**:

     ```
     You are a helpful assistant for BotCampus AI. Keep answers concise and friendly.
     If the user asks to post/share/send something, use the Slack tool.
     ```

3. **OpenAI Chat Model**

   * Credential: your OpenAI key
   * Model: `gpt-4o-mini` (or any available)

4. **Send a message in Slack**

   * **Connect this node to the AI Agent’s Tool port** (the agent will call it).

### Try it

> “Hi, I’m Rahul, joining as a Data Engineer. Create a good intro of me and **send it to Slack**.”

The agent writes the intro and **calls Slack directly**—no Code/IF needed.

---

# ✅ Why choose **OpenAI** for tool workflows (what you should remember)

* **Native tool/function calling**
  OpenAI chat models are designed to call tools and pass **structured arguments**. No hand-rolled JSON formats, no parsing code, no brittle IF trees.

* **Higher reliability for “do X then use tool Y”**
  When users say “send/post/share,” OpenAI models consistently pick the right tool and deliver the final text—exactly what you saw.

* **Less glue, fewer failure points**
  With OpenAI: Chat Trigger → Agent → OpenAI → Slack (1 hop to tool).
  With Mistral-Tiny: you must force JSON → parse → branch → Slack (more places to break).

* **Better adherence to tool descriptions**
  When you describe what a tool does (e.g., “Posts a message to Slack”), OpenAI follows that guidance more consistently. Fewer “weird outputs,” fewer “forgot to call the tool” moments.

* **Multi-step behavior**
  OpenAI models handle “compose, then send” as a single operation—no extra orchestration. They will write content *and* invoke the tool in one turn.

* **Time saver for production**
  For real workflows where a human says “post this to Slack/Notion/GitHub,” OpenAI’s tool semantics are the shortest route to something dependable.

> When should you still use **Mistral-Tiny**?
>
> * Free budget.
> * Pure text generation/summarization or batch jobs.
> * You’re teaching/learning structured orchestration (JSON + Code + IF).

---

## Troubleshooting (quick)

* **Slack posts empty** (Mistral path): your model didn’t produce valid JSON → lower temperature (0.4), keep the schema strict, and keep the system message visible.
* **Nothing posts**: ensure your Slack bot is in the channel and the n8n Slack node uses the same workspace token.
* **Agent waits forever**: OpenRouter rate-limit or network hiccup—reduce retries/timeouts in node options, try again.

---