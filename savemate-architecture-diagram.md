# SaveMate Architecture Diagram

Complete system architecture for SaveMate - Local Deals Platform

**Version:** 1.0.0 (Phase 5 Complete)  
**Last Updated:** November 24, 2025  
**Status:** ✅ Backend Complete | 🔄 Frontend In Planning

---

## 📋 Table of Contents

1. [High-Level Architecture](#high-level-architecture)
2. [Backend Architecture](#backend-architecture)
3. [Database Architecture](#database-architecture)
4. [API Layer](#api-layer)
5. [Authentication Flow](#authentication-flow)
6. [Data Flow](#data-flow)
7. [Technology Stack](#technology-stack)
8. [Future Architecture](#future-architecture)

---

## 🏗️ High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        CLIENT LAYER                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │ Web Browser  │  │ Mobile App   │  │   Admin      │      │
│  │  (React)     │  │  (Future)    │  │  Dashboard   │      │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘      │
│         │                  │                  │               │
│         └──────────────────┴──────────────────┘               │
│                            │                                  │
└────────────────────────────┼──────────────────────────────────┘
                             │
                    ┌────────▼─────────┐
                    │   HTTPS/REST     │
                    └────────┬─────────┘
                             │
┌────────────────────────────▼──────────────────────────────────┐
│                     API GATEWAY LAYER                          │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │              FastAPI Application                        │  │
│  │  ┌──────────────────────────────────────────────────┐  │  │
│  │  │  CORS Middleware │ Auth Middleware │ Logging     │  │  │
│  │  └──────────────────────────────────────────────────┘  │  │
│  │  ┌──────────────────────────────────────────────────┐  │  │
│  │  │  Swagger UI  │  ReDoc  │  Health Check Endpoints │  │  │
│  │  └──────────────────────────────────────────────────┘  │  │
│  └─────────────────────────────────────────────────────────┘  │
│                            │                                   │
│         ┌──────────────────┼──────────────────┐              │
│         │                  │                  │               │
│    ┌────▼─────┐      ┌────▼─────┐      ┌────▼─────┐        │
│    │   Auth   │      │  Deals   │      │ Business │        │
│    │  Routes  │      │  Routes  │      │  Routes  │        │
│    └────┬─────┘      └────┬─────┘      └────┬─────┘        │
│         │                  │                  │               │
│    ┌────▼─────┐      ┌────▼─────┐                          │
│    │Favorites │      │ Reviews  │                          │
│    │  Routes  │      │  Routes  │                          │
│    └────┬─────┘      └────┬─────┘                          │
│         │                  │                                  │
└─────────┼──────────────────┼──────────────────────────────────┘
          │                  │
┌─────────▼──────────────────▼──────────────────────────────────┐
│                    BUSINESS LOGIC LAYER                        │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐          │
│  │   Security  │  │  Validation │  │   Models    │          │
│  │  (JWT, Hash)│  │  (Pydantic) │  │  (Beanie)   │          │
│  └─────────────┘  └─────────────┘  └─────────────┘          │
└────────────────────────────┬───────────────────────────────────┘
                             │
┌────────────────────────────▼───────────────────────────────────┐
│                      DATA ACCESS LAYER                          │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │              Beanie ODM (Motor Driver)                  │  │
│  └─────────────────────────────────────────────────────────┘  │
└────────────────────────────┬───────────────────────────────────┘
                             │
┌────────────────────────────▼───────────────────────────────────┐
│                      DATABASE LAYER                             │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │                  MongoDB Atlas (Cloud)                  │  │
│  │  ┌──────┐  ┌──────┐  ┌──────┐  ┌──────┐  ┌──────┐    │  │
│  │  │Users │  │Deals │  │Business│ │Favs  │  │Reviews│   │  │
│  │  └──────┘  └──────┘  └──────┘  └──────┘  └──────┘    │  │
│  └─────────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────────┘
```

---

## ⚙️ Backend Architecture (Detailed)

```
savemate-backend/
│
├── app/
│   ├── main.py                    # FastAPI Application Entry
│   │   ├── CORS Configuration
│   │   ├── Router Registration
│   │   ├── Lifespan Events
│   │   └── Health Check Endpoints
│   │
│   ├── config.py                  # Configuration Management
│   │   ├── Environment Variables
│   │   ├── Database Settings
│   │   └── Security Settings
│   │
│   ├── database.py                # Database Connection
│   │   ├── MongoDB Client
│   │   ├── Beanie Initialization
│   │   └── Connection Management
│   │
│   ├── core/                      # Core Functionality
│   │   ├── security.py            # Security Utilities
│   │   │   ├── Password Hashing (bcrypt)
│   │   │   ├── JWT Token Creation
│   │   │   ├── Token Verification
│   │   │   └── Token Refresh
│   │   │
│   │   └── config.py              # Core Configuration
│   │
│   ├── models/                    # Database Models (Beanie)
│   │   ├── user.py                # User Document
│   │   ├── deal.py                # Deal Document
│   │   ├── business.py            # Business Document
│   │   ├── category.py            # Category Document
│   │   ├── favorite.py            # Favorite Document
│   │   └── review.py              # Review Document
│   │
│   ├── schemas/                   # Pydantic Schemas
│   │   ├── auth_schema.py         # Auth Request/Response
│   │   ├── deal_schema.py         # Deal Request/Response
│   │   ├── business_schema.py     # Business Request/Response
│   │   ├── favorite_schema.py     # Favorite Request/Response
│   │   └── review_schema.py       # Review Request/Response
│   │
│   └── api/
│       └── routes/                # API Route Handlers
│           ├── auth.py            # Authentication (3 endpoints)
│           ├── deals.py           # Deals CRUD (6 endpoints)
│           ├── businesses.py      # Business CRUD (7 endpoints)
│           ├── favorites.py       # Favorites (4 endpoints)
│           └── reviews.py         # Reviews (5 endpoints)
│
├── .env                           # Environment Variables
├── .gitignore                     # Git Ignore Rules
├── requirements.txt               # Python Dependencies
└── README.md                      # Documentation
```

**Total Endpoints:** 25  
**Total Models:** 6  
**Total Collections:** 6

---

## 💾 Database Architecture

### Collection Structure

```
MongoDB Atlas - "savemate" Database
│
├── users                          # User Accounts
│   ├── _id (ObjectId)
│   ├── email (unique, indexed)
│   ├── username (unique, indexed)
│   ├── hashed_password
│   ├── full_name
│   ├── is_active
│   ├── is_business_owner
│   ├── created_at
│   └── updated_at
│
├── deals                          # Deal Listings
│   ├── _id (ObjectId)
│   ├── title
│   ├── description
│   ├── original_price
│   ├── discounted_price
│   ├── discount_percentage (auto-calculated)
│   ├── category (enum)
│   ├── business_id (ref: users._id)
│   ├── business_name
│   ├── location (dict)
│   ├── start_date / end_date
│   ├── status (enum)
│   ├── views_count
│   ├── saves_count
│   ├── created_by (ref: users._id)
│   └── timestamps
│
├── businesses                     # Business Profiles
│   ├── _id (ObjectId)
│   ├── owner_id (ref: users._id)
│   ├── business_name
│   ├── category (enum)
│   ├── email, phone, website
│   ├── location (dict)
│   ├── operating_hours (array)
│   ├── status (enum)
│   ├── rating_average / rating_count
│   ├── total_deals / active_deals
│   └── timestamps
│
├── favorites                      # User Favorites (M:N)
│   ├── _id (ObjectId)
│   ├── user_id (ref: users._id)
│   ├── deal_id (ref: deals._id)
│   └── created_at
│   │
│   └── Index: (user_id, deal_id) unique
│
├── reviews                        # Deal Reviews (M:N)
│   ├── _id (ObjectId)
│   ├── deal_id (ref: deals._id)
│   ├── user_id (ref: users._id)
│   ├── business_id (ref: businesses._id)
│   ├── rating (1-5)
│   ├── title, comment
│   ├── helpful_count
│   └── timestamps
│   │
│   └── Index: (user_id, deal_id) unique
│
└── categories                     # Deal Categories
    ├── _id (ObjectId)
    ├── name
    ├── slug (unique)
    ├── description
    └── is_active
```

### Relationships

```
User (1) ────── (N) Business
  │                    │
  │                    │
  │ (created_by)       │ (business_id)
  │                    │
  ├─────────────── (N) Deal ◄──────┐
  │                    │            │
  │                    │            │
  │                    │            │
  ├── (M:N) ──── Favorite          │
  │                                 │
  └── (M:N) ──── Review ────────────┘
                     │
                     │ (business_id)
                     │
                 Business
```

---

## 🔌 API Layer

### Endpoint Organization

```
/api/v1/
│
├── /auth/                         # Authentication
│   ├── POST   /register           # Register user
│   ├── POST   /login              # Login user
│   └── POST   /refresh            # Refresh token
│
├── /deals/                        # Deals Management
│   ├── POST   /                   # Create deal
│   ├── GET    /                   # Get all deals (filters, pagination)
│   ├── GET    /{id}               # Get single deal
│   ├── PUT    /{id}               # Update deal
│   ├── DELETE /{id}               # Delete deal
│   └── GET    /category/{cat}     # Get deals by category
│
├── /businesses/                   # Business Management
│   ├── POST   /                   # Create business
│   ├── GET    /                   # Get all businesses
│   ├── GET    /{id}               # Get single business
│   ├── PUT    /{id}               # Update business
│   ├── DELETE /{id}               # Delete business
│   ├── GET    /owner/{user_id}    # Get user's businesses
│   └── GET    /{id}/deals         # Get business deals
│
├── /favorites/                    # User Favorites
│   ├── POST   /                   # Add to favorites
│   ├── DELETE /{deal_id}          # Remove from favorites
│   ├── GET    /                   # Get user favorites
│   └── GET    /check/{deal_id}    # Check if favorited
│
└── /reviews/                      # Reviews & Ratings
    ├── POST   /                   # Create review
    ├── GET    /deal/{deal_id}     # Get deal reviews
    ├── GET    /user/{user_id}     # Get user reviews
    ├── PUT    /{id}               # Update review
    └── POST   /{id}/helpful       # Mark as helpful
```

---

## 🔐 Authentication Flow

```
┌──────────────┐
│    CLIENT    │
└──────┬───────┘
       │
       │ 1. POST /auth/register or /auth/login
       │    { email, password }
       ▼
┌──────────────────────────────────────────┐
│         FastAPI Auth Endpoint            │
└──────┬───────────────────────────────────┘
       │
       │ 2. Validate credentials
       │    (bcrypt password check)
       ▼
┌──────────────────────────────────────────┐
│         Security Module                  │
│  ┌────────────────────────────────────┐ │
│  │  create_access_token()             │ │
│  │  create_refresh_token()            │ │
│  │  Payload: user_id, exp, type       │ │
│  └────────────────────────────────────┘ │
└──────┬───────────────────────────────────┘
       │
       │ 3. Return tokens
       │    { access_token, refresh_token }
       ▼
┌──────────────┐
│    CLIENT    │
│  Store tokens│
└──────┬───────┘
       │
       │ 4. Authenticated Request
       │    Headers: Authorization: Bearer {access_token}
       ▼
┌──────────────────────────────────────────┐
│      Protected Endpoint                  │
└──────┬───────────────────────────────────┘
       │
       │ 5. Verify token (JWT decode + validate)
       ▼
┌──────────────────────────────────────────┐
│         Security Module                  │
│  ┌────────────────────────────────────┐ │
│  │  verify_token()                    │ │
│  │  Check expiration, signature       │ │
│  └────────────────────────────────────┘ │
└──────┬───────────────────────────────────┘
       │
       │ 6a. Valid → Process request
       │ 6b. Invalid/Expired → 401 Unauthorized
       ▼
┌──────────────┐
│   RESPONSE   │
└──────────────┘
```

### Token Details

**Access Token:**
- Expires: 30 minutes
- Purpose: API authentication
- Contains: user_id, token_type

**Refresh Token:**
- Expires: 7 days
- Purpose: Get new access token
- Contains: user_id, token_type

---

## 📊 Data Flow Examples

### Example 1: Create Deal Flow

```
User → POST /api/v1/deals/
  │
  ├─► Authenticate (JWT)
  │     │
  │     ├─► Valid? → Continue
  │     └─► Invalid? → 401 Unauthorized
  │
  ├─► Validate Request (Pydantic)
  │     ├─► Check: title, prices, dates
  │     ├─► Auto-calculate: discount_percentage
  │     └─► Validate: location, category
  │
  ├─► Create Deal Document
  │     ├─► business_id = current_user_id
  │     ├─► status = "active"
  │     ├─► created_by = current_user_id
  │     └─► timestamps
  │
  ├─► Save to MongoDB (deals collection)
  │
  └─► Return 201 Created
        └─► DealResponse schema
```

### Example 2: Add to Favorites Flow

```
User → POST /api/v1/favorites/
  │       { deal_id: "..." }
  │
  ├─► Authenticate
  │
  ├─► Check if already favorited
  │     ├─► Query: (user_id, deal_id)
  │     └─► Exists? → 400 Bad Request
  │
  ├─► Verify deal exists
  │     └─► Not found? → 404 Not Found
  │
  ├─► Create Favorite document
  │     ├─► user_id = current_user_id
  │     └─► deal_id = request.deal_id
  │
  ├─► Increment deal.saves_count
  │     └─► UPDATE deals SET saves_count += 1
  │
  └─► Return 201 Created
```

### Example 3: Get Deals with Filters

```
User → GET /api/v1/deals/?category=food&city=Warsaw&min_discount=30
  │
  ├─► Parse Query Parameters
  │     ├─► category = "food"
  │     ├─► city = "Warsaw"
  │     ├─► min_discount = 30
  │     └─► page, page_size, sort_by, sort_order
  │
  ├─► Build MongoDB Query
  │     {
  │       "category": "food",
  │       "location.city": { $regex: "warsaw", $options: "i" },
  │       "discount_percentage": { $gte: 30 },
  │       "status": "active"
  │     }
  │
  ├─► Execute Query
  │     ├─► Count total results
  │     ├─► Apply pagination (.skip().limit())
  │     └─► Apply sorting
  │
  └─► Return 200 OK
        ├─► deals: [...]
        ├─► total, page, page_size
        └─► total_pages
```

---

## 🛠️ Technology Stack

### Backend

| Component | Technology | Purpose |
|-----------|------------|---------|
| Framework | FastAPI 0.104+ | REST API framework |
| Language | Python 3.8+ | Programming language |
| Server | Uvicorn | ASGI server |
| ODM | Beanie 1.23+ | MongoDB object mapper |
| Database Driver | Motor 3.3+ | Async MongoDB driver |
| Validation | Pydantic 2.5+ | Data validation |
| Auth | JWT (python-jose) | Token authentication |
| Password | bcrypt 4.1.2 | Password hashing |

### Database

| Component | Technology | Purpose |
|-----------|------------|---------|
| Database | MongoDB Atlas | Cloud NoSQL database |
| Collections | 6 | Data storage |
| Indexes | Compound + Single | Query optimization |

### DevOps

| Component | Technology | Purpose |
|-----------|------------|---------|
| Version Control | Git + GitHub | Code management |
| Documentation | Swagger UI + ReDoc | API docs |
| Environment | dotenv | Config management |

---

## 🚀 Future Architecture (Phase 6+)

### Planned Additions

```
┌─────────────────────────────────────────────────────────────┐
│                  FUTURE COMPONENTS                           │
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │   Redis      │  │  Celery      │  │   AWS S3     │     │
│  │  (Caching)   │  │ (Task Queue) │  │(Image Store) │     │
│  └──────────────┘  └──────────────┘  └──────────────┘     │
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │  SendGrid    │  │  Sentry      │  │  Prometheus  │     │
│  │   (Email)    │  │(Error Track) │  │ (Monitoring) │     │
│  └──────────────┘  └──────────────┘  └──────────────┘     │
│                                                              │
│  ┌─────────────────────────────────────────────────────┐   │
│  │           Polish API Integrations                   │   │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐         │   │
│  │  │  Google  │  │ Allegro  │  │   GUS    │         │   │
│  │  │  Places  │  │   API    │  │   API    │         │   │
│  │  └──────────┘  └──────────┘  └──────────┘         │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                              │
│  ┌─────────────────────────────────────────────────────┐   │
│  │              Frontend (React)                       │   │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐         │   │
│  │  │  Vite    │  │ Tailwind │  │ Shadcn/ui│         │   │
│  │  └──────────┘  └──────────┘  └──────────┘         │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

### Phase 6 Features
- 🔄 Polish API Integration (Google Places, Allegro, GUS)
- 🗺️ Advanced Geolocation Search
- 📧 Email Notifications (SendGrid)
- 📊 Admin Dashboard
- 🖼️ Image Upload (AWS S3)

### Phase 7+ Features
- ⚡ Redis Caching
- 🔔 Real-time Notifications (WebSocket)
- 📱 Mobile App API
- 🧪 Automated Testing
- 🚀 CI/CD Pipeline
- 📈 Analytics & Monitoring

---

## 📐 Deployment Architecture (Future)

```
┌─────────────────────────────────────────────────────────────┐
│                      PRODUCTION                              │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐  │
│  │            Load Balancer (Nginx/AWS ALB)             │  │
│  └──────────────────┬───────────────────────────────────┘  │
│                     │                                       │
│         ┌───────────┼───────────┐                          │
│         │           │           │                           │
│    ┌────▼────┐ ┌────▼────┐ ┌────▼────┐                    │
│    │  API    │ │  API    │ │  API    │                    │
│    │Instance1│ │Instance2│ │Instance3│                    │
│    └────┬────┘ └────┬────┘ └────┬────┘                    │
│         │           │           │                           │
│         └───────────┼───────────┘                          │
│                     │                                       │
│         ┌───────────▼───────────┐                          │
│         │                       │                           │
│    ┌────▼────┐          ┌──────▼──────┐                   │
│    │ MongoDB │          │    Redis    │                   │
│    │ Atlas   │          │   (Cache)   │                   │
│    │Replica  │          └─────────────┘                   │
│    │  Set    │                                             │
│    └─────────┘                                             │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## 📊 Current Status

### ✅ Completed (Phase 1-5)

| Phase | Component | Status |
|-------|-----------|--------|
| 1 | Project Setup | ✅ Complete |
| 2 | Database Models | ✅ Complete |
| 3 | Authentication | ✅ Complete |
| 4 | Deals System | ✅ Complete |
| 5 | Business/Favorites/Reviews | ✅ Complete |

**Total:**
- 25 API Endpoints
- 6 Database Collections
- 6 Models with Beanie ODM
- JWT Authentication
- Complete CRUD Operations
- Advanced Filtering & Search
- Pagination & Sorting

### 🔄 In Progress (Phase 6)
- API Integration Planning
- Frontend Architecture Design

### 🔮 Planned (Phase 7+)
- Caching Layer
- Background Tasks
- Real-time Features
- Mobile App
- Production Deployment

---

**Last Updated:** November 24, 2025  
**Version:** 1.0.0 (Phase 5 Complete)  
**Repository:** https://github.com/jenfranx30/savemate-backend

---

For detailed API documentation, see [SaveMate_API_Documentation.md](SaveMate_API_Documentation.md)
