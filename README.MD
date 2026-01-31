# 🧰 Database Connectivity: 🟠 Oracle, 🐘 PostgreSQL & 🐬 MySQL

This repository documents **step-by-step database connectivity** between **Oracle 11g**, **PostgreSQL**, and **MySQL** using **ODBC drivers** and **Foreign Data Wrappers (FDW)**.  
It demonstrates how to configure cross-database connections, create database links, and perform queries and joins across heterogeneous databases.

---
# 🚀 Features  
- Connect Oracle ↔ PostgreSQL
- Connect Oracle ↔ MySQL
- Sample configurations for TNS, listener, ODBC
- Scripts for testing database connections

## 🧰 Databases & Tools Used 
- 🟠 Oracle Database 11g
- 🐘 PostgreSQL 12
- 🐬 MySQL 8.0
- 🔌ODBC Drivers
  - 🔗 PostgreSQL ANSI Driver
  - 🔗 MySQL ODBC 8.0 ANSI Driver
  - ⚙️ DG4ODBC (Oracle Heterogeneous Services)
- 🧩 Foreign Data Wrappers
  - 🧩 `oracle_fdw`
  - 🧩 `odbc_fdw`

---

## 📁 Project Structure
```text
database-connectivity-oracle-postgres-mysql/
├── README.MD
├── oracle/
│   ├── listener.ora
│   ├── tnsnames.ora
│   └── setup-instructions.md
├── postgres/
│   ├── pg_hba.conf
│   └── setup-instructions.md
├── mysql/
│   └── my.cnf
├── screenshots/
│   ├── oracle-connect.png
│   ├── postgres-connect.png
│   └── mysql-connect.png
├── docs/
│   └── full-guide.pdf
└── examples/
    ├── test-oracle-connect.sql
    └── test-postgres-connect.sql
```
---
# 1️⃣ 🐘 PostgreSQL →🟠 Oracle Connectivity

## 🧩 Software Required
- 🟠 Oracle 11g
- 🐘 PostgreSQL
- 🔗 PostgreSQL ODBC ANSI Driver

## 🔌 ODBC Configuration (🐘 PostgreSQL)

1. 🖥️ Open **Control Panel**
2. 🧰 Go to **Administrative Tools**
3. ⚙️ Open **ODBC Data Source (32-bit / 64-bit)**
4. 🗂️ Select **System DSN**
5. ➕ Click **Add**
6. 🔗 Select **PostgreSQL ODBC Driver (ANSI)**
7. ⚙️ Configure:
   - 🏷️ Data Source Name: `psqlConnect`
   - 🗄️ Database: postgres
   - 🌐 Server: localhost
   - 🔢 Port: 5432
   - 👤 Username & Password
8. ✅ Click **Test**, then **Save**

---

## 🟠 Oracle Configuration

### 📝 Edit `initdg4odbc.ora`
📂 Location:
```powershell
C:\oraclexe\app\oracle\product\11.2.0\server\hs\admin
```


✏️ Modify:
```properties
HS_FDS_CONNECT_INFO = psqlConnect
HS_FDS_TRACE_LEVEL = OFF
```


💾 Save as: `initpsqlConnect.ora`


---

### 📝 Edit `listener.ora`

➕ Add:
```ini
(SID_DESC =
  (SID_NAME = psqlConnect)     
  (ORACLE_HOME = C:\oraclexe\app\oracle\product\11.2.0\server)     
  (PROGRAM = dg4odbc)
)
```
---

### 📝 Edit `tnsnames.ora`

➕ Add:
```ini
psqlConnect =
     (DESCRIPTION =
          (ADDRESS = (PROTOCOL = TCP)(HOST = localhost)(PORT = 1521))
          (CONNECT_DATA = (SID = psqlConnect))
          (HS = OK)
      )
```

---

### 🔄 Restart Oracle Listener

Run Command Prompt As Admistrative Access
```cmd
lsnrctl
```
```c
stop
```
```cmd
start
```
```cmd
exit
```
✅ Test Connection:
```powershell
tnsping psqlConnect
```
---

## 🔗 Create Database Link (Oracle)
```sql
CREATE PUBLIC DATABASE 
LINK PS_LINK CONNECT TO "postgres" 
IDENTIFIED BY "admin" USING 'psqlConnect';
```
---

## 📊 Query PostgreSQL Table from Oracle
```sql
SELECT * FROM "author"@PS_LINK;
```

---

# 2️⃣ 🐬 MySQL → 🟠 Oracle Connectivity

## 🧩 Software Required
- 🟠 Oracle 11g
- 🐬 MySQL 8.0
- 🔗 MySQL ODBC 8.0 ANSI Driver

## 🔌 ODBC Configuration (🐬 MySQL)

- 🏷️ Data Source Name: `mysqlConnect`
- 🌐 Server: `localhost`
- 🔢 Port: `3306`
- 👤 User: `root`
- 🗄️ Database: `sakila`

---

## 🟠 Oracle Configuration

### 📝 Edit `initdg4odbc.ora`
```properties
HS_FDS_CONNECT_INFO = mysqlConnect
HS_FDS_TRACE_LEVEL = OFF
```

💾 Save as: ### initmysqlConnect.ora

---

### 📝 Edit `listener.ora`
```ini
(SID_DESC =
    (SID_NAME = mysqlConnect)
    (ORACLE_HOME = C:\oraclexe\app\oracle\product\11.2.0\server)
    (PROGRAM = dg4odbc)
)
```
---

### 📝 Edit `tnsnames.ora`
```ini
mysqlConnect =
  (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = localhost)(PORT = 1521))
      (CONNECT_DATA = (SID = mysqlConnect))
      (HS = OK)
  )
```
---

## 🔗 Create Database Link
```sql
CREATE PUBLIC DATABASE LINK MYSQL_LINK 
CONNECT TO "root" 
IDENTIFIED BY "admin" USING 'mysqlConnect';
```

---

## 📊 Query MySQL Table from Oracle
```sql
SELECT * FROM "book"@MYSQL_LINK;
```

---

# 3️⃣ 🟠 Oracle → 🐘 PostgreSQL Connectivity (oracle_fdw)

## 📦 Installation
1. ⬇️ Download `oracle_fdw`
2. 📁 Copy `.dll` files to:
```powershell
C:\Program Files\PostgreSQL\12\lib
```
3. Copy extension files to:
```powershell
C:\Program Files\PostgreSQL\12\share\extension
```

---

## 🐘 PostgreSQL Configuration
```sql
CREATE EXTENSION oracle_fdw;
```
```sql
CREATE SERVER oradb 
FOREIGN DATA WRAPPER oracle_fdw 
OPTIONS (dbserver 'localhost');
```
```sql
GRANT USAGE ON FOREIGN SERVER oradb TO postgres;
```
```sql
CREATE USER MAPPING FOR postgres 
SERVER oradb 
OPTIONS (USER 'SYSTEM', PASSWORD 'admin');
```

---

## 🗂️ Create Foreign Table
```sql
CREATE FOREIGN TABLE O_AUTHOR(author_id VARCHAR(5), 
author_name VARCHAR(30)) 
SERVER oradb 
OPTIONS (table 'AUTHOR');
```

---

## 📊 Query Oracle Table from PostgreSQL
```sql
SELECT * FROM O_AUTHOR;
```


---

# 4️⃣ 🐬 MySQL → 🐘 PostgreSQL Connectivity (odbc_fdw)

## 📦 Installation
1. ⬇️ Download `fdw_win64_12.zip`
2. 📁 Copy files to PostgreSQL `lib` and `extension` directories

---

## 🐘 PostgreSQL Configuration
```sql
CREATE EXTENSION odbc_fdw;
```
```sql
CREATE SERVER odbc_server 
FOREIGN DATA WRAPPER odbc_fdw 
OPTIONS (dsn 'mysqlConnect');
```
```sql
GRANT USAGE ON FOREIGN SERVER odbc_server TO postgres;
```
```sql
CREATE USER MAPPING FOR postgres 
SERVER odbc_server 
OPTIONS (odbc_UID 'root', odbc_PWD '');
```


---

## 🗂️ Create Foreign Table
```sql
CREATE FOREIGN TABLE M_AUTHOR(author_id VARCHAR(5),
author_name VARCHAR(30)) SERVER odbc_server 
OPTIONS (odbc_DATABASE 'sakila', table 'AUTHOR');
```


---

## 🔗 Join Queries Across Databases
```sql
SELECT B.book_title, A.author_name 
FROM "book"@PS_LINK B 
JOIN "author"@MYSQL_LINK A ON B.author_id = A.author_id;
```
---

## ✅ Project Outcome

- ✅ Successfully connected Oracle, PostgreSQL, and MySQL
- 🔄 Executed cross-database queries and joins
- 🌍 Demonstrated real-world heterogeneous database integration

<p align="center">
  📘 This project is created strictly for educational and learning purposes.
</p>
<p align="center">
    ⭐ If you find this project helpful, feel free to star the repository!
</p>
<p align="center">
  © 2026 <strong><a href = "https://bemaurya.github.io">BeMaurya</a></strong>. All rights reserved.
</p>
