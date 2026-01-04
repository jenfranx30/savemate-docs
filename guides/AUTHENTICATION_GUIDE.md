# Authentication Implementation Guide

## JWT Authentication Flow

1. **User Login:**
   - Submit email + password
   - Backend verifies credentials
   - Returns access_token + refresh_token

2. **API Requests:**
   - Include token in header:
     ```
     Authorization: Bearer <access_token>
     ```

3. **Token Refresh:**
   - Access token expires after 30 minutes
   - Use refresh token to get new access token

## Implementation

### Frontend (React):
```javascript
const login = async (email, password) => {
  const response = await axios.post('/api/v1/auth/login', {
    email,
    password
  });
  
  localStorage.setItem('token', response.data.access_token);
};
```

### Backend (FastAPI):
```python
@router.post("/login")
async def login(credentials: LoginSchema):
    user = await User.find_one(User.email == credentials.email)
    if not verify_password(credentials.password, user.password_hash):
        raise HTTPException(401, "Invalid credentials")
    
    token = create_access_token({"sub": user.email})
    return {"access_token": token}
```

See [AUTH_ENDPOINTS.md](../api/AUTH_ENDPOINTS.md) for complete API documentation.
