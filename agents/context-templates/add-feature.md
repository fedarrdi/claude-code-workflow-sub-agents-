# Context Template: Add Feature

## Use Case
Adding a new feature that spans multiple components or modules with module-level context loading.

## Context Level
**Module** (2,000-3,500 tokens)

## Required Context

### Phase 1: Feature Discovery (Load First)
```
SPECIFICATION.md                          # Feature requirements (if specified)
src/[module]/index.ts                     # Module entry point
server/routes/[module].ts                 # API routes (if backend feature)
src/types/[module].ts                     # Module type definitions
```
**Tokens**: ~600

### Phase 2: Implementation Planning (Load Next)
```
src/components/[related-components].tsx   # Components to modify/create
server/controllers/[module].ts            # Backend controllers (if needed)
server/db/models/[resource].ts            # Database models (if needed)
src/hooks/[custom-hooks].ts               # Hooks to create/modify
src/utils/[utilities].ts                  # Utility functions needed
```
**Tokens**: ~1,800 (cumulative ~2,400)

### Phase 3: Integration (Load Next)
```
src/pages/[affected-pages].tsx            # Pages using the feature
src/routes.tsx                            # Route configuration (if new pages)
server/middleware/[middleware].ts         # Middleware (if needed)
```
**Tokens**: ~1,000 (cumulative ~3,400)

### Phase 4: Testing (Load Last)
```
src/components/[component].test.tsx       # Component tests
server/routes/[module].test.ts            # API tests (if backend)
tests/fixtures/[test-data].json           # Test data
```
**Tokens**: ~800 (cumulative ~4,200)

## Context to Exclude

### Always Exclude
```
Unrelated modules/**                # Other feature modules
node_modules/**                     # Dependencies
dist/**                             # Build output
*.log                               # Logs
coverage/**                         # Test coverage
.git/**                             # Git history
migrations/**                       # Old database migrations (unless modifying)
```

### Usually Exclude (load only if integrating)
```
src/App.tsx                         # Main app (unless modifying app-level state)
Configuration files                 # Unless adding configuration
Third-party integrations            # Unless feature uses them
Admin features                      # Unless feature is admin-related
```

## Execution Steps

### Step 1: Analyze Feature Requirements
1. Read feature specification or request
2. Identify feature scope (UI only, API only, full-stack)
3. Determine affected modules
4. List components to create/modify
5. List API endpoints to create/modify
6. Identify database changes needed
7. Estimate complexity (small, medium, large)

### Step 2: Load Discovery Context
1. Load SPECIFICATION.md (if exists)
2. Load module entry point
3. Load existing module files to understand patterns
4. **Stop here** - plan before loading more

### Step 3: Plan Implementation
1. Break feature into sub-tasks
2. Identify dependencies between sub-tasks
3. Determine implementation order
4. List files to create
5. List files to modify

### Step 4: Implement Incrementally
1. Load context for first sub-task
2. Implement sub-task
3. Unload unnecessary files
4. Load context for next sub-task
5. Repeat until feature complete

### Step 5: Integrate Feature
1. Load integration points (pages, routes)
2. Connect feature to existing app
3. Add navigation if needed
4. Add permissions if needed

### Step 6: Test Feature
1. Load test files
2. Write unit tests
3. Write integration tests
4. Use Playwright for E2E tests
5. Validate all scenarios

## Example: Add "Post Comments" Feature

### Feature Scope
- Users can view comments on posts
- Users can add comments to posts
- Users can delete their own comments
- Real-time comment count display
- Backend API for CRUD operations

### Context Loading Sequence

```yaml
Phase 1 - Discovery (Initial Load):
  files:
    - SPECIFICATION.md (feature requirements)
    - src/components/Post.tsx (existing post component)
    - server/routes/posts.ts (existing post API)
    - src/types/post.ts (post types)
  tokens: ~700
  action: Understand existing post system

Phase 2 - Backend Implementation (Context Expansion):
  context_diff:
    add:
      - server/controllers/comments.ts (new)
      - server/db/models/comment.ts (new)
      - server/types/comment.ts (new)
      - server/middleware/auth.ts (for ownership check)
    remove:
      - SPECIFICATION.md (no longer needed)
  tokens: ~2,200 total
  action: Implement comment API endpoints

Phase 3 - Frontend Implementation (Context Diff):
  context_diff:
    add:
      - src/components/CommentList.tsx (new)
      - src/components/CommentForm.tsx (new)
      - src/components/Comment.tsx (new)
      - src/hooks/useComments.ts (new)
      - src/api/comments.ts (new)
    remove:
      - server/controllers/comments.ts (backend complete)
      - server/db/models/comment.ts
      - server/middleware/auth.ts
  tokens: ~2,800 total
  action: Implement comment UI components

Phase 4 - Integration (Context Diff):
  context_diff:
    add:
      - src/components/PostDetail.tsx (integrate comments)
    remove:
      - src/components/CommentForm.tsx (implementation complete)
      - src/hooks/useComments.ts (implementation complete)
  tokens: ~2,400 total
  action: Integrate comments into post detail view

Phase 5 - Testing (Test Context):
  context_diff:
    add:
      - server/routes/comments.test.ts
      - src/components/CommentList.test.tsx
      - tests/fixtures/comments.json
    remove:
      - src/components/PostDetail.tsx
      - src/components/Post.tsx
    keep:
      - src/types/comment.ts
  tokens: ~1,800 total
  action: Write and run tests
```

## Implementation Patterns

### Pattern 1: UI-Only Feature
```yaml
context:
  - Frontend components only
  - Hooks and utilities
  - Types
  - Design system
tokens: 1,500-2,500
example: Add dark mode toggle
```

### Pattern 2: API-Only Feature
```yaml
context:
  - Backend routes and controllers
  - Database models
  - Middleware
  - Types
tokens: 1,800-2,800
example: Add rate limiting
```

### Pattern 3: Full-Stack Feature (Most Common)
```yaml
context:
  - Backend API (routes, controllers, models)
  - Frontend components
  - Hooks and utilities
  - Types (shared)
tokens: 2,500-3,500
example: Add comments feature
```

### Pattern 4: Integration Feature
```yaml
context:
  - Integration module
  - API client
  - Configuration
  - Types
tokens: 1,200-2,000
example: Add third-party analytics
```

## Optimization Tips

### Tip 1: Implement Backend First
For full-stack features:
1. Implement and test backend API first
2. Unload backend context
3. Load frontend context
4. Implement frontend using API
This keeps context focused and manageable.

### Tip 2: Use Context Diff Aggressively
After each sub-task:
- Unload files no longer being modified
- Keep type definitions loaded
- Load only files for next sub-task

### Tip 3: Break Large Features into Phases
If feature is >4,000 tokens:
1. Implement core functionality first
2. Test and validate
3. Add enhancements in separate phase
4. Test again

### Tip 4: Cache Common Patterns
If implementing similar features:
- Check context cache for pattern
- Reuse cached context structure
- Adapt to specific feature needs

### Tip 5: Test Incrementally
Don't wait until feature is complete:
- Test backend API as soon as implemented
- Test frontend components as created
- Run integration tests at the end

## Validation Checklist

Before considering feature complete:

- [ ] All sub-tasks implemented
- [ ] Backend API endpoints working
- [ ] Frontend components implemented
- [ ] Integration points connected
- [ ] Database changes applied (if any)
- [ ] Authentication/authorization added (if needed)
- [ ] Error handling implemented
- [ ] Edge cases handled
- [ ] Unit tests written and passing
- [ ] Integration tests written and passing
- [ ] E2E tests with Playwright passing
- [ ] Manual testing completed
- [ ] Documentation updated (if public API)
- [ ] Context used: <4,000 tokens peak
- [ ] Token savings: >90% vs full codebase

## Common Pitfalls to Avoid

### Pitfall 1: Loading Everything at Once
Don't load frontend and backend context simultaneously.

❌ **Bad**: Load `src/**` and `server/**` at the same time
✅ **Good**: Load backend first, then switch to frontend

### Pitfall 2: Not Using Context Diff
Don't keep accumulating context throughout implementation.

❌ **Bad**: Keep all files loaded from Phase 1 to Phase 5
✅ **Good**: Unload completed files, load new files as needed

### Pitfall 3: Skipping Planning Phase
Don't start implementing without understanding existing patterns.

❌ **Bad**: Start creating files immediately
✅ **Good**: Load existing module files, understand patterns first

### Pitfall 4: Loading Unrelated Features
Don't load other feature modules "for reference."

❌ **Bad**: Load user profile feature when implementing comments
✅ **Good**: Load only comment-related files

### Pitfall 5: Testing Too Late
Don't wait until entire feature is done to test.

❌ **Bad**: Test everything at once at the end
✅ **Good**: Test backend, then test frontend, then test integration

## Token Budget

### Target Budget: 2,000-3,500 tokens

| Phase | Files | Tokens | Cumulative | Context Diff |
|-------|-------|--------|------------|--------------|
| Discovery | 4-5 | 700 | 700 | Initial load |
| Backend | +4-6 | 1,500 | 2,200 | +backend, -specs |
| Frontend | +5-7 | 1,600 | 2,800 | +frontend, -backend |
| Integration | +2-3 | 600 | 2,400 | +pages, -components |
| Testing | +3-4 | 800 | 1,800 | +tests, -implementation |
| **Peak** | **~12-15** | **~2,800** | - | - |

### Token Breakdown by Feature Type

| Feature Type | Avg Files | Avg Tokens | Peak Tokens |
|--------------|-----------|------------|-------------|
| UI-Only | 8-12 | 2,000 | 2,500 |
| API-Only | 6-10 | 2,200 | 2,800 |
| Full-Stack | 12-18 | 2,800 | 3,500 |
| Integration | 5-8 | 1,600 | 2,000 |

## Sub-Task Breakdown Example

### Feature: Add Comments System

#### Sub-Task 1: Backend API (Backend Engineer)
```yaml
context:
  - server/routes/posts.ts
  - server/controllers/comments.ts (new)
  - server/db/models/comment.ts (new)
  - server/types/comment.ts (new)
tokens: ~1,800
time: 15 minutes
```

#### Sub-Task 2: API Client (Frontend Engineer)
```yaml
context:
  - src/api/comments.ts (new)
  - src/types/comment.ts (shared)
  - src/api/client.ts (base API client)
tokens: ~800
time: 5 minutes
```

#### Sub-Task 3: UI Components (Frontend Engineer)
```yaml
context:
  - src/components/CommentList.tsx (new)
  - src/components/CommentForm.tsx (new)
  - src/components/Comment.tsx (new)
  - src/types/component.ts
  - src/theme/index.ts
tokens: ~1,500
time: 20 minutes
```

#### Sub-Task 4: State Management (Frontend Engineer)
```yaml
context:
  - src/hooks/useComments.ts (new)
  - src/api/comments.ts
  - src/types/comment.ts
tokens: ~700
time: 10 minutes
```

#### Sub-Task 5: Integration (Frontend Engineer)
```yaml
context:
  - src/components/Post.tsx
  - src/components/PostDetail.tsx
  - src/components/CommentList.tsx
tokens: ~1,000
time: 10 minutes
```

#### Sub-Task 6: Testing (QA Agent)
```yaml
context_backend:
  - server/routes/comments.test.ts
  - tests/fixtures/comments.json
tokens: ~600
time: 10 minutes

context_frontend:
  - src/components/CommentList.test.tsx
tokens: ~500
time: 10 minutes with Playwright
```

**Total Feature:**
- Time: ~80 minutes
- Peak Tokens: ~2,800
- Savings: 94% vs full context

## Success Metrics

### Expected Outcomes
- **Token usage**: 2,000-3,500 tokens peak
- **Token savings**: >90% compared to full context
- **Files loaded**: 12-18 files peak
- **Implementation time**: 45-90 minutes
- **Context expansions**: 4-5 phases
- **Context diff operations**: 4-5
- **Success rate**: >90%

### Comparison to Full Context

| Metric | With Template | Without Template | Improvement |
|--------|---------------|------------------|-------------|
| Tokens (peak) | ~2,800 | ~50,000 | **94% reduction** |
| Files (peak) | ~15 | ~200 | **92.5% reduction** |
| Time | ~60 min | ~120 min | **50% faster** |
| Focus | High | Low | Clear improvement |

## Related Templates

- **add-api-endpoint.md** - For backend portion of feature
- **add-ui-component.md** - For frontend components
- **fix-bug.md** - For fixing bugs in new features
- **run-tests.md** - For comprehensive feature testing
