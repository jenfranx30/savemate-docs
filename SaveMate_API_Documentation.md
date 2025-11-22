# SaveMate API Documentation
## RESTful API Endpoints - FastAPI Backend

---

## Base Information

**Base URL**: `http://localhost:8000/api/v1`
**Production URL**: `https://api.savemate.com/api/v1` (when deployed)

**API Version**: v1
**Framework**: FastAPI 0.104+
**Authentication**: JWT Bearer Token
**Content-Type**: `application/json`

**Interactive Documentation**:
- Swagger UI: `http://localhost:8000/docs`
- ReDoc: `http://localhost:8000/redoc`

---

## Authentication

All protected endpoints require a JWT token in the Authorization header:

```
Authorization: Bearer <access_token>
```

### Token Structure:
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "bearer",
  "expires_in": 1800
}
```

---

## 1. AUTHENTICATION ENDPOINTS

### **POST** `/auth/register`
Register a new user account.

**Request Body**:
```json
{
  "email": "john.doe@example.com",
  "username": "johndoe",
  "password": "SecurePass123!",
  "full_name": "John Doe"
}
```

**Response** (201 Created):
```json
{
  "message": "User registered successfully",
  "user": {
    "id": "507f1f77bcf86cd799439011",
    "email": "john.doe@example.com",
    "username": "johndoe",
    "full_name": "John Doe",
    "created_at": "2025-11-22T10:00:00Z"
  },
  "tokens": {
    "access_token": "eyJ...",
    "refresh_token": "eyJ...",
    "token_type": "bearer"
  }
}
```

**Validation**:
- Email: Valid email format, unique
- Username: 3-30 chars, alphanumeric + underscore, unique
- Password: Min 8 chars, at least 1 uppercase, 1 lowercase, 1 number
- Full name: Required

**Errors**:
- `400`: Invalid input data
- `409`: Email or username already exists

---

### **POST** `/auth/login`
Login with email/username and password.

**Request Body**:
```json
{
  "email_or_username": "johndoe",
  "password": "SecurePass123!"
}
```

**Response** (200 OK):
```json
{
  "message": "Login successful",
  "user": {
    "id": "507f1f77bcf86cd799439011",
    "email": "john.doe@example.com",
    "username": "johndoe",
    "full_name": "John Doe",
    "profile_picture": "https://res.cloudinary.com/..."
  },
  "tokens": {
    "access_token": "eyJ...",
    "refresh_token": "eyJ...",
    "token_type": "bearer",
    "expires_in": 1800
  }
}
```

**Errors**:
- `401`: Invalid credentials
- `404`: User not found

---

### **POST** `/auth/refresh`
Refresh access token using refresh token.

**Request Body**:
```json
{
  "refresh_token": "eyJ..."
}
```

**Response** (200 OK):
```json
{
  "access_token": "eyJ...",
  "token_type": "bearer",
  "expires_in": 1800
}
```

**Errors**:
- `401`: Invalid or expired refresh token

---

### **POST** `/auth/logout`
Logout user (invalidate tokens).

**Headers**: 
```
Authorization: Bearer <access_token>
```

**Response** (200 OK):
```json
{
  "message": "Logout successful"
}
```

---

### **POST** `/auth/forgot-password`
Request password reset email.

**Request Body**:
```json
{
  "email": "john.doe@example.com"
}
```

**Response** (200 OK):
```json
{
  "message": "Password reset link sent to email"
}
```

---

### **POST** `/auth/reset-password`
Reset password with token from email.

**Request Body**:
```json
{
  "token": "reset_token_here",
  "new_password": "NewSecurePass123!"
}
```

**Response** (200 OK):
```json
{
  "message": "Password reset successful"
}
```

**Errors**:
- `400`: Invalid or expired token

---

## 2. USER ENDPOINTS

### **GET** `/users/me`
Get current user profile (authenticated).

**Headers**: 
```
Authorization: Bearer <access_token>
```

**Response** (200 OK):
```json
{
  "id": "507f1f77bcf86cd799439011",
  "email": "john.doe@example.com",
  "username": "johndoe",
  "full_name": "John Doe",
  "profile_picture": "https://res.cloudinary.com/...",
  "bio": "Food enthusiast and deal hunter",
  "location": {
    "type": "Point",
    "coordinates": [21.0122, 52.2297]
  },
  "preferences": {
    "favorite_categories": ["food-dining", "entertainment"],
    "notification_radius": 5.0,
    "email_notifications": true
  },
  "saved_deals": ["507f1f77bcf86cd799439012", "507f1f77bcf86cd799439013"],
  "is_business_owner": false,
  "created_at": "2025-11-01T10:00:00Z",
  "updated_at": "2025-11-22T10:00:00Z"
}
```

---

### **PUT** `/users/me`
Update current user profile.

**Headers**: 
```
Authorization: Bearer <access_token>
```

**Request Body** (all fields optional):
```json
{
  "full_name": "John A. Doe",
  "bio": "Updated bio",
  "phone": "+48 123 456 789",
  "location": {
    "type": "Point",
    "coordinates": [21.0122, 52.2297]
  },
  "preferences": {
    "favorite_categories": ["food-dining", "shopping"],
    "notification_radius": 10.0,
    "email_notifications": false
  }
}
```

**Response** (200 OK):
```json
{
  "message": "Profile updated successfully",
  "user": { /* updated user object */ }
}
```

---

### **DELETE** `/users/me`
Delete current user account.

**Headers**: 
```
Authorization: Bearer <access_token>
```

**Request Body**:
```json
{
  "password": "SecurePass123!",
  "confirmation": "DELETE"
}
```

**Response** (200 OK):
```json
{
  "message": "Account deleted successfully"
}
```

---

### **POST** `/users/me/avatar`
Upload profile picture.

**Headers**: 
```
Authorization: Bearer <access_token>
Content-Type: multipart/form-data
```

**Request Body** (form-data):
```
file: [image file]
```

**Response** (200 OK):
```json
{
  "message": "Avatar uploaded successfully",
  "profile_picture": "https://res.cloudinary.com/..."
}
```

**Constraints**:
- Max file size: 5MB
- Allowed formats: jpg, jpeg, png, webp
- Auto-resized to 400x400

---

### **GET** `/users/{user_id}`
Get public user profile by ID.

**Response** (200 OK):
```json
{
  "id": "507f1f77bcf86cd799439011",
  "username": "johndoe",
  "full_name": "John Doe",
  "profile_picture": "https://res.cloudinary.com/...",
  "bio": "Food enthusiast",
  "is_business_owner": false,
  "member_since": "2025-11-01T10:00:00Z"
}
```

**Note**: Only public information returned (no email, saved deals, etc.)

---

### **GET** `/users/me/saved-deals`
Get user's saved deals.

**Headers**: 
```
Authorization: Bearer <access_token>
```

**Query Parameters**:
- `page` (int, default: 1)
- `limit` (int, default: 20, max: 100)

**Response** (200 OK):
```json
{
  "deals": [
    {
      "id": "507f1f77bcf86cd799439012",
      "title": "50% Off Pizza",
      "business_name": "Pizza Palace",
      "discount_percentage": 50,
      "valid_until": "2025-12-31T23:59:59Z",
      /* ... other deal fields */
    }
  ],
  "total": 5,
  "page": 1,
  "pages": 1
}
```

---

### **POST** `/users/me/saved-deals/{deal_id}`
Save a deal to favorites.

**Headers**: 
```
Authorization: Bearer <access_token>
```

**Response** (200 OK):
```json
{
  "message": "Deal saved successfully"
}
```

---

### **DELETE** `/users/me/saved-deals/{deal_id}`
Remove deal from favorites.

**Headers**: 
```
Authorization: Bearer <access_token>
```

**Response** (200 OK):
```json
{
  "message": "Deal removed from saved"
}
```

---

## 3. DEAL ENDPOINTS

### **GET** `/deals`
Get all deals with filtering and pagination.

**Query Parameters**:
```
?page=1
&limit=20
&category=food-dining
&min_discount=20
&max_price=100
&lat=52.2297
&lng=21.0122
&radius=5
&sort=newest|popular|discount|expiring
&search=pizza
```

**Response** (200 OK):
```json
{
  "deals": [
    {
      "id": "507f1f77bcf86cd799439012",
      "title": "50% Off All Pizzas",
      "description": "Get half price on any pizza...",
      "business": {
        "id": "507f1f77bcf86cd799439013",
        "name": "Pizza Palace",
        "logo": "https://res.cloudinary.com/..."
      },
      "category": {
        "id": "507f1f77bcf86cd799439014",
        "name": "Food & Dining",
        "slug": "food-dining"
      },
      "original_price": 40.0,
      "discounted_price": 20.0,
      "discount_percentage": 50.0,
      "currency": "PLN",
      "images": ["https://res.cloudinary.com/..."],
      "location": {
        "type": "Point",
        "coordinates": [21.0122, 52.2297]
      },
      "address": {
        "street": "ul. Marszałkowska 123",
        "city": "Warsaw",
        "zip_code": "00-001"
      },
      "valid_from": "2025-11-22T00:00:00Z",
      "valid_until": "2025-12-31T23:59:59Z",
      "days_remaining": 39,
      "views_count": 1250,
      "saves_count": 342,
      "is_active": true,
      "is_expired": false,
      "created_at": "2025-11-20T10:00:00Z"
    }
  ],
  "total": 150,
  "page": 1,
  "pages": 8,
  "limit": 20
}
```

**Sort Options**:
- `newest`: Most recently created
- `popular`: Most saves/views
- `discount`: Highest discount percentage
- `expiring`: Ending soonest

---

### **POST** `/deals`
Create a new deal (business owners only).

**Headers**: 
```
Authorization: Bearer <access_token>
```

**Request Body**:
```json
{
  "title": "50% Off All Pizzas",
  "description": "Get half price on any pizza during lunch hours (11 AM - 3 PM)",
  "business_id": "507f1f77bcf86cd799439013",
  "category_id": "507f1f77bcf86cd799439014",
  "original_price": 40.0,
  "discounted_price": 20.0,
  "valid_from": "2025-11-22T00:00:00Z",
  "valid_until": "2025-12-31T23:59:59Z",
  "terms_conditions": "Valid for dine-in only. One per customer.",
  "redemption_code": "PIZZA50",
  "max_redemptions": 100,
  "location": {
    "type": "Point",
    "coordinates": [21.0122, 52.2297]
  },
  "address": {
    "street": "ul. Marszałkowska 123",
    "city": "Warsaw",
    "state": "Mazovia",
    "zip_code": "00-001",
    "country": "Poland"
  }
}
```

**Response** (201 Created):
```json
{
  "message": "Deal created successfully",
  "deal": { /* full deal object */ },
  "id": "507f1f77bcf86cd799439012"
}
```

**Validation**:
- User must own the business
- Valid_until must be in the future
- Discounted_price < Original_price
- All required fields present

---

### **GET** `/deals/{deal_id}`
Get single deal by ID.

**Response** (200 OK):
```json
{
  "id": "507f1f77bcf86cd799439012",
  "title": "50% Off All Pizzas",
  /* ... full deal object with business and category info ... */
}
```

**Side Effect**: Increments `views_count` by 1

---

### **PUT** `/deals/{deal_id}`
Update deal (owner only).

**Headers**: 
```
Authorization: Bearer <access_token>
```

**Request Body** (all fields optional):
```json
{
  "title": "Updated Title",
  "description": "Updated description",
  "discounted_price": 15.0,
  "valid_until": "2025-12-31T23:59:59Z",
  "is_active": true
}
```

**Response** (200 OK):
```json
{
  "message": "Deal updated successfully",
  "deal": { /* updated deal object */ }
}
```

**Authorization**: Must be deal creator or business owner

---

### **DELETE** `/deals/{deal_id}`
Delete deal (owner only).

**Headers**: 
```
Authorization: Bearer <access_token>
```

**Response** (200 OK):
```json
{
  "message": "Deal deleted successfully"
}
```

---

### **GET** `/deals/nearby`
Get deals near a location (optimized geospatial query).

**Query Parameters**:
```
?lat=52.2297
&lng=21.0122
&radius=5          # km
&category=food-dining
&limit=20
```

**Response**: Same as GET `/deals` but sorted by distance

---

### **GET** `/deals/search`
Full-text search deals.

**Query Parameters**:
```
?q=pizza+restaurant
&category=food-dining
&limit=20
```

**Response**: Same format as GET `/deals`

---

### **GET** `/deals/category/{category_slug}`
Get deals by category.

**Example**: `/deals/category/food-dining`

**Query Parameters**:
```
?page=1
&limit=20
&sort=newest
```

**Response**: Same format as GET `/deals`

---

### **POST** `/deals/{deal_id}/images`
Upload images for a deal.

**Headers**: 
```
Authorization: Bearer <access_token>
Content-Type: multipart/form-data
```

**Request Body** (form-data):
```
files: [image1.jpg, image2.jpg, ...]
```

**Response** (200 OK):
```json
{
  "message": "Images uploaded successfully",
  "images": [
    "https://res.cloudinary.com/...",
    "https://res.cloudinary.com/..."
  ]
}
```

**Constraints**:
- Max 5 images per deal
- Max 5MB per image
- Formats: jpg, jpeg, png, webp

---

## 4. BUSINESS ENDPOINTS

### **GET** `/businesses`
Get all businesses.

**Query Parameters**:
```
?page=1
&limit=20
&category=restaurant
&lat=52.2297
&lng=21.0122
&radius=10
&verified=true
&search=pizza
```

**Response** (200 OK):
```json
{
  "businesses": [
    {
      "id": "507f1f77bcf86cd799439013",
      "name": "Pizza Palace",
      "description": "Best pizza in Warsaw since 2010",
      "category": "Restaurant",
      "logo": "https://res.cloudinary.com/...",
      "cover_image": "https://res.cloudinary.com/...",
      "location": {
        "type": "Point",
        "coordinates": [21.0122, 52.2297]
      },
      "address": {
        "street": "ul. Marszałkowska 123",
        "city": "Warsaw",
        "zip_code": "00-001"
      },
      "contact": {
        "phone": "+48 22 123 4567",
        "email": "contact@pizzapalace.pl",
        "website": "https://pizzapalace.pl"
      },
      "rating": 4.5,
      "total_reviews": 328,
      "total_deals": 12,
      "is_verified": true,
      "created_at": "2025-01-15T10:00:00Z"
    }
  ],
  "total": 45,
  "page": 1,
  "pages": 3
}
```

---

### **POST** `/businesses`
Create a new business.

**Headers**: 
```
Authorization: Bearer <access_token>
```

**Request Body**:
```json
{
  "name": "Pizza Palace",
  "description": "Best pizza in Warsaw since 2010",
  "category": "Restaurant",
  "location": {
    "type": "Point",
    "coordinates": [21.0122, 52.2297]
  },
  "address": {
    "street": "ul. Marszałkowska 123",
    "city": "Warsaw",
    "state": "Mazovia",
    "zip_code": "00-001",
    "country": "Poland"
  },
  "contact": {
    "phone": "+48 22 123 4567",
    "email": "contact@pizzapalace.pl",
    "website": "https://pizzapalace.pl"
  },
  "operating_hours": {
    "monday": {"open": "09:00", "close": "22:00"},
    "tuesday": {"open": "09:00", "close": "22:00"},
    "sunday": {"closed": true}
  }
}
```

**Response** (201 Created):
```json
{
  "message": "Business created successfully",
  "business": { /* full business object */ },
  "id": "507f1f77bcf86cd799439013"
}
```

**Side Effect**: User's `is_business_owner` set to `true`

---

### **GET** `/businesses/{business_id}`
Get business by ID.

**Response** (200 OK):
```json
{
  /* Full business object with all details */
}
```

---

### **PUT** `/businesses/{business_id}`
Update business (owner only).

**Headers**: 
```
Authorization: Bearer <access_token>
```

**Request Body** (all fields optional):
```json
{
  "description": "Updated description",
  "contact": {
    "phone": "+48 22 999 8888"
  }
}
```

**Response** (200 OK):
```json
{
  "message": "Business updated successfully",
  "business": { /* updated business object */ }
}
```

---

### **DELETE** `/businesses/{business_id}`
Delete business (owner only).

**Headers**: 
```
Authorization: Bearer <access_token>
```

**Response** (200 OK):
```json
{
  "message": "Business deleted successfully"
}
```

**Note**: All deals associated with business will be deactivated

---

### **GET** `/businesses/{business_id}/deals`
Get all deals for a specific business.

**Query Parameters**:
```
?page=1
&limit=20
&active=true
```

**Response**: Same format as GET `/deals`

---

### **POST** `/businesses/{business_id}/logo`
Upload business logo.

**Headers**: 
```
Authorization: Bearer <access_token>
Content-Type: multipart/form-data
```

**Request Body** (form-data):
```
file: [image file]
```

**Response** (200 OK):
```json
{
  "message": "Logo uploaded successfully",
  "logo": "https://res.cloudinary.com/..."
}
```

---

## 5. CATEGORY ENDPOINTS

### **GET** `/categories`
Get all categories.

**Query Parameters**:
```
?active=true
&featured=true
```

**Response** (200 OK):
```json
{
  "categories": [
    {
      "id": "507f1f77bcf86cd799439014",
      "name": "Food & Dining",
      "slug": "food-dining",
      "description": "Restaurants, cafes, and food delivery",
      "icon": "🍔",
      "color": "#EF4444",
      "deals_count": 145,
      "is_active": true,
      "is_featured": true
    }
  ],
  "total": 10
}
```

---

### **POST** `/categories`
Create new category (admin only).

**Headers**: 
```
Authorization: Bearer <access_token>
X-Admin-Token: <admin_secret>
```

**Request Body**:
```json
{
  "name": "Food & Dining",
  "slug": "food-dining",
  "description": "Restaurants and cafes",
  "icon": "🍔",
  "color": "#EF4444",
  "is_featured": true
}
```

**Response** (201 Created):
```json
{
  "message": "Category created successfully",
  "category": { /* full category object */ }
}
```

---

### **GET** `/categories/{category_id}`
Get category by ID.

**Response** (200 OK):
```json
{
  "id": "507f1f77bcf86cd799439014",
  "name": "Food & Dining",
  /* ... full category object ... */
}
```

---

### **PUT** `/categories/{category_id}`
Update category (admin only).

**Headers**: 
```
Authorization: Bearer <access_token>
X-Admin-Token: <admin_secret>
```

**Request Body**:
```json
{
  "name": "Updated Name",
  "is_active": false
}
```

**Response** (200 OK):
```json
{
  "message": "Category updated successfully",
  "category": { /* updated object */ }
}
```

---

### **DELETE** `/categories/{category_id}`
Delete category (admin only).

**Response** (200 OK):
```json
{
  "message": "Category deleted successfully"
}
```

**Note**: Deals in this category will need to be recategorized

---

## Error Responses

### Standard Error Format:
```json
{
  "detail": "Error message",
  "status_code": 400,
  "error_type": "ValidationError",
  "timestamp": "2025-11-22T10:00:00Z"
}
```

### Common Status Codes:
- `200`: Success
- `201`: Created
- `204`: No Content
- `400`: Bad Request (validation error)
- `401`: Unauthorized (invalid/missing token)
- `403`: Forbidden (insufficient permissions)
- `404`: Not Found
- `409`: Conflict (duplicate resource)
- `422`: Unprocessable Entity (Pydantic validation)
- `429`: Too Many Requests (rate limit)
- `500`: Internal Server Error

---

## Rate Limiting

**Default Limits**:
- Public endpoints: 100 requests/minute
- Authenticated endpoints: 300 requests/minute
- Upload endpoints: 20 requests/minute

**Headers**:
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1700654400
```

---

## Pagination

**Query Parameters**:
```
?page=1        # Page number (1-indexed)
&limit=20      # Items per page (max 100)
```

**Response Metadata**:
```json
{
  "data": [...],
  "total": 150,
  "page": 1,
  "pages": 8,
  "limit": 20,
  "has_next": true,
  "has_prev": false
}
```

---

## Testing the API

### Using cURL:
```bash
# Register
curl -X POST http://localhost:8000/api/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","username":"testuser","password":"Test123!","full_name":"Test User"}'

# Login
curl -X POST http://localhost:8000/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email_or_username":"testuser","password":"Test123!"}'

# Get deals (with token)
curl -X GET "http://localhost:8000/api/v1/deals?limit=5" \
  -H "Authorization: Bearer YOUR_TOKEN_HERE"
```

### Using FastAPI Swagger UI:
1. Run backend: `uvicorn app.main:app --reload`
2. Open browser: `http://localhost:8000/docs`
3. Try endpoints interactively
4. Authorize with JWT token

---

*API Documentation v1.0*
*Last Updated: November 22, 2025*
*Framework: FastAPI + Python 3.11*
