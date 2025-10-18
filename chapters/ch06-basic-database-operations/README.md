# Chapter 6: Basic Database Operations

## 6.1 Creating Databases (use command)

In MongoDB, databases are created implicitly when you first write data to them.

### **Creating a Database**

```javascript
// Connect to MongoDB
mongosh

// Create/switch to database (created on first write)
use myapp

// Verify current database
db
// Output: myapp

// Show all databases
show dbs
// Output:
// admin     40.00 KiB
// config    12.00 KiB
// local     72.00 KiB
```

**Important:** A new database won't appear in `show dbs` until you write data to it.

### **Creating Database with Data**

```javascript
use myapp

// Insert data (creates database if not exists)
db.users.insertOne({ name: "John", age: 30 })

// Now the database is created
show dbs
// Output now includes: myapp
```

### **Database Naming Rules**

- Case-insensitive on Linux/macOS
- Max 64 characters (Windows) or 255 bytes (others)
- Cannot contain `/\. "$*<>:|?` characters
- Reserved databases: `admin`, `config`, `local`
- Recommended: lowercase, no spaces

### **Switching Between Databases**

```javascript
use database1
db.collection1.find()

use database2
db.collection2.find()

// Show current database
db  // Output: database2
```

### **Database Access Methods**

```javascript
// Method 1: Direct database creation (implicit)
use newdb
db.collection.insertOne({})

// Method 2: URI specification
// mongosh mongodb://localhost:27017/mydb

// Method 3: Node.js driver
const client = new MongoClient('mongodb://localhost:27017');
const db = client.db('mydb');
```

---

## 6.2 Showing Databases (show dbs)

### **List All Databases**

```javascript
show dbs
// or
db.adminCommand({ listDatabases: 1 })

// Output example:
// admin     40.00 KiB
// config    60.00 KiB
// local     72.00 KiB
// myapp    128.00 KiB
// testdb    256.00 KiB
```

### **Get Database Size**

```javascript
// Size in bytes of current database
db.stats()

// Output includes:
// {
//   "db": "myapp",
//   "collections": 5,
//   "views": 0,
//   "objects": 1250,
//   "avgObjSize": 2048,
//   "dataSize": 2560000,
//   "storageSize": 4096000,
//   "indexes": 3,
//   "indexSize": 1024000,
//   "fileSize": 8388608,
//   "ok": 1
// }
```

### **List Specific Database Info**

```javascript
// Get info about specific database
db.adminCommand({ listDatabases: 1 }).databases.forEach(db => {
  console.log(`${db.name}: ${(db.sizeOnDisk / 1024 / 1024).toFixed(2)} MB`)
})

// Output:
// admin: 0.04 MB
// config: 0.06 MB
// local: 0.07 MB
// myapp: 0.12 MB
```

### **Check Database Existence**

```javascript
use testdb

// Try to access
db.stats()  // Will show stats even if empty (after we write data)

// Or check in list
show dbs | grep testdb
```

---

## 6.3 Creating Collections

Collections are created implicitly when you insert data, or explicitly with specific options.

### **Implicit Creation (on first write)**

```javascript
use myapp

// Collection created automatically on insert
db.users.insertOne({ name: "John", age: 30 })

// Verify collection created
show collections
// Output:
// users
```

### **Explicit Collection Creation**

```javascript
use myapp

// Create collection with default options
db.createCollection("products")

// Create collection with options
db.createCollection("logs", {
  capped: true,
  size: 104857600,  // 100MB
  max: 100000       // max 100k documents
})

// Create with schema validation
db.createCollection("students", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["name", "email"],
      properties: {
        name: { bsonType: "string" },
        email: { bsonType: "string" },
        age: { bsonType: "int" }
      }
    }
  }
})
```

### **Collection Options**

| Option | Purpose |
|--------|---------|
| `capped` | Fixed-size collection (auto-delete old docs) |
| `size` | Max bytes in capped collection |
| `max` | Max documents in capped collection |
| `validator` | Schema validation rules |
| `validationLevel` | `off`, `moderate`, `strict` |
| `validationAction` | `error` (reject) or `warn` (log warning) |

### **Capped Collections**

```javascript
// Create capped collection (like queue/log)
db.createCollection("activity_log", {
  capped: true,
  size: 10000000,  // 10MB
  max: 50000       // max 50k documents
})

// Insert documents
db.activity_log.insertOne({ event: "login", user: "john" })

// Oldest documents automatically removed when size exceeded
```

---

## 6.4 Showing Collections

### **List All Collections**

```javascript
show collections

// Output:
// addresses
// comments
// posts
// users

// Or via command
db.getCollectionNames()
// Output: [ 'addresses', 'comments', 'posts', 'users' ]
```

### **Collection Statistics**

```javascript
// Get collection stats
db.users.stats()

// Output includes:
// {
//   "ns": "myapp.users",
//   "count": 1250,
//   "size": 2560000,
//   "avgObjSize": 2048,
//   "storageSize": 4096000,
//   "indexes": 2,
//   "indexSizes": { "_id_": 40960, "email_1": 20480 }
// }
```

### **Collection Info Details**

```javascript
// Document count
db.users.countDocuments()
// Output: 1250

// Estimated count (faster)
db.users.estimatedDocumentCount()
// Output: 1250

// List all indexes
db.users.getIndexes()
// Output: [
//   { "v": 2, "key": { "_id": 1 }, "name": "_id_" },
//   { "v": 2, "key": { "email": 1 }, "name": "email_1" }
// ]
```

### **View Collection Details**

```javascript
// Get collection info
db.getCollection("users").stats()

// Check if collection exists
db.getCollectionNames().includes("users")
// Output: true

// Get collection info with details
db.listCollections()
// Output includes all collections with metadata
```

---

## 6.5 Dropping Databases

### **Delete Entire Database**

```javascript
use myapp

// Drop database
db.dropDatabase()

// Output:
// { "ok": 1 }

// Verify deletion
show dbs  // myapp no longer listed

// Or check
use myapp
db.stats()  // Error: database not found
```

**⚠️ Warning:** This operation is permanent and irreversible!

### **Backup Before Dropping**

```bash
# Export database before dropping
mongodump --db myapp --out ./backup

# Drop database (safe after backup)
# mongosh
# use myapp
# db.dropDatabase()

# Restore if needed
mongorestore --db myapp ./backup/myapp
```

### **Conditional Drop**

```javascript
// Check if database exists and drop
if (db.adminCommand({ listDatabases: 1 }).databases.some(db => db.name === "myapp")) {
  use myapp
  db.dropDatabase()
  console.log("Database dropped")
} else {
  console.log("Database not found")
}
```

---

## 6.6 Dropping Collections

### **Delete Single Collection**

```javascript
use myapp

// Drop collection
db.users.drop()

// Output: true

// Verify
show collections  // users no longer listed
```

### **Drop Multiple Collections**

```javascript
// Drop multiple collections
db.users.drop()
db.posts.drop()
db.comments.drop()

// Or loop
["users", "posts", "comments"].forEach(col => {
  db[col].drop()
  console.log(`${col} dropped`)
})
```

### **Drop Collection if Exists**

```javascript
// Check before dropping
if (db.getCollectionNames().includes("users")) {
  db.users.drop()
  console.log("Collection dropped")
} else {
  console.log("Collection not found")
}
```

### **Delete All Data (Keep Collection)**

```javascript
// Delete all documents but keep collection
db.users.deleteMany({})

// Collection still exists
show collections  // users still listed

// But is empty
db.users.countDocuments()  // Output: 0
```

---

## Node.js Examples

### **Database Operations with mongodb driver**

```javascript
const { MongoClient } = require('mongodb');

async function main() {
  const client = new MongoClient('mongodb://localhost:27017');
  
  try {
    await client.connect();
    
    // Get database
    const db = client.db('myapp');
    
    // Create collection
    await db.createCollection('users');
    
    // List collections
    const collections = await db.listCollections().toArray();
    console.log(collections);
    
    // Get collection stats
    const stats = await db.collection('users').stats();
    console.log(stats);
    
    // Drop collection
    await db.collection('users').drop();
    
    // Drop database
    await client.db('myapp').dropDatabase();
    
  } finally {
    await client.close();
  }
}

main().catch(console.error);
```

---

## GitHub Resources

- **MongoDB Collection Methods**: [mongodb/docs-collection-api](https://github.com/mongodb/docs)
- **MongoDB Database Methods**: [mongodb/docs-database-api](https://github.com/mongodb/docs)
- **Database Tools**: [mongodb/mongo-tools](https://github.com/mongodb/mongo-tools)

---

Navigation: [← Chapter 5: MongoDB Installation & Setup](../ch05-mongodb-installation-setup/README.md) | [↑ Index](../../index.md) | [→ Chapter 7: CRUD Operations - Create](../ch07-crud-operations-create/README.md)
