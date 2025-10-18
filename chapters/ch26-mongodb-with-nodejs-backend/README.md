# Chapter 26: MongoDB with Node.js (Backend)

## 26.1 MongoDB Native Driver

Official npm package: `mongodb`

```bash
npm install mongodb
```

---

## 26.2 Connecting Node.js to MongoDB

```javascript
const { MongoClient } = require('mongodb');
const client = new MongoClient('mongodb://localhost:27017');

await client.connect();
const db = client.db('myapp');
const collection = db.collection('users');
```

---

## 26.3 Creating Express Server

```javascript
const express = require('express');
const { MongoClient } = require('mongodb');
const app = express();

app.use(express.json());

const client = new MongoClient('mongodb://localhost:27017');

app.get('/api/users', async (req, res) => {
  try {
    const db = client.db('myapp');
    const users = await db.collection('users').find({}).toArray();
    res.json(users);
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

app.listen(5000, () => console.log('Server running'));
```

---

## 26.4 CRUD Operations with Node.js

```javascript
// CREATE
await users.insertOne({ name: "John", age: 30 });

// READ
const user = await users.findOne({ name: "John" });

// UPDATE
await users.updateOne({ name: "John" }, { $set: { age: 31 } });

// DELETE
await users.deleteOne({ name: "John" });
```

---

## 26.5 Error Handling

```javascript
try {
  const result = await users.insertOne({ name: "John" });
} catch (err) {
  if (err.code === 11000) {
    console.log('Duplicate key error');
  } else {
    console.error('Database error:', err.message);
  }
}
```

---

## 26.6 Connection Pooling

```javascript
const client = new MongoClient(uri, {
  maxPoolSize: 10,
  minPoolSize: 2,
  serverSelectionTimeoutMS: 5000
});
```

---

## GitHub Resources

- **mongodb/node-mongodb-native**: [github.com/mongodb/node-mongodb-native](https://github.com/mongodb/node-mongodb-native)

---

Navigation: [← Chapter 25: MongoDB Compass](../ch25-mongodb-compass/README.md) | [↑ Index](../../index.md) | [→ Chapter 27: Mongoose ODM](../ch27-mongoose-odm/README.md)

