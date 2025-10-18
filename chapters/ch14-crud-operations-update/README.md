# Chapter 14: CRUD Operations - Update

## 14.1 updateOne() Method

**updateOne()** updates the first document matching the filter.

### **Syntax**

```javascript
db.collection.updateOne(filter, update, options)
```

### **Simple Update**

```javascript
// Update user's status
db.users.updateOne(
  { _id: ObjectId("507f1f77bcf86cd799439011") },
  { $set: { status: "active" } }
)

// Output:
// {
//   "acknowledged": true,
//   "matchedCount": 1,
//   "modifiedCount": 1
// }
```

---

## 14.2 updateMany() Method

**updateMany()** updates all documents matching the filter.

### **Syntax**

```javascript
db.collection.updateMany(filter, update, options)
```

### **Update Multiple Documents**

```javascript
// Set all inactive users to active
db.users.updateMany(
  { status: "inactive" },
  { $set: { status: "active" } }
)

// Output:
// {
//   "acknowledged": true,
//   "matchedCount": 5,
//   "modifiedCount": 5
// }
```

---

## 14.3 $set Operator

**$set** sets field values (creates field if doesn't exist).

### **Syntax**

```javascript
{ $set: { field: value } }
```

### **Examples**

```javascript
// Update single field
db.users.updateOne(
  { name: "John" },
  { $set: { age: 31 } }
)

// Update multiple fields
db.users.updateOne(
  { name: "John" },
  { $set: { age: 31, status: "active" } }
)

// Create new field if doesn't exist
db.users.updateOne(
  { name: "John" },
  { $set: { bio: "Software engineer" } }
)

// Update nested field
db.users.updateOne(
  { name: "John" },
  { $set: { "address.city": "San Francisco" } }
)
```

---

## 14.4 $unset Operator

**$unset** removes a field from a document.

### **Syntax**

```javascript
{ $unset: { field: "" } }
```

### **Examples**

```javascript
// Remove single field
db.users.updateOne(
  { name: "John" },
  { $unset: { bio: "" } }
)

// Remove multiple fields
db.users.updateMany(
  { status: "deleted" },
  { $unset: { email: "", phone: "" } }
)

// Remove nested field
db.users.updateOne(
  { name: "John" },
  { $unset: { "address.zipcode": "" } }
)
```

---

## 14.5 $inc Operator

**$inc** increments numeric field values.

### **Syntax**

```javascript
{ $inc: { field: amount } }
```

### **Examples**

```javascript
// Increment age by 1
db.users.updateOne(
  { name: "John" },
  { $inc: { age: 1 } }
)

// Increment product quantity by 10
db.products.updateOne(
  { name: "Laptop" },
  { $inc: { stock: 10 } }
)

// Decrement (negative increment)
db.orders.updateOne(
  { _id: 1 },
  { $inc: { itemsRemaining: -1 } }
)

// Multiple increments
db.stats.updateOne(
  { user: "john" },
  { $inc: { loginCount: 1, pageViews: 5 } }
)
```

---

## 14.6 $push Operator (Arrays)

**$push** adds an element to an array field.

### **Syntax**

```javascript
{ $push: { arrayField: value } }
```

### **Examples**

```javascript
// Add skill to user's skills array
db.users.updateOne(
  { name: "John" },
  { $push: { skills: "MongoDB" } }
)

// Add multiple elements (one at a time)
db.users.updateOne(
  { name: "John" },
  { $push: { skills: "Node.js" } }
)

// Add nested object to array
db.users.updateOne(
  { name: "John" },
  { $push: { addresses: { city: "SF", zip: "94102" } } }
)

// Add multiple at once with $each
db.users.updateOne(
  { name: "John" },
  { $push: { skills: { $each: ["Python", "Java"] } } }
)
```

---

## 14.7 $pull Operator (Arrays)

**$pull** removes elements from an array field.

### **Syntax**

```javascript
{ $pull: { arrayField: value } }
```

### **Examples**

```javascript
// Remove skill from array
db.users.updateOne(
  { name: "John" },
  { $pull: { skills: "Java" } }
)

// Remove object from array
db.users.updateOne(
  { name: "John" },
  { $pull: { comments: { _id: ObjectId("...") } } }
)

// Remove with condition
db.users.updateOne(
  { name: "John" },
  { $pull: { scores: { $lt: 50 } } }  // Remove scores < 50
)

// Update many and pull
db.orders.updateMany(
  { status: "cancelled" },
  { $pull: { items: { cancelled: true } } }
)
```

---

## Update Examples with Node.js

```javascript
const { MongoClient } = require('mongodb');

async function updateExamples() {
  const client = new MongoClient('mongodb://localhost:27017');
  
  try {
    await client.connect();
    const db = client.db('myapp');
    const users = db.collection('users');
    
    // 1. Update one document
    await users.updateOne(
      { _id: ObjectId("507f1f77bcf86cd799439011") },
      { $set: { status: "active", lastLogin: new Date() } }
    );
    
    // 2. Update many documents
    await users.updateMany(
      { age: { $lt: 18 } },
      { $set: { accountType: "minor" } }
    );
    
    // 3. Array operations
    await users.updateOne(
      { _id: ObjectId("...") },
      { 
        $inc: { totalPosts: 1 },
        $push: { recentTags: "nodejs" },
        $set: { lastUpdated: new Date() }
      }
    );
    
  } finally {
    await client.close();
  }
}

updateExamples().catch(console.error);
```

---

## Update Operators Summary

| Operator | Purpose |
|----------|---------|
| `$set` | Set field value |
| `$unset` | Remove field |
| `$inc` | Increment number |
| `$push` | Add to array |
| `$pull` | Remove from array |
| `$addToSet` | Add to array if not exists |
| `$pop` | Remove first/last array element |
| `$rename` | Rename field |
| `$currentDate` | Set to current date |
| `$mul` | Multiply numeric value |
| `$min` | Set if less than |
| `$max` | Set if greater than |

---

## GitHub Resources

- **MongoDB Update**: [mongodb/docs-update](https://github.com/mongodb/docs)
- **Update Operators**: [mongodb/update-operators](https://github.com/mongodb/mongo-manual)

---

Navigation: [← Chapter 13: Expressions & Projections](../ch13-expressions-projections/README.md) | [↑ Index](../../index.md) | [→ Chapter 15: CRUD Operations - Delete](../ch15-crud-operations-delete/README.md)
