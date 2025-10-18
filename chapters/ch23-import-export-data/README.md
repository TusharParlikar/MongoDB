# Chapter 23: Import/Export Data

## 23.1 mongoimport Command

```bash
# Import JSON
mongoimport --db myapp --collection users --file users.json

# Import with merge (upsert)
mongoimport --db myapp --collection users --file users.json --upsertFields email

# Import CSV
mongoimport --db myapp --collection products --type csv --file products.csv --headerline
```

---

## 23.2 mongoexport Command

```bash
# Export JSON
mongoexport --db myapp --collection users --out users.json

# Export with query filter
mongoexport --db myapp --collection users --query '{"status":"active"}' --out active_users.json

# Export CSV
mongoexport --db myapp --collection users --type csv --fields name,email,age --out users.csv
```

---

## 23.3 Importing JSON Files

```bash
mongoimport --db shopdb --collection products \
  --file ./data/products.json \
  --jsonArray  # For array of objects

# Drop collection first
mongoimport --db shopdb --collection products \
  --file ./data/products.json \
  --drop
```

---

## 23.4 Importing JSON Arrays

```json
[
  { "name": "Product 1", "price": 100 },
  { "name": "Product 2", "price": 200 }
]
```

```bash
mongoimport --db shopdb --collection products \
  --file products.json \
  --jsonArray
```

---

## 23.5 Exporting to JSON

```bash
# Pretty-printed JSON
mongoexport --db myapp --collection users --out users.json --pretty

# Specific fields only
mongoexport --db myapp --collection users \
  --fields name,email,age \
  --out user_summary.json
```

---

Navigation: [← Chapter 22: Aggregation Expressions](../ch22-aggregation-expressions/README.md) | [↑ Index](../../index.md) | [→ Chapter 24: MongoDB Atlas](../ch24-mongodb-atlas/README.md)

