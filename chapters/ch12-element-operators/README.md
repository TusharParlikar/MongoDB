# Chapter 12: Element Operators

Element operators check document field properties.

## 12.1 $exists Operator

Matches documents that have a specified field (regardless of value).

### **Syntax**

```javascript
{ field: { $exists: true } }   // Field exists
{ field: { $exists: false } }  // Field missing
```

### **Examples**

```javascript
// Find users WITH a phone field
db.users.find({ phone: { $exists: true } })

// Find users WITHOUT a phone field
db.users.find({ phone: { $exists: false } })

// Find posts WITH a bio field
db.profiles.find({ bio: { $exists: true } })

// Find products WITHOUT discount field
db.products.find({ discount: { $exists: false } })
```

### **With null Values**

```javascript
// Documents where field exists (includes null)
db.users.find({ phone: { $exists: true } })
// Matches: phone: "555-1234" AND phone: null

// Documents where field is missing
db.users.find({ phone: { $exists: false } })
// Matches: { name: "John" } (no phone field)

// Combination: missing OR null
db.users.find({
  $or: [
    { phone: { $exists: false } },
    { phone: null }
  ]
})
```

---

## 12.2 $type Operator

Matches documents where a field is of a specific type.

### **Syntax**

```javascript
{ field: { $type: "typename" } }
// or numeric: { field: { $type: 2 } }
```

### **BSON Type Names**

| Type | Name | Number |
|------|------|--------|
| String | "string" | 2 |
| Int32 | "int" | 16 |
| Int64 | "long" | 18 |
| Double | "double" | 1 |
| Boolean | "bool" | 8 |
| Array | "array" | 4 |
| Object | "object" | 3 |
| Date | "date" | 9 |
| Null | "null" | 10 |
| ObjectId | "objectId" | 7 |
| Binary | "binData" | 5 |

### **Examples**

```javascript
// Find users where age is integer
db.users.find({ age: { $type: "int" } })

// Find fields containing strings
db.users.find({ name: { $type: "string" } })

// Find posts with date objects
db.posts.find({ createdAt: { $type: "date" } })

// Find users with array skills
db.users.find({ skills: { $type: "array" } })

// Find users where email is NOT a string
db.users.find({ email: { $type: { $ne: "string" } } })
```

### **Practical Use Cases**

```javascript
// Fix inconsistent data types
// Find documents where price is string instead of number
db.products.find({ price: { $type: "string" } })

// Find documents missing type conversion
db.orders.find({ quantity: { $type: "string" } })

// Multiple types
db.users.find({
  $or: [
    { age: { $type: "int" } },
    { age: { $type: "double" } }
  ]
})
```

---

## 12.3 Checking Field Existence

Common patterns for checking field existence and values.

### **Field Exists and is Not Null**

```javascript
// Combined check
db.users.find({
  phone: {
    $exists: true,
    $ne: null
  ]
})

// Or simpler
db.users.find({ phone: { $exists: true, $ne: null } })
```

### **Field Exists But is Null**

```javascript
db.users.find({
  phone: {
    $exists: true,
  },
  phone: null
})

// Or using $and
db.users.find({
  $and: [
    { phone: { $exists: true } },
    { phone: null }
  ]
})
```

### **Field Missing or Null**

```javascript
db.users.find({
  $or: [
    { phone: { $exists: false } },
    { phone: null }
  ]
})
```

### **Field Exists and Has Specific Type**

```javascript
// Field must exist and be a string
db.users.find({
  email: {
    $exists: true,
    $type: "string"
  }
})

// Field must exist and be an array
db.users.find({
  tags: {
    $exists: true,
    $type: "array"
  }
})
```

### **Practical Data Quality Checks**

```javascript
// Find incomplete user profiles (missing required fields)
db.users.find({
  $or: [
    { name: { $exists: false } },
    { email: { $exists: false } },
    { age: { $exists: false } }
  ]
})

// Find users with all required fields
db.users.find({
  name: { $exists: true },
  email: { $exists: true },
  age: { $exists: true }
})

// Find records with data quality issues
db.products.find({
  $or: [
    { price: { $type: "string" } },      // Should be number
    { quantity: { $type: "string" } },   // Should be number
    { name: { $type: { $ne: "string" } } } // Should be string
  ]
})
```

### **Node.js Example**

```javascript
async function findIncompleteProfiles() {
  const client = new MongoClient('mongodb://localhost:27017');
  
  try {
    await client.connect();
    const db = client.db('myapp');
    
    // Find profiles missing required fields
    const incompleteProfiles = await db.collection('users').find({
      $or: [
        { firstName: { $exists: false } },
        { lastName: { $exists: false } },
        { email: { $exists: false } }
      ]
    }).toArray();
    
    console.log(`Found ${incompleteProfiles.length} incomplete profiles`);
    return incompleteProfiles;
  } finally {
    await client.close();
  }
}

findIncompleteProfiles().catch(console.error);
```

---

## GitHub Resources

- **MongoDB Element Operators**: [mongodb/docs-operators-element](https://github.com/mongodb/docs)
- **Type Checking**: [mongodb/bson-types](https://github.com/mongodb/specifications/blob/master/source/bson-spec/bson-spec.rst)

---

Navigation: [← Chapter 11: Logical Operators](../ch11-logical-operators/README.md) | [↑ Index](../../index.md) | [→ Chapter 13: Expressions & Projections](../ch13-expressions-projections/README.md)
