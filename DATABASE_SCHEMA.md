# SaveMate Database Schema

**Database:** MongoDB (NoSQL)  
**ODM:** Beanie  
**Version:** 1.0.0

---

## Collections Overview

| Collection | Documents | Description |
|------------|-----------|-------------|
| users | 13 | User accounts (consumers & businesses) |
| businesses | 4 | Business profiles |
| deals | 56 | Active and expired deals |
| categories | 20 | Deal categories |
| favorites | ~50 | User-saved deals |
| reviews | ~30 | Deal reviews and ratings |
| verification_documents | Variable | Business verification documents |
| business_verification_status | Variable | Verification tracking |

---

## Users Collection

```javascript
{
  "_id": ObjectId("..."),
  "email": "user@example.com",        // unique, indexed
  "username": "john_doe",             // unique, indexed
  "password_hash": "$2b$12...",
  "role": "user",                     // "user" | "business"
  "created_at": ISODate("2025-01-01T00:00:00Z"),
  "updated_at": ISODate("2025-01-01T00:00:00Z"),
  "profile": {
    "name": "John Doe",
    "phone": "+1234567890",
    "avatar_url": "https://...",
    "bio": "..."
  },
  "favorites": [
    ObjectId("deal1"),
    ObjectId("deal2")
  ],
  "settings": {
    "notifications_enabled": true,
    "email_updates": true,
    "theme": "light"
  }
}
```

**Indexes:**
```javascript
{ "email": 1 } unique
{ "username": 1 } unique
{ "role": 1 }
```

---

## Businesses Collection

```javascript
{
  "_id": ObjectId("..."),
  "user_id": ObjectId("..."),         // reference to users
  "business_name": "Awesome Business",
  "description": "We sell great things",
  "email": "business@example.com",
  "phone": "+1234567890",
  "category": "Food & Dining",
  "location": {
    "address": "123 Main St",
    "city": "New York",
    "state": "NY",
    "zip": "10001",
    "coordinates": [-73.9857, 40.7484]  // [lng, lat]
  },
  "verification_status": "verified",   // "pending" | "verified" | "rejected"
  "verified_at": ISODate("2025-01-15T00:00:00Z"),
  "logo_url": "https://...",
  "images": ["https://...", "https://..."],
  "hours": {
    "monday": "9:00 AM - 5:00 PM",
    "tuesday": "9:00 AM - 5:00 PM",
    // ...
  },
  "social": {
    "website": "https://...",
    "facebook": "https://...",
    "instagram": "https://..."
  },
  "stats": {
    "total_deals": 12,
    "total_views": 1500,
    "total_saves": 234
  },
  "created_at": ISODate("2025-01-01T00:00:00Z"),
  "updated_at": ISODate("2025-01-01T00:00:00Z")
}
```

**Indexes:**
```javascript
{ "user_id": 1 }
{ "verification_status": 1 }
{ "location.coordinates": "2dsphere" }  // for geo queries
{ "category": 1 }
```

---

## Deals Collection

```javascript
{
  "_id": ObjectId("..."),
  "business_id": ObjectId("..."),
  "title": "50% Off All Items",
  "description": "Limited time offer on all products",
  "category": "Retail",
  "original_price": 100.00,
  "discounted_price": 50.00,
  "discount_percentage": 50,
  "image_url": "https://...",
  "start_date": ISODate("2025-01-01T00:00:00Z"),
  "end_date": ISODate("2025-02-01T00:00:00Z"),
  "active": true,
  "views": 123,
  "saves": 45,
  "redemptions": 12,
  "terms": "Cannot be combined with other offers",
  "tags": ["sale", "clearance", "limited-time"],
  "created_at": ISODate("2025-01-01T00:00:00Z"),
  "updated_at": ISODate("2025-01-01T00:00:00Z")
}
```

**Indexes:**
```javascript
{ "business_id": 1 }
{ "category": 1 }
{ "active": 1 }
{ "end_date": 1 }
{ "title": "text", "description": "text" }  // full-text search
```

---

## Categories Collection

```javascript
{
  "_id": ObjectId("..."),
  "name": "Food & Dining",
  "slug": "food-dining",
  "icon": "🍔",
  "description": "Restaurants, cafes, and food services",
  "active": true,
  "order": 1,
  "created_at": ISODate("2025-01-01T00:00:00Z")
}
```

---

## Favorites Collection

```javascript
{
  "_id": ObjectId("..."),
  "user_id": ObjectId("..."),
  "deal_id": ObjectId("..."),
  "created_at": ISODate("2025-01-01T00:00:00Z")
}
```

**Indexes:**
```javascript
{ "user_id": 1, "deal_id": 1 } unique
```

---

## Reviews Collection

```javascript
{
  "_id": ObjectId("..."),
  "user_id": ObjectId("..."),
  "deal_id": ObjectId("..."),
  "business_id": ObjectId("..."),
  "rating": 5,                // 1-5 stars
  "title": "Great deal!",
  "comment": "Really enjoyed this offer",
  "verified_purchase": true,
  "helpful_count": 10,
  "created_at": ISODate("2025-01-01T00:00:00Z"),
  "updated_at": ISODate("2025-01-01T00:00:00Z")
}
```

**Indexes:**
```javascript
{ "deal_id": 1 }
{ "business_id": 1 }
{ "user_id": 1 }
```

---

## Database Queries

### Find nearby businesses:
```javascript
db.businesses.find({
  "location.coordinates": {
    $nearSphere: {
      $geometry: {
        type: "Point",
        coordinates: [-73.9857, 40.7484]  // [lng, lat]
      },
      $maxDistance: 5000  // meters
    }
  }
})
```

### Search deals:
```javascript
db.deals.find({
  $text: { $search: "pizza" },
  active: true,
  end_date: { $gte: new Date() }
})
```

### Get user favorites with deal details:
```javascript
db.users.aggregate([
  { $match: { _id: userId } },
  { $lookup: {
      from: "deals",
      localField: "favorites",
      foreignField: "_id",
      as: "favorite_deals"
  }}
])
```

---

For full model implementation, see [Backend Models](./backend/MODELS.md)
