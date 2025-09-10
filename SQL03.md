# 🐾 SQL - 03

**PREVIOUSLY**
- `SELECT ... WHERE`
- `UPDATE ... WHERE`
- `DELETE ... WHERE`

**TODAY**
- 🔗 Relationships between tables
- 🔑 Primary keys, Foreign keys
- 🔀 JOINs

## 🐕 Relationships between tables

PostgreSQL is an **RDBMS** → Relational Database Management System.  
Today we focus on the **Relational** part.

📌 Example: **Veterinary Clinic Appointment System**

### Tables:
- `clients`
- `pets`
- `appointments`

### Relationships:
- A client can have many pets  
- A client can have many appointments  
- An appointment belongs to one client  
- An appointment belongs to one pet  
- A pet can have many appointments  

---
## 👥 Create Clients Table

```sql
CREATE TABLE clients (
    id          SERIAL PRIMARY KEY,   -- PK = unique identifier
    name        TEXT,
    email       TEXT
);
```

---

## 🐾 Create Pets Table
```sql
CREATE TABLE clients (
    id          SERIAL PRIMARY KEY,   -- PK = unique identifier
    name        TEXT,
    email       TEXT
);
```

**Example Inserts**
```sql
-- Insert a client first
INSERT INTO clients (name, email)
VALUES ('Alice', 'alice@example.org');

-- Insert a pet linked to Alice (client_id=1)
INSERT INTO pets (name, species, client_id)
VALUES ('Veera', 'cat', 1);

-- ❌ Invalid: client_id 41 does not exist
INSERT INTO pets (name, species, client_id)
VALUES ('Rauli', 'cat', 41);

-- ❌ Invalid: wrong data type
INSERT INTO pets (name, species, client_id)
VALUES ('Alpi', 'dog', 'alpidog');
```

---

## 📅 Create Appointments Table
```sql
CREATE TABLE appointments (
    id          SERIAL PRIMARY KEY,
    date        DATE,
    client_id   INT,
    pet_id      INT,
    FOREIGN KEY (client_id) REFERENCES clients(id),
    FOREIGN KEY (pet_id) REFERENCES pets(id)
);
```

**Example Inserts**
```sql
-- Normal insert
INSERT INTO appointments (date, client_id, pet_id)
VALUES ('2021-10-01', 1, 1);

-- Forcing ID (not recommended)
INSERT INTO appointments (id, date, client_id, pet_id)
VALUES (5221512, '2021-10-01', 1, 1);

-- Check data
SELECT * FROM appointments;
```

---

## ⚡ TRUNCATE vs DELETE
```sql
TRUNCATE TABLE appointments;    -- Quickly removes ALL rows, resets sequence
DELETE FROM appointments;       -- Removes rows, keeps sequence counter
```
## ✅ Mini Exercise: Difference?

- **TRUNCATE** is faster, cannot be rolled back row by row, resets IDs.

- **DELETE** removes rows one by one, IDs continue from last used.

---
## 🔑 Keys

**Primary Key (PK)**
- Unique identifier for each row
- Usually auto-generated (SERIAL / UUID)

**Foreign Key (FK)**
- A reference to a PK in another table
- Enforces relationships between tables

---

## 🔀 JOINs

JOINs combine data from multiple tables.

**INNER JOIN Example 1**
```sql
SELECT
    clients.name AS client_name,
    pets.name AS pet_name
FROM clients
INNER JOIN pets
    ON clients.id = pets.client_id;
```

**INNER JOIN Example 2**
```sql
SELECT
    clients.name AS client_name,
    pets.name AS pet_name,
    appointments.date AS appointment_date
FROM clients
INNER JOIN pets
    ON clients.id = pets.client_id
INNER JOIN appointments
    ON pets.id = appointments.pet_id;
```

**INNER JOIN Example 3 (all data)**
```sql
SELECT *
FROM clients
INNER JOIN pets
    ON clients.id = pets.client_id
INNER JOIN appointments
    ON pets.id = appointments.pet_id;
```

---

## 🛠️ Schema (Full Example)

```sql
CREATE TABLE clients(
    id SERIAL PRIMARY KEY,  
    name TEXT,
    email TEXT
);

CREATE TABLE pets(
    id SERIAL PRIMARY KEY,
    name TEXT,
    species TEXT,
    client_id INT,
    FOREIGN KEY (client_id) REFERENCES clients(id)
);

CREATE TABLE appointments(
    id SERIAL PRIMARY KEY,
    description TEXT,
    pet_id INT,
    client_id INT,
    FOREIGN KEY (pet_id) REFERENCES pets(id),
    FOREIGN KEY (client_id) REFERENCES clients(id)
);

-- Add column later
ALTER TABLE appointments
ADD COLUMN date TIMESTAMP DEFAULT CURRENT_TIMESTAMP;
```

---

## ✅ Wrap-up

- Use **FOREIGN KEY** to connect tables
- PK = unique row identifier
- JOIN combines results from multiple tables
- Focus: **INNER JOIN** for matched results

---

## 🎯 Exercises (with answers)

Insert Bob as a client (bob@example.org):
```sql
INSERT INTO clients (name, email)
VALUES ('Bob', 'bob@example.org');
```

Add Bob’s pet Max (dog):
```sql
INSERT INTO pets (name, species, client_id)
VALUES ('Max', 'dog', 2);
```

Add appointment for Bob + Max:
```sql
INSERT INTO appointments (description, client_id, pet_id)
VALUES ('Vaccination', 2, 2);
```

Show all clients with their pets:
```sql
SELECT clients.name, pets.name
FROM clients
INNER JOIN pets ON clients.id = pets.client_id;

```

Show all appointments with client + pet names:
```sql
SELECT clients.name AS client_name, pets.name AS pet_name, appointments.description
FROM appointments
INNER JOIN clients ON clients.id = appointments.client_id
INNER JOIN pets ON pets.id = appointments.pet_id;

```

