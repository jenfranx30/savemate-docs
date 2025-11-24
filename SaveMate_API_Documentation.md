# SaveMate API Documentation

Complete API documentation for SaveMate - Local Deals Platform Backend

**Version:** 1.0.0  
**Base URL:** `http://localhost:8000`  
**API Prefix:** `/api/v1`  
**Last Updated:** November 24, 2025

---

## Table of Contents

1. [Authentication](#authentication)
2. [Deals](#deals)
3. [Businesses](#businesses)
4. [Favorites](#favorites)
5. [Reviews](#reviews)
6. [Error Codes](#error-codes)
7. [Rate Limiting](#rate-limiting)

---

## 🔐 Authentication

All authenticated endpoints require a valid JWT token in the Authorization header:

```
Authorization: Bearer YOUR_ACCESS_TOKEN
```

### Token Expiration
- **Access Token:** 30 minutes
- **Refresh Token:** 7 days

---

## 1️⃣ Authentication Endpoints

### 1.1 Register User

Create a new user account.

**Endpoint:** `POST /api/v1/auth/register`  
**Authentication:** Not required

**Request Body:**
```json
{
  "email": "user@example.com",
  "username": "johndoe",
  "password": "SecurePass123!",
  "full_name": "John Doe",
  "is_business_owner": false
}
```

**Validation Rules:**
- Email: Valid email format, unique
- Username: Alphanumeric + underscore, unique, 3-30 characters
- Password: Minimum 8 characters
- Full name: 2-100 characters

**Success Response (201 Created):**
```json
{
  "message": "User registered successfully",
  "tokens": {
    "access_token": "eyJhbGci...",
    "refresh_token": "eyJhbGci...",
    "token_type": "bearer"
  },
  "user": {
    "id": "507f1f77bcf86cd799439011",
    "email": "user@example.com",
    "username": "johndoe",
    "full_name": "John Doe",
    "is_business_owner": false,
    "created_at": "2025-11-24T10:00:00"
  }
}
```

**Error Responses:**
- `400 Bad Request` - Email or username already exists
- `422 Unprocessable Entity` - Validation error

---

### 1.2 Login

Authenticate user and receive tokens.

**Endpoint:** `POST /api/v1/auth/login`  
**Authentication:** Not required

**Request Body:**
```json
{
  "email_or_username": "johndoe",
  "password": "SecurePass123!"
}
```

**Success Response (200 OK):**
```json
{
  "message": "Login successful",
  "tokens": {
    "access_token": "eyJhbGci...",
    "refresh_token": "eyJhbGci...",
    "token_type": "bearer"
  },
  "user": {
    "id": "507f1f77bcf86cd799439011",
    "email": "user@example.com",
    "username": "johndoe",
    "full_name": "John Doe",
    "is_business_owner": false
  }
}
```

**Error Responses:**
- `401 Unauthorized` - Invalid credentials
- `422 Unprocessable Entity` - Validation error

---

### 1.3 Refresh Token

Get new access token using refresh token.

**Endpoint:** `POST /api/v1/auth/refresh`  
**Authentication:** Not required (uses refresh token)

**Request Body:**
```json
{
  "refresh_token": "eyJhbGci..."
}
```

**Success Response (200 OK):**
```json
{
  "access_token": "eyJhbGci...",
  "refresh_token": "eyJhbGci...",
  "token_type": "bearer"
}
```

**Error Responses:**
- `401 Unauthorized` - Invalid or expired refresh token

---

## 2️⃣ Deals Endpoints

### 2.1 Create Deal

Create a new deal listing.

**Endpoint:** `POST /api/v1/deals/`  
**Authentication:** Required

**Request Body:**
```json
{
  "title": "50% Off Large Pizza",
  "description": "Get half off any large pizza with 3+ toppings. Valid for dine-in and takeout.",
  "original_price": 39.99,
  "discounted_price": 19.99,
  "category": "food",
  "tags": ["pizza", "italian", "dinner", "warsaw"],
  "business_name": "Mario's Pizzeria",
  "location": {
    "address": "ul. Marszałkowska 123",
    "city": "Warsaw",
    "postal_code": "00-001",
    "country": "Poland",
    "latitude": 52.2297,
    "longitude": 21.0122
  },
  "end_date": "2025-12-31T23:59:59",
  "start_date": "2025-11-24T00:00:00",
  "image_url": "https://example.com/pizza.jpg",
  "additional_images": ["https://example.com/pizza2.jpg"],
  "terms": "One per customer. Valid Monday-Thursday only.",
  "quantity_available": 100
}
```

**Categories:** `food`, `drinks`, `shopping`, `entertainment`, `health`, `beauty`, `services`, `travel`, `electronics`, `other`

**Success Response (201 Created):**
```json
{
  "id": "507f1f77bcf86cd799439011",
  "title": "50% Off Large Pizza",
  "description": "Get half off any large pizza...",
  "original_price": 39.99,
  "discounted_price": 19.99,
  "discount_percentage": 50,
  "category": "food",
  "tags": ["pizza", "italian", "dinner", "warsaw"],
  "business_id": "temp_user_id",
  "business_name": "Mario's Pizzeria",
  "location": {...},
  "start_date": "2025-11-24T00:00:00",
  "end_date": "2025-12-31T23:59:59",
  "status": "active",
  "is_featured": false,
  "image_url": "https://example.com/pizza.jpg",
  "additional_images": [],
  "views_count": 0,
  "saves_count": 0,
  "terms": "One per customer...",
  "quantity_available": 100,
  "created_at": "2025-11-24T10:00:00",
  "updated_at": "2025-11-24T10:00:00",
  "created_by": "temp_user_id"
}
```

---

### 2.2 Get All Deals

Retrieve deals with filtering, pagination, and sorting.

**Endpoint:** `GET /api/v1/deals/`  
**Authentication:** Not required

**Query Parameters:**
- `category` (optional): Filter by category
- `city` (optional): Filter by city (case-insensitive)
- `min_discount` (optional): Minimum discount percentage (0-100)
- `max_price` (optional): Maximum discounted price
- `search` (optional): Search in title, description, business name, tags
- `is_featured` (optional): Filter featured deals (true/false)
- `deal_status` (optional): active, expired, pending, inactive (default: active)
- `page` (optional): Page number (default: 1)
- `page_size` (optional): Items per page (1-100, default: 10)
- `sort_by` (optional): created_at, discount_percentage, discounted_price, end_date (default: created_at)
- `sort_order` (optional): asc, desc (default: desc)

**Example Request:**
```
GET /api/v1/deals/?category=food&city=Warsaw&min_discount=30&page=1&page_size=10
```

**Success Response (200 OK):**
```json
{
  "deals": [...],
  "total": 45,
  "page": 1,
  "page_size": 10,
  "total_pages": 5
}
```

---

### 2.3 Get Single Deal

Get detailed information about a specific deal.

**Endpoint:** `GET /api/v1/deals/{deal_id}`  
**Authentication:** Not required

**Success Response (200 OK):**
```json
{
  "id": "507f1f77bcf86cd799439011",
  "title": "50% Off Large Pizza",
  ...
  "views_count": 151
}
```

**Note:** View count increments automatically.

---

### 2.4 Update Deal

Update an existing deal.

**Endpoint:** `PUT /api/v1/deals/{deal_id}`  
**Authentication:** Required (must be deal owner)

**Request Body:** (all fields optional)
```json
{
  "title": "60% Off Large Pizza - Extended!",
  "discounted_price": 15.99,
  "end_date": "2026-01-31T23:59:59"
}
```

**Success Response (200 OK):**
Returns updated deal object with recalculated discount_percentage.

---

### 2.5 Delete Deal

Delete a deal.

**Endpoint:** `DELETE /api/v1/deals/{deal_id}`  
**Authentication:** Required (must be deal owner)

**Success Response (200 OK):**
```json
{
  "message": "Deal deleted successfully",
  "deal_id": "507f1f77bcf86cd799439011"
}
```

---

### 2.6 Get Deals by Category

Get deals for a specific category.

**Endpoint:** `GET /api/v1/deals/category/{category}`  
**Authentication:** Not required

**Query Parameters:**
- `limit` (optional): Max results (1-100, default: 20)

**Success Response (200 OK):**
```json
[
  {
    "id": "507f1f77bcf86cd799439011",
    "title": "50% Off Pizza",
    "discounted_price": 19.99,
    "discount_percentage": 50,
    "category": "food",
    "business_name": "Mario's Pizzeria",
    "city": "Warsaw",
    "image_url": "https://example.com/pizza.jpg",
    "end_date": "2025-12-31T23:59:59",
    "status": "active"
  }
]
```

---

## 3️⃣ Business Endpoints

### 3.1 Create Business

Register a new business profile.

**Endpoint:** `POST /api/v1/businesses/`  
**Authentication:** Required

**Request Body:**
```json
{
  "business_name": "Mario's Pizzeria",
  "description": "Authentic Italian pizza in Warsaw. Family-owned since 1995.",
  "category": "restaurant",
  "email": "info@mariospizza.pl",
  "phone": "+48 22 123 4567",
  "website": "https://mariospizza.pl",
  "location": {
    "address": "ul. Marszałkowska 123",
    "city": "Warsaw",
    "postal_code": "00-001",
    "country": "Poland"
  },
  "logo_url": "https://example.com/logo.png",
  "operating_hours": [
    {
      "day": "Monday",
      "open_time": "11:00",
      "close_time": "22:00",
      "is_closed": false
    },
    {
      "day": "Sunday",
      "is_closed": true
    }
  ],
  "tags": ["italian", "pizza", "restaurant"]
}
```

**Business Categories:** `restaurant`, `cafe`, `retail`, `grocery`, `beauty`, `fitness`, `entertainment`, `services`, `healthcare`, `other`

**Success Response (201 Created):**
```json
{
  "id": "507f1f77bcf86cd799439012",
  "owner_id": "507f1f77bcf86cd799439011",
  "business_name": "Mario's Pizzeria",
  "description": "Authentic Italian pizza...",
  "category": "restaurant",
  "email": "info@mariospizza.pl",
  "phone": "+48 22 123 4567",
  "website": "https://mariospizza.pl",
  "location": {...},
  "logo_url": "https://example.com/logo.png",
  "operating_hours": [...],
  "status": "pending",
  "is_verified": false,
  "rating_average": 0.0,
  "rating_count": 0,
  "total_deals": 0,
  "active_deals": 0,
  "followers_count": 0,
  "tags": ["italian", "pizza", "restaurant"],
  "created_at": "2025-11-24T10:00:00",
  "updated_at": "2025-11-24T10:00:00"
}
```

---

### 3.2 Get All Businesses

**Endpoint:** `GET /api/v1/businesses/`  
**Authentication:** Not required

**Query Parameters:**
- `category` (optional): Filter by business category
- `city` (optional): Filter by city
- `is_verified` (optional): Filter verified businesses
- `status_filter` (optional): pending, active, suspended, closed (default: active)
- `page`, `page_size`: Pagination

**Success Response (200 OK):**
```json
{
  "businesses": [...],
  "total": 25,
  "page": 1,
  "page_size": 10,
  "total_pages": 3
}
```

---

### 3.3 Get Single Business

**Endpoint:** `GET /api/v1/businesses/{business_id}`  
**Authentication:** Not required

Returns complete business information.

---

### 3.4 Update Business

**Endpoint:** `PUT /api/v1/businesses/{business_id}`  
**Authentication:** Required (must be owner)

---

### 3.5 Delete Business

**Endpoint:** `DELETE /api/v1/businesses/{business_id}`  
**Authentication:** Required (must be owner)

---

### 3.6 Get User's Businesses

**Endpoint:** `GET /api/v1/businesses/owner/{user_id}`  
**Authentication:** Not required

Returns all businesses owned by a specific user.

---

### 3.7 Get Business Deals

**Endpoint:** `GET /api/v1/businesses/{business_id}/deals`  
**Authentication:** Not required

Returns all deals from a specific business.

---

## 4️⃣ Favorites Endpoints

### 4.1 Add to Favorites

**Endpoint:** `POST /api/v1/favorites/`  
**Authentication:** Required

**Request Body:**
```json
{
  "deal_id": "507f1f77bcf86cd799439011"
}
```

**Success Response (201 Created):**
```json
{
  "id": "507f1f77bcf86cd799439013",
  "user_id": "507f1f77bcf86cd799439010",
  "deal_id": "507f1f77bcf86cd799439011",
  "created_at": "2025-11-24T10:00:00"
}
```

**Note:** Automatically increments deal's `saves_count`.

---

### 4.2 Remove from Favorites

**Endpoint:** `DELETE /api/v1/favorites/{deal_id}`  
**Authentication:** Required

**Success Response (200 OK):**
```json
{
  "message": "Removed from favorites",
  "deal_id": "507f1f77bcf86cd799439011"
}
```

---

### 4.3 Get User Favorites

**Endpoint:** `GET /api/v1/favorites/`  
**Authentication:** Required

**Success Response (200 OK):**
```json
{
  "favorites": [
    {
      "id": "507f1f77bcf86cd799439013",
      "user_id": "507f1f77bcf86cd799439010",
      "deal_id": "507f1f77bcf86cd799439011",
      "created_at": "2025-11-24T10:00:00",
      "deal": {
        "id": "507f1f77bcf86cd799439011",
        "title": "50% Off Pizza",
        "discounted_price": 19.99,
        "discount_percentage": 50,
        "category": "food",
        "business_name": "Mario's Pizzeria",
        "image_url": "https://example.com/pizza.jpg",
        "status": "active",
        "end_date": "2025-12-31T23:59:59"
      }
    }
  ],
  "total": 5
}
```

---

### 4.4 Check if Favorited

**Endpoint:** `GET /api/v1/favorites/check/{deal_id}`  
**Authentication:** Required

**Success Response (200 OK):**
```json
{
  "is_favorited": true,
  "deal_id": "507f1f77bcf86cd799439011"
}
```

---

## 5️⃣ Reviews Endpoints

### 5.1 Create Review

**Endpoint:** `POST /api/v1/reviews/`  
**Authentication:** Required

**Request Body:**
```json
{
  "deal_id": "507f1f77bcf86cd799439011",
  "rating": 5,
  "title": "Amazing pizza deal!",
  "comment": "The pizza was delicious and the discount was fantastic. Highly recommend!"
}
```

**Validation:**
- Rating: 1-5 (integer)
- Comment: 10-500 characters
- One review per user per deal

**Success Response (201 Created):**
```json
{
  "id": "507f1f77bcf86cd799439014",
  "deal_id": "507f1f77bcf86cd799439011",
  "user_id": "507f1f77bcf86cd799439010",
  "business_id": "507f1f77bcf86cd799439012",
  "rating": 5,
  "title": "Amazing pizza deal!",
  "comment": "The pizza was delicious...",
  "helpful_count": 0,
  "is_verified_purchase": false,
  "created_at": "2025-11-24T10:00:00",
  "updated_at": "2025-11-24T10:00:00"
}
```

**Note:** Automatically updates business rating average.

---

### 5.2 Get Deal Reviews

**Endpoint:** `GET /api/v1/reviews/deal/{deal_id}`  
**Authentication:** Not required

**Success Response (200 OK):**
```json
{
  "reviews": [...],
  "total": 15,
  "average_rating": 4.7
}
```

---

### 5.3 Get User Reviews

**Endpoint:** `GET /api/v1/reviews/user/{user_id}`  
**Authentication:** Not required

Returns all reviews by a specific user.

---

### 5.4 Update Review

**Endpoint:** `PUT /api/v1/reviews/{review_id}`  
**Authentication:** Required (must be review author)

**Request Body:**
```json
{
  "rating": 4,
  "comment": "Updated: Still good but service was slower this time."
}
```

---

### 5.5 Mark Review as Helpful

**Endpoint:** `POST /api/v1/reviews/{review_id}/helpful`  
**Authentication:** Not required

**Success Response (200 OK):**
```json
{
  "message": "Marked as helpful",
  "helpful_count": 11
}
```

---

## ⚠️ Error Codes

| Code | Description |
|------|-------------|
| 200 | Success |
| 201 | Created |
| 400 | Bad Request - Invalid input |
| 401 | Unauthorized - Missing/invalid token |
| 403 | Forbidden - No permission |
| 404 | Not Found |
| 422 | Validation Error |
| 500 | Internal Server Error |

**Error Response Format:**
```json
{
  "detail": "Error message here"
}
```

---

## 🚦 Rate Limiting

**Coming in Phase 6**

Currently no rate limiting is implemented. Production deployment will include:
- 100 requests per minute per IP
- 1000 requests per hour per user
- Special limits for authentication endpoints

---

## 📝 Notes

- All timestamps are in UTC
- All prices are in PLN (Polish Złoty)
- MongoDB ObjectId format: 24-character hexadecimal string
- Bearer token must be included in Authorization header for protected endpoints

---

**For more information, visit:** http://localhost:8000/docs
