# Code Style Guide

## Python (Backend)

Follow PEP 8:
```python
# Good
def create_user(email: str, username: str) -> User:
    """Create a new user account."""
    return User(email=email, username=username)

# Bad
def createuser(e,u):
    return User(email=e,username=u)
```

Use type hints:
```python
def get_deals(category: str = None) -> list[Deal]:
    pass
```

## JavaScript (Frontend)

Use ESLint:
```javascript
// Good
const handleSubmit = async (event) => {
  event.preventDefault();
  await login(email, password);
};

// Bad
const handleSubmit=async(e)=>{e.preventDefault();await login(email,password);}
```

## Component Structure

```jsx
// 1. Imports
import { useState } from 'react';

// 2. Component
export default function MyComponent({ prop1, prop2 }) {
  // 3. State
  const [state, setState] = useState();
  
  // 4. Effects
  useEffect(() => {}, []);
  
  // 5. Handlers
  const handleClick = () => {};
  
  // 6. Render
  return <div>...</div>;
}
```
