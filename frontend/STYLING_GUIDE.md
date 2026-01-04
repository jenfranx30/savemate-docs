# Tailwind CSS Styling Guide

## Common Patterns

### Card Component
```jsx
<div className="bg-white rounded-lg shadow-md p-6 hover:shadow-lg transition">
  {/* Card content */}
</div>
```

### Button Styles
```jsx
// Primary
<button className="bg-blue-600 text-white px-4 py-2 rounded-md hover:bg-blue-700">

// Secondary
<button className="bg-gray-200 text-gray-800 px-4 py-2 rounded-md hover:bg-gray-300">

// Danger
<button className="bg-red-600 text-white px-4 py-2 rounded-md hover:bg-red-700">
```

### Form Inputs
```jsx
<input 
  className="w-full px-4 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
/>
```

### Responsive Grid
```jsx
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
  {/* Grid items */}
</div>
```

### Mobile Navigation
```jsx
<nav className="fixed bottom-0 w-full bg-white border-t md:hidden">
  <div className="flex justify-around py-2">
    {/* Nav items */}
  </div>
</nav>
```
