# SaveMate Database Schema

Complete database schema documentation for SaveMate API using MongoDB and Beanie ODM.

**Database Name:** `savemate`  
**Database Type:** MongoDB Atlas  
**ODM:** Beanie (based on Pydantic)  
**Last Updated:** November 24, 2025

---

## 📋 Table of Contents

1. [Overview](#overview)
2. [Collections](#collections)
3. [User Collection](#user-collection)
4. [Deal Collection](#deal-collection)
5. [Business Collection](#business-collection)
6. [Category Collection](#category-collection)
7. [Favorite Collection](#favorite-collection)
8. [Review Collection](#review-collection)
9. [Relationships](#relationships)
10. [Indexes](#indexes)

---

## 🔍 Overview

SaveMate uses MongoDB as its primary database with 6 collections:

| Collection | Documents | Description |
|------------|-----------|-------------|
| `users` | User accounts | Authentication and user profiles |
| `deals` | Deal listings | Local deals and discounts |
| `businesses` | Business profiles | Business information and ratings |
| `categories` | Deal categories | Predefined deal categories |
| `favorites` | User favorites | User-deal relationships |
| `reviews` | Deal reviews | Reviews and ratings |

**Total Collections:** 6  
**Estimated Data:** ~10,000+ documents (production)

---

## 1️⃣ User Collection

**Collection Name:** `users`

Stores user account information and authentication data.

### Schema

```python
{
  "_id": ObjectId,
  "email": String (unique, lowercase),
  "username": String (unique, lowercase),
  "hashed_password": String,
  "full_name": String,
  "is_active": Boolean,
  "is_business_owner": Boolean,
  "created_at": DateTime,
  "updated_at": DateTime
}
```

### Field Details

| Field | Type | Required | Unique | Default | Description |
|-------|------|----------|--------|---------|-------------|
| `_id` | ObjectId | Yes | Yes | Auto | MongoDB document ID |
| `email` | String | Yes | Yes | - | User email (EmailStr) |
| `username` | String | Yes | Yes | - | Username (3-30 chars) |
| `hashed_password` | String | Yes | No | - | Bcrypt hashed password |
| `full_name` | String | Yes | No | - | User's full name (2-100 chars) |
| `is_active` | Boolean | Yes | No | `True` | Account active status |
| `is_business_owner` | Boolean | Yes | No | `False` | Business owner flag |
| `created_at` | DateTime | Yes | No | `utcnow()` | Account creation timestamp |
| `updated_at` | DateTime | Yes | No | `utcnow()` | Last update timestamp |

### Validation Rules

- **Email:** Valid email format, converted to lowercase
- **Username:** Alphanumeric + underscore only, 3-30 characters, lowercase
- **Password:** Minimum 8 characters (stored as bcrypt hash)
- **Full Name:** 2-100 characters

### Example Document

```json
{
  "_id": ObjectId("507f1f77bcf86cd799439011"),
  "email": "johndoe@example.com",
  "username": "johndoe",
  "hashed_password": "$2b$12$LQv3c1yqBWVHxkd0LHAkCOYz6TtxMQJqhN8/LewY5GyYqgPgqYSai",
  "full_name": "John Doe",
  "is_active": true,
  "is_business_owner": false,
  "created_at": "2025-11-24T10:00:00.000Z",
  "updated_at": "2025-11-24T10:00:00.000Z"
}
```

### Indexes

- `email`: Unique index
- `username`: Unique index

---

## 2️⃣ Deal Collection

**Collection Name:** `deals`

Stores deal listings and promotions.

### Schema

```python
{
  "_id": ObjectId,
  "title": String,
  "description": String,
  "original_price": Float,
  "discounted_price": Float,
  "discount_percentage": Integer,
  "category": DealCategory (enum),
  "tags": List[String],
  "business_id": String,
  "business_name": String,
  "location": {
    "address": String,
    "city": String,
    "postal_code": String,
    "country": String,
    "latitude": Float (optional),
    "longitude": Float (optional)
  },
  "start_date": DateTime,
  "end_date": DateTime,
  "status": DealStatus (enum),
  "is_featured": Boolean,
  "image_url": HttpUrl (optional),
  "additional_images": List[HttpUrl],
  "views_count": Integer,
  "saves_count": Integer,
  "terms": String (optional),
  "quantity_available": Integer (optional),
  "created_at": DateTime,
  "updated_at": DateTime,
  "created_by": String
}
```

### Field Details

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `_id` | ObjectId | Yes | Auto | MongoDB document ID |
| `title` | String | Yes | - | Deal title (3-200 chars) |
| `description` | String | Yes | - | Deal description (10-1000 chars) |
| `original_price` | Float | Yes | - | Original price (>0) |
| `discounted_price` | Float | Yes | - | Discounted price (>0, < original) |
| `discount_percentage` | Integer | Yes | Auto | Calculated discount % (0-100) |
| `category` | Enum | Yes | - | Deal category |
| `tags` | Array | No | `[]` | Search tags (max 10) |
| `business_id` | String | Yes | - | Business/owner ID |
| `business_name` | String | Yes | - | Business name (2-200 chars) |
| `location` | Object | Yes | - | Deal location |
| `start_date` | DateTime | Yes | `utcnow()` | Deal start date |
| `end_date` | DateTime | Yes | - | Deal end date (future) |
| `status` | Enum | Yes | `active` | Deal status |
| `is_featured` | Boolean | Yes | `False` | Featured flag |
| `image_url` | HttpUrl | No | `None` | Main image URL |
| `additional_images` | Array | No | `[]` | Additional images (max 5) |
| `views_count` | Integer | Yes | `0` | View counter |
| `saves_count` | Integer | Yes | `0` | Save counter |
| `terms` | String | No | `None` | Terms & conditions (max 500) |
| `quantity_available` | Integer | No | `None` | Available quantity |
| `created_at` | DateTime | Yes | `utcnow()` | Creation timestamp |
| `updated_at` | DateTime | Yes | `utcnow()` | Last update timestamp |
| `created_by` | String | Yes | - | Creator user ID |

### Enums

**DealCategory:**
```python
FOOD = "food"
DRINKS = "drinks"
SHOPPING = "shopping"
ENTERTAINMENT = "entertainment"
HEALTH = "health"
BEAUTY = "beauty"
SERVICES = "services"
TRAVEL = "travel"
ELECTRONICS = "electronics"
OTHER = "other"
```

**DealStatus:**
```python
ACTIVE = "active"
EXPIRED = "expired"
PENDING = "pending"
INACTIVE = "inactive"
```

### Helper Methods

- `calculate_discount_percentage()`: Auto-calculate discount
- `is_expired()`: Check if deal has expired
- `is_valid()`: Check if deal is valid (not expired, active)
- `increment_views()`: Increment view counter
- `increment_saves()`: Increment save counter

### Example Document

```json
{
  "_id": ObjectId("507f1f77bcf86cd799439012"),
  "title": "50% Off Large Pizza",
  "description": "Get half off any large pizza with 3 or more toppings.",
  "original_price": 39.99,
  "discounted_price": 19.99,
  "discount_percentage": 50,
  "category": "food",
  "tags": ["pizza", "italian", "dinner", "warsaw"],
  "business_id": "507f1f77bcf86cd799439011",
  "business_name": "Mario's Pizzeria",
  "location": {
    "address": "ul. Marszałkowska 123",
    "city": "Warsaw",
    "postal_code": "00-001",
    "country": "Poland",
    "latitude": 52.2297,
    "longitude": 21.0122
  },
  "start_date": "2025-11-24T00:00:00.000Z",
  "end_date": "2025-12-31T23:59:59.000Z",
  "status": "active",
  "is_featured": false,
  "image_url": "https://example.com/pizza.jpg",
  "additional_images": [],
  "views_count": 156,
  "saves_count": 23,
  "terms": "One per customer. Valid Monday-Thursday only.",
  "quantity_available": 100,
  "created_at": "2025-11-24T10:00:00.000Z",
  "updated_at": "2025-11-24T10:00:00.000Z",
  "created_by": "507f1f77bcf86cd799439011"
}
```

### Indexes

- No geospatial indexes (simple dict location)
- Consider adding text index on `title`, `description`, `tags` for search

---

## 3️⃣ Business Collection

**Collection Name:** `businesses`

Stores business profiles and information.

### Schema

```python
{
  "_id": ObjectId,
  "owner_id": String,
  "business_name": String,
  "description": String,
  "category": BusinessCategory (enum),
  "email": EmailStr,
  "phone": String,
  "website": HttpUrl (optional),
  "location": {
    "address": String,
    "city": String,
    "postal_code": String,
    "country": String
  },
  "logo_url": HttpUrl (optional),
  "cover_image_url": HttpUrl (optional),
  "images": List[HttpUrl],
  "operating_hours": List[OperatingHours],
  "status": BusinessStatus (enum),
  "is_verified": Boolean,
  "rating_average": Float,
  "rating_count": Integer,
  "total_deals": Integer,
  "active_deals": Integer,
  "followers_count": Integer,
  "tags": List[String],
  "created_at": DateTime,
  "updated_at": DateTime
}
```

### Field Details

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `_id` | ObjectId | Yes | Auto | MongoDB document ID |
| `owner_id` | String | Yes | - | User ID of business owner |
| `business_name` | String | Yes | - | Business name (2-200 chars) |
| `description` | String | Yes | - | Business description (20-1000 chars) |
| `category` | Enum | Yes | - | Business category |
| `email` | EmailStr | Yes | - | Business contact email |
| `phone` | String | Yes | - | Phone number (9-20 chars) |
| `website` | HttpUrl | No | `None` | Business website |
| `location` | Object | Yes | - | Business location |
| `logo_url` | HttpUrl | No | `None` | Business logo URL |
| `cover_image_url` | HttpUrl | No | `None` | Cover image URL |
| `images` | Array | No | `[]` | Additional images |
| `operating_hours` | Array | No | `[]` | Weekly operating hours |
| `status` | Enum | Yes | `pending` | Business status |
| `is_verified` | Boolean | Yes | `False` | Verification status |
| `rating_average` | Float | Yes | `0.0` | Average rating (0-5) |
| `rating_count` | Integer | Yes | `0` | Number of ratings |
| `total_deals` | Integer | Yes | `0` | Total deals created |
| `active_deals` | Integer | Yes | `0` | Currently active deals |
| `followers_count` | Integer | Yes | `0` | Number of followers |
| `tags` | Array | No | `[]` | Search tags (max 10) |
| `created_at` | DateTime | Yes | `utcnow()` | Creation timestamp |
| `updated_at` | DateTime | Yes | `utcnow()` | Last update timestamp |

### Enums

**BusinessCategory:**
```python
RESTAURANT = "restaurant"
CAFE = "cafe"
RETAIL = "retail"
GROCERY = "grocery"
BEAUTY = "beauty"
FITNESS = "fitness"
ENTERTAINMENT = "entertainment"
SERVICES = "services"
HEALTHCARE = "healthcare"
OTHER = "other"
```

**BusinessStatus:**
```python
PENDING = "pending"
ACTIVE = "active"
SUSPENDED = "suspended"
CLOSED = "closed"
```

### Operating Hours Schema

```python
{
  "day": String,
  "open_time": String (optional, format: "HH:MM"),
  "close_time": String (optional, format: "HH:MM"),
  "is_closed": Boolean
}
```

### Helper Methods

- `increment_deals()`: Increment deal counters
- `decrement_active_deals()`: Decrement active deal count
- `update_rating()`: Update average rating

### Example Document

```json
{
  "_id": ObjectId("507f1f77bcf86cd799439013"),
  "owner_id": "507f1f77bcf86cd799439011",
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
  "cover_image_url": "https://example.com/cover.jpg",
  "images": [],
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
  "status": "active",
  "is_verified": true,
  "rating_average": 4.5,
  "rating_count": 127,
  "total_deals": 45,
  "active_deals": 12,
  "followers_count": 523,
  "tags": ["italian", "pizza", "restaurant"],
  "created_at": "2025-11-24T10:00:00.000Z",
  "updated_at": "2025-11-24T10:00:00.000Z"
}
```

---

## 4️⃣ Category Collection

**Collection Name:** `categories`

Stores predefined deal categories.

### Schema

```python
{
  "_id": ObjectId,
  "name": String,
  "slug": String,
  "description": String,
  "icon": String (optional),
  "is_active": Boolean,
  "created_at": DateTime
}
```

### Example Document

```json
{
  "_id": ObjectId("507f1f77bcf86cd799439014"),
  "name": "Food & Dining",
  "slug": "food",
  "description": "Restaurants, cafes, and food deals",
  "icon": "utensils",
  "is_active": true,
  "created_at": "2025-11-24T10:00:00.000Z"
}
```

---

## 5️⃣ Favorite Collection

**Collection Name:** `favorites`

Stores user-deal favorite relationships.

### Schema

```python
{
  "_id": ObjectId,
  "user_id": String,
  "deal_id": String,
  "created_at": DateTime
}
```

### Field Details

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `_id` | ObjectId | Yes | MongoDB document ID |
| `user_id` | String | Yes | User ID who favorited |
| `deal_id` | String | Yes | Deal ID that was favorited |
| `created_at` | DateTime | Yes | When favorited |

### Example Document

```json
{
  "_id": ObjectId("507f1f77bcf86cd799439015"),
  "user_id": "507f1f77bcf86cd799439011",
  "deal_id": "507f1f77bcf86cd799439012",
  "created_at": "2025-11-24T10:00:00.000Z"
}
```

### Indexes

- Compound index on `(user_id, deal_id)` for uniqueness
- Index on `user_id` for user's favorites queries

---

## 6️⃣ Review Collection

**Collection Name:** `reviews`

Stores deal reviews and ratings.

### Schema

```python
{
  "_id": ObjectId,
  "deal_id": String,
  "user_id": String,
  "business_id": String,
  "rating": Integer (1-5),
  "title": String (optional),
  "comment": String,
  "helpful_count": Integer,
  "is_verified_purchase": Boolean,
  "created_at": DateTime,
  "updated_at": DateTime
}
```

### Field Details

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `_id` | ObjectId | Yes | Auto | MongoDB document ID |
| `deal_id` | String | Yes | - | Deal being reviewed |
| `user_id` | String | Yes | - | Reviewer user ID |
| `business_id` | String | Yes | - | Business ID |
| `rating` | Integer | Yes | - | Star rating (1-5) |
| `title` | String | No | `None` | Review title (max 100) |
| `comment` | String | Yes | - | Review text (10-500 chars) |
| `helpful_count` | Integer | Yes | `0` | Helpful votes |
| `is_verified_purchase` | Boolean | Yes | `False` | Verified redemption |
| `created_at` | DateTime | Yes | `utcnow()` | Creation timestamp |
| `updated_at` | DateTime | Yes | `utcnow()` | Last update timestamp |

### Helper Methods

- `increment_helpful()`: Increment helpful counter

### Example Document

```json
{
  "_id": ObjectId("507f1f77bcf86cd799439016"),
  "deal_id": "507f1f77bcf86cd799439012",
  "user_id": "507f1f77bcf86cd799439011",
  "business_id": "507f1f77bcf86cd799439013",
  "rating": 5,
  "title": "Amazing pizza deal!",
  "comment": "The pizza was delicious and the discount was fantastic. Highly recommend!",
  "helpful_count": 12,
  "is_verified_purchase": true,
  "created_at": "2025-11-24T10:00:00.000Z",
  "updated_at": "2025-11-24T10:00:00.000Z"
}
```

### Indexes

- Compound index on `(user_id, deal_id)` for uniqueness
- Index on `deal_id` for deal reviews queries

---

## 🔗 Relationships

### Entity Relationship Diagram

```
┌─────────────┐
│    USER     │
└──────┬──────┘
       │
       ├──────────────┐
       │              │
       │              ▼
       │      ┌────────────┐
       │      │  BUSINESS  │
       │      └──────┬─────┘
       │             │
       │             ▼
       │      ┌────────────┐
       ├─────▶│    DEAL    │◀────┐
       │      └──────┬─────┘     │
       │             │            │
       ▼             ▼            │
┌────────────┐  ┌────────┐       │
│  FAVORITE  │  │ REVIEW │───────┘
└────────────┘  └────────┘
```

### Relationship Details

1. **User → Business** (1:N)
   - One user can own multiple businesses
   - `businesses.owner_id` references `users._id`

2. **Business → Deal** (1:N)
   - One business can have multiple deals
   - `deals.business_id` references `businesses._id`

3. **User → Deal** (1:N via created_by)
   - One user can create multiple deals
   - `deals.created_by` references `users._id`

4. **User → Favorite → Deal** (M:N)
   - Many users can favorite many deals
   - `favorites.user_id` references `users._id`
   - `favorites.deal_id` references `deals._id`

5. **User → Review → Deal** (M:N)
   - Many users can review many deals
   - `reviews.user_id` references `users._id`
   - `reviews.deal_id` references `deals._id`
   - One review per user per deal

6. **Review → Business** (N:1)
   - Many reviews belong to one business
   - `reviews.business_id` references `businesses._id`

---

## 📊 Indexes

### Current Indexes

| Collection | Field(s) | Type | Unique | Purpose |
|------------|----------|------|--------|---------|
| users | email | Single | Yes | Fast user lookup |
| users | username | Single | Yes | Fast user lookup |
| favorites | (user_id, deal_id) | Compound | Yes | Prevent duplicates |
| favorites | user_id | Single | No | User favorites query |
| reviews | (user_id, deal_id) | Compound | Yes | One review per deal |
| reviews | deal_id | Single | No | Deal reviews query |

### Recommended Future Indexes

- **deals:** Text index on `(title, description, tags)` for full-text search
- **deals:** Index on `category` for category filtering
- **deals:** Index on `location.city` for location filtering
- **businesses:** Index on `category` for category filtering
- **businesses:** Index on `location.city` for location filtering

---

## 💾 Storage Estimates

| Collection | Avg Doc Size | Est. Documents | Est. Storage |
|------------|--------------|----------------|--------------|
| users | 250 bytes | 10,000 | 2.5 MB |
| deals | 1 KB | 50,000 | 50 MB |
| businesses | 800 bytes | 5,000 | 4 MB |
| categories | 150 bytes | 20 | 3 KB |
| favorites | 100 bytes | 100,000 | 10 MB |
| reviews | 300 bytes | 75,000 | 22.5 MB |
| **Total** | - | **240,020** | **~89 MB** |

---

## 🔐 Data Security

1. **Password Storage:** Bcrypt hashing with salt
2. **Sensitive Fields:** Never exposed in API responses
3. **Email Privacy:** Converted to lowercase for consistency
4. **Soft Deletes:** Consider implementing for data retention
5. **Audit Trail:** Timestamps track all changes

---

## 📝 Notes

- All `_id` fields are MongoDB ObjectId (24-character hex string)
- All timestamps are stored in UTC
- All prices are in PLN (Polish Złoty)
- Location coordinates use standard lat/long format
- HTTP URLs validated via Pydantic HttpUrl type
- Email addresses validated via Pydantic EmailStr type

---

**Last Updated:** November 24, 2025  
**Version:** 1.0.0 (Phase 5 Complete)  
**Database:** MongoDB Atlas  
**Collections:** 6

---

For API documentation, see [SaveMate_API_Documentation.md](SaveMate_API_Documentation.md)
