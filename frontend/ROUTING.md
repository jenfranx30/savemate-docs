# React Router Configuration

## Route Setup

```jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      <Routes>
        {/* Public routes */}
        <Route path="/" element={<Home />} />
        <Route path="/login" element={<Login />} />
        <Route path="/register" element={<Register />} />
        <Route path="/deals" element={<Deals />} />
        
        {/* Protected routes */}
        <Route path="/dashboard" element={
          <ProtectedRoute>
            <Dashboard />
          </ProtectedRoute>
        } />
        
        {/* Business routes */}
        <Route path="/business/dashboard" element={
          <BusinessRoute>
            <BusinessDashboard />
          </BusinessRoute>
        } />
      </Routes>
    </BrowserRouter>
  );
}
```

## Protected Route Component

```jsx
function ProtectedRoute({ children }) {
  const { token } = useContext(AuthContext);
  
  if (!token) {
    return <Navigate to="/login" replace />;
  }
  
  return children;
}
```
