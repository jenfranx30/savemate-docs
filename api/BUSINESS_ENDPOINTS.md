# Business API Endpoints

**Base Path:** `/api/v1/businesses`

## GET /businesses

List all verified businesses.

**Response (200):**
```json
[
  {
    "id": "business_id",
    "business_name": "Awesome Business",
    "category": "Food & Dining",
    "location": {
      "city": "New York",
      "state": "NY"
    },
    "verification_status": "verified",
    "logo_url": "https://..."
  }
]
```

## POST /businesses

Create business profile (requires user account).

**Request Body:**
```json
{
  "business_name": "Awesome Business",
  "email": "business@example.com",
  "phone": "+1234567890",
  "category": "Food & Dining",
  "description": "We sell great things",
  "location": {
    "address": "123 Main St",
    "city": "New York",
    "state": "NY",
    "zip": "10001"
  }
}
```

## GET /businesses/me

Get logged-in user's business profile.

**Headers:**
```
Authorization: Bearer <business_token>
```

**Response (200):**
```json
{
  "id": "business_id",
  "business_name": "Awesome Business",
  "verification_status": "pending",
  "stats": {
    "total_deals": 12,
    "total_views": 1500
  }
}
```
