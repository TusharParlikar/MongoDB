# Chapter 30: Project - Contact Form

## 30.1 Project Overview

Build a full-stack contact form with MongoDB backend, Express API, and React frontend. Features: form submission, validation, admin panel to view messages.

---

## 30.2 Database Schema

```javascript
const contactSchema = new mongoose.Schema({
  name: { type: String, required: true },
  email: { type: String, required: true },
  message: { type: String, required: true },
  phone: String,
  createdAt: { type: Date, default: Date.now }
});

const Contact = mongoose.model('Contact', contactSchema);
```

---

## 30.3 Backend API (Express)

```javascript
const express = require('express');
const mongoose = require('mongoose');
const app = express();

app.use(express.json());
mongoose.connect('mongodb://localhost:27017/contact-form');

// POST - Submit form
app.post('/api/contacts', async (req, res) => {
  try {
    const contact = new Contact(req.body);
    await contact.save();
    res.json({ success: true, id: contact._id });
  } catch (err) {
    res.status(400).json({ error: err.message });
  }
});

// GET - All contacts (admin)
app.get('/api/contacts', async (req, res) => {
  const contacts = await Contact.find().sort({ createdAt: -1 });
  res.json(contacts);
});

app.listen(5000);
```

---

## 30.4 Frontend Form (React)

```jsx
import React, { useState } from 'react';
import axios from 'axios';

function ContactForm() {
  const [form, setForm] = useState({ name: '', email: '', message: '' });

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      await axios.post('/api/contacts', form);
      alert('Message sent!');
      setForm({ name: '', email: '', message: '' });
    } catch (err) {
      alert('Error: ' + err.message);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input required placeholder="Name" onChange={(e) => setForm({...form, name: e.target.value})} />
      <input required type="email" placeholder="Email" onChange={(e) => setForm({...form, email: e.target.value})} />
      <textarea required placeholder="Message" onChange={(e) => setForm({...form, message: e.target.value})}></textarea>
      <button type="submit">Send</button>
    </form>
  );
}
```

---

## 30.5 Form Submission Handler

```jsx
const handleSubmit = async (e) => {
  e.preventDefault();
  const data = { name, email, message, phone };
  const response = await axios.post('/api/contacts', data);
  if (response.data.success) {
    // Show success, reset form
    resetForm();
  }
};
```

---

## 30.6 Storing Data in MongoDB

Express route receives data → Validates → Mongoose saves to MongoDB with timestamp.

---

## 30.7 Admin Panel

```jsx
function AdminPanel() {
  const [contacts, setContacts] = useState([]);

  useEffect(() => {
    axios.get('/api/contacts').then(res => setContacts(res.data));
  }, []);

  return (
    <table>
      <thead>
        <tr><th>Name</th><th>Email</th><th>Date</th></tr>
      </thead>
      <tbody>
        {contacts.map(c => (
          <tr key={c._id}>
            <td>{c.name}</td>
            <td>{c.email}</td>
            <td>{new Date(c.createdAt).toLocaleDateString()}</td>
          </tr>
        ))}
      </tbody>
    </table>
  );
}
```

---

GitHub: [github.com/traversymedia](https://github.com/traversymedia)

---

Navigation: [← Chapter 29: MERN Stack Integration](../ch29-mern-stack-integration/README.md) | [↑ Index](../../index.md) | [→ Chapter 31: Deployment & Hosting](../ch31-deployment-hosting/README.md)
