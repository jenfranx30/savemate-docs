# React Component Guide

## Component Structure

```
src/
├── components/
│   ├── common/          # Reusable components
│   │   ├── Button.jsx
│   │   ├── Card.jsx
│   │   └── Input.jsx
│   ├── layout/          # Layout components
│   │   ├── Header.jsx
│   │   ├── Footer.jsx
│   │   └── Navigation.jsx
│   └── features/        # Feature-specific
│       ├── DealCard.jsx
│       ├── BusinessCard.jsx
│       └── ReviewForm.jsx
```

## Example Components

### DealCard.jsx
```jsx
export default function DealCard({ deal }) {
  return (
    <div className="bg-white rounded-lg shadow p-4">
      <img src={deal.image_url} alt={deal.title} />
      <h3>{deal.title}</h3>
      <p>{deal.discount_percentage}% OFF</p>
      <button>Save Deal</button>
    </div>
  );
}
```

### Button.jsx (Reusable)
```jsx
export default function Button({ children, variant, onClick }) {
  const baseClasses = "px-4 py-2 rounded font-medium";
  const variants = {
    primary: "bg-blue-600 text-white",
    secondary: "bg-gray-200 text-gray-800"
  };
  
  return (
    <button 
      className={`${baseClasses} ${variants[variant]}`}
      onClick={onClick}
    >
      {children}
    </button>
  );
}
```
