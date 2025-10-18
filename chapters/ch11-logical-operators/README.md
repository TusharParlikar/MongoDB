# Chapter 11: Logical Operators

Logical operators combine multiple queries or conditions.

## 11.1 $and Operator

Matches documents satisfying ALL conditions.

### **Syntax**

```javascript
{ $and: [ { condition1 }, { condition2 }, ... ] }
```

### **Implicit AND (Default)**

```javascript
// These are equivalent:
db.users.find({ age: 30, status: "active" })
db.users.find({ $and: [ { age: 30 }, { status: "active" } ] })

// Both return users with age 30 AND status active
```

### **Explicit AND (Complex Conditions)**

```javascript
// Multiple conditions on same field
db.products.find({
  $and: [
    { price: { $gte: 100 } },
    { price: { $lte: 500 } }
  ]
})
// Price between 100 and 500

// Mix of conditions
db.employees.find({
  $and: [
    { department: "Engineering" },
    { salary: { $gt: 100000 } },
    { active: true }
  ]
})
```

---

## 11.2 $or Operator

Matches documents satisfying ANY condition.

### **Syntax**

```javascript
{ $or: [ { condition1 }, { condition2 }, ... ] }
```

### **Examples**

```javascript
// Find users age 25 OR age 30
db.users.find({
  $or: [
    { age: 25 },
    { age: 30 }
  ]
})

// Find inactive users OR users with no status
db.users.find({
  $or: [
    { status: "inactive" },
    { status: { $exists: false } }
  ]
})

// Department Sales OR Marketing
db.employees.find({
  $or: [
    { department: "Sales" },
    { department: "Marketing" }
  ]
})
```

---

## 11.3 $not Operator

Matches documents where condition is FALSE.

### **Syntax**

```javascript
{ field: { $not: { operator: value } } }
```

### **Examples**

```javascript
// NOT greater than 30 (≤ 30)
db.users.find({ age: { $not: { $gt: 30 } } })

// NOT in array
db.users.find({ status: { $not: { $in: ["pending", "rejected"] } } })

// NOT exists
db.users.find({ bio: { $not: { $exists: true } } })
```

---

## 11.4 $nor Operator

Matches documents where NONE of the conditions are true.

### **Syntax**

```javascript
{ $nor: [ { condition1 }, { condition2 }, ... ] }
```

### **Examples**

```javascript
// NOT (status = "active" OR status = "pending")
db.orders.find({
  $nor: [
    { status: "active" },
    { status: "pending" }
  ]
})
// Returns: Orders with status "completed", "cancelled", etc.

// Neither young nor high salary
db.employees.find({
  $nor: [
    { age: { $lt: 25 } },
    { salary: { $gt: 200000 } }
  ]
})
```

---

## 11.5 Combining Logical Operators

Complex queries often mix multiple logical operators.

### **AND + OR**

```javascript
// (age 25 OR age 30) AND status active
db.users.find({
  $and: [
    { $or: [ { age: 25 }, { age: 30 } ] },
    { status: "active" }
  ]
})

// More readably:
db.users.find({
  status: "active",  // AND (implicit)
  $or: [
    { age: 25 },
    { age: 30 }
  ]
})
```

### **Complex Nested Logic**

```javascript
// (dept = Sales OR dept = Marketing) AND (salary >= 80000 OR senior = true)
db.employees.find({
  $and: [
    {
      $or: [
        { department: "Sales" },
        { department: "Marketing" }
      ]
    },
    {
      $or: [
        { salary: { $gte: 80000 } },
        { senior: true }
      ]
    }
  ]
})
```

### **Node.js Example**

```javascript
async function searchUsers(filters) {
  const client = new MongoClient('mongodb://localhost:27017');
  
  try {
    await client.connect();
    const db = client.db('myapp');
    
    // Build query: (city = SF OR city = NY) AND status = active
    const query = {
      status: "active",
      $or: [
        { city: "San Francisco" },
        { city: "New York" }
      ]
    };
    
    const users = await db.collection('users').find(query).toArray();
    return users;
  } finally {
    await client.close();
  }
}
```

---

## Logical Operators Summary

| Operator | Behavior |
|----------|----------|
| `$and` (implicit) | ALL conditions true |
| `$or` | ANY condition true |
| `$not` | Negates condition |
| `$nor` | NONE conditions true |

---

## GitHub Resources

- **MongoDB Logical Operators**: [mongodb/docs-operators-logical](https://github.com/mongodb/docs)

---

Navigation: [← Chapter 10: Cursor Methods](../ch10-cursor-methods/README.md) | [↑ Index](../../index.md) | [→ Chapter 12: Element Operators](../ch12-element-operators/README.md)
