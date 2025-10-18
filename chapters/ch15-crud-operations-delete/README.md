# Chapter 15: CRUD Operations - Delete

## 15.1 deleteOne() Method

**deleteOne()** deletes the first document matching the filter.

### **Syntax**

```javascript
db.collection.deleteOne(filter, options)
```

### **Examples**

```javascript
// Delete specific user
db.users.deleteOne({ _id: ObjectId("507f1f77bcf86cd799439011") })

// Output:
// {
//   "acknowledged": true,
//   "deletedCount": 1
// }

// Delete first user named John
db.users.deleteOne({ name: "John" })

// Delete first inactive user
db.users.deleteOne({ status: "inactive" })
```

---

## 15.2 deleteMany() Method

**deleteMany()** deletes all documents matching the filter.

### **Syntax**

```javascript
db.collection.deleteMany(filter)
```

### **Examples**

```javascript
// Delete all inactive users
db.users.deleteMany({ status: "inactive" })

// Output:
// {
//   "acknowledged": true,
//   "deletedCount": 47
// }

// Delete old posts
db.posts.deleteMany({ 
  createdAt: { $lt: ISODate("2020-01-01") }
})

// Delete all documents (empty filter)
db.logs.deleteMany({})

// Conditional delete
db.orders.deleteMany({
  $or: [
    { status: "cancelled" },
    { status: "expired" }
  ]
})
```

---

## 15.3 Safe Deletion Practices

### **Always Backup Before Bulk Delete**

```bash
# Backup collection before deletion
mongodump --db myapp --collection users --out ./backup

# Or export to JSON
mongoexport --db myapp --collection users --out users.json
```

### **Test Query Before Delete**

```javascript
// ALWAYS verify what you're deleting first
db.users.find({ status: "inactive" }).count()
// Returns: 47

// Then delete
db.users.deleteMany({ status: "inactive" })
```

### **Use Transactions for Critical Deletes**

```javascript
// MongoDB 4.0+ supports transactions
async function safeDelete() {
  const session = client.startSession();
  const transactionOptions = {
    readConcern: { level: "snapshot" },
    writeConcern: { w: "majority" },
    readPreference: "primary"
  };
  
  try {
    await session.withTransaction(async () => {
      await db.users.deleteMany({ status: "inactive" }, { session });
      // Rolls back if error occurs
    }, transactionOptions);
  } finally {
    await session.endSession();
  }
}
```

### **Archive Before Delete**

```javascript
// 1. Copy to archive collection
db.users.insertMany(
  db.users.find({ status: "inactive" }).toArray()
)

// 2. Move data
db.users_archive.insertMany(
  db.users.find({ status: "inactive" }).toArray()
)

// 3. Delete original
db.users.deleteMany({ status: "inactive" })
```

### **Soft Delete Pattern (Recommended)**

```javascript
// Instead of deleting, mark as deleted
db.users.updateMany(
  { status: "inactive" },
  { $set: { deleted: true, deletedAt: new Date() } }
)

// Query excludes deleted docs
db.users.find({ deleted: { $ne: true } })

// Can recover deleted docs later
db.users.updateOne(
  { _id: ObjectId("..."), deleted: true },
  { $unset: { deleted: "", deletedAt: "" } }
)
```

### **Node.js Safe Delete Example**

```javascript
const { MongoClient } = require('mongodb');

async function safeDeleteInactive() {
  const client = new MongoClient('mongodb://localhost:27017');
  
  try {
    await client.connect();
    const db = client.db('myapp');
    const users = db.collection('users');
    
    // 1. Count what will be deleted
    const count = await users.countDocuments({ status: "inactive" });
    console.log(`Will delete ${count} inactive users`);
    
    // 2. Confirm (in real app, ask user)
    if (count > 1000) {
      console.log('Too many! Aborting for safety');
      return;
    }
    
    // 3. Archive first
    const inactiveUsers = await users.find({ status: "inactive" }).toArray();
    if (inactiveUsers.length > 0) {
      await db.collection('users_archive').insertMany(inactiveUsers);
      console.log(`Archived ${inactiveUsers.length} users`);
    }
    
    // 4. Delete
    const result = await users.deleteMany({ status: "inactive" });
    console.log(`Deleted ${result.deletedCount} users`);
    
  } catch (err) {
    console.error('Delete operation failed:', err.message);
    // Archive should have succeeded, delete failed
  } finally {
    await client.close();
  }
}

safeDeleteInactive().catch(console.error);
```

### **Bulk Delete with Timeout**

```javascript
// Delete with timeout to prevent long locks
async function deleteWithTimeout(filter, timeoutMs = 30000) {
  const client = new MongoClient('mongodb://localhost:27017');
  
  try {
    await client.connect();
    const db = client.db('myapp');
    
    const promise = db.collection('users').deleteMany(filter);
    const timeoutPromise = new Promise((_, reject) => 
      setTimeout(() => reject(new Error('Delete timeout')), timeoutMs)
    );
    
    const result = await Promise.race([promise, timeoutPromise]);
    return result;
  } finally {
    await client.close();
  }
}

deleteWithTimeout({ status: "inactive" }, 5000).catch(console.error);
```

### **Dry Run (Preview Deletion)**

```javascript
async function previewDeletion(filter) {
  const client = new MongoClient('mongodb://localhost:27017');
  
  try {
    await client.connect();
    const db = client.db('myapp');
    
    // Get what would be deleted
    const preview = await db.collection('users')
      .find(filter)
      .limit(10)
      .toArray();
    
    const total = await db.collection('users')
      .countDocuments(filter);
    
    console.log(`Would delete ${total} documents`);
    console.log('First 10:');
    console.table(preview);
    
    return { total, preview };
  } finally {
    await client.close();
  }
}

previewDeletion({ status: "inactive" }).catch(console.error);
```

---

## GitHub Resources

- **MongoDB Delete**: [mongodb/docs-delete](https://github.com/mongodb/docs)
- **Safe Practices**: [mongodb/best-practices](https://github.com/mongodb/mongo-manual)

---

Navigation: [← Chapter 14: CRUD Operations - Update](../ch14-crud-operations-update/README.md) | [↑ Index](../../index.md) | [→ Chapter 16: Embedded Documents](../ch16-embedded-documents/README.md)
