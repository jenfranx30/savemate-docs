# SaveMate Project Repositories

Complete overview of all SaveMate project repositories and their status.

**Last Updated:** November 24, 2025  
**Project Status:** Phase 5 Complete (Backend) | Phase 6 In Planning

---

## 📋 Table of Contents

1. [Repository Overview](#repository-overview)
2. [Backend Repository](#backend-repository)
3. [Documentation Repository](#documentation-repository)
4. [Frontend Repository (Future)](#frontend-repository-future)
5. [Repository Relationships](#repository-relationships)
6. [Development Workflow](#development-workflow)

---

## 🗂️ Repository Overview

SaveMate uses a multi-repository architecture for better organization and separation of concerns.

| Repository | Status | Purpose | Tech Stack |
|-----------|--------|---------|------------|
| **savemate-backend** | ✅ Active | REST API Backend | FastAPI, MongoDB, Python |
| **savemate-docs** | ✅ Active | Project Documentation | Markdown |
| **savemate-frontend** | 🔄 Planned | Web Application | React, Vite, Tailwind |

**Total Repositories:** 3 (2 active, 1 planned)

---

## 1️⃣ Backend Repository

### Repository Details

**Name:** `savemate-backend`  
**URL:** https://github.com/jenfranx30/savemate-backend  
**Status:** ✅ Phase 5 Complete  
**Language:** Python 3.8+  
**Framework:** FastAPI 0.104+

### Description

REST API backend for SaveMate local deals platform. Provides complete functionality for user authentication, deal management, business profiles, favorites, and reviews.

### Tech Stack

- **Framework:** FastAPI
- **Database:** MongoDB Atlas (Cloud)
- **ODM:** Beanie (Pydantic-based)
- **Authentication:** JWT (JSON Web Tokens)
- **Password Hashing:** bcrypt
- **Validation:** Pydantic
- **Server:** Uvicorn (ASGI)
- **Documentation:** Swagger UI, ReDoc

### Project Structure

```
savemate-backend/
├── app/
│   ├── main.py                    # Application entry point
│   ├── config.py                  # Configuration
│   ├── database.py                # Database connection
│   ├── core/                      # Core utilities
│   │   └── security.py            # JWT, password hashing
│   ├── models/                    # Database models (6 models)
│   │   ├── user.py
│   │   ├── deal.py
│   │   ├── business.py
│   │   ├── category.py
│   │   ├── favorite.py
│   │   └── review.py
│   ├── schemas/                   # Pydantic schemas (5 schemas)
│   │   ├── auth_schema.py
│   │   ├── deal_schema.py
│   │   ├── business_schema.py
│   │   ├── favorite_schema.py
│   │   └── review_schema.py
│   └── api/
│       └── routes/                # API endpoints (5 route files)
│           ├── auth.py            # 3 endpoints
│           ├── deals.py           # 6 endpoints
│           ├── businesses.py      # 7 endpoints
│           ├── favorites.py       # 4 endpoints
│           └── reviews.py         # 5 endpoints
├── .env                           # Environment variables (not in Git)
├── .gitignore
├── requirements.txt               # Dependencies
└── README.md                      # Documentation
```

### Key Features

#### ✅ Phase 1: Project Setup
- FastAPI application structure
- MongoDB Atlas integration
- Environment configuration
- Git repository initialization

#### ✅ Phase 2: Database Models
- User model (authentication)
- Deal model (promotions)
- Business model (profiles)
- Category model (classification)
- Favorite model (user-deal relationship)
- Review model (ratings)

#### ✅ Phase 3: Authentication System
- User registration
- Email/username login
- JWT access tokens (30 min expiry)
- JWT refresh tokens (7 day expiry)
- Password hashing (bcrypt)
- Token refresh mechanism

#### ✅ Phase 4: Deals Management
- Create, read, update, delete deals
- Advanced filtering (category, city, price, discount)
- Full-text search
- Pagination and sorting
- Location tracking
- View and save counters
- Deal expiration handling
- 10 deal categories

#### ✅ Phase 5: Business, Favorites & Reviews
- Business profile CRUD operations
- User favorites system
- Reviews and ratings (1-5 stars)
- Business ratings calculation
- Operating hours support
- Duplicate review prevention
- Helpful votes system

### API Endpoints

**Total:** 25 endpoints across 5 resources

| Resource | Endpoints | Authentication |
|----------|-----------|----------------|
| Authentication | 3 | Mixed |
| Deals | 6 | Mixed |
| Businesses | 7 | Mixed |
| Favorites | 4 | Required |
| Reviews | 5 | Mixed |

### Database Collections

| Collection | Purpose | Documents (Est.) |
|------------|---------|------------------|
| users | User accounts | 10,000+ |
| deals | Deal listings | 50,000+ |
| businesses | Business profiles | 5,000+ |
| categories | Deal categories | 20 |
| favorites | User favorites | 100,000+ |
| reviews | Deal reviews | 75,000+ |

### Development Status

**Current Phase:** Phase 5 Complete  
**Next Phase:** Phase 6 (API Integration)

**Completed:**
- ✅ 25 API endpoints
- ✅ 6 database models
- ✅ JWT authentication
- ✅ CRUD operations
- ✅ Filtering & search
- ✅ Pagination & sorting

**In Progress:**
- 🔄 Polish API integration planning
- 🔄 Frontend architecture design

**Planned:**
- 📋 Google Places API integration
- 📋 Allegro API integration
- 📋 Email notifications
- 📋 Image upload (AWS S3)
- 📋 Admin dashboard
- 📋 Caching layer (Redis)

### Installation

```bash
# Clone repository
git clone https://github.com/jenfranx30/savemate-backend.git
cd savemate-backend

# Create virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Configure environment
cp .env.example .env  # Edit with your settings

# Run server
uvicorn app.main:app --reload
```

### Documentation

- **Swagger UI:** http://localhost:8000/docs
- **ReDoc:** http://localhost:8000/redoc
- **README:** Complete setup and usage guide
- **API Docs:** Comprehensive endpoint documentation

### Recent Commits

- Phase 5 Complete: Business Profiles, Favorites & Reviews (Nov 24, 2025)
- Phase 4 Complete: Deals Management System (Nov 24, 2025)
- Phase 3 Complete: Authentication System (Nov 23, 2025)

---

## 2️⃣ Documentation Repository

### Repository Details

**Name:** `savemate-docs`  
**URL:** https://github.com/jenfranx30/savemate-docs  
**Status:** ✅ Active & Updated  
**Format:** Markdown

### Description

Centralized documentation for the SaveMate project including API documentation, database schemas, architecture diagrams, and project planning.

### Documentation Files

| File | Description | Last Updated |
|------|-------------|--------------|
| **SaveMate_API_Documentation.md** | Complete API reference | Nov 24, 2025 |
| **SaveMate_Database_Schema.md** | Database schema & relationships | Nov 24, 2025 |
| **savemate-architecture-diagram.md** | System architecture | Nov 24, 2025 |
| **PROJECT_REPOSITORIES.md** | Repository overview (this file) | Nov 24, 2025 |
| **SaveMate_Updated_Trello_Tasks.md** | Trello board tasks | Nov 23, 2025 |
| **savemate-best-practices.md** | Development guidelines | Nov 22, 2025 |
| **savemate_stack_analysis.md** | Technology stack analysis | Nov 21, 2025 |

### Documentation Structure

```
savemate-docs/
├── SaveMate_API_Documentation.md      # API reference (all 25 endpoints)
├── SaveMate_Database_Schema.md        # Database schema (6 collections)
├── savemate-architecture-diagram.md   # Architecture diagrams
├── PROJECT_REPOSITORIES.md            # This file
├── SaveMate_Updated_Trello_Tasks.md   # Project tasks
├── savemate-best-practices.md         # Development guidelines
└── savemate_stack_analysis.md         # Tech stack details
```

### Key Documentation

#### API Documentation
- Complete endpoint reference
- Request/response examples
- Authentication guide
- Error codes
- Query parameters
- Validation rules

#### Database Schema
- 6 collection schemas
- Field descriptions
- Relationships diagram
- Indexes
- Example documents
- Storage estimates

#### Architecture Diagrams
- High-level architecture
- Backend architecture
- Database architecture
- Authentication flow
- Data flow examples
- Technology stack
- Future architecture

### Usage

All documentation is written in Markdown and can be viewed directly on GitHub or cloned locally:

```bash
git clone https://github.com/jenfranx30/savemate-docs.git
cd savemate-docs
```

---

## 3️⃣ Frontend Repository (Future)

### Repository Details

**Name:** `savemate-frontend` (Planned)  
**URL:** https://github.com/jenfranx30/savemate-frontend (TBD)  
**Status:** 🔄 Planning Phase  
**Planned Start:** Phase 7

### Planned Tech Stack

**Recommended:** React 18 + Vite + Tailwind CSS

| Component | Technology | Purpose |
|-----------|------------|---------|
| Framework | React 18 | UI library |
| Build Tool | Vite | Fast build tool |
| Styling | Tailwind CSS | Utility-first CSS |
| Routing | React Router | Navigation |
| State Management | React Query + Context | Data fetching & state |
| HTTP Client | Axios | API calls |
| UI Components | Shadcn/ui | Component library |
| Form Handling | React Hook Form | Form validation |
| Authentication | JWT + Local Storage | Auth management |

### Planned Features

#### User Interface
- 🏠 Home page with featured deals
- 🔍 Advanced search with filters
- 📱 Deal details page
- ⭐ User favorites dashboard
- 🏪 Business profiles
- 👤 User profile & settings
- 📝 Review submission
- 🔐 Login/Register pages

#### Features
- Responsive design (mobile-first)
- Dark mode support
- Real-time search
- Map integration (Google Maps)
- Image galleries
- Rating displays
- Share functionality
- Progressive Web App (PWA)

### Planned Structure

```
savemate-frontend/
├── src/
│   ├── components/         # Reusable components
│   │   ├── common/        # Buttons, inputs, cards
│   │   ├── deals/         # Deal-related components
│   │   ├── business/      # Business components
│   │   └── layout/        # Header, footer, nav
│   ├── pages/             # Page components
│   │   ├── Home.jsx
│   │   ├── Deals.jsx
│   │   ├── DealDetail.jsx
│   │   ├── Favorites.jsx
│   │   ├── Business.jsx
│   │   ├── Profile.jsx
│   │   └── Auth/
│   ├── services/          # API services
│   │   ├── api.js
│   │   ├── auth.js
│   │   ├── deals.js
│   │   └── business.js
│   ├── hooks/             # Custom React hooks
│   ├── context/           # Context providers
│   ├── utils/             # Utility functions
│   └── App.jsx
├── public/
├── package.json
└── README.md
```

### Timeline

- **Phase 7:** Frontend Setup & Basic Pages (Planned)
- **Phase 8:** Advanced Features & Integration (Planned)
- **Phase 9:** Testing & Deployment (Planned)

---

## 🔗 Repository Relationships

```
┌─────────────────────────────────────────────────────────┐
│                   SaveMate Project                      │
└─────────────────────────────────────────────────────────┘
                            │
              ┌─────────────┼─────────────┐
              │             │             │
              ▼             ▼             ▼
┌────────────────┐  ┌────────────┐  ┌─────────────┐
│  savemate-     │  │ savemate-  │  │ savemate-   │
│   backend      │  │   docs     │  │  frontend   │
│                │  │            │  │  (Planned)  │
│  ✅ Active     │  │ ✅ Active  │  │  🔄 Future  │
└────────┬───────┘  └────────────┘  └──────┬──────┘
         │                                   │
         │         API Communication         │
         └───────────────────────────────────┘
                  (REST / JSON)
```

### Communication Flow

```
Frontend (React)
    │
    │ HTTP/HTTPS Requests
    │ (JSON payloads)
    │
    ▼
Backend API (FastAPI)
    │
    │ MongoDB Queries
    │ (Beanie ODM)
    │
    ▼
MongoDB Atlas (Database)
```

---

## 🔄 Development Workflow

### Branch Strategy

All repositories use the same branching strategy:

| Branch | Purpose | Protected |
|--------|---------|-----------|
| `main` | Production-ready code | ✅ Yes |
| `develop` | Integration branch | 🔄 Future |
| `feature/*` | New features | No |
| `bugfix/*` | Bug fixes | No |
| `hotfix/*` | Critical fixes | No |

### Commit Convention

```
<type>: <subject>

<body>

<footer>
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes
- `refactor`: Code refactoring
- `test`: Test additions/changes
- `chore`: Build/config changes

**Example:**
```
feat: Add business profile CRUD endpoints

- Implemented create, read, update, delete operations
- Added business ratings calculation
- Included operating hours support

Closes #12
```

### Release Process

1. Complete phase implementation
2. Update documentation
3. Test all endpoints
4. Commit with descriptive message
5. Push to GitHub
6. Tag release (v1.0.0, v1.1.0, etc.)

---

## 📊 Project Statistics

### Overall Progress

| Phase | Status | Repositories |
|-------|--------|--------------|
| 1-5 | ✅ Complete | Backend, Docs |
| 6 | 🔄 Planning | Backend, Docs |
| 7+ | 📋 Planned | All 3 |

### Code Statistics (Backend)

- **Total Lines:** ~5,000+
- **Python Files:** 20+
- **API Endpoints:** 25
- **Database Models:** 6
- **Test Coverage:** Coming soon

### Documentation Statistics

- **Documentation Files:** 7
- **Total Pages:** ~100+
- **API Examples:** 50+
- **Diagrams:** 10+

---

## 🎯 Future Plans

### Phase 6: API Integration
- Google Places API for Polish businesses
- Allegro API for product deals
- OpenStreetMap for geocoding
- Email notifications (SendGrid)

### Phase 7: Frontend Development
- Create savemate-frontend repository
- Implement React application
- Connect to backend API
- Deploy to production

### Phase 8: Advanced Features
- Redis caching
- Background tasks (Celery)
- Real-time updates (WebSocket)
- Mobile app API
- Analytics dashboard

### Phase 9: Production Deployment
- AWS/Railway deployment
- CI/CD pipeline
- Monitoring & logging
- Performance optimization
- Security hardening

---

## 🤝 Contributing

### For Team Members

1. Clone the repository you want to work on
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Create a pull request
6. Wait for code review
7. Merge after approval

### Repository Access

All team members have access to:
- ✅ savemate-backend (read/write)
- ✅ savemate-docs (read/write)
- 🔄 savemate-frontend (coming soon)

---

## 📞 Support

### Repository Issues

Report issues in the respective repository:
- Backend bugs: https://github.com/jenfranx30/savemate-backend/issues
- Documentation: https://github.com/jenfranx30/savemate-docs/issues

### Contact

- **Project Lead:** Rustam Islamov
- **Team:** 5 members
- **University:** WSB Dąbrowa Górnicza
- **Course:** Agile Project Management
- **Methodology:** Kanban (Trello)

---

## 🎓 Academic Context

**University:** WSB University in Dąbrowa Górnicza  
**Program:** Master's in Computer Science
**Course:** Agile Project Management  
**Professor:** Prof. Dawid Jurczyński  
**Timeline:** November 2025 - January 2026  
**Methodology:** Kanban

---

**Last Updated:** November 24, 2025  
**Version:** 1.0.0 (Phase 5 Complete)  
**Status:** Active Development

---

For detailed documentation, see the respective repository READMEs and the savemate-docs repository.
