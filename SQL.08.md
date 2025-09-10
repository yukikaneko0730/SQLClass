# 🐘 SQL - 08

**PREVIOUSLY**
- ORM with Sequelize  

**TODAY**
- Practice Sequelize CRUD with an Express and Postman backend  

---

## 🧑‍💻 Practice

Today is a **review day**.  
We will focus on **hands-on coding**.  
👉 Please code along!  

---

## ⚡ Example Setup

### 1. Install dependencies
```bash
npm install express sequelize pg pg-hstore
```

### 2. Database connection (db.js)
```js
import { Sequelize } from 'sequelize';

const sequelize = new Sequelize('shop', 'postgres', 'password123', {
  host: 'localhost',
  dialect: 'postgres',
});

export default sequelize;
```

### 3. Define a Model (models/User.js)
```js
import { DataTypes } from 'sequelize';
import sequelize from '../db.js';

const User = sequelize.define('User', {
  name: { type: DataTypes.STRING, allowNull: false },
  email: { type: DataTypes.STRING, unique: true, allowNull: false },
});

export default User;
```
### 4. Express CRUD routes (index.js)
```js
import express from 'express';
import sequelize from './db.js';
import User from './models/User.js';

const app = express();
app.use(express.json());

// CREATE
app.post('/users', async (req, res) => {
  const user = await User.create(req.body);
  res.json(user);
});

// READ all
app.get('/users', async (req, res) => {
  const users = await User.findAll();
  res.json(users);
});

// READ one
app.get('/users/:id', async (req, res) => {
  const user = await User.findByPk(req.params.id);
  res.json(user);
});

// UPDATE
app.put('/users/:id', async (req, res) => {
  const user = await User.findByPk(req.params.id);
  user.set(req.body);
  await user.save();
  res.json(user);
});

// DELETE
app.delete('/users/:id', async (req, res) => {
  const user = await User.findByPk(req.params.id);
  await user.destroy();
  res.json({ message: 'Deleted successfully' });
});

// Start server
sequelize.sync().then(() => {
  app.listen(3000, () => console.log('🚀 Server running on http://localhost:3000'));
});
```

---

## 🧪 Test with Postman
- POST /users → Create a new user
- GET /users → List all users
- GET /users/:id → Get one user by ID
- PUT /users/:id → Update a user
- DELETE /users/:id → Delete a user

--- 

## ✅ Wrap-up
- Practiced Sequelize + Express CRUD
- Tested with Postman
- This is a foundation for building full REST APIs

---
# 🐘 Sequelize + TypeScript Relations

We will learn how to model **1-to-Many** and **Many-to-Many** relationships in Sequelize with TypeScript.  

---

## ⚙️ Setup
Make sure you already have:
```bash
npm install sequelize pg pg-hstore
npm install -D typescript ts-node @types/node
```
---
## 1️⃣ One-to-Many Example (Client → Pets)
models/Client.ts
```ts
import { DataTypes, Model, Optional } from "sequelize";
import sequelize from "../db";

interface ClientAttributes {
  id: number;
  name: string;
  email: string;
}

interface ClientCreationAttributes extends Optional<ClientAttributes, "id"> {}

class Client extends Model<ClientAttributes, ClientCreationAttributes>
  implements ClientAttributes {
  public id!: number;
  public name!: string;
  public email!: string;
}

Client.init(
  {
    id: { type: DataTypes.INTEGER, autoIncrement: true, primaryKey: true },
    name: { type: DataTypes.STRING, allowNull: false },
    email: { type: DataTypes.STRING, allowNull: false, unique: true },
  },
  { sequelize, modelName: "Client", tableName: "clients" }
);

export default Client;
```

models/Pet.ts
```ts
import { DataTypes, Model, Optional } from "sequelize";
import sequelize from "../db";
import Client from "./Client";

interface PetAttributes {
  id: number;
  name: string;
  species: string;
  clientId: number;
}

interface PetCreationAttributes extends Optional<PetAttributes, "id"> {}

class Pet extends Model<PetAttributes, PetCreationAttributes>
  implements PetAttributes {
  public id!: number;
  public name!: string;
  public species!: string;
  public clientId!: number;
}

Pet.init(
  {
    id: { type: DataTypes.INTEGER, autoIncrement: true, primaryKey: true },
    name: { type: DataTypes.STRING, allowNull: false },
    species: { type: DataTypes.STRING, allowNull: false },
    clientId: { type: DataTypes.INTEGER, allowNull: false },
  },
  { sequelize, modelName: "Pet", tableName: "pets" }
);

// Define relation
Client.hasMany(Pet, { foreignKey: "clientId" });
Pet.belongsTo(Client, { foreignKey: "clientId" });

export default Pet;
```
**📝 Memo**
- Client.hasMany(Pet) → one client can have many pets
- Pet.belongsTo(Client) → each pet belongs to one client

---

## 2️⃣ Many-to-Many Example (Student ↔ Course)
models/Student.ts
```ts
import { DataTypes, Model, Optional } from "sequelize";
import sequelize from "../db";

interface StudentAttributes {
  id: number;
  name: string;
}

interface StudentCreationAttributes extends Optional<StudentAttributes, "id"> {}

class Student extends Model<StudentAttributes, StudentCreationAttributes>
  implements StudentAttributes {
  public id!: number;
  public name!: string;
}

Student.init(
  {
    id: { type: DataTypes.INTEGER, autoIncrement: true, primaryKey: true },
    name: { type: DataTypes.STRING, allowNull: false },
  },
  { sequelize, modelName: "Student", tableName: "students" }
);

export default Student;
```

models/Course.ts

```ts
import { DataTypes, Model, Optional } from "sequelize";
import sequelize from "../db";

interface CourseAttributes {
  id: number;
  title: string;
}

interface CourseCreationAttributes extends Optional<CourseAttributes, "id"> {}

class Course extends Model<CourseAttributes, CourseCreationAttributes>
  implements CourseAttributes {
  public id!: number;
  public title!: string;
}

Course.init(
  {
    id: { type: DataTypes.INTEGER, autoIncrement: true, primaryKey: true },
    title: { type: DataTypes.STRING, allowNull: false },
  },
  { sequelize, modelName: "Course", tableName: "courses" }
);

export default Course;
```

models/index.ts (relations setup)

```ts
import Student from "./Student";
import Course from "./Course";

// Many-to-Many relation
Student.belongsToMany(Course, { through: "StudentCourses" });
Course.belongsToMany(Student, { through: "StudentCourses" });

export { Student, Course };
```

**📝 Memo**

- Sequelize automatically creates a join table StudentCourses.
- You can query with student.addCourse(course) or course.addStudent(student).

---

## 3️⃣ Usage Example

```ts
import sequelize from "./db";
import Client from "./models/Client";
import Pet from "./models/Pet";
import { Student, Course } from "./models";

async function main() {
  await sequelize.sync({ force: true });
  console.log("✅ Database synced");

  // One-to-Many
  const alice = await Client.create({ name: "Alice", email: "alice@mail.com" });
  await Pet.create({ name: "Fluffy", species: "cat", clientId: alice.id });
  await Pet.create({ name: "Rex", species: "dog", clientId: alice.id });

  const pets = await alice.getPets();
  console.log("Alice’s pets:", pets.map(p => p.toJSON()));

  // Many-to-Many
  const bob = await Student.create({ name: "Bob" });
  const math = await Course.create({ title: "Math 101" });
  const history = await Course.create({ title: "History 201" });

  await bob.addCourse(math);
  await bob.addCourse(history);

  const bobCourses = await bob.getCourses();
  console.log("Bob’s courses:", bobCourses.map(c => c.toJSON()));
}

main();
```

## ✅ Wrap-up

- 1-to-Many: Client.hasMany(Pet) + Pet.belongsTo(Client)

- Many-to-Many: Student.belongsToMany(Course, { through })