# Chapter 29: MERN Stack Integration

## 29.1 MERN Overview

**MERN Stack Components:**
- **M**ongoDB: NoSQL database
- **E**xpress: Node.js web framework
- **R**eact: Frontend UI library
- **N**ode.js: JavaScript runtime

---

## 29.2 Frontend Setup (React)

```jsx
// App.js
import React, { useState, useEffect } from 'react';
import axios from 'axios';

function App() {
  const [items, setItems] = useState([]);

  useEffect(() => {
    axios.get('/api/items')
      .then(res => setItems(res.data))
      .catch(err => console.log(err));
  }, []);

  return <div>{items.map(item => <div key={item._id}>{item.name}</div>)}</div>;
}
```

---

## 29.3 Backend Setup (Express)

```javascript
const express = require('express');
const mongoose = require('mongoose');
const app = express();

app.use(express.json());

mongoose.connect('mongodb://localhost:27017/mern-app');

const itemSchema = new mongoose.Schema({ name: String });
const Item = mongoose.model('Item', itemSchema);

app.get('/api/items', async (req, res) => {
  const items = await Item.find();
  res.json(items);
});

app.listen(5000, () => console.log('Server running'));
```

---

## 29.4 API Development

```javascript
// POST endpoint
app.post('/api/items', async (req, res) => {
  const newItem = new Item({ name: req.body.name });
  await newItem.save();
  res.json(newItem);
});

// DELETE endpoint
app.delete('/api/items/:id', async (req, res) => {
  await Item.findByIdAndDelete(req.params.id);
  res.json({ success: true });
});
```

---

## 29.5 Frontend-Backend Communication

```jsx
// Create
const handleCreate = async (name) => {
  const res = await axios.post('/api/items', { name });
  setItems([...items, res.data]);
};

// Delete
const handleDelete = async (id) => {
  await axios.delete(`/api/items/${id}`);
  setItems(items.filter(item => item._id !== id));
};
```

---

## 29.6 Full Stack Data Flow

```
User Input (React)
    ↓
Axios POST to /api/items
    ↓
Express Route Handler
    ↓
Mongoose Model Save
    ↓
MongoDB Document Insert
    ↓
Response with New Item
    ↓
Update React State
    ↓
Re-render UI
```

---

## GitHub Resources

- **facebook/react**: [github.com/facebook/react](https://github.com/facebook/react)
- **expressjs/express**: [github.com/expressjs/express](https://github.com/expressjs/express)

---

Navigation: [← Chapter 28: CRUD with Mongoose](../ch28-crud-with-mongoose/README.md) | [↑ Index](../../index.md) | [→ Chapter 30: Project - Contact Form](../ch30-project-contact-form/README.md)
