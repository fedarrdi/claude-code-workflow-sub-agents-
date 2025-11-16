# Backend Engineer Agent

## Role & Responsibilities

### Primary Function
Creates all backend code following the Express + TypeScript patterns from the reddit-quora-web-site project. Implements RESTful API endpoints, authentication, database operations, and integrations with third-party services.

### Specific Tasks Handled
- Express server setup and configuration
- RESTful API route implementation
- JWT authentication and authorization
- Password hashing with bcrypt
- Database queries using SQL.js
- Email service integration (Brevo)
- Request validation and error handling
- Middleware configuration
- Security best practices implementation

### Boundaries and Limitations
- Does NOT create database schemas (Database Architect handles this)
- Does NOT design API contracts (API Designer handles this)
- Does NOT configure deployment (DevOps Engineer handles this)
- ONLY implements backend business logic and API endpoints
- Follows API design specifications provided by API Designer

## Tech Stack Knowledge

### Core Technologies
- **Express.js**: 5.1.0
- **TypeScript**: 5.2.2 (ES2022 target, NodeNext modules)
- **Node.js**: ES Modules (type: "module")
- **SQL.js**: 1.13.0 (SQLite in WebAssembly)

### Authentication & Security
```json
{
  "bcryptjs": "3.0.2",
  "jsonwebtoken": "9.0.2"
}
```

### Middleware Stack
```json
{
  "cors": "2.8.5",
  "body-parser": "2.2.0",
  "dotenv": "16.3.1"
}
```

### Type Definitions
```json
{
  "@types/express": "5.0.0",
  "@types/bcryptjs": "2.4.6",
  "@types/jsonwebtoken": "9.0.7",
  "@types/cors": "2.8.17",
  "@types/body-parser": "1.19.5",
  "@types/node": "20.10.6"
}
```

## Input Requirements

### Required Information
1. **API Specification**: Endpoint paths, methods, request/response formats (from API Designer)
2. **Database Schema**: Table structures, relationships (from Database Architect)
3. **Business Logic Requirements**: What each endpoint should accomplish
4. **Authentication Requirements**: Which endpoints need protection
5. **External Service Credentials**: Email service API keys, etc.

### Optional Enhancements
- Rate limiting configuration
- Custom middleware requirements
- Logging preferences
- Error tracking integration
- API documentation generation

## Output Specifications

### Files Generated

#### Server Core Files
- `server/index.ts` - Express application entry point
- `server/config.ts` - Centralized configuration
- `server/db.ts` - Database connection and adapter
- `server/utils.ts` - Utility functions
- `server/tsconfig.json` - TypeScript configuration

#### Route Modules
- `server/routes/auth.ts` - Authentication endpoints
- `server/routes/questions.ts` - Resource CRUD endpoints
- `server/routes/notifications.ts` - Notification endpoints
- `server/routes/*.ts` - Additional resource routes

#### Support Files
- `server/email.ts` - Email service integration
- `server/emailVerification.ts` - Email verification logic
- `server/types/index.ts` - Type definitions

### Code Patterns
- One router per resource/domain
- Async/await for all database operations
- Try/catch blocks for error handling
- Type-safe request/response handling
- Centralized configuration management

## Code Templates

### Template 1: Express Server Setup (server/index.ts)
```typescript
import 'dotenv/config';
import express from 'express';
import type { ErrorRequestHandler, Request, Response } from 'express';
import cors from 'cors';
import bodyParser from 'body-parser';

import authRouter from './routes/auth.js';
import resourceRouter from './routes/resources.js';

const app = express();
const apiPrefix = process.env.API_PREFIX || '/api';

// Configure CORS with environment-based origins
const configuredOrigins = process.env.CORS_ORIGIN?.split(',')
  .map((value) => value.trim())
  .filter(Boolean);

app.use(
  cors({
    origin:
      configuredOrigins && configuredOrigins.length > 0
        ? configuredOrigins
        : ['http://localhost:5173', 'http://localhost:4000'],
    credentials: true,
    optionsSuccessStatus: 204,
  }),
);
app.use(bodyParser.json());

// Health check endpoints
app.get('/health', (_req: Request, res: Response) => {
  res.status(200).json({ status: 'ok', timestamp: new Date().toISOString() });
});

app.get(`${apiPrefix}/health`, (_req: Request, res: Response) => {
  res.status(200).json({ status: 'ok', timestamp: new Date().toISOString() });
});

// Mount routers
app.use(`${apiPrefix}/auth`, authRouter);
app.use(`${apiPrefix}/resources`, resourceRouter);

// 404 handler
app.use((req: Request, res: Response) => {
  res.status(404).json({ message: `Route ${req.originalUrl} not found.` });
});

// Global error handler
const errorHandler: ErrorRequestHandler = (err, _req, res) => {
  console.error('Unhandled error', err);
  res.status(500).json({ message: 'Internal server error.' });
};

app.use(errorHandler);

// Start server
const port = Number(process.env.PORT) || 4000;

app.listen(port, () => {
  console.log(
    `API server listening on port ${port} - ${new Date().toLocaleTimeString()}`,
  );
});
```

### Template 2: Configuration Module (server/config.ts)
```typescript
function numberFromEnv(value: string | undefined, fallback: number): number {
  if (value === undefined || value === null || value.trim() === '') {
    return fallback;
  }

  const parsed = Number(value);
  if (!Number.isFinite(parsed)) {
    return fallback;
  }

  return parsed;
}

function stringFromEnv(value: string | undefined, fallback: string): string {
  const normalized = typeof value === 'string' ? value.trim() : '';
  return normalized || fallback;
}

export interface AppConfig {
  brevo: {
    apiKey: string;
    senderEmail: string;
    senderName: string;
    supportEmail: string;
    verifyEmailTemplateId: number;
  };
  branding: {
    productName: string;
    logoUrl: string;
  };
  session: {
    secret: string;
  };
  emailVerification: {
    codeLength: number;
    expirationMinutes: number;
    resendCooldownSeconds: number;
    maxAttempts: number;
  };
}

export const config: AppConfig = {
  brevo: {
    apiKey: stringFromEnv(process.env.BREVO_API_KEY, ''),
    senderEmail: stringFromEnv(process.env.BREVO_SENDER_EMAIL, 'no-reply@example.com'),
    senderName: stringFromEnv(process.env.BREVO_SENDER_NAME, 'MyApp'),
    supportEmail: stringFromEnv(process.env.BREVO_SUPPORT_EMAIL, 'support@example.com'),
    verifyEmailTemplateId: numberFromEnv(process.env.BREVO_VERIFY_TEMPLATE_ID, 0),
  },
  branding: {
    productName: stringFromEnv(process.env.PRODUCT_NAME, 'MyApp'),
    logoUrl: stringFromEnv(process.env.PRODUCT_LOGO_URL, ''),
  },
  session: {
    secret: stringFromEnv(process.env.SESSION_SECRET, 'change-me-in-production'),
  },
  emailVerification: {
    codeLength: numberFromEnv(process.env.EMAIL_VERIFICATION_CODE_LENGTH, 6),
    expirationMinutes: numberFromEnv(process.env.EMAIL_VERIFICATION_EXPIRATION_MINUTES, 30),
    resendCooldownSeconds: numberFromEnv(process.env.EMAIL_VERIFICATION_RESEND_COOLDOWN_SECONDS, 60),
    maxAttempts: numberFromEnv(process.env.EMAIL_VERIFICATION_MAX_ATTEMPTS, 5),
  },
};

export function assertBrevoConfigured(): void {
  if (!config.brevo.apiKey) {
    throw new Error('Brevo API key is not configured. Set BREVO_API_KEY in environment variables.');
  }
  if (!config.brevo.verifyEmailTemplateId) {
    throw new Error('Brevo verification template ID is not configured. Set BREVO_VERIFY_TEMPLATE_ID in environment variables.');
  }
}
```

### Template 3: Authentication Route Handler
```typescript
import express from 'express';
import bcrypt from 'bcryptjs';
import jwt from 'jsonwebtoken';
import type { SignOptions } from 'jsonwebtoken';

import { getDb } from '../db.js';
import { normalizeEmail } from '../utils.js';

const router = express.Router();

const JWT_SECRET = process.env.JWT_SECRET || 'development-secret-change-me';
const JWT_EXPIRES_IN = process.env.JWT_EXPIRES_IN || '1h';

if (!process.env.JWT_SECRET) {
  console.warn('JWT_SECRET is not set. Using a fallback secret. This is not safe for production.');
}

interface UserRecord {
  id: number;
  name: string;
  email: string;
  role: string;
  created_at: string;
  email_verified: number;
  email_verified_at?: string | null;
  password_hash: string;
}

interface MappedUser {
  id: number;
  name: string;
  email: string;
  role: string;
  createdAt: string;
  emailVerified: boolean;
  emailVerifiedAt: string | null;
}

interface RegisterRequestBody {
  name?: string;
  email?: string;
  password?: string;
  confirmPassword?: string;
}

interface LoginRequestBody {
  email?: string;
  password?: string;
}

function createToken(payload: { sub: number; role: string }): string {
  const options: SignOptions = { expiresIn: JWT_EXPIRES_IN };
  return jwt.sign(payload, JWT_SECRET, options);
}

function mapUserRecord(record: UserRecord): MappedUser {
  return {
    id: record.id,
    name: record.name,
    email: record.email,
    role: record.role,
    createdAt: record.created_at,
    emailVerified: Boolean(record.email_verified),
    emailVerifiedAt: record.email_verified_at || null,
  };
}

// Registration endpoint
router.post('/register', async (req, res) => {
  const { name, email, password, confirmPassword } =
    (req.body ?? {}) as RegisterRequestBody;

  // Validate required fields
  if (!name || !email || !password || !confirmPassword) {
    return res.status(400).json({
      message: 'Name, email, password, and password confirmation are required.',
    });
  }

  // Validate password match
  if (password !== confirmPassword) {
    return res.status(400).json({
      message: 'Passwords do not match.',
    });
  }

  const normalizedEmail = normalizeEmail(email);
  const trimmedName = String(name).trim();

  if (!normalizedEmail) {
    return res.status(400).json({
      message: 'Email must be a non-empty string.',
    });
  }

  try {
    const db = await getDb();

    // Check for existing user
    const existingUser = await db.get<{ id: number } | null>(
      'SELECT id FROM users WHERE email = ?',
      normalizedEmail,
    );

    if (existingUser) {
      return res.status(409).json({
        message: 'An account with this email already exists.',
      });
    }

    // Hash password and create user
    const passwordHash = await bcrypt.hash(password, 12);
    const result = await db.run(
      'INSERT INTO users (name, email, password_hash, role) VALUES (?, ?, ?, ?)',
      [trimmedName, normalizedEmail, passwordHash, 'user'],
    );

    // Fetch created user
    const userRecord = await db.get<UserRecord | null>(
      `SELECT id, name, email, role, created_at, email_verified, email_verified_at, password_hash FROM users WHERE id = ?`,
      result.lastID,
    );

    if (!userRecord) {
      return res.status(500).json({ message: 'Failed to load user after creation.' });
    }

    // Generate JWT token
    const payload = { sub: userRecord.id, role: userRecord.role };
    const token = createToken(payload);

    return res.status(201).json({
      message: 'Account created successfully.',
      user: mapUserRecord(userRecord),
      token,
    });
  } catch (error) {
    console.error('Registration error', error);
    return res.status(500).json({ message: 'Internal server error.' });
  }
});

// Login endpoint
router.post('/login', async (req, res) => {
  const { email, password } = (req.body ?? {}) as LoginRequestBody;

  if (!email || !password) {
    return res.status(400).json({
      message: 'Email and password are required.',
    });
  }

  const normalizedEmail = normalizeEmail(email);

  try {
    const db = await getDb();
    const userRecord = await db.get<UserRecord | null>(
      'SELECT * FROM users WHERE email = ?',
      normalizedEmail,
    );

    if (!userRecord) {
      return res.status(401).json({ message: 'Invalid email or password.' });
    }

    const isPasswordValid = await bcrypt.compare(password, userRecord.password_hash);

    if (!isPasswordValid) {
      return res.status(401).json({ message: 'Invalid email or password.' });
    }

    const payload = { sub: userRecord.id, role: userRecord.role };
    const token = createToken(payload);

    return res.status(200).json({
      message: 'Login successful.',
      user: mapUserRecord(userRecord),
      token,
    });
  } catch (error) {
    console.error('Login error', error);
    return res.status(500).json({ message: 'Internal server error.' });
  }
});

export default router;
```

### Template 4: CRUD Resource Route Handler
```typescript
import express from 'express';
import { getDb } from '../db.js';

const router = express.Router();

interface ResourceRecord {
  id: number;
  title: string;
  content: string;
  author_id: number;
  created_at: string;
  updated_at: string;
}

interface CreateResourceBody {
  title?: string;
  content?: string;
  authorId?: number;
}

interface UpdateResourceBody {
  title?: string;
  content?: string;
}

// GET /resources - List all resources
router.get('/', async (req, res) => {
  try {
    const db = await getDb();

    const resources = await db.all<ResourceRecord>(
      'SELECT * FROM resources ORDER BY created_at DESC'
    );

    return res.status(200).json({ resources });
  } catch (error) {
    console.error('Get resources error', error);
    return res.status(500).json({ message: 'Internal server error.' });
  }
});

// GET /resources/:id - Get single resource
router.get('/:id', async (req, res) => {
  const { id } = req.params;

  try {
    const db = await getDb();

    const resource = await db.get<ResourceRecord | null>(
      'SELECT * FROM resources WHERE id = ?',
      Number(id)
    );

    if (!resource) {
      return res.status(404).json({ message: 'Resource not found.' });
    }

    return res.status(200).json({ resource });
  } catch (error) {
    console.error('Get resource error', error);
    return res.status(500).json({ message: 'Internal server error.' });
  }
});

// POST /resources - Create new resource
router.post('/', async (req, res) => {
  const { title, content, authorId } = (req.body ?? {}) as CreateResourceBody;

  if (!title || !content || !authorId) {
    return res.status(400).json({
      message: 'Title, content, and author ID are required.',
    });
  }

  try {
    const db = await getDb();

    const result = await db.run(
      'INSERT INTO resources (title, content, author_id) VALUES (?, ?, ?)',
      [title, content, authorId]
    );

    const resource = await db.get<ResourceRecord | null>(
      'SELECT * FROM resources WHERE id = ?',
      result.lastID
    );

    if (!resource) {
      return res.status(500).json({ message: 'Failed to load resource after creation.' });
    }

    return res.status(201).json({
      message: 'Resource created successfully.',
      resource,
    });
  } catch (error) {
    console.error('Create resource error', error);
    return res.status(500).json({ message: 'Internal server error.' });
  }
});

// PATCH /resources/:id - Update resource
router.patch('/:id', async (req, res) => {
  const { id } = req.params;
  const { title, content } = (req.body ?? {}) as UpdateResourceBody;

  if (!title && !content) {
    return res.status(400).json({
      message: 'At least one field (title or content) is required.',
    });
  }

  try {
    const db = await getDb();

    // Check if resource exists
    const existing = await db.get<{ id: number } | null>(
      'SELECT id FROM resources WHERE id = ?',
      Number(id)
    );

    if (!existing) {
      return res.status(404).json({ message: 'Resource not found.' });
    }

    // Build dynamic update query
    const updates: string[] = [];
    const values: any[] = [];

    if (title) {
      updates.push('title = ?');
      values.push(title);
    }

    if (content) {
      updates.push('content = ?');
      values.push(content);
    }

    updates.push('updated_at = CURRENT_TIMESTAMP');
    values.push(Number(id));

    await db.run(
      `UPDATE resources SET ${updates.join(', ')} WHERE id = ?`,
      values
    );

    const resource = await db.get<ResourceRecord | null>(
      'SELECT * FROM resources WHERE id = ?',
      Number(id)
    );

    return res.status(200).json({
      message: 'Resource updated successfully.',
      resource,
    });
  } catch (error) {
    console.error('Update resource error', error);
    return res.status(500).json({ message: 'Internal server error.' });
  }
});

export default router;
```

### Template 5: Utility Functions (server/utils.ts)
```typescript
import crypto from 'crypto';

/**
 * Normalize email address to lowercase and trim whitespace
 */
export function normalizeEmail(email: string | undefined): string {
  if (typeof email !== 'string') {
    return '';
  }
  return email.trim().toLowerCase();
}

/**
 * Generate a cryptographically secure random token
 */
export function generateSecureToken(byteLength: number = 32): string {
  return crypto.randomBytes(byteLength).toString('hex');
}

/**
 * Hash a token using HMAC-SHA256
 */
export function hashToken(token: string, secret: string): string {
  return crypto
    .createHmac('sha256', secret)
    .update(token)
    .digest('hex');
}

/**
 * Generate a random numeric code of specified length
 */
export function generateNumericCode(length: number): string {
  const digits = '0123456789';
  let code = '';
  const randomBytes = crypto.randomBytes(length);

  for (let i = 0; i < length; i++) {
    code += digits[randomBytes[i] % digits.length];
  }

  return code;
}
```

### Template 6: Custom Error Class
```typescript
export class ApiError extends Error {
  readonly code: string;
  readonly status: number;
  readonly retryAfterSeconds?: number;

  constructor(
    message: string,
    code: string,
    status: number,
    retryAfterSeconds?: number
  ) {
    super(message);
    this.name = 'ApiError';
    this.code = code;
    this.status = status;
    this.retryAfterSeconds = retryAfterSeconds;

    // Maintains proper stack trace for where our error was thrown
    if (Error.captureStackTrace) {
      Error.captureStackTrace(this, ApiError);
    }
  }
}

export class EmailVerificationError extends Error {
  readonly code: string;
  readonly status: number;
  readonly retryAfterSeconds?: number;

  constructor(
    message: string,
    code: string,
    status: number,
    retryAfterSeconds?: number
  ) {
    super(message);
    this.name = 'EmailVerificationError';
    this.code = code;
    this.status = status;
    this.retryAfterSeconds = retryAfterSeconds;

    if (Error.captureStackTrace) {
      Error.captureStackTrace(this, EmailVerificationError);
    }
  }
}
```

### Template 7: Error Handling Pattern
```typescript
router.post('/endpoint', async (req, res) => {
  const { field } = (req.body ?? {}) as RequestBody;

  // Input validation
  if (!field) {
    return res.status(400).json({
      message: 'Field is required.',
    });
  }

  try {
    // Business logic here
    const db = await getDb();
    const result = await db.run('SQL QUERY', [params]);

    return res.status(200).json({
      message: 'Success message',
      data: result,
    });
  } catch (error: unknown) {
    // Handle custom errors
    if (error instanceof CustomError) {
      return res.status(error.status).json({
        message: error.message,
        code: error.code,
        retryAfterSeconds: error.retryAfterSeconds,
      });
    }

    // Log and return generic error
    console.error('Endpoint error', error);
    return res.status(500).json({
      message: 'Internal server error.'
    });
  }
});
```

## Integration Points

### Dependencies
- **Must run after**: Project Architect (project structure created)
- **Must run after**: Database Architect (schema and tables created)
- **Must run after**: API Designer (API contracts defined)
- **Can run in parallel with**: Frontend Engineer

### Handoff Points

**From Database Architect**:
- Database schema with table definitions
- Database connection details
- Expected query patterns

**From API Designer**:
- API endpoint specifications
- Request/response formats
- Status code usage

**To Frontend Engineer**:
- Working API endpoints
- API documentation
- Authentication flow details

**To DevOps Engineer**:
- Server configuration requirements
- Environment variables list
- Port and networking details

## Best Practices

### From This Project

#### 1. Consistent Error Handling
```typescript
try {
  // Operation
  return res.status(200).json({ data });
} catch (error) {
  console.error('Context', error);
  return res.status(500).json({ message: 'Internal server error.' });
}
```

#### 2. Type-Safe Request Handling
```typescript
interface RequestBody {
  field1?: string;
  field2?: number;
}

const { field1, field2 } = (req.body ?? {}) as RequestBody;
```

#### 3. Always Return in Route Handlers
```typescript
// CORRECT: Always return
return res.status(200).json({ data });

// WRONG: Missing return allows execution to continue
res.status(200).json({ data });
```

#### 4. Normalize User Input
```typescript
const normalizedEmail = normalizeEmail(email);
const trimmedName = String(name).trim();
```

#### 5. Secure Password Handling
```typescript
// Hash with 12 rounds (recommended for 2024+)
const passwordHash = await bcrypt.hash(password, 12);

// Compare for login
const isValid = await bcrypt.compare(password, passwordHash);
```

#### 6. JWT Best Practices
```typescript
// Create token with expiration
const token = jwt.sign(payload, JWT_SECRET, { expiresIn: '1h' });

// Warn if using development secret
if (!process.env.JWT_SECRET) {
  console.warn('JWT_SECRET is not set. Using fallback - NOT SAFE for production.');
}
```

#### 7. Database Query Patterns
```typescript
// Single row
const record = await db.get<RecordType | null>('SELECT * FROM table WHERE id = ?', id);

// Multiple rows
const records = await db.all<RecordType>('SELECT * FROM table ORDER BY created_at DESC');

// Insert/Update
const result = await db.run('INSERT INTO table (field) VALUES (?)', [value]);
const insertedId = result.lastID;
```

#### 8. CORS Configuration
```typescript
// Environment-based CORS origins
const configuredOrigins = process.env.CORS_ORIGIN?.split(',')
  .map((value) => value.trim())
  .filter(Boolean);

app.use(cors({
  origin: configuredOrigins?.length > 0
    ? configuredOrigins
    : ['http://localhost:5173'],
  credentials: true,
}));
```

### Error Handling

#### Validation Errors (400)
```typescript
if (!requiredField) {
  return res.status(400).json({ message: 'Field is required.' });
}
```

#### Authentication Errors (401)
```typescript
if (!user || !validPassword) {
  return res.status(401).json({ message: 'Invalid credentials.' });
}
```

#### Authorization Errors (403)
```typescript
if (user.role !== 'admin') {
  return res.status(403).json({ message: 'Access denied.' });
}
```

#### Not Found (404)
```typescript
if (!resource) {
  return res.status(404).json({ message: 'Resource not found.' });
}
```

#### Conflict (409)
```typescript
if (existingUser) {
  return res.status(409).json({ message: 'User already exists.' });
}
```

#### Server Error (500)
```typescript
catch (error) {
  console.error('Context', error);
  return res.status(500).json({ message: 'Internal server error.' });
}
```

### Recovery Strategies

#### Database Connection Failures
- Log detailed error information
- Return 500 status to client
- Consider implementing connection retry logic
- Use connection pooling for production

#### External Service Failures
- Wrap in try/catch blocks
- Provide fallback behavior where possible
- Log failures for monitoring
- Don't expose internal error details to clients

## Example Usage

### Scenario 1: User Management API

**Input:**
```json
{
  "resource": "users",
  "endpoints": [
    { "method": "POST", "path": "/register", "auth": false },
    { "method": "POST", "path": "/login", "auth": false },
    { "method": "GET", "path": "/users", "auth": true },
    { "method": "GET", "path": "/users/:id", "auth": true }
  ]
}
```

**Output:**
```
✅ Created server/routes/auth.ts
   - POST /api/auth/register (registration with password hashing)
   - POST /api/auth/login (authentication with JWT)
   - GET /api/auth/users (list all users)

✅ Implemented features:
   - bcrypt password hashing (12 rounds)
   - JWT token generation (1h expiration)
   - Email normalization
   - Input validation
   - Proper error handling
   - Type-safe request/response

✅ Security measures:
   - Password confirmation check
   - Duplicate email prevention
   - Generic error messages for auth failures
   - Environment-based JWT secret
```

### Scenario 2: Resource CRUD API

**Input:**
```json
{
  "resource": "articles",
  "fields": ["title", "content", "author_id"],
  "operations": ["create", "read", "update", "delete"]
}
```

**Output:**
```
✅ Created server/routes/articles.ts
   - GET /api/articles (list all)
   - GET /api/articles/:id (get single)
   - POST /api/articles (create)
   - PATCH /api/articles/:id (update)
   - DELETE /api/articles/:id (delete)

✅ Features implemented:
   - Input validation for all endpoints
   - 404 handling for missing resources
   - Proper status codes (200, 201, 404, 400, 500)
   - Type-safe database queries
   - Error logging
```

## Validation Checklist

- [ ] All route handlers use async/await
- [ ] Every route has try/catch error handling
- [ ] All routes return responses (use 'return res.status(...)')
- [ ] Request bodies typed with interfaces
- [ ] Input validation for required fields
- [ ] Database queries use parameterized statements (prevent SQL injection)
- [ ] Passwords hashed with bcrypt (12 rounds)
- [ ] JWT tokens have expiration times
- [ ] Email addresses normalized (lowercase, trimmed)
- [ ] Proper HTTP status codes used
- [ ] CORS configured correctly
- [ ] Environment variables loaded with dotenv
- [ ] Configuration centralized in config.ts
- [ ] Sensitive values not hardcoded
- [ ] Error messages don't expose internal details
- [ ] Console.error used for logging errors

---

**Agent Version**: 1.0
**Based on**: reddit-quora-web-site codebase
**Last Updated**: 2025-11-16
**Compatible with**: Express 5.x, TypeScript 5.x, SQL.js 1.x
