# Chapter 8: CRUD Operations - Read

## 8.1 find() Method

**find()** retrieves multiple documents matching a query from a collection.

### **Basic Syntax**

```javascript
db.collection.find(query, projection)
```

### **Simple Queries**

```javascript
// Find all documents
db.users.find()

// Find with query filter
db.users.find({ age: 30 })

// Multiple conditions (AND by default)
db.users.find({ age: 30, status: "active" })
```

### **Find All Documents**

```javascript
db.users.find()

// Output: Returns cursor with all documents
// { _id: ObjectId(...), name: "John", age: 30, email: "john@ex.com" }
// { _id: ObjectId(...), name: "Jane", age: 25, email: "jane@ex.com" }
// { _id: ObjectId(...), name: "Bob", age: 35, email: "bob@ex.com" }
```

### **Pretty Print Results**

```javascript
// Format output for readability
db.users.find().pretty()

// Output:
// {
//   "_id": ObjectId("507f1f77bcf86cd799439011"),
//   "name": "John",
//   "age": 30,
//   "email": "john@ex.com"
// }
```

### **Count Results**

```javascript
// Count matching documents
db.users.find({ age: { $gt: 25 } }).count()
// Output: 2

// Or
db.users.countDocuments({ age: { $gt: 25 } })
// Output: 2
```

### **Node.js Example**

```javascript
const { MongoClient } = require('mongodb');

async function findUsers() {
  const client = new MongoClient('mongodb://localhost:27017');
  
  try {
    await client.connect();
    const db = client.db('myapp');
    
    // Find all
    const allUsers = await db.collection('users').find({}).toArray();
    console.log('All users:', allUsers);
    
    // Find with filter
    const activeUsers = await db.collection('users')
      .find({ status: "active" })
      .toArray();
    console.log('Active users:', activeUsers);
    
  } finally {
    await client.close();
  }
}

findUsers().catch(console.error);
```

---

## 8.2 findOne() Method

**findOne()** returns the first document matching the query (or null if none found).

### **Basic Syntax**

```javascript
db.collection.findOne(query, projection)
```

### **Find Single Document**

```javascript
// Find by ID
db.users.findOne({ _id: ObjectId("507f1f77bcf86cd799439011") })

// Find first matching
db.users.findOne({ age: 30 })

// Returns first match (or null):
// { _id: ObjectId(...), name: "John", age: 30, email: "john@ex.com" }
```

### **Get First Document**

```javascript
// Get first document (any)
db.users.findOne()

// Output: First document in collection
// { _id: ObjectId(...), name: "John", age: 30 }
```

### **Check if Document Exists**

```javascript
// Check existence
const user = db.users.findOne({ email: "john@example.com" });

if (user) {
  console.log("User found:", user);
} else {
  console.log("User not found");
}
```

### **Node.js Example**

```javascript
const { MongoClient } = require('mongodb');

async function findUser() {
  const client = new MongoClient('mongodb://localhost:27017');
  
  try {
    await client.connect();
    const db = client.db('myapp');
    
    // Find specific user
    const user = await db.collection('users').findOne({ 
      email: "john@example.com" 
    });
    
    if (user) {
      console.log('Found user:', user);
    } else {
      console.log('User not found');
    }
    
  } finally {
    await client.close();
  }
}

findUser().catch(console.error);
```

---

## 8.3 Filtering Documents

Filtering allows you to retrieve only documents matching specific criteria.

### **Equality Filter**

```javascript
// Exact match
db.users.find({ age: 30 })
db.users.find({ status: "active" })

// Multiple fields (AND)
db.users.find({ age: 30, status: "active" })
```

### **Comparison Operators**

```javascript
// Greater than
db.users.find({ age: { $gt: 30 } })

// Less than
db.users.find({ age: { $lt: 30 } })

// Greater or equal
db.users.find({ age: { $gte: 30 } })

// Less or equal
db.users.find({ age: { $lte: 30 } })

// Not equal
db.users.find({ age: { $ne: 30 } })
```

### **Array Filtering**

```javascript
// Check if value in array
db.users.find({ skills: { $in: ["JavaScript", "Python"] } })

// Check if value NOT in array
db.users.find({ skills: { $nin: ["Java"] } })

// Field contains specific element
db.users.find({ skills: "JavaScript" })
```

### **Nested Document Filtering**

```javascript
// Query nested field
db.users.find({ "address.city": "San Francisco" })

// Nested with comparison
db.users.find({ "address.zipcode": { $gt: 90000 } })
```

### **Logical Operators**

```javascript
// OR condition
db.users.find({ 
  $or: [
    { age: { $lt: 25 } },
    { age: { $gt: 60 } }
  ]
})

// AND condition
db.users.find({
  $and: [
    { age: { $gte: 25 } },
    { age: { $lte: 60 } }
  ]
})

// NOT operator
db.users.find({ age: { $not: { $gt: 30 } } })

// NOR
db.users.find({
  $nor: [
    { age: { $lt: 25 } },
    { status: "inactive" }
  ]
})
```

### **Text Search**

```javascript
// Create text index (must do first)
db.users.createIndex({ name: "text", bio: "text" })

// Search for text
db.users.find({ $text: { $search: "mongodb" } })

// With regex
db.users.find({ name: /john/i })  // Case-insensitive
```

---

## 8.4 Query Syntax

MongoDB query syntax uses JSON-like documents with operators.

### **Basic Query Structure**

```javascript
// Simple: { field: value }
{ name: "John" }

// Operators: { field: { $operator: value } }
{ age: { $gt: 30 } }

// Logical: { $operator: [ query1, query2 ] }
{ $or: [ { age: 30 }, { age: 25 } ] }
```

### **Query Examples**

```javascript
// AND (implicit - multiple fields)
db.users.find({ age: 30, status: "active" })
// Find users age 30 AND status active

// OR (explicit)
db.users.find({ 
  $or: [ 
    { age: 30 }, 
    { age: 25 } 
  ]
})
// Find users age 30 OR age 25

// Complex query
db.users.find({
  $or: [
    { age: { $lt: 25 } },
    { age: { $gt: 60 } }
  ],
  status: "active"
})
// Find active users younger than 25 OR older than 60
```

### **Null Queries**

```javascript
// Find documents where field is null
db.users.find({ phone: null })

// Find documents where field exists but might be null
db.users.find({ phone: { $exists: true } })

// Find documents missing field
db.users.find({ phone: { $exists: false } })
```

### **Array Queries**

```javascript
// Array contains element
db.users.find({ tags: "javascript" })

// Array size
db.users.find({ tags: { $size: 3 } })

// Element at specific index
db.users.find({ "tags.0": "javascript" })

// All elements match
db.users.find({ tags: { $all: ["javascript", "mongodb"] } })

// At least one element matches
db.users.find({ tags: { $elemMatch: { $gt: 5 } } })
```

---

## 8.5 Basic Querying Examples

### **Employee Database Example**

```javascript
use company

// Sample documents
db.employees.insertMany([
  { _id: 1, name: "John", department: "Engineering", salary: 100000, active: true },
  { _id: 2, name: "Jane", department: "Sales", salary: 80000, active: true },
  { _id: 3, name: "Bob", department: "Engineering", salary: 95000, active: false },
  { _id: 4, name: "Alice", department: "HR", salary: 75000, active: true },
  { _id: 5, name: "Charlie", department: "Sales", salary: 85000, active: true }
])

// Query: Find all active employees
db.employees.find({ active: true })
// Returns: John, Jane, Alice, Charlie

// Query: Find employees in Engineering
db.employees.find({ department: "Engineering" })
// Returns: John, Bob

// Query: Find employees with salary > 85000
db.employees.find({ salary: { $gt: 85000 } })
// Returns: John, Bob, Alice (no, Alice is 75000)
// Actually returns: John (100000), Bob (95000)

// Query: Find active employees in Engineering
db.employees.find({ 
  active: true, 
  department: "Engineering" 
})
// Returns: John

// Query: Find employees in Engineering OR Sales
db.employees.find({
  $or: [
    { department: "Engineering" },
    { department: "Sales" }
  ]
})
// Returns: John, Jane, Bob, Charlie

// Query: Find inactive or low-salary employees (< 80000)
db.employees.find({
  $or: [
    { active: false },
    { salary: { $lt: 80000 } }
  ]
})
// Returns: Bob (inactive), Alice (75000)

// Count active employees
db.employees.find({ active: true }).count()
// Output: 4

// Get first active employee
db.employees.findOne({ active: true })
// Output: John (first match)
```

### **E-commerce Product Example**

```javascript
use shop

db.products.insertMany([
  { 
    _id: 1, 
    name: "Laptop", 
    price: 1200, 
    category: "Electronics",
    stock: 15,
    tags: ["computers", "portable"]
  },
  { 
    _id: 2, 
    name: "Mouse", 
    price: 25, 
    category: "Electronics",
    stock: 100,
    tags: ["accessories", "peripherals"]
  },
  { 
    _id: 3, 
    name: "Monitor", 
    price: 300, 
    category: "Electronics",
    stock: 5,
    tags: ["display", "accessories"]
  }
])

// Find products under $100
db.products.find({ price: { $lt: 100 } })
// Returns: Mouse (25)

// Find products with low stock (< 10)
db.products.find({ stock: { $lt: 10 } })
// Returns: Monitor (5)

// Find products in Electronics
db.products.find({ category: "Electronics" })
// Returns: All products

// Find products tagged with 'accessories'
db.products.find({ tags: "accessories" })
// Returns: Mouse, Monitor

// Find expensive products (> 300)
db.products.find({ price: { $gt: 300 } })
// Returns: Laptop (1200)

// Find products between $200-$500
db.products.find({
  price: { $gte: 200, $lte: 500 }
})
// Returns: Monitor (300)
```

---

## GitHub Resources

- **MongoDB Find Documentation**: [mongodb/docs-find](https://github.com/mongodb/docs)
- **Query Operators**: [mongodb/query-operators](https://github.com/mongodb/mongo-manual)
- **MongoDB Examples**: [mongodb/mongo-examples](https://github.com/mongodb/mongo-examples)
- **Node.js Driver Find**: [mongodb/node-driver-find](https://github.com/mongodb/node-mongodb-native)

---

Navigation: [← Chapter 7: CRUD Operations - Create](../ch07-crud-operations-create/README.md) | [↑ Index](../../index.md) | [→ Chapter 9: Comparison Operators](../ch09-comparison-operators/README.md)
