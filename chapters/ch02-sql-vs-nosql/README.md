# Chapter 2: SQL vs NoSQL

## 2.1 SQL Databases Overview

**SQL (Structured Query Language)** databases are relational databases that organize data in **tables with rows and columns**. They follow a strict schema and use structured queries.

**SQL Database Characteristics:**
- **ACID Transactions**: Atomicity, Consistency, Isolation, Durability
- **Rigid Schema**: Predefined table structure with fixed columns
- **Normalization**: Data split across multiple tables to reduce redundancy
- **Foreign Keys**: References between tables maintain relationships
- **SQL Queries**: Powerful, standardized query language

**Popular SQL Databases:**
- PostgreSQL
- MySQL
- Microsoft SQL Server
- Oracle Database
- MariaDB

**Example SQL Schema:**
```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100),
  age INT,
  created_at TIMESTAMP
);

CREATE TABLE addresses (
  id INT PRIMARY KEY,
  user_id INT FOREIGN KEY,
  street VARCHAR(100),
  city VARCHAR(50),
  country VARCHAR(50)
);
```

**Pros:**
✅ Data consistency  
✅ Complex joins  
✅ Transaction support  
✅ Established & proven  

**Cons:**
❌ Scaling complexity (vertical scaling)  
❌ Schema rigidity  
❌ Less flexible for varying data  

---

## 2.2 NoSQL Databases Overview

**NoSQL (Not Only SQL)** databases provide flexible, scalable alternatives to traditional SQL. They don't require predefined schemas and handle distributed data effectively.

**NoSQL Database Types:**

| Type | Example | Use Case |
|------|---------|----------|
| **Document** | MongoDB, CouchDB | Flexible JSON-like data |
| **Key-Value** | Redis, Memcached | Fast caching, sessions |
| **Wide-Column** | Cassandra, HBase | Time-series, analytics |
| **Search** | Elasticsearch | Full-text search, logs |
| **Graph** | Neo4j | Relationships, networks |

**MongoDB (Document NoSQL) Characteristics:**
- **Flexible Schema**: Documents in same collection vary
- **Horizontal Scaling**: Easy sharding across servers
- **High Performance**: Optimized for read/write operations
- **Rich Queries**: Aggregation framework, text search
- **JSON-like Format**: Developer-friendly document structure

**Example MongoDB Structure:**
```javascript
db.users.insertOne({
  name: "Alice",
  email: "alice@example.com",
  age: 25,
  skills: ["JavaScript", "Python"],
  address: {
    street: "123 Main St",
    city: "San Francisco"
  }
});
```

**Pros:**
✅ Horizontal scalability  
✅ Schema flexibility  
✅ High performance  
✅ Developer-friendly  

**Cons:**
❌ Limited ACID support (older versions)  
❌ Potential data duplication  
❌ Larger document sizes  

---

## 2.3 Structured vs Unstructured Data

**Structured Data:**
- **Defined Schema**: Fixed format, known fields
- **Highly Organized**: Data fits neatly in tables/rows
- **Example**: Employee records with fixed fields

```sql
-- Structured: All users have same fields
id | name | email | age | department
---|------|-------|-----|------------
1  | John | j@ex  | 30  | Sales
2  | Jane | j2@ex | 28  | Engineering
```

**Unstructured Data:**
- **No Predefined Schema**: Variable format, flexible fields
- **Variable Content**: Documents can have different fields
- **Example**: Social media posts, user profiles, logs

```json
// Unstructured: Different users have different fields
{
  "name": "John",
  "email": "j@example.com",
  "age": 30,
  "department": "Sales"
}

{
  "name": "Jane",
  "email": "j2@example.com",
  "skills": ["Node.js", "React"],
  "bio": "Full-stack developer"
  // Different structure!
}
```

**Semi-Structured Data (MongoDB Sweet Spot):**
- Has some organizational structure (JSON)
- But flexible schema for variations

```javascript
// Semi-structured: MongoDB documents with flexible fields
{
  _id: ObjectId(),
  name: "User 1",
  email: "user1@ex.com",
  metadata: { ... }  // Optional
}

{
  _id: ObjectId(),
  name: "User 2",
  phone: "555-1234",
  preferences: { ... }  // Different field!
}
```

---

## 2.4 Tables vs Collections vs Documents

**SQL: Tables & Rows**
```
Table: users
┌────────────────────────────────────────┐
│ id | name    | email       | age       │
├────┼─────────┼─────────────┼───────────┤
│ 1  │ John    │ j@ex.com    │ 30        │
│ 2  │ Jane    │ j2@ex.com   │ 28        │
└────────────────────────────────────────┘
```

**MongoDB: Collections & Documents**
```
Collection: users
[
  {
    "_id": ObjectId("..."),
    "name": "John",
    "email": "j@ex.com",
    "age": 30,
    "skills": ["Node.js", "MongoDB"]
  },
  {
    "_id": ObjectId("..."),
    "name": "Jane",
    "email": "j2@ex.com",
    "age": 28,
    "department": "Engineering"
  }
]
```

**Key Differences:**

| Aspect | SQL Table | MongoDB Collection |
|--------|-----------|-------------------|
| **Rows** | Fixed structure | Flexible documents |
| **Columns** | Predefined | Variable per document |
| **Relations** | Foreign keys | Embedding/References |
| **Joins** | JOINS keyword | $lookup operator |
| **Null Values** | Explicit NULL | Field simply absent |
| **Schema Change** | ALTER TABLE | Just add field |

---

## 2.5 When to Use SQL vs NoSQL

### **Use SQL When:**
✅ Data is highly relational  
✅ Strong data consistency required  
✅ Complex queries with multiple joins  
✅ Transactions across multiple operations  
✅ Data structure is stable/predictable  

**Example:** Banking systems, ERP, OLTP systems

### **Use NoSQL (MongoDB) When:**
✅ Data is loosely structured/hierarchical  
✅ Horizontal scalability needed  
✅ High read/write throughput  
✅ Schema evolves frequently  
✅ Document-centric data  

**Example:** Social media, content management, IoT, analytics

### **Comparison Table:**

| Scenario | SQL | MongoDB |
|----------|-----|---------|
| Blog with fixed post structure | ✅ Good | OK |
| Blog with varied content types | ❌ Hard | ✅ Perfect |
| Banking transactions | ✅ Best | OK |
| User profiles with varied fields | ❌ Awkward | ✅ Ideal |
| Large-scale real-time data | ❌ Difficult | ✅ Great |
| Reporting with complex queries | ✅ Best | OK (Aggregation) |

### **Hybrid Approach:**
Many organizations use **both**:
- MongoDB for user-generated content, logs, analytics
- PostgreSQL for financial transactions, critical data

---

## GitHub Resources

- **MongoDB Documentation**: [mongodb/docs](https://github.com/mongodb/docs)
- **MongoDB vs SQL Examples**: [mongodb/sql-to-mongo-aggregation](https://github.com/mongodb/sql-to-mongo-aggregation)
- **Comparison Tools**: [sqlnoSql](https://github.com/sureshchand/sqlnoSql)
- **Learning Resources**: [30-Days-Of-MongoDB](https://github.com/iampawan/30-Days-Of-MongoDB)

---

Navigation: [← Chapter 1: Introduction to MongoDB](../ch01-introduction-to-mongodb/README.md) | [↑ Index](../../index.md) | [→ Chapter 3: JSON vs BSON](../ch03-json-vs-bson/README.md)
