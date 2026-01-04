# Admin API Endpoints

**Base Path:** `/api/v1/admin`

## GET /users

List all users (admin only).

**Headers:**
```
Authorization: Bearer <admin_token>
```

**Response (200):**
```json
[
  {
    "id": "user_id",
    "email": "user@example.com",
    "username": "johndoe",
    "role": "user",
    "created_at": "2025-01-01T00:00:00Z"
  }
]
```

## GET /stats

Platform statistics.

**Response (200):**
```json
{
  "total_users": 13,
  "total_businesses": 4,
  "total_deals": 56,
  "pending_verifications": 2,
  "active_deals": 52
}
```

## GET /pending

Get pending verifications.

**Response (200):**
```json
[
  {
    "business_id": "business_id",
    "business_name": "New Business",
    "submitted_at": "2025-01-01T00:00:00Z",
    "documents": ["license", "tax_id", "address"]
  }
]
```
