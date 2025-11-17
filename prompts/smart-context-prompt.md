# Smart Context-Aware Execution

## Overview
This prompt provides instructions for context-aware agent execution to minimize token usage and improve performance. All agents should follow these principles when loading context and executing tasks.

## Core Principles

### 1. Start with Minimal Context
**Always begin with the smallest possible context that could accomplish the task.**

- Load only the files you need to read/modify
- Exclude unrelated modules and directories
- Use the Context Manager to identify minimal file set
- Expand context only when you hit a blocker

**Example:**
```
Task: "Add a logout button to the navigation bar"
Minimal Context:
  - src/components/Navigation.tsx (the file to modify)
  - src/hooks/useAuth.ts (for logout function)
  - src/types/auth.ts (for type definitions)

DON'T load:
  - Other components (unless Navigation imports them)
  - API routes (logout function already exists in useAuth)
  - Database models
  - Test files (load only after implementation)
```

### 2. Expand Context as Needed
**If you encounter missing dependencies or unclear interfaces, expand incrementally.**

Expansion triggers:
- Type definition not found → Load type files
- Function not found → Load the module that exports it
- Integration point unclear → Load interface files
- Testing required → Load relevant test files

**Example:**
```
Initial: src/components/Button.tsx
Expand to: src/components/Button.tsx + src/theme/colors.ts (needed for styling)
Expand to: + src/types/component.ts (needed for prop types)
Stop: Don't load entire theme system, just colors.ts
```

### 3. Use Context Markers
**Mark which context you're currently using and what you might need next.**

Before starting work:
```markdown
Current Context:
- src/api/posts/routes.ts (target file)
- src/types/api.ts (type definitions)

May Need:
- src/db/models/post.ts (if queries need modification)
- src/middleware/auth.ts (if auth changes needed)
```

During work, update as you expand:
```markdown
Context Expanded:
✅ src/api/posts/routes.ts
✅ src/types/api.ts
✅ src/db/models/post.ts (added - needed for new query)

Still Excluded:
❌ Other API routes (not relevant)
❌ Frontend components (backend task)
```

### 4. Respect Module Boundaries
**Don't cross module boundaries unless the task explicitly requires it.**

Module boundaries to respect:
- Frontend ↔ Backend (only load both if full-stack task)
- Auth ↔ Posts ↔ Users (only load related modules)
- Components ↔ Pages (load together only if integrating)
- Database ↔ API (load together only if changing schema + API)

**Example:**
```
Task: "Fix user avatar not displaying"
Module: Frontend UI Components
Load:
  ✅ src/components/Avatar.tsx
  ✅ src/hooks/useUser.ts

Don't Load:
  ❌ src/api/users/* (backend API not needed)
  ❌ server/db/* (database not needed)
  ❌ src/pages/* (not modifying pages)
```

### 5. Use Context Caching
**Check if a similar task has been done before and reuse its context.**

Before loading context:
1. Check `/context/cache/` for similar task patterns
2. Review Context Manager's cached contexts
3. Reuse cached context if task is similar
4. Invalidate cache if files have changed

Cached patterns to look for:
- `add-ui-component.json` - For adding new UI components
- `fix-auth-error.json` - For authentication bugs
- `add-api-endpoint.json` - For new API endpoints
- `refactor-component.json` - For refactoring tasks

### 6. Integrate MCP Tools Efficiently
**When using MCP tools like Playwright, load only the context needed for that specific test.**

MCP tool context rules:
- **Playwright for component test**: Load component file + its immediate props/types
- **Playwright for feature test**: Load feature module files (3-5 components max)
- **Playwright for E2E test**: Load workflow files (authentication → target feature)

**Example:**
```
Task: "Test the registration form with Playwright"
Context for Playwright:
  ✅ src/components/auth/RegistrationForm.tsx
  ✅ src/utils/validation.ts
  ✅ src/types/form.ts
  ✅ src/routes.tsx (for /register route)

Don't Load:
  ❌ src/api/* (Playwright tests UI, not backend)
  ❌ src/components/Dashboard/* (not part of registration)
  ❌ server/* (frontend test only)
```

### 7. Use Context Diffs
**For multi-step or iterative tasks, use context diff to load only changed/new files.**

Context diff workflow:
1. Initial load: Entry points + target files
2. Make changes
3. Next iteration: Load ONLY newly referenced files
4. Unload files no longer needed
5. Keep type definitions loaded throughout

**Example:**
```
Step 1 - Initial Implementation:
  Load: [PostList.tsx, Post.tsx, types/post.ts]

Step 2 - Add Feature:
  Context Diff: +[PostActions.tsx, hooks/usePostActions.ts]
  Keep: [types/post.ts] (still needed)
  Unload: [PostList.tsx] (no longer modifying)

Step 3 - Add Tests:
  Context Diff: +[Post.test.tsx, PostActions.test.tsx]
  Keep: [types/post.ts]
  Unload: [hooks/usePostActions.ts] (implementation complete)
```

## Execution Workflow

### Step 1: Analyze Request
```
Questions to ask:
1. What is the task type? (feature addition, bug fix, refactoring, testing)
2. Which module(s) are affected?
3. Is this a frontend, backend, or full-stack task?
4. What is the minimal file set needed?
5. Are there cached contexts for similar tasks?
```

### Step 2: Load Minimal Context
```
Actions:
1. Query Context Manager for minimal context
2. Load only required files (3-7 files typically)
3. Exclude all other modules
4. Document what's loaded and why
```

### Step 3: Execute with Focus
```
Actions:
1. Work within loaded context
2. Mark when you need to expand context
3. Load additional files one at a time as needed
4. Keep running list of files accessed
```

### Step 4: Validate and Test
```
Actions:
1. Load test files for affected code
2. Use MCP tools with minimal context
3. Run only affected tests
4. Document test results
```

### Step 5: Update Context System
```
Actions:
1. Record which files were actually used
2. Update context cache if task was successful
3. Log token usage and time saved
4. Provide feedback to Workflow Optimizer
```

## Context Loading Patterns

### Pattern 1: Add UI Component
```yaml
context_level: minimal
estimated_tokens: 800-1200

load:
  - Target parent component file
  - Design system/theme files
  - Component prop types
  - Similar existing component (for patterns)

exclude:
  - Other pages/components
  - Backend code
  - Database files
  - Test files (until implementation done)
```

### Pattern 2: Add API Endpoint
```yaml
context_level: module
estimated_tokens: 1500-2000

load:
  - API routes file
  - Controller file (new or existing)
  - Database model
  - Request/response types
  - Auth middleware (if protected endpoint)

exclude:
  - Frontend components
  - Other API modules
  - Database migrations
  - Test files (until implementation done)
```

### Pattern 3: Fix Bug
```yaml
context_level: minimal
estimated_tokens: 500-1000

load:
  - File where error occurs
  - Files in error stack trace
  - Type definitions
  - Immediate dependencies

exclude:
  - Unrelated modules
  - Test files (load after fix)
  - Documentation
  - Configuration files
```

### Pattern 4: Refactor Module
```yaml
context_level: module
estimated_tokens: 2000-4000

load:
  - All files in target module
  - Type definitions
  - Files that import from module
  - Test files for module

exclude:
  - Unrelated modules
  - Build configuration
  - Documentation (unless API changed)
```

### Pattern 5: Integration Task
```yaml
context_level: full
estimated_tokens: 4000-6000

load:
  - Multiple modules involved
  - Integration points
  - Shared types
  - Configuration files

exclude:
  - Unrelated modules
  - Can still exclude tests until final validation
```

## Token Budget Guidelines

### Token Budget by Task Type

| Task Type | Context Level | Estimated Tokens | Max Files |
|-----------|---------------|------------------|-----------|
| Small bug fix | Minimal | 500-1,000 | 2-4 |
| Add simple component | Minimal | 800-1,200 | 3-5 |
| Add API endpoint | Module | 1,500-2,000 | 5-8 |
| Feature addition | Module | 2,000-3,500 | 7-12 |
| Refactoring | Module | 2,000-4,000 | 10-15 |
| Integration | Full | 4,000-6,000 | 15-25 |
| Major feature | Full | 5,000-10,000 | 20-40 |

### Token Warnings

🟢 **Green Zone (< 2,000 tokens)**: Optimal context size, proceed confidently

🟡 **Yellow Zone (2,000-5,000 tokens)**: Acceptable, but review if all files are needed

🔴 **Red Zone (> 5,000 tokens)**: Too much context, look for optimization opportunities

🚫 **Critical (> 10,000 tokens)**: Stop and reassess strategy, break into smaller tasks

## MCP Tool Integration Guidelines

### Playwright Testing Context

#### Component Test Context
```
Load:
  - Component file being tested
  - Component prop types/interfaces
  - Any child components used directly

Tokens: ~500-800

Example:
  Testing LoginButton.tsx
  Load: LoginButton.tsx, button.types.ts, useAuth.ts
  Exclude: LoginPage.tsx, other auth components
```

#### Feature Test Context
```
Load:
  - Main feature component(s)
  - Related sub-components
  - State management hooks
  - Route configuration (if needed)

Tokens: ~1200-1800

Example:
  Testing user registration flow
  Load: RegisterPage.tsx, RegistrationForm.tsx, useAuth.ts, routes.tsx
  Exclude: Dashboard, other features
```

#### E2E Test Context
```
Load:
  - All components in user workflow
  - Authentication components
  - Navigation components
  - Target feature components

Tokens: ~2000-3000

Example:
  Testing "Register → Verify → Dashboard" flow
  Load: Auth module, Dashboard module, Navigation
  Exclude: Admin features, settings, other workflows
```

### MCP Tool Optimization
- Initialize browser once, reuse for multiple tests
- Run independent tests in parallel when possible
- Capture screenshots only on failure or explicit validation
- Use accessibility snapshots instead of full page loads when possible
- Close browser sessions when done to free resources

## Example Workflows

### Example 1: Add "Share" Button to Post Component

#### Request Analysis
```
Task Type: Feature addition (UI)
Module: UI Components
Complexity: Low
Expected Context: Minimal
```

#### Context Loading
```yaml
Step 1 - Initial Load:
  files:
    - src/components/Post.tsx (will add button here)
    - src/components/Button.tsx (reuse button component)
    - src/types/post.ts (post types)
  tokens: ~900

Step 2 - Add Share Hook:
  context_diff:
    add:
      - src/hooks/useShare.ts (new file)
      - src/types/share.ts (new types)
  tokens: +400 = 1,300 total

Step 3 - Testing:
  context_diff:
    add:
      - src/components/Post.test.tsx
    remove:
      - src/components/Button.tsx (not modifying)
  tokens: 1,100 total (after removal)

Total: ~1,300 tokens peak
Savings: ~48,700 tokens (vs loading entire codebase)
Time: ~3 minutes
```

### Example 2: Fix Authentication Error

#### Request Analysis
```
Task Type: Bug fix
Error: "Token validation failing"
Module: Backend Authentication
Complexity: Low
Expected Context: Minimal
```

#### Context Loading
```yaml
Step 1 - Error Trace:
  files:
    - server/routes/auth.ts (error location)
    - server/middleware/auth.ts (token validation)
    - server/types/auth.ts (auth types)
  tokens: ~700

Step 2 - Investigation:
  context_diff:
    add:
      - server/utils/jwt.ts (token generation)
  tokens: ~900 total

Step 3 - Fix + Test:
  context_diff:
    add:
      - server/routes/auth.test.ts
  tokens: ~1,200 total

Total: ~1,200 tokens peak
Savings: ~48,800 tokens
Time: ~5 minutes
```

### Example 3: Add New API Endpoint

#### Request Analysis
```
Task Type: Feature addition (Backend)
Endpoint: GET /api/posts/:id/comments
Module: API + Database
Complexity: Medium
Expected Context: Module
```

#### Context Loading
```yaml
Step 1 - Design:
  files:
    - server/routes/posts.ts (existing routes)
    - server/types/api.ts (response types)
  tokens: ~600

Step 2 - Implementation:
  context_diff:
    add:
      - server/controllers/comments.ts (new)
      - server/db/models/comment.ts (model)
      - server/middleware/auth.ts (auth check)
  tokens: ~1,800 total

Step 3 - Testing:
  context_diff:
    add:
      - server/routes/posts.test.ts
      - tests/fixtures/comments.json
    remove:
      - server/middleware/auth.ts (not modifying)
  tokens: ~2,000 total

Total: ~2,000 tokens peak
Savings: ~48,000 tokens
Time: ~8 minutes
```

## Best Practices Summary

✅ **DO:**
- Start with 2-5 files maximum
- Use Context Manager to identify minimal context
- Expand context incrementally as needed
- Use context caching for repeated tasks
- Respect module boundaries
- Load tests only after implementation
- Use MCP tools with focused context
- Track token usage and optimize
- Document what context you're using

❌ **DON'T:**
- Load entire codebase "just in case"
- Cross module boundaries unnecessarily
- Load test files before implementation is done
- Ignore context cache
- Load unrelated configuration files
- Use MCP tools with full codebase context
- Skip context documentation
- Forget to update context cache after successful tasks

## Measuring Success

### Context Efficiency Metrics

Track these metrics for each task:
- **Tokens used**: Actual context loaded
- **Token savings**: Compared to full codebase load
- **Files loaded**: Number of files in context
- **Time taken**: Total execution time
- **Cache hits**: How often cached context was reused
- **Context expansions**: How many times context was expanded
- **Success rate**: Task completed successfully with minimal context

### Target Metrics

- Token savings: **>90%** compared to full context
- Average files loaded: **<10** for most tasks
- Cache hit rate: **>60%** for common task patterns
- Context expansion rate: **<2** expansions per task
- Success rate: **>95%** task completion with minimal context

## Integration with Context System

This prompt works together with:
- **Context Manager**: Provides minimal file sets
- **Workflow Optimizer**: Identifies task patterns
- **MCP Coordinator**: Optimizes MCP tool usage
- **Execute with Context**: Implements execution strategies
- **Context Templates**: Provides pre-built patterns

Always consult these systems before loading context and executing tasks.
