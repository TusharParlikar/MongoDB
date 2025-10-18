# Chapter 13: Expressions & Projections

Projections control which fields are returned from queries.

## 13.1 Projection Basics

### **What is a Projection?**

A projection specifies which fields to include or exclude from query results, reducing data transfer and improving performance.

### **Syntax**

```javascript
db.collection.find(query, projection)

// Or with mongosh
db.collection.find(query, { field: 1 })
```

### **Projection Values**

| Value | Meaning |
|-------|---------|
| 1 (or true) | Include field |
| 0 (or false) | Exclude field |
| -1 | Exclude field |

---

## 13.2 Including/Excluding Fields

### **Include Specific Fields**

```javascript
// Return only name and email (plus _id by default)
db.users.find({}, { name: 1, email: 1 })

// Output:
// { _id: ObjectId(...), name: "John", email: "john@ex.com" }
// { _id: ObjectId(...), name: "Jane", email: "jane@ex.com" }
```

### **Exclude Specific Fields**

```javascript
// Return everything except password
db.users.find({}, { password: 0 })

// Output:
// { _id: ObjectId(...), name: "John", email: "john@ex.com", age: 30 }
// (password field removed)
```

### **Exclude _id**

```javascript
// Exclude _id field
db.users.find({}, { _id: 0, name: 1, email: 1 })

// Output:
// { name: "John", email: "john@ex.com" }
```

### **Important Rules**

```javascript
// ❌ Cannot mix include and exclude (except _id)
db.users.find({}, { name: 1, password: 0 })  // ERROR

// ✅ Correct: include with _id excluded
db.users.find({}, { _id: 0, name: 1, email: 1 })

// ✅ Correct: exclude with _id included
db.users.find({}, { password: 0, ssn: 0 })
```

---

## 13.3 Using Expressions in Queries

Expressions calculate values within queries (aggregation pipeline).

### **Arithmetic Expressions**

```javascript
// Use aggregation pipeline for expressions
db.products.aggregate([
  {
    $project: {
      name: 1,
      price: 1,
      discountedPrice: { $multiply: ["$price", 0.9] }  // 10% discount
    }
  }
])

// Output:
// { name: "Laptop", price: 1000, discountedPrice: 900 }
```

### **String Expressions**

```javascript
// Concatenate strings
db.users.aggregate([
  {
    $project: {
      fullName: { $concat: ["$firstName", " ", "$lastName"] },
      email: 1
    }
  }
])

// Output:
// { fullName: "John Doe", email: "john@ex.com" }
```

### **Conditional Expressions**

```javascript
// Use $cond for if-then-else
db.orders.aggregate([
  {
    $project: {
      orderId: 1,
      total: 1,
      category: {
        $cond: [
          { $gte: ["$total", 100] },
          "high-value",
          "regular"
        ]
      }
    }
  }
])
```

---

## 13.4 Field Selection Patterns

### **Minimal Projection (Select Key Fields Only)**

```javascript
// Load only what you need
db.users.find({}, { _id: 1, name: 1, email: 1 })

// Benefits:
// - Reduced document size
// - Faster network transfer
// - Less memory usage
```

### **Security Projection (Exclude Sensitive Data)**

```javascript
// Hide passwords and sensitive info
db.users.find({}, { password: 0, ssn: 0, creditCard: 0 })

// Or explicitly include safe fields
db.users.find({}, { _id: 0, name: 1, email: 1, age: 1 })
```

### **Performance Projection (Nested Fields)**

```javascript
// Include nested fields
db.users.find({}, { name: 1, "address.city": 1, "address.zip": 1 })

// Output:
// {
//   _id: ObjectId(...),
//   name: "John",
//   address: { city: "SF", zip: "94102" }
// }
```

### **Array Projection**

```javascript
// Project only first element of array
db.users.find({}, { name: 1, skills: { $slice: 2 } })
// Returns first 2 skills

// Skip first, return next 5
db.users.find({}, { name: 1, skills: { $slice: [1, 5] } })
```

---

## Node.js Projection Examples

```javascript
const { MongoClient } = require('mongodb');

async function projectionExamples() {
  const client = new MongoClient('mongodb://localhost:27017');
  
  try {
    await client.connect();
    const db = client.db('myapp');
    const users = db.collection('users');
    
    // 1. Select specific fields
    const result1 = await users.find(
      {},
      { projection: { name: 1, email: 1 } }
    ).toArray();
    console.log('Select fields:', result1);
    
    // 2. Exclude sensitive fields
    const result2 = await users.find(
      {},
      { projection: { password: 0, ssn: 0 } }
    ).toArray();
    console.log('Exclude fields:', result2);
    
    // 3. Nested field projection
    const result3 = await users.find(
      {},
      { projection: { name: 1, "address.city": 1 } }
    ).toArray();
    console.log('Nested fields:', result3);
    
  } finally {
    await client.close();
  }
}

projectionExamples().catch(console.error);
```

---

## GitHub Resources

- **MongoDB Projections**: [mongodb/docs-projections](https://github.com/mongodb/docs)
- **Field Selection**: [mongodb/projection-operators](https://github.com/mongodb/mongo-manual)

---

Navigation: [← Chapter 12: Element Operators](../ch12-element-operators/README.md) | [↑ Index](../../index.md) | [→ Chapter 14: CRUD Operations - Update](../ch14-crud-operations-update/README.md)
