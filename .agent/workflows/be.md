---
description: Backend Engineering - API & Database
auto_execution_mode: 3
---

# BE Skill - Backend Development Best Practices

Comprehensive workflow for Backend Engineers covering API design, database optimization, authentication, security, and performance.

## How to Use This Workflow

When implementing backend features, follow this systematic approach:

### Prerequisites

- ✅ Requirements from BA (user stories, acceptance criteria)
- ✅ Architecture design from Planning phase
- ✅ Database schema defined
- ✅ Development environment set up

---

## Step 1: RESTful API Design

### 1.1 API Naming Conventions

**Resource Naming:**
```
✅ Use nouns, not verbs
GET    /users          (not /getUsers)
POST   /products       (not /createProduct)
PUT    /orders/{id}    (not /updateOrder/{id})

✅ Use plural for collections
/products, /orders, /users

✅ Nested resources for relationships
/users/{id}/orders
/products/{id}/reviews

✅ Query params for filters/pagination
/products?category=electronics&page=2&limit=20
/users?role=admin&sort=created_at&order=desc
```

**HTTP Methods:**
| Method | Purpose | Idempotent | Safe |
|--------|---------|------------|------|
| GET | Retrieve resources | ✅ | ✅ |
| POST | Create resources | ❌ | ❌ |
| PUT | Replace resources | ✅ | ❌ |
| PATCH | Partial update | ❌ | ❌ |
| DELETE | Remove resources | ✅ | ❌ |

### 1.2 API Endpoint Examples

**Users API:**
```typescript
// Authentication
POST   /api/auth/register        // Register new user
POST   /api/auth/login           // Login
POST   /api/auth/logout          // Logout
POST   /api/auth/refresh         // Refresh token
POST   /api/auth/forgot-password // Request reset
POST   /api/auth/reset-password  // Reset with token

// Users
GET    /api/users                // List users (admin only)
GET    /api/users/{id}           // Get user by ID
PUT    /api/users/{id}           // Update user
DELETE /api/users/{id}           // Delete user
GET    /api/users/me             // Get current user
PUT    /api/users/me             // Update current user
PUT    /api/users/me/password    // Change password

// User's resources
GET    /api/users/{id}/orders    // Get user's orders
GET   /api/users/{id}/addresses // Get user's addresses
```

**Products API:**
```typescript
GET    /api/products             // List products
GET    /api/products/{id}        // Get product
POST   /api/products             // Create (admin)
PUT    /api/products/{id}        // Update (admin)
DELETE /api/products/{id}        // Delete (admin)
GET    /api/products/{id}/reviews // Get reviews

// Filtering & Pagination
GET /api/products?category=shoes&min_price=50&max_price=200&sort=price&order=asc&page=1&limit=20
```

### 1.3 Request/Response Format

**Request Example:**
```json
POST /api/products
Content-Type: application/json
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...

{
  "name": "Running Shoes",
  "description": "Professional running shoes",
  "price": 89.99,
  "category_id": "uuid-here",
  "stock_quantity": 50,
  "images": ["url1.jpg", "url2.jpg"]
}
```

**Success Response:**
```json
HTTP/1.1 201 Created
Content-Type: application/json

{
  "success": true,
  "data": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "name": "Running Shoes",
    "description": "Professional running shoes",
    "price": 89.99,
    "category_id": "uuid-here",
    "stock_quantity": 50,
    "images": ["url1.jpg", "url2.jpg"],
    "created_at": "2026-01-16T00:00:00Z",
    "updated_at": "2026-01-16T00:00:00Z"
  }
}
```

**Error Response:**
```json
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input",
    "details": [
      {
        "field": "price",
        "message": "Price must be a positive number"
      },
      {
        "field": "name",
        "message": "Name is required"
      }
    ]
  }
}
```

### 1.4 HTTP Status Codes

**Success (2xx):**
- `200 OK` - GET, PUT, PATCH success
- `201 Created` - POST success (return created resource)
- `204 No Content` - DELETE success

**Client Errors (4xx):**
- `400 Bad Request` - Invalid input/validation error
- `401 Unauthorized` - Not authenticated
- `403 Forbidden` - Authenticated but not authorized
- `404 Not Found` - Resource doesn't exist
- `409 Conflict` - Duplicate resource (e.g., email exists)
- `422 Unprocessable Entity` - Validation error
- `429 Too Many Requests` - Rate limit exceeded

**Server Errors (5xx):**
- `500 Internal Server Error` - Unexpected server error
- `503 Service Unavailable` - Temporary downtime

---

## Step 2: Authentication & Authorization

### 2.1 JWT Authentication Pattern

**Implementation (Node.js/Express):**
```typescript
import jwt from 'jsonwebtoken'
import bcrypt from 'bcrypt'

// Register
export async function register(req, res) {
  const { email, password, name } = req.body
  
  // Validate input
  if (!email || !password || !name) {
    return res.status(400).json({ error: 'All fields required' })
  }
  
  // Check if user exists
  const existingUser = await User.findOne({ email })
  if (existingUser) {
    return res.status(409).json({ error: 'Email already registered' })
  }
  
  // Hash password
  const passwordHash = await bcrypt.hash(password, 10)
  
  // Create user
  const user = await User.create({
    email,
    password_hash: passwordHash,
    name
  })
  
  // Generate JWT
  const accessToken = jwt.sign(
    { userId: user.id, email: user.email },
    process.env.JWT_SECRET!,
    { expiresIn: '15m' }
  )
  
  const refreshToken = jwt.sign(
    { userId: user.id },
    process.env.JWT_REFRESH_SECRET!,
    { expiresIn: '7d' }
  )
  
  res.status(201).json({
    user: { id: user.id, email: user.email, name: user.name },
    accessToken,
    refreshToken
  })
}

// Login
export async function login(req, res) {
  const { email, password } = req.body
  
  const user = await User.findOne({ email })
  if (!user) {
    return res.status(401).json({ error: 'Invalid credentials' })
  }
  
  const validPassword = await bcrypt.compare(password, user.password_hash)
  if (!validPassword) {
    return res.status(401).json({ error: 'Invalid credentials' })
  }
  
  const accessToken = jwt.sign(
    { userId: user.id, email: user.email, role: user.role },
    process.env.JWT_SECRET!,
    { expiresIn: '15m' }
  )
  
  const refreshToken = jwt.sign(
    { userId: user.id },
    process.env.JWT_REFRESH_SECRET!,
    { expiresIn: '7d' }
  )
  
  res.json({ user, accessToken, refreshToken })
}

// Auth Middleware
export function authenticateToken(req, res, next) {
  const authHeader = req.headers['authorization']
  const token = authHeader && authHeader.split(' ')[1]
  
  if (!token) {
    return res.status(401).json({ error: 'No token provided' })
  }
  
  jwt.verify(token, process.env.JWT_SECRET!, (err, user) => {
    if (err) {
      return res.status(403).json({ error: 'Invalid token' })
    }
    req.user = user
    next()
  })
}

// Role-based authorization
export function requireRole(...roles: string[]) {
  return (req, res, next) => {
    if (!req.user) {
      return res.status(401).json({ error: 'Not authenticated' })
    }
    
    if (!roles.includes(req.user.role)) {
      return res.status(403).json({ error: 'Insufficient permissions' })
    }
    
    next()
  }
}

// Usage
router.post('/products', 
  authenticateToken, 
  requireRole('admin'), 
  productController.create
)
```

### 2.2 Password Security

**Best Practices:**
```typescript
import bcrypt from 'bcrypt'

// Hash password (10 rounds)
const hash = await bcrypt.hash(password, 10)

// Verify password
const valid = await bcrypt.compare(password, hash)

// Password validation
function validatePassword(password: string): string[] {
  const errors = []
  
  if (password.length < 8) {
    errors.push('Password must be at least 8 characters')
  }
  if (!/[A-Z]/.test(password)) {
    errors.push('Password must contain uppercase letter')
  }
  if (!/[a-z]/.test(password)) {
    errors.push('Password must contain lowercase letter')
  }
  if (!/[0-9]/.test(password)) {
    errors.push('Password must contain number')
  }
  if (!/[!@#$%^&*]/.test(password)) {
    errors.push('Password must contain special character')
  }
  
  return errors
}
```

---

## Step 3: Database Optimization

### 3.1 Indexing Strategy

**Index Types:**
```sql
-- Primary key (automatic)
PRIMARY KEY (id)

-- Unique index
CREATE UNIQUE INDEX idx_users_email ON users(email);

-- Single-column index (for WHERE clauses)
CREATE INDEX idx_products_category_id ON products(category_id);
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_orders_status ON orders(status);

-- Composite index (for multi-column queries)
CREATE INDEX idx_products_category_price 
ON products(category_id, price);

-- Partial index (for filtered queries)
CREATE INDEX idx_orders_pending 
ON orders(user_id, created_at) 
WHERE status = 'pending';

-- Full-text search index (PostgreSQL)
CREATE INDEX idx_products_search 
ON products USING gin(to_tsvector('english', name || ' ' || description));
```

**When to Index:**
- ✅ Foreign keys
- ✅ Columns in WHERE clauses
- ✅ Columns in ORDER BY
- ✅ Columns in JOIN conditions
- ❌ Small tables (<1000 rows)
- ❌ Columns with low cardinality (few distinct values)
- ❌ Frequently updated columns

### 3.2 Query Optimization

**Avoid N+1 Queries:**
```typescript
// ❌ Bad: N+1 query problem
const users = await User.findAll()
for (const user of users) {
  const orders = await Order.findAll({ where: { userId: user.id } })
  user.orders = orders
}

// ✅ Good: Use JOIN or eager loading
const users = await User.findAll({
  include: [{ model: Order, as: 'orders' }]
})

// SQL equivalent
SELECT users.*, orders.*
FROM users
LEFT JOIN orders ON orders.user_id = users.id
```

**Use Pagination:**
```typescript
// ✅ Always paginate large result sets
GET /api/products?page=1&limit=20

// Implementation
const page = parseInt(req.query.page) || 1
const limit = parseInt(req.query.limit) || 20
const offset = (page - 1) * limit

const { rows: products, count } = await Product.findAndCountAll({
  limit,
  offset,
  order: [['created_at', 'DESC']]
})

res.json({
  data: products,
  pagination: {
    page,
    limit,
    total: count,
    totalPages: Math.ceil(count / limit)
  }
})
```

**Select Only Needed Columns:**
```typescript
// ❌ Bad: Select all columns
const users = await User.findAll()

// ✅ Good: Select specific columns
const users = await User.findAll({
  attributes: ['id', 'name', 'email']
})

// SQL
SELECT id, name, email FROM users
```

### 3.3 Caching Strategy

**Redis Caching:**
```typescript
import Redis from 'ioredis'

const redis = new Redis(process.env.REDIS_URL)

// Cache product details
async function getProduct(id: string) {
  const cacheKey = `product:${id}`
  
  // Check cache first
  const cached = await redis.get(cacheKey)
  if (cached) {
    return JSON.parse(cached)
  }
  
  // Fetch from database
  const product = await Product.findByPk(id)
  
  // Store in cache (TTL: 1 hour)
  await redis.setex(cacheKey, 3600, JSON.stringify(product))
  
  return product
}

// Invalidate cache on update
async function updateProduct(id: string, data: any) {
  const product = await Product.update(data, { where: { id } })
  
  // Invalidate cache
  await redis.del(`product:${id}`)
  
  return product
}

// Cache expensive queries
async function getPopularProducts() {
  const cacheKey = 'products:popular'
  
  const cached = await redis.get(cacheKey)
  if (cached) {
    return JSON.parse(cached)
  }
  
  const products = await Product.findAll({
    where: { popularity_score: { [Op.gte]: 80 } },
    limit: 10,
    order: [['popularity_score', 'DESC']]
  })
  
  // Cache for 15 minutes
  await redis.setex(cacheKey, 900, JSON.stringify(products))
  
  return products
}
```

---

## Step 4: Error Handling

### 4.1 Custom Error Classes

```typescript
export class AppError extends Error {
  constructor(
    public statusCode: number,
    public message: string,
    public code?: string
  ) {
    super(message)
    this.name = this.constructor.name
    Error.captureStackTrace(this, this.constructor)
  }
}

export class NotFoundError extends AppError {
  constructor(resource: string, id: string) {
    super(404, `${resource} with id ${id} not found`, 'NOT_FOUND')
  }
}

export class ValidationError extends AppError {
  constructor(public errors: Record<string, string>) {
    super(400, 'Validation failed', 'VALIDATION_ERROR')
  }
}

export class UnauthorizedError extends AppError {
  constructor(message = 'Unauthorized') {
    super(401, message, 'UNAUTHORIZED')
  }
}

export class ForbiddenError extends AppError {
  constructor(message = 'Forbidden') {
    super(403, message, 'FORBIDDEN')
  }
}
```

### 4.2 Global Error Handler

```typescript
// Error handling middleware (Express)
export function errorHandler(err: Error, req, res, next) {
  // Log error
  logger.error(err.stack)
  
  // Handle known errors
  if (err instanceof AppError) {
    return res.status(err.statusCode).json({
      success: false,
      error: {
        code: err.code,
        message: err.message,
        ...(err instanceof ValidationError && { details: err.errors })
      }
    })
  }
  
  // Handle JWT errors
  if (err.name === 'JsonWebTokenError') {
    return res.status(401).json({
      success: false,
      error: { code: 'INVALID_TOKEN', message: 'Invalid token' }
    })
  }
  
  if (err.name === 'TokenExpiredError') {
    return res.status(401).json({
      success: false,
      error: { code: 'TOKEN_EXPIRED', message: 'Token expired' }
    })
  }
  
  // Handle database errors
  if (err.name === 'SequelizeUniqueConstraintError') {
    return res.status(409).json({
      success: false,
      error: { code: 'DUPLICATE_ENTRY', message: 'Resource already exists' }
    })
  }
  
  // Default error
  res.status(500).json({
    success: false,
    error: {
      code: 'INTERNAL_ERROR',
      message: 'Internal server error'
    }
  })
}

// Usage
app.use(errorHandler)
```

---

## Step 5: Security Best Practices

### 5.1 OWASP Top 10 Protection

**SQL Injection Prevention:**
```typescript
// ✅ Use ORM/parameterized queries
const user = await User.findOne({
  where: { email: req.body.email }
})

// ❌ Never use raw SQL with string concatenation
// const user = await db.query(`SELECT * FROM users WHERE email = '${req.body.email}'`)
```

**XSS Prevention:**
```typescript
import helmet from 'helmet'
import xss from 'xss-clean'

app.use(helmet()) // Set security headers
app.use(xss()) // Sanitize user input

// Also sanitize in database
import { escape } from 'validator'
const sanitized = escape(userInput)
```

**Rate Limiting:**
```typescript
import rateLimit from 'express-rate-limit'

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // 100 requests per window
  message: 'Too many requests'
})

app.use('/api/', limiter)

// Stricter limit for auth endpoints
const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5,
  message: 'Too many login attempts'
})

app.use('/api/auth/login', authLimiter)
```

**CORS Configuration:**
```typescript
import cors from 'cors'

const corsOptions = {
  origin: process.env.ALLOWED_ORIGINS?.split(',') || [],
  credentials: true,
  optionsSuccessStatus: 200
}

app.use(cors(corsOptions))
```

### 5.2 Environment Variables

```bash
# .env
NODE_ENV=production
PORT=3000

# Database
DATABASE_URL=postgresql://user:pass@localhost:5432/dbname

# JWT
JWT_SECRET=your-super-secret-key-change-in-production
JWT_REFRESH_SECRET=different-secret-for-refresh
JWT_EXPIRES_IN=15m

# Redis
REDIS_URL=redis://localhost:6379

# Email
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your-email@gmail.com
SMTP_PASS=your-app-password

# External APIs
STRIPE_SECRET_KEY=sk_test_...
AWS_ACCESS_KEY_ID=AKIA...
AWS_SECRET_ACCESS_KEY=...
```

```typescript
// Load environment variables
import dotenv from 'dotenv'
dotenv.config()

// Validate required vars
const requiredEnvVars = [
  'DATABASE_URL',
  'JWT_SECRET',
  'JWT_REFRESH_SECRET'
]

for (const envVar of requiredEnvVars) {
  if (!process.env[envVar]) {
    throw new Error(`Missing required env var: ${envVar}`)
  }
}
```

---

## BE Development Checklist

### API Design
- [ ] RESTful conventions followed
- [ ] Proper HTTP methods used
- [ ] Correct status codes returned
- [ ] Request/response format standardized
- [ ] API versioning implemented (if needed)

### Authentication & Authorization
- [ ] JWT implemented securely
- [ ] Passwords hashed (bcrypt)
- [ ] Role-based access control
- [ ] Token refresh mechanism
- [ ] Session management

### Database
- [ ] Indexes created on foreign keys
- [ ] Queries optimized (no N+1)
- [ ] Pagination implemented
- [ ] Migrations written
- [ ] Seeds for dev data

### Performance
- [ ] Caching implemented (Redis)
- [ ] Database queries optimized
- [ ] Rate limiting configured
- [ ] Compression enabled
- [ ] Connection pooling

### Security
- [ ] Input validation
- [ ] SQL injection prevention
- [ ] XSS prevention
- [ ] CORS configured
- [ ] Helmet.js security headers
- [ ] Secrets in environment variables
- [ ] HTTPS in production

### Error Handling
- [ ] Global error handler
- [ ] Custom error classes
- [ ] Proper error logging
- [ ] User-friendly error messages
- [ ] Stack traces hidden in production

### Testing
- [ ] Unit tests for services
- [ ] Integration tests for APIs
- [ ] Test coverage > 80%
- [ ] All tests passing

---

## Next Steps

After BE implementation:
1. **Code review** with team
2. **API testing** with Postman/Insomnia
3. **Move to QAQC** phase using `/qaqc`
4. **Prepare for deployment** with `/devops`

## BE Tools

**Frameworks:** Express, Fastify, NestJS, FastAPI, Django, Spring Boot
**ORMs:** Sequelize, Prisma, TypeORM, SQLAlchemy, Hibernate
**Testing:** Jest, Mocha, Pytest, JUnit
**API Docs:** Swagger/OpenAPI, Postman
**Monitoring:** Sentry, LogRocket, Datadog
