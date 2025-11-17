# Tech Stack Configuration

**Version**: 1.0
**Based on**: reddit-quora-web-site project
**Last Updated**: 2025-11-16
**Purpose**: Centralized tech stack definition for all agents

---

## Overview

This configuration defines the exact technologies, versions, and patterns used by all agents to build web applications. All agents MUST follow this stack to ensure consistency and compatibility.

## Project Type

**Full-Stack Web Application**
- Modern SPA (Single Page Application) frontend
- RESTful API backend
- File-based SQLite database
- JWT authentication
- Email integration (optional)

---

## Frontend Stack

### Core Framework
```json
{
  "react": "18.2.0",
  "react-dom": "18.2.0"
}
```

**Why React 18?**
- Concurrent features
- Automatic batching
- Hooks-based architecture
- Large ecosystem

### Language
```json
{
  "typescript": "5.2.2"
}
```

**TypeScript Configuration**:
- Target: ES2020
- Module: ESNext
- JSX: react-jsx
- Strict mode enabled
- No unused locals/parameters

### Build Tool
```json
{
  "vite": "5.0.8",
  "@vitejs/plugin-react": "4.2.1"
}
```

**Vite Features**:
- Lightning fast HMR (Hot Module Replacement)
- Optimized builds
- Dev proxy to backend
- TypeScript support out of box

**Vite Configuration**:
```typescript
{
  server: {
    port: 5173,
    proxy: {
      '/api': {
        target: 'http://localhost:4000',
        changeOrigin: true
      }
    }
  }
}
```

### Styling
```json
{
  "tailwindcss": "3.3.6",
  "postcss": "8.4.32",
  "autoprefixer": "10.4.16"
}
```

**Tailwind Configuration**:
- Dark mode: class-based
- Custom colors: primary, secondary, quora-red, quora-blue, quora-bg, quora-card, quora-border, quora-text
- Custom fonts: Inter (body), Poppins (headings)
- Custom animations: gradient, float

### Routing
```json
{
  "react-router-dom": "6.20.0"
}
```

**Routing Pattern**:
- BrowserRouter
- Route-based code splitting
- Protected routes with Navigate
- Nested routes support

### Icons
```json
{
  "lucide-react": "0.424.0"
}
```

**Why Lucide?**
- Tree-shakeable
- Consistent design
- TypeScript support
- Lightweight

### Frontend Dev Dependencies
```json
{
  "@types/react": "18.2.43",
  "@types/react-dom": "18.2.17",
  "@typescript-eslint/eslint-plugin": "6.14.0",
  "@typescript-eslint/parser": "6.14.0",
  "eslint": "8.55.0",
  "eslint-plugin-react-hooks": "4.6.0",
  "eslint-plugin-react-refresh": "0.4.5"
}
```

---

## Backend Stack

### Core Framework
```json
{
  "express": "5.1.0"
}
```

**Why Express 5?**
- Stable and mature
- Large middleware ecosystem
- TypeScript support
- RESTful API patterns

### Language
```json
{
  "typescript": "5.2.2"
}
```

**TypeScript Configuration** (Backend):
- Target: ES2022
- Module: NodeNext
- Module Resolution: NodeNext
- Strict mode enabled
- Output: dist/server

### Runtime Environment
```
Node.js with ES Modules (type: "module" in package.json)
```

### Middleware
```json
{
  "cors": "2.8.5",
  "body-parser": "2.2.0",
  "dotenv": "16.3.1"
}
```

**Middleware Configuration**:
- CORS: Configurable origins via env var
- Body Parser: JSON parsing
- Dotenv: Environment variable loading

### Authentication
```json
{
  "jsonwebtoken": "9.0.2",
  "bcryptjs": "3.0.2"
}
```

**Authentication Pattern**:
- JWT tokens with 1h expiration
- bcrypt password hashing (12 rounds)
- Bearer token authentication
- Token stored in localStorage (frontend)

### Database Driver
```json
{
  "sql.js": "1.13.0"
}
```

**Database Pattern**:
- SQLite compiled to WebAssembly
- In-memory database with file persistence
- Manual save after writes
- File location: `server/data/*.sqlite`

### Email Service (Optional)
```
Brevo API (formerly Sendinblue)
```

**Email Features**:
- Email verification codes
- Password reset emails
- Question answered notifications
- Template-based emails

### Backend Dev Dependencies
```json
{
  "@types/express": "5.0.0",
  "@types/bcryptjs": "2.4.6",
  "@types/jsonwebtoken": "9.0.7",
  "@types/cors": "2.8.17",
  "@types/body-parser": "1.19.5",
  "@types/node": "20.10.6",
  "tsx": "4.19.0",
  "concurrently": "9.2.1"
}
```

---

## Database Stack

### Database Engine
```
SQLite 3 (via SQL.js 1.13.0)
```

### Why SQL.js?
- No database server required
- File-based storage
- Full SQL support
- Cross-platform
- Easy deployment

### Database Patterns

**Schema Definition**:
```sql
CREATE TABLE IF NOT EXISTS tablename (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  field TEXT NOT NULL,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

**Data Types**:
- INTEGER (for numbers, IDs, booleans as 0/1)
- TEXT (for strings)
- DATETIME (for timestamps, stored as ISO 8601 strings)

**Constraints**:
- PRIMARY KEY AUTOINCREMENT
- NOT NULL
- UNIQUE
- CHECK (for enum-like values)
- FOREIGN KEY

**Timestamps**:
- Use DATETIME DEFAULT CURRENT_TIMESTAMP
- Store as ISO 8601: `new Date().toISOString()`

**Migration Strategy**:
- Column existence checks before ALTER TABLE
- Safe incremental migrations
- Persist after schema changes

---

## Development Tools

### Package Manager
```
npm (comes with Node.js)
```

### TypeScript Compilation
```json
{
  "frontend": "tsc (via Vite)",
  "backend": "tsc -p server/tsconfig.json"
}
```

### Development Server
```json
{
  "tsx": "4.19.0",
  "concurrently": "9.2.1"
}
```

**Dev Script**:
```bash
concurrently --kill-others-on-fail \
  "npm run dev:types" \
  "tsx watch server/index.ts" \
  "vite"
```

**What runs concurrently?**:
1. TypeScript type checking in watch mode
2. Backend server with hot reload (tsx watch)
3. Frontend dev server (Vite)

### Linting
```json
{
  "eslint": "8.55.0",
  "@typescript-eslint/eslint-plugin": "6.14.0",
  "@typescript-eslint/parser": "6.14.0",
  "eslint-plugin-react-hooks": "4.6.0",
  "eslint-plugin-react-refresh": "0.4.5"
}
```

**ESLint Rules**:
- React Hooks rules enforced
- Unused variables warned (except prefixed with `_`)
- TypeScript recommended rules
- React Refresh component export rules

### Build Tools
```json
{
  "vite": "5.0.8",
  "postcss": "8.4.32",
  "autoprefixer": "10.4.16",
  "tailwindcss": "3.3.6"
}
```

---

## Architecture Patterns

### Project Structure
```
project-name/
├── src/                    # Frontend source
│   ├── components/         # React components
│   ├── pages/              # Page components (routes)
│   ├── lib/                # Utilities (API client)
│   ├── types/              # TypeScript types
│   ├── mock/               # Mock data (if needed)
│   ├── App.tsx             # Root component
│   ├── main.tsx            # Entry point
│   └── index.css           # Global styles
├── server/                 # Backend source
│   ├── routes/             # Express route handlers
│   ├── types/              # Backend TypeScript types
│   ├── data/               # SQLite database files
│   ├── db.ts               # Database adapter
│   ├── config.ts           # Configuration management
│   ├── utils.ts            # Utility functions
│   ├── index.ts            # Express app entry
│   └── tsconfig.json       # Backend TS config
├── public/                 # Static assets
├── dist/                   # Build output
├── package.json            # Dependencies and scripts
├── tsconfig.json           # Frontend TS config
├── vite.config.ts          # Vite configuration
├── tailwind.config.js      # Tailwind configuration
├── postcss.config.js       # PostCSS configuration
├── .eslintrc.cjs           # ESLint configuration
├── .env                    # Environment variables (gitignored)
├── .env.example            # Environment template
├── .gitignore              # Git ignore patterns
└── index.html              # HTML entry point
```

### Frontend Patterns

**Component Style**:
- Functional components with hooks
- TypeScript interfaces for props
- No class components

**State Management**:
- useState for local state
- useEffect for side effects
- useMemo for derived state
- useCallback for stable functions
- LocalStorage for persistence

**API Integration**:
- Centralized API client (`src/lib/api.ts`)
- Retry logic on failures
- Type-safe requests/responses
- Error handling

**Styling**:
- Tailwind utility classes
- Dark mode with `dark:` variants
- Responsive with `sm:`, `md:`, `lg:` breakpoints
- Custom components in `src/components/`

### Backend Patterns

**Route Organization**:
- One router per resource/domain
- Routes in `server/routes/*.ts`
- Mounted with API prefix (`/api`)

**Request Handling**:
```typescript
router.post('/endpoint', async (req, res) => {
  const { field } = req.body as RequestBody;

  // Validation
  if (!field) {
    return res.status(400).json({ message: 'Field required' });
  }

  try {
    // Business logic
    const result = await db.run('SQL', [params]);
    return res.status(200).json({ result });
  } catch (error) {
    console.error('Error:', error);
    return res.status(500).json({ message: 'Internal error' });
  }
});
```

**Database Access**:
```typescript
import { getDb } from './db.js';

const db = await getDb();
const result = await db.get('SELECT * FROM table WHERE id = ?', id);
```

**Configuration**:
- Centralized in `server/config.ts`
- Environment variables via dotenv
- Type-safe config object
- Validation of required vars

### Database Patterns

**Adapter Pattern**:
```typescript
export abstract class BaseDatabaseAdapter {
  abstract get<T>(sql: string, ...params: unknown[]): Promise<T | null>;
  abstract all<T>(sql: string, ...params: unknown[]): Promise<T[]>;
  abstract run(sql: string, ...params: unknown[]): Promise<DatabaseRunResult>;
}
```

**Query Patterns**:
```typescript
// Single record
const user = await db.get<User>('SELECT * FROM users WHERE id = ?', userId);

// Multiple records
const users = await db.all<User>('SELECT * FROM users ORDER BY created_at DESC');

// Insert
const result = await db.run(
  'INSERT INTO users (name, email) VALUES (?, ?)',
  [name, email]
);
const newId = result.lastID;

// Update
await db.run('UPDATE users SET name = ? WHERE id = ?', [newName, userId]);
```

**Prepared Statements** (SQL Injection Prevention):
- Always use `?` placeholders
- Never concatenate user input into SQL strings
- Bind parameters via array

---

## API Conventions

### URL Structure
```
/api/auth/register          POST
/api/auth/login             POST
/api/auth/verification      POST
/api/resources              GET, POST
/api/resources/:id          GET, PATCH, DELETE
```

### Status Codes
- `200 OK`: Successful GET, PATCH
- `201 Created`: Successful POST (resource created)
- `204 No Content`: Successful DELETE
- `400 Bad Request`: Invalid input
- `401 Unauthorized`: Authentication required/failed
- `403 Forbidden`: Authenticated but not authorized
- `404 Not Found`: Resource doesn't exist
- `409 Conflict`: Duplicate resource
- `500 Internal Server Error`: Server error

### Request Format
```json
{
  "field1": "value",
  "field2": 123
}
```

### Response Format
```json
{
  "message": "Success message",
  "data": { ... }
}
```

### Error Format
```json
{
  "message": "Error description",
  "code": "ERROR_CODE",
  "retryAfterSeconds": 60
}
```

---

## Environment Variables

### Required Variables
```bash
# Application
PRODUCT_NAME=YourApp
PRODUCT_LOGO_URL=https://example.com/logo.png
PUBLIC_BASE_URL=http://localhost:5173

# Server
PORT=4000
API_PREFIX=/api
CORS_ORIGIN=http://localhost:5173

# Security
SESSION_SECRET=random-32-char-string
JWT_SECRET=random-32-char-string
JWT_EXPIRES_IN=1h

# Admin Bootstrap
ADMIN_EMAIL=admin@example.com
ADMIN_PASSWORD=secure-password

# Email Verification
EMAIL_VERIFICATION_CODE_LENGTH=6
EMAIL_VERIFICATION_EXPIRATION_MINUTES=30
EMAIL_VERIFICATION_RESEND_COOLDOWN_SECONDS=60
EMAIL_VERIFICATION_MAX_ATTEMPTS=5
```

### Optional Variables (Email Service)
```bash
# Brevo (Email)
BREVO_API_KEY=your-api-key
BREVO_SENDER_EMAIL=noreply@example.com
BREVO_SUPPORT_EMAIL=support@example.com
BREVO_SENDER_NAME="Your App"
BREVO_VERIFY_TEMPLATE_ID=2
BREVO_QUESTION_ANSWERED_TEMPLATE_ID=3
BREVO_PASSWORD_RESET_TEMPLATE_ID=4
```

---

## Security Best Practices

### Password Security
- Hash with bcrypt
- Use 12 rounds (cost factor)
- Never store plain text passwords
- Never log passwords

### Token Security
- JWT tokens expire (1h default)
- Store in localStorage (frontend)
- Send via Authorization header: `Bearer <token>`
- Validate on every protected route

### SQL Injection Prevention
- Always use prepared statements
- Never concatenate user input into SQL
- Use parameterized queries with `?` placeholders

### CORS Configuration
- Configure allowed origins via environment
- Use credentials: true for cookies
- Validate origins in production

### Input Validation
- Validate all user input on backend
- Check required fields
- Validate email format
- Enforce constraints

### Secret Management
- Never commit `.env` to git
- Use random secrets (crypto.randomBytes)
- Provide `.env.example` template
- Document all required variables

---

## Ports Configuration

### Development
- **Frontend**: 5173 (Vite default)
- **Backend**: 4000 (configurable via PORT env var)

### Why These Ports?
- 5173: Vite's default, well-known
- 4000: Non-privileged, commonly used for APIs
- No conflicts with common services

### Proxy Configuration
Frontend proxies `/api` requests to backend:
```typescript
proxy: {
  '/api': {
    target: 'http://localhost:4000',
    changeOrigin: true
  }
}
```

---

## Features Support

### Built-in Features
✅ User authentication (register, login)
✅ Email verification
✅ Password reset
✅ JWT token auth
✅ Dark mode (class-based)
✅ Responsive design (mobile, tablet, desktop)
✅ CRUD operations
✅ File-based database
✅ Hot reload (dev)
✅ Production builds
✅ TypeScript throughout
✅ ESLint code quality

### Optional Features
🔲 Email notifications (requires Brevo)
🔲 Payment integration (requires additional setup)
🔲 File uploads (requires additional setup)
🔲 Real-time features (requires WebSockets)
🔲 Full-text search (SQLite FTS)

---

## Testing Strategy

### Current State
⚠️ **No testing framework in base stack**

### Recommended (Not Implemented)
```json
{
  "vitest": "^1.0.0",
  "@testing-library/react": "^14.0.0",
  "@testing-library/jest-dom": "^6.0.0",
  "@testing-library/user-event": "^14.0.0"
}
```

### Quality Assurance
Instead of automated tests, use:
- ESLint for code quality
- TypeScript for type safety
- Manual testing of user flows
- Code review checklist

---

## Build & Deployment

### Development Build
```bash
npm run dev
# Starts: TypeScript checking, backend (port 4000), frontend (port 5173)
```

### Production Build
```bash
npm run build
# Output: dist/ (frontend static files), dist/server/ (compiled backend)
```

### Preview Production
```bash
npm run preview
# Serves production build locally for testing
```

### Deployment Checklist
- [ ] Build completes without errors
- [ ] Environment variables set
- [ ] Database file accessible
- [ ] CORS origins updated for production
- [ ] Secrets rotated from defaults
- [ ] Admin password changed
- [ ] Email service configured (if using)

---

## Browser Support

### Target Browsers
- Chrome/Edge (last 2 versions)
- Firefox (last 2 versions)
- Safari (last 2 versions)
- Mobile Safari (iOS 13+)
- Chrome Android (last 2 versions)

### Polyfills
Not required for target browsers (ES2020 support)

---

## Performance Targets

### Frontend
- Initial page load: < 2s
- Time to Interactive: < 3s
- Lighthouse Performance: > 90

### Backend
- API response time: < 200ms
- Database query time: < 50ms
- JWT validation: < 10ms

### Bundle Sizes
- Frontend JS: < 500KB (gzipped)
- CSS: < 50KB (gzipped)

---

## Accessibility Standards

### WCAG 2.1 Level AA Compliance
- [ ] Keyboard navigation
- [ ] Screen reader support
- [ ] Color contrast ratios (4.5:1 minimum)
- [ ] Focus indicators
- [ ] Semantic HTML
- [ ] Form labels
- [ ] Image alt text
- [ ] ARIA labels where needed

---

## Limitations & Known Issues

### Database
- SQLite file-based (not suitable for high-concurrency)
- No connection pooling
- Manual persistence required
- Single-threaded writes

### Authentication
- Token-based (no session management)
- localStorage (XSS risk if not careful)
- No refresh tokens (logout on expiration)

### Email
- Requires third-party service (Brevo)
- Rate limits apply
- Template management external

### Scaling
- Designed for MVP/small-medium apps
- Not optimized for high traffic
- No caching layer
- No CDN integration

---

## Migration Path (Future)

### If You Outgrow This Stack

**Database**:
- SQLite → PostgreSQL or MySQL
- Keep same query patterns
- Update database adapter

**Authentication**:
- Add refresh tokens
- Implement session management
- Add OAuth providers

**Deployment**:
- Add Docker containers
- Implement CI/CD
- Add monitoring
- Implement caching

**Features**:
- Add WebSockets for real-time
- Implement file upload service
- Add search indexing
- Implement rate limiting

---

## Version Compatibility

### Minimum Versions
- Node.js: 18.x or higher
- npm: 9.x or higher

### Maximum Versions
- Tested up to Node.js 20.x
- Should work with Node.js 21.x (untested)

---

## Conclusion

This tech stack is optimized for:
- ✅ Rapid development
- ✅ Type safety
- ✅ Modern developer experience
- ✅ Easy deployment
- ✅ MVP and small-medium applications

**Perfect for**: Prototypes, MVPs, small projects, learning
**Not suitable for**: High-traffic apps, enterprise scale, complex data models

**All agents MUST follow this configuration to ensure consistency.**

---

**Configuration Version**: 1.0
**Agents Using This**: Product Analyst, Project Architect, Database Architect, API Designer, Backend Engineer, Frontend Engineer, DevOps Engineer, Quality Assurance, Premium UI Designer, Design Review Agent
**Last Verified**: 2025-11-16
