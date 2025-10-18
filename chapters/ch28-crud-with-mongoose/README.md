# Chapter 28: CRUD with Mongoose

## 28.1 Create Operations

```javascript
const user = new User({ name: "Alice", email: "alice@ex.com", age: 25 });
await user.save(); // or User.create({...})
```

---

## 28.2 Read Operations

```javascript
// Find all
const users = await User.find();

// Find by ID
const user = await User.findById('507f1f77bcf86cd799439011');

// Find one
const user = await User.findOne({ email: 'alice@ex.com' });

// With exec
const user = await User.findById(id).exec();
```

---

## 28.3 Update Operations

```javascript
// FindByIdAndUpdate
const updated = await User.findByIdAndUpdate(id, { age: 26 }, { new: true });

// UpdateOne
await User.updateOne({ name: 'Alice' }, { age: 27 });

// UpdateMany
await User.updateMany({ active: true }, { lastActive: new Date() });
```

---

## 28.4 Delete Operations

```javascript
// FindByIdAndDelete
const deleted = await User.findByIdAndDelete(id);

// DeleteOne
await User.deleteOne({ name: 'Alice' });

// DeleteMany
await User.deleteMany({ active: false });
```

---

## 28.5 Query Building

```javascript
// Chaining
const users = await User.find()
  .where('age').gte(18)
  .sort({ createdAt: -1 })
  .limit(10);

// Lean (returns plain JS, faster)
const users = await User.find().lean().limit(100);
```

---

## 28.6 Population (Relationships)

```javascript
const postSchema = new mongoose.Schema({
  title: String,
  author: { type: mongoose.Schema.Types.ObjectId, ref: 'User' }
});

// Populate author info
const post = await Post.findById(postId).populate('author');
console.log(post.author.name); // Author details included
```

---

## GitHub Resources

- **Automattic/mongoose**: [github.com/Automattic/mongoose](https://github.com/Automattic/mongoose)

---

Navigation: [← Chapter 27: Mongoose ODM](../ch27-mongoose-odm/README.md) | [↑ Index](../../index.md) | [→ Chapter 29: MERN Stack Integration](../ch29-mern-stack-integration/README.md)

