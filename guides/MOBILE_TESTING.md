# Mobile Testing Guide

## Testing on Physical Devices

### 1. Start Dev Servers with Network Access
```bash
# Backend
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000

# Frontend  
npm run dev -- --host 0.0.0.0
```

### 2. Find Your Local IP
```bash
# macOS/Linux
ifconfig | grep "inet " | grep -v 127.0.0.1

# Windows
ipconfig
```

### 3. Access from Mobile
- Connect phone to same WiFi network
- Open browser: `http://<your-ipv4 address>:5173`

## Browser Developer Tools

### Chrome DevTools
1. Open DevTools (F12)
2. Click device toggle (Ctrl+Shift+M)
3. Select device or set custom dimensions

### Responsive Testing Sizes
- iPhone 12: 390x844
- Pixel 5: 393x851  
- iPad: 768x1024

## E2E Testing

See [E2E_TESTING_README.md] for Playwright mobile testing setup.(To be Implemented)
