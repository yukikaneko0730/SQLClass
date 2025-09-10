# SQL - 02

## Previously
- Database concepts
- SQL Server setup
- SQL basic queries: `CREATE`, `INSERT`, `SELECT`

---

## Today
- `SELECT ... WHERE`
- `UPDATE ... WHERE`
- `DELETE ... WHERE`
- Running queries from files
- CRUD queries



---

## Getting Started

**CRUD = Create, Read, Update, Delete**

Start psql session:
```bash
psql -U coolzero91 -h localhost -d test
```

---

## 🏗️ Table Setup

We’ll call this table people 👥

```sql
CREATE TABLE people (
    id SERIAL,
    name TEXT,
    age INT,
    verified BOOLEAN        DEFAULT FALSE,
    created_at TIMESTAMP    DEFAULT CURRENT_TIMESTAMP
);
```
- DEFAULT → sets a default value
- CURRENT_TIMESTAMP → gives current date & time

---

## ➕ CREATE (Insert Data)
```sql
INSERT INTO people (name, age, verified)
VALUES
    ('Alice', 25), -- intentional error!
    ('Bob', 30, true),
    ('Charlie', 35, true),
    ('Xie', 28, true),
    ('Ahmad', 41, true),
    ('Petteri', 19, true);
```
**📝 Mini Exercise (with answers)**
```sql
-- Insert Ada
INSERT INTO people (name, age) VALUES ('Ada', 21);

-- Insert Linus, not verified
INSERT INTO people (name, age, verified) VALUES ('Linus', 52, false);

-- Insert Zach with custom created_at
INSERT INTO people (name, age, created_at) 
VALUES ('Zach', 33, '2004-01-01 00:00:00');
```

---

## READ (SELECT)

```sql
SELECT * FROM people;
SELECT name FROM people WHERE id = 3;
SELECT name FROM people WHERE age > 30;
SELECT name FROM people WHERE age > 30 AND age < 40;
```

💡 WHERE = **clause** in SQL → filters / scopes the query

**📝 Mini Exercise (with answers)**
```sql
-- Verified only
SELECT * FROM people WHERE verified = true;

-- Verified and older than 30
SELECT * FROM people WHERE verified = true AND age > 30;

-- Verified and age between 30 and 40
SELECT * FROM people WHERE verified = true AND age > 30 AND age < 40;
```

---

## ✏️ UPDATE (Change Data)
```sql
UPDATE people SET age = 50; -- ⚠️ Oops → updated all rows!
SELECT * FROM people;
```

**Fix with WHERE:**

```sql
UPDATE people SET age = 25 WHERE id = 1;
UPDATE people SET age = 30 WHERE id = 2;
UPDATE people SET age = 35 WHERE id = 3;
UPDATE people SET age = 28 WHERE id = 4;
UPDATE people SET age = 41 WHERE id = 5;
UPDATE people SET age = 18 WHERE name = 'Xie';
```

**📝 Mini Exercise (with answers)**
```sql
-- Insert Ricardo
INSERT INTO people (name, age, verified) VALUES ('Ricardo', 40, false);

-- Update Ricardo’s age
UPDATE people SET age = 40 WHERE name = 'Ricardo';

-- Update Ricardo’s verified status
UPDATE people SET verified = true WHERE name = 'Ricardo';

-- Check
SELECT * FROM people;
```

---

## 🗑️ DELETE (Remove Data)

⚠️ DELETE queries cannot be undone!

```sql
DELETE FROM people WHERE id = 6;
DELETE FROM people WHERE name = 'Petteri';
SELECT * FROM people;
```

**📝 Mini Exercise (with answers)**
```sql
-- Delete all unverified
DELETE FROM people WHERE verified = false;

-- Insert Matti
INSERT INTO people (name, age) VALUES ('Matti', 50);

-- Check
SELECT * FROM people;

-- Find Matti's id
SELECT id FROM people WHERE name = 'Matti';

-- Get last inserted id in Postgres
SELECT currval(pg_get_serial_sequence('people','id'));

-- Do ids always increment by 1?
-- 👉 Yes, SERIAL increments by 1, but you can configure sequences differently.

-- Do deleted ids get reused?
-- 👉 No, once an id is used, it’s not reused unless you reset the sequence.

-- How long can names be?
-- 👉 TEXT can be very long (up to 1 GB). Use VARCHAR(n) to limit.

```

---

## 📂 Running Queries from Files

Create queries.sql:
```sql
-- USE test;  (Not needed in Postgres!)
INSERT INTO people (name, age) VALUES ('Marc', 72);

SELECT * FROM people;
```

Run:
```bash
psql -U coolzero91 -h localhost -d test -f queries.sql
```

Redirect output to file:
```bash
psql -U coolzero91 -h localhost -d test -f queries.sql > results.txt
```

**📝 Mini Exercise (with answers)**
```sql
-- queries2.sql
INSERT INTO people (name, age) VALUES ('Sara', 22);
SELECT * FROM people;
```
Run it:
```bash
psql -U coolzero91 -h localhost -d test -f queries2.sql
```

👉 Note: In Postgres, USE test; is not needed (that’s MySQL syntax).
You pick the DB with -d test in psql.

---

## Wrap-up

- **Create** → `INSERT`
- **Read** → `SELECT`
- **Update** → `UPDATE`
- **Delete** → `DELETE`

✅ Learned `WHERE` to scope queries  
✅ Learned to run queries from files  
✅ Learned to redirect output to a file


