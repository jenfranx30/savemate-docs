# SaveMate - Updated Trello Tasks Documentation
## Python/FastAPI + React + MongoDB Stack

---

## TASK 1: Selecting Technologies ✅ COMPLETE

**Status**: Complete (Nov 22, 2025)
**Priority**: High
**Labels**: Setup, High Priority, Research

### FINAL DECISION on technology stack for SaveMate project:

#### **STACK ARCHITECTURE**:
**Full Stack**: React + FastAPI + MongoDB (Modern Python Web Stack)

#### **FRONTEND**:
- **Framework**: React 18
- **Routing**: React Router v6
- **State Management**: Context API (with option for Redux Toolkit if needed)
- **Styling**: Tailwind CSS
- **HTTP Client**: Axios
- **Map Integration**: React Leaflet
- **Image Storage**: Cloudinary
- **Build Tool**: Vite
- **Package Manager**: npm or yarn

#### **BACKEND**:
- **Language**: Python 3.11+
- **Framework**: FastAPI
- **Database**: MongoDB
- **ODM**: Motor (async MongoDB driver) + Beanie (ODM)
- **Authentication**: JWT (JSON Web Tokens) with python-jose
- **Password Hashing**: Passlib with bcrypt
- **File Upload**: FastAPI UploadFile + Cloudinary Python SDK
- **API Style**: RESTful API with automatic OpenAPI documentation
- **CORS**: FastAPI CORS middleware
- **Validation**: Pydantic models (built-in with FastAPI)
- **Environment**: python-dotenv for configuration

#### **DEVELOPMENT TOOLS**:
- **API Testing**: FastAPI automatic Swagger UI at /docs
- **Version Control**: Git + GitHub
- **Code Formatting**: Black (Python), Prettier (JavaScript)
- **Linting**: Pylint/Flake8 (Python), ESLint (JavaScript)

#### **DEPLOYMENT**:
- **Backend**: Render / Railway / Heroku (Python support)
- **Frontend**: Vercel / Netlify
- **Database**: MongoDB Atlas (cloud)
- **Image Storage**: Cloudinary

### CHECKLIST COMPLETED:
✅ Research React vs Vue vs Angular
✅ Research Tailwind vs Bootstrap
✅ Research map options
✅ Team member votes
✅ Final decision documented

### RESEARCH DOCUMENT:
Attached: "RESEARCH ANALYSIS FOR COMPLETED DECISIONS.pdf"

---

## TASK 2: Set Up Project Repositories and Folder Structure ✅ COMPLETE

**Status**: Complete (100%)
**Priority**: High
**Labels**: Setup, DevOps
**Due Date**: Nov 25, 2025

### TASK DESCRIPTION:
Initialize GitHub repositories and create complete folder structure for SaveMate frontend (React) and backend (Python/FastAPI) applications.

Establish organized, production-ready project structure that supports efficient team collaboration and follows industry best practices for React + FastAPI + MongoDB stack development.

### GITHUB REPOSITORIES (3 total):

1. **savemate-frontend** - React 18 application repository
   - Repository URL: [GitHub link]
   - Tech: React 18, Vite, Tailwind CSS, React Router v6

2. **savemate-backend** - Python/FastAPI API repository
   - Repository URL: [GitHub link]
   - Tech: Python 3.11+, FastAPI, Motor, Beanie, MongoDB

3. **savemate-docs** - Project documentation
   - Repository URL: [GitHub link]
   - Contains: Architecture docs, API specs, meeting notes

### INITIAL SETUP COMPLETED:
✅ All 3 repositories created and accessible
✅ All dependencies installed and working
✅ Both projects run successfully locally
✅ Repository links shared to assigned team members
✅ Documentation complete

### LABELS:
- DevOps
- Setup

### PRIORITY: High

### CHECKLIST:
✅ All 3 repositories created and accessible
✅ All dependencies installed and working
✅ Both projects run successfully locally
✅ Share repository links to assigned team members
✅ Documentation complete

### ATTACHMENTS:
- GitHub repository links (3)
- Screenshot: "week 1 installing dependencies complete.bmp"

---

## TASK 3: Designing the Project Architecture (Frontend + Backend) 🔄 IN PROGRESS

**Status**: In Progress (29% complete)
**Priority**: High
**Labels**: High Priority, Backend, Frontend, Documentation
**Due Date**: Nov 25, 2025
**Assigned**: RI, MR, J

### TASK DESCRIPTION:
Design comprehensive architecture for SaveMate application covering both frontend (React) and backend (Python/FastAPI) components, including database schema, API structure, and project organization.

---

## ARCHITECTURE COMPONENTS:

### **FRONTEND ARCHITECTURE**:

#### Core Technologies:
- **Framework**: React 18 (with Hooks)
- **Routing**: React Router v6
- **State Management**: Context API / Redux Toolkit
- **Styling**: Tailwind CSS with custom configuration
- **HTTP Client**: Axios with interceptors
- **Maps**: React Leaflet for interactive maps
- **Image Display**: Cloudinary with React integration
- **Form Handling**: React Hook Form + Yup validation
- **Date Handling**: date-fns or dayjs
- **Notifications**: React Toastify

#### Folder Structure:
```
savemate-frontend/
├── src/
│   ├── components/          # Reusable UI components
│   │   ├── common/         # Buttons, Cards, Modals
│   │   ├── deals/          # Deal-related components
│   │   ├── map/            # Map components
│   │   └── layout/         # Header, Footer, Navigation
│   ├── pages/              # Route-level components
│   │   ├── Home.jsx
│   │   ├── DealsList.jsx
│   │   ├── DealDetails.jsx
│   │   ├── Profile.jsx
│   │   └── Auth/
│   ├── context/            # Context API providers
│   │   ├── AuthContext.jsx
│   │   ├── DealContext.jsx
│   │   └── ThemeContext.jsx
│   ├── hooks/              # Custom React hooks
│   ├── services/           # API service functions
│   │   ├── api.js          # Axios configuration
│   │   ├── authService.js
│   │   └── dealService.js
│   ├── utils/              # Utility functions
│   ├── assets/             # Images, icons, fonts
│   ├── styles/             # Global CSS, Tailwind config
│   ├── App.jsx
│   └── main.jsx
├── public/
├── .env.example
├── vite.config.js
├── tailwind.config.js
└── package.json
```

---

### **BACKEND ARCHITECTURE**:

#### Core Technologies:
- **Language & Runtime**: Python 3.11+
- **Framework**: FastAPI 0.104+
- **Database**: MongoDB 7.0+
- **Async Driver**: Motor (async MongoDB driver)
- **ODM**: Beanie (async Document ODM)
- **Authentication**: JWT with python-jose[cryptography]
- **Password Hashing**: Passlib[bcrypt]
- **File Upload**: FastAPI UploadFile + Cloudinary Python SDK
- **Validation**: Pydantic v2 (built-in with FastAPI)
- **CORS**: FastAPI CORS middleware
- **Environment**: python-dotenv
- **API Documentation**: Automatic Swagger UI (FastAPI built-in)

#### Folder Structure:
```
savemate-backend/
├── app/
│   ├── main.py                 # FastAPI application entry point
│   ├── config.py               # Configuration & environment variables
│   ├── database.py             # MongoDB connection setup
│   │
│   ├── models/                 # Pydantic & Beanie models
│   │   ├── __init__.py
│   │   ├── user.py             # User document model
│   │   ├── deal.py             # Deal document model
│   │   ├── business.py         # Business document model
│   │   └── category.py         # Category document model
│   │
│   ├── schemas/                # Pydantic schemas (request/response)
│   │   ├── __init__.py
│   │   ├── user_schema.py      # UserCreate, UserUpdate, UserResponse
│   │   ├── deal_schema.py      # DealCreate, DealUpdate, DealResponse
│   │   ├── auth_schema.py      # Token, LoginRequest
│   │   └── common_schema.py    # Shared schemas
│   │
│   ├── api/                    # API route handlers
│   │   ├── __init__.py
│   │   ├── deps.py             # Dependencies (get_current_user, etc.)
│   │   └── routes/
│   │       ├── __init__.py
│   │       ├── auth.py         # /auth/* routes
│   │       ├── users.py        # /users/* routes
│   │       ├── deals.py        # /deals/* routes
│   │       ├── businesses.py   # /businesses/* routes
│   │       └── categories.py   # /categories/* routes
│   │
│   ├── services/               # Business logic layer
│   │   ├── __init__.py
│   │   ├── auth_service.py     # Authentication logic
│   │   ├── user_service.py     # User operations
│   │   ├── deal_service.py     # Deal operations
│   │   └── cloudinary_service.py # Image upload logic
│   │
│   ├── utils/                  # Utility functions
│   │   ├── __init__.py
│   │   ├── security.py         # JWT & password hashing
│   │   ├── validators.py       # Custom validators
│   │   └── helpers.py          # Helper functions
│   │
│   └── middleware/             # Custom middleware
│       ├── __init__.py
│       └── error_handler.py    # Global error handling
│
├── tests/                      # Test files
│   ├── test_auth.py
│   ├── test_deals.py
│   └── test_users.py
│
├── requirements.txt            # Python dependencies
├── .env.example               # Example environment variables
├── .gitignore
└── README.md
```

#### Required Python Dependencies:
```txt
fastapi==0.104.1
uvicorn[standard]==0.24.0
motor==3.3.2
beanie==1.23.0
pydantic==2.5.0
pydantic-settings==2.1.0
python-jose[cryptography]==3.3.0
passlib[bcrypt]==1.7.4
python-multipart==0.0.6
cloudinary==1.36.0
python-dotenv==1.0.0
```

---

### **DATABASE SCHEMA (MongoDB)**:

#### **Collection: users**
```python
{
    "_id": ObjectId,                    # Auto-generated MongoDB ID
    "email": String,                    # Unique, indexed
    "username": String,                 # Unique, indexed
    "password_hash": String,            # Bcrypt hashed
    "full_name": String,
    "profile_picture": String,          # Cloudinary URL
    "location": {
        "type": "Point",
        "coordinates": [longitude, latitude]  # GeoJSON format
    },
    "preferences": {
        "favorite_categories": [ObjectId],  # References to categories
        "notification_radius": Float,       # km radius for deals
        "email_notifications": Boolean
    },
    "saved_deals": [ObjectId],          # References to deals
    "created_at": DateTime,
    "updated_at": DateTime,
    "is_active": Boolean,
    "is_business_owner": Boolean
}
```

**Indexes**:
- email (unique)
- username (unique)
- location (2dsphere for geospatial queries)

---

#### **Collection: deals**
```python
{
    "_id": ObjectId,
    "title": String,                    # Required, max 200 chars
    "description": String,              # Required, max 2000 chars
    "business_id": ObjectId,            # Reference to businesses
    "category_id": ObjectId,            # Reference to categories
    "discount_percentage": Float,       # 0-100
    "original_price": Float,
    "discounted_price": Float,
    "images": [String],                 # Cloudinary URLs
    "location": {
        "type": "Point",
        "coordinates": [longitude, latitude]
    },
    "address": {
        "street": String,
        "city": String,
        "state": String,
        "zip_code": String,
        "country": String
    },
    "valid_from": DateTime,
    "valid_until": DateTime,
    "terms_conditions": String,
    "redemption_code": String,          # Optional promo code
    "is_active": Boolean,
    "views_count": Integer,
    "saves_count": Integer,
    "created_by": ObjectId,             # Reference to users
    "created_at": DateTime,
    "updated_at": DateTime
}
```

**Indexes**:
- business_id
- category_id
- location (2dsphere)
- valid_until (for expiration queries)
- created_at (for sorting)

---

#### **Collection: businesses**
```python
{
    "_id": ObjectId,
    "name": String,                     # Required
    "description": String,
    "owner_id": ObjectId,               # Reference to users
    "logo": String,                     # Cloudinary URL
    "cover_image": String,              # Cloudinary URL
    "category": String,
    "location": {
        "type": "Point",
        "coordinates": [longitude, latitude]
    },
    "address": {
        "street": String,
        "city": String,
        "state": String,
        "zip_code": String,
        "country": String
    },
    "contact": {
        "phone": String,
        "email": String,
        "website": String
    },
    "social_media": {
        "facebook": String,
        "instagram": String,
        "twitter": String
    },
    "operating_hours": {
        "monday": {"open": String, "close": String},
        "tuesday": {"open": String, "close": String},
        # ... other days
    },
    "rating": Float,                    # Average rating
    "total_deals": Integer,
    "is_verified": Boolean,
    "created_at": DateTime,
    "updated_at": DateTime
}
```

**Indexes**:
- owner_id
- location (2dsphere)
- name (text search)

---

#### **Collection: categories**
```python
{
    "_id": ObjectId,
    "name": String,                     # Unique, e.g., "Food & Dining"
    "slug": String,                     # URL-friendly, e.g., "food-dining"
    "description": String,
    "icon": String,                     # Icon name or URL
    "color": String,                    # Hex color code
    "parent_category": ObjectId,        # Optional, for subcategories
    "is_active": Boolean,
    "created_at": DateTime
}
```

**Indexes**:
- name (unique)
- slug (unique)

---

### **API ENDPOINTS DOCUMENTATION**:

#### **BASE URL**: `http://localhost:8000/api/v1`

#### **Authentication Endpoints** (`/auth`):
```
POST   /auth/register          # Register new user
POST   /auth/login             # Login user
POST   /auth/refresh           # Refresh access token
POST   /auth/logout            # Logout user
POST   /auth/forgot-password   # Request password reset
POST   /auth/reset-password    # Reset password with token
```

#### **User Endpoints** (`/users`):
```
GET    /users/me               # Get current user profile
PUT    /users/me               # Update current user profile
DELETE /users/me               # Delete current user account
POST   /users/me/avatar        # Upload profile picture
GET    /users/{user_id}        # Get user by ID (public info)
GET    /users/me/saved-deals   # Get saved deals
POST   /users/me/saved-deals/{deal_id}  # Save a deal
DELETE /users/me/saved-deals/{deal_id}  # Unsave a deal
```

#### **Deal Endpoints** (`/deals`):
```
GET    /deals                  # Get all deals (with filters)
POST   /deals                  # Create new deal (auth required)
GET    /deals/{deal_id}        # Get deal by ID
PUT    /deals/{deal_id}        # Update deal (owner only)
DELETE /deals/{deal_id}        # Delete deal (owner only)
GET    /deals/nearby           # Get deals near location
GET    /deals/search           # Search deals by keyword
GET    /deals/category/{cat_id} # Get deals by category
POST   /deals/{deal_id}/images # Upload deal images
```

#### **Business Endpoints** (`/businesses`):
```
GET    /businesses             # Get all businesses
POST   /businesses             # Create new business (auth required)
GET    /businesses/{business_id} # Get business by ID
PUT    /businesses/{business_id} # Update business (owner only)
DELETE /businesses/{business_id} # Delete business (owner only)
GET    /businesses/{business_id}/deals # Get deals for business
POST   /businesses/{business_id}/logo  # Upload business logo
```

#### **Category Endpoints** (`/categories`):
```
GET    /categories             # Get all categories
POST   /categories             # Create category (admin only)
GET    /categories/{category_id} # Get category by ID
PUT    /categories/{category_id} # Update category (admin only)
DELETE /categories/{category_id} # Delete category (admin only)
```

#### **Query Parameters** (for filtering):
```
GET /deals?
    category=food-dining
    &min_discount=20
    &max_price=50
    &lat=40.7128
    &lng=-74.0060
    &radius=5
    &sort=newest|popular|discount
    &page=1
    &limit=20
```

---

### **AUTHENTICATION FLOW**:

1. **User Registration**:
   - User submits email, username, password
   - Backend validates input with Pydantic
   - Password hashed with bcrypt
   - User document created in MongoDB
   - JWT tokens returned (access + refresh)

2. **User Login**:
   - User submits email/username + password
   - Backend verifies credentials
   - JWT tokens generated and returned
   - Frontend stores tokens (localStorage/cookies)

3. **Protected Routes**:
   - Frontend sends JWT in Authorization header
   - Backend validates token with `get_current_user` dependency
   - User object injected into route handler

4. **Token Structure**:
```python
{
    "access_token": "eyJ...",  # Expires in 30 minutes
    "refresh_token": "eyJ...", # Expires in 7 days
    "token_type": "bearer"
}
```

---

### **GEOSPATIAL QUERIES**:

MongoDB 2dsphere indexes enable location-based queries:

```python
# Find deals within 5km radius
deals_nearby = await Deal.find(
    {
        "location": {
            "$near": {
                "$geometry": {
                    "type": "Point",
                    "coordinates": [lng, lat]
                },
                "$maxDistance": 5000  # 5km in meters
            }
        },
        "is_active": True,
        "valid_until": {"$gte": datetime.now()}
    }
).to_list()
```

---

### **FILE UPLOAD WORKFLOW**:

1. Frontend uploads image via form
2. Backend receives UploadFile
3. Image validated (type, size)
4. Uploaded to Cloudinary via Python SDK
5. Cloudinary URL returned
6. URL stored in MongoDB document

---

### **ERROR HANDLING**:

FastAPI automatic error responses:
```python
{
    "detail": "Error message",
    "status_code": 400,
    "error_type": "ValidationError"
}
```

Custom exception handlers for:
- 400: Bad Request
- 401: Unauthorized
- 403: Forbidden
- 404: Not Found
- 422: Validation Error
- 500: Internal Server Error

---

### **ENVIRONMENT VARIABLES**:

#### Backend (.env):
```
# MongoDB
MONGODB_URL=mongodb+srv://username:password@cluster.mongodb.net
DATABASE_NAME=savemate

# JWT
SECRET_KEY=your-secret-key-here-change-in-production
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30
REFRESH_TOKEN_EXPIRE_DAYS=7

# Cloudinary
CLOUDINARY_CLOUD_NAME=your-cloud-name
CLOUDINARY_API_KEY=your-api-key
CLOUDINARY_API_SECRET=your-api-secret

# CORS
ALLOWED_ORIGINS=http://localhost:5173,http://localhost:3000

# Application
DEBUG=True
API_V1_PREFIX=/api/v1
```

#### Frontend (.env):
```
VITE_API_BASE_URL=http://localhost:8000/api/v1
VITE_CLOUDINARY_CLOUD_NAME=your-cloud-name
VITE_CLOUDINARY_UPLOAD_PRESET=your-preset
VITE_MAP_API_KEY=your-map-api-key
```

---

## PENDING TASKS (to complete this card):

### CHECKLIST (29% complete):
✅ Research best practices for MERN stack (now Python/FastAPI stack)
✅ Create architecture diagram
⬜ **Design database schema** (see above - needs team review)
⬜ **Plan folder structure** (see above - needs team review)
⬜ **Document API endpoints** (see above - needs team review)
⬜ **Get team approval on architecture**
⬜ **Share documentation with team**

---

## NEXT STEPS:

1. **Review this architecture document with team**
2. **Get approval from all team members (RI, MR, J)**
3. **Implement folder structure in both repositories**
4. **Create Pydantic models and schemas**
5. **Set up MongoDB connection with Beanie**
6. **Implement authentication system**
7. **Build API endpoints incrementally**
8. **Test with FastAPI Swagger UI**

---

## AUTOMATIC DOCUMENTATION:

Once backend is running, interactive API docs available at:
- **Swagger UI**: http://localhost:8000/docs
- **ReDoc**: http://localhost:8000/redoc

No manual documentation needed - FastAPI generates it automatically! 🎉

---

*Document updated: November 22, 2025*
*Stack: Python/FastAPI + React + MongoDB*
