# Verification API Endpoints

**Base Path:** `/api/v1/verification`

## POST /submit

Submit verification documents.

**Headers:**
```
Authorization: Bearer <business_token>
Content-Type: multipart/form-data
```

**Form Data:**
- `business_license`: File
- `tax_id`: File
- `proof_of_address`: File

**Response (201):**
```json
{
  "message": "Documents submitted successfully",
  "status": "pending_review"
}
```

## GET /status

Get verification status.

**Headers:**
```
Authorization: Bearer <business_token>
```

**Response (200):**
```json
{
  "status": "pending",
  "submitted_at": "2025-01-01T00:00:00Z",
  "documents": {
    "business_license": "submitted",
    "tax_id": "submitted",
    "proof_of_address": "submitted"
  }
}
```

## PUT /approve (Admin only)

Approve business verification.

**Request Body:**
```json
{
  "business_id": "business_id",
  "notes": "All documents verified"
}
```

**Response (200):**
```json
{
  "message": "Business verified successfully",
  "verified_at": "2025-01-15T00:00:00Z"
}
```
