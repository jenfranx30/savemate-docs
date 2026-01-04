# Business Verification System Guide

## Verification Process

### 1. Business Registration
- Business creates account
- Fills business profile
- Verification status: "pending"

### 2. Document Submission
Required documents:
- Business license
- Tax ID/EIN
- Proof of address (utility bill, lease)

### 3. Admin Review
- Admin views submitted documents
- Verifies authenticity
- Approves or rejects

### 4. Verification Complete
- Status updated to "verified"
- Verification badge displayed
- Business can create deals

## Implementation

### Submit Documents (Business):
```javascript
const submitVerification = async (documents) => {
  const formData = new FormData();
  formData.append('business_license', documents.license);
  formData.append('tax_id', documents.taxId);
  formData.append('proof_of_address', documents.address);
  
  await axios.post('/api/v1/verification/submit', formData, {
    headers: { 'Content-Type': 'multipart/form-data' }
  });
};
```

### Approve/Reject (Admin):
```javascript
const approveVerification = async (businessId) => {
  await axios.put(`/api/v1/verification/${businessId}/approve`);
};
```

See [VERIFICATION_ENDPOINTS.md](../api/VERIFICATION_ENDPOINTS.md) for API details.
