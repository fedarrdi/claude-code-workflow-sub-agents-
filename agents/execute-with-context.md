# Execute with Context - Context-Aware Agent Execution System

## Overview
This document provides a comprehensive system for executing agents with different context levels (minimal, module, full) with examples and optimization rules. It serves as the execution guide for all agent operations.

## Context Levels

### Level 1: Minimal Context (500-1,200 tokens)
**Use For:** Single file modifications, small bug fixes, isolated component additions

**What to Load:**
- Target file(s) being modified (1-3 files)
- Immediate dependencies (imports)
- Type definitions for the target
- One similar file for pattern matching (optional)

**What to Exclude:**
- Other modules
- Test files (load after implementation)
- Configuration files
- Documentation

**Examples:**
- Add a button to a component
- Fix a null reference error
- Update a function signature
- Add a utility function

### Level 2: Module Context (1,200-3,500 tokens)
**Use For:** Feature additions, refactoring within a module, API endpoint additions

**What to Load:**
- All files in the target module (5-15 files)
- Module entry points
- Direct dependencies (one level deep)
- Related test files
- Type definitions

**What to Exclude:**
- Other modules
- Distant dependencies
- Build configuration
- Global utilities (unless used)

**Examples:**
- Add comment feature to posts
- Create new API endpoint with controller
- Refactor authentication module
- Add form validation

### Level 3: Full Context (3,500-10,000 tokens)
**Use For:** Cross-module changes, architectural refactors, major features

**What to Load:**
- Multiple related modules
- Integration points
- Shared types and utilities
- Configuration files
- Major dependencies

**What to Exclude:**
- Unrelated features
- Still exclude node_modules
- Historical migrations
- Documentation (unless updating)

**Examples:**
- Add new feature spanning frontend and backend
- Refactor authentication across app
- Add payment integration
- Major state management changes

## Execution Patterns

### Pattern 1: Add UI Component (Minimal Context)

```yaml
Context Level: Minimal
Estimated Tokens: 800-1,200
Estimated Time: 5-10 minutes

Steps:
  1. Analyze Request:
     - Component name and purpose
     - Where it will be used
     - Required props

  2. Load Context:
     files:
       - src/components/[SimilarComponent].tsx (for patterns)
       - src/theme/index.ts (design system)
       - src/types/component.ts (prop types)
     tokens: ~500

  3. Implement Component:
     agent: frontend-engineer
     context_expansion:
       - src/hooks/[customHook].ts (if needed)
       - src/utils/[utility].ts (if needed)
     tokens: ~1,000

  4. Integrate Component:
     context_diff:
       add: src/components/[Parent].tsx
       remove: [SimilarComponent].tsx
     tokens: ~900

  5. Test Component:
     agent: quality-assurance
     mcp_tool: playwright
     context: Component file + types only
     tokens: ~400

Success Criteria:
  - Component renders correctly
  - Props work as expected
  - Styling matches design system
  - Playwright tests pass
  - Total tokens: <1,500
```

### Pattern 2: Add API Endpoint (Module Context)

```yaml
Context Level: Module
Estimated Tokens: 1,500-2,000
Estimated Time: 10-15 minutes

Steps:
  1. Analyze Request:
     - HTTP method and path
     - Request/response types
     - Authentication required?
     - Validation required?

  2. Load Context:
     files:
       - server/routes/[module].ts
       - server/types/api.ts
       - SPECIFICATION.md (if specified)
     tokens: ~600

  3. Design Endpoint:
     agent: api-designer
     action: Define endpoint signature
     no_additional_context: true

  4. Implement Backend:
     agent: backend-engineer
     context_expansion:
       - server/controllers/[endpoint].ts (new)
       - server/db/models/[resource].ts
       - server/middleware/auth.ts
       - server/types/[resource].ts
     tokens: ~1,800

  5. Test Endpoint:
     agent: quality-assurance
     context:
       - server/routes/[module].test.ts
       - tests/fixtures/[resource].json
     tokens: ~2,000

Success Criteria:
  - Endpoint responds correctly
  - Authentication works
  - Validation works
  - Tests pass
  - Total tokens: <2,500
```

### Pattern 3: Fix Bug (Minimal Context)

```yaml
Context Level: Minimal
Estimated Tokens: 500-1,000
Estimated Time: 3-8 minutes

Steps:
  1. Analyze Error:
     - Error message
     - Stack trace
     - File and line number
     - Reproduction steps

  2. Load Trace Context:
     files:
       - [file-with-error].ts/tsx
       - [files-in-stack-trace].ts/tsx
     tokens: ~300

  3. Investigate:
     agent: backend-engineer OR frontend-engineer
     context_expansion:
       - src/types/[types].ts (if type error)
       - [dependency-files].ts (if needed)
     tokens: ~700

  4. Implement Fix:
     action: Modify buggy code
     no_additional_context: true

  5. Validate Fix:
     agent: quality-assurance
     context:
       - [fixed-file].test.ts
     tokens: ~900

Success Criteria:
  - Error no longer occurs
  - Root cause addressed
  - Regression test added
  - Existing tests pass
  - Total tokens: <1,200
```

### Pattern 4: Add Feature (Module to Full Context)

```yaml
Context Level: Module → Full
Estimated Tokens: 2,500-3,500
Estimated Time: 45-90 minutes

Steps:
  1. Analyze Feature:
     - Feature scope (UI, API, full-stack)
     - Affected modules
     - Integration points
     - Dependencies

  2. Load Discovery Context:
     files:
       - SPECIFICATION.md
       - src/[module]/index.ts
       - server/routes/[module].ts
     tokens: ~600

  3. Plan Implementation:
     agent: orchestrator OR product-analyst
     action: Break into sub-tasks
     context: Same as discovery

  4. Implement Backend:
     agent: backend-engineer
     context_expansion:
       - server/controllers/**
       - server/db/models/**
       - server/types/**
       - server/middleware/**
     tokens: ~2,200

  5. Implement Frontend:
     agent: frontend-engineer
     context_diff:
       remove: server files
       add: frontend files
         - src/components/[Feature]/**
         - src/hooks/**
         - src/api/**
     tokens: ~2,800

  6. Integrate:
     agent: frontend-engineer
     context_diff:
       remove: implementation files
       add:
         - src/pages/[Page].tsx
         - src/routes.tsx
     tokens: ~2,400

  7. Test:
     agent: quality-assurance
     mcp_tool: playwright
     context: Feature files only
     tokens: ~1,800

Success Criteria:
  - Backend API working
  - Frontend UI working
  - Integration complete
  - All tests passing
  - Total peak tokens: <3,500
```

### Pattern 5: Refactor Module (Module Context)

```yaml
Context Level: Module
Estimated Tokens: 2,000-4,000
Estimated Time: 30-60 minutes

Steps:
  1. Analyze Refactor:
     - Target code to refactor
     - All usages of target code
     - Impact on other modules

  2. Load Module Context:
     files:
       - [module]/**/*.ts (all files in module)
       - src/types/[module].ts
     tokens: ~1,500

  3. Find All Usages:
     tool: grep or search
     action: Find references
     no_additional_files: true

  4. Refactor Incrementally:
     agent: relevant engineer
     process:
       - Refactor one file at a time
       - Use context diff between files
       - Load next file, unload previous
     tokens: ~600 per file (not cumulative)

  5. Update Types:
     context:
       - Type definition files only
     tokens: ~800

  6. Update Tests:
     context:
       - Test files for module
     tokens: ~1,200

Success Criteria:
  - All files refactored
  - Types consistent
  - Tests updated and passing
  - No broken references
  - Token usage managed with context diff
```

## Execution Rules

### Rule 1: Always Start Minimal
Begin with the smallest context that could work. Expand only when blocked.

**Example:**
```
Task: Add logout button
Initial: Load Navigation.tsx only (400 tokens)
Blocked: Need logout function
Expand: Add useAuth.ts (600 tokens total)
Complete: Don't load more
```

### Rule 2: Use Context Diff for Multi-Step Tasks
Don't accumulate context. Unload completed files, load new files.

**Example:**
```
Step 1: Backend (load server files) - 1,800 tokens
Step 2: Frontend (unload server, load src files) - 1,600 tokens
Step 3: Testing (unload implementation, load tests) - 1,000 tokens
Peak: 1,800 tokens (not 4,400 cumulative)
```

### Rule 3: Respect Module Boundaries
Don't cross module boundaries unless task requires it.

**Example:**
```
Task: Fix auth bug
Load: src/auth/** only
Don't Load: src/posts/**, src/comments/** (different modules)
```

### Rule 4: Load Tests Last
Implement first, test later. Don't load tests during implementation.

**Example:**
```
Phase 1-3: Implementation (no test files)
Phase 4: Load test files for validation
```

### Rule 5: Cache Common Patterns
Check cache before loading context.

**Example:**
```
Task: Add button component
Check: /context/cache/add-ui-component.json
Hit: Reuse cached context
Miss: Build new context, cache it
```

### Rule 6: Track Token Usage
Monitor tokens used, warn if exceeding budget.

**Budgets:**
- Minimal: <1,200 tokens
- Module: <3,500 tokens
- Full: <10,000 tokens

**Action:**
- Green (<budget): Continue
- Yellow (near budget): Review if all files needed
- Red (>budget): Stop, optimize context

### Rule 7: Use MCP Tools Efficiently
For Playwright tests, load only test target + types.

**Example:**
```
Test: Button component
Load: Button.tsx + button.types.ts (~400 tokens)
Don't Load: Entire app, other components
```

### Rule 8: Measure and Learn
Track metrics for every execution to improve patterns.

**Metrics:**
- Tokens used
- Time taken
- Success/failure
- Context expansions
- Cache hits/misses

## Agent-Specific Execution Guides

### Orchestrator Agent
```yaml
Role: Coordinates other agents
Context Need: Minimal
Typical Load:
  - SPECIFICATION.md
  - Agent definitions
  - Task breakdown
Tokens: 500-1,000
Execution:
  - Analyzes requirement
  - Breaks into sub-tasks
  - Assigns to specialized agents
  - Monitors progress
```

### Backend Engineer
```yaml
Role: Implements backend logic
Context Need: Module
Typical Load:
  - server/routes/[module].ts
  - server/controllers/**
  - server/db/models/**
  - server/types/**
  - server/middleware/**
Tokens: 1,500-2,500
Execution:
  - Implements API endpoints
  - Database operations
  - Business logic
  - Error handling
```

### Frontend Engineer
```yaml
Role: Implements UI components
Context Need: Minimal to Module
Typical Load:
  - src/components/**
  - src/hooks/**
  - src/types/**
  - src/theme/**
Tokens: 800-2,000
Execution:
  - Creates React components
  - Implements interactivity
  - Applies styling
  - State management
```

### Quality Assurance
```yaml
Role: Tests code quality and functionality
Context Need: Minimal (for tests)
Typical Load:
  - Target component/feature only
  - Type definitions
  - Test fixtures
Tokens: 400-1,500
MCP Tools: Playwright
Execution:
  - Unit tests
  - Integration tests
  - E2E tests with Playwright
  - Security validation
```

### Database Architect
```yaml
Role: Designs database schemas
Context Need: Module
Typical Load:
  - server/db/models/**
  - prisma/schema.prisma
  - SPECIFICATION.md
  - server/types/**
Tokens: 1,000-2,000
Execution:
  - Schema design
  - Migrations
  - Database initialization
  - Seed data
```

### API Designer
```yaml
Role: Designs API contracts
Context Need: Minimal
Typical Load:
  - server/types/api.ts
  - SPECIFICATION.md
  - Existing routes (for patterns)
Tokens: 500-1,000
Execution:
  - API specification
  - Request/response types
  - Documentation
  - Validation rules
```

### Premium UI Designer
```yaml
Role: Enhances UI polish
Context Need: Minimal
Typical Load:
  - Target components
  - src/theme/**
  - Design system
Tokens: 600-1,200
MCP Tools: Playwright (for visual validation)
Execution:
  - Visual enhancements
  - Animation/transitions
  - Responsive design
  - Accessibility improvements
```

### DevOps Engineer
```yaml
Role: Manages deployment and environment
Context Need: Minimal
Typical Load:
  - Configuration files
  - Environment templates
  - Build scripts
Tokens: 400-800
Execution:
  - Environment setup
  - Build configuration
  - Deployment scripts
  - CI/CD pipelines
```

## Example Execution Flows

### Flow 1: Simple Task - Add Logout Button

```
1. User Request: "Add a logout button to the navigation"

2. Orchestrator Analysis:
   - Task Type: UI Component Addition
   - Complexity: Simple
   - Context Level: Minimal
   - Agent: Frontend Engineer

3. Context Manager Query:
   Pattern: add-ui-component
   Response:
     - src/components/Navigation.tsx
     - src/hooks/useAuth.ts
     - src/types/component.ts
   Tokens: ~800

4. Frontend Engineer Execution:
   - Load context (800 tokens)
   - Add LogoutButton to Navigation
   - Use useAuth.logout() function
   - Apply styling from theme
   - Complete in 5 minutes

5. Quality Assurance Validation:
   - Load: Navigation.tsx, component.types.ts
   - Playwright: Click logout button
   - Assert: User logged out, redirected
   - Tokens: ~400
   - Complete in 2 minutes

Total Time: 7 minutes
Total Tokens (peak): 800
Success: ✅
```

### Flow 2: Medium Task - Add Comments Feature

```
1. User Request: "Add comments feature to posts"

2. Orchestrator Analysis:
   - Task Type: Feature Addition
   - Complexity: Medium (full-stack)
   - Context Level: Module
   - Agents: Backend Engineer, Frontend Engineer, QA

3. Sub-Tasks:
   - Backend: API endpoints
   - Frontend: UI components
   - Integration: Connect UI to API
   - Testing: E2E workflow

4. Backend Engineer Execution:
   Context Load:
     - server/routes/posts.ts
     - server/controllers/comments.ts (new)
     - server/db/models/comment.ts (new)
     - server/types/comment.ts (new)
     - server/middleware/auth.ts
   Tokens: ~1,800
   Time: 15 minutes
   Output: API endpoints working

5. Frontend Engineer Execution:
   Context Diff:
     Unload: server files
     Load:
       - src/components/CommentList.tsx (new)
       - src/components/CommentForm.tsx (new)
       - src/components/Comment.tsx (new)
       - src/hooks/useComments.ts (new)
       - src/api/comments.ts (new)
   Tokens: ~1,600
   Time: 20 minutes
   Output: UI components working

6. Frontend Engineer Integration:
   Context Diff:
     Unload: CommentForm.tsx, useComments.ts
     Load: src/components/PostDetail.tsx
   Tokens: ~1,200
   Time: 10 minutes
   Output: Comments integrated into posts

7. Quality Assurance Testing:
   Context Load:
     - CommentList.tsx, CommentForm.tsx, Comment.tsx
     - Types
   MCP Tool: Playwright
   Tests:
     - Display comments
     - Add comment
     - Delete comment
   Tokens: ~1,600
   Time: 8 minutes
   Result: All tests passing ✅

Total Time: 53 minutes
Peak Tokens: 1,800
Total Savings: 96% vs full context
Success: ✅
```

### Flow 3: Complex Task - Authentication Refactor

```
1. User Request: "Refactor authentication to use JWT tokens"

2. Orchestrator Analysis:
   - Task Type: Refactoring
   - Complexity: Complex (cross-module)
   - Context Level: Module → Full
   - Agents: Backend Engineer, Frontend Engineer, QA

3. Backend Engineer - Phase 1: JWT Implementation
   Context Load:
     - server/middleware/auth.ts
     - server/utils/jwt.ts (new)
     - server/types/auth.ts
     - server/routes/auth.ts
     - .env (add JWT_SECRET)
   Tokens: ~1,800
   Time: 25 minutes

4. Backend Engineer - Phase 2: Update Routes
   Context Diff:
     Keep: auth.ts, jwt.ts
     Load: server/routes/*.ts (all routes using auth)
   Tokens: ~2,400
   Time: 15 minutes

5. Frontend Engineer - Update Auth Hook
   Context Diff:
     Unload: server files
     Load:
       - src/hooks/useAuth.ts
       - src/api/auth.ts
       - src/types/auth.ts
       - localStorage utilities
   Tokens: ~1,200
   Time: 15 minutes

6. Quality Assurance - Full Auth Testing
   Context Load:
     - Auth components
     - Protected routes
   MCP Tool: Playwright
   Tests:
     - Login with JWT
     - Protected route access
     - Token refresh
     - Logout
   Tokens: ~1,800
   Time: 15 minutes

Total Time: 70 minutes
Peak Tokens: 2,400
Success: ✅
```

## Optimization Strategies

### Strategy 1: Parallel Agent Execution
Run independent sub-tasks in parallel:

```
Feature: Add user profile page

Parallel Execution:
  Agent 1: Backend Engineer (API endpoint) - 15 min
  Agent 2: Frontend Engineer (Profile component) - 15 min
  (Both run simultaneously)

Sequential Execution:
  Agent 1 → Agent 2 - 30 min

Time Saved: 50%
```

### Strategy 2: Context Caching
Cache frequently used context patterns:

```
First Time: Add button component - Load context (800 tokens, 10 sec)
Second Time: Add another button - Cache hit (800 tokens, 1 sec)
Time Saved: 90%
```

### Strategy 3: Incremental Loading
Load context incrementally as needed:

```
Load 1: Target file only - 300 tokens
  ↓ [Need dependency]
Load 2: +1 dependency file - 500 tokens
  ↓ [Need type]
Load 3: +1 type file - 600 tokens
  ↓ [Complete]

vs Loading All: 2,000 tokens upfront
Savings: 70%
```

### Strategy 4: Context Pruning
Unload files as soon as they're no longer needed:

```
Step 1: Backend implementation (Load: 1,800 tokens)
Step 2: Backend complete (Unload: 1,800, Load frontend: 1,600)
Step 3: Frontend complete (Unload: 1,600, Load tests: 800)

Peak: 1,800 tokens (not 4,200 cumulative)
Savings: 57%
```

## Success Metrics

### Target Metrics by Task Type

| Task Type | Target Tokens | Target Time | Cache Hit % |
|-----------|---------------|-------------|-------------|
| Simple bug fix | <1,000 | <10 min | 50%+ |
| Add component | <1,200 | <15 min | 60%+ |
| Add API endpoint | <2,000 | <20 min | 40%+ |
| Add feature | <3,500 | <90 min | 30%+ |
| Refactoring | <4,000 | <90 min | 20%+ |

### Overall Goals
- **Token Savings**: >90% vs full context
- **Execution Speed**: 2-3x faster with focused context
- **Cache Hit Rate**: >50% for common tasks
- **Success Rate**: >95% task completion

## Troubleshooting

### Issue: Context Too Large
**Symptom**: Token usage >5,000 for simple task
**Solution**:
1. Review loaded files
2. Identify unrelated files
3. Unload unnecessary files
4. Consider breaking into sub-tasks

### Issue: Missing Dependencies
**Symptom**: Error due to undefined import
**Solution**:
1. Expand context incrementally
2. Load only the specific dependency
3. Don't load entire module

### Issue: Cache Misses
**Symptom**: Low cache hit rate <30%
**Solution**:
1. Review task patterns
2. Create new cache entries
3. Update cache templates

### Issue: Slow Execution
**Symptom**: Tasks taking 2x longer than expected
**Solution**:
1. Check if loading too much context
2. Consider parallel agent execution
3. Use context diff more aggressively

## Related Documents

- **smart-context-prompt.md**: Instructions for context-aware execution
- **context-manager.md**: Context registry and management
- **workflow-optimizer.md**: Workflow patterns and optimization
- **mcp-coordinator.md**: MCP tool integration
- **context-templates/**: Specific templates for common tasks
