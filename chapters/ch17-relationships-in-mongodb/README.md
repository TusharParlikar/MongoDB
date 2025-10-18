# Chapter 17: Relationships in MongoDB

## 17.1 One-to-One Relationships

A user has exactly one profile.

### **Embedded Approach**

```javascript
// Profile embedded in user document
db.users.insertOne({
  _id: 1,
  name: "John",
  profile: {
    bio: "Software engineer",
    avatar: "john.jpg",
    verified: true
  }
})

// Query
db.users.find({ "profile.verified": true })
```

### **Referenced Approach**

```javascript
// Separate collections with reference
db.users.insertOne({
  _id: 1,
  name: "John",
  profileId: ObjectId("...")
})

db.profiles.insertOne({
  _id: ObjectId("..."),
  userId: 1,
  bio: "Software engineer",
  avatar: "john.jpg"
})

// Query requires join (2 queries)
const user = db.users.findOne({ _id: 1 });
const profile = db.profiles.findOne({ userId: user._id });
```

### **When to Use**

- **Embedded**: Small profile, single object, always needed
- **Referenced**: Large profile, independent, optional queries

---

## 17.2 One-to-Many Relationships

A user has many posts.

### **Embedded Approach (Store Posts in User)**

```javascript
db.users.insertOne({
  _id: 1,
  name: "John",
  posts: [
    { _id: 1, title: "Post 1", content: "..." },
    { _id: 2, title: "Post 2", content: "..." }
  ]
})

// Query
db.users.find({ "posts.title": "Post 1" })

// Drawback: Document size grows, hard to paginate
```

### **Referenced Approach (Better for Many)**

```javascript
// Users collection
db.users.insertOne({
  _id: 1,
  name: "John"
})

// Posts collection
db.posts.insertMany([
  { _id: 1, userId: 1, title: "Post 1", content: "..." },
  { _id: 2, userId: 1, title: "Post 2", content: "..." }
])

// Get user's posts
db.posts.find({ userId: 1 })

// Get with aggregation join
db.posts.aggregate([
  { $match: { userId: 1 } },
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

## 17.3 Many-to-Many Relationships

Users have many skills, skills have many users.

### **Array of IDs Approach**

```javascript
// Users with skill IDs
db.users.insertOne({
  _id: 1,
  name: "John",
  skillIds: [1, 2, 3]  // References to skills
})

// Skills
db.skills.insertMany([
  { _id: 1, name: "JavaScript" },
  { _id: 2, name: "MongoDB" },
  { _id: 3, name: "Node.js" }
])

// Find users with JavaScript
db.users.find({ skillIds: 1 })

// Find skill details
const user = db.users.findOne({ _id: 1 });
db.skills.find({ _id: { $in: user.skillIds } })
```

### **Array of Objects Approach (Denormalization)**

```javascript
// Store skill details in user document
db.users.insertOne({
  _id: 1,
  name: "John",
  skills: [
    { skillId: 1, name: "JavaScript", level: "expert" },
    { skillId: 2, name: "MongoDB", level: "intermediate" }
  ]
})

// Query
db.users.find({ "skills.level": "expert" })

// Pro: Single query
// Con: Duplicate data if skills change
```

### **Join Table Approach (Most Flexible)**

```javascript
// Users
db.users.insertOne({ _id: 1, name: "John" })
db.users.insertOne({ _id: 2, name: "Jane" })

// Skills
db.skills.insertMany([
  { _id: 1, name: "JavaScript" },
  { _id: 2, name: "MongoDB" }
])

// User-Skill mapping
db.userSkills.insertMany([
  { userId: 1, skillId: 1, level: "expert" },
  { userId: 1, skillId: 2, level: "intermediate" },
  { userId: 2, skillId: 1, level: "beginner" }
])

// Query: John's skills
db.userSkills.aggregate([
  { $match: { userId: 1 } },
  {
    $lookup: {
      from: "skills",
      localField: "skillId",
      foreignField: "_id",
      as: "skill"
    }
  }
])
```

---

## 17.4 Embedded vs Referenced Data

### **Decision Matrix**

| Factor | Embedded | Referenced |
|--------|----------|-----------|
| **Data size** | Small | Large |
| **Query complexity** | Single query | Multiple/joins |
| **Updates** | Atomic | Complex |
| **Data duplication** | None | Potential |
| **Document growth** | Can grow too large | Stable |

### **Examples**

**Use Embedded:**
```javascript
// Address in User (one per user, small)
db.users.insertOne({
  name: "John",
  address: { street: "123 Main", city: "SF" }
})

// Settings in User (one per user, changes with user)
db.users.insertOne({
  name: "John",
  settings: { theme: "dark", language: "en" }
})
```

**Use Referenced:**
```javascript
// Many posts from one user (unbounded array)
db.users.insertOne({ _id: 1, name: "John" })
db.posts.insertMany([
  { userId: 1, title: "Post 1" },
  { userId: 1, title: "Post 2" }
  // ... potentially thousands
])

// Many comments on one post
db.posts.insertOne({ _id: 1, title: "Post 1" })
db.comments.insertMany([
  { postId: 1, text: "Comment 1" },
  { postId: 1, text: "Comment 2" }
  // ... potentially thousands
])
```

### **Node.js Aggregation Example**

```javascript
const { MongoClient } = require('mongodb');

async function getUserWithPosts() {
  const client = new MongoClient('mongodb://localhost:27017');
  
  try {
    await client.connect();
    const db = client.db('myapp');
    
    // Aggregation with $lookup (join)
    const result = await db.collection('users').aggregate([
      { $match: { _id: 1 } },
      {
        $lookup: {
          from: "posts",
          localField: "_id",
          foreignField: "userId",
          as: "posts"
        }
      },
      {
        $project: {
          name: 1,
          postCount: { $size: "$posts" },
          posts: 1
        }
      }
    ]).toArray();
    
    console.log(result);
  } finally {
    await client.close();
  }
}

getUserWithPosts().catch(console.error);
```

---

## GitHub Resources

- **MongoDB Data Modeling**: [mongodb/docs-data-modeling](https://github.com/mongodb/docs)
- **Relationships**: [mongodb/relationships](https://github.com/mongodb/mongo-manual)

---

Navigation: [← Chapter 16: Embedded Documents](../ch16-embedded-documents/README.md) | [↑ Index](../../index.md) | [→ Chapter 18: Indexing](../ch18-indexing/README.md)
