# SaveMate System Architecture

**Version:** 1.0.0  
**Last Updated:** January 2026

---

## Table of Contents

1. [Overview](#overview)
2. [High-Level Architecture](#high-level-architecture)
3. [Frontend Architecture](#frontend-architecture)
4. [Backend Architecture](#backend-architecture)
5. [Database Architecture](#database-architecture)
6. [Security Architecture](#security-architecture)
7. [Deployment Architecture](#deployment-architecture)
8. [Integration Points](#integration-points)

---

## Overview

SaveMate follows a modern three-tier architecture:

```
┌────────────────┐
│ Presentation   │  React SPA (Vite)
├────────────────┤
│ Application    │  FastAPI REST API
├────────────────┤
│ Data          │  MongoDB Atlas
└────────────────┘
```

### Design Principles

1. **Separation of Concerns** - Clear boundaries between layers
2. **Scalability** - Horizontal scaling capability
3. **Security First** - Security at every layer
4. **Mobile-First** - Responsive design priority
5. **API-Driven** - Frontend/backend decoupling

---

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                      CLIENT TIER                                 │
├─────────────────────────────────────────────────────────────────┤
│  Desktop Browser          │        Mobile Browser               │
│  ├─ React Components      │        ├─ Responsive UI            │
│  ├─ Context State         │        ├─ Touch Interface          │
│  ├─ React Router          │        ├─ Bottom Navigation        │
│  └─ Tailwind CSS          │        └─ PWA Ready                │
└──────────────┬──────────────────────────┬──────────────────────┘
               │     REST API (HTTPS)      │
               │     JSON                  │
               ▼                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                   APPLICATION TIER (FastAPI)                     │
├─────────────────────────────────────────────────────────────────┤
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐         │
│  │ Auth Service │  │ Deal Service │  │ Business Svc │         │
│  └──────────────┘  └──────────────┘  └──────────────┘         │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐         │
│  │ Verification │  │ Category Svc │  │ Review Svc   │         │
│  └──────────────┘  └──────────────┘  └──────────────┘         │
│                                                                  │
│  Middleware: CORS, Auth, Logging, Error Handling                │
└──────────────┬──────────────────────────────────────────────────┘
               │     Beanie ODM
               ▼
┌─────────────────────────────────────────────────────────────────┐
│                      DATA TIER (MongoDB)                         │
├─────────────────────────────────────────────────────────────────┤
│  Collections:                                                    │
│  users │ businesses │ deals │ categories │ favorites │ reviews  │
│                                                                  │
│  Indexes: Unique, Geo-spatial, Text search                      │
└─────────────────────────────────────────────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────────────────────────────┐
│                    EXTERNAL SERVICES                             │
├─────────────────────────────────────────────────────────────────┤
│  Cloudinary  │  Google Maps  │  Email (planned)  │  Payment    │
└─────────────────────────────────────────────────────────────────┘
```

---

## Frontend Architecture

### Component Hierarchy

```
App
├── Router
│   ├── Public Routes
│   │   ├── Home
│   │   ├── Login
│   │   ├── Register
│   │   └── Deals (Browse)
│   │
│   ├── Protected Routes (User)
│   │   ├── Dashboard
│   │   ├── Profile
│   │   ├── Favorites
│   │   └── Settings
│   │
│   └── Protected Routes (Business)
│       ├── BusinessDashboard
│       ├── DealManagement
│       ├── BusinessProfile
│       └── Verification
│
├── Context Providers
│   ├── AuthContext
│   ├── ThemeContext
│   └── NotificationContext
│
└── Shared Components
    ├── Navigation
    ├── Header
    ├── Footer
    └── Modals
```

### State Management

```javascript
// AuthContext
{
  user: User | null,
  token: string | null,
  login: (credentials) => Promise<void>,
  logout: () => void,
  register: (data) => Promise<void>,
  isAuthenticated: boolean,
  isLoading: boolean
}

// ThemeContext
{
  theme: 'light' | 'dark',
  toggleTheme: () => void
}

// NotificationContext
{
  notifications: Notification[],
  addNotification: (message) => void,
  removeNotification: (id) => void
}
```

### Routing Strategy

```javascript
// Public routes
'/'              → Home
'/login'         → Login
'/register'      → Register
'/deals'         → Browse Deals
'/deals/:id'     → Deal Details
'/businesses'    → Browse Businesses
'/business/:id'  → Business Profile

// User routes (protected)
'/dashboard'     → User Dashboard
'/profile'       → User Profile
'/favorites'     → Saved Deals
'/settings'      → User Settings

// Business routes (protected + role check)
'/business/dashboard'     → Business Dashboard
'/business/deals'         → Manage Deals
'/business/profile'       → Business Profile
'/business/verification'  → Verification
'/business/analytics'     → Analytics
```

---

## Backend Architecture

### Service Layer Pattern

```
┌─────────────────────────────────────┐
│         API Routes Layer            │
│  (Request handling, validation)     │
└──────────────┬──────────────────────┘
               ▼
┌─────────────────────────────────────┐
│        Service Layer                │
│  (Business logic, orchestration)    │
└──────────────┬──────────────────────┘
               ▼
┌─────────────────────────────────────┐
│         Repository Layer            │
│  (Database operations)              │
└──────────────┬──────────────────────┘
               ▼
┌─────────────────────────────────────┐
│         Database (MongoDB)          │
└─────────────────────────────────────┘
```

### Directory Structure

```
app/
├── main.py                 # Application entry point
├── config.py               # Configuration management
│
├── models/                 # Database models (Beanie)
│   ├── user.py
│   ├── business.py
│   ├── deal.py
│   └── category.py
│
├── schemas/                # Pydantic schemas
│   ├── user.py
│   ├── business.py
│   ├── deal.py
│   └── auth.py
│
├── routes/                 # API endpoints
│   ├── auth.py
│   ├── deals.py
│   ├── businesses.py
│   └── verification.py
│
├── services/               # Business logic
│   ├── auth_service.py
│   ├── deal_service.py
│   └── verification_service.py
│
├── middleware/             # Custom middleware
│   ├── auth.py
│   ├── cors.py
│   └── error_handler.py
│
└── utils/                  # Utility functions
    ├── security.py
    ├── cloudinary.py
    └── validators.py
```

### Request Flow

```
1. HTTP Request arrives
   ↓
2. CORS Middleware (origin check)
   ↓
3. Route Handler (FastAPI)
   ↓
4. Request Validation (Pydantic)
   ↓
5. Authentication Middleware (JWT)
   ↓
6. Authorization Check (role-based)
   ↓
7. Service Layer (business logic)
   ↓
8. Repository Layer (database ops)
   ↓
9. Response Serialization (Pydantic)
   ↓
10. HTTP Response returned
```

---

## Database Architecture

### Collection Design

**Users Collection:**
```javascript
{
  _id: ObjectId,
  email: String (indexed, unique),
  username: String (indexed, unique),
  password_hash: String,
  role: String,  // "user" | "business"
  created_at: DateTime,
  updated_at: DateTime,
  profile: {
    name: String,
    phone: String,
    avatar_url: String
  },
  favorites: [ObjectId],  // References to deals
  settings: {
    notifications: Boolean,
    email_updates: Boolean
  }
}
```

**Businesses Collection:**
```javascript
{
  _id: ObjectId,
  user_id: ObjectId (indexed),  // Reference to users
  business_name: String,
  description: String,
  email: String,
  phone: String,
  category: String,
  location: {
    address: String,
    city: String,
    state: String,
    zip: String,
    coordinates: [Number, Number]  // [lng, lat] for geospatial
  },
  verification_status: String,  // "pending" | "verified" | "rejected"
  verified_at: DateTime,
  logo_url: String,
  images: [String],
  hours: {
    monday: String,
    tuesday: String,
    // ...
  },
  created_at: DateTime,
  updated_at: DateTime
}

// Geospatial index
db.businesses.createIndex({ "location.coordinates": "2dsphere" })
```

**Deals Collection:**
```javascript
{
  _id: ObjectId,
  business_id: ObjectId (indexed),
  title: String,
  description: String,
  category: String (indexed),
  original_price: Number,
  discounted_price: Number,
  discount_percentage: Number,
  image_url: String,
  start_date: DateTime,
  end_date: DateTime (indexed),
  active: Boolean (indexed),
  views: Number,
  saves: Number,
  redemptions: Number,
  terms: String,
  created_at: DateTime,
  updated_at: DateTime
}

// Text search index
db.deals.createIndex({ title: "text", description: "text" })
```

### Indexing Strategy

```javascript
// Performance indexes
users.email (unique)
users.username (unique)
businesses.user_id
businesses.verification_status
businesses.location.coordinates (2dsphere)
deals.business_id
deals.category
deals.active
deals.end_date
deals.{title, description} (text search)
```

---

## Security Architecture

### Authentication Flow

```
┌─────────────┐
│   Client    │
└──────┬──────┘
       │ 1. POST /auth/login
       │    {email, password}
       ▼
┌─────────────────────┐
│   API Server        │
│   2. Verify password│
│   3. Generate JWT   │
└──────┬──────────────┘
       │ 4. Return tokens
       │    {access_token, refresh_token}
       ▼
┌─────────────┐
│   Client    │
│ Store tokens│
└──────┬──────┘
       │ 5. Subsequent requests
       │    Authorization: Bearer <token>
       ▼
┌─────────────────────┐
│   API Server        │
│   6. Verify JWT     │
│   7. Check expiry   │
│   8. Process request│
└─────────────────────┘
```

### Security Layers

```
Layer 1: Network Security
├─ HTTPS/TLS encryption
├─ CORS origin whitelist
└─ Rate limiting

Layer 2: Authentication
├─ JWT with expiry
├─ Refresh token rotation
├─ Password hashing (bcrypt)
└─ Session management

Layer 3: Authorization
├─ Role-based access (RBAC)
├─ Resource ownership
└─ Permission checks

Layer 4: Data Security
├─ Input validation (Pydantic)
├─ SQL injection prevention (ODM)
├─ XSS prevention (React)
└─ CSRF protection

Layer 5: Application Security
├─ Dependency scanning
├─ Error handling
├─ Logging & monitoring
└─ Security headers
```

---

## Deployment Architecture

### Production Environment

```
┌─────────────────────────────────────────────────┐
│              CDN (Cloudflare)                   │
│           Static Assets & Caching               │
└──────────────┬──────────────────────────────────┘
               │
┌──────────────┴──────────────────────────────────┐
│                                                  │
▼                                                  ▼
┌────────────────────┐              ┌────────────────────┐
│   Vercel           │              │  Railway/Render    │
│   Frontend Host    │◄────REST────►│   Backend Host     │
│   - React Build    │              │   - FastAPI        │
│   - Auto-scaling   │              │   - Uvicorn        │
└────────────────────┘              └─────────┬──────────┘
                                              │
                                              ▼
                                    ┌────────────────────┐
                                    │  MongoDB Atlas     │
                                    │  Database Cluster  │
                                    │  - Replica Set     │
                                    │  - Auto-backup     │
                                    └────────────────────┘
                                              │
                                              ▼
                                    ┌────────────────────┐
                                    │  External Services │
                                    │  - Cloudinary      │
                                    │  - Google Maps     │
                                    │  - Email (planned) │
                                    └────────────────────┘
```

### Scalability Considerations

**Horizontal Scaling:**
- Frontend: Vercel auto-scales
- Backend: Multiple instances with load balancer
- Database: MongoDB replica sets

**Caching Strategy:**
- CDN: Static assets
- API: Redis cache (planned)
- Database: Query result caching

**Performance Optimization:**
- Image optimization (Cloudinary)
- Code splitting (React)
- Database indexing
- API response compression

---

## Integration Points

### Cloudinary Integration

```
Purpose: Image upload, storage, transformation
Flow:
1. Client selects image
2. Frontend uploads to Cloudinary
3. Cloudinary returns URL
4. URL stored in database
5. Images served via CDN
```

### Google Maps Integration

```
Purpose: Geocoding, location services
Flow:
1. User enters address
2. Frontend calls Google Geocoding API
3. Coordinates returned
4. Stored in business location
5. Used for proximity search
```

### Email Service Integration (Planned)

```
Purpose: Notifications, verification
Flow:
1. Backend triggers email event
2. Email service queues message
3. Template rendered
4. Email sent to user
5. Delivery tracking
```

---

## Monitoring & Observability

### Logging Strategy

```
Frontend:
- Error logging (Sentry planned)
- User analytics (Google Analytics planned)
- Performance monitoring

Backend:
- Request/response logging
- Error tracking
- Performance metrics
- Database query logging

Database:
- Query performance
- Slow query log
- Connection pool metrics
```

### Health Checks

```
GET /health
→ Database connection
→ External service availability
→ System resources
→ Response time

GET /metrics
→ Request count
→ Error rate
→ Response time p50/p95/p99
→ Active connections
```

---

## Disaster Recovery

### Backup Strategy

**Database:**
- Daily automated backups (MongoDB Atlas)
- Point-in-time recovery
- Geo-redundant storage

**Code:**
- Git version control
- Multiple remote repositories
- Tagged releases

**Images:**
- Cloudinary backup
- Redundant storage

### Recovery Procedures

**Data Loss:**
1. Identify affected timeframe
2. Restore from backup
3. Verify data integrity
4. Resume operations

**Service Outage:**
1. Switch to backup instance
2. Investigate root cause
3. Implement fix
4. Post-mortem analysis

---

## Future Architecture Enhancements

### Planned Improvements

1. **Microservices** - Split monolith into services
2. **Message Queue** - RabbitMQ/Redis for async tasks
3. **Caching Layer** - Redis for performance
4. **GraphQL** - Alternative to REST API
5. **WebSockets** - Real-time notifications
6. **CDN** - CloudFlare integration
7. **Containerization** - Docker deployment
8. **Orchestration** - Kubernetes for scaling

---

**Document Version:** 1.0.0  
**Last Updated:** January 2026
