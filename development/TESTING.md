# Testing Guide

## Backend Tests (pytest)

```python
# tests/test_auth.py
import pytest
from app.services.auth import verify_password, get_password_hash

def test_password_hashing():
    password = "SecurePass123"
    hashed = get_password_hash(password)
    assert verify_password(password, hashed)
    assert not verify_password("wrong", hashed)

@pytest.mark.asyncio
async def test_user_registration(client):
    response = await client.post("/api/v1/auth/register", json={
        "email": "test@example.com",
        "username": "testuser",
        "password": "SecurePass123"
    })
    assert response.status_code == 201
```

Run tests:
```bash
pytest tests/ -v
```

## Frontend Tests (Vitest)

```javascript
// tests/components/Button.test.jsx
import { render, screen } from '@testing-library/react';
import Button from '../Button';

test('renders button with text', () => {
  render(<Button>Click me</Button>);
  expect(screen.getByText('Click me')).toBeInTheDocument();
});
```

Run tests:
```bash
npm test
```

## E2E Tests (Playwright)

See [E2E_TESTING_README.md] for complete Playwright test suite.(To be Implemented)
