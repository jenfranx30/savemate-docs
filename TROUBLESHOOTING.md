# SaveMate Troubleshooting Guide

## Common Issues

### Password Too Long Error
**Error:** "password cannot be longer than 72 bytes"
**Solution:** Add `maxLength={72}` to password inputs

### CORS Error
**Error:** "CORS policy: No 'Access-Control-Allow-Origin'"
**Solution:** Update backend CORS to include frontend URL

### Database Connection Failed
**Error:** "Failed to connect to MongoDB"
**Solution:** Check MongoDB URL, network access, and credentials

### Login 500 Error
**Error:** "Internal Server Error on login"
**Solution:** Check backend logs, verify password length, check MongoDB connection

See complete troubleshooting in deployment report.
