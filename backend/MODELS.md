# Database Models (Beanie ODM)

## User Model

```python
from beanie import Document
from pydantic import EmailStr
from datetime import datetime

class User(Document):
    email: EmailStr
    username: str
    password_hash: str
    role: str = "user"  # "user" | "business"
    created_at: datetime = datetime.utcnow()
    favorites: list[str] = []
    
    class Settings:
        name = "users"
        indexes = [
            IndexModel([("email", 1)], unique=True),
            IndexModel([("username", 1)], unique=True)
        ]
```

## Business Model

```python
class Business(Document):
    user_id: str
    business_name: str
    email: EmailStr
    category: str
    verification_status: str = "pending"
    location: dict
    
    class Settings:
        name = "businesses"
        indexes = [
            IndexModel([("user_id", 1)]),
            IndexModel([("verification_status", 1)])
        ]
```

## Deal Model

```python
class Deal(Document):
    business_id: str
    title: str
    description: str
    category: str
    original_price: float
    discounted_price: float
    active: bool = True
    views: int = 0
    saves: int = 0
    
    class Settings:
        name = "deals"
        indexes = [
            IndexModel([("business_id", 1)]),
            IndexModel([("category", 1)]),
            IndexModel([("active", 1)])
        ]
```
