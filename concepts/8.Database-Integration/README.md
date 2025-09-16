
---

## 🔧 Prerequisite – MySQL Setup

Before we integrate with Slack, make sure MySQL is ready:

1. **Install Microsoft Visual C++ Redistributable latest**
    
    https://learn.microsoft.com/en-us/cpp/windows/latest-supported-vc-redist?view=msvc-170

2. **Install MySQL locally or on a server**

   * Mac/Linux: `brew install mysql` or use package manager.
   * Windows: Use MySQL installer from [mysql.com](https://dev.mysql.com/downloads/installer/).

3. **Create a Demo Database**

   ```sql
   CREATE DATABASE botcampus_demo;
   USE botcampus_demo;
   ```

4. **Create a Customers Table**

   ```sql
   CREATE TABLE customers (
       id INT AUTO_INCREMENT PRIMARY KEY,
       first_name VARCHAR(50),
       last_name VARCHAR(50),
       email VARCHAR(100),
       company VARCHAR(100),
       created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
   );
   ```

5. **Insert Sample Data**

   ```sql
   INSERT INTO customers (first_name, last_name, email, company) VALUES
   ('Rahul', 'Sharma', 'rahul.sharma@deloitte.com', 'Deloitte'),
   ('Priya', 'Nair', 'priya.nair@accenture.com', 'Accenture'),
   ('Aamir', 'Khan', 'aamir.khan@cognizant.com', 'Cognizant'),
   ('Fatima', 'Ali', 'fatima.ali@tcs.com', 'TCS');
   ```

✅ Now your MySQL is seeded with realistic customer data.

---

## 🗄️ Database → Slack Integration 🚀

👉 **Query MySQL customers and send them into Slack.**

---

### 🎯 Goal

1. Fetch **new customers** from MySQL.
2. Format their details.
3. Send Slack alerts to the team.
4. *(Optional)* Insert a log entry into another MySQL table.

---

### 🪜 Step-by-Step Guide

1. **Manual Trigger**

   * Start with a Manual Trigger.

---

2. **MySQL Node – Fetch Customers**

   * Add a **MySQL Node**.
   * Connect using DB credentials.
   * Query:

   ```sql
   SELECT id, first_name, last_name, email, company
   FROM customers
   WHERE created_at >= NOW() - INTERVAL 1 DAY;
   ```

---

3. **Rename Keys (Clean Fields)**

   * Add **Rename Keys Node**.
   * Map:

     * `first_name` → `FirstName`
     * `last_name` → `LastName`
     * `company` → `Company`

---

4. **Slack Node – Send Message**

   * Add **Slack Node**.
   * Operation: Send Message → Channel: `#alerts`.
   * Message template:

   ```
   🎉 New Customer Added!  
   👤 Name: {{$json["FirstName"]}} {{$json["LastName"]}}  
   🏢 Company: {{$json["Company"]}}  
   📧 Email: {{$json["email"]}}
   ```

---

5. **(Optional) Insert Into Logs**

   * Add another **MySQL Node**.
   * Query:

   ```sql
   INSERT INTO notifications_log (customer_id, channel, sent_at)
   VALUES ({{$json["id"]}}, 'Slack', NOW());
   ```

---

### ⚡ Real-Time Use Cases

* **Sales** → Notify team of new signups instantly.
* **Recruitment** → Ping Slack when a new candidate is entered.
* **Support** → Track high-value customers joining.

---
