# SaveMate - System Architecture Diagram

## Complete System Architecture

```mermaid
graph TB
    subgraph "Client Layer"
        WebBrowser[Web Browser]
        MobileDevice[Mobile Device]
    end

    subgraph "Frontend - React 18"
        ReactApp[React Application]
        Router[React Router v6]
        StateManagement[Context API / Redux Toolkit]
        UIComponents[UI Components<br/>Tailwind CSS]
        HTTPClient[Axios HTTP Client]
        Maps[React Leaflet<br/>Map Integration]
    end

    subgraph "Backend - Node.js + Express"
        ExpressAPI[Express.js API Server]
        
        subgraph "Middleware Layer"
            Auth[JWT Authentication]
            Validation[Input Validation]
            ErrorHandler[Error Handler]
            RateLimiter[Rate Limiter]
            Security[Security<br/>Helmet.js]
        end
        
        subgraph "Controllers"
            UserController[User Controller]
            DealController[Deal Controller]
            BusinessController[Business Controller]
            AuthController[Auth Controller]
        end
        
        subgraph "Services"
            UserService[User Service]
            DealService[Deal Service]
            BusinessService[Business Service]
            EmailService[Email Service]
        end
    end

    subgraph "Database Layer"
        MongoDB[(MongoDB<br/>Database)]
        
        subgraph "Collections"
            Users[Users Collection]
            Deals[Deals Collection]
            Businesses[Business Collection]
        end
    end

    subgraph "External Services"
        Cloudinary[Cloudinary<br/>Image Storage]
        GoogleMaps[Google Maps API<br/>or OpenStreetMap]
        EmailProvider[Email Service<br/>SendGrid/Nodemailer]
    end

    subgraph "File Upload"
        Multer[Multer<br/>File Upload Handler]
    end

    %% Client to Frontend connections
    WebBrowser --> ReactApp
    MobileDevice --> ReactApp
    
    %% Frontend internal connections
    ReactApp --> Router
    ReactApp --> StateManagement
    ReactApp --> UIComponents
    ReactApp --> HTTPClient
    ReactApp --> Maps
    
    %% Frontend to Backend
    HTTPClient --> ExpressAPI
    
    %% Backend middleware flow
    ExpressAPI --> Auth
    ExpressAPI --> Validation
    ExpressAPI --> ErrorHandler
    ExpressAPI --> RateLimiter
    ExpressAPI --> Security
    
    %% Middleware to Controllers
    Auth --> UserController
    Auth --> DealController
    Auth --> BusinessController
    Auth --> AuthController
    
    %% Controllers to Services
    UserController --> UserService
    DealController --> DealService
    BusinessController --> BusinessService
    AuthController --> UserService
    
    %% Services to Database
    UserService --> MongoDB
    DealService --> MongoDB
    BusinessService --> MongoDB
    
    %% Database Collections
    MongoDB --> Users
    MongoDB --> Deals
    MongoDB --> Businesses
    
    %% External Services connections
    DealService --> Cloudinary
    BusinessService --> Cloudinary
    Multer --> Cloudinary
    Maps --> GoogleMaps
    UserService --> EmailProvider
    
    %% File Upload flow
    ExpressAPI --> Multer

    classDef frontend fill:#61dafb,stroke:#333,stroke-width:2px,color:#000
    classDef backend fill:#68a063,stroke:#333,stroke-width:2px,color:#fff
    classDef database fill:#4db33d,stroke:#333,stroke-width:2px,color:#fff
    classDef external fill:#f39c12,stroke:#333,stroke-width:2px,color:#000
    
    class ReactApp,Router,StateManagement,UIComponents,HTTPClient,Maps frontend
    class ExpressAPI,Auth,Validation,ErrorHandler,RateLimiter,Security,UserController,DealController,BusinessController,AuthController,UserService,DealService,BusinessService,EmailService,Multer backend
    class MongoDB,Users,Deals,Businesses database
    class Cloudinary,GoogleMaps,EmailProvider external
```

## Data Flow Architecture

```mermaid
sequenceDiagram
    participant U as User
    participant F as Frontend<br/>(React)
    participant A as API Gateway<br/>(Express)
    participant M as Middleware<br/>(Auth/Validation)
    participant C as Controller
    participant S as Service
    participant DB as MongoDB
    participant E as External Services<br/>(Cloudinary)

    U->>F: Browse Deals
    F->>A: GET /api/v1/deals
    A->>M: Validate Request
    M->>M: Check JWT Token
    M->>C: Forward Request
    C->>S: Process Request
    S->>DB: Query Deals
    DB-->>S: Return Data
    S->>S: Format Response
    S-->>C: Return Deals
    C-->>A: Send Response
    A-->>F: JSON Response
    F-->>U: Display Deals

    Note over U,E: User Creates New Deal

    U->>F: Submit Deal Form + Images
    F->>A: POST /api/v1/deals
    A->>M: Validate & Authenticate
    M->>M: Verify JWT
    M->>M: Validate Input
    M->>C: Forward Valid Request
    C->>S: Process Deal Creation
    S->>E: Upload Images to Cloudinary
    E-->>S: Return Image URLs
    S->>DB: Save Deal with Image URLs
    DB-->>S: Confirm Save
    S-->>C: Return Created Deal
    C-->>A: Send Response
    A-->>F: JSON Response (201)
    F-->>U: Show Success + Deal Details
```

## Authentication Flow

```mermaid
sequenceDiagram
    participant U as User
    participant F as Frontend
    participant A as Auth API
    participant M as Auth Middleware
    participant DB as MongoDB
    participant J as JWT Service

    Note over U,J: User Registration

    U->>F: Enter Registration Info
    F->>A: POST /api/v1/auth/register
    A->>M: Validate Input
    M->>M: Hash Password (Bcrypt)
    M->>DB: Save User
    DB-->>M: User Created
    M->>J: Generate JWT Token
    J-->>A: Return Token
    A-->>F: Token + User Data
    F->>F: Store Token (Cookie/Storage)
    F-->>U: Redirect to Dashboard

    Note over U,J: User Login

    U->>F: Enter Credentials
    F->>A: POST /api/v1/auth/login
    A->>M: Validate Input
    M->>DB: Find User by Email
    DB-->>M: User Data
    M->>M: Compare Password (Bcrypt)
    M->>J: Generate JWT Token
    J-->>A: Return Token
    A-->>F: Token + User Data
    F->>F: Store Token
    F-->>U: Redirect to Dashboard

    Note over U,J: Protected Route Access

    U->>F: Access Protected Page
    F->>F: Check Token Exists
    F->>A: GET /api/v1/protected<br/>Header: Authorization Bearer Token
    A->>M: Verify JWT Token
    M->>J: Decode & Validate Token
    J-->>M: Token Valid + User ID
    M->>DB: Fetch User
    DB-->>M: User Data
    M-->>A: Attach User to Request
    A-->>F: Protected Data
    F-->>U: Display Protected Content
```

## Database Schema Relationships

```mermaid
erDiagram
    USER ||--o{ DEAL : creates
    USER {
        ObjectId _id PK
        string name
        string email UK
        string password
        string role
        string avatar
        object location
        array preferences
        date createdAt
        date updatedAt
    }
    
    DEAL ||--|| BUSINESS : "belongs to"
    DEAL {
        ObjectId _id PK
        string title
        string description
        number price
        number discount
        ObjectId business FK
        ObjectId createdBy FK
        array images
        object location
        date expiresAt
        boolean isActive
        array categories
        date createdAt
        date updatedAt
    }
    
    BUSINESS ||--o{ DEAL : offers
    BUSINESS {
        ObjectId _id PK
        string name
        string description
        string email
        string phone
        object address
        object location
        array images
        string website
        array categories
        ObjectId owner FK
        date createdAt
        date updatedAt
    }
    
    USER ||--o{ BUSINESS : owns
```

## Folder Structure

### Backend Structure
```
backend/
├── config/
│   ├── db.js                 # MongoDB connection
│   ├── auth.js               # JWT configuration
│   └── cloudinary.js         # Cloudinary setup
├── controllers/
│   ├── authController.js     # Authentication logic
│   ├── userController.js     # User CRUD operations
│   ├── dealController.js     # Deal CRUD operations
│   └── businessController.js # Business CRUD operations
├── middleware/
│   ├── auth.js               # JWT authentication
│   ├── error.js              # Error handling
│   ├── validation.js         # Input validation
│   ├── upload.js             # File upload (Multer)
│   └── rateLimiter.js        # Rate limiting
├── models/
│   ├── User.js               # User schema
│   ├── Deal.js               # Deal schema
│   └── Business.js           # Business schema
├── routes/
│   ├── authRoutes.js         # Auth endpoints
│   ├── userRoutes.js         # User endpoints
│   ├── dealRoutes.js         # Deal endpoints
│   └── businessRoutes.js     # Business endpoints
├── services/
│   ├── userService.js        # User business logic
│   ├── dealService.js        # Deal business logic
│   ├── businessService.js    # Business business logic
│   └── emailService.js       # Email functionality
├── utils/
│   ├── ApiError.js           # Custom error class
│   ├── ApiResponse.js        # Response formatter
│   ├── asyncHandler.js       # Async wrapper
│   └── validators.js         # Validation helpers
├── .env                       # Environment variables
├── .gitignore
├── package.json
└── server.js                  # Entry point
```

### Frontend Structure
```
frontend/
├── public/
│   ├── index.html
│   └── favicon.ico
├── src/
│   ├── components/
│   │   ├── common/
│   │   │   ├── Header.jsx
│   │   │   ├── Footer.jsx
│   │   │   ├── Loader.jsx
│   │   │   └── ErrorBoundary.jsx
│   │   ├── deals/
│   │   │   ├── DealCard.jsx
│   │   │   ├── DealList.jsx
│   │   │   ├── DealDetail.jsx
│   │   │   └── DealForm.jsx
│   │   ├── business/
│   │   │   ├── BusinessCard.jsx
│   │   │   ├── BusinessList.jsx
│   │   │   └── BusinessProfile.jsx
│   │   ├── auth/
│   │   │   ├── Login.jsx
│   │   │   ├── Register.jsx
│   │   │   └── PrivateRoute.jsx
│   │   └── map/
│   │       ├── MapView.jsx
│   │       └── LocationPicker.jsx
│   ├── pages/
│   │   ├── Home.jsx
│   │   ├── Deals.jsx
│   │   ├── DealDetails.jsx
│   │   ├── CreateDeal.jsx
│   │   ├── Profile.jsx
│   │   ├── Business.jsx
│   │   └── NotFound.jsx
│   ├── context/
│   │   ├── AuthContext.jsx
│   │   └── DealContext.jsx
│   ├── hooks/
│   │   ├── useAuth.js
│   │   ├── useDeals.js
│   │   └── useLocation.js
│   ├── services/
│   │   ├── api.js             # Axios configuration
│   │   ├── authService.js     # Auth API calls
│   │   ├── dealService.js     # Deal API calls
│   │   └── businessService.js # Business API calls
│   ├── utils/
│   │   ├── formatters.js      # Data formatting
│   │   ├── validators.js      # Form validation
│   │   └── constants.js       # App constants
│   ├── styles/
│   │   └── index.css          # Tailwind imports
│   ├── App.jsx
│   ├── index.js
│   └── routes.js
├── .env
├── .gitignore
├── package.json
├── tailwind.config.js
└── vite.config.js
```

## API Endpoints Structure

### Authentication Endpoints
```
POST   /api/v1/auth/register          # User registration
POST   /api/v1/auth/login             # User login
POST   /api/v1/auth/logout            # User logout
GET    /api/v1/auth/me                # Get current user
POST   /api/v1/auth/forgot-password   # Request password reset
PUT    /api/v1/auth/reset-password    # Reset password
```

### User Endpoints
```
GET    /api/v1/users                  # Get all users (admin)
GET    /api/v1/users/:id              # Get user by ID
PUT    /api/v1/users/:id              # Update user
DELETE /api/v1/users/:id              # Delete user
PUT    /api/v1/users/:id/avatar       # Update user avatar
```

### Deal Endpoints
```
GET    /api/v1/deals                  # Get all deals (with filters)
GET    /api/v1/deals/:id              # Get deal by ID
POST   /api/v1/deals                  # Create new deal (auth required)
PUT    /api/v1/deals/:id              # Update deal (auth required)
DELETE /api/v1/deals/:id              # Delete deal (auth required)
GET    /api/v1/deals/nearby           # Get deals near location
GET    /api/v1/deals/category/:cat    # Get deals by category
```

### Business Endpoints
```
GET    /api/v1/businesses             # Get all businesses
GET    /api/v1/businesses/:id         # Get business by ID
POST   /api/v1/businesses             # Create business (auth required)
PUT    /api/v1/businesses/:id         # Update business (auth required)
DELETE /api/v1/businesses/:id         # Delete business (auth required)
GET    /api/v1/businesses/:id/deals   # Get all deals for a business
```

## Deployment Architecture

```mermaid
graph TB
    subgraph "Development"
        DevFrontend[Local React Dev Server<br/>Vite]
        DevBackend[Local Node Server]
        DevDB[Local MongoDB]
    end

    subgraph "Production Environment"
        subgraph "Frontend Hosting - Vercel/Netlify"
            CDN[CDN<br/>Static Assets]
            ReactBuild[React Production Build]
        end
        
        subgraph "Backend Hosting - Render/Railway"
            LoadBalancer[Load Balancer]
            NodeServer1[Node.js Instance 1]
            NodeServer2[Node.js Instance 2]
        end
        
        subgraph "Database - MongoDB Atlas"
            PrimaryDB[(Primary MongoDB)]
            ReplicaDB1[(Replica 1)]
            ReplicaDB2[(Replica 2)]
        end
        
        subgraph "External Services"
            CloudinaryProd[Cloudinary CDN]
            MapService[Map Service API]
        end
    end

    Users[End Users] --> CDN
    CDN --> ReactBuild
    ReactBuild --> LoadBalancer
    LoadBalancer --> NodeServer1
    LoadBalancer --> NodeServer2
    NodeServer1 --> PrimaryDB
    NodeServer2 --> PrimaryDB
    PrimaryDB --> ReplicaDB1
    PrimaryDB --> ReplicaDB2
    NodeServer1 --> CloudinaryProd
    NodeServer2 --> CloudinaryProd
    ReactBuild --> MapService

    classDef dev fill:#e74c3c,stroke:#333,stroke-width:2px,color:#fff
    classDef prod fill:#27ae60,stroke:#333,stroke-width:2px,color:#fff
    
    class DevFrontend,DevBackend,DevDB dev
    class CDN,ReactBuild,LoadBalancer,NodeServer1,NodeServer2,PrimaryDB,ReplicaDB1,ReplicaDB2,CloudinaryProd,MapService prod
```

## Technology Stack Summary

### Frontend Technologies
- **Framework**: React 18
- **Routing**: React Router v6
- **State Management**: Context API / Redux Toolkit
- **Styling**: Tailwind CSS
- **HTTP Client**: Axios
- **Maps**: React Leaflet or Google Maps API
- **Image Display**: Cloudinary
- **Form Handling**: React Hook Form
- **Date Handling**: date-fns
- **Build Tool**: Vite

### Backend Technologies
- **Runtime**: Node.js v18+
- **Framework**: Express.js
- **Database**: MongoDB (Mongoose ODM)
- **Authentication**: JWT (JSON Web Tokens)
- **Password Hashing**: Bcrypt
- **File Upload**: Multer
- **Image Storage**: Cloudinary
- **Validation**: Express Validator
- **Security**: Helmet.js, express-mongo-sanitize
- **Rate Limiting**: express-rate-limit
- **CORS**: cors middleware

### DevOps & Tools
- **Version Control**: Git & GitHub
- **API Testing**: Postman
- **Code Quality**: ESLint, Prettier
- **Testing**: Jest, React Testing Library
- **CI/CD**: GitHub Actions
- **Deployment**: Vercel (Frontend), Render/Railway (Backend)
- **Database Hosting**: MongoDB Atlas

---

## Notes for Implementation

1. **Environment Variables**: Always use .env files and never commit them to version control
2. **Security**: Implement rate limiting, input validation, and sanitization from the start
3. **Error Handling**: Use centralized error handling middleware
4. **Logging**: Implement proper logging for debugging and monitoring
5. **Testing**: Write tests as you develop, not after
6. **Documentation**: Keep API documentation updated using tools like Swagger
7. **Code Reviews**: Use pull requests and require reviews before merging
8. **Performance**: Optimize queries, implement caching where appropriate
9. **Scalability**: Design with growth in mind - use proper indexing, pagination
10. **Backup**: Regular database backups are essential
