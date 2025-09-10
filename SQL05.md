# 🐘 SQL - 05

**PREVIOUSLY**
- JOINs  
- Ordering and limiting query results  

**TODAY**
- 🔄 Transactions  
- 🧩 The ACID principle  
- 📐 Normalization  

---

## 🔄 Transactions

- A **transaction** = a sequence of SQL executed as a single unit  
- Example: transferring money between accounts  

```sql
-- Create accounts table
CREATE TABLE xaccounts (
    id      SERIAL PRIMARY KEY,
    name    TEXT,
    balance INT
);

-- Insert two accounts
INSERT INTO xaccounts (name, balance)
VALUES
    ('Alice', 100),
    ('Bob', 100);

-- Check balances
SELECT * FROM xaccounts;

-- Start a transaction
BEGIN;
UPDATE xaccounts SET balance=9999 WHERE id = 1;    -- OK
UPDATE xaccounts SET failure=9999 WHERE id = 1;    -- ❌ Invalid column
ROLLBACK;  -- Rollback ensures no changes are applied

-- Verify rollback
SELECT * FROM xaccounts;

-- Working transaction
BEGIN;
UPDATE xaccounts SET balance=50 WHERE id = 1;
UPDATE xaccounts SET balance=150 WHERE id = 2;
COMMIT;   -- Changes are saved

-- Verify commit
SELECT * FROM xaccounts;
```

---

## 🧩 Transactions and ACID

Database transactions must follow ACID:
- **Atomic** → All or nothing
- **Consistent** → DB valid before & after
- **Isolated** → Independent from other transactions
- **Durable** → Once committed, it’s permanent

---

## 📐 Data Normalization

**Normalization** = organizing data to:
- avoid redundancy
- improve data integrity

**❌ Non-normalized table**
```sql
CREATE TABLE employees (
    id              SERIAL PRIMARY KEY,
    name            TEXT,
    email           TEXT,
    job_title       TEXT,
    job_description TEXT
);

INSERT INTO employees (name, email, job_title, job_description)
VALUES
    ('CoolZero91', 'cz@example.org', 'HACKER', 'HAX'),
    ('Rauli', 'rauli@example.org', 'Developer', 'Writes HTML, CSS and JavaScript code for our software projects and products'),
    ('Veera', 'veera@example.org', 'Developer', 'Writes HTML, CSS and JavaScript code for our software projects and products'),
    ('Alpi', 'alpi@example.org', 'Developer', 'Writes HTML, CSS and JavaScript code for our software projects and products');
```

## ✅ Normalized structure
```sql
-- Separate jobs table
CREATE TABLE jobs (
    id          SERIAL PRIMARY KEY,
    title       TEXT,
    description TEXT
);

INSERT INTO jobs (title, description)
VALUES
    ('Developer', 'Writes HTML, CSS and JavaScript code for our software projects and products'),
    ('HACKER', 'HAX');

-- Add relation in employees
ALTER TABLE employees ADD COLUMN job_id INT;
ALTER TABLE employees ADD FOREIGN KEY (job_id) REFERENCES jobs(id);

-- Update employees to use job_id
UPDATE employees SET job_id = 1 WHERE job_title = 'Developer';
UPDATE employees SET job_id = 2 WHERE job_title = 'HACKER';

-- Drop redundant columns
ALTER TABLE employees DROP COLUMN job_title;
ALTER TABLE employees DROP COLUMN job_description;
```

---
## ✅ Wrap-up

- **Transactions = ensure SQL runs as one unit**
👉 A transaction makes sure multiple SQL statements are treated as a single operation. Either all succeed, or none do.

- **ACID = reliability of transactions**
👉 The ACID principles (Atomicity, Consistency, Isolation, Durability) guarantee that transactions are processed safely and reliably.

- **Normalization = reduce redundancy & improve integrity**
👉 Normalization organizes data into related tables to avoid duplication and ensure data consistency.

- **ALTER TABLE = modify schema after creation**
👉 The ALTER TABLE command allows us to change the structure of a table (add, remove, or update columns) even after it has been created.

---

## ✅ Wrap-up

- Transactions = ensure SQL runs as one unit
- ACID = reliability of transactions
- Normalization = reduce redundancy & improve integrity
- ALTER TABLE = modify schema after creation