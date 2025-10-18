# Appendix: MongoDB Reference Guide

## A. MongoDB Commands Reference

### Database Commands
```bash
show dbs                          # List all databases
use dbname                        # Switch/create database
db.createCollection('name')       # Create collection
db.dropDatabase()                 # Drop current database
db.stats()                        # Database statistics
```

### Collection Commands
```bash
show collections                  # List collections
db.collection.drop()              # Drop collection
db.collection.stats()             # Collection statistics
db.collection.count()             # Document count
```

### CRUD Commands
```bash
db.col.insertOne({...})           # Insert single doc
db.col.insertMany([{...}])        # Insert multiple
db.col.find()                     # Find all
db.col.findOne({...})             # Find one
db.col.updateOne({}, {$set: {}})  # Update one
db.col.updateMany({}, {$set: {}}) # Update many
db.col.deleteOne({})              # Delete one
db.col.deleteMany({})             # Delete many
```

### Index Commands
```bash
db.col.createIndex({field: 1})    # Create index
db.col.getIndexes()               # List indexes
db.col.dropIndex('field_1')       # Drop index
db.col.dropIndexes()              # Drop all indexes
```

### Aggregation Commands
```bash
db.col.aggregate([...])           # Run pipeline
db.col.aggregate([...]).explain() # Pipeline explain
```

---

## B. Mongoose Cheat Sheet

### Schema Types
```javascript
String, Number, Date, Boolean, Array, Object, ObjectId, Mixed, Decimal128, Buffer, Map
```

### Schema Definition
```javascript
const schema = new mongoose.Schema({
  field: String,
  num: { type: Number, required: true },
  email: { type: String, unique: true, lowercase: true },
  age: { type: Number, min: 0, max: 120 },
  status: { type: String, enum: ['active', 'inactive'] },
  createdAt: { type: Date, default: Date.now }
});
```

### Validators
```javascript
required, min, max, minlength, maxlength, enum, match, validate
```

### Query Methods
```javascript
Model.find()
Model.findById()
Model.findOne()
Model.updateOne()
Model.updateMany()
Model.deleteOne()
Model.deleteMany()
Model.findByIdAndUpdate()
Model.findByIdAndDelete()
Model.countDocuments()
```

### Query Helpers
```javascript
.select()        # Field selection
.limit()         # Limit results
.skip()          # Skip documents
.sort()          # Sort results
.exec()          # Execute query
.lean()          # Plain JS objects
.populate()      # Join references
```

### Middleware
```javascript
schema.pre('save', function(next) { });
schema.post('save', function(doc) { });
schema.pre('updateOne', function(next) { });
```

---

## C. Common Error Messages & Solutions

| Error | Cause | Solution |
|-------|-------|----------|
| **E11000** | Duplicate key in unique field | Check unique constraints, use `.updateOne()` with `upsert: true` |
| **MongoError: not master** | Connection to replica set failed | Check connection string, verify network |
| **Timeout exceeded** | Query too slow or connection lost | Add indexes, reduce query size, check network |
| **ValidationError** | Schema validation failed | Validate input data before save |
| **CastError** | Invalid ObjectId format | Pass valid 24-char hex string |
| **ReferenceError** | Field not defined in schema | Add field to schema or use .strict(false) |
| **ConnectionRefused** | MongoDB not running | Start MongoDB: `mongod` or `systemctl start mongod` |
| **Authentication failed** | Wrong credentials | Verify username/password in connection string |

---

## D. Resources & Links

### Official Documentation
- **MongoDB Docs**: https://docs.mongodb.com
- **Mongoose Docs**: https://mongoosejs.com
- **MongoDB University**: https://university.mongodb.com

### Community & Help
- **Stack Overflow**: [tag: mongodb](https://stackoverflow.com/questions/tagged/mongodb)
- **MongoDB Forums**: https://developer.mongodb.com/community/forums
- **Node.js**: https://nodejs.org/docs

### Tools & Extensions
- **Compass**: https://www.mongodb.com/products/compass (GUI)
- **Atlas**: https://www.mongodb.com/cloud/atlas (Cloud)
- **Postman**: https://www.postman.com (API Testing)
- **VS Code Extensions**: MongoDB for VS Code, Postman

### GitHub Resources
- **mongodb/mongo**: https://github.com/mongodb/mongo
- **Automattic/mongoose**: https://github.com/Automattic/mongoose
- **mongodb/node-mongodb-native**: https://github.com/mongodb/node-mongodb-native
- **nodebestpractices**: https://github.com/goldbergyoni/nodebestpractices
- **awesome-mongodb**: https://github.com/ramnes/awesome-mongodb

### Learning Resources
- **MongoDB YouTube**: https://www.youtube.com/mongodb
- **Traversy Media**: MERN stack tutorials
- **FreeCodeCamp**: Full MongoDB courses
- **Udemy**: Various MongoDB & MERN courses

---

## E. Quick Reference Tables

### Query Operators

| Operator | Purpose | Example |
|----------|---------|---------|
| $eq | Equal | `{age: {$eq: 25}}` |
| $ne | Not equal | `{status: {$ne: 'inactive'}}` |
| $gt | Greater than | `{age: {$gt: 18}}` |
| $gte | Greater or equal | `{score: {$gte: 80}}` |
| $lt | Less than | `{age: {$lt: 65}}` |
| $lte | Less or equal | `{score: {$lte: 100}}` |
| $in | In array | `{status: {$in: ['active', 'pending']}}` |
| $nin | Not in array | `{status: {$nin: ['deleted']}}` |
| $and | Logical AND | `{$and: [{age: {$gt: 18}}, {active: true}]}` |
| $or | Logical OR | `{$or: [{role: 'admin'}, {role: 'mod'}]}` |
| $not | Logical NOT | `{age: {$not: {$gt: 65}}}` |
| $exists | Field exists | `{phone: {$exists: true}}` |
| $type | BSON type | `{email: {$type: 'string'}}` |

### Update Operators

| Operator | Purpose |
|----------|---------|
| $set | Set field value |
| $unset | Remove field |
| $inc | Increment number |
| $push | Add to array |
| $pull | Remove from array |
| $addToSet | Add if not exists |
| $pop | Remove first/last |
| $rename | Rename field |
| $mul | Multiply field |
| $min | Set if less |
| $max | Set if greater |
| $currentDate | Set to current date |

### Aggregation Stages

| Stage | Purpose |
|-------|---------|
| $match | Filter documents |
| $project | Select/compute fields |
| $group | Group by field |
| $sort | Sort documents |
| $limit | Limit results |
| $skip | Skip documents |
| $unwind | Deconstruct array |
| $lookup | Join collections |
| $filter | Filter arrays |
| $facet | Multi-stage output |

---

Navigation: [← Chapter 32: Best Practices & Security](./chapters/ch32-best-practices-security/README.md) | [↑ Index](./index.md)
