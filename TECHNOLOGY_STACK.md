# SaveMate Technology Stack Documentation

**Project:** SaveMate - Local Deals and Discounts Platform   
**Date Finalized:** November 19, 2025  
**Decision Method:** Team research and decisions

---

##  Table of Contents

1. [Frontend Stack](#frontend-stack)
2. [Backend Stack](#backend-stack)
3. [Cloud Services and APIs](#cloud-services--apis)
4. [Authentication and Security](#authentication--security)
5. [Development Tools](#development-tools)
6. [Deployment and Hosting](#deployment--hosting)
7. [Package Management](#package-management)
8. [Future Considerations](#future-considerations)
9. [Technology Justification Matrix](#technology-justification-matrix)
10. [Decision Timeline](#decision-timeline)
11. [References and Resources](#references--resources)

---

## Frontend Stack

### Framework: React.js 18.x

**Chosen over:** Vue.js, Angular

**Reason:**
- Largest ecosystem with 220k+ npm packages
- Strong component reusability for deal cards, business profiles
- Extensive community support for 7-week timeline
- Team familiarity with JavaScript
- Industry-standard for similar applications

**Key Benefits for SaveMate:**
- Reusable components (DealCard, BusinessCard, UserProfile)
- Fast development with ready-made libraries
- Excellent mobile performance
- Strong React Router for SPA navigation

**Installation:**
```bash
npx create-react-app savemate-frontend
cd savemate-frontend
npm install react-router-dom axios
```

---

### Styling: Tailwind CSS 3.x

**Chosen over:** Bootstrap, Material-UI

**Reason:**
- Custom design without "Bootstrap look"
- Smaller bundle size (5-10KB vs 150KB)
- Utility-first approach speeds development 30%
- Perfect integration with React components
- Mobile-first responsive design

**Key Benefits for SaveMate:**
- Quick prototyping for 7-week deadline
- Consistent design system
- Highly customizable deal cards
- Excellent mobile responsiveness
- No CSS file switching

**Installation:**
```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

**Configuration Example:**
```javascript
// tailwind.config.js
module.exports = {
  content: [
    "./src/**/*.{js,jsx,ts,tsx}",
  ],
  theme: {
    extend: {
      colors: {
        primary: '#3B82F6',
        secondary: '#10B981',
        accent: '#F59E0B',
      },
    },
  },
  plugins: [],
}
```

---

### State Management: React Context API

**Chosen over:** Redux, Zustand

**Reason:**
- Built into React (no extra library)
- Sufficient for SaveMate's scope
- Simpler learning curve
- Faster implementation

**Use Cases:**
- User authentication state
- Current location data
- Shopping cart/saved deals
- Theme preferences

**Context Structure:**
```javascript
// contexts/
├── AuthContext.js      // User authentication state
├── LocationContext.js  // User location for "Near Me"
├── ThemeContext.js     // Dark/light mode
└── FavoritesContext.js // Saved deals
```

---

### Routing: React Router v6

**Chosen over:** Next.js routing

**Reason:**
- Standard SPA routing solution
- Easy to implement
- Good documentation
- Team familiarity

**Routes:**
- `/` - Homepage with deals
- `/deals/:id` - Deal details
- `/business/dashboard` - Business panel
- `/profile` - User profile
- `/login` - Login page
- `/register` - Registration page
- `/favorites` - Saved deals
- `/search` - Search results

**Installation:**
```bash
npm install react-router-dom
```

---

## Backend Stack

### Runtime: Node.js 18+ LTS

**Chosen over:** Python, PHP

**Reason:**
- JavaScript full-stack (same language frontend/backend)
- Excellent for JSON APIs
- Large package ecosystem (npm)
- Team JavaScript expertise
- Non-blocking I/O for handling multiple requests

**Installation:**
Download from: https://nodejs.org/

**Verify Installation:**
```bash
node --version  # Should show v18.x.x or higher
npm --version
```

---

### Framework: Express.js 4.x

**Chosen over:** Nest.js, Fastify

**Reason:**
- Minimalist and flexible
- Extensive middleware ecosystem
- Excellent documentation
- Quick to set up REST APIs
- Team familiarity

**API Structure:**
```
/api/auth       - Authentication endpoints
  POST /register
  POST /login
  POST /logout
  POST /refresh-token

/api/users      - User management
  GET /profile
  PUT /profile
  POST /favorites/:dealId
  DELETE /favorites/:dealId
  GET /favorites

/api/deals      - Deals CRUD operations
  GET /          (list all deals with pagination)
  GET /:id       (get single deal)
  POST /         (create deal - business only)
  PUT /:id       (update deal - business only)
  DELETE /:id    (delete deal - business only)
  GET /search    (search deals)

/api/business   - Business management
  POST /register
  GET /:id
  PUT /:id
  GET /:id/deals
  POST /verify
```

**Installation:**
```bash
mkdir savemate-backend
cd savemate-backend
npm init -y
npm install express cors dotenv helmet
npm install --save-dev nodemon
```

---

### Database: MongoDB Atlas

**Chosen over:** PostgreSQL, MySQL

**Reason:**
- NoSQL flexibility for evolving schema
- JSON-like documents match JavaScript
- Free cloud hosting (Atlas)
- Excellent with Node.js (Mongoose)
- Fast prototyping

**Collections:**
```
users {
  _id: ObjectId
  name: String
  email: String (unique, indexed)
  password: String (hashed)
  role: String (enum: ['user', 'business', 'admin'])
  favorites: [ObjectId] (references to deals)
  createdAt: Date
  updatedAt: Date
}

deals {
  _id: ObjectId
  title: String
  description: String
  discount: Number
  category: String
  location: {
    address: String
    coordinates: [Number] (lat, lng)
  }
  images: [String] (Cloudinary URLs)
  business: ObjectId (reference to businesses)
  expiryDate: Date
  status: String (enum: ['active', 'expired'])
  views: Number
  saves: Number
  createdAt: Date
  updatedAt: Date
}

businesses {
  _id: ObjectId
  name: String
  description: String
  owner: ObjectId (reference to users)
  address: String
  contact: {
    phone: String
    email: String
    website: String
  }
  location: {
    coordinates: [Number]
  }
  hours: String
  categories: [String]
  verified: Boolean
  logo: String (Cloudinary URL)
  createdAt: Date
  updatedAt: Date
}

categories {
  _id: ObjectId
  name: String (unique)
  icon: String
  description: String
}
```

**Setup:**
1. Create account at https://www.mongodb.com/cloud/atlas
2. Create free M0 cluster (512MB)
3. Whitelist IP address (0.0.0.0/0 for development)
4. Create database user
5. Get connection string

---

### ODM: Mongoose 7.x

**Chosen over:** Native MongoDB driver

**Reason:**
- Schema validation
- Easier data modeling
- Built-in middleware hooks
- Better error handling
- TypeScript support (if needed later)

**Installation:**
```bash
npm install mongoose
```

**Connection Example:**
```javascript
// config/database.js
const mongoose = require('mongoose');

const connectDB = async () => {
  try {
    await mongoose.connect(process.env.MONGODB_URI, {
      useNewUrlParser: true,
      useUnifiedTopology: true,
    });
    console.log('MongoDB Connected...');
  } catch (err) {
    console.error(err.message);
    process.exit(1);
  }
};

module.exports = connectDB;
```

---

## Cloud Services & APIs

### Image Storage: Cloudinary

**Chosen over:** AWS S3, Firebase Storage

**Reason:**
- Generous free tier (25 credits/month)
- Automatic image optimization
- Easy React integration
- Image transformations included
- Simple API
- No credit card required for free tier

**Usage:**
- Deal images (multiple per deal)
- Business logos
- User profile pictures
- Automatic resizing for mobile (responsive images)

**Free Tier Limits:**
- 25 credits/month
- 25GB storage
- 25GB bandwidth
- Image transformations included

**Installation:**
```bash
npm install cloudinary multer
```

**Configuration:**
```javascript
// config/cloudinary.js
const cloudinary = require('cloudinary').v2;

cloudinary.config({
  cloud_name: process.env.CLOUDINARY_CLOUD_NAME,
  api_key: process.env.CLOUDINARY_API_KEY,
  api_secret: process.env.CLOUDINARY_API_SECRET
});

module.exports = cloudinary;
```

---

### Maps: React Leaflet (OpenStreetMap)

**Chosen over:** Google Maps, Mapbox

**Reason:**
- **Zero cost** (critical for student project)
- No API keys or billing required
- Sufficient features for SaveMate
- Excellent React integration
- Lightweight (40KB)
- Open source community support

**Features:**
- Business location markers
- "Near Me" functionality
- Custom deal markers
- Directions integration
- Mobile touch support
- Clustering for multiple nearby deals

**Trade-offs Accepted:**
- Simpler aesthetics than Google Maps
- No 3D buildings or Street View
- Basic routing only
- Acceptable for MVP and academic demonstration

**Installation:**
```bash
npm install react-leaflet leaflet
```

**Basic Implementation:**
```javascript
import { MapContainer, TileLayer, Marker, Popup } from 'react-leaflet';
import 'leaflet/dist/leaflet.css';

<MapContainer center={[lat, lng]} zoom={13}>
  <TileLayer
    url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
    attribution='&copy; OpenStreetMap contributors'
  />
  <Marker position={[lat, lng]}>
    <Popup>Business Location</Popup>
  </Marker>
</MapContainer>
```

---

## Authentication & Security

### Authentication: JWT (JSON Web Tokens)

**Implementation Details:**
- Token-based authentication
- 7-day access token expiration
- 30-day refresh token expiration
- Refresh token support
- Stored in localStorage (access token) and httpOnly cookies (refresh token)

**Installation:**
```bash
npm install jsonwebtoken
```

**Token Structure:**
```javascript
{
  id: user._id,
  email: user.email,
  role: user.role,
  iat: issued_at_timestamp,
  exp: expiration_timestamp
}
```

---

### Password Security: bcrypt.js

**Implementation:**
- Password hashing with salt rounds: 10
- Secure password comparison
- Never store plain text passwords

**Installation:**
```bash
npm install bcryptjs
```

**Usage:**
```javascript
const bcrypt = require('bcryptjs');

// Hash password
const salt = await bcrypt.genSalt(10);
const hashedPassword = await bcrypt.hash(password, salt);

// Compare password
const isMatch = await bcrypt.compare(password, hashedPassword);
```

---

### Security Measures

**Packages to Install:**
```bash
npm install helmet cors express-rate-limit express-validator
```

**Security Implementation:**

1. **Helmet.js** - HTTP headers security
   ```javascript
   const helmet = require('helmet');
   app.use(helmet());
   ```

2. **CORS Configuration** - Cross-Origin Resource Sharing
   ```javascript
   const cors = require('cors');
   app.use(cors({
     origin: process.env.FRONTEND_URL,
     credentials: true
   }));
   ```

3. **Rate Limiting** - Prevent brute force attacks
   ```javascript
   const rateLimit = require('express-rate-limit');
   const limiter = rateLimit({
     windowMs: 15 * 60 * 1000, // 15 minutes
     max: 100 // limit each IP to 100 requests per windowMs
   });
   app.use('/api/', limiter);
   ```

4. **Input Validation** - Prevent injection attacks
   ```javascript
   const { check, validationResult } = require('express-validator');
   ```

5. **XSS Protection** - Already included in Helmet

6. **Environment Variables** - Secure sensitive data
   ```javascript
   require('dotenv').config();
   ```

---

## Development Tools

### Version Control: Git & GitHub

**Repository Structure:**
```
savemate-app/
├── frontend/          (React application)
├── backend/           (Node.js/Express API)
├── .gitignore
├── README.md
└── TECHNOLOGY_STACK.md (this document)
```

**Branching Strategy:**
- `main` - Production-ready code
- `dev` - Development branch
- `feature/*` - Feature branches
- `hotfix/*` - Emergency fixes

**Workflow:**
1. Create feature branch from `dev`
2. Develop feature
3. Create pull request
4. Code review by team member
5. Merge to `dev`
6. Deploy `dev` to staging
7. Merge `dev` to `main` for production

**GitHub Repository:**
- Repository name: `savemate-app`
- Public/Private: Private (for development)
- Collaborators: All 5 team members

---

### Code Editor: VS Code

**Recommended Extensions:**
- ESLint - Code linting
- Prettier - Code formatting
- ES7+ React/Redux/React-Native snippets
- Tailwind CSS IntelliSense
- MongoDB for VS Code
- GitLens - Git supercharged

**Settings:**
```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "prettier.semi": true,
  "prettier.singleQuote": true
}
```

---

### API Testing: Postman

**Setup:**
- Create workspace: "SaveMate API"
- Create collections for each endpoint group
- Save environment variables
- Create test scripts

**Collections Structure:**
```
SaveMate API/
├── Auth Endpoints
│   ├── Register User
│   ├── Login User
│   ├── Logout User
│   └── Refresh Token
├── User Endpoints
├── Deals Endpoints
└── Business Endpoints
```

**Environment Variables:**
```
BASE_URL: http://localhost:5000/api
TOKEN: {{auth_token}}
```

---

### Project Management: Trello (Kanban)

**Board Structure:**
- BACKLOG
- TO DO
- IN PROGRESS (WIP: 3)
- REVIEW
- TESTING
- DONE
- ARCHIVE

**Labels:**
- Setup (green)
- High Priority (red)
- Research (blue)
- Frontend (purple)
- Backend (orange)
- Bug (red)
- Enhancement (yellow)

**Card Format:**
```
Card Title: Clear, actionable task
Due Date: Set for all cards
Members: Assigned team members
Labels: Relevant labels
Description: Detailed requirements
Checklist: Sub-tasks
Attachments: Related files
```

---

## Deployment & Hosting

### Frontend: Vercel

**Chosen over:** Netlify, GitHub Pages

**Reason:**
- Free for hobby projects
- Automatic deployments from GitHub
- Excellent performance (Edge Network)
- Easy custom domains
- Built-in analytics
- Zero configuration for React

**Deployment Process:**
1. Connect GitHub repository
2. Select `frontend` directory
3. Set build command: `npm run build`
4. Set output directory: `build`
5. Auto-deploy on push to `main`

**Environment Variables:**
```
REACT_APP_API_URL=https://savemate-api.onrender.com
REACT_APP_CLOUDINARY_CLOUD_NAME=your_cloud_name
```

**URL:** savemate.vercel.app

---

### Backend: Render (or Railway)

**Chosen over:** Heroku, AWS

**Reason:**
- Free tier available
- Automatic deployments from GitHub
- Easy environment variables
- Good documentation
- No credit card required for free tier
- Built-in HTTPS
- Auto-sleep after inactivity (acceptable for student project)

**Deployment Process:**
1. Connect GitHub repository
2. Select `backend` directory
3. Set start command: `npm start`
4. Configure environment variables
5. Auto-deploy on push to `main`

**Environment Variables:**
```
NODE_ENV=production
MONGODB_URI=mongodb+srv://...
JWT_SECRET=your_secret_key
JWT_REFRESH_SECRET=your_refresh_secret
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret
FRONTEND_URL=https://savemate.vercel.app
```

**Free Tier Limitations:**
- 750 hours/month (sufficient for 1 service)
- Auto-sleep after 15 minutes inactivity
- 512MB RAM
- Shared CPU

**URL:** savemate-api.onrender.com

---

### Database: MongoDB Atlas

**Cluster Configuration:**
- Tier: M0 (Free)
- Provider: AWS
- Region: Closest to deployment server
- Storage: 512MB (sufficient for development)

**Connection String:**
```
mongodb+srv://username:password@cluster.mongodb.net/savemate?retryWrites=true&w=majority
```

**Security:**
- Database user with restricted permissions
- IP whitelist: 0.0.0.0/0 (all IPs for development)
- Connection string in environment variable (never commit to Git)

---

## Package Management

### npm (Node Package Manager)

**Reason for npm over yarn:**
- Standard for Node.js projects
- Extensive package registry (2M+ packages)
- Good dependency management
- Team familiarity
- Package-lock.json for consistency

**Essential Commands:**
```bash
npm install          # Install all dependencies
npm install <pkg>    # Install package
npm install -D <pkg> # Install dev dependency
npm start            # Start development server
npm run build        # Build for production
npm test             # Run tests
npm audit            # Check security vulnerabilities
npm update           # Update packages
```

**Package.json Scripts (Backend Example):**
```json
{
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js",
    "test": "jest",
    "seed": "node scripts/seed.js"
  }
}
```

---

## Future Considerations

**Not Implemented (but considered):**

### 1. TypeScript
**Why not now:** Would add type safety but increases complexity and setup time  
**Why later:** Better for large-scale applications with multiple developers  
**Decision:** Deferred to post-MVP if needed

### 2. Redux
**Why not now:** Overkill for SaveMate's state management needs  
**Why later:** Needed if state becomes very complex (50+ states)  
**Decision:** Context API is sufficient for our 5 main state contexts

### 3. Next.js
**Why not now:** Provides SSR and better SEO, but adds complexity  
**Why later:** If SEO becomes critical or need server-side rendering  
**Decision:** SaveMate is primarily a web app, not a content site needing SEO

### 4. GraphQL
**Why not now:** More efficient than REST, but REST APIs are simpler to implement  
**Why later:** If we need more flexible data queries  
**Decision:** REST APIs are sufficient within our 7-week constraint

### 5. Docker
**Why not now:** Great for deployment consistency, but adds DevOps complexity  
**Why later:** For production deployment with microservices  
**Decision:** Beyond our course scope, Vercel/Render handle deployment

### 6. Testing Frameworks
**Status:** Jest and React Testing Library planned for Week 6  
**Coverage goal:** 60% code coverage minimum  
**Types:** Unit tests, integration tests, E2E tests

### 7. CI/CD Pipeline
**Why not now:** Manual deployments sufficient for 7-week project  
**Why later:** For automated testing and deployment  
**Decision:** GitHub Actions could be added in Week 7 if time permits

---

## Technology Justification Matrix

| Technology | Learning Curve | Development Speed | Community Support | Cost | Total Score |
|------------|----------------|-------------------|-------------------|------|-------------|
| React | Medium | Fast | Excellent | Free | 9/10 |
| Tailwind | Easy | Very Fast | Good | Free | 9/10 |
| Node.js | Easy | Fast | Excellent | Free | 10/10 |
| Express | Easy | Very Fast | Excellent | Free | 10/10 |
| MongoDB | Easy | Fast | Excellent | Free | 10/10 |
| React Leaflet | Medium | Medium | Good | Free | 8/10 |
| Cloudinary | Easy | Fast | Good | Free tier | 9/10 |

**Average Score:** 9.1/10

---

**Skill Gaps Identified:**
- Tailwind CSS (all members have knowledge)
- React Leaflet (all members have knowledge)
- Cloudinary integration (some members have knowledge)

**Mitigation Strategy:**
- Pair programming for new technologies
- Online tutorials and documentation review
- Weekly knowledge sharing sessions
- Code reviews to share best practices
- Internal documentation for common patterns

---

## Decision Timeline

### November 14-17, 2025: Research Phase
**Activities:**
- Individual research on technology alternatives
- Pros/cons documentation for each option
- Cost analysis (critical for each team member's budget)
- Community support evaluation
- Learning curve assessment

**Research Deliverables:**
- React vs Vue vs Angular comparison
- Tailwind vs Bootstrap analysis
- Map solutions comparison (Google Maps, Mapbox, React Leaflet)
- Database options evaluation (MongoDB, PostgreSQL)
- Hosting providers comparison

---

**Discussion Points:**
- Reviewed all research findings
- Voted on each technology choice
- Discussed budget constraints
- Evaluated team skills and preferences
- Reached consensus on full stack

**Decisions Made:**
- Frontend: React + Tailwind + Context API
- Backend: Node.js + Express + MongoDB
- Maps: React Leaflet (zero cost!)
- Images: Cloudinary (free tier)
- Deployment: Vercel + Render

---

### November 19, 2025: Documentation Complete
**Activities:**
- Created this TECHNOLOGY_STACK.md document
- Shared with all assigned team members via GitHub
- Added to project repository
- Reviewed and approved by team leader

---

## References and Resources

### React
- **Official React Documentation:** https://react.dev
- **React Router:** https://reactrouter.com
- **Book:** "Learning React" by Alex Banks & Eve Porcello
- **Tutorial:** React Beta Documentation (react.dev)

### Tailwind CSS
- **Official Documentation:** https://tailwindcss.com
- **Tailwind UI Components:** https://tailwindui.com
- **Article:** "Tailwind CSS Best Practices" - LogRocket
- **Video:** "Tailwind CSS Crash Course" by Traversy Media

### Node.js and Express
- **Node.js Documentation:** https://nodejs.org
- **Express.js Guide:** https://expressjs.com
- **Book:** "Node.js Design Patterns" by Mario Casciaro
- **Tutorial:** "Node.js Crash Course" by Traversy Media

### MongoDB
- **MongoDB University:** https://university.mongodb.com
- **Mongoose Documentation:** https://mongoosejs.com
- **Course:** "MongoDB for JavaScript Developers" (Free)
- **Guide:** "MongoDB Schema Design Best Practices"

### React Leaflet
- **Official Documentation:** https://react-leaflet.js.org
- **OpenStreetMap:** https://openstreetmap.org
- **Tutorial:** "React Leaflet Tutorial" by Leigh Halliday
- **Examples:** React Leaflet GitHub Examples

### Cloudinary
- **Cloudinary Documentation:** https://cloudinary.com/documentation
- **React SDK Guide:** Cloudinary React Integration
- **Video:** "Cloudinary Image Upload in React"
- **Blog:** "Optimizing Images with Cloudinary"

### Authentication and Security
- **JWT Documentation:** https://jwt.io
- **Article:** "JWT Authentication Best Practices"
- **Guide:** "Securing Node.js Applications"
- **OWASP Top 10:** https://owasp.org/www-project-top-ten/

### Deployment
- **Vercel Documentation:** https://vercel.com/docs
- **Render Documentation:** https://render.com/docs
- **MongoDB Atlas Setup:** Atlas Documentation
- **Guide:** "Deploying MERN Stack Applications"

---

## Approval and Sign-off

**Approved by:** All Team Members (JY, RI, MR, RY, SS)  
**Date:** November 19, 2025   
**Next Review:** If issues arise during development

---

## Document Maintenance

**Version:** 1.0  
**Last Updated:** November 19, 2025  
**Status:** FINALIZED

**Change Log:**
- v1.0 (Nov 19, 2025) - Initial document created with all finalized decisions

**Future Updates:**
- If technology changes needed, create v1.1 with clear justification
- Document any package version updates
- Add lessons learned section after project completion

---

## Quick Reference

###  Package Installation Commands

**Frontend:**
```bash
npx create-react-app savemate-frontend
cd savemate-frontend
npm install react-router-dom axios react-leaflet leaflet
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

**Backend:**
```bash
mkdir savemate-backend
cd savemate-backend
npm init -y
npm install express mongoose dotenv cors helmet bcryptjs jsonwebtoken
npm install cloudinary multer express-rate-limit express-validator
npm install --save-dev nodemon
```

---

###  Essential Configuration Files

**`.env` (Backend - DO NOT COMMIT):**
```
NODE_ENV=development
PORT=5000
MONGODB_URI=your_mongodb_connection_string
JWT_SECRET=your_super_secret_key_min_32_chars
JWT_REFRESH_SECRET=your_refresh_secret_key
JWT_EXPIRE=7d
JWT_REFRESH_EXPIRE=30d
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret
FRONTEND_URL=http://localhost:3000
```

**`.env` (Frontend - DO NOT COMMIT):**
```
REACT_APP_API_URL=http://localhost:5000/api
REACT_APP_CLOUDINARY_CLOUD_NAME=your_cloud_name
```

**`.gitignore`:**
```
node_modules/
.env
.env.local
.env.production
build/
dist/
.DS_Store
npm-debug.log*
yarn-debug.log*
yarn-error.log*
```

---

###  Development Commands

**Start Frontend:**
```bash
cd frontend
npm start          # Runs on http://localhost:3000
```

**Start Backend:**
```bash
cd backend
npm run dev        # Runs on http://localhost:5000 with nodemon
```

**Run Both Concurrently:**
```bash
# Option 1: Two terminals
Terminal 1: cd frontend && npm start
Terminal 2: cd backend && npm run dev

# Option 2: Install concurrently
npm install -g concurrently
concurrently "cd frontend && npm start" "cd backend && npm run dev"
```

---

**End of Document**

*This document serves as the authoritative reference for all technology decisions in the SaveMate project. All team members should refer to this document when implementing features or making technology-related decisions.*

---

**Contact Information:**
For questions about this document, contact the team lead or post in the team group chat channel.

**Last Reviewed:** November 19, 2025  
**Next Review:** End of Week 7 (Post-project retrospective)
