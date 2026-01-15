---
name: be
description: "Backend engineering API and database development. Actions: design API, implement endpoints, optimize database, create authentication, secure, cache, handle errors, validate input. Projects: REST API, GraphQL API, microservices, backend service, server development. Topics: RESTful API, HTTP methods, status codes, JWT, OAuth, authentication, authorization, RBAC, database indexing, query optimization, N+1 queries, caching, Redis, SQL injection, XSS, CORS, rate limiting, error handling, validation, bcrypt, password hashing."
---

# BE Skill - Backend Development

Backend engineering best practices for API design, database optimization, authentication, security, and performance.

## Prerequisites

- ✅ Requirements from BA
- ✅ Architecture design  
- ✅ Database schema
- ✅ Dev environment ready

## RESTful API Design

### Naming Conventions

```
✅ Use nouns, not verbs
GET    /users          (not /getUsers)
POST   /products       (not /createProduct)

✅ Plural for collections
/products, /orders, /users

✅ Nested resources
/users/{id}/orders
/products/{id}/reviews

✅ Query params for filtering
/products?category=shoes&page=2&limit=20
```

### HTTP Methods & Status Codes

| Method | Purpose | Status Codes |
|--------|---------|--------------|
| GET | Retrieve | 200, 404 |
| POST | Create | 201, 400, 409 |
| PUT | Replace | 200, 404 |
| PATCH | Update | 200, 404 |
| DELETE | Remove | 204, 404 |

**Common Status Codes:**
- 200 OK, 201 Created, 204 No Content
- 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found
- 500 Internal Server Error

### API Response Format

```json
// Success
{
  "success": true,
  "data": { ... }
}

// Error
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input",
    "details": [ ... ]
  }
}
```

---

## Authentication & Authorization

### JWT Pattern

```typescript
import jwt from 'jsonwebtoken'
import bcrypt from 'bcrypt'

// Register
const passwordHash = await bcrypt.hash(password, 10)
const user = await User.create({ email, password_hash: passwordHash })

const accessToken = jwt.sign(
  { userId: user.id, email: user.email },
  process.env.JWT_SECRET!,
  { expiresIn: '15m' }
)

// Middleware
function authenticateToken(req, res, next) {
  const token = req.headers['authorization']?.split(' ')[1]
  if (!token) return res.status(401).json({ error: 'No token' })
  
  jwt.verify(token, process.env.JWT_SECRET!, (err, user) => {
    if (err) return res.status(403).json({ error: 'Invalid token' })
    req.user = user
    next()
  })
}

// Role-based authorization
function requireRole(...roles: string[]) {
  return (req, res, next) => {
    if (!roles.includes(req.user.role)) {
      return res.status(403).json({ error: 'Forbidden' })
    }
    next()
  }
}
```

---

## Database Optimization

### Indexing

```sql
-- Primary key (automatic)
PRIMARY KEY (id)

-- Foreign keys
CREATE INDEX idx_orders_user_id ON orders(user_id);

-- Unique constraints
CREATE UNIQUE INDEX idx_users_email ON users(email);

-- Composite indexes
CREATE INDEX idx_products_category_price 
ON products(category_id, price);

-- Partial indexes
CREATE INDEX idx_orders_pending 
ON orders(user_id) WHERE status = 'pending';
```

**When to Index:**
- ✅ Foreign keys
- ✅ WHERE clauses
- ✅ ORDER BY columns
- ✅ JOIN conditions
- ❌ Small tables (<1000 rows)
- ❌ Frequently updated columns

### Query Optimization

```typescript
// ❌ N+1 Problem
const users = await User.findAll()
for (const user of users) {
  user.orders = await Order.findAll({ where: { userId: user.id } })
}

// ✅ Use JOIN / Eager Loading
const users = await User.findAll({
  include: [{ model: Order }]
})

// ✅ Pagination
const { rows, count } = await Product.findAndCountAll({
  limit: 20,
  offset: (page - 1) * 20
})

// ✅ Select specific columns
const users = await User.findAll({
  attributes: ['id', 'name', 'email']
})
```

### Caching with Redis

```typescript
import Redis from 'ioredis'
const redis = new Redis()

async function getProduct(id: string) {
  // Check cache
  const cached = await redis.get(`product:${id}`)
  if (cached) return JSON.parse(cached)
  
  // Fetch from DB
  const product = await Product.findByPk(id)
  
  // Cache for 1 hour
  await redis.setex(`product:${id}`, 3600, JSON.stringify(product))
  
  return product
}

// Invalidate on update
await redis.del(`product:${id}`)
```

---

## Error Handling

### Custom Errors

```typescript
class AppError extends Error {
  constructor(
    public statusCode: number,
    public message: string,
    public code?: string
  ) {
    super(message)
  }
}

class NotFoundError extends AppError {
  constructor(resource: string, id: string) {
    super(404, `${resource} ${id} not found`, 'NOT_FOUND')
  }
}

class ValidationError extends AppError {
  constructor(public errors: Record<string, string>) {
    super(400, 'Validation failed', 'VALIDATION_ERROR')
  }
}
```

### Global Error Handler

```typescript
app.use((err: Error, req, res, next) => {
  logger.error(err.stack)
  
  if (err instanceof AppError) {
    return res.status(err.statusCode).json({
      success: false,
      error: { code: err.code, message: err.message }
    })
  }
  
  res.status(500).json({
    success: false,
    error: { code: 'INTERNAL_ERROR', message: 'Server error' }
  })
})
```

---

## Security Best Practices

### OWASP Protection

```typescript
import helmet from 'helmet'
import rateLimit from 'express-rate-limit'
import cors from 'cors'

// Security headers
app.use(helmet())

// CORS
app.use(cors({
  origin: process.env.ALLOWED_ORIGINS?.split(','),
  credentials: true
}))

// Rate limiting
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100
})
app.use('/api/', limiter)

// Input validation
import { body, validationResult } from 'express-validator'

router.post('/users',
  body('email').isEmail(),
  body('password').isLength({ min: 8 }),
  (req, res) => {
    const errors = validationResult(req)
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() })
    }
    // ...
  }
)
```

### Prevention Checklist

- [ ] ✅ Use parameterized queries (ORM)
- [ ] ✅ Hash passwords (bcrypt)
- [ ] ✅ Validate all input
- [ ] ✅ Sanitize output (XSS)
- [ ] ✅ Use HTTPS
- [ ] ✅ Set security headers (helmet)
- [ ] ✅ Rate limiting
- [ ] ✅ CORS configuration
- [ ] ✅ Secrets in env variables

---

## Development Checklist

### API
- [ ] RESTful conventions
- [ ] Proper HTTP methods
- [ ] Correct status codes
- [ ] Standardized responses
- [ ] API documentation

### Auth
- [ ] JWT implemented
- [ ] Password hashing
- [ ] RBAC (role-based access)
- [ ] Token refresh
- [ ] Session management

### Database
- [ ] Indexes on FKs
- [ ] No N+1 queries
- [ ] Pagination
- [ ] Migrations
- [ ] Seeds

### Performance
- [ ] Redis caching
- [ ] Optimized queries
- [ ] Rate limiting
- [ ] Compression
- [ ] Connection pooling

### Security
- [ ] Input validation
- [ ] SQL injection prevention
- [ ] XSS prevention
- [ ] CORS configured
- [ ] Secrets secured

---

## Tools

**Frameworks:** Express, Fastify, NestJS, FastAPI, Django
**ORMs:** Sequelize, Prisma, TypeORM, SQLAlchemy
**Testing:** Jest, Mocha, Pytest
**API Docs:** Swagger, Postman
**Monitoring:** Sentry, Datadog

## Next Steps

1. Code review
2. API testing
3. Move to QAQC (`/qaqc`)
4. Prepare deployment (`/devops`)
