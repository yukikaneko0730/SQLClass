# 🐘 SQL - 07

**PREVIOUSLY**
- Running SQL queries from Node  

**TODAY**
- ORM with Sequelize  

---

## 🔄 ORM (Object-Relational Mapping)

- **ODM**: Object-Document Mapping (for MongoDB/NoSQL) → e.g., Mongoose  
- **ORM**: Object-Relational Mapping (for SQL) → e.g., Sequelize  

👉 Why use ORM?  
- SQL databases use **tables, rows, columns**.  
- In JS/TS, we prefer **objects, arrays, methods**.  
- ORMs bridge this gap → we can use objects instead of raw SQL.  

### Example with Sequelize

```js
// Create a new user
const jane = await User.create({ name: 'Jane' });

// Find all users
const users = await User.findAll(); 

// Find a user by ID
const project = await Project.findByPk(123); // "Pk" = Primary Key
```
**📝 Memo:**
- Sequelize translates these commands into SQL.
- Example mappings:
```sql
-- Create a new user
INSERT INTO users (name) VALUES ('Jane');
-- Find all users
SELECT * FROM users;
-- Find a user by ID
SELECT * FROM projects WHERE id = 123;
```
👉 ORM also protects against SQL injection by default.

---

## ⭐ Sequelize

**Sequelize** = popular ORM for **Node.js**  
- Promise-based  
- Works with:  
  - PostgreSQL  
  - MySQL / MariaDB  
  - SQLite  
  - Microsoft SQL Server  

📚 **Docs**  
- [npm page](https://www.npmjs.com/package/sequelize)  
- [Official docs](https://sequelize.org/)  

📝 **Memo**  
- For self-study: SQLite is a very lightweight and popular database.  

---

## ⚙️ Typical Sequelize Program

A program with Sequelize usually has these steps:

1. Prepare the database connection  
2. Define & initialize models  
3. Connect to the database  
4. Synchronize the models with the database  

👉 With TypeScript, using models can be tricky → check docs for examples.  

---

## ✅ Wrap-up

- ORM = use JS/TS objects to interact with SQL databases.  
- Sequelize = a promise-based ORM that works with many SQL servers.  


---

## 🧑‍💻 Sequelize Practical Example

### 1. Database Connection (`db.js`)

```js
import { Sequelize } from 'sequelize';

// Create a new Sequelize instance
const sequelize = new Sequelize('shop', 'postgres', 'password123', {
  host: 'localhost',
  dialect: 'postgres',  // could be 'mysql' | 'mariadb' | 'sqlite' | 'mssql'
});

export default sequelize;
```

**📝 Memo**

- Replace 'shop', 'postgres', 'password123' with your own DB, user, and password.

- dialect tells Sequelize which SQL engine you’re using.

---

### 2. Define a Model (models/User.js)
```js
import { DataTypes } from 'sequelize';
import sequelize from '../db.js';

// Define a "User" model
const User = sequelize.define('User', {
  name: {
    type: DataTypes.STRING,
    allowNull: false, // name cannot be null
  },
  email: {
    type: DataTypes.STRING,
    unique: true,     // must be unique
    allowNull: false,
  }
});

export default User;
```
**📝 Memo**

- Models = table definitions.
- By default, Sequelize will create a table Users.

---

## 3. Synchronize Database (index.js)
```js
import sequelize from './db.js';
import User from './models/User.js';

async function main() {
  try {
    // Connect & sync database
    await sequelize.authenticate();
    console.log('✅ Database connected');

    await sequelize.sync({ force: true }); 
    // force: true = drops and recreates tables (useful for dev only!)

    // INSERT
    const alice = await User.create({ name: 'Alice', email: 'alice@example.com' });
    console.log('Inserted:', alice.toJSON());

    // SELECT
    const users = await User.findAll();
    console.log('All users:', users.map(u => u.toJSON()));

    // UPDATE
    alice.email = 'alice@newmail.com';
    await alice.save();
    console.log('Updated:', alice.toJSON());

    // DELETE
    await alice.destroy();
    console.log('Deleted Alice');
  } catch (error) {
    console.error('❌ Error:', error);
  } finally {
    await sequelize.close();
  }
}

main();
```

**📝 Memo**

- sequelize.sync({ force: true }) → resets tables, use carefully.
- User.create() = INSERT, User.findAll() = SELECT, save() = UPDATE, destroy() = DELETE.
