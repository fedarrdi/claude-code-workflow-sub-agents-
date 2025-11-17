# Project Specification: [Project Name]

**Created by**: Product Analyst
**Date**: [YYYY-MM-DD]
**Version**: 1.0

## Overview

**Project Name**: [Name]
**Description**: [2-3 sentence description of what this project does]
**Target Users**: [Who will use this application]
**Tech Stack**: See `agents/tech-stack-config.md` for complete details
  - Frontend: React 18 + TypeScript + Tailwind CSS + Vite
  - Backend: Express 5 + TypeScript + Node.js
  - Database: SQL.js (SQLite)

## Business Requirements

### Problem Statement
[What problem does this solve? 2-3 sentences]

### Target Audience
- **Primary Users**: [description]
- **Secondary Users**: [if applicable]

### Core Value Proposition
[List 3-5 bullet points of key value this provides to users]
- [Value 1]
- [Value 2]
- [Value 3]

## MVP Features

[List all features for the Minimum Viable Product. For each feature, include:]

1. **[Feature Name]**
   - User Story: As a [user type], I want to [action] so that [benefit]
   - Priority: [HIGH/MEDIUM/LOW] (MVP Critical/Nice to have)
   - Complexity: [LOW/MEDIUM/HIGH]
   - Notes: [Any additional context]

2. **[Feature Name]**
   - User Story: As a [user type], I want to [action] so that [benefit]
   - Priority: [HIGH/MEDIUM/LOW]
   - Complexity: [LOW/MEDIUM/HIGH]
   - Notes: [Any additional context]

[Continue for all MVP features...]

## Future Enhancements (Post-MVP)
[Features that can wait for version 2]
- [Feature X]
- [Feature Y]
- [Feature Z]

## Data Models

[For each entity in your system, create a section like this:]

### Entity: [EntityName]
**Description**: [What this entity represents]

**Fields:**
- `id` ([integer/text], primary key, [auto-increment if integer])
- `field_name` (data_type, constraints) - Description
- `another_field` (data_type, constraints) - Description
- `created_at` (datetime, auto) - Timestamp
- [Add all fields...]

**Relationships:**
- [Describe relationships, e.g., "One User → Many Posts"]
- [One [This Entity] → Many [Other Entity]]

**Constraints:**
- [List any check constraints, unique constraints, etc.]
- [e.g., "Email must be unique"]
- [e.g., "Status must be in ['active', 'inactive']"]

**Indexes:**
- [List fields that should be indexed]
- [e.g., "Index on `email` for fast lookups"]
- [e.g., "Index on `created_at` for sorting"]

---

### Entity: User (Default - Always Include)
**Description**: Represents registered users of the platform

**Fields:**
- `id` (integer, primary key, auto-increment)
- `name` (text, required) - Display name
- `email` (text, required, unique) - Login credential
- `password_hash` (text, required) - Hashed password
- `role` (text, required) - Either 'user' or 'admin'
- `created_at` (datetime, auto) - Account creation timestamp
- `email_verified` (integer, default 0) - Email verification status
- `email_verified_at` (datetime, nullable) - Verification timestamp

**Relationships:**
- One User → Many [YourEntities]

**Constraints:**
- Email must be unique
- Role must be 'user' or 'admin'

**Indexes:**
- Index on `email` for fast login lookups

---

[Add more entities as needed...]

## Database Schema

```sql
-- Users table (authentication - ALWAYS INCLUDE)
CREATE TABLE IF NOT EXISTS users (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  name TEXT NOT NULL,
  email TEXT NOT NULL UNIQUE,
  password_hash TEXT NOT NULL,
  role TEXT NOT NULL CHECK(role IN ('user', 'admin')),
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  email_verified INTEGER DEFAULT 0,
  email_verified_at DATETIME
);

-- [Your Entity] table
CREATE TABLE IF NOT EXISTS [table_name] (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  field1 TEXT NOT NULL,
  field2 TEXT,
  user_id INTEGER NOT NULL,
  status TEXT NOT NULL CHECK(status IN ('value1', 'value2')),
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  updated_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(id)
);

-- [Add more tables...]

-- Indexes
CREATE INDEX IF NOT EXISTS idx_[table]_[field] ON [table]([field]);
[Add more indexes...]
```

**Seed Data:**
- Admin account: `ADMIN_EMAIL` and `ADMIN_PASSWORD` from .env

## API Endpoints

### Authentication (Default - Always Include)

| Method | Path | Description | Auth Required |
|--------|------|-------------|---------------|
| POST | `/api/auth/register` | Create new user account | No |
| POST | `/api/auth/login` | Authenticate user | No |
| GET | `/api/auth/me` | Get current user info | Yes |
| POST | `/api/auth/verification/request` | Request email verification | Yes |
| POST | `/api/auth/verification/verify` | Verify email with code | Yes |

**POST /api/auth/register**
- Request: `{ name, email, password, confirmPassword }`
- Response (201): `{ message, user, token }`
- Errors: 400 (validation), 409 (duplicate email)

**POST /api/auth/login**
- Request: `{ email, password }`
- Response (200): `{ message, user, token }`
- Errors: 400 (missing fields), 401 (invalid credentials)

---

### [Resource] Endpoints

| Method | Path | Description | Auth Required |
|--------|------|-------------|---------------|
| GET | `/api/[resources]` | List all resources | [Yes/No] |
| GET | `/api/[resources]/:id` | Get single resource | [Yes/No] |
| POST | `/api/[resources]` | Create new resource | [Yes/No] |
| PATCH | `/api/[resources]/:id` | Update resource | [Yes/No] |
| DELETE | `/api/[resources]/:id` | Delete resource | [Yes/No] |

**GET /api/[resources]**
- Query params: `?[filter_params]`
- Response (200): `{ [resources]: [...] }`

**POST /api/[resources]**
- Request: `{ field1, field2, ... }`
- Response (201): `{ message, [resource] }`
- Errors: 400 (validation), 401 (not authenticated)

**PATCH /api/[resources]/:id**
- Request: `{ field1?, field2?, ... }`
- Response (200): `{ message, [resource] }`
- Errors: 400, 401, 403 (not authorized), 404

[Add more endpoint groups...]

---

## UI Pages & Components

### Page Structure

| Route | Component | Auth Required | Description |
|-------|-----------|---------------|-------------|
| `/` | HomePage | No | Landing page / main view |
| `/login` | LoginPage | No | User authentication |
| `/signup` | SignupPage | No | User registration |
| `/[resource]/:id` | [Resource]DetailPage | [Yes/No] | Single resource view |
| `/[resource]/new` | Create[Resource]Page | Yes | Create new resource |
| `/dashboard` | DashboardPage | Yes | User's dashboard |
| `/admin` | AdminDashboardPage | Yes (admin) | Admin panel |
[Add more routes...]

---

### Component Hierarchy

**App.tsx**
├── Header (navigation, auth status, dark mode toggle)
├── Router
│   ├── HomePage
│   │   ├── [MainSection]
│   │   ├── [ResourceList]
│   │   │   └── [ResourceCard] (multiple)
│   │   └── [OtherSection]
│   ├── [Resource]DetailPage
│   │   ├── [ResourceHeader]
│   │   ├── [ResourceContent]
│   │   └── [InteractionSection]
│   ├── Create[Resource]Page
│   │   └── [Resource]Form
│   └── DashboardPage
│       ├── [UserStats]
│       └── [UserResourceList]
└── Footer

[Describe the component tree for your application]

---

### Key Components

**[Component1Name]**
- Props: `{ prop1, prop2, ... }`
- Features: [List key features/behaviors]
- Dark mode support: [Yes/No]

**[Component2Name]**
- Props: `{ prop1, prop2, ... }`
- Features: [List key features/behaviors]

[Describe all major components...]

**Header** (Always include)
- Features: Logo, navigation links, auth buttons/user menu
- Responsive: Hamburger menu on mobile
- Dark mode toggle

## User Flows

### 1. User Registration & Email Verification (Default - Always Include)
1. User visits `/signup`
2. User fills form: name, email, password, confirmPassword
3. System validates input client-side
4. POST `/api/auth/register`
5. System creates user account (email_verified = false)
6. System returns JWT token
7. User automatically logged in
8. User sees verification prompt
9. User clicks "Send verification code"
10. System sends 6-digit code to email
11. User enters code
12. POST `/api/auth/verification/verify`
13. System verifies code
14. User email_verified = true
15. User sees success message

### 2. [Main User Flow - Describe primary use case]
**Precondition**: [Any requirements, e.g., "User must be authenticated"]

1. [Step 1]
2. [Step 2]
3. [Step 3]
[Continue with all steps...]

### 3. [Secondary User Flow]
**Precondition**: [Any requirements]

1. [Step 1]
2. [Step 2]
[Continue...]

### 4. Admin Workflow (If applicable)
**Precondition**: User role must be 'admin'

1. Admin logs in with admin credentials
2. Admin navigates to `/admin`
3. [Describe admin capabilities]
[Continue...]

## Agent Execution Plan

### Phase 1: Foundation (Sequential)

**Step 1: Project Architect**
- Input: Project name "[Name]", description, tech stack
- Tasks:
  - Create project structure
  - Install dependencies
  - Configure build tools
- Output: Ready-to-develop project
- Validation: `npm install` succeeds

**Step 2: DevOps Engineer**
- Input: Environment variables (see below)
- Tasks:
  - Create `.env` file
  - Configure CORS origins
  - Set up port configuration
- Output: Environment configured
- Validation: `.env` file exists with all required variables

**Step 3: Database Architect**
- Input: Database schema (see Data Models section)
- Tasks:
  - Implement database adapter
  - Create tables: [list all tables]
  - Set up foreign keys
  - Seed admin account
- Output: `server/db.ts` and database file
- Validation: Database file created, admin account seeded

**Step 4: API Designer**
- Input: API endpoints list (see API Endpoints section)
- Tasks:
  - Document all endpoints
  - Define request/response types
  - Specify status codes
  - Create type definitions
- Output: API_SPECIFICATION.md
- Validation: All endpoints documented

---

### Phase 2: Implementation (Parallel Possible)

**Step 5: Backend Engineer**
- Input: API specification, database schema
- Tasks:
  - Implement auth routes
  - Implement [resource] routes
  - [List other route groups]
  - Add authentication middleware
  - Implement business logic
  - Add validation
- Output: Working backend API
- Validation: All endpoints respond correctly
- Can run in parallel with: Frontend Engineer (after API Designer)

**Step 6: Frontend Engineer**
- Input: UI pages list, API specification
- Tasks:
  - Create [list all pages]
  - Implement authentication flow
  - Integrate with API
  - Add dark mode
- Output: Working frontend application
- Validation: All pages render, API calls work
- Can run in parallel with: Backend Engineer

---

### Phase 3: Quality & Enhancement (Sequential)

**Step 7: Quality Assurance**
- Input: Complete codebase
- Tasks:
  - Run ESLint
  - Check TypeScript compilation
  - Review security practices
  - Validate error handling
- Output: Quality report
- Validation: No critical issues found

---

### Environment Variables Required

```bash
# Application
PRODUCT_NAME=[Your App Name]
PUBLIC_BASE_URL=http://localhost:5173

# Server
PORT=4000
API_PREFIX=/api
CORS_ORIGIN=http://localhost:5173

# Security
SESSION_SECRET=[random-secret]
JWT_SECRET=[random-secret]
JWT_EXPIRES_IN=1h

# Admin Account
ADMIN_EMAIL=admin@example.com
ADMIN_PASSWORD=[secure-password]

# Email Verification
EMAIL_VERIFICATION_CODE_LENGTH=6
EMAIL_VERIFICATION_EXPIRATION_MINUTES=30
EMAIL_VERIFICATION_RESEND_COOLDOWN_SECONDS=60
EMAIL_VERIFICATION_MAX_ATTEMPTS=5

# Email Service (Brevo - if needed)
BREVO_API_KEY=[your-key]
BREVO_SENDER_EMAIL=noreply@yourdomain.com
BREVO_SENDER_NAME="[Your App Name]"
BREVO_VERIFY_TEMPLATE_ID=2

# Add any custom environment variables your app needs
```

---

## Success Criteria

Before marking project as complete, verify ALL of these:

- [ ] All agents executed successfully
- [ ] Development servers running (`npm run dev`)
- [ ] All API endpoints functional
- [ ] All UI pages rendering
- [ ] Authentication working end-to-end
- [ ] [Key feature 1] working
- [ ] [Key feature 2] working
- [ ] [Key feature 3] working
- [ ] Database persisting correctly
- [ ] No TypeScript errors
- [ ] No ESLint errors
- [ ] Dark mode functional
- [ ] Responsive on mobile/tablet/desktop

---

**End of Specification**

---

## Notes for Product Analyst

When using this template:

1. **Replace all [placeholders]** with actual values
2. **Delete sections** that don't apply to your project
3. **Add sections** for project-specific requirements
4. **Be specific** - avoid vague descriptions
5. **Include examples** where helpful
6. **Validate completeness** - ensure all features map to database entities, API endpoints, and UI pages
7. **Review with user** - if anything is unclear, ask more questions

**Remember**: A complete, detailed specification leads to a better final product!
