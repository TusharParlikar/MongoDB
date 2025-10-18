# Chapter 19: Aggregation Framework - Basics

## 19.1 Introduction to Aggregation

The aggregation framework processes data through a pipeline of stages, transforming and analyzing data.

### **vs find()**

```javascript
// find(): Simple queries
db.users.find({ age: { $gt: 30 } })

// Aggregation: Complex transformations
db.users.aggregate([
  { $match: { age: { $gt: 30 } } },
  { $group: { _id: "$department", count: { $sum: 1 } } },
  { $sort: { count: -1 } }
])
```

---

## 19.2 Aggregation Pipeline Concept

A pipeline passes documents through stages, each transforming the data.

```
[Initial Documents] → [$match] → [$project] → [$group] → [$sort] → [Results]
```

---

## 19.3 Basic Aggregation Operations

### **$match Stage**

```javascript
// Filter documents (like find)
db.users.aggregate([
  { $match: { status: "active" } }
])
```

### **$group Stage**

```javascript
// Group and aggregate
db.users.aggregate([
  {
    $group: {
      _id: "$department",
      count: { $sum: 1 },
      avgSalary: { $avg: "$salary" }
    }
  }
])
```

### **$project Stage**

```javascript
// Select/transform fields
db.users.aggregate([
  {
    $project: {
      name: 1,
      department: 1,
      salaryRange: { $cond: [{ $gte: ["$salary", 100000] }, "high", "low"] }
    }
  }
])
```

---

## 19.4 Stages in Pipeline

### **Common Stages**

| Stage | Purpose |
|-------|---------|
| `$match` | Filter documents |
| `$project` | Select/transform fields |
| `$group` | Group and aggregate |
| `$sort` | Order documents |
| `$limit` | Limit results |
| `$skip` | Skip documents |
| `$lookup` | Join collections |
| `$unwind` | Flatten arrays |
| `$facet` | Multiple pipelines |

### **Node.js Example**

```javascript
const { MongoClient } = require('mongodb');

async function aggregationExample() {
  const client = new MongoClient('mongodb://localhost:27017');
  
  try {
    await client.connect();
    const db = client.db('myapp');
    
    const result = await db.collection('orders').aggregate([
      { $match: { status: "completed" } },
      { $group: { 
          _id: "$customerId", 
          totalSpent: { $sum: "$amount" },
          count: { $sum: 1 }
        }
      },
      { $sort: { totalSpent: -1 } },
      { $limit: 10 }
    ]).toArray();
    
    console.log('Top customers:', result);
  } finally {
    await client.close();
  }
}

aggregationExample().catch(console.error);
```

---

## GitHub Resources

- **MongoDB Aggregation**: [mongodb/docs-aggregation](https://github.com/mongodb/docs)

---

Navigation: [← Chapter 18: Indexing](../ch18-indexing/README.md) | [↑ Index](../../index.md) | [→ Chapter 20: Aggregation Pipeline Stages](../ch20-aggregation-pipeline-stages/README.md)
