# SQL - 01 

## Database Concepts

- **Databases** are used to store data.
- **File systems** are used to store files.

### Why not just use files?
- Files are **very slow**.
- Files are not well structured for lots of variously formatted data.
- Files are not easy to search.
- Files are not optimized for constant read/write operations.
- Files don’t handle **concurrent access** well.  
  *(Concurrent = many users or processes accessing at once)*

---

## Database Types: SQL vs NoSQL

### SQL (Relational Databases)
- Data stored in **structured tables** (like Excel/Google Sheets).
- Uses **SQL (Structured Query Language)**.
- A database is a group of tables.

Example tables:
- **Products**
- **Orders**
- **Users**

A **Users table** has:
- Columns: `ID`, `Name`, `Email`, `Password`
- Rows (example):


> SQL is the **most common type** of database.  
> SQL is a **language standard**, but each SQL database has small variations.

### NoSQL (Non-relational Databases)
- Data can be stored in **many different ways**:
- Key-value pairs (Redis)
- Documents in collections, like an array of objects (MongoDB)

---

## Database Diagrams: ERD

- **Flowcharts** → visualize process/data flow.
- **System architecture diagrams** → database shown as a cylinder.
- **Entity-Relationship Diagram (ERD)**:
- Describes **entities (tables)** in the DB.
- Shows **relationships** between tables.

---

## SQL Server Setup (PostgreSQL)

We’ll use **PostgreSQL** (aka Postgres/PG/psql).

- Free & open-source
- Powerful and feature-rich
- Extremely popular

## Install on Ubuntu
```bash
sudo apt install postgresql
````

Installs Postgres and starts it as a background service.

Check status:
```bash
sudo systemctl status postgresql
```
**Connect to Postgres**
```bash
psql                     # current user (likely fails)
sudo psql                # root (likely fails)
sudo -u postgres psql    # postgres user (works)
```

**Create a new user & DB**
```sql
CREATE USER fahim WITH SUPERUSER CREATEDB CREATEROLE CONNECTION LIMIT -1;
ALTER USER fahim WITH PASSWORD 'fahim123!';

CREATE DATABASE test;
exit;
```

***Terminology***
**psql** → CLI tool
**Postgres** → database server
**test** → database we created
One Postgres server = many databases

---

### SQL Basic Statements: CREATE / INSERT / SELECT

Connect to our DB:
```bash
psql -U fahim -h localhost -d test
```

**Example queries**
```sql
CREATE TABLE pets (
  id SERIAL PRIMARY KEY,
  name VARCHAR(50),
  age INT
);

INSERT INTO pets (name, age) VALUES ('Bobby', 3);

SELECT * FROM pets;
```

- `CREATE TABLE` → creates a new table
- `INSERT INTO` → inserts a new row
- `SELECT` → retrieves data

💡 The word **querying** is often used specifically for `SELECT` statements,  
even though all SQL statements are technically queries.

---

### Docs
- [SQL SELECT](https://www.postgresql.org/docs/16/sql-select.html)  
- [SQL INSERT](https://www.postgresql.org/docs/16/sql-insert.html)  
- [SQL CREATE TABLE](https://www.postgresql.org/docs/16/sql-createtable.html)  

---

## Wrap-up

- Databases are more efficient than files.
- **SQL databases (RDBMS):** structured tables, SQL language.
- **NoSQL:** various storage models.
- **ERDs** visualize DB structures.
- **PostgreSQL** is a popular open-source RDBMS.

### We practiced:
- `CREATE TABLE`
- `INSERT`
- `SELECT`
