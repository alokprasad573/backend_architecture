# Backend Architecture Proposal for SUVIDHA-LITE+ Admin Dashboard

Based on the project title "SUVIDHA-LITE+ Admin Dashboard Hierarchical Architecture", I propose the following backend architecture using the MERN stack (MongoDB, Express, React, Node.js).

## 1. Technology Stack
- **Runtime**: Node.js
- **Framework**: Express.js
- **Database**: MongoDB (with Mongoose for ORM)
- **Authentication**: JWT (JSON Web Tokens) with `bcryptjs` for hashing
- **Validation**: `joi` or `express-validator`
- **Logging**: `winston` or `morgan`

## 2. Hierarchical Role-Based Access Control (RBAC)
To support the "Hierarchical" aspect, we will implement a middleware-based permission system.

**Proposed Roles (Hierarchical):**
1.  **Super Admin**: Full access to system configuration, all users, and all data.
2.  **Admin**: Management of specific modules/regions, can manage Managers and Users.
3.  **Manager**: Limited management capabilities (e.g., only view reports or manage specific subsets of users).
4.  **User**: Basic access to view their own data and submit requests.

## 3. Directory Structure

```
backend/
├── config/
│   ├── db.js               # Database connection
│   └── default.json        # Config variables (or use .env)
├── controllers/
│   ├── authController.js   # Login, Register, Refresh Token
│   ├── userController.js   # User CRUD (Admin only)
│   └── dashboardController.js # Aggregated stats for dashboard
├── middleware/
│   ├── auth.js             # Verify JWT token
│   ├── roleCheck.js        # Middleware to check specific roles/permissions
│   └── errorHandler.js     # Global error handling
├── models/
│   ├── User.js             # User schema (includes role)
│   ├── ActivityLog.js      # Audit logs for admin actions
│   └── Request.js          # (Example) Main data entity
├── routes/
│   ├── authRoutes.js       # /api/auth
│   ├── userRoutes.js       # /api/users
│   └── dashboardRoutes.js  # /api/dashboard
├── utils/
│   ├── emailService.js     # Email helper
│   └── logger.js           # Logger configuration
├── .env                    # Environment variables (PORT, MONGO_URI, JWT_SECRET)
├── server.js               # Entry point
└── package.json            # Dependencies
```

## 4. Key Implementation Details

**User Model (`models/User.js`):**
```javascript
const UserSchema = new mongoose.Schema({
  username: { type: String, required: true, unique: true },
  email: { type: String, required: true, unique: true },
  password: { type: String, required: true },
  role: { 
    type: String, 
    enum: ['SuperAdmin', 'Admin', 'Manager', 'User'], 
    default: 'User' 
  },
  hierarchyLevel: { type: Number, default: 1 }, // Helper to compare ranks
  isActive: { type: Boolean, default: true },
  createdAt: { type: Date, default: Date.now }
});
```

**Role Middleware (`middleware/roleCheck.js`):**
```javascript
const checkRole = (requiredRoles) => (req, res, next) => {
  if (!requiredRoles.includes(req.user.role)) {
    return res.status(403).json({ msg: 'Access denied: Insufficient privileges' });
  }
  next();
};
```

## 5. Next Steps
1.  Initialize the Node.js project (`npm init`).
2.  Install dependencies (`npm install express mongoose dotenv cors helmet`).
3.  Set up the folder structure.
4.  Implement the basic server and database connection.
