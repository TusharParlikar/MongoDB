# Chapter 4: MongoDB Architecture

## 4.1 MongoDB Server

**MongoDB Server** is the core database engine that handles all data storage, queries, and operations.

**Key Components:**

### **Process Structure:**
```
mongod (MongoDB Daemon)
├── Connection Handler
├── Query Engine
├── Storage Engine
├── Replication Module
├── Sharding Module
└── Journal & Logging
```

### **Port & Connection:**
- **Default Port**: 27017
- **Connection URI**: `mongodb://localhost:27017/mydb`
- Supports multiple concurrent connections
- Thread-based connection handling

### **Installation:**
```bash
# macOS (Homebrew)
brew tap mongodb/brew
brew install mongodb-community

# Linux (Ubuntu)
sudo apt-get install -y mongodb

# Windows
# Download from https://www.mongodb.com/try/download/community
```

### **Starting MongoDB:**
```bash
# Linux/macOS
mongod

# Windows
"C:\Program Files\MongoDB\Server\7.0\bin\mongod.exe"

# With specific config
mongod --config /path/to/mongod.conf
```

### **Configuration File (mongod.conf):**
```yaml
# Network
net:
  port: 27017
  bindIp: localhost

# Storage
storage:
  dbPath: /data/db
  engine: wiredTiger

# Security
security:
  authorization: enabled

# Replication
replication:
  replSetName: "rs0"
```

---

## 4.2 Storage Engine (WiredTiger)

**WiredTiger** is MongoDB's default storage engine since version 3.2, providing performance and reliability.

### **WiredTiger Characteristics:**

| Feature | Benefit |
|---------|---------|
| **Compression** | 10-20% space savings |
| **MVCC** | Readers don't block writers |
| **Document-Level Locking** | Fine-grained concurrency |
| **Caching** | In-memory data cache |
| **Journaling** | Crash recovery |

### **WiredTiger Architecture:**

```
┌─────────────────────────────────────┐
│    MongoDB Query Layer              │
│   (CRUD, Indexing, Aggregation)     │
└──────────────┬──────────────────────┘
               │
┌──────────────▼──────────────────────┐
│   WiredTiger Storage Engine         │
│  ┌─────────────────────────────────┐│
│  │ In-Memory Cache (Default: 50%)  ││
│  └─────────────────────────────────┘│
│  ┌─────────────────────────────────┐│
│  │ B-Tree Data Structure            ││
│  │ (Fast lookups, inserts, deletes) ││
│  └─────────────────────────────────┘│
│  ┌─────────────────────────────────┐│
│  │ Compression & Journaling        ││
│  └─────────────────────────────────┘│
└──────────────┬──────────────────────┘
               │
┌──────────────▼──────────────────────┐
│      File System / Disk Storage     │
│   (.wt files in dbPath directory)   │
└─────────────────────────────────────┘
```

### **Memory Management:**

```javascript
// WiredTiger default memory allocation:
cachedMemoryPercent = 50% of available RAM

Example on 16GB server:
- Available RAM: 16GB
- WiredTiger cache: 8GB
- Operating system cache: 8GB
```

### **Compression Algorithms:**

```javascript
// Available compression options:
1. snappy (default) - Fast, moderate compression
   Compression ratio: ~30-40%

2. zlib - High compression, slower
   Compression ratio: ~50-70%

3. zstd - Balanced
   Compression ratio: ~40-60%

4. none - No compression (fastest)
   Compression ratio: 0%
```

### **Document-Level Locking:**

```
Before WiredTiger (collection-level lock):
Thread A: INSERT → Locks entire collection
Thread B: UPDATE → WAITS for lock ⏳
Thread C: DELETE → WAITS for lock ⏳

After WiredTiger (document-level lock):
Thread A: INSERT document 1 → Locks doc 1
Thread B: UPDATE document 2 → Locks doc 2 ✓
Thread C: DELETE document 3 → Locks doc 3 ✓
Better concurrency!
```

---

## 4.3 How MongoDB Works

### **Complete Data Flow:**

```
┌────────────────────────────────────────────┐
│           Client Application               │
│  (JavaScript, Python, Java, etc)           │
└────────────────────┬───────────────────────┘
                     │ Sends query (JSON)
                     ▼
┌────────────────────────────────────────────┐
│      MongoDB Driver/Client Library         │
│  (mongodb npm, pymongo, etc)               │
└────────────────────┬───────────────────────┘
                     │ Converts to wire protocol
                     ▼
┌────────────────────────────────────────────┐
│           MongoDB Server (mongod)          │
│  1. Connection Handler (port 27017)        │
│  2. Parses request                         │
│  3. Query Planner (optimizes query)        │
│  4. Query Executor (runs plan)             │
└────────────────────┬───────────────────────┘
                     │
        ┌────────────┼────────────┐
        ▼            ▼            ▼
    ┌────────┐  ┌────────┐  ┌────────┐
    │ Index  │  │Storage │  │ Memory │
    │ Check  │  │ Engine │  │ Cache  │
    └────────┘  └────────┘  └────────┘
        │            │            │
        └────────────┼────────────┘
                     │
                     ▼
        ┌─────────────────────────┐
        │  Disk Storage           │
        │  (.wt data files)       │
        │  (.wt index files)      │
        │  (Journal/oplog)        │
        └─────────────────────────┘
```

### **Example Operation Flow (Insert):**

```javascript
// Client code
db.users.insertOne({ name: "John", age: 30 })

// 1. Driver converts to BSON
[Binary BSON data]

// 2. Sends via wire protocol to server
POST /admin/command → mongod

// 3. Server receives & validates
✓ Check authorization
✓ Check schema validation (if enabled)

// 4. WiredTiger storage engine
✓ Find insertion point in B-tree
✓ Allocate space
✓ Write to memory cache

// 5. Journal write
✓ Write to journal file (for crash recovery)

// 6. Acknowledgment sent back
{ ok: 1, insertedId: ObjectId(...) }

// 7. Optionally, write to disk
(Background thread periodically flushes cache)
```

---

## 4.4 Database → Collections → Documents

**Hierarchical Structure:**

```
MongoDB Server
│
├─── Database 1 (e.g., "myapp")
│    │
│    ├─── Collection: users
│    │    ├─── Document: { _id: 1, name: "John", ... }
│    │    ├─── Document: { _id: 2, name: "Jane", ... }
│    │    └─── Document: { _id: 3, name: "Bob", ... }
│    │
│    ├─── Collection: posts
│    │    ├─── Document: { _id: 1, title: "Post 1", ... }
│    │    └─── Document: { _id: 2, title: "Post 2", ... }
│    │
│    └─── Collection: comments
│         └─── Document: { _id: 1, text: "...", ... }
│
├─── Database 2 (e.g., "analytics")
│    └─── Collection: events
│         └─── Document: { _id: 1, type: "click", ... }
│
└─── Database 3 (e.g., "admin")
     └─── System collections
```

### **Terminology:**

| Term | SQL Equivalent | Description |
|------|---|---|
| **Database** | Database | Container for collections |
| **Collection** | Table | Container for documents |
| **Document** | Row | Individual data unit |
| **Field** | Column | Property in document |
| **Index** | Index | Fast lookup structure |

### **Example:**

```javascript
// Access/create database
use myapp

// Create/access collection
db.users

// Insert document
db.users.insertOne({
  name: "John",
  email: "john@example.com",
  age: 30
})

// Query collection
db.users.find({ age: { $gt: 25 } })
```

### **Database Limits:**

| Item | Limit |
|------|-------|
| Collections per database | ~64,000 |
| Document size | 16 MB |
| Nested depth | Limited by doc size |
| Field name length | Practical limit ~1KB |

---

## 4.5 Full Stack Architecture (Frontend + Backend + Database)

### **Complete Web Application Stack:**

```
┌─────────────────────────────────────────────────────┐
│                  FRONTEND LAYER                     │
│        (Browser - React, Vue, Angular, etc)        │
│  - User Interface                                   │
│  - Form handling                                    │
│  - Real-time updates (WebSockets)                   │
└───────────────────────┬─────────────────────────────┘
                        │ HTTP/HTTPS
                        │ REST API or GraphQL
                        ▼
┌─────────────────────────────────────────────────────┐
│               BACKEND/API LAYER                     │
│        (Node.js, Python, Java, Go, etc)             │
│  - Express.js/FastAPI/Spring server                 │
│  - Authentication & Authorization                   │
│  - Business logic                                   │
│  - Validation                                       │
│  - Error handling                                   │
└───────────────────────┬─────────────────────────────┘
                        │ MongoDB driver
                        │ (mongodb npm, pymongo, etc)
                        ▼
┌─────────────────────────────────────────────────────┐
│             DATABASE LAYER                          │
│                 (MongoDB)                           │
│  - mongod server (port 27017)                       │
│  - Collections & documents                          │
│  - Indexes                                          │
│  - Replication (optional)                           │
│  - Sharding (optional)                              │
└───────────────────────┬─────────────────────────────┘
                        │
                        ▼
            ┌───────────────────────┐
            │   Disk Storage        │
            │  (Data + Indexes)     │
            └───────────────────────┘
```

### **Node.js + Express + MongoDB Example:**

```javascript
// server.js
const express = require('express');
const { MongoClient } = require('mongodb');
const app = express();

app.use(express.json());

const mongoUri = 'mongodb://localhost:27017';
const client = new MongoClient(mongoUri);

app.get('/api/users', async (req, res) => {
  try {
    await client.connect();
    const db = client.db('myapp');
    const users = await db.collection('users').find({}).toArray();
    res.json(users);
  } catch (err) {
    res.status(500).json({ error: err.message });
  } finally {
    await client.close();
  }
});

app.post('/api/users', async (req, res) => {
  try {
    await client.connect();
    const db = client.db('myapp');
    const result = await db.collection('users').insertOne(req.body);
    res.json({ insertedId: result.insertedId });
  } catch (err) {
    res.status(500).json({ error: err.message });
  } finally {
    await client.close();
  }
});

app.listen(5000, () => console.log('Server running on port 5000'));
```

### **Data Flow Example: Creating a User**

```
1. User fills form in React (frontend)
   Name: "John", Email: "john@ex.com"

2. React sends HTTP POST request
   POST /api/users
   Body: { name: "John", email: "john@ex.com" }

3. Express backend receives request
   ✓ Validates input
   ✓ Sanitizes data
   ✓ Checks authorization

4. MongoDB driver converts to BSON
   [Binary BSON encoding]

5. Sends to MongoDB server
   INSERT command

6. WiredTiger storage engine
   ✓ Writes to memory cache
   ✓ Writes to journal
   ✓ Assigns ObjectId

7. MongoDB returns acknowledgment
   { insertedId: ObjectId(...), ok: 1 }

8. Express sends response to React
   { success: true, id: "..." }

9. React updates UI
   Shows success message, refreshes list
```

### **Replication & Sharding (Advanced):**

```
REPLICATION (High Availability):
┌──────────────────────────────────────────┐
│  Replica Set (3+ nodes)                  │
├──────────────────────────────────────────┤
│  Primary Node          (Read + Write)    │
│  Secondary Node 1      (Read only)       │
│  Secondary Node 2      (Read only)       │
│  Arbiter (optional)    (Voting only)     │
└──────────────────────────────────────────┘

SHARDING (Horizontal Scaling):
┌──────────────────────────────────────────┐
│  MongoDB Cluster                         │
├──────────────────────────────────────────┤
│  Shard 1 (Data: A-M) ← Collections split │
│  Shard 2 (Data: N-Z) ← across shards    │
│  Shard 3 (Config)    ← Routing info     │
└──────────────────────────────────────────┘
```

---

## GitHub Resources

- **MongoDB Server**: [mongodb/mongo](https://github.com/mongodb/mongo)
- **MongoDB Architecture Docs**: [mongodb/docs-architecture](https://github.com/mongodb/docs-ecosystem)
- **WiredTiger**: [wiredtiger/wiredtiger](https://github.com/wiredtiger/wiredtiger)
- **MongoDB Docker**: [mongodb/mongo-docker](https://github.com/docker-library/mongo)

---

Navigation: [← Chapter 3: JSON vs BSON](../ch03-json-vs-bson/README.md) | [↑ Index](../../index.md) | [→ Chapter 5: MongoDB Installation & Setup](../ch05-mongodb-installation-setup/README.md)
