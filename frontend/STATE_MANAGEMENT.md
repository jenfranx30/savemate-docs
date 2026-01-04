# State Management with Context API

## AuthContext

```jsx
// contexts/AuthContext.jsx
import { createContext, useState, useEffect } from 'react';

export const AuthContext = createContext();

export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  const [token, setToken] = useState(localStorage.getItem('token'));
  
  const login = async (email, password) => {
    const response = await axios.post('/api/v1/auth/login', {
      email,
      password
    });
    
    setToken(response.data.access_token);
    localStorage.setItem('token', response.data.access_token);
  };
  
  const logout = () => {
    setToken(null);
    setUser(null);
    localStorage.removeItem('token');
  };
  
  return (
    <AuthContext.Provider value={{ user, token, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}
```

## Usage

```jsx
import { useContext } from 'react';
import { AuthContext } from './contexts/AuthContext';

function LoginForm() {
  const { login } = useContext(AuthContext);
  
  const handleSubmit = async (e) => {
    e.preventDefault();
    await login(email, password);
  };
  
  return <form onSubmit={handleSubmit}>...</form>;
}
```
