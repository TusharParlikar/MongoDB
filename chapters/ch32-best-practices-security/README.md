# Chapter 32: Best Practices & Security

## 32.1 Schema Design Best Practices

```javascript
// ✅ Good: Indexed email, required name, default timestamp
const userSchema = new mongoose.Schema({
  name: { type: String, required: true },
  email: { type: String, required: true, unique: true, lowercase: true },
  role: { type: String, enum: ['user', 'admin'], default: 'user' },
  createdAt: { type: Date, default: Date.now }
});

userSchema.index({ email: 1 });

// ❌ Bad: No indexes, missing validation, inconsistent naming
const userSchema = new mongoose.Schema({
  userName: String,
  userEmail: String,
  USER_ROLE: String
});
```

---

## 32.2 Query Optimization

```javascript
// ✅ Use .lean() for read-only data
const users = await User.find().lean();

// ✅ Use .select() to exclude fields
const users = await User.find().select('-password');

// ✅ Use .explain() to check index usage
const plan = await User.find({ email: 'test@ex.com' }).explain('executionStats');

// ❌ Avoid: N+1 queries
for (let user of users) {
  const posts = await Post.find({ userId: user._id });
}
```

---

## 32.3 Security Considerations

```javascript
// ✅ Hash passwords
const bcrypt = require('bcrypt');
userSchema.pre('save', async function(next) {
  if (!this.isModified('password')) return next();
  this.password = await bcrypt.hash(this.password, 10);
  next();
});

// ✅ Validate input
app.post('/api/users', (req, res) => {
  if (!req.body.email.includes('@')) return res.status(400).send('Invalid email');
  // Create user...
});

// ✅ Use JWT for auth
const token = jwt.sign({ id: user._id }, process.env.JWT_SECRET, { expiresIn: '7d' });
```

---

## 32.4 Authentication & Authorization

```javascript
// JWT Middleware
const authenticate = (req, res, next) => {
  const token = req.headers.authorization?.split(' ')[1];
  if (!token) return res.status(401).send('No token');
  try {
    req.user = jwt.verify(token, process.env.JWT_SECRET);
    next();
  } catch (err) {
    res.status(403).send('Invalid token');
  }
};

// Role-based access
const authorize = (roles) => (req, res, next) => {
  if (!roles.includes(req.user.role)) {
    return res.status(403).send('Forbidden');
  }
  next();
};

app.delete('/api/users/:id', authenticate, authorize(['admin']), async (req, res) => {
  await User.findByIdAndDelete(req.params.id);
  res.json({ success: true });
});
```

---

## 32.5 Data Validation

```javascript
// Schema validation
const userSchema = new mongoose.Schema({
  name: { type: String, required: true, minlength: 3, maxlength: 50 },
  email: { type: String, required: true, match: /.+\@.+\..+/ },
  age: { type: Number, min: 0, max: 150 }
});

// Custom validators
userSchema.pre('save', function(next) {
  if (this.age < 18) {
    this.invalidate('age', 'Must be 18+');
  }
  next();
});

// Try/catch on save
try {
  await user.save();
} catch (err) {
  if (err.name === 'ValidationError') {
    console.log('Validation failed:', err.errors);
  }
}
```

---

## 32.6 Error Handling & Logging

```javascript
// Global error handler
app.use((err, req, res, next) => {
  console.error(err.stack);
  if (err.code === 11000) {
    return res.status(400).json({ error: 'Duplicate key' });
  }
  if (err.name === 'ValidationError') {
    return res.status(400).json({ error: 'Invalid data' });
  }
  res.status(500).json({ error: 'Server error' });
});

// Log to file
const fs = require('fs');
app.use((err, req, res, next) => {
  fs.appendFileSync('errors.log', `${new Date()}: ${err.message}\n`);
  res.status(500).json({ error: 'Error logged' });
});
```

---

## Security Checklist

- ✅ Use environment variables for secrets
- ✅ Hash passwords with bcrypt
- ✅ Validate all inputs
- ✅ Use HTTPS only
- ✅ Implement rate limiting
- ✅ Enable CORS carefully
- ✅ Monitor database logs
- ✅ Regular backups
- ✅ Keep dependencies updated
- ✅ Never commit .env files

---

GitHub: [github.com/goldbergyoni/nodebestpractices](https://github.com/goldbergyoni/nodebestpractices)

---

Navigation: [← Chapter 31: Deployment & Hosting](../ch31-deployment-hosting/README.md) | [↑ Index](../../index.md) | [→ Appendix](../../APPENDIX.md)
