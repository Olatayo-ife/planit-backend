# ðŸ” Authentication API Documentation

Base URL:
```
/api/auth
```

All responses follow this format:
```json
{
  "success": true,
  "message": "Message text",
  "data": { ... }
}
```
If an error occurs:
```json
{
  "success": false,
  "message": "Error description"
}
```

---

### **1ï¸âƒ£ POST /signup**
**Description:** Register a new user.

**Access:** Public

**Request Body:**
```json
{
  "fullName": "John Doe",
  "email": "john@example.com",
  "password": "yourPassword123",
  "phoneNumber": "+2348123456789",
  "role": "planner"  // Optional: defaults to "planner"
}
```

**Success Response (201):**
```json
{
  "success": true,
  "message": "User registered successfully",
  "data": {
    "user": {
      "id": "user_id",
      "email": "john@example.com",
      "fullName": "John Doe",
      "role": "planner",
      "phoneNumber": "+2348123456789"
    },
    "accessToken": "JWT_ACCESS_TOKEN",
    "refreshToken": "JWT_REFRESH_TOKEN"
  }
}
```

**Error Responses:**
- 409 â€” Email already exists
- 400 â€” Validation error

---

### **2ï¸âƒ£ POST /login**
**Description:** Authenticate an existing user.

**Access:** Public

**Request Body:**
```json
{
  "email": "john@example.com",
  "password": "yourPassword123"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Login successful",
  "data": {
    "user": {
      "id": "user_id",
      "email": "john@example.com",
      "fullName": "John Doe",
      "role": "planner",
      "phoneNumber": "+2348123456789"
    },
    "accessToken": "JWT_ACCESS_TOKEN",
    "refreshToken": "JWT_REFRESH_TOKEN"
  }
}
```

**Error Responses:**
- 401 â€” Invalid email or password
- 403 â€” Account deactivated

---

### **3ï¸âƒ£ POST /refresh**
**Description:** Generate a new access token using a valid refresh token.

**Access:** Public

**Request Body:**
```json
{
  "refreshToken": "JWT_REFRESH_TOKEN"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Token refreshed successfully",
  "data": {
    "accessToken": "NEW_ACCESS_TOKEN",
    "refreshToken": "NEW_REFRESH_TOKEN"
  }
}
```

**Error Responses:**
- 401 â€” Invalid or expired refresh token

---

### **4ï¸âƒ£ POST /logout**
**Description:** Logout user by revoking the provided refresh token.

**Access:** Private (requires Bearer token)

**Headers:**
```
Authorization: Bearer <ACCESS_TOKEN>
```

**Request Body:**
```json
{
  "refreshToken": "JWT_REFRESH_TOKEN"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Logged out successfully"
}
```

---

### **5ï¸âƒ£ POST /logout-all**
**Description:** Logout user from all devices (revokes all refresh tokens).

**Access:** Private

**Headers:**
```
Authorization: Bearer <ACCESS_TOKEN>
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Logged out from all devices successfully"
}
```

---

### **6ï¸âƒ£ GET /me**
**Description:** Fetch the authenticated user's profile.

**Access:** Private

**Headers:**
```
Authorization: Bearer <ACCESS_TOKEN>
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "id": "user_id",
    "email": "john@example.com",
    "fullName": "John Doe",
    "role": "planner",
    "phoneNumber": "+2348123456789",
    "emailVerified": false,
    "createdAt": "2025-11-05T10:00:00.000Z"
  }
}
```

---

### **7ï¸âƒ£ PUT /profile**
**Description:** Update user profile information.

**Access:** Private

**Headers:**
```
Authorization: Bearer <ACCESS_TOKEN>
```

**Request Body:**
```json
{
  "fullName": "John Updated",
  "phoneNumber": "+2349000000000"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Profile updated successfully",
  "data": {
    "id": "user_id",
    "email": "john@example.com",
    "fullName": "John Updated",
    "role": "planner",
    "phoneNumber": "+2349000000000"
  }
}
```

---

### **8ï¸âƒ£ PUT /change-password**
**Description:** Change the logged-in userâ€™s password.

**Access:** Private

**Headers:**
```
Authorization: Bearer <ACCESS_TOKEN>
```

**Request Body:**
```json
{
  "currentPassword": "oldPassword123",
  "newPassword": "newSecurePassword456"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Password changed successfully. Please login again."
}
```

**Error Responses:**
- 401 â€” Incorrect current password

---

## ðŸ§© Middleware Summary

| Middleware | Purpose | Access |
|-------------|----------|--------|
| `authenticate` | Verifies access token and attaches user info to `req.user` | Required for private routes |
| `optionalAuth` | Attaches user if token exists, else continues | Optional |
| `requireRole('admin')` | Restrict access to admin users | Protected routes |
| `requirePlanner`, `requireVendor`, etc. | Role-specific short forms | Protected routes |
| `requireOwnerOrAdmin('userId')` | Allows access if the authenticated user owns the resource or is admin | Protected routes |

---

## ðŸ§  Error Handling

| Error Type | HTTP Code | Example Message |
|-------------|------------|----------------|
| `ValidationError` | 400 | Invalid input |
| `AuthenticationError` | 401 | Invalid or expired token |
| `AuthorizationError` | 403 | Access denied |
| `NotFoundError` | 404 | Resource not found |
| `ConflictError` | 409 | Duplicate entry |
| `AppError` | 500 | Internal server error |

---

## ðŸ§± Roles

```js
const { ROLES } = require('../models/User');

ROLES = {
  ADMIN: 'admin',
  PLANNER: 'planner',
  VENDOR: 'vendor'
}
```
