# Database Connectivity: Oracle, PostgreSQL & MySQL

This repository documents **step-by-step database connectivity** between **Oracle 11g**, **PostgreSQL**, and **MySQL** using **ODBC drivers** and **Foreign Data Wrappers (FDW)**.  
It demonstrates how to configure cross-database connections, create database links, and perform queries and joins across heterogeneous databases.

---

## Author Information

- **Name:** Shubham Kumar  
- **Course:** MCA (Regular)  
- **Project:** Database Connectivity  

---

## Databases & Tools Used

- Oracle Database 11g
- PostgreSQL 12
- MySQL 8.0
- ODBC Drivers
  - PostgreSQL ANSI Driver
  - MySQL ODBC 8.0 ANSI Driver
- Foreign Data Wrappers
  - `oracle_fdw`
  - `odbc_fdw`

---

# 1. PostgreSQL → Oracle Connectivity

## Software Required
- Oracle 11g
- PostgreSQL
- PostgreSQL ODBC ANSI Driver

## ODBC Configuration (PostgreSQL)

1. Open **Control Panel**
2. Go to **Administrative Tools**
3. Open **ODBC Data Source (32-bit / 64-bit)**
4. Select **System DSN**
5. Click **Add**
6. Select **PostgreSQL ODBC Driver (ANSI)**
7. Configure:
   - Data Source Name: `psqlConnect`
   - Database: postgres
   - Server: localhost
   - Port: 5432
   - Username & Password
8. Click **Test**, then **Save**

---

## Oracle Configuration

### Edit `initdg4odbc.ora`
Location:
