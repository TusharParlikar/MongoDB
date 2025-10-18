# Chapter 16: Embedded Documents

## 16.1 Understanding Nested Objects

**Embedded documents** are documents nested within other documents, useful for related data.

### **Structure**

```javascript
// Single embedded document
db.users.insertOne({
  name: "John",
  address: {          // ← Embedded document
    street: "123 Main St",
    city: "San Francisco",
    zipcode: "94102"
  }
})

// Array of embedded documents
db.users.insertOne({
  name: "Jane",
  addresses: [        // ← Array of embedded docs
    { city: "SF", primary: true },
    { city: "NYC", primary: false }
  ]
})
```

### **Benefits vs Drawbacks**

| Benefit | Drawback |
|---------|----------|
| Single query | Document size limited (16MB) |
| Atomic updates | Can't index deeply nested |
| Good for small related data | Data duplication |

---

## 16.2 Querying Embedded Documents

### **Query Nested Fields**

```javascript
// Find users in San Francisco
db.users.find({ "address.city": "San Francisco" })

// Find with nested comparison
db.users.find({ "address.zipcode": { $gt: "94000" } })

// Multiple nested conditions
db.users.find({
  "address.city": "SF",
  "address.zipcode": { $exists: true }
})
```

### **Query Arrays of Objects**

```javascript
// Find users with NYC address
db.users.find({ "addresses.city": "NYC" })

// Find primary addresses
db.users.find({ "addresses.primary": true })

// Query with $elemMatch (exact object match)
db.users.find({
  addresses: { 
    $elemMatch: { 
      city: "SF", 
      primary: true 
    } 
  }
})
```

---

## 16.3 Updating Embedded Documents

### **Update Nested Field**

```javascript
// Update city in address
db.users.updateOne(
  { name: "John" },
  { $set: { "address.city": "Seattle" } }
)
```

### **Update in Array of Objects**

```javascript
// Update first matching address
db.users.updateOne(
  { name: "Jane", "addresses.city": "SF" },
  { $set: { "addresses.$.primary": false } }  // $ = matched array index
)

// Update all matching
db.users.updateMany(
  { _id: ObjectId("...") },
  { $set: { "addresses.$[elem].verified": true } },
  { arrayFilters: [{ "elem.city": "SF" }] }
)
```

### **Add to Nested Array**

```javascript
// Add address
db.users.updateOne(
  { name: "John" },
  { $push: { addresses: { city: "Boston", primary: false } } }
)
```

---

## 16.4 Array of Objects

### **Insert Array of Embedded Documents**

```javascript
db.users.insertOne({
  name: "Alice",
  emails: [
    { address: "alice@work.com", verified: true, type: "work" },
    { address: "alice@personal.com", verified: false, type: "personal" }
  ]
})
```

### **Query Arrays**

```javascript
// Find users with Gmail
db.users.find({ "emails.address": /gmail\.com/ })

// Find verified emails
db.users.find({ "emails.verified": true })

// Find all emails of user
db.users.findOne({ name: "Alice" }).emails
```

### **Array Operations**

```javascript
// Add email
db.users.updateOne(
  { name: "Alice" },
  { $push: { emails: { address: "alice@new.com", verified: false } } }
)

// Remove email
db.users.updateOne(
  { name: "Alice" },
  { $pull: { emails: { address: "alice@personal.com" } } }
)

// Update email
db.users.updateOne(
  { name: "Alice", "emails.address": "alice@work.com" },
  { $set: { "emails.$.verified": true } }
)

// Get array size
db.users.find(
  { name: "Alice" },
  { emails: { $size: 2 } }
)
```

---

## Node.js Examples

```javascript
const { MongoClient, ObjectId } = require('mongodb');

async function embeddedDocExamples() {
  const client = new MongoClient('mongodb://localhost:27017');
  
  try {
    await client.connect();
    const db = client.db('myapp');
    const users = db.collection('users');
    
    // Insert with embedded doc
    await users.insertOne({
      name: "John",
      address: {
        street: "123 Main",
        city: "SF",
        zip: "94102"
      }
    });
    
    // Query nested field
    const user = await users.findOne({ "address.city": "SF" });
    console.log('Found user:', user);
    
    // Update nested
    await users.updateOne(
      { name: "John" },
      { $set: { "address.city": "Seattle" } }
    );
    
    // Array of objects
    await users.updateOne(
      { name: "John" },
      { 
        $push: { 
          phones: {
            number: "555-1234",
            type: "mobile"
          }
        }
      }
    );
    
  } finally {
    await client.close();
  }
}

embeddedDocExamples().catch(console.error);
```

---

## GitHub Resources

- **MongoDB Embedded Documents**: [mongodb/docs-embedded](https://github.com/mongodb/docs)

---

Navigation: [← Chapter 15: CRUD Operations - Delete](../ch15-crud-operations-delete/README.md) | [↑ Index](../../index.md) | [→ Chapter 17: Relationships in MongoDB](../ch17-relationships-in-mongodb/README.md)
