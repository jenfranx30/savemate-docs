# Pydantic Schemas

## User Schemas

```python
from pydantic import BaseModel, EmailStr

class UserCreate(BaseModel):
    email: EmailStr
    username: str
    password: str

class UserLogin(BaseModel):
    email: EmailStr
    password: str

class UserResponse(BaseModel):
    id: str
    email: EmailStr
    username: str
    role: str
```

## Deal Schemas

```python
class DealCreate(BaseModel):
    title: str
    description: str
    category: str
    original_price: float
    discounted_price: float
    end_date: datetime

class DealResponse(BaseModel):
    id: str
    title: str
    discount_percentage: float
    image_url: str
```
