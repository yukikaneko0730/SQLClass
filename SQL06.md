# 🐘 SQL - 06

**PREVIOUSLY**
- Transactions  
- The ACID principle  
- Normalization  

**TODAY**
- ⚡ SQL from JavaScript/TypeScript  

---

## ⚡ SQL from JS/TS

- We can use SQL directly from JavaScript/TypeScript.  
- Many libraries exist. Today we use [`postgres`](https://www.npmjs.com/package/postgres) because it’s simple.  
- Reminder: **Never commit `.env`** or hardcode secrets. Use environment variables.  

### Example: DB connection (`server.js`)

```js
import postgres from 'postgres';

// create a connection
const sql = postgres({
    host: "localhost",
    port: "5432",
    database: "shop",
    username: "fahim",
    password: ""  // ❌ Don’t hardcode in real projects, use process.env.PG_PASSWORD
});

export default sql;
```

**📝 Memo:**
- Replace "localhost" etc. with your own settings.
- In production, you should configure this via .env.
- Example:
```bash
PGHOST=localhost
PGUSER=fahim
PGPASSWORD=secret
PGDATABASE=shop
PGPORT=5432
```

---

## Example query
```js
import sql from './server.js';

const clients = await sql`
  SELECT * FROM clients LIMIT 5
`;

console.log(clients);
```

**📝 Memo:**
- The syntax sql`SELECT ...` is a tagged template literal.
- It automatically escapes values → safe against SQL injection.

---

## ✅ Wrap-up

- We can now connect JavaScript/TypeScript apps with PostgreSQL.

- This means we’re full-stack:
🌕Front-end
🌞Back-end (Express)
🪐Database (Postgres)


---

## 🧑‍💻 Practical SQL Queries from Node.js

Here are some common database operations (`SELECT`, `INSERT`, `UPDATE`) using the `postgres` npm package.

---

## 1. SELECT (Read data)

```js
import sql from './server.js';

async function getClients() {
  const clients = await sql`
    SELECT * FROM clients LIMIT 5
  `;
  console.log(clients);
}

getClients();
```

**📝 Memo:**
- Returns an array of rows (JavaScript objects).
- Each row’s column becomes a property.

---

## 2. INSERT (Add new data)
```js
import sql from './server.js';

async function addClient(name, email) {
  const result = await sql`
    INSERT INTO clients (name, email)
    VALUES (${name}, ${email})
    RETURNING *
  `;
  console.log('Inserted:', result[0]);
}

addClient('Alice', 'alice@example.com');
```

**📝 Memo:**
- ${name} and ${email} are safely interpolated → prevents SQL injection.
- RETURNING * gives back the inserted row.

---

## 3. UPDATE (Change existing data)
```js
import sql from './server.js';

async function updateClientEmail(id, newEmail) {
  const result = await sql`
    UPDATE clients
    SET email = ${newEmail}
    WHERE id = ${id}
    RETURNING *
  `;
  console.log('Updated:', result[0]);
}

updateClientEmail(1, 'alice@newmail.com');
```

**📝 Memo:**
- Always use WHERE in an UPDATE query!
- Without WHERE, it will update all rows.
- RETURNING * shows the updated record.
✅ With these three building blocks (SELECT, INSERT, UPDATE), you can already implement a simple CRUD API with Express + Postgres.