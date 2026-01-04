# Business Logic Services

## Auth Service

```python
from passlib.context import CryptContext
from jose import jwt

pwd_context = CryptContext(schemes=["bcrypt"])

def verify_password(plain: str, hashed: str) -> bool:
    return pwd_context.verify(plain, hashed)

def get_password_hash(password: str) -> str:
    return pwd_context.hash(password)

def create_access_token(data: dict) -> str:
    return jwt.encode(data, SECRET_KEY, algorithm=ALGORITHM)
```

## Deal Service

```python
async def create_deal(deal_data: DealCreate, business_id: str):
    deal = Deal(**deal_data.dict(), business_id=business_id)
    await deal.insert()
    return deal

async def get_active_deals(category: str = None):
    query = {"active": True}
    if category:
        query["category"] = category
    return await Deal.find(query).to_list()
```
