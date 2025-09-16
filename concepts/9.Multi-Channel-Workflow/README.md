
---

## 🗄️ Database → Slack Integration 🚀

👉 **Query real data from MySQL/Postgres and send instant Slack notifications.**

---

### 🎯 Goal

Show how n8n can:

1. Connect to a database (MySQL/Postgres).
2. Query **new customer records**.
3. Send them into Slack as formatted alerts.
4. *(Optional)* Log the notification into another DB table for auditing.

---

### 🪜 Step-by-Step Demo

1. **Trigger Node**

   * Use **Manual Trigger** for demo.
   * In real-world → this could be scheduled with a **Cron Node** (e.g., check every 5 mins).

---

2. **Database Node – Fetch Customers**

   * Add a **MySQL/Postgres Node**.
   * Configure DB credentials (host, user, password, db).
   * Query:

   ```sql
   SELECT id, first_name, last_name, email, company
   FROM customers
   WHERE created_at >= NOW() - INTERVAL '1 DAY';
   ```

   * This pulls all customers added in the **last 24 hours**.

---

3. **Rename Keys (Data Cleaning)**

   * Use **Rename Keys Node**.
   * Clean DB-style fields → Slack-friendly keys:

     * `first_name` → `FirstName`
     * `last_name` → `LastName`
     * `company` → `Company`

---

4. **Slack Node – Send Notification**

   * Add a **Slack Node**.
   * Operation: **Send Message**.
   * Channel: `#alerts`.
   * Message template:

   ```
   🎉 New Customer Added!  
   👤 Name: {{$json["FirstName"]}} {{$json["LastName"]}}  
   🏢 Company: {{$json["Company"]}}  
   📧 Email: {{$json["email"]}}
   ```

---

5. **(Optional) Insert Into Logs Table**

   * Add another **Database Node** (MySQL/Postgres).
   * Query:

   ```sql
   INSERT INTO notifications_log (customer_id, channel, sent_at)
   VALUES ({{$json["id"]}}, 'Slack', NOW());
   ```

   * This keeps an audit trail of every Slack message sent.

---

### ⚡ Real-Time Use Cases

* **Sales Ops** → Get notified instantly in Slack when new customers are added.
* **Support Teams** → Alert when new high-value accounts are onboarded.
* **Audit/Compliance** → Keep log records of all notifications in DB.

---

✅ **Takeaway**: This demo shows that n8n isn’t just for APIs — it can sit **between backend DBs and modern tools like Slack**, acting as middleware for **automation + monitoring**.

---
