# Authentication API Endpoints

**Base Path:** `/api/v1/auth`

## POST /register

Register new user account.

**Request Body:**
```json
{
  "email": "user@example.com",
  "username": "johndoe",
  "password": "SecurePass123"
}
```

**Response (201):**
```json
{
  "id": "user_id",
  "email": "user@example.com",
  "username": "johndoe",
  "created_at": "2025-01-01T00:00:00Z"
}
```

## POST /login

Login existing user.

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "SecurePass123"
}
```

**Response (200):**
```json
{
  "access_token": "eyJhbGci...",
  "token_type": "bearer",
  "expires_in": 1800
}
```

## GET /me

Get current authenticated user.

**Headers:**
```
Authorization: Bearer <access_token>
```

**Response (200):**
```json
{
  "id": "user_id",
  "email": "user@example.com",
  "username": "johndoe",
  "role": "user",
  "profile": {
    "name": "John Doe",
    "avatar_url": "https://..."
  }
}
```

For complete authentication guide, see [AUTHENTICATION_GUIDE.md](../guides/AUTHENTICATION_GUIDE.md)
