# Context Template: Add API Endpoint

## Use Case
Adding a new RESTful API endpoint to an existing module with minimal context loading.

## Context Level
**Module** (1,500-2,000 tokens)

## Required Context

### Phase 1: API Design (Load First)
```
server/routes/[module].ts          # Existing routes for the module
server/types/api.ts                # API request/response types
SPECIFICATION.md                   # If endpoint is spec-defined
```
**Tokens**: ~600

### Phase 2: Implementation (Load Next)
```
server/controllers/[endpoint].ts   # New or existing controller
server/db/models/[resource].ts     # Database model for the resource
server/middleware/auth.ts          # If endpoint requires authentication
server/middleware/validation.ts    # If endpoint requires validation
server/types/[resource].ts         # Resource type definitions
```
**Tokens**: ~1,200 (cumulative ~1,800)

### Phase 3: Testing (Load Last)
```
server/routes/[module].test.ts     # Test file for routes
tests/fixtures/[resource].json     # Test data fixtures
```
**Tokens**: ~400 (cumulative ~2,200)

## Context to Exclude

### Always Exclude
```
src/**                             # Frontend code (unless full-stack task)
server/routes/[other-modules]/**   # Other API modules
server/db/migrations/**            # Database migrations
node_modules/**                    # Dependencies
dist/**                            # Build output
*.log                              # Logs
coverage/**                        # Test coverage
```

### Usually Exclude (load only if needed)
```
server/config.ts                   # Config (unless adding config)
server/utils/**                    # Utilities (load specific ones if needed)
.env                               # Environment variables
server/db/seeds/**                 # Seed data
```

## Execution Steps

### Step 1: Analyze Endpoint Requirements
1. Parse endpoint specification or request
2. Identify HTTP method (GET, POST, PUT, DELETE, PATCH)
3. Identify resource type (users, posts, comments, etc.)
4. Determine if authentication is required
5. Determine if validation is required
6. Identify response format

### Step 2: Load Minimal Context
1. Load existing routes file for the module
2. Load API types file
3. Check SPECIFICATION.md if endpoint is documented
4. **Stop here** - don't load anything else yet

### Step 3: Design Endpoint Signature
1. Define route path (e.g., `/api/posts/:id/comments`)
2. Define request types (params, query, body)
3. Define response type
4. Document in code

### Step 4: Expand Context for Implementation
1. Load or create controller file
2. Load database model for resource
3. Load auth middleware if needed
4. Load validation middleware if needed
5. Load resource type definitions

### Step 5: Implement Endpoint
1. Add route to routes file
2. Implement controller logic
3. Add error handling
4. Add validation
5. Add authentication check
6. Test manually with curl or Postman

### Step 6: Add Tests
1. Load test file
2. Load fixtures
3. Write test cases
4. Run tests
5. Fix any issues

## Example: Add GET /api/posts/:id/comments

### Context Loading Sequence

```yaml
Phase 1 - Design (Initial Load):
  files:
    - server/routes/posts.ts
    - server/types/api.ts
    - SPECIFICATION.md
  tokens: ~600
  action: Review existing patterns, design endpoint signature

Phase 2 - Implementation (Context Expansion):
  context_diff:
    add:
      - server/controllers/comments.ts (new file)
      - server/db/models/comment.ts
      - server/middleware/auth.ts
      - server/types/comment.ts
  tokens: ~1,800 total
  action: Implement controller and add route

Phase 3 - Testing (Final Context):
  context_diff:
    add:
      - server/routes/posts.test.ts
      - tests/fixtures/comments.json
    remove:
      - SPECIFICATION.md (no longer needed)
  tokens: ~2,000 total
  action: Write and run tests
```

### Implementation Code Pattern

```typescript
// In server/routes/posts.ts
import { getCommentsByPostId } from '../controllers/comments.js';
import { authenticateToken } from '../middleware/auth.js';

router.get('/:id/comments', authenticateToken, async (req, res) => {
  try {
    const comments = await getCommentsByPostId(parseInt(req.params.id));
    res.json({ success: true, data: comments });
  } catch (error) {
    res.status(500).json({ success: false, error: error.message });
  }
});
```

## Optimization Tips

### Tip 1: Reuse Existing Patterns
Look at existing endpoints in the same routes file to match patterns:
- Error handling style
- Response format
- Authentication approach
- Validation approach

### Tip 2: Check Context Cache
Before loading context, check for cached patterns:
- `add-api-endpoint-crud.json` - For CRUD operations
- `add-api-endpoint-auth.json` - For authenticated endpoints
- `add-api-endpoint-nested.json` - For nested resource endpoints

### Tip 3: Load Types Early
Load type definition files early (Phase 1) as they're small and helpful for understanding data structures.

### Tip 4: Defer Test Context
Don't load test files until implementation is complete. Tests often need mock data and fixtures that aren't needed during implementation.

### Tip 5: Skip Migrations
Unless you're also modifying the database schema, skip loading migration files.

## Validation Checklist

Before considering the task complete:

- [ ] Endpoint added to routes file
- [ ] Controller logic implemented
- [ ] Request/response types defined
- [ ] Authentication added (if required)
- [ ] Validation added (if required)
- [ ] Error handling implemented
- [ ] Tests written and passing
- [ ] Manual testing with curl/Postman successful
- [ ] Documentation updated (if API is documented)
- [ ] Context used: <2,500 tokens
- [ ] Token savings: >95% vs full codebase

## Common Pitfalls to Avoid

### Pitfall 1: Loading Entire API
Don't load all routes files, only the module you're working on.

❌ **Bad**: Load `server/routes/**/*.ts`
✅ **Good**: Load `server/routes/posts.ts` only

### Pitfall 2: Loading Frontend Code
Unless it's a full-stack task, don't load frontend files.

❌ **Bad**: Load `src/pages/PostDetail.tsx` to understand UI
✅ **Good**: Only load backend API files

### Pitfall 3: Loading All Models
Only load the model(s) directly related to your endpoint.

❌ **Bad**: Load `server/db/models/*.ts`
✅ **Good**: Load `server/db/models/comment.ts` only

### Pitfall 4: Loading Config Too Early
Only load config files if you need to add configuration.

❌ **Bad**: Load `server/config.ts` in Phase 1
✅ **Good**: Load only if adding config (rare)

### Pitfall 5: Loading Migrations
Migrations are historical records, not needed for adding endpoints.

❌ **Bad**: Load `server/db/migrations/*.ts`
✅ **Good**: Skip migrations entirely

## Token Budget

### Target Budget: 1,500-2,000 tokens

| Phase | Files | Tokens | Cumulative |
|-------|-------|--------|------------|
| Design | 3-4 | 600 | 600 |
| Implementation | +3-5 | 1,200 | 1,800 |
| Testing | +2-3 | 400 | 2,200 |
| **Total** | **8-12** | **2,200** | - |

### Token Breakdown by File Type

| File Type | Avg Tokens | Example |
|-----------|------------|---------|
| Routes file | 200-300 | `server/routes/posts.ts` |
| Controller file | 300-400 | `server/controllers/comments.ts` |
| Model file | 200-300 | `server/db/models/comment.ts` |
| Types file | 100-200 | `server/types/comment.ts` |
| Middleware file | 150-250 | `server/middleware/auth.ts` |
| Test file | 200-300 | `server/routes/posts.test.ts` |
| Fixture file | 50-100 | `tests/fixtures/comments.json` |

## Success Metrics

### Expected Outcomes
- **Token usage**: 1,500-2,200 tokens
- **Token savings**: >95% compared to full context
- **Files loaded**: 8-12 files
- **Implementation time**: 10-15 minutes
- **Context expansions**: 2-3 phases
- **Success rate**: >95%

### Comparison to Full Context

| Metric | With Template | Without Template | Improvement |
|--------|---------------|------------------|-------------|
| Tokens | ~2,000 | ~50,000 | **96% reduction** |
| Files | ~10 | ~200 | **95% reduction** |
| Time | ~12 min | ~30 min | **60% faster** |
| Focus | High | Low | Clear improvement |

## Related Templates

- **add-ui-component.md** - For adding frontend components
- **fix-bug.md** - For fixing backend API bugs
- **add-feature.md** - For larger features spanning multiple endpoints
- **run-tests.md** - For comprehensive API testing
