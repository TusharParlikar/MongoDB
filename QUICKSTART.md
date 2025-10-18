# 🚀 Quick Start Guide

Get started with MongoDB Complete Mastery Course in 5 minutes!

---

## 📋 Prerequisites Checklist

Before you begin, make sure you have:

- [ ] **Basic JavaScript knowledge** - Variables, functions, promises, async/await
- [ ] **Command line familiarity** - Basic terminal/command prompt usage
- [ ] **Text editor** - VS Code, Sublime Text, or similar
- [ ] **Internet connection** - For downloading MongoDB and accessing resources

---

## ⚡ 5-Minute Setup

### Step 1: Install MongoDB (Choose One)

#### Option A: MongoDB Community Edition (Local)

**macOS:**
```bash
# Install using Homebrew
brew tap mongodb/brew
brew install mongodb-community@6.0
brew services start mongodb-community@6.0
```

**Linux (Ubuntu/Debian):**
```bash
# Import MongoDB GPG key
wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | sudo apt-key add -

# Add repository
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list

# Install MongoDB
sudo apt-get update
sudo apt-get install -y mongodb-org

# Start MongoDB
sudo systemctl start mongod
```

**Windows:**
1. Download installer from [mongodb.com/try/download/community](https://www.mongodb.com/try/download/community)
2. Run installer and follow wizard
3. MongoDB runs as a Windows service automatically

#### Option B: MongoDB Atlas (Cloud - Recommended for Beginners)

1. Go to [mongodb.com/cloud/atlas](https://www.mongodb.com/cloud/atlas)
2. Click "Try Free"
3. Create account (Google/GitHub sign-in available)
4. Choose FREE tier (M0 - 512MB)
5. Select cloud provider and region
6. Create cluster (takes 3-5 minutes)
7. Get connection string from "Connect" button

---

### Step 2: Install MongoDB Shell (mongosh)

**macOS:**
```bash
brew install mongosh
```

**Linux:**
```bash
wget https://downloads.mongodb.com/compass/mongodb-mongosh_1.10.6_amd64.deb
sudo dpkg -i mongodb-mongosh_1.10.6_amd64.deb
```

**Windows:**
```bash
# Download from mongodb.com/try/download/shell
# Or use chocolatey:
choco install mongosh
```

**Verify Installation:**
```bash
mongosh --version
# Should output: 1.10.x or higher
```

---

### Step 3: Connect to MongoDB

#### If Using Local MongoDB:
```bash
mongosh
# You should see: Current Mongosh version: x.x.x
# Connected to: mongodb://127.0.0.1:27017
```

#### If Using MongoDB Atlas:
```bash
# Replace with your actual connection string
mongosh "mongodb+srv://username:password@cluster.mongodb.net/"
```

---

### Step 4: Run Your First Commands

```javascript
// 1. Create a database
use myapp

// 2. Insert a document
db.users.insertOne({
  name: "Alice",
  email: "alice@example.com",
  age: 28
})

// 3. Query documents
db.users.find()

// 4. Update a document
db.users.updateOne(
  { name: "Alice" },
  { $set: { age: 29 } }
)

// 5. Delete a document
db.users.deleteOne({ name: "Alice" })
```

**Expected Output:**
```json
{
  "acknowledged": true,
  "insertedId": ObjectId("507f1f77bcf86cd799439011")
}
```

---

## 📚 Start Learning

Now you're ready! Follow this path:

### Week 1: Foundations (Chapters 1-5)
```bash
# Start here
1. Read Chapter 1: Introduction to MongoDB
2. Read Chapter 2: SQL vs NoSQL
3. Practice basic commands from Chapter 6
```

**Time:** 5-7 hours | **Level:** ⭐ Beginner

### Week 2: CRUD Mastery (Chapters 6-10)
```bash
# Learn core operations
1. Practice insertOne/insertMany
2. Master find() with filters
3. Learn comparison operators
4. Implement pagination with cursors
```

**Time:** 8-10 hours | **Level:** ⭐⭐ Intermediate

### Week 3: Advanced Queries (Chapters 11-17)
```bash
# Complex queries
1. Logical operators ($and, $or)
2. Projections and expressions
3. Update/delete operations
4. Data modeling patterns
```

**Time:** 10-12 hours | **Level:** ⭐⭐⭐ Advanced

### Week 4: Optimization & Tools (Chapters 18-25)
```bash
# Professional techniques
1. Indexing strategies
2. Aggregation pipelines
3. MongoDB Atlas setup
4. Compass GUI usage
```

**Time:** 12-15 hours | **Level:** ⭐⭐⭐⭐ Expert

---

## 🎯 Your First Mini-Project

### Build a Todo List Database

**Step 1: Create the database**
```javascript
use todo_app
```

**Step 2: Insert tasks**
```javascript
db.todos.insertMany([
  { task: "Learn MongoDB", completed: false, priority: "high" },
  { task: "Build a project", completed: false, priority: "medium" },
  { task: "Deploy to production", completed: false, priority: "low" }
])
```

**Step 3: Query tasks**
```javascript
// Find all incomplete tasks
db.todos.find({ completed: false })

// Find high-priority tasks
db.todos.find({ priority: "high" })

// Count total tasks
db.todos.countDocuments()
```

**Step 4: Update a task**
```javascript
db.todos.updateOne(
  { task: "Learn MongoDB" },
  { $set: { completed: true } }
)
```

**Step 5: Delete completed tasks**
```javascript
db.todos.deleteMany({ completed: true })
```

---

## 🛠️ Install Node.js Integration (Optional)

If you want to build backend applications:

### Install Node.js
```bash
# Download from nodejs.org
# Or use package manager:

# macOS
brew install node

# Linux
sudo apt-get install nodejs npm

# Windows
choco install nodejs
```

### Create Node.js Project
```bash
# Create project folder
mkdir mongodb-node-app
cd mongodb-node-app

# Initialize package.json
npm init -y

# Install MongoDB driver
npm install mongodb

# Or install Mongoose (recommended)
npm install mongoose
```

### Test Connection (Node.js)
```javascript
// test.js
const { MongoClient } = require('mongodb');

async function main() {
  const uri = "mongodb://localhost:27017";
  const client = new MongoClient(uri);
  
  try {
    await client.connect();
    console.log("✅ Connected to MongoDB!");
    
    const db = client.db('myapp');
    const users = await db.collection('users').find().toArray();
    console.log('Users:', users);
    
  } finally {
    await client.close();
  }
}

main();
```

Run it:
```bash
node test.js
```

---

## 🎓 Learning Tips

### 1. **Practice Daily**
- Spend 30-60 minutes per day
- Type out all examples yourself
- Don't copy-paste blindly

### 2. **Build Projects**
- Apply concepts immediately
- Start small, grow gradually
- Share your projects!

### 3. **Join Community**
- Stack Overflow: [mongodb tag](https://stackoverflow.com/questions/tagged/mongodb)
- MongoDB Forums: [community.mongodb.com](https://www.mongodb.com/community/forums)
- Discord/Slack communities

### 4. **Use Documentation**
- Official docs: [docs.mongodb.com](https://docs.mongodb.com)
- This course appendix
- MongoDB University (free courses)

---

## 📱 Tools to Install (Recommended)

### MongoDB Compass (GUI Tool)
```bash
# Download from: mongodb.com/products/compass

# macOS
brew install --cask mongodb-compass

# Windows
choco install mongodb-compass

# Linux
# Download .deb or .rpm from website
```

### Postman (API Testing)
```bash
# Download from: postman.com/downloads

# Useful for testing REST APIs with MongoDB backend
```

### VS Code Extensions
- MongoDB for VS Code
- Thunder Client (API testing)
- REST Client

---

## 🐛 Troubleshooting

### Issue: "mongosh: command not found"
**Solution:**
```bash
# Add to PATH (macOS/Linux)
export PATH=$PATH:/usr/local/bin

# Verify installation
which mongosh
```

### Issue: "MongoServerError: connection refused"
**Solution:**
```bash
# Check if MongoDB is running
ps aux | grep mongod

# Start MongoDB
# macOS
brew services start mongodb-community

# Linux
sudo systemctl start mongod

# Windows
net start MongoDB
```

### Issue: "Authentication failed"
**Solution:**
```bash
# Check your connection string
# For Atlas, verify username/password
# Ensure IP whitelist includes your IP
```

### Issue: "Cannot find module 'mongodb'"
**Solution:**
```bash
# Install the driver
npm install mongodb

# Or for Mongoose
npm install mongoose
```

---

## ✅ You're Ready!

If you've completed this guide, you should have:

- ✅ MongoDB installed and running
- ✅ MongoDB Shell (mongosh) working
- ✅ Executed your first CRUD operations
- ✅ Understood the course structure
- ✅ (Optional) Node.js integration setup

---

## 🚀 Next Steps

1. **Read the full README** - [README.md](README.md)
2. **Start Chapter 1** - [Introduction to MongoDB](chapters/ch01-introduction-to-mongodb/README.md)
3. **Join the community** - GitHub Discussions
4. **Star the repo** - Help others discover this course!

---

## 📞 Need Help?

-  Ask in [Discussions](https://github.com/TusharParlikar/MongoDB/discussions)
- 🐛 Report issues in [Issues](https://github.com/TusharParlikar/MongoDB/issues)
- 📧 Email the maintainer

---

<div align="center">

**Happy Learning! 🎓**

[⬆ Back to Top](#-quick-start-guide)

</div>
