# Chapter 27: Mongoose ODM

## 27.1 What is Mongoose?

Mongoose is an ODM (Object Document Mapper) that provides schema-based modeling for MongoDB.

---

## 27.2 Installing Mongoose

```bash
npm install mongoose
```

---

## 27.3 Connecting with Mongoose

```javascript
const mongoose = require('mongoose');

mongoose.connect('mongodb://localhost:27017/myapp', {
  useNewUrlParser: true,
  useUnifiedTopology: true
});

const db = mongoose.connection;
db.on('error', console.error.bind(console, 'Connection error:'));
db.once('open', () => console.log('Connected'));
```

---

## 27.4 Defining Schemas

```javascript
const userSchema = new mongoose.Schema({
  name: String,
  email: { type: String, required: true, unique: true },
  age: { type: Number, min: 0, max: 120 },
  active: { type: Boolean, default: true },
  createdAt: { type: Date, default: Date.now }
});
```

---

## 27.5 Creating Models

```javascript
const User = mongoose.model('User', userSchema);

// Now use User for CRUD
const user = new User({ name: "John", email: "john@ex.com" });
await user.save();
```

---

## 27.6 Schema Validation

```javascript
const userSchema = new mongoose.Schema({
  name: { type: String, required: true },
  email: { type: String, required: true, unique: true },
  age: { type: Number, required: true, min: 18 },
  phone: { type: String, match: /\d{10}/ }
});

// Validate before save
const user = new User({ name: "John" });
try {
  await user.validate();
} catch (err) {
  console.log('Validation error:', err.message);
}
```

---

## 27.7 Mongoose Middleware

```javascript
userSchema.pre('save', function(next) {
  console.log('About to save user:', this.name);
  next();
});

userSchema.post('save', function(doc) {
  console.log('Saved user:', doc.name);
});
```

---

## GitHub Resources

- **Automattic/mongoose**: [github.com/Automattic/mongoose](https://github.com/Automattic/mongoose)

---

Navigation: [← Chapter 26: MongoDB with Node.js (Backend)](../ch26-mongodb-with-nodejs-backend/README.md) | [↑ Index](../../index.md) | [→ Chapter 28: CRUD with Mongoose](../ch28-crud-with-mongoose/README.md)

