# Chapter 31: Deployment & Hosting

## 31.1 Deployment Options

- **VPS** (Virtual Private Server): Full control, Hostinger, DigitalOcean
- **PaaS** (Platform as a Service): Heroku (shutting down), Render, Railway
- **Container**: Docker + Kubernetes, AWS ECS

---

## 31.2 Hostinger VPS Setup

```bash
# SSH into server
ssh root@your_ip

# Update system
apt-get update && apt-get upgrade -y

# Install Node.js
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
apt-get install -y nodejs

# Install MongoDB or use Atlas
apt-get install -y mongodb-org
```

---

## 31.3 Environment Variables

```bash
# .env file
MONGODB_URI=mongodb+srv://user:pass@cluster.mongodb.net/dbname
NODE_ENV=production
PORT=5000
JWT_SECRET=your-secret-key
```

```javascript
require('dotenv').config();
const mongoUri = process.env.MONGODB_URI;
```

---

## 31.4 Deploy Frontend (React Build)

```bash
# Build React app
npm run build

# Serve static files with Express
app.use(express.static('build'));
app.get('*', (req, res) => res.sendFile('build/index.html'));
```

---

## 31.5 Deploy Backend (PM2)

```bash
# Install PM2
npm install -g pm2

# Create app.config.js
module.exports = {
  apps: [{
    name: 'mern-app',
    script: './server.js',
    env: { NODE_ENV: 'production' },
    instances: 'max',
    exec_mode: 'cluster'
  }]
};

# Start with PM2
pm2 start app.config.js
pm2 save
```

---

## 31.6 Connect to Production Database

```javascript
// Use MongoDB Atlas (cloud)
const uri = process.env.MONGODB_URI || 'mongodb://localhost:27017/app';
mongoose.connect(uri, {
  useNewUrlParser: true,
  useUnifiedTopology: true
});
```

---

## 31.7 Production Deployment Checklist

- ✅ Use HTTPS (SSL certificate)
- ✅ Set NODE_ENV=production
- ✅ Hide sensitive data in .env
- ✅ Use connection pooling
- ✅ Enable database backups
- ✅ Monitor error logs
- ✅ Set up reverse proxy (Nginx)

---

GitHub: [github.com/topics/mern-deployment](https://github.com/topics/mern-deployment)

---

Navigation: [← Chapter 30: Project - Contact Form](../ch30-project-contact-form/README.md) | [↑ Index](../../index.md) | [→ Chapter 32: Best Practices & Security](../ch32-best-practices-security/README.md)
