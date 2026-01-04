# Database Setup Guide

## MongoDB Atlas Setup

### 1. Create Cluster
1. Go to https://mongodb.com/cloud/atlas
2. Create free M0 cluster
3. Choose region closest to backend

### 2. Create Database User
1. Database Access → Add New User
2. Username: `savemate_user`
3. Password: Generate strong password
4. Role: Read and write to any database

### 3. Network Access
1. Network Access → Add IP Address
2. For development: 0.0.0.0/0 (allow all)
3. For production: Add specific IPs

### 4. Connection String
```
mongodb+srv://savemate_user:<password>@cluster0.xxxxx.mongodb.net/savemate?retryWrites=true&w=majority
```

## Local MongoDB (Optional)
```bash
# Install MongoDB
brew install mongodb-community  # macOS
# or download from mongodb.com

# Start MongoDB
mongod --dbpath ~/data/db

# Connection string
mongodb://localhost:27017/savemate
```

See [DATABASE_SCHEMA.md](../DATABASE_SCHEMA.md) for collection structure.
