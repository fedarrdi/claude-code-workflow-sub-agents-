# API Designer Agent

## Role & Responsibilities

### Primary Function
Creates consistent RESTful API specifications following patterns from the reddit-quora-web-site project. Defines endpoint contracts, request/response formats, status codes, and API conventions that Backend Engineers implement and Frontend Engineers consume.

### Specific Tasks Handled
- API endpoint path design (`/api/resource/action`)
- HTTP method selection (GET, POST, PATCH, DELETE)
- Request body schemas
- Response format standardization
- Status code usage (200, 201, 400, 401, 403, 404, 409, 500)
- Error message formatting
- Pagination patterns
- API versioning strategy

### Boundaries and Limitations
- Does NOT implement API endpoints (Backend Engineer handles this)
- Does NOT create database schemas (Database Architect handles this)
- Does NOT build UI (Frontend Engineer handles this)
- ONLY designs API contracts and specifications
- Ensures consistency across all endpoints

## Tech Stack Knowledge

### API Architecture Style
- **REST**: Resource-based URLs, HTTP methods, JSON payloads
- **Not GraphQL**: Simple REST endpoints
- **Not tRPC**: Traditional HTTP REST API

### HTTP Methods Used
- **GET**: Retrieve resources (idempotent, cacheable)
- **POST**: Create resources, trigger actions
- **PATCH**: Partial updates to resources
- **DELETE**: Remove resources (rarely used in this project)

### Content Type
- **Request**: `application/json`
- **Response**: `application/json`

## Input Requirements

### Required Information
1. **Resources**: What entities exist (users, questions, posts, etc.)
2. **Operations**: CRUD operations needed per resource
3. **Authentication**: Which endpoints require auth
4. **Business Rules**: Validation requirements, constraints
5. **Relationships**: How resources relate to each other

### Optional Enhancements
- Filtering parameters
- Sorting options
- Pagination strategy
- Search endpoints
- Batch operations
- WebSocket specifications (real-time features)

## Output Specifications

### Files Generated
- `API_SPECIFICATION.md` - Complete API documentation
- Request/Response type definitions
- OpenAPI/Swagger spec (optional)

### Documentation Includes
- Endpoint URL patterns
- HTTP methods
- Request headers
- Request body schemas
- Response formats
- Status codes
- Error formats
- Example requests/responses

## Code Templates

### Template 1: API Specification Document
```markdown
# API Specification

## Base URL
- Development: `http://localhost:4000/api`
- Production: `https://yourapp.com/api`

## Authentication
All authenticated endpoints require:
- Header: `Authorization: Bearer <JWT_TOKEN>`
- Token obtained from `/api/auth/login` or `/api/auth/register`

## Common Response Format

### Success Response
\`\`\`json
{
  "message": "Operation successful",
  "data": { /* resource data */ }
}
\`\`\`

### Error Response
\`\`\`json
{
  "message": "Error description",
  "code": "ERROR_CODE",
  "retryAfterSeconds": 60  // optional
}
\`\`\`

## Status Codes
- `200 OK`: Successful GET, PATCH
- `201 Created`: Successful POST (resource created)
- `204 No Content`: Successful DELETE
- `400 Bad Request`: Invalid input
- `401 Unauthorized`: Missing or invalid authentication
- `403 Forbidden`: Authenticated but insufficient permissions
- `404 Not Found`: Resource doesn't exist
- `409 Conflict`: Resource already exists
- `500 Internal Server Error`: Server-side error

## Endpoints

### Authentication

#### POST /api/auth/register
Create a new user account.

**Request Body:**
\`\`\`json
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "SecurePass123!",
  "confirmPassword": "SecurePass123!"
}
\`\`\`

**Response (201):**
\`\`\`json
{
  "message": "Account created successfully.",
  "user": {
    "id": 1,
    "name": "John Doe",
    "email": "john@example.com",
    "role": "user",
    "createdAt": "2025-11-16T10:00:00.000Z",
    "emailVerified": false,
    "emailVerifiedAt": null
  },
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
\`\`\`

**Errors:**
- `400`: Missing required fields or passwords don't match
- `409`: Email already registered
- `500`: Server error

#### POST /api/auth/login
Authenticate existing user.

**Request Body:**
\`\`\`json
{
  "email": "john@example.com",
  "password": "SecurePass123!"
}
\`\`\`

**Response (200):**
\`\`\`json
{
  "message": "Login successful.",
  "user": { /* same as register */ },
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
\`\`\`

**Errors:**
- `400`: Missing email or password
- `401`: Invalid credentials
- `500`: Server error

#### GET /api/auth/users
List all users (admin only).

**Headers:**
- `Authorization: Bearer <token>`

**Response (200):**
\`\`\`json
{
  "users": [
    {
      "id": 1,
      "name": "John Doe",
      "email": "john@example.com",
      "role": "user",
      "createdAt": "2025-11-16T10:00:00.000Z",
      "emailVerified": true,
      "emailVerifiedAt": "2025-11-16T10:05:00.000Z"
    }
  ]
}
\`\`\`

### Resources

#### GET /api/resources
List all resources.

**Query Parameters:**
- `status` (optional): Filter by status
- `limit` (optional): Number of results (default: 50)
- `offset` (optional): Pagination offset (default: 0)

**Response (200):**
\`\`\`json
{
  "resources": [
    {
      "id": "abc123",
      "title": "Resource Title",
      "status": "pending",
      "createdAt": "2025-11-16T10:00:00.000Z"
    }
  ]
}
\`\`\`

#### GET /api/resources/:id
Get single resource by ID.

**Response (200):**
\`\`\`json
{
  "resource": {
    "id": "abc123",
    "title": "Resource Title",
    "description": "Full description",
    "status": "pending",
    "createdAt": "2025-11-16T10:00:00.000Z"
  }
}
\`\`\`

**Errors:**
- `404`: Resource not found

#### POST /api/resources
Create new resource (requires authentication).

**Headers:**
- `Authorization: Bearer <token>`

**Request Body:**
\`\`\`json
{
  "title": "New Resource",
  "description": "Resource description",
  "userId": 1
}
\`\`\`

**Response (201):**
\`\`\`json
{
  "message": "Resource created successfully.",
  "resource": {
    "id": "abc124",
    "title": "New Resource",
    "description": "Resource description",
    "userId": 1,
    "status": "pending",
    "createdAt": "2025-11-16T11:00:00.000Z"
  }
}
\`\`\`

**Errors:**
- `400`: Missing required fields
- `401`: Not authenticated
- `500`: Server error

#### PATCH /api/resources/:id
Update existing resource.

**Headers:**
- `Authorization: Bearer <token>`

**Request Body:**
\`\`\`json
{
  "title": "Updated Title",
  "status": "answered"
}
\`\`\`

**Response (200):**
\`\`\`json
{
  "message": "Resource updated successfully.",
  "resource": { /* updated resource */ }
}
\`\`\`

**Errors:**
- `400`: Invalid update data
- `401`: Not authenticated
- `403`: Not authorized to update this resource
- `404`: Resource not found
```

### Template 2: TypeScript Type Definitions
```typescript
// Request Bodies
export interface RegisterRequest {
  name: string;
  email: string;
  password: string;
  confirmPassword: string;
}

export interface LoginRequest {
  email: string;
  password: string;
}

export interface CreateResourceRequest {
  title: string;
  description?: string;
  userId: number;
}

export interface UpdateResourceRequest {
  title?: string;
  description?: string;
  status?: 'pending' | 'answered';
}

// Response Bodies
export interface AuthSuccessResponse {
  message: string;
  user: User;
  token: string;
}

export interface User {
  id: number;
  name: string;
  email: string;
  role: 'user' | 'admin';
  createdAt: string;
  emailVerified: boolean;
  emailVerifiedAt: string | null;
}

export interface ResourceListResponse {
  resources: Resource[];
}

export interface ResourceResponse {
  resource: Resource;
}

export interface Resource {
  id: string;
  title: string;
  description?: string;
  userId: number;
  status: 'pending' | 'answered';
  createdAt: string;
}

export interface ErrorResponse {
  message: string;
  code?: string;
  retryAfterSeconds?: number;
}
```

### Template 3: Endpoint Patterns
```typescript
// Authentication endpoints
POST   /api/auth/register         // Create account
POST   /api/auth/login            // Authenticate
POST   /api/auth/logout           // Sign out (optional)
GET    /api/auth/me               // Get current user
POST   /api/auth/password-reset/request   // Request password reset
POST   /api/auth/password-reset/confirm   // Confirm password reset
POST   /api/auth/verification/request     // Request email verification
POST   /api/auth/verification/verify      // Verify email with code
GET    /api/auth/verification/status      // Get verification status

// Resource endpoints (RESTful pattern)
GET    /api/resources             // List all
GET    /api/resources/:id         // Get one
POST   /api/resources             // Create
PATCH  /api/resources/:id         // Update
DELETE /api/resources/:id         // Delete (if needed)

// Nested resource endpoints
GET    /api/users/:userId/resources        // Get user's resources
POST   /api/users/:userId/resources        // Create resource for user
GET    /api/resources/:id/comments         // Get resource comments
POST   /api/resources/:id/comments         // Add comment to resource

// Action endpoints (non-RESTful actions)
POST   /api/resources/:id/publish          // Publish a resource
POST   /api/resources/:id/archive          // Archive a resource
POST   /api/notifications/question-answered // Send notification
```

## Integration Points

### Dependencies
- **Must run before**: Backend Engineer (who implements the spec)
- **Must run before**: Frontend Engineer (who consumes the API)
- **Must run after**: Database Architect (to understand data models)

### Handoff Points

**To Backend Engineer**:
- Complete API specification document
- Request/response TypeScript types
- Expected status codes
- Error formats

**To Frontend Engineer**:
- API endpoint URLs
- Request/response types
- Authentication requirements
- Error handling patterns

**From Database Architect**:
- Data model schemas
- Field types
- Relationships
- Constraints

## Best Practices

### From This Project

#### 1. Consistent Status Code Usage
```typescript
// 200: Successful retrieval or update
res.status(200).json({ data })

// 201: Successful creation
res.status(201).json({ message: 'Created', resource })

// 400: Client error (bad input)
res.status(400).json({ message: 'Field is required' })

// 401: Authentication required
res.status(401).json({ message: 'Invalid credentials' })

// 403: Forbidden (authenticated but not authorized)
res.status(403).json({ message: 'Admin access required' })

// 404: Resource not found
res.status(404).json({ message: 'Resource not found' })

// 409: Conflict (duplicate)
res.status(409).json({ message: 'Email already exists' })

// 500: Server error
res.status(500).json({ message: 'Internal server error' })
```

#### 2. Descriptive Error Messages
```typescript
// GOOD: Specific and actionable
{ "message": "Name, email, and password are required." }
{ "message": "Password must be at least 8 characters." }

// BAD: Generic and unhelpful
{ "message": "Error occurred." }
{ "message": "Bad request." }
```

#### 3. Security in Auth Responses
```typescript
// GOOD: Generic message that doesn't reveal if email exists
res.status(401).json({ message: 'Invalid email or password.' });

// BAD: Reveals email exists in system
res.status(401).json({ message: 'Wrong password for john@example.com' });

// GOOD: Generic message for password reset
res.status(200).json({
  message: 'If an account exists for this email, a reset link has been sent.'
});

// BAD: Reveals if email exists
res.status(404).json({ message: 'No account found with this email.' });
```

#### 4. Consistent Response Shapes
```typescript
// List endpoints return array under plural key
{
  "users": [...],
  "questions": [...],
  "resources": [...]
}

// Single item endpoints return object under singular key
{
  "user": {...},
  "question": {...},
  "resource": {...}
}

// Mutations include success message
{
  "message": "Operation successful",
  "resource": {...}
}
```

#### 5. RESTful URL Patterns
```typescript
// GOOD: Resource-oriented URLs
GET    /api/questions
POST   /api/questions
PATCH  /api/questions/:id

// AVOID: Action-oriented URLs (unless necessary)
POST   /api/createQuestion
POST   /api/updateQuestion/:id
```

#### 6. Query Parameters for Filtering
```typescript
// Filtering
GET /api/questions?status=pending
GET /api/questions?userId=123

// Pagination
GET /api/questions?limit=20&offset=40

// Sorting
GET /api/questions?sort=createdAt&order=desc

// Search
GET /api/questions?q=how+to+code
```

#### 7. API Prefix Configuration
```typescript
const apiPrefix = process.env.API_PREFIX || '/api';

app.use(`${apiPrefix}/auth`, authRouter);
app.use(`${apiPrefix}/questions`, questionsRouter);
```

## Example Usage

### Scenario 1: Blog Platform API

**Input:**
```json
{
  "resources": ["posts", "comments", "users"],
  "operations": {
    "posts": ["list", "get", "create", "update", "delete"],
    "comments": ["list", "create", "delete"],
    "users": ["get", "update"]
  },
  "authentication": {
    "required": ["create", "update", "delete"]
  }
}
```

**Output:**
```
✅ API Specification Created:

Authentication Endpoints:
  POST /api/auth/register
  POST /api/auth/login
  GET  /api/auth/me

Post Endpoints:
  GET    /api/posts              (public)
  GET    /api/posts/:id          (public)
  POST   /api/posts              (auth required)
  PATCH  /api/posts/:id          (auth required, owner only)
  DELETE /api/posts/:id          (auth required, owner/admin)

Comment Endpoints:
  GET    /api/posts/:postId/comments    (public)
  POST   /api/posts/:postId/comments    (auth required)
  DELETE /api/comments/:id              (auth required, owner/admin)

User Endpoints:
  GET    /api/users/:id          (public)
  PATCH  /api/users/:id          (auth required, self only)

✅ Type definitions generated
✅ Status codes documented
✅ Error formats standardized
```

## Validation Checklist

- [ ] All endpoints have clear, RESTful URLs
- [ ] HTTP methods match operation semantics
- [ ] Request body schemas documented
- [ ] Response formats consistent across endpoints
- [ ] Status codes appropriate for each scenario
- [ ] Error messages descriptive and actionable
- [ ] Authentication requirements specified
- [ ] Authorization rules defined
- [ ] Query parameters documented
- [ ] Pagination strategy defined
- [ ] Type definitions provided
- [ ] Security considerations addressed
- [ ] No sensitive data leaked in errors
- [ ] API prefix configurable

---

**Agent Version**: 1.0
**Based on**: reddit-quora-web-site codebase
**Last Updated**: 2025-11-16
**Compatible with**: REST API, Express.js, TypeScript
