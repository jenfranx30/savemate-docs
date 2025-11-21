# SaveMate - MERN Stack Best Practices Documentation

## Project Overview
SaveMate is a local deals web application built using the MERN stack (MongoDB, Express.js, React 18, Node.js).

---

## 1. FRONTEND BEST PRACTICES (React 18)

### Component Architecture
- **Use Component-Based Design**: Build small, reusable components
- **Follow Single Responsibility Principle**: Each component should have one clear purpose
- **Implement Smart/Dumb Component Pattern**:
  - Smart components (containers): Handle logic and state
  - Dumb components (presentational): Focus on UI rendering

### State Management (Context API/Redux Toolkit)
- **Use Context API for**: Global UI state, theme, authentication
- **Use Redux Toolkit for**: Complex state logic, data caching
- **Keep state minimal**: Only store what's necessary
- **Normalize state shape**: Avoid deeply nested structures

### React Router v6
- **Implement Nested Routes**: For complex layouts
- **Use Protected Routes**: For authenticated pages
- **Implement Lazy Loading**: Split code with React.lazy() and Suspense
- **Use Outlet Component**: For nested route rendering

### Performance Optimization
- **Use React.memo**: Prevent unnecessary re-renders
- **Implement useMemo and useCallback**: Optimize expensive calculations
- **Code Splitting**: Use dynamic imports for route-based splitting
- **Optimize Images**: Use WebP format, lazy loading with Cloudinary

### Styling with Tailwind CSS
- **Use Utility-First Approach**: Compose designs with utility classes
- **Create Custom Components**: For repeated patterns
- **Implement Dark Mode**: Using Tailwind's dark: variant
- **Mobile-First Design**: Start with mobile, then scale up

---

## 2. BACKEND BEST PRACTICES (Node.js + Express.js)

### Project Structure
```
backend/
├── config/           # Configuration files
│   ├── db.js        # Database connection
│   └── auth.js      # JWT configuration
├── controllers/      # Request handlers
├── middleware/       # Custom middleware
│   ├── auth.js      # Authentication middleware
│   ├── error.js     # Error handling
│   └── validate.js  # Input validation
├── models/          # Mongoose schemas
├── routes/          # API routes
├── utils/           # Helper functions
├── .env             # Environment variables
└── server.js        # Entry point
```

### API Design (RESTful)
- **Use Proper HTTP Methods**:
  - GET: Retrieve data
  - POST: Create new resources
  - PUT/PATCH: Update resources
  - DELETE: Remove resources
- **Implement Versioning**: `/api/v1/deals`
- **Use Meaningful Endpoints**: `/api/v1/deals/:id` not `/api/v1/get-deal`
- **Return Appropriate Status Codes**:
  - 200: Success
  - 201: Created
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 500: Internal Server Error

### Middleware Best Practices
- **Order Matters**: Place middleware in correct sequence
- **Use Express Built-ins**:
  - express.json(): Parse JSON bodies
  - express.urlencoded(): Parse URL-encoded data
- **Implement Custom Middleware**:
  - Authentication/Authorization
  - Error handling
  - Request logging
  - Input validation

### Asynchronous Operations
- **Use async/await**: Cleaner than callbacks or promises
- **Implement try-catch blocks**: Handle errors properly
- **Use Promise.all()**: For concurrent operations
- **Avoid blocking operations**: Keep Event Loop free

---

## 3. DATABASE BEST PRACTICES (MongoDB + Mongoose)

### Schema Design
- **Embed vs Reference**:
  - Embed: For one-to-few relationships (e.g., user address)
  - Reference: For one-to-many or many-to-many (e.g., user deals)
- **Use Schema Validation**: Define required fields, types, and constraints
- **Add Timestamps**: Use `{ timestamps: true }` for createdAt/updatedAt
- **Create Indexes**: Improve query performance on frequently searched fields

### Mongoose Models
```javascript
const dealSchema = new mongoose.Schema({
  title: {
    type: String,
    required: [true, 'Title is required'],
    trim: true,
    maxlength: [100, 'Title cannot exceed 100 characters']
  },
  description: {
    type: String,
    required: [true, 'Description is required'],
    maxlength: [500, 'Description cannot exceed 500 characters']
  },
  price: {
    type: Number,
    required: [true, 'Price is required'],
    min: [0, 'Price cannot be negative']
  },
  discount: {
    type: Number,
    min: 0,
    max: 100
  },
  business: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Business',
    required: true
  },
  images: [{
    public_id: String,
    url: String
  }],
  location: {
    type: {
      type: String,
      enum: ['Point'],
      default: 'Point'
    },
    coordinates: {
      type: [Number],
      required: true
    }
  },
  expiresAt: {
    type: Date,
    required: true
  },
  isActive: {
    type: Boolean,
    default: true
  }
}, {
  timestamps: true
});

// Create geospatial index
dealSchema.index({ location: '2dsphere' });
```

### Query Optimization
- **Use Lean Queries**: `.lean()` for read-only operations
- **Implement Pagination**: Limit results with skip() and limit()
- **Use Projections**: Select only needed fields
- **Avoid N+1 Queries**: Use populate() wisely or aggregation

### Connection Management
- **Use Connection Pooling**: Default size is 5, adjust as needed
- **Handle Connection Errors**: Implement retry logic
- **Close Connections Gracefully**: On app shutdown

---

## 4. SECURITY BEST PRACTICES

### Authentication & Authorization (JWT)
- **Store JWT in HttpOnly Cookies**: Prevent XSS attacks
- **Implement Token Expiration**: Short-lived access tokens (15-30 min)
- **Use Refresh Tokens**: For long-term sessions
- **Hash Passwords with Bcrypt**: Use salt rounds of 10-12
- **Implement Multi-Factor Authentication (MFA)**: For sensitive operations

### Input Validation & Sanitization
- **Validate All Inputs**: Use express-validator or Joi
- **Sanitize User Input**: Remove dangerous characters
- **Use Mongoose Sanitize**: Prevent NoSQL injection
```javascript
const mongoSanitize = require('express-mongo-sanitize');
app.use(mongoSanitize());
```

### HTTP Headers Security
- **Use Helmet.js**: Set security-related HTTP headers
```javascript
const helmet = require('helmet');
app.use(helmet());
```

### Rate Limiting
- **Implement Rate Limiting**: Prevent DoS and brute force attacks
```javascript
const rateLimit = require('express-rate-limit');
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100 // limit each IP to 100 requests per windowMs
});
app.use('/api/', limiter);
```

### CORS Configuration
- **Configure CORS Properly**: Allow only trusted origins
```javascript
const cors = require('cors');
app.use(cors({
  origin: process.env.FRONTEND_URL,
  credentials: true
}));
```

### Environment Variables
- **Never Commit .env Files**: Add to .gitignore
- **Use Strong Secrets**: For JWT_SECRET (at least 32 characters)
- **Validate Environment Variables**: On startup

### Data Encryption
- **Use HTTPS**: Encrypt data in transit (use Let's Encrypt)
- **Encrypt Sensitive Data**: At rest in database
- **Use SSL/TLS**: For MongoDB connections

---

## 5. FILE UPLOAD BEST PRACTICES (Multer + Cloudinary)

### Multer Configuration
- **Set File Size Limits**: Prevent large file uploads
- **Validate File Types**: Accept only images (jpg, png, webp)
- **Use Memory Storage**: For direct upload to Cloudinary

### Cloudinary Integration
- **Use Transformations**: Optimize images on-the-fly
- **Implement Lazy Loading**: Use Cloudinary's lazy loading
- **Set Resource Types**: Organize uploads in folders
- **Delete Unused Images**: Clean up when deals/businesses are deleted

```javascript
const cloudinary = require('cloudinary').v2;

cloudinary.config({
  cloud_name: process.env.CLOUDINARY_CLOUD_NAME,
  api_key: process.env.CLOUDINARY_API_KEY,
  api_secret: process.env.CLOUDINARY_API_SECRET
});

// Upload with transformations
const result = await cloudinary.uploader.upload(file.path, {
  folder: 'savemate/deals',
  width: 800,
  crop: 'limit',
  quality: 'auto',
  fetch_format: 'auto'
});
```

---

## 6. ERROR HANDLING

### Centralized Error Handling
```javascript
// middleware/error.js
const errorHandler = (err, req, res, next) => {
  let error = { ...err };
  error.message = err.message;

  // Log error for debugging
  console.error(err);

  // Mongoose bad ObjectId
  if (err.name === 'CastError') {
    const message = 'Resource not found';
    error = new ErrorResponse(message, 404);
  }

  // Mongoose duplicate key
  if (err.code === 11000) {
    const message = 'Duplicate field value entered';
    error = new ErrorResponse(message, 400);
  }

  // Mongoose validation error
  if (err.name === 'ValidationError') {
    const message = Object.values(err.errors).map(val => val.message);
    error = new ErrorResponse(message, 400);
  }

  res.status(error.statusCode || 500).json({
    success: false,
    error: error.message || 'Server Error'
  });
};
```

### Custom Error Class
```javascript
class ErrorResponse extends Error {
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
  }
}
```

---

## 7. TESTING

### Unit Testing
- **Test Individual Functions**: Use Jest
- **Mock External Dependencies**: Database, APIs
- **Aim for High Coverage**: At least 80%

### Integration Testing
- **Test API Endpoints**: Use Supertest
- **Test Database Operations**: Use test database
- **Test Authentication Flow**: Complete user journey

### End-to-End Testing
- **Test User Flows**: Use Cypress or Playwright
- **Test Critical Paths**: Registration, login, deal creation

---

## 8. LOGGING & MONITORING

### Logging
- **Use Winston or Pino**: Structured logging
- **Log Levels**: error, warn, info, debug
- **Don't Log Sensitive Data**: Passwords, tokens

```javascript
const winston = require('winston');

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' })
  ]
});

if (process.env.NODE_ENV !== 'production') {
  logger.add(new winston.transports.Console({
    format: winston.format.simple()
  }));
}
```

---

## 9. DEPLOYMENT

### Environment Setup
- **Use Different Configs**: Development, staging, production
- **Set NODE_ENV=production**: In production

### Docker Containerization
- **Create Dockerfile**: For both frontend and backend
- **Use Docker Compose**: For local development
- **Multi-stage Builds**: Optimize image size

### CI/CD
- **Use GitHub Actions**: Automate testing and deployment
- **Run Tests on PR**: Before merging
- **Automated Deployment**: On merge to main branch

### Hosting Options
- **Backend**: Render, Railway, AWS, DigitalOcean
- **Frontend**: Vercel, Netlify, AWS S3 + CloudFront
- **Database**: MongoDB Atlas (recommended)

---

## 10. PERFORMANCE OPTIMIZATION

### Backend
- **Use Clustering**: Utilize all CPU cores
- **Implement Caching**: Redis for frequently accessed data
- **Compress Responses**: Use compression middleware
- **Database Connection Pooling**: Reuse connections

### Frontend
- **Code Splitting**: Load only what's needed
- **Lazy Loading**: For images and components
- **Service Workers**: For offline capability
- **CDN**: Use Cloudinary's CDN for images

---

## 11. MAP INTEGRATION (React Leaflet / Google Maps)

### React Leaflet (Recommended for cost)
- **Free and Open Source**: No API key needed
- **Lightweight**: Better performance
- **Custom Markers**: Easy to customize

```javascript
import { MapContainer, TileLayer, Marker, Popup } from 'react-leaflet';

<MapContainer center={[lat, lng]} zoom={13}>
  <TileLayer url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png" />
  <Marker position={[lat, lng]}>
    <Popup>{dealTitle}</Popup>
  </Marker>
</MapContainer>
```

### Google Maps API (More features)
- **Better Integration**: With Google services
- **More Features**: Street View, Traffic
- **Cost**: Pay per API call (free tier available)

---

## 12. VERSION CONTROL (Git)

### Branching Strategy
- **main**: Production-ready code
- **develop**: Integration branch
- **feature/**: New features
- **bugfix/**: Bug fixes
- **hotfix/**: Urgent production fixes

### Commit Messages
- **Use Conventional Commits**: 
  - `feat: add deal search feature`
  - `fix: resolve authentication bug`
  - `docs: update API documentation`
  - `refactor: optimize database queries`

### Pull Requests
- **Write Clear Descriptions**: What and why
- **Request Reviews**: From team members
- **Run Tests**: Before merging

---

## 13. DOCUMENTATION

### Code Documentation
- **Comment Complex Logic**: Explain why, not what
- **Use JSDoc**: For functions and classes
- **Keep README Updated**: Installation, setup, usage

### API Documentation
- **Use Swagger/OpenAPI**: Generate interactive docs
- **Document All Endpoints**: Request/response examples
- **Include Authentication**: How to obtain and use tokens

---

## QUICK REFERENCE CHECKLIST

### Before Starting Development
- [ ] Set up version control (Git + GitHub)
- [ ] Create .gitignore file
- [ ] Set up environment variables
- [ ] Initialize npm projects (frontend + backend)
- [ ] Install ESLint and Prettier

### During Development
- [ ] Follow naming conventions
- [ ] Write clean, readable code
- [ ] Implement error handling
- [ ] Validate all inputs
- [ ] Write tests as you go
- [ ] Commit frequently with meaningful messages

### Before Deployment
- [ ] Run all tests
- [ ] Check for console.log() statements
- [ ] Verify environment variables
- [ ] Test in production-like environment
- [ ] Review security checklist
- [ ] Optimize images and assets
- [ ] Set up monitoring and logging

---

## RECOMMENDED PACKAGES

### Backend
- express
- mongoose
- jsonwebtoken
- bcryptjs
- express-validator
- helmet
- cors
- express-rate-limit
- express-mongo-sanitize
- multer
- cloudinary
- dotenv
- winston (logging)

### Frontend
- react
- react-router-dom
- axios
- react-query (data fetching)
- react-hook-form (forms)
- react-hot-toast (notifications)
- react-leaflet (maps)
- date-fns (date manipulation)
- zustand or redux-toolkit (state management)

---

## CONCLUSION

Following these best practices will help you build a secure, scalable, and maintainable SaveMate application. Remember:

1. **Security First**: Never compromise on security
2. **User Experience**: Fast, responsive, intuitive
3. **Code Quality**: Clean, documented, tested
4. **Performance**: Optimize for speed and efficiency
5. **Scalability**: Design for growth from day one

Good luck with your SaveMate project! 🚀
