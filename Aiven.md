# Migrating a Local MySQL Database to Aiven and Deploying with Render

## Table of Contents

- [Step 1: Create a Database in Aiven](#step-1-create-a-database-in-aiven)
- [Step 2: Note the Connection Details](#step-2-note-the-connection-details)
- [Step 3: Download the CA Certificate](#step-3-download-the-ca-certificate)
- [Step 4: Export Your Local Database](#step-4-export-your-local-database)
- [Step 5: Connect MySQL Workbench to Aiven](#step-5-connect-mysql-workbench-to-aiven)
- [Step 6: Import the SQL File](#step-6-import-the-sql-file)
- [Step 7: Verify the Migration](#step-7-verify-the-migration)
- [Step 8: Update Your Flask Application](#step-8-update-your-flask-application)
- [Step 9: Deploy to Render](#step-9-deploy-to-render)

---

# Step 1: Create a Database in Aiven

1. Open your **Aiven MySQL** service.
2. Navigate to:

   ```
   Connect
       └── Databases
   ```

3. Click **Create Database**.
4. Enter a database name, for example:

   ```
   mydatabase
   ```

5. Click **Add Database**.

---

# Step 2: Note the Connection Details

From the **Overview** page, copy the following details:

| Parameter | Example |
|-----------|---------|
| Host | `mysql-xxxxxxxx.aivencloud.com` |
| Port | `12345` |
| Username | `avnadmin` |
| Password | `********` |
| Database | `mydatabase` |

> **Note:** Keep these values safe. They will be required while connecting from MySQL Workbench, Flask, and Render.

---

# Step 3: Download the CA Certificate

Aiven requires **SSL/TLS** connections.

1. Open the **Overview** page.
2. Locate **CA Certificate**.
3. Click **Download**.
4. Save it as:

   ```
   ca.pem
   ```

You'll use this certificate while connecting securely to the database.

---

# Step 4: Export Your Local Database

Open **MySQL Workbench**.

Navigate to:

```
Server
    └── Data Export
```

Then:

1. Select your local database.
2. Choose:

   ```
   Export to Self-Contained File
   ```

3. Save the file, for example:

   ```
   C:\Users\YourName\Desktop\database.sql
   ```

4. Click:

   ```
   Start Export
   ```

---

# Step 5: Connect MySQL Workbench to Aiven

Open **MySQL Workbench**.

Create a new connection by clicking the **+** icon.

Fill in the following:

| Setting | Value |
|---------|------|
| Connection Name | Aiven |
| Hostname | `<Host from Aiven>` |
| Port | `<Port from Aiven>` |
| Username | `avnadmin` |

Click:

```
Store in Vault
```

or

```
Store Password
```

Enter your Aiven password.

## Configure SSL

Go to the **SSL** tab.

Set:

| Option | Value |
|---------|------|
| Use SSL | Require and Verify CA |
| SSL CA File | Browse → `ca.pem` |

Click:

```
Test Connection
```

If the connection succeeds, click **OK**.

---

# Step 6: Import the SQL File

Open the newly created **Aiven** connection.

Navigate to:

```
Server
    └── Data Import
```

Choose:

```
Import from Self-Contained File
```

Select:

```
database.sql
```

Choose the target schema:

```
mydatabase
```

Click:

```
Start Import
```

Wait until the import finishes successfully.

---

# Step 7: Verify the Migration

Refresh the **Schemas** section.

You should now see all your tables, for example:

```
users
orders
products
...
```

Run a sample query:

```sql
SELECT * FROM users;
```

If records are returned, your migration was successful.

---

# Step 8: Update Your Flask Application

Replace your local MySQL connection:

```python
host="localhost"
```

with your Aiven connection details:

```python
host="mysql-xxxxxxxx.aivencloud.com"
port=12345
user="avnadmin"
password="your_password"
database="mydatabase"
```

### Example

```python
import mysql.connector

connection = mysql.connector.connect(
    host="mysql-xxxxxxxx.aivencloud.com",
    port=12345,
    user="avnadmin",
    password="your_password",
    database="mydatabase",
    ssl_ca="ca.pem"
)
```

> **Note:** Since Aiven requires SSL, include the `ssl_ca` parameter (or equivalent SSL configuration supported by your MySQL client).

---

# Step 9: Deploy to Render

In your **Render** service, add the following environment variables.

| Variable | Value |
|----------|------|
| DB_HOST | mysql-xxxxxxxx.aivencloud.com |
| DB_PORT | 12345 |
| DB_USER | avnadmin |
| DB_PASSWORD | your_password |
| DB_NAME | mydatabase |

## Access Environment Variables in Flask

Instead of hardcoding credentials, use environment variables.

```python
import os
import mysql.connector

connection = mysql.connector.connect(
    host=os.getenv("DB_HOST"),
    port=int(os.getenv("DB_PORT")),
    user=os.getenv("DB_USER"),
    password=os.getenv("DB_PASSWORD"),
    database=os.getenv("DB_NAME"),
    ssl_ca="ca.pem"
)
```

---

# Step 10: Configure Flask for Aiven MySQL (Port & SSL)

After migrating your database to Aiven, you'll need to update your Flask application's MySQL configuration.

---

## 1. Configure the Database Connection

If you're using **Flask-MySQLdb**, your application may already contain the following configuration:

```python
app.config['MYSQL_HOST'] = os.environ['MYSQL_HOST']
app.config['MYSQL_USER'] = os.environ['MYSQL_USER']
app.config['MYSQL_PASSWORD'] = os.environ['MYSQL_PASSWORD']
app.config['MYSQL_DB'] = os.environ['MYSQL_DB']
```

### Add the MySQL Port

Aiven uses a custom port instead of the default MySQL port (3306), so add:

```python
app.config['MYSQL_PORT'] = int(os.environ['MYSQL_PORT'])
```

Your configuration should now look like:

```python
app.config['MYSQL_HOST'] = os.environ['MYSQL_HOST']
app.config['MYSQL_USER'] = os.environ['MYSQL_USER']
app.config['MYSQL_PASSWORD'] = os.environ['MYSQL_PASSWORD']
app.config['MYSQL_DB'] = os.environ['MYSQL_DB']
app.config['MYSQL_PORT'] = int(os.environ['MYSQL_PORT'])
```

---

## 2. Configure SSL (Required for Aiven)

Aiven requires all MySQL connections to use **SSL/TLS**.

If your application uses:

```python
from flask_mysqldb import MySQL
```

you must also configure the SSL certificate.

After downloading the **CA Certificate** (`ca.pem`) from the Aiven dashboard, add the following configuration:

```python
app.config['MYSQL_CUSTOM_OPTIONS'] = {
    "ssl": {
        "ca": "ca.pem"
    }
}
```

> **Important:** Ensure the `ca.pem` file is included in your project and is accessible at runtime. If it is stored in another location, update the path accordingly.

---

## 3. Complete Flask Configuration Example

```python
import os

app.config['MYSQL_HOST'] = os.environ['MYSQL_HOST']
app.config['MYSQL_USER'] = os.environ['MYSQL_USER']
app.config['MYSQL_PASSWORD'] = os.environ['MYSQL_PASSWORD']
app.config['MYSQL_DB'] = os.environ['MYSQL_DB']
app.config['MYSQL_PORT'] = int(os.environ['MYSQL_PORT'])

app.config['MYSQL_CUSTOM_OPTIONS'] = {
    "ssl": {
        "ca": "ca.pem"
    }
}
```

---

## 4. Ensure Render Environment Variables Are Set

Add the following environment variables to your **Render** service:

| Variable | Value |
|----------|------|
| `MYSQL_HOST` | `mysql-xxxxxxxx.aivencloud.com` |
| `MYSQL_PORT` | `12345` |
| `MYSQL_USER` | `avnadmin` |
| `MYSQL_PASSWORD` | `your_password` |
| `MYSQL_DB` | `mydatabase` |

---

## 5. Place the CA Certificate

Your project structure should look similar to:

```
project/
│
├── app.py
├── ca.pem
├── requirements.txt
├── render.yaml (optional)
├── static/
├── templates/
└── ...
```

If the certificate is inside another folder, specify its relative or absolute path:

```python
app.config['MYSQL_CUSTOM_OPTIONS'] = {
    "ssl": {
        "ca": "certificates/ca.pem"
    }
}
```

---

## Checklist

- ✅ Added `MYSQL_PORT` configuration
- ✅ Downloaded the Aiven `ca.pem` certificate
- ✅ Configured SSL using `MYSQL_CUSTOM_OPTIONS`
- ✅ Added MySQL environment variables in Render
- ✅ Included `ca.pem` in the project before deployment

> **Note:** Without the SSL configuration and CA certificate, connections to an Aiven MySQL database will typically fail because Aiven enforces secure (TLS/SSL) connections.


# Migration Checklist

- ✅ Create a database in Aiven
- ✅ Copy host, port, username, password, and database name
- ✅ Download the `ca.pem` SSL certificate
- ✅ Export the local MySQL database
- ✅ Connect MySQL Workbench to Aiven using SSL
- ✅ Import the SQL file
- ✅ Verify all tables and data
- ✅ Update the Flask application's database configuration
- ✅ Add environment variables in Render
- ✅ Redeploy the application

---

# Result

Your application is now configured to:

- Use **Aiven MySQL** as the cloud database.
- Connect securely using **SSL/TLS**.
- Read database credentials from **Render environment variables**.
- Run without relying on a local MySQL server.
