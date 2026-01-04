# SaveMate API Reference

**Base URL:** `https://savemate-backend-deployment1.onrender.com` *(production)*  
**Base URL:** `http://localhost:8000/api/v1` *(development)*

**Version:** 1.0.0  
**Last Updated:** January 2026

---

## Authentication

All authenticated endpoints require a Bearer token in the Authorization header:

```
Authorization: Bearer <access_token>
```

---

## Endpoints Summary

### Authentication
```
POST   /auth/register          - Register new user
POST   /auth/login             - Login user
POST   /auth/refresh           - Refresh access token
POST   /auth/logout            - Logout user
GET    /auth/me                - Get current user
PUT    /auth/profile           - Update profile
POST   /auth/forgot-password   - Request password reset
POST   /auth/reset-password    - Reset password
```

### Deals
```
GET    /deals                  - List all deals
POST   /deals                  - Create deal (business only)
GET    /deals/{id}             - Get deal by ID
PUT    /deals/{id}             - Update deal
DELETE /deals/{id}             - Delete deal
GET    /deals/search           - Search deals
GET    /deals/category/{cat}   - Get deals by category
GET    /deals/trending         - Get trending deals
```

### Businesses
```
GET    /businesses             - List all businesses
POST   /businesses             - Create business profile
GET    /businesses/{id}        - Get business by ID
PUT    /businesses/{id}        - Update business
GET    /businesses/me          - Get my business
GET    /businesses/{id}/deals  - Get business deals
GET    /businesses/nearby      - Get nearby businesses
```

### Categories
```
GET    /categories             - List all categories
POST   /categories             - Create category (admin)
GET    /categories/{id}        - Get category by ID
PUT    /categories/{id}        - Update category (admin)
DELETE /categories/{id}        - Delete category (admin)
```

### Favorites
```
GET    /favorites              - Get user's favorites
POST   /favorites/{deal_id}    - Add to favorites
DELETE /favorites/{deal_id}    - Remove from favorites
```

### Reviews
```
GET    /reviews/deal/{id}      - Get deal reviews
POST   /reviews                - Create review
PUT    /reviews/{id}           - Update review
DELETE /reviews/{id}           - Delete review
```

### Verification
```
POST   /verification/submit    - Submit verification documents
GET    /verification/status    - Get verification status
PUT    /verification/approve   - Approve (admin)
PUT    /verification/reject    - Reject (admin)
```

### Admin
```
GET    /admin/users            - List all users
PUT    /admin/users/{id}       - Update user
DELETE /admin/users/{id}       - Delete user
GET    /admin/stats            - Platform statistics
GET    /admin/pending          - Pending verifications
```

---

## Response Format

### Success Response
```json
{
  "success": true,
  "data": {
    // Response data
  },
  "message": "Operation successful"
}
```

### Error Response
```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human readable error message",
    "details": {}
  }
}
```

---

## Status Codes

```
200 OK                  - Successful request
201 Created             - Resource created
204 No Content          - Successful with no data
400 Bad Request         - Invalid input
401 Unauthorized        - Missing/invalid token
403 Forbidden           - Insufficient permissions
404 Not Found           - Resource not found
409 Conflict            - Resource already exists
422 Unprocessable       - Validation error
500 Internal Error      - Server error
```

---

For detailed endpoint documentation, see:
- [Auth Endpoints](./api/AUTH_ENDPOINTS.md)
- [Deals Endpoints](./api/DEALS_ENDPOINTS.md)
- [Business Endpoints](./api/BUSINESS_ENDPOINTS.md)
- [Verification Endpoints](./api/VERIFICATION_ENDPOINTS.md)

**Full interactive API docs:** `http://localhost:8000/docs` (Swagger UI)
