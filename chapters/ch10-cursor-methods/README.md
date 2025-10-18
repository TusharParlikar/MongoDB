# Chapter 10: Cursor Methods

A cursor is an object that iterates through query results. Cursor methods control how results are retrieved.

## 10.1 Understanding Cursors

### **What is a Cursor?**

A cursor is a pointer to the result set of a query. It doesn't fetch all documents immediately; instead, it retrieves them in batches for efficiency.

### **Creating a Cursor**

```javascript
// Create cursor (doesn't execute immediately)
const cursor = db.users.find({ age: { $gt: 25 } })

// Iterate through cursor
cursor.forEach(user => {
  console.log(user.name)
})

// Or convert to array
const users = cursor.toArray()
```

### **Cursor Lifecycle**

```
1. Query issued
   ↓
2. Cursor created (no fetch yet)
   ↓
3. First batch fetched (default: 101 documents)
   ↓
4. Iterate/process results
   ↓
5. When need more, fetch next batch
   ↓
6. Continue until all documents processed
```

---

## 10.2 Batching in MongoDB

MongoDB fetches results in batches for performance and memory efficiency.

### **Default Batch Size**

```javascript
// mongosh fetches 20 documents initially
db.collection.find()

// If you request more, fetches next batch of 101 documents
// Batch size: 20 (initial display), then 101 (subsequent)
```

### **Custom Batch Size**

```javascript
// Set batch size in find
db.users.find().batchSize(50)

// Or in Node.js
const users = await db.collection('users')
  .find({})
  .batchSize(100)
  .toArray()

// Get remaining from cursor
const batch1 = cursor.toArray()  // Gets one batch
const remaining = cursor.batchSize(0).toArray()  // Gets rest
```

### **Batch Size Behavior**

| batchSize | Behavior |
|-----------|----------|
| 0 | Default (20 initial, then 101) |
| 1 | Fetch 1 document at a time |
| 100 | Fetch 100 at a time |
| -1 | Close cursor after one batch |

---

## 10.3 count() Method

**count()** returns the number of documents matching a query.

### **Syntax**

```javascript
db.collection.find(query).count()
db.collection.countDocuments(query)
```

### **Examples**

```javascript
// Count all documents
db.users.find().count()
db.users.countDocuments()

// Count with filter
db.users.find({ age: { $gt: 25 } }).count()
db.users.countDocuments({ age: { $gt: 25 } })

// Count specific value
db.employees.countDocuments({ department: "Engineering" })
```

### **count() vs countDocuments()**

| Method | Speed | Notes |
|--------|-------|-------|
| `.count()` | Faster | Estimates collection size (approximate) |
| `.countDocuments()` | Slower | Exact count, ignores hints |

```javascript
// Use .count() for estimates
db.users.find().count()  // ~1250 (estimate)

// Use .countDocuments() for accurate counts
db.users.countDocuments()  // 1247 (exact)
```

### **Node.js Example**

```javascript
const count = await db.collection('users').countDocuments({ age: { $gt: 25 } });
console.log(`Found ${count} users over 25`);
```

---

## 10.4 limit() Method

**limit()** restricts the number of documents returned.

### **Syntax**

```javascript
db.collection.find(query).limit(n)
```

### **Examples**

```javascript
// Get first 10 documents
db.users.find().limit(10)

// Get 5 products ordered by price
db.products.find().sort({ price: 1 }).limit(5)

// Pagination: first page (10 per page)
db.users.find().skip(0).limit(10)

// Pagination: second page
db.users.find().skip(10).limit(10)
```

### **Practical Uses**

```javascript
// Get top 5 employees by salary
db.employees.find().sort({ salary: -1 }).limit(5)

// Get latest 20 posts
db.posts.find().sort({ createdAt: -1 }).limit(20)

// Get first user (similar to findOne)
db.users.find().limit(1)
```

### **Node.js Example**

```javascript
const topProducts = await db.collection('products')
  .find({})
  .sort({ rating: -1 })
  .limit(10)
  .toArray();
console.log('Top 10 rated products:', topProducts);
```

---

## 10.5 skip() Method

**skip()** skips a specified number of documents, useful for pagination.

### **Syntax**

```javascript
db.collection.find(query).skip(n)
```

### **Examples**

```javascript
// Skip first 10, get next 10 (pagination: page 2)
db.users.find().skip(10).limit(10)

// Skip first 100, get 50
db.products.find().skip(100).limit(50)

// Skip all but last 5
db.posts.find().skip(db.posts.countDocuments() - 5)
```

### **Pagination Pattern**

```javascript
// Page 1: documents 1-10
db.users.find().skip(0).limit(10)

// Page 2: documents 11-20
db.users.find().skip(10).limit(10)

// Page N: documents ((N-1)*pageSize) to (N*pageSize)
const pageNum = 3
const pageSize = 10
db.users.find().skip((pageNum - 1) * pageSize).limit(pageSize)
```

### **Node.js Pagination Example**

```javascript
async function getPaginatedUsers(page = 1, pageSize = 10) {
  const client = new MongoClient('mongodb://localhost:27017');
  
  try {
    await client.connect();
    const db = client.db('myapp');
    
    const skip = (page - 1) * pageSize;
    const users = await db.collection('users')
      .find({})
      .skip(skip)
      .limit(pageSize)
      .toArray();
    
    const total = await db.collection('users').countDocuments();
    
    return {
      users,
      page,
      pageSize,
      total,
      pages: Math.ceil(total / pageSize)
    };
  } finally {
    await client.close();
  }
}

getPaginatedUsers(2, 20).then(result => console.log(result));
```

---

## 10.6 sort() Method (Ascending/Descending)

**sort()** orders documents by specified fields.

### **Syntax**

```javascript
db.collection.find().sort({ field: 1 })  // 1 = ascending
db.collection.find().sort({ field: -1 }) // -1 = descending
```

### **Single Field Sort**

```javascript
// Sort by age ascending (youngest first)
db.users.find().sort({ age: 1 })

// Sort by age descending (oldest first)
db.users.find().sort({ age: -1 })

// Sort by name A-Z
db.users.find().sort({ name: 1 })

// Sort by name Z-A
db.users.find().sort({ name: -1 })
```

### **Multiple Field Sort**

```javascript
// Sort by department (ascending), then by salary (descending)
db.employees.find().sort({ department: 1, salary: -1 })

// Sort by status (active first), then by age (youngest first)
db.users.find().sort({ active: -1, age: 1 })
```

### **Sorting with Other Cursor Methods**

```javascript
// Sort, skip, limit (recommended order)
db.products.find()
  .sort({ price: -1 })    // Most expensive first
  .skip(10)               // Skip first 10
  .limit(20)              // Get next 20

// Get top 10 by rating
db.products.find()
  .sort({ rating: -1 })
  .limit(10)

// Pagination with sort
db.users.find()
  .sort({ createdAt: -1 })  // Newest first
  .skip(20)
  .limit(10)
```

### **Sorting Edge Cases**

```javascript
// Sort by nested field
db.users.find().sort({ "address.city": 1 })

// Missing fields sorted to end (for ascending) or start (for descending)
db.users.find().sort({ middle_name: 1 })  // Documents without middle_name at end

// Date sorting
db.posts.find().sort({ publishedAt: -1 })  // Most recent first

// Numeric strings sort as strings, not numbers
// "10" < "2" when sorting strings
```

### **Node.js Example**

```javascript
async function getLatestPosts(limit = 10) {
  const client = new MongoClient('mongodb://localhost:27017');
  
  try {
    await client.connect();
    const db = client.db('blog');
    
    const posts = await db.collection('posts')
      .find({})
      .sort({ createdAt: -1 })  // Newest first
      .limit(limit)
      .toArray();
    
    return posts;
  } finally {
    await client.close();
  }
}

getLatestPosts(5).then(posts => console.log(posts));
```

---

## Cursor Methods Chaining

Methods can be chained together for complex queries:

```javascript
// Combine multiple methods
db.employees.find({ department: "Engineering" })
  .sort({ salary: -1 })
  .skip(5)
  .limit(10)
  .toArray()

// Breaking it down:
// 1. Find: Filter to Engineering department
// 2. Sort: Order by salary (highest first)
// 3. Skip: Skip first 5
// 4. Limit: Get next 10
// Result: Employees 6-15 by salary in Engineering
```

---

## GitHub Resources

- **MongoDB Cursor**: [mongodb/docs-cursor](https://github.com/mongodb/docs)
- **Cursor Methods**: [mongodb/cursor-reference](https://github.com/mongodb/mongo-manual)

---

Navigation: [← Chapter 9: Comparison Operators](../ch09-comparison-operators/README.md) | [↑ Index](../../index.md) | [→ Chapter 11: Logical Operators](../ch11-logical-operators/README.md)
