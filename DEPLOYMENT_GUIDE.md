# SaveMate Deployment Guide

## Quick Deployment Checklist

### Frontend (Vercel)
1. Push code to GitHub
2. Connect Vercel to repository
3. Set environment variables:
   ```
   VITE_API_BASE_URL=https://savemate-backend-deployment1.onrender.com
   ```
4. Deploy

### Backend (Render)
1. Push code to GitHub
2. Connect Render to repository
3. Set environment variables:
   ```
   MONGODB_URL=mongodb+srv://...(provided upon request)
   SECRET_KEY=your-secret-key(provided upon request)
   CLOUDINARY_*=your-credentials(provided upon request)
   ```
4. Deploy

### Database (MongoDB Atlas)
1. Create cluster (M0 free tier)
2. Create database user
3. Whitelist IP addresses
4. Copy connection string

See [DEPLOYMENT_REPORT.md] for detailed deployment analysis.
