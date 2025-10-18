# Chapter 21: Aggregation Operators

## 21.1 Arithmetic Operators
`$add`, `$subtract`, `$multiply`, `$divide`, `$mod`

```javascript
db.products.aggregate([
  {
    $project: {
      name: 1,
      price: 1,
      discounted: { $multiply: ["$price", 0.9] },
      taxed: { $add: ["$price", { $multiply: ["$price", 0.1] }] }
    }
  }
])
```

---

## 21.2 String Operators
`$concat`, `$substr`, `$toLower`, `$toUpper`, `$split`

```javascript
db.users.aggregate([
  {
    $project: {
      fullName: { $concat: ["$firstName", " ", "$lastName"] },
      email: { $toLower: "$emailAddress" }
    }
  }
])
```

---

## 21.3 Array Operators
`$push`, `$addToSet`, `$first`, `$last`, `$size`, `$filter`, `$map`

```javascript
db.orders.aggregate([
  {
    $group: {
      _id: "$customerId",
      items: { $push: "$product" },
      uniqueItems: { $addToSet: "$product" },
      itemCount: { $sum: 1 }
    }
  }
])
```

---

## 21.4 Conditional Operators
`$cond`, `$ifNull`, `$switch`, `$case`

```javascript
db.employees.aggregate([
  {
    $project: {
      name: 1,
      salary: 1,
      bonus: {
        $cond: [
          { $gte: ["$salary", 100000] },
          { $multiply: ["$salary", 0.1] },
          { $multiply: ["$salary", 0.05] }
        ]
      }
    }
  }
])
```

---

## 21.5 Date Operators
`$year`, `$month`, `$dayOfMonth`, `$hour`, `$minute`, `$dateToString`

```javascript
db.posts.aggregate([
  {
    $project: {
      title: 1,
      year: { $year: "$createdAt" },
      month: { $month: "$createdAt" },
      formattedDate: { $dateToString: { format: "%Y-%m-%d", date: "$createdAt" } }
    }
  }
])
```

---

Navigation: [← Chapter 20: Aggregation Pipeline Stages](../ch20-aggregation-pipeline-stages/README.md) | [↑ Index](../../index.md) | [→ Chapter 22: Aggregation Expressions](../ch22-aggregation-expressions/README.md)

