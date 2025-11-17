# Project Specification: BlogHub

**Created by**: Product Analyst
**Date**: 2025-11-16
**Version**: 1.0

## Overview

**Project Name**: BlogHub
**Description**: A multi-author blogging platform where registered users can write and publish articles, and anyone (including guests) can read and comment on them.
**Target Users**: Content creators (bloggers) and readers
**Tech Stack**: See `agents/tech-stack-config.md` for complete details
  - Frontend: React 18 + TypeScript + Tailwind CSS + Vite
  - Backend: Express 5 + TypeScript + Node.js
  - Database: SQL.js (SQLite)

## Business Requirements

### Problem Statement
Enable multiple authors to share content publicly with an engaged audience that can provide feedback through comments without registration barriers.

### Target Audience
- **Primary Users**: Writers and bloggers who want to publish content
- **Secondary Users**: Readers who want to consume content and engage

### Core Value Proposition
- Writers can easily publish articles
- Readers can consume content and engage without creating accounts
- Simple moderation for quality control

## MVP Features

1. **User Registration & Authentication**
   - User Story: As a writer, I want to create an account so that I can write and publish articles
   - Priority: HIGH (MVP Critical)
   - Complexity: MEDIUM
   - Notes: Standard email/password registration

2. **Article Creation & Publishing**
   - User Story: As a registered user, I want to write and publish articles so that others can read them
   - Priority: HIGH (MVP Critical)
   - Complexity: MEDIUM
   - Notes: Articles are public by default once published

3. **Article Viewing**
   - User Story: As anyone (guest or user), I want to read published articles
   - Priority: HIGH (MVP Critical)
   - Complexity: LOW

4. **Guest Commenting**
   - User Story: As anyone (guest or user), I want to comment on articles to share my thoughts
   - Priority: HIGH (MVP Critical)
   - Complexity: MEDIUM
   - Notes: Guest users provide name when commenting

5. **Admin Moderation**
   - User Story: As an admin, I want to delete inappropriate articles/comments
   - Priority: MEDIUM
   - Complexity: LOW

## Future Enhancements (Post-MVP)
- Categories/tags for articles
- Article search functionality
- User profiles with bio
- Article likes/reactions
- Draft saving
- Rich text editor
- Featured articles

## Data Models

### Entity: User
**Description**: Represents registered writers on the platform

**Fields:**
- `id` (integer, primary key, auto-increment)
- `name` (text, required) - Display name
- `email` (text, required, unique) - Login credential
- `password_hash` (text, required) - Hashed password
- `role` (text, required) - Either 'user' or 'admin'
- `created_at` (datetime, auto) - Account creation timestamp
- `email_verified` (integer, default 0) - Email verification status (0/1)
- `email_verified_at` (datetime, nullable) - Verification timestamp

**Relationships:**
- One User → Many Articles
- One User → Many Comments (if registered user commented)

**Constraints:**
- Email must be unique
- Role must be 'user' or 'admin'

**Indexes:**
- Index on `email` for fast login lookups

---

### Entity: Article
**Description**: Represents blog articles/posts

**Fields:**
- `id` (integer, primary key, auto-increment)
- `title` (text, required, max 200 chars)
- `content` (text, required)
- `author_id` (integer, foreign key → users.id)
- `status` (text, required, check constraint) - 'draft' or 'published'
- `created_at` (datetime, auto)
- `updated_at` (datetime, auto)

**Relationships:**
- Many Articles → One User (author)
- One Article → Many Comments

**Constraints:**
- Status must be in ['draft', 'published']
- Title max length 200 characters

**Indexes:**
- Index on `author_id` for user-specific queries
- Index on `status` for filtering published articles
- Index on `created_at` for sorting

---

### Entity: Comment
**Description**: Represents comments on articles (from guests or registered users)

**Fields:**
- `id` (integer, primary key, auto-increment)
- `article_id` (integer, foreign key → articles.id)
- `author_name` (text, required) - Name provided by commenter
- `author_user_id` (integer, nullable, foreign key → users.id) - If registered user
- `content` (text, required, max 500 chars)
- `created_at` (datetime, auto)

**Relationships:**
- Many Comments → One Article
- Many Comments → One User (optional, if registered user)

**Constraints:**
- Content max length 500 characters

**Indexes:**
- Index on `article_id` for fetching article comments
- Index on `created_at` for sorting

## Database Schema

```sql
-- Users table (authentication)
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

-- Articles table
CREATE TABLE IF NOT EXISTS articles (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  title TEXT NOT NULL,
  content TEXT NOT NULL,
  author_id INTEGER NOT NULL,
  status TEXT NOT NULL CHECK(status IN ('draft', 'published')),
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  updated_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (author_id) REFERENCES users(id)
);

-- Comments table
CREATE TABLE IF NOT EXISTS comments (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  article_id INTEGER NOT NULL,
  author_name TEXT NOT NULL,
  author_user_id INTEGER,
  content TEXT NOT NULL,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (article_id) REFERENCES articles(id),
  FOREIGN KEY (author_user_id) REFERENCES users(id)
);

-- Indexes
CREATE INDEX IF NOT EXISTS idx_articles_author ON articles(author_id);
CREATE INDEX IF NOT EXISTS idx_articles_status ON articles(status);
CREATE INDEX IF NOT EXISTS idx_articles_created ON articles(created_at);
CREATE INDEX IF NOT EXISTS idx_comments_article ON comments(article_id);
CREATE INDEX IF NOT EXISTS idx_comments_created ON comments(created_at);
```

**Seed Data:**
- Admin account: `ADMIN_EMAIL` and `ADMIN_PASSWORD` from .env

## API Endpoints

### Authentication

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

### Article Endpoints

| Method | Path | Description | Auth Required |
|--------|------|-------------|---------------|
| GET | `/api/articles` | List published articles | No |
| GET | `/api/articles/:id` | Get single article | No |
| POST | `/api/articles` | Create new article | Yes |
| PATCH | `/api/articles/:id` | Update article | Yes (owner/admin) |
| DELETE | `/api/articles/:id` | Delete article | Yes (owner/admin) |
| GET | `/api/users/:userId/articles` | Get user's articles | No |

**GET /api/articles**
- Query params: `?status=published&limit=50&offset=0`
- Response (200): `{ articles: [...] }`

**POST /api/articles**
- Request: `{ title, content, status }`
- Response (201): `{ message, article }`
- Errors: 400 (validation), 401 (not authenticated)

**PATCH /api/articles/:id**
- Request: `{ title?, content?, status? }`
- Response (200): `{ message, article }`
- Errors: 400, 401, 403 (not owner), 404

---

### Comment Endpoints

| Method | Path | Description | Auth Required |
|--------|------|-------------|---------------|
| GET | `/api/articles/:articleId/comments` | List article comments | No |
| POST | `/api/articles/:articleId/comments` | Add comment to article | No |
| DELETE | `/api/comments/:id` | Delete comment | Yes (admin only) |

**GET /api/articles/:articleId/comments**
- Response (200): `{ comments: [...] }`

**POST /api/articles/:articleId/comments**
- Request: `{ author_name, content, author_user_id? }`
- Response (201): `{ message, comment }`
- Errors: 400 (validation), 404 (article not found)

**DELETE /api/comments/:id**
- Response (200): `{ message }`
- Errors: 401, 403 (admin only), 404

## UI Pages & Components

### Page Structure

| Route | Component | Auth Required | Description |
|-------|-----------|---------------|-------------|
| `/` | HomePage | No | Landing page with article list |
| `/login` | LoginPage | No | User authentication |
| `/signup` | SignupPage | No | User registration |
| `/articles/:id` | ArticleDetailPage | No | Single article view |
| `/articles/new` | CreateArticlePage | Yes | Create new article |
| `/articles/:id/edit` | EditArticlePage | Yes (owner) | Edit article |
| `/dashboard` | DashboardPage | Yes | User's articles dashboard |
| `/admin` | AdminDashboardPage | Yes (admin) | Admin panel |

---

### Component Hierarchy

**App.tsx**
├── Header (navigation, auth status, dark mode toggle)
├── Router
│   ├── HomePage
│   │   ├── HeroSection
│   │   ├── ArticleList
│   │   │   └── ArticleCard (multiple)
│   │   └── Pagination
│   ├── ArticleDetailPage
│   │   ├── ArticleHeader (title, author, date)
│   │   ├── ArticleContent
│   │   └── CommentSection
│   │       ├── CommentForm
│   │       └── CommentList
│   │           └── CommentCard (multiple)
│   ├── CreateArticlePage
│   │   └── ArticleForm
│   ├── EditArticlePage
│   │   └── ArticleForm (with initial data)
│   ├── DashboardPage
│   │   ├── UserStats
│   │   ├── ArticleList (user's articles)
│   │   └── QuickActions
│   └── AdminDashboardPage
│       ├── UserManagement
│       └── ContentModeration
└── Footer

---

### Key Components

**ArticleCard**
- Props: `{ id, title, content (excerpt), author, createdAt, status }`
- Features: Click to navigate, hover effects, responsive
- Dark mode support

**ArticleForm**
- Props: `{ onSubmit, initialData?, isEditing? }`
- Features: Validation, error handling, loading states
- Fields: Title (required), Content (textarea, required), Status (draft/published)

**CommentForm**
- Props: `{ articleId, onCommentAdded }`
- Features: Guest or user commenting
- Fields: Name (required for guests), Content (required)

**Header**
- Features: Logo, navigation links, auth buttons/user menu
- Responsive: Hamburger menu on mobile
- Dark mode toggle

## User Flows

### 1. User Registration & Email Verification
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

### 2. Creating an Article
**Precondition**: User must be authenticated

1. User clicks "Write Article" button in header
2. User navigates to `/articles/new`
3. User fills form: title, content
4. User selects status: draft or published
5. User clicks "Submit"
6. System validates input
7. POST `/api/articles`
8. System creates article
9. System returns created article
10. User redirected to `/articles/:id`
11. User sees success message

### 3. Commenting on Article (Guest)
**Precondition**: None (public feature)

1. Guest visits `/articles/:id`
2. Guest reads article
3. Guest scrolls to comment section
4. Guest fills form: name, comment
5. Guest clicks "Post Comment"
6. System validates input
7. POST `/api/articles/:id/comments`
8. System creates comment (author_user_id = null)
9. Comment appears in list
10. Guest sees success message

### 4. Admin Moderation
**Precondition**: User role must be 'admin'

1. Admin logs in with admin credentials
2. Admin navigates to `/admin`
3. Admin sees dashboard with:
   - Total users count
   - Total articles count
   - Recent comments
4. Admin reviews flagged content
5. Admin clicks "Delete" on inappropriate comment
6. DELETE `/api/comments/:id`
7. System deletes comment
8. Comment removed from list

## Agent Execution Plan

### Phase 1: Foundation (Sequential)

**Step 1: Project Architect**
- Input: Project name "BlogHub", description, tech stack
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
  - Create tables: users, articles, comments
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
  - Implement auth routes (register, login, verification)
  - Implement article routes (CRUD)
  - Implement comment routes (create, list, delete)
  - Add authentication middleware
  - Implement business logic
  - Add validation
- Output: Working backend API
- Validation: All endpoints respond correctly
- Can run in parallel with: Frontend Engineer (after API Designer)

**Step 6: Frontend Engineer**
- Input: UI pages list, API specification
- Tasks:
  - Create HomePage with article list
  - Create ArticleDetailPage with comments
  - Create CreateArticlePage and EditArticlePage
  - Create DashboardPage
  - Create AdminDashboardPage
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
PRODUCT_NAME=BlogHub
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

# Email Verification (optional)
EMAIL_VERIFICATION_CODE_LENGTH=6
EMAIL_VERIFICATION_EXPIRATION_MINUTES=30
EMAIL_VERIFICATION_RESEND_COOLDOWN_SECONDS=60
EMAIL_VERIFICATION_MAX_ATTEMPTS=5

# Email Service (Brevo - optional)
BREVO_API_KEY=[your-key]
BREVO_SENDER_EMAIL=noreply@bloghub.com
BREVO_SENDER_NAME="BlogHub"
BREVO_VERIFY_TEMPLATE_ID=2
```

---

## Success Criteria

- [ ] All agents executed successfully
- [ ] Development servers running (`npm run dev`)
- [ ] All API endpoints functional
- [ ] All UI pages rendering
- [ ] Authentication working end-to-end
- [ ] Users can register and log in
- [ ] Users can create and edit articles
- [ ] Guests can view articles
- [ ] Guests can post comments
- [ ] Admin can delete comments
- [ ] Database persisting correctly
- [ ] No TypeScript errors
- [ ] No ESLint errors
- [ ] Dark mode functional
- [ ] Responsive on mobile/tablet/desktop

---

**End of Specification**
