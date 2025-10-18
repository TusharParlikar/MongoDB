# Chapter 18: Indexing

## 18.1 What are Indexes?

Indexes speed up query execution by organizing data for fast lookups.

### **Without Index**

```
Find { age: 30 }
├─ Scan document 1: age = 25 (no match)
├─ Scan document 2: age = 30 (MATCH!)
├─ Scan document 3: age = 25 (no match)
└─ Scan 1 million documents ← SLOW
```

### **With Index**

```
Find { age: 30 }
├─ Look up in B-tree index
└─ Jump directly to matching docs ← FAST
```

---

## 18.2 Creating Indexes

### **Single Field Index**

```javascript
// Ascending index
db.users.createIndex({ age: 1 })

// Descending index
db.users.createIndex({ age: -1 })

// Query now uses index
db.users.find({ age: 30 }).explain()
```

### **Compound Index**

```javascript
// Multiple fields
db.users.createIndex({ department: 1, salary: -1 })

// Useful for queries with both fields
db.users.find({ department: "Engineering", salary: { $gt: 100000 } })
```

### **Unique Index**

```javascript
// Enforce uniqueness
db.users.createIndex({ email: 1 }, { unique: true })

// Prevents duplicates
db.users.insertOne({ email: "duplicate@ex.com" })  // Error
```

### **Text Index**

```javascript
// Full-text search
db.posts.createIndex({ title: "text", content: "text" })

// Search
db.posts.find({ $text: { $search: "mongodb" } })
```

---

## 18.3 Types of Indexes

| Type | Purpose | Syntax |
|------|---------|--------|
| Single Field | Fast lookup | `{ field: 1 }` |
| Compound | Multi-field queries | `{ f1: 1, f2: -1 }` |
| Text | Full-text search | `{ field: "text" }` |
| Geospatial | Location queries | `{ location: "2dsphere" }` |
| TTL | Auto-expire docs | `{ createdAt: 1 }` with expireAfterSeconds |
| Sparse | Skip null/missing | `{ sparse: true }` |
| Unique | Enforce uniqueness | `{ unique: true }` |

---

## 18.4 Index Performance

### **View Index Usage**

```javascript
db.users.find({ age: 30 }).explain("executionStats")

// Output shows:
// executionStages: { stage: "COLLSCAN" } // No index used
// executionStages: { stage: "IXSCAN" }   // Index used
```

### **Index Size**

```javascript
db.users.stats().indexSizes
// { "_id_": 40960, "age_1": 20480 }
```

---

## 18.5 Managing Indexes

### **List Indexes**

```javascript
db.users.getIndexes()
// [
//   { v: 2, key: { _id: 1 } },
//   { v: 2, key: { age: 1 } }
// ]
```

### **Drop Index**

```javascript
db.users.dropIndex("age_1")
db.users.dropIndexes()  // Drop all except _id
```

### **Node.js Example**

```javascript
const { MongoClient } = require('mongodb');

async function manageIndexes() {
  const client = new MongoClient('mongodb://localhost:27017');
  
  try {
    await client.connect();
    const users = client.db('myapp').collection('users');
    
    // Create indexes
    await users.createIndex({ email: 1 }, { unique: true });
    await users.createIndex({ age: 1 });
    await users.createIndex({ "address.city": 1 });
    
    // Get indexes
    const indexes = await users.getIndexes();
    console.log('Indexes:', indexes);
    
    // Drop index
    await users.dropIndex('age_1');
    
  } finally {
    await client.close();
  }
}

manageIndexes().catch(console.error);
```

---

## GitHub Resources

- **MongoDB Indexes**: [mongodb/docs-indexes](https://github.com/mongodb/docs)

---

Navigation: [← Chapter 17: Relationships in MongoDB](../ch17-relationships-in-mongodb/README.md) | [↑ Index](../../index.md) | [→ Chapter 19: Aggregation Framework - Basics](../ch19-aggregation-framework-basics/README.md)
