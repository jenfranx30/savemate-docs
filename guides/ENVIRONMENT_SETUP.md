# Environment Setup Guide

## Backend Environment Variables

Create `.env` file in backend root:

```env
# Database
MONGODB_URL=mongodb+srv://user:pass@cluster.mongodb.net/savemate

# Security
SECRET_KEY=your-super-secret-key-min-32-chars
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30

# Cloudinary (Image Upload)
CLOUDINARY_CLOUD_NAME=your-cloud-name
CLOUDINARY_API_KEY=your-api-key
CLOUDINARY_API_SECRET=your-api-secret

# CORS
FRONTEND_URL=http://localhost:5173
```

## Frontend Environment Variables

Create `.env` file in frontend root:

```env
# API URL
VITE_API_BASE_URL=http://localhost:8000

# Cloudinary
VITE_CLOUDINARY_CLOUD_NAME=your-cloud-name

# Google Maps (optional)
VITE_GOOGLE_MAPS_API_KEY=your-api-key
```

## Production Environment Variables

### Vercel (Frontend)
```env
VITE_API_BASE_URL=https://savemate-backend.onrender.com
```

### Render (Backend)
```env
MONGODB_URL=<production-mongodb-url>
SECRET_KEY=<production-secret-key>
FRONTEND_URL=https://savemate-frontend.vercel.app
```
