# Chapter 20: Aggregation Pipeline Stages

## 20.1 $match Stage
Filters documents matching criteria (like `find()`).

```javascript
db.users.aggregate([
  { $match: { age: { $gt: 25 } } }
])
```

---

## 20.2 $project Stage
Selects/reshapes fields in documents.

```javascript
db.users.aggregate([
  {
    $project: {
      name: 1,
      age: 1,
      isAdult: { $gte: ["$age", 18] }
    }
  }
])
```

---

## 20.3 $group Stage
Groups documents and performs aggregations.

```javascript
db.users.aggregate([
  {
    $group: {
      _id: "$department",
      count: { $sum: 1 },
      avgAge: { $avg: "$age" },
      maxSalary: { $max: "$salary" }
    }
  }
])
```

**Accumulators:** `$sum`, `$avg`, `$min`, `$max`, `$first`, `$last`, `$push`, `$addToSet`

---

## 20.4 $sort Stage
Orders documents (1 = ascending, -1 = descending).

```javascript
db.users.aggregate([
  { $sort: { salary: -1 } }  // Highest salary first
])
```

---

## 20.5 $limit Stage
Limits result count.

```javascript
db.users.aggregate([
  { $match: { active: true } },
  { $limit: 10 }
])
```

---

## 20.6 $skip Stage
Skips documents (pagination).

```javascript
db.users.aggregate([
  { $skip: 20 },
  { $limit: 10 }
])
```

---

## 20.7 $unwind Stage
Flattens arrays to multiple documents.

```javascript
// Input: { name: "John", skills: ["JS", "Python"] }
db.users.aggregate([
  { $unwind: "$skills" }
])
// Output:
// { name: "John", skills: "JS" }
// { name: "John", skills: "Python" }
```

---

## 20.8 $lookup Stage
Joins collections (like SQL JOIN).

```javascript
db.posts.aggregate([
  {
    $lookup: {
      from: "users",
      localField: "userId",
      foreignField: "_id",
      as: "author"
    }
  }
])
```

---

## 20.9 $filter Stage
Filters array elements.

```javascript
db.users.aggregate([
  {
    $project: {
      name: 1,
      adultSkills: {
        $filter: {
          input: "$skills",
          as: "skill",
          cond: { $gte: ["$$skill.level", 3] }
        }
      }
    }
  }
])
```

---

## Node.js Example

```javascript
async function complexPipeline() {
  const result = await db.collection('orders').aggregate([
    { $match: { status: "completed" } },
    { $lookup: { from: "users", localField: "userId", foreignField: "_id", as: "customer" } },
    { $unwind: "$items" },
    { $group: {
        _id: "$items.productId",
        totalSold: { $sum: "$items.quantity" },
        revenue: { $sum: "$items.price" }
      }
    },
    { $sort: { revenue: -1 } },
    { $limit: 10 }
  ]).toArray();
  return result;
}
```

---

## GitHub Resources

- **Aggregation Stages**: [mongodb/aggregation-stages](https://github.com/mongodb/docs)

---

Navigation: [← Chapter 19: Aggregation Framework - Basics](../ch19-aggregation-framework-basics/README.md) | [↑ Index](../../index.md) | [→ Chapter 21: Aggregation Operators](../ch21-aggregation-operators/README.md)
