# SaveMate Database Schema - Complete Implementation
## MongoDB with Beanie ODM (Python)

---

## Overview

This document provides the complete MongoDB database schema for SaveMate using **Beanie ODM** (Object Document Mapper) for Python/FastAPI.

### Why Beanie?
- ✅ Async/await support (works perfectly with FastAPI)
- ✅ Pydantic v2 integration (automatic validation)
- ✅ Type hints everywhere
- ✅ Clean, Pythonic API
- ✅ Automatic index management

---

## Database Structure

**Database Name**: `savemate`

**Collections**:
1. `users` - User accounts and profiles
2. `deals` - Local deals and promotions
3. `businesses` - Business information
4. `categories` - Deal categories
5. `reviews` (optional future feature)

---

## Collection 1: USERS

### Purpose
Store user account information, authentication credentials, preferences, and saved deals.

### Beanie Model (`app/models/user.py`)

```python
from datetime import datetime
from typing import Optional, List
from beanie import Document, Indexed, Link
from pydantic import EmailStr, Field, field_validator
from pymongo import IndexModel, GEOSPHERE

class Location(BaseModel):
    """Embedded GeoJSON location"""
    type: str = "Point"
    coordinates: List[float]  # [longitude, latitude]
    
    @field_validator('coordinates')
    @classmethod
    def validate_coordinates(cls, v):
        if len(v) != 2:
            raise ValueError('Coordinates must be [longitude, latitude]')
        lng, lat = v
        if not (-180 <= lng <= 180):
            raise ValueError('Longitude must be between -180 and 180')
        if not (-90 <= lat <= 90):
            raise ValueError('Latitude must be between -90 and 90')
        return v


class UserPreferences(BaseModel):
    """Embedded user preferences"""
    favorite_categories: List[str] = Field(default_factory=list)
    notification_radius: float = 5.0  # km
    email_notifications: bool = True
    push_notifications: bool = True


class User(Document):
    """User document model"""
    
    # Authentication
    email: Indexed(EmailStr, unique=True)
    username: Indexed(str, unique=True)
    password_hash: str
    
    # Profile
    full_name: str
    profile_picture: Optional[str] = None  # Cloudinary URL
    bio: Optional[str] = Field(None, max_length=500)
    phone: Optional[str] = None
    
    # Location (GeoJSON format for geospatial queries)
    location: Optional[Location] = None
    
    # Preferences
    preferences: UserPreferences = Field(default_factory=UserPreferences)
    
    # Saved deals (references to Deal documents)
    saved_deals: List[str] = Field(default_factory=list)  # Deal IDs
    
    # Business owner status
    is_business_owner: bool = False
    owned_businesses: List[str] = Field(default_factory=list)  # Business IDs
    
    # Account status
    is_active: bool = True
    is_verified: bool = False
    email_verified: bool = False
    
    # Timestamps
    created_at: datetime = Field(default_factory=datetime.utcnow)
    updated_at: datetime = Field(default_factory=datetime.utcnow)
    last_login: Optional[datetime] = None
    
    class Settings:
        name = "users"  # Collection name
        indexes = [
            IndexModel([("location", GEOSPHERE)]),  # Geospatial index
            "email",
            "username",
            "created_at",
        ]
    
    class Config:
        json_schema_extra = {
            "example": {
                "email": "john.doe@example.com",
                "username": "johndoe",
                "full_name": "John Doe",
                "location": {
                    "type": "Point",
                    "coordinates": [-74.0060, 40.7128]  # NYC
                },
                "is_business_owner": False
            }
        }
```

### Fields Explanation:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `email` | EmailStr | Yes | Unique email address |
| `username` | str | Yes | Unique username |
| `password_hash` | str | Yes | Bcrypt hashed password |
| `full_name` | str | Yes | User's full name |
| `profile_picture` | str | No | Cloudinary URL |
| `location` | GeoJSON | No | User's location for nearby deals |
| `preferences` | Object | Yes | Notification and category preferences |
| `saved_deals` | Array | Yes | List of saved deal IDs |
| `is_business_owner` | bool | Yes | Whether user owns businesses |
| `is_active` | bool | Yes | Account active status |
| `created_at` | datetime | Yes | Account creation timestamp |

### Indexes:
1. **email** (unique) - Fast login lookups
2. **username** (unique) - Fast profile lookups
3. **location** (2dsphere) - Geospatial queries for nearby deals
4. **created_at** - Sorting users by registration date

---

## Collection 2: DEALS

### Purpose
Store local deals, promotions, and discounts offered by businesses.

### Beanie Model (`app/models/deal.py`)

```python
from datetime import datetime
from typing import Optional, List
from beanie import Document, Indexed, Link, BackLink
from pydantic import Field, field_validator, computed_field
from pymongo import IndexModel, GEOSPHERE, DESCENDING

class Address(BaseModel):
    """Embedded address"""
    street: str
    city: str
    state: str
    zip_code: str
    country: str = "Poland"


class Deal(Document):
    """Deal document model"""
    
    # Basic Information
    title: Indexed(str) = Field(..., min_length=3, max_length=200)
    description: str = Field(..., min_length=10, max_length=2000)
    
    # Relationships
    business_id: str  # Reference to Business
    category_id: str  # Reference to Category
    created_by: str  # Reference to User (business owner)
    
    # Pricing
    original_price: float = Field(..., gt=0)
    discounted_price: float = Field(..., gt=0)
    discount_percentage: Optional[float] = Field(None, ge=0, le=100)
    currency: str = "PLN"
    
    # Media
    images: List[str] = Field(default_factory=list)  # Cloudinary URLs
    
    # Location (GeoJSON format)
    location: Location
    address: Address
    
    # Validity
    valid_from: datetime = Field(default_factory=datetime.utcnow)
    valid_until: datetime
    
    # Terms
    terms_conditions: Optional[str] = Field(None, max_length=1000)
    redemption_code: Optional[str] = None
    max_redemptions: Optional[int] = None
    current_redemptions: int = 0
    
    # Engagement Metrics
    views_count: int = 0
    saves_count: int = 0
    shares_count: int = 0
    
    # Status
    is_active: bool = True
    is_featured: bool = False
    
    # Timestamps
    created_at: datetime = Field(default_factory=datetime.utcnow)
    updated_at: datetime = Field(default_factory=datetime.utcnow)
    
    @computed_field
    @property
    def is_expired(self) -> bool:
        """Check if deal has expired"""
        return datetime.utcnow() > self.valid_until
    
    @computed_field
    @property
    def days_remaining(self) -> int:
        """Calculate days remaining"""
        delta = self.valid_until - datetime.utcnow()
        return max(0, delta.days)
    
    @field_validator('discounted_price')
    @classmethod
    def validate_discount(cls, v, info):
        """Ensure discounted price is less than original"""
        if 'original_price' in info.data and v >= info.data['original_price']:
            raise ValueError('Discounted price must be less than original price')
        return v
    
    def calculate_discount_percentage(self):
        """Calculate discount percentage"""
        self.discount_percentage = round(
            ((self.original_price - self.discounted_price) / self.original_price) * 100,
            2
        )
    
    class Settings:
        name = "deals"
        indexes = [
            IndexModel([("location", GEOSPHERE)]),  # Geospatial queries
            IndexModel([("business_id", 1)]),
            IndexModel([("category_id", 1)]),
            IndexModel([("created_by", 1)]),
            IndexModel([("valid_until", 1)]),  # For expiration checks
            IndexModel([("created_at", DESCENDING)]),  # Newest first
            IndexModel([("is_active", 1), ("valid_until", 1)]),  # Active deals
            IndexModel([("title", "text"), ("description", "text")]),  # Text search
        ]
    
    class Config:
        json_schema_extra = {
            "example": {
                "title": "50% Off All Pizzas",
                "description": "Get half off on any pizza during lunch hours",
                "business_id": "507f1f77bcf86cd799439011",
                "category_id": "507f1f77bcf86cd799439012",
                "original_price": 40.0,
                "discounted_price": 20.0,
                "valid_until": "2025-12-31T23:59:59",
                "location": {
                    "type": "Point",
                    "coordinates": [-74.0060, 40.7128]
                },
                "address": {
                    "street": "123 Main St",
                    "city": "Warsaw",
                    "state": "Mazovia",
                    "zip_code": "00-001",
                    "country": "Poland"
                }
            }
        }
```

### Fields Explanation:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `title` | str | Yes | Deal title (3-200 chars) |
| `description` | str | Yes | Detailed description (10-2000 chars) |
| `business_id` | str | Yes | Reference to business |
| `category_id` | str | Yes | Reference to category |
| `original_price` | float | Yes | Original price before discount |
| `discounted_price` | float | Yes | Price after discount |
| `discount_percentage` | float | No | Calculated percentage off |
| `images` | Array | No | List of image URLs |
| `location` | GeoJSON | Yes | Deal location for map |
| `valid_until` | datetime | Yes | Expiration date/time |
| `is_active` | bool | Yes | Whether deal is currently active |

### Indexes:
1. **location** (2dsphere) - Find nearby deals
2. **business_id** - Get all deals for a business
3. **category_id** - Filter deals by category
4. **valid_until** - Check for expired deals
5. **created_at** (desc) - Sort by newest
6. **text** (title, description) - Full-text search

---

## Collection 3: BUSINESSES

### Purpose
Store business information for companies offering deals.

### Beanie Model (`app/models/business.py`)

```python
from datetime import datetime
from typing import Optional, List, Dict
from beanie import Document, Indexed
from pydantic import Field, EmailStr, HttpUrl
from pymongo import IndexModel, GEOSPHERE

class ContactInfo(BaseModel):
    """Embedded contact information"""
    phone: Optional[str] = None
    email: Optional[EmailStr] = None
    website: Optional[HttpUrl] = None


class SocialMedia(BaseModel):
    """Embedded social media links"""
    facebook: Optional[str] = None
    instagram: Optional[str] = None
    twitter: Optional[str] = None
    linkedin: Optional[str] = None


class OperatingHours(BaseModel):
    """Embedded operating hours"""
    open: str  # e.g., "09:00"
    close: str  # e.g., "21:00"
    closed: bool = False


class Business(Document):
    """Business document model"""
    
    # Basic Information
    name: Indexed(str) = Field(..., min_length=2, max_length=200)
    description: str = Field(..., max_length=2000)
    category: str  # e.g., "Restaurant", "Retail", "Services"
    
    # Ownership
    owner_id: str  # Reference to User
    
    # Media
    logo: Optional[str] = None  # Cloudinary URL
    cover_image: Optional[str] = None  # Cloudinary URL
    gallery: List[str] = Field(default_factory=list)  # Additional images
    
    # Location
    location: Location
    address: Address
    
    # Contact
    contact: ContactInfo = Field(default_factory=ContactInfo)
    social_media: SocialMedia = Field(default_factory=SocialMedia)
    
    # Operating Hours (day: hours)
    operating_hours: Dict[str, OperatingHours] = Field(default_factory=dict)
    
    # Statistics
    rating: float = Field(default=0.0, ge=0, le=5)
    total_reviews: int = 0
    total_deals: int = 0
    followers_count: int = 0
    
    # Verification
    is_verified: bool = False
    verification_date: Optional[datetime] = None
    
    # Status
    is_active: bool = True
    
    # Timestamps
    created_at: datetime = Field(default_factory=datetime.utcnow)
    updated_at: datetime = Field(default_factory=datetime.utcnow)
    
    class Settings:
        name = "businesses"
        indexes = [
            IndexModel([("location", GEOSPHERE)]),
            IndexModel([("owner_id", 1)]),
            IndexModel([("name", "text"), ("description", "text")]),
            "is_verified",
            "category",
        ]
    
    class Config:
        json_schema_extra = {
            "example": {
                "name": "Pizza Palace",
                "description": "Best pizza in Warsaw since 2010",
                "category": "Restaurant",
                "owner_id": "507f1f77bcf86cd799439011",
                "location": {
                    "type": "Point",
                    "coordinates": [21.0122, 52.2297]
                },
                "address": {
                    "street": "ul. Marszałkowska 123",
                    "city": "Warsaw",
                    "state": "Mazovia",
                    "zip_code": "00-001",
                    "country": "Poland"
                },
                "contact": {
                    "phone": "+48 22 123 4567",
                    "email": "contact@pizzapalace.pl",
                    "website": "https://pizzapalace.pl"
                }
            }
        }
```

### Fields Explanation:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | str | Yes | Business name |
| `description` | str | Yes | About the business |
| `category` | str | Yes | Business type |
| `owner_id` | str | Yes | Reference to User |
| `logo` | str | No | Business logo URL |
| `location` | GeoJSON | Yes | Business location |
| `address` | Object | Yes | Full address |
| `contact` | Object | No | Phone, email, website |
| `operating_hours` | Object | No | Weekly schedule |
| `rating` | float | Yes | Average rating (0-5) |
| `is_verified` | bool | Yes | Verified badge status |

### Indexes:
1. **location** (2dsphere) - Find nearby businesses
2. **owner_id** - Get businesses owned by user
3. **text** (name, description) - Search businesses
4. **category** - Filter by business type

---

## Collection 4: CATEGORIES

### Purpose
Store deal categories for organization and filtering.

### Beanie Model (`app/models/category.py`)

```python
from datetime import datetime
from typing import Optional
from beanie import Document, Indexed
from pydantic import Field

class Category(Document):
    """Category document model"""
    
    # Basic Information
    name: Indexed(str, unique=True) = Field(..., min_length=2, max_length=100)
    slug: Indexed(str, unique=True)  # URL-friendly name
    description: Optional[str] = Field(None, max_length=500)
    
    # Visual
    icon: str = "tag"  # Icon name or emoji
    color: str = "#3B82F6"  # Hex color code
    image: Optional[str] = None  # Category image URL
    
    # Hierarchy
    parent_category: Optional[str] = None  # For subcategories
    order: int = 0  # Display order
    
    # Statistics
    deals_count: int = 0
    
    # Status
    is_active: bool = True
    is_featured: bool = False
    
    # Timestamps
    created_at: datetime = Field(default_factory=datetime.utcnow)
    
    class Settings:
        name = "categories"
        indexes = [
            "name",
            "slug",
            "parent_category",
            "is_active",
        ]
    
    class Config:
        json_schema_extra = {
            "example": {
                "name": "Food & Dining",
                "slug": "food-dining",
                "description": "Restaurants, cafes, and food delivery",
                "icon": "🍔",
                "color": "#EF4444",
                "is_featured": True
            }
        }
```

### Fields Explanation:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | str | Yes | Category name (unique) |
| `slug` | str | Yes | URL-friendly identifier |
| `description` | str | No | Category description |
| `icon` | str | Yes | Icon/emoji representation |
| `color` | str | Yes | Theme color (hex) |
| `parent_category` | str | No | For nested categories |
| `deals_count` | int | Yes | Number of deals in category |
| `is_active` | bool | Yes | Whether category is visible |

### Suggested Categories:
1. 🍔 Food and Dining
2. 🛍️ Shopping and Retail
3. 💇 Beauty and Wellness
4. 🎭 Entertainment
5. 🏋️ Fitness and Sports
6. 🏨 Travel and Hotels
7. 🚗 Automotive
8. 🏠 Home and Garden
9. 📚 Education
10. 💼 Services

---

## Geospatial Queries Examples

### Find Deals Near Location

```python
from motor.motor_asyncio import AsyncIOMotorClient

async def get_nearby_deals(lat: float, lng: float, radius_km: float = 5):
    """Find deals within radius of coordinates"""
    
    radius_meters = radius_km * 1000
    
    deals = await Deal.find(
        {
            "location": {
                "$near": {
                    "$geometry": {
                        "type": "Point",
                        "coordinates": [lng, lat]
                    },
                    "$maxDistance": radius_meters
                }
            },
            "is_active": True,
            "valid_until": {"$gte": datetime.utcnow()}
        }
    ).to_list()
    
    return deals
```

### Find Deals Within Polygon (City Boundaries)

```python
async def get_deals_in_area(polygon_coords: List[List[float]]):
    """Find deals within a geographic polygon"""
    
    deals = await Deal.find(
        {
            "location": {
                "$geoWithin": {
                    "$geometry": {
                        "type": "Polygon",
                        "coordinates": [polygon_coords]
                    }
                }
            },
            "is_active": True
        }
    ).to_list()
    
    return deals
```

---

## Database Initialization

### Setup Script (`app/database.py`)

```python
from beanie import init_beanie
from motor.motor_asyncio import AsyncIOMotorClient
from app.models.user import User
from app.models.deal import Deal
from app.models.business import Business
from app.models.category import Category
from app.config import settings

async def init_db():
    """Initialize database connection and models"""
    
    # Create Motor client
    client = AsyncIOMotorClient(settings.MONGODB_URL)
    
    # Initialize beanie with models
    await init_beanie(
        database=client[settings.DATABASE_NAME],
        document_models=[
            User,
            Deal,
            Business,
            Category,
        ]
    )
    
    print(f"✅ Connected to MongoDB: {settings.DATABASE_NAME}")
```

---

## Seeding Initial Data

### Category Seeder

```python
async def seed_categories():
    """Seed initial categories"""
    
    categories = [
        {"name": "Food & Dining", "slug": "food-dining", "icon": "🍔", "color": "#EF4444"},
        {"name": "Shopping & Retail", "slug": "shopping", "icon": "🛍️", "color": "#8B5CF6"},
        {"name": "Beauty & Wellness", "slug": "beauty", "icon": "💇", "color": "#EC4899"},
        {"name": "Entertainment", "slug": "entertainment", "icon": "🎭", "color": "#F59E0B"},
        {"name": "Fitness & Sports", "slug": "fitness", "icon": "🏋️", "color": "#10B981"},
        {"name": "Travel & Hotels", "slug": "travel", "icon": "🏨", "color": "#3B82F6"},
    ]
    
    for cat_data in categories:
        existing = await Category.find_one(Category.slug == cat_data["slug"])
        if not existing:
            category = Category(**cat_data)
            await category.insert()
            print(f"✅ Created category: {category.name}")
```

---

## Performance Optimization

### Indexing Strategy:
1. **Geospatial indexes** on all location fields
2. **Text indexes** for search functionality
3. **Compound indexes** for common query patterns
4. **Unique indexes** on email, username, slugs

### Query Optimization:
1. Use **projection** to fetch only needed fields
2. Implement **pagination** for large result sets
3. Use **aggregation pipelines** for complex queries
4. Cache frequently accessed data

---

## Data Validation Rules

### User:
- ✅ Email must be valid and unique
- ✅ Username: 3-30 characters, alphanumeric + underscore
- ✅ Password: min 8 characters (hashed with bcrypt)
- ✅ Location coordinates must be valid

### Deal:
- ✅ Title: 3-200 characters
- ✅ Discounted price < Original price
- ✅ Valid_until must be in the future
- ✅ At least 1 image recommended

### Business:
- ✅ Name: 2-200 characters
- ✅ Valid phone number format
- ✅ Valid email format
- ✅ Valid website URL

---

*Database Schema Document*
*Last Updated: November 22, 2025*
*Stack: MongoDB + Beanie ODM + FastAPI*
