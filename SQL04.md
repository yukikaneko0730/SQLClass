# 🐘 SQL - 04

**PREVIOUSLY**
- Relationships between tables  
- Primary keys, Foreign keys  
- JOINs  

**TODAY**
- 📊 Ordering and limiting query results  

---

## 🔢 Query results limit

- Tables can contain thousands or millions of rows.  
- Often, we don’t want to see *all* rows.  

📌 Example:  
Amazon search for "books" → only the first 10 results are shown.  

```sql
-- Limit the number of rows returned to 10
SELECT * FROM clients LIMIT 10;

-- Limit the number of rows returned to 10, starting from row 10
-- This is like "skip the first 10 rows"
-- Or, "start at row 11"
-- Or, "with 10 per page, show me page 2"
SELECT * FROM clients LIMIT 10 OFFSET 10;
```

**Pagination**

- Page 1 → LIMIT 10 OFFSET 0
- Page 2 → LIMIT 10 OFFSET 10
- Page 3 → LIMIT 10 OFFSET 20
- Page 4 → LIMIT 10 OFFSET 30

---

## 📚 Query results order

Sometimes we want results in a specific order.

```sql
-- Order the results by title
SELECT * FROM books ORDER BY title;

-- Order the results by publication date
SELECT * FROM books ORDER BY publication_date;

-- Order the results by title in reverse order
SELECT * FROM books ORDER BY title DESC; -- DESC = descending

-- Order the results by price
SELECT * FROM books ORDER BY price;
```

---

## 🔍 A realistic search query

📌 Example: A web shop search
- Genre = Fantasy
- Price < 20 €
- Ordered by publication date
- Show only the first 10 results

```sql
SELECT * FROM books
    WHERE genre = 'fantasy'
    AND price < 20
    ORDER BY publication_date
    LIMIT 10;
```

👉 In practice, genre might be in another table, requiring a JOIN.

---

## 🧑‍💻 Practical JOIN example

```sql
SELECT 
    appointments.id   AS appointmentID, 
    date              AS appointmentdate, 
    description       AS desc, 
    pets.name         AS petname,
    clients.name      AS clientname
FROM appointments 
    INNER JOIN pets    ON appointments.pet_id = pets.id
    INNER JOIN clients ON appointments.client_id = clients.id;
```

---

## 🎯 Combined example with LIMIT, ORDER, WHERE

```sql
SELECT * FROM pets
    WHERE pets.name LIKE 'i%'   -- names starting with "i"
    ORDER BY pets.name
    LIMIT 5
    OFFSET 1;                   -- skip the first match
```

---

## ✅ Wrap-up
- LIMIT → restrict number of rows returned
- OFFSET → skip rows (used for pagination)
- ORDER BY → sort results
- Combine with WHERE for powerful searching