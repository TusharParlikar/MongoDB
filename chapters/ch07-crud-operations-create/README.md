# Chapter 7: CRUD Operations - Create

## 7.1 insertOne() Method

**insertOne()** inserts a single document into a collection.

### **Basic Syntax**

```javascript
db.collection.insertOne(document, options)
```

### **Simple Insert**

```javascript
use myapp

db.users.insertOne({
  name: "John Doe",
  email: "john@example.com",
  age: 30,
  active: true
})

// Output:
// {
//   "acknowledged": true,
//   "insertedId": ObjectId("507f1f77bcf86cd799439011")
// }
```

### **Insert with Custom _id**

```javascript
// By default, MongoDB generates _id
db.users.insertOne({
  _id: 1,
  name: "John",
  email: "john@example.com"
})

// Output:
// {
//   "acknowledged": true,
//   "insertedId": 1
// }
```

### **Insert with Nested Documents**

```javascript
db.users.insertOne({
  name: "Alice",
  email: "alice@example.com",
  address: {
    street: "123 Main St",
    city: "San Francisco",
    zipcode: "94102"
  },
  skills: ["JavaScript", "Python", "MongoDB"]
})
```

### **Node.js Example**

```javascript
const { MongoClient } = require('mongodb');

async function insertUser() {
  const client = new MongoClient('mongodb://localhost:27017');
  
  try {
    await client.connect();
    const db = client.db('myapp');
    const result = await db.collection('users').insertOne({
      name: "John",
      email: "john@example.com",
      age: 30
    });
    console.log('Inserted ID:', result.insertedId);
  } finally {
    await client.close();
  }
}

insertUser().catch(console.error);
```

---

## 7.2 insertMany() Method

**insertMany()** inserts multiple documents in a single operation.

### **Basic Syntax**

```javascript
db.collection.insertMany(documents, options)
```

### **Insert Multiple Documents**

```javascript
db.users.insertMany([
  { name: "John", email: "john@example.com", age: 30 },
  { name: "Jane", email: "jane@example.com", age: 25 },
  { name: "Bob", email: "bob@example.com", age: 35 }
])

// Output:
// {
//   "acknowledged": true,
//   "insertedIds": [
//     ObjectId("507f1f77bcf86cd799439011"),
//     ObjectId("507f1f77bcf86cd799439012"),
//     ObjectId("507f1f77bcf86cd799439013")
//   ]
// }
```

### **Insert Many with Custom IDs**

```javascript
db.users.insertMany([
  { _id: 1, name: "User 1", status: "active" },
  { _id: 2, name: "User 2", status: "inactive" },
  { _id: 3, name: "User 3", status: "active" }
])
```

### **insertMany() Options**

```javascript
// ordered: true (default) - stops on first error
// ordered: false - inserts all possible documents

db.users.insertMany(
  [
    { name: "John", email: "john@ex.com" },
    { name: "Jane", email: "jane@ex.com" }
  ],
  { ordered: true }  // Stop if duplicate email
)
```

### **Node.js Example**

```javascript
const { MongoClient } = require('mongodb');

async function insertMultipleUsers() {
  const client = new MongoClient('mongodb://localhost:27017');
  
  try {
    await client.connect();
    const db = client.db('myapp');
    
    const users = [
      { name: "John", email: "john@example.com", age: 30 },
      { name: "Jane", email: "jane@example.com", age: 25 },
      { name: "Bob", email: "bob@example.com", age: 35 }
    ];
    
    const result = await db.collection('users').insertMany(users);
    console.log(`Inserted ${result.insertedIds.length} documents`);
  } finally {
    await client.close();
  }
}

insertMultipleUsers().catch(console.error);
```

---

## 7.3 Using Quotes in Field Names

MongoDB allows flexible field naming, including spaces and special characters.

### **Field Name Guidelines**

```javascript
// Valid field names:
db.collection.insertOne({
  "first name": "John",        // Space in name
  "user-email": "john@ex.com", // Hyphen
  "email_address": "john@ex.com", // Underscore
  "123email": "john@ex.com",   // Starts with number
  "$price": 100                // $ prefix (query operators use this)
})

// Invalid field names:
{
  ".name": "John"              // ❌ Cannot start with dot
  "name.": "John"              // ❌ Cannot end with dot
}
```

### **Accessing Fields with Special Names**

```javascript
// Insert document with special field names
db.products.insertOne({
  "product name": "Laptop",
  "unit price": 999.99
})

// Query using bracket notation
db.products.find({ "product name": "Laptop" })

// Or dot notation with quotes
db.products.find({ "\"product name\"": "Laptop" })
```

### **Nested Field Names with Quotes**

```javascript
db.users.insertOne({
  name: "John",
  "contact info": {
    "email address": "john@example.com",
    "phone number": "555-1234"
  }
})

// Query nested field with spaces
db.users.find({ "contact info.email address": "john@example.com" })
```

### **Best Practice**

```javascript
// ✅ Recommended: Use camelCase or snake_case (no spaces)
db.users.insertOne({
  firstName: "John",
  lastName: "Doe",
  emailAddress: "john@example.com",
  user_status: "active"
})

// ❌ Avoid: Spaces and special characters
db.users.insertOne({
  "first name": "John",
  "last name": "Doe"
})
```

---

## 7.4 Ordered vs Unordered Inserts

MongoDB supports both ordered and unordered bulk inserts with different behaviors on errors.

### **Ordered Insert (Default)**

```javascript
// Stops on first error
db.users.insertMany(
  [
    { _id: 1, name: "John" },
    { _id: 2, name: "Jane" },  // ← Error: duplicate _id
    { _id: 3, name: "Bob" }    // ← Not inserted
  ],
  { ordered: true }  // default
)

// Result: Only documents 1 inserted, 2 causes error, 3 skipped
```

### **Unordered Insert**

```javascript
// Continues despite errors
db.users.insertMany(
  [
    { _id: 1, name: "John" },
    { _id: 2, name: "Jane" },  // ← Error: duplicate _id
    { _id: 3, name: "Bob" }    // ← Still inserted!
  ],
  { ordered: false }
)

// Result: Documents 1 and 3 inserted, 2 failed but didn't stop insertion
```

### **Comparison**

| Aspect | Ordered (true) | Unordered (false) |
|--------|---|---|
| **On Error** | Stops immediately | Continues |
| **Performance** | Slightly slower | Slightly faster |
| **Use Case** | Transactions, data integrity | Bulk imports, tolerates failures |
| **Inserted Count** | Up to first error | All possible documents |

### **Node.js Example**

```javascript
async function bulkInsert() {
  const client = new MongoClient('mongodb://localhost:27017');
  
  try {
    await client.connect();
    const db = client.db('myapp');
    
    // Unordered: continue on errors
    const result = await db.collection('users').insertMany(
      [
        { _id: 1, name: "John" },
        { _id: 1, name: "Duplicate" },  // Error
        { _id: 2, name: "Jane" }
      ],
      { ordered: false }
    );
    
    console.log(`Inserted: ${result.insertedIds.length} documents`);
  } catch (err) {
    console.error('Bulk insert error:', err.message);
  } finally {
    await client.close();
  }
}
```

---

## 7.5 Handling Duplicate Key Errors

MongoDB enforces unique indexes, causing duplicate key errors when inserting duplicate values.

### **Duplicate _id Error**

```javascript
// First insert (success)
db.users.insertOne({ _id: 1, name: "John" })

// Second insert with same _id (ERROR)
db.users.insertOne({ _id: 1, name: "Jane" })

// MongoError: E11000 duplicate key error collection: myapp.users index: _id_ dup key: { _id: 1 }
```

### **Unique Index Error**

```javascript
// Create unique index on email
db.users.createIndex({ email: 1 }, { unique: true })

// First insert
db.users.insertOne({ name: "John", email: "john@example.com" })

// Duplicate email (ERROR)
db.users.insertOne({ name: "Jane", email: "john@example.com" })

// MongoError: E11000 duplicate key error collection: myapp.users index: email_1 dup key: { email: "john@example.com" }
```

### **Handling Errors in Node.js**

```javascript
async function insertWithErrorHandling() {
  const client = new MongoClient('mongodb://localhost:27017');
  
  try {
    await client.connect();
    const db = client.db('myapp');
    
    // Unique index
    await db.collection('users').createIndex({ email: 1 }, { unique: true });
    
    // First insert
    await db.collection('users').insertOne({
      name: "John",
      email: "john@example.com"
    });
    console.log('First insert successful');
    
    // Try duplicate insert
    await db.collection('users').insertOne({
      name: "Jane",
      email: "john@example.com"
    });
  } catch (err) {
    if (err.code === 11000) {
      console.log('Duplicate key error. Skipping insert.');
      console.log('Duplicate field:', Object.keys(err.keyPattern));
    } else {
      console.error('Other error:', err.message);
    }
  } finally {
    await client.close();
  }
}

insertWithErrorHandling().catch(console.error);
```

### **Check for Existing Document Before Insert**

```javascript
async function insertOrSkip(userData) {
  const client = new MongoClient('mongodb://localhost:27017');
  
  try {
    await client.connect();
    const db = client.db('myapp');
    const users = db.collection('users');
    
    // Check if user exists
    const existing = await users.findOne({ email: userData.email });
    
    if (existing) {
      console.log('User already exists:', existing._id);
      return existing._id;
    }
    
    // Insert if not exists
    const result = await users.insertOne(userData);
    console.log('User inserted:', result.insertedId);
    return result.insertedId;
    
  } finally {
    await client.close();
  }
}

insertOrSkip({ name: "John", email: "john@example.com" })
  .catch(console.error);
```

### **updateOne() with upsert (Alternative)**

```javascript
// Insert or update if exists (doesn't error)
db.users.updateOne(
  { email: "john@example.com" },
  { $set: { name: "John", age: 30 } },
  { upsert: true }  // Insert if not found
)

// Result:
// {
//   "matched": 0,
//   "modified": 0,
//   "upsertedId": ObjectId("...")
// }
```

---

## 7.6 Case Sensitivity in MongoDB

MongoDB field names and values are **case-sensitive**. Database and collection names follow OS case sensitivity rules.

### **Field Name Case Sensitivity**

```javascript
// These are different fields:
db.users.insertOne({
  name: "John",      // lowercase
  Name: "Jane",      // uppercase N
  NAME: "Bob"        // all uppercase
})

// All three fields exist in the document
db.users.findOne()
// {
//   name: "John",
//   Name: "Jane",
//   NAME: "Bob"
// }

// Case matters in queries
db.users.find({ name: "John" })      // ✅ Matches
db.users.find({ Name: "John" })      // ❌ No match
db.users.find({ NAME: "John" })      // ❌ No match
```

### **Value Case Sensitivity**

```javascript
// Insert values
db.users.insertOne({ name: "john" })
db.users.insertOne({ name: "JOHN" })
db.users.insertOne({ name: "John" })

// Query with exact case
db.users.find({ name: "john" })     // 1 match
db.users.find({ name: "JOHN" })     // 1 match
db.users.find({ name: "John" })     // 1 match

// Case-insensitive query (regex)
db.users.find({ name: /^john$/i })  // 3 matches (i = ignore case)
```

### **Database & Collection Names**

```javascript
// Database names: case-insensitive on Windows, case-sensitive on Linux
use MyApp   // On Windows: creates myapp
use MYAPP   // On Windows: accesses same myapp

// Collections: case-sensitive on all platforms
db.createCollection("Users")
db.createCollection("users")

// These are two different collections
show collections
// Users
// users
```

### **Best Practices**

```javascript
// ✅ Consistent naming convention
db.users.insertOne({
  firstName: "John",
  lastName: "Doe",
  emailAddress: "john@example.com"
})

// ❌ Inconsistent naming
db.users.insertOne({
  firstName: "John",
  last_name: "Doe",
  EmailAddress: "john@example.com"
})
```

---

## GitHub Resources

- **MongoDB Insert Documentation**: [mongodb/docs-insert](https://github.com/mongodb/docs)
- **MongoDB Node.js Driver**: [mongodb/node-mongodb-native](https://github.com/mongodb/node-mongodb-native)
- **Insert Examples**: [mongodb/mongo-examples](https://github.com/mongodb/mongo-examples)

---

Navigation: [← Chapter 6: Basic Database Operations](../ch06-basic-database-operations/README.md) | [↑ Index](../../index.md) | [→ Chapter 8: CRUD Operations - Read](../ch08-crud-operations-read/README.md)
