# Deals API Endpoints

**Base Path:** `/api/v1/deals`

## GET /deals

List all active deals.

**Query Parameters:**
- `category`: Filter by category
- `limit`: Number of results (default: 50)
- `skip`: Pagination offset
- `sort`: Sort field (created_at, discount_percentage)

**Response (200):**
```json
[
  {
    "id": "deal_id",
    "title": "50% Off All Items",
    "description": "Limited time offer",
    "business_id": "business_id",
    "category": "Retail",
    "original_price": 100.00,
    "discounted_price": 50.00,
    "discount_percentage": 50,
    "image_url": "https://...",
    "end_date": "2025-02-01T00:00:00Z"
  }
]
```

## POST /deals

Create new deal (business only).

**Headers:**
```
Authorization: Bearer <business_token>
```

**Request Body:**
```json
{
  "title": "50% Off All Items",
  "description": "Limited time offer",
  "category": "Retail",
  "original_price": 100.00,
  "discounted_price": 50.00,
  "image_url": "https://...",
  "end_date": "2025-02-01"
}
```

**Response (201):**
```json
{
  "id": "deal_id",
  "title": "50% Off All Items",
  "created_at": "2025-01-01T00:00:00Z"
}
```

## GET /deals/{id}

Get deal by ID.

**Response (200):**
```json
{
  "id": "deal_id",
  "title": "50% Off All Items",
  "business": {
    "id": "business_id",
    "name": "Awesome Business",
    "verified": true
  },
  "views": 123,
  "saves": 45
}
```
