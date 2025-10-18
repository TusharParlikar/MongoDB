# Chapter 22: Aggregation Expressions

## 22.1 Using Expressions in $group

```javascript
db.sales.aggregate([
  {
    $group: {
      _id: "$region",
      totalRevenue: { $sum: "$amount" },
      avgSale: { $avg: "$amount" },
      minSale: { $min: "$amount" },
      maxSale: { $max: "$amount" },
      count: { $sum: 1 }
    }
  }
])
```

---

## 22.2 Calculated Fields

Create new computed fields in documents.

```javascript
db.products.aggregate([
  {
    $project: {
      name: 1,
      price: 1,
      quantity: 1,
      totalValue: { $multiply: ["$price", "$quantity"] },
      profit: { $subtract: [{ $multiply: ["$price", "$quantity"] }, "$cost"] }
    }
  }
])
```

---

## 22.3 Aggregation Variables

Use variables in aggregation expressions.

```javascript
db.students.aggregate([
  {
    $project: {
      name: 1,
      scores: 1,
      average: {
        $avg: {
          $map: {
            input: "$scores",
            as: "score",
            in: "$$score"
          }
        }
      }
    }
  }
])
```

---

Navigation: [← Chapter 21: Aggregation Operators](../ch21-aggregation-operators/README.md) | [↑ Index](../../index.md) | [→ Chapter 23: Import/Export Data](../ch23-import-export-data/README.md)

