# Chapter 9: Comparison Operators

Comparison operators compare field values with specified values in queries.

## 9.1 $eq (Equal To)

Matches documents where a field equals the specified value.

### **Syntax**

```javascript
{ field: { $eq: value } }
// or simply: { field: value }
```

### **Examples**

```javascript
use shop

db.products.insertMany([
  { name: "Laptop", price: 1200, status: "active" },
  { name: "Mouse", price: 25, status: "inactive" },
  { name: "Keyboard", price: 80, status: "active" }
])

// Find products with price = 25
db.products.find({ price: { $eq: 25 } })
db.products.find({ price: 25 })  // Same as above

// Find inactive products
db.products.find({ status: { $eq: "inactive" } })
db.products.find({ status: "inactive" })  // Same as above
```

---

## 9.2 $ne (Not Equal To)

Matches documents where a field does NOT equal the specified value.

### **Syntax**

```javascript
{ field: { $ne: value } }
```

### **Examples**

```javascript
// Find products that are NOT $25
db.products.find({ price: { $ne: 25 } })
// Returns: Laptop (1200), Keyboard (80)

// Find products that are NOT active
db.products.find({ status: { $ne: "active" } })
// Returns: Mouse (inactive)

// Field doesn't exist OR not equal
db.products.find({ category: { $ne: "Electronics" } })
// Includes documents without 'category' field
```

---

## 9.3 $gt (Greater Than)

Matches documents where a field is greater than the specified value.

### **Syntax**

```javascript
{ field: { $gt: value } }
```

### **Examples**

```javascript
// Find products over $100
db.products.find({ price: { $gt: 100 } })
// Returns: Laptop (1200)

// Find products with price > 80
db.products.find({ price: { $gt: 80 } })
// Returns: Laptop (1200), Mouse (25) - NO, Mouse is 25, not > 80
// Returns: Laptop (1200)

// Date comparison
db.posts.find({ createdAt: { $gt: ISODate("2023-01-01") } })
```

---

## 9.4 $gte (Greater Than or Equal)

Matches documents where a field is greater than or equal to the specified value.

### **Syntax**

```javascript
{ field: { $gte: value } }
```

### **Examples**

```javascript
// Find products >= $80
db.products.find({ price: { $gte: 80 } })
// Returns: Laptop (1200), Keyboard (80)

// Numeric comparison
db.employees.find({ salary: { $gte: 100000 } })

// Age >= 18
db.users.find({ age: { $gte: 18 } })
```

---

## 9.5 $lt (Less Than)

Matches documents where a field is less than the specified value.

### **Syntax**

```javascript
{ field: { $lt: value } }
```

### **Examples**

```javascript
// Find products under $100
db.products.find({ price: { $lt: 100 } })
// Returns: Mouse (25), Keyboard (80)

// Age less than 30
db.users.find({ age: { $lt: 30 } })

// Earlier than date
db.posts.find({ createdAt: { $lt: ISODate("2023-01-01") } })
```

---

## 9.6 $lte (Less Than or Equal)

Matches documents where a field is less than or equal to the specified value.

### **Syntax**

```javascript
{ field: { $lte: value } }
```

### **Examples**

```javascript
// Find products <= $80
db.products.find({ price: { $lte: 80 } })
// Returns: Mouse (25), Keyboard (80)

// Maximum age 65
db.employees.find({ age: { $lte: 65 } })
```

---

## 9.7 $in Operator

Matches documents where a field value is in an array of specified values.

### **Syntax**

```javascript
{ field: { $in: [ value1, value2, ... ] } }
```

### **Examples**

```javascript
// Find products that are either "Laptop" or "Keyboard"
db.products.find({ name: { $in: ["Laptop", "Keyboard"] } })

// Find employees in specific departments
db.employees.find({ department: { $in: ["Engineering", "Sales"] } })

// Price is exactly 25, 80, or 1200
db.products.find({ price: { $in: [25, 80, 1200] } })

// Multiple conditions with $in
db.users.find({
  status: "active",
  role: { $in: ["admin", "moderator"] }
})
```

### **Node.js Example**

```javascript
const { MongoClient } = require('mongodb');

async function findProductsByNames(names) {
  const client = new MongoClient('mongodb://localhost:27017');
  
  try {
    await client.connect();
    const db = client.db('shop');
    
    const products = await db.collection('products')
      .find({ name: { $in: names } })
      .toArray();
    
    console.log('Found products:', products);
  } finally {
    await client.close();
  }
}

findProductsByNames(["Laptop", "Mouse"]).catch(console.error);
```

---

## 9.8 $nin (Not In) Operator

Matches documents where a field value is NOT in an array of specified values.

### **Syntax**

```javascript
{ field: { $nin: [ value1, value2, ... ] } }
```

### **Examples**

```javascript
// Find products that are NOT "Mouse" or "Keyboard"
db.products.find({ name: { $nin: ["Mouse", "Keyboard"] } })
// Returns: Laptop

// Employees NOT in these departments
db.employees.find({ department: { $nin: ["Sales", "Marketing"] } })

// Status is not pending or rejected
db.orders.find({ status: { $nin: ["pending", "rejected"] } })

// $nin also matches documents without the field
db.users.find({ country: { $nin: ["US", "CA"] } })
// Includes documents without 'country' field
```

---

## Range Queries (Combining Operators)

Combine multiple comparison operators for range queries.

### **Between Two Values**

```javascript
// Price between $100 and $1000
db.products.find({
  price: {
    $gte: 100,
    $lte: 1000
  }
})

// Age between 25 and 65
db.employees.find({
  age: {
    $gte: 25,
    $lte: 65
  }
})

// Date range
db.posts.find({
  createdAt: {
    $gte: ISODate("2023-01-01"),
    $lt: ISODate("2023-12-31")
  }
})
```

---

## Comparison Operators Summary Table

| Operator | Meaning | Example |
|----------|---------|---------|
| `$eq` | Equal | `{ price: { $eq: 100 } }` |
| `$ne` | Not equal | `{ price: { $ne: 100 } }` |
| `$gt` | Greater than | `{ price: { $gt: 100 } }` |
| `$gte` | Greater or equal | `{ price: { $gte: 100 } }` |
| `$lt` | Less than | `{ price: { $lt: 100 } }` |
| `$lte` | Less or equal | `{ price: { $lte: 100 } }` |
| `$in` | In array | `{ price: { $in: [25, 50, 100] } }` |
| `$nin` | Not in array | `{ price: { $nin: [25, 50] } }` |

---

## GitHub Resources

- **MongoDB Comparison Operators**: [mongodb/docs-operators-comparison](https://github.com/mongodb/docs)
- **Query Examples**: [mongodb/mongo-examples](https://github.com/mongodb/mongo-examples)

---

Navigation: [← Chapter 8: CRUD Operations - Read](../ch08-crud-operations-read/README.md) | [↑ Index](../../index.md) | [→ Chapter 10: Cursor Methods](../ch10-cursor-methods/README.md)
