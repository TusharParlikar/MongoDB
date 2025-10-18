# Chapter 24: MongoDB Atlas

## 24.1 What is MongoDB Atlas?

MongoDB's managed cloud database service. No server administration needed.

---

## 24.2 Cloud Database Service

- Hosted on AWS, Google Cloud, Azure
- Auto backups & recovery
- Built-in replication & sharding
- Monitoring & alerts
- Free and paid tiers

---

## 24.3 Setting up Atlas Account

1. Go to [mongodb.com/cloud/atlas](https://www.mongodb.com/cloud/atlas)
2. Sign up / Log in
3. Create organization & project
4. Build database cluster

---

## 24.4 Creating Clusters

```
Atlas Dashboard →
  Build Database →
    Select Shared (Free) or Dedicated →
    Choose Region →
    Create Cluster →
    Create Database User →
    Add IP Whitelist →
    Connect
```

---

## 24.5 Connecting to Atlas

```javascript
const uri = "mongodb+srv://username:password@cluster.mongodb.net/mydb";
const client = new MongoClient(uri);
await client.connect();
```

Connection String from Atlas:
```
mongodb+srv://<username>:<password>@<cluster>.mongodb.net/mydb?retryWrites=true&w=majority
```

---

## 24.6 Free Tier vs Paid Plans

| Feature | Free | Paid |
|---------|------|------|
| Storage | 512MB | Unlimited |
| Connections | Shared | Dedicated |
| Backup | Snapshot | Continuous |
| Support | Community | Premium |
| Replication | Yes | Yes |

---

Navigation: [← Chapter 23: Import/Export Data](../ch23-import-export-data/README.md) | [↑ Index](../../index.md) | [→ Chapter 25: MongoDB Compass](../ch25-mongodb-compass/README.md)

