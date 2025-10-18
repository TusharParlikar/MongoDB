# Chapter 5: MongoDB Installation & Setup

## 5.1 Installing MongoDB Community Server

MongoDB Community Edition is free and open-source. Here's how to install on all major platforms.

### **macOS Installation (Homebrew)**

```bash
# 1. Tap MongoDB Homebrew repository
brew tap mongodb/brew

# 2. Install MongoDB Community Edition
brew install mongodb-community

# 3. Check version
mongod --version

# 4. Verify installation
which mongod
# Output: /usr/local/bin/mongod
```

### **Linux Installation (Ubuntu/Debian)**

```bash
# 1. Import GPG key
curl -fsSL https://www.mongodb.org/static/pgp/server-7.0.asc | sudo apt-key add -

# 2. Add MongoDB repository
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list

# 3. Update package manager
sudo apt-get update

# 4. Install MongoDB
sudo apt-get install -y mongodb-org

# 5. Start MongoDB service
sudo systemctl start mongod

# 6. Enable auto-start on boot
sudo systemctl enable mongod

# 7. Check status
sudo systemctl status mongod
```

### **Windows Installation**

```powershell
# Option 1: Download MSI installer
# Visit: https://www.mongodb.com/try/download/community
# Run: mongodb-windows-x86_64-7.0.0-signed.msi

# Option 2: Using Chocolatey
choco install mongodb

# Option 3: Manual ZIP extraction
# 1. Download ZIP file
# 2. Extract to C:\Program Files\MongoDB\Server\7.0
# 3. Add to PATH environment variable
# 4. Create data directory: C:\data\db

# Start MongoDB
"C:\Program Files\MongoDB\Server\7.0\bin\mongod.exe"
```

### **Docker Installation**

```bash
# 1. Pull MongoDB image
docker pull mongo:latest

# 2. Run MongoDB container
docker run --name mongodb -d -p 27017:27017 mongo:latest

# 3. Run with persistent volume
docker run --name mongodb -d \
  -p 27017:27017 \
  -v mongodb_data:/data/db \
  mongo:latest

# 4. Access MongoDB shell in container
docker exec -it mongodb mongosh

# 5. Stop container
docker stop mongodb
```

### **Installation Directory Structure**

```
macOS/Linux: /usr/local/bin/
├── mongod           (database server)
├── mongosh          (shell)
├── mongoimport      (data import)
├── mongoexport      (data export)
└── mongodump        (backup)

Windows: C:\Program Files\MongoDB\Server\7.0\bin\
├── mongod.exe
├── mongosh.exe
├── mongoimport.exe
└── ...
```

---

## 5.2 Installing MongoDB Shell (mongosh)

**mongosh** is the modern interactive MongoDB shell, replacing the deprecated `mongo` shell.

### **Installation**

**Homebrew (macOS):**
```bash
brew install mongosh
```

**Linux (Ubuntu):**
```bash
# Using official repository
curl -fsSL https://www.mongodb.org/static/pgp/server-7.0.asc | sudo apt-key add -
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu focal/mongosh main" | sudo tee /etc/apt/sources.list.d/mongosh.list
sudo apt-get update
sudo apt-get install -y mongosh
```

**Windows:**
```powershell
choco install mongosh
# or download from https://www.mongodb.com/try/download/shell
```

**npm:**
```bash
npm install -g mongosh
```

### **Verify Installation**

```bash
mongosh --version
# Output: 1.10.0 or higher
```

---

## 5.3 Installing Database Tools

MongoDB Database Tools include `mongoimport`, `mongoexport`, `mongodump`, `mongorestore`, etc.

### **Installation Methods**

**macOS:**
```bash
brew install mongodb-database-tools
```

**Linux:**
```bash
# Download tools package
wget https://fastdl.mongodb.org/tools/db/mongodb-database-tools-ubuntu2004-x86_64-100.8.0.deb

# Install
sudo dpkg -i mongodb-database-tools-ubuntu2004-x86_64-100.8.0.deb

# Verify
mongoimport --version
```

**Windows:**
```powershell
# Download from: https://www.mongodb.com/try/download/database-tools
# Or use Chocolatey:
choco install mongodb-cli-tools
```

**npm:**
```bash
npm install -g mongodb-database-tools
```

### **Included Tools**

| Tool | Purpose |
|------|---------|
| `mongoimport` | Import JSON/CSV to MongoDB |
| `mongoexport` | Export MongoDB to JSON/CSV |
| `mongodump` | Backup entire database |
| `mongorestore` | Restore from backup |
| `mongostat` | Monitor MongoDB statistics |
| `mongotop` | Show top operations |
| `bsondump` | Convert BSON to JSON |

---

## 5.4 Verifying Installation

### **Check All Components:**

```bash
# 1. Check MongoDB server
mongod --version
# Output: db version v7.0.0, target: 7.0.0

# 2. Check MongoDB shell
mongosh --version
# Output: 1.10.0

# 3. Check database tools
mongoimport --version
# Output: mongoimport version: 100.8.0

# 4. Check PATH
echo $PATH  # Should include MongoDB bin directory
```

### **Test Connectivity**

```bash
# Start MongoDB in background (one terminal)
mongod

# In another terminal, connect
mongosh

# You should see the mongosh prompt:
# test>

# Run a test command
test> db.version()
# Output: 7.0.0

# Exit shell
test> exit
```

### **Create Test Database**

```javascript
// In mongosh shell
use testdb

db.testcollection.insertOne({ message: "Hello MongoDB!" })

db.testcollection.findOne()
// Output: {
//   _id: ObjectId("..."),
//   message: 'Hello MongoDB!'
// }
```

---

## 5.5 Starting MongoDB Server

### **Manual Start**

**macOS/Linux:**
```bash
# Start with default settings
mongod

# Output should show:
# [initandlisten] Listening on 127.0.0.1:27017
# [initandlisten] waiting for connections on port 27017
```

**Windows:**
```powershell
# Start from command prompt
"C:\Program Files\MongoDB\Server\7.0\bin\mongod.exe"
```

### **Service Start (Background)**

**macOS (Homebrew):**
```bash
# Start service
brew services start mongodb-community

# Stop service
brew services stop mongodb-community

# Restart service
brew services restart mongodb-community

# Check status
brew services list
```

**Linux (systemd):**
```bash
# Start service
sudo systemctl start mongod

# Stop service
sudo systemctl stop mongod

# Restart service
sudo systemctl restart mongod

# Enable auto-start on boot
sudo systemctl enable mongod

# Check status
sudo systemctl status mongod
```

**Windows (Services):**
```powershell
# Start service
net start MongoDB

# Stop service
net stop MongoDB

# Or use Services GUI:
# services.msc > MongoDB > Start/Stop
```

### **With Configuration File**

**Create Config File (mongod.conf):**

```yaml
# Network
net:
  port: 27017
  bindIp: localhost
  maxIncomingConnections: 65536

# Storage
storage:
  dbPath: /data/db
  journal:
    enabled: true
  wiredTiger:
    engineConfig:
      cacheSizeGB: 2

# Security (optional)
security:
  authorization: enabled

# Replication (optional)
replication:
  replSetName: "rs0"

# Logging
systemLog:
  destination: file
  path: /var/log/mongodb/mongod.log
  logAppend: true
```

**Start with Config:**
```bash
mongod --config /path/to/mongod.conf

# Or
mongod -f /path/to/mongod.conf
```

### **Custom Data Directory**

```bash
# Create data directory
mkdir -p /data/db

# Set permissions (Linux/macOS)
chmod -R 755 /data/db

# Start MongoDB with custom path
mongod --dbpath /data/db

# For Windows
mongod --dbpath "C:\data\db"
```

### **Startup Options**

```bash
# Port
mongod --port 27018

# Data directory
mongod --dbpath /custom/path

# Replica set
mongod --replSet "rs0"

# Bind to all interfaces
mongod --bind_ip 0.0.0.0  # Not recommended for production

# Verbose logging
mongod --verbose --logpath /var/log/mongod.log

# Foreground (don't daemonize)
mongod --nofork
```

### **Verify Server is Running**

```bash
# Check if listening on port
netstat -an | grep 27017  # Linux/macOS
netstat -ano | grep 27017  # Windows

# Try connecting
mongosh

# Should connect without error and show:
# test>
```

---

## GitHub Resources

- **MongoDB Installation Guide**: [mongodb/docs-installation](https://github.com/mongodb/docs)
- **MongoDB Community**: [mongodb/mongo](https://github.com/mongodb/mongo)
- **Docker MongoDB**: [docker-library/mongo](https://github.com/docker-library/mongo)
- **MongoDB Configuration**: [mongodb/server-docs](https://docs.mongodb.com/manual/reference/configuration-file-settings/)

---

Navigation: [← Chapter 4: MongoDB Architecture](../ch04-mongodb-architecture/README.md) | [↑ Index](../../index.md) | [→ Chapter 6: Basic Database Operations](../ch06-basic-database-operations/README.md)
