# Context Template: Fix Bug

## Use Case
Fixing bugs with trace-focused context loading - load only files in the error stack trace and immediate dependencies.

## Context Level
**Minimal** (500-1,000 tokens)

## Required Context

### Phase 1: Error Trace Analysis (Load First)
```
[file-where-error-occurs].ts/tsx          # Primary file with the error
[error-stack-trace-files].ts/tsx          # Files in the stack trace
```
**Tokens**: ~300

### Phase 2: Root Cause Investigation (Load Next)
```
src/types/[relevant-types].ts              # Type definitions
[dependency-files].ts/tsx                  # Immediate dependencies of error file
[utility-files].ts                         # Utility functions called by error file
```
**Tokens**: ~500 (cumulative ~800)

### Phase 3: Fix Validation (Load Last)
```
[corresponding-test-file].test.ts/tsx      # Test file for the fixed code
tests/fixtures/[test-data].json            # Test data if needed
```
**Tokens**: ~300 (cumulative ~1,100)

## Context to Exclude

### Always Exclude
```
Unrelated modules/**                # Modules not in error trace
Unrelated components/**             # Components not in error trace
node_modules/**                     # Dependencies
dist/**                             # Build output
*.log                               # Logs (error message is provided separately)
coverage/**                         # Test coverage
build/**                            # Build artifacts
```

### Usually Exclude (load only if in trace)
```
Configuration files                 # Unless config is causing the issue
Database files                      # Unless DB query is failing
API route files                     # Unless API call is failing
Migration files                     # Unless migration broke something
```

## Execution Steps

### Step 1: Analyze Error Information
1. Read error message carefully
2. Identify error type (TypeError, ReferenceError, etc.)
3. Extract stack trace
4. Identify primary file where error occurs
5. Identify line number if available
6. Understand what operation was being attempted

### Step 2: Load Trace Context
1. Load the file where error occurs
2. Load files in stack trace (top 2-3 levels)
3. **Stop here** - don't load anything else yet
4. Examine the error location

### Step 3: Investigate Root Cause
1. Examine code around error line
2. Identify what's null/undefined/missing
3. Trace back to where variable is set
4. Load dependency files only if needed
5. Load type definitions if type error

### Step 4: Implement Fix
1. Modify the buggy code
2. Add null checks if needed
3. Add error handling if needed
4. Fix type issues if needed
5. Add defensive programming

### Step 5: Validate Fix
1. Load test file
2. Write regression test (if none exists)
3. Run existing tests
4. Manually test the scenario
5. Verify fix doesn't break other functionality

## Example: Fix "Cannot read property 'avatar' of undefined"

### Error Information
```
Error: TypeError: Cannot read property 'avatar' of undefined
Location: src/components/Avatar.tsx:15
Stack Trace:
  at Avatar (src/components/Avatar.tsx:15:28)
  at UserProfile (src/components/UserProfile.tsx:42:10)
  at renderWithHooks (react-dom)
```

### Context Loading Sequence

```yaml
Phase 1 - Error Trace (Initial Load):
  files:
    - src/components/Avatar.tsx (error location)
    - src/components/UserProfile.tsx (in stack trace)
  tokens: ~350
  action: Identify that user object is undefined

Phase 2 - Root Cause (Context Expansion):
  context_diff:
    add:
      - src/hooks/useUser.ts (provides user data)
      - src/types/user.ts (user type definition)
  tokens: ~700 total
  action: Find that user can be null but Avatar doesn't handle it

Phase 3 - Fix Implementation:
  context_diff:
    # No new files needed
  tokens: ~700 total
  action: Add null check and fallback in Avatar component

Phase 4 - Validation (Test Context):
  context_diff:
    add:
      - src/components/Avatar.test.tsx
    remove:
      - src/components/UserProfile.tsx (no longer needed)
  tokens: ~800 total
  action: Write test for null user case, verify fix
```

### Fix Code Pattern

```typescript
// Before (buggy code)
export const Avatar: React.FC<AvatarProps> = ({ user }) => {
  return (
    <img src={user.avatar} alt={user.name} />
  );
};

// After (fixed code)
export const Avatar: React.FC<AvatarProps> = ({ user }) => {
  const avatarUrl = user?.avatar || '/default-avatar.png';
  const userName = user?.name || 'User';

  return (
    <img src={avatarUrl} alt={userName} />
  );
};
```

## Optimization Tips

### Tip 1: Follow the Stack Trace
Only load files that appear in the error stack trace. Don't load "related" files unless they're in the trace.

### Tip 2: Start at the Error Location
Load the file with the error first, examine the code, then expand outward only if needed.

### Tip 3: Load Types Early
Type definition files are small and help understand what should/shouldn't be null or undefined.

### Tip 4: Skip Tests Until Fix is Ready
Don't load test files during investigation. Load them only when implementing the fix.

### Tip 5: Use Console Logs Strategically
If error isn't clear, add console.logs to trace values. Don't load more context unnecessarily.

## Bug Categories and Context Strategies

### Type 1: Null/Undefined Errors
```yaml
Load:
  - Error file
  - Type definitions
  - Source of null value (trace back)
Skip:
  - Test files (until fix ready)
  - Unrelated components
Tokens: ~500-700
```

### Type 2: Type Errors
```yaml
Load:
  - Error file
  - Type definition files
  - Interface definitions
Skip:
  - Implementation details of other modules
  - Test files
Tokens: ~400-600
```

### Type 3: Logic Errors
```yaml
Load:
  - Error file
  - Related business logic files
  - Type definitions
  - Utility functions used
Skip:
  - Other features
  - Configuration files
Tokens: ~600-900
```

### Type 4: API/Network Errors
```yaml
Load:
  - API client file
  - API route file (if backend error)
  - Error response types
  - Request/response types
Skip:
  - UI components (unless debugging UI error handling)
  - Database files (unless DB query issue)
Tokens: ~700-1,000
```

### Type 5: State Management Errors
```yaml
Load:
  - Component with error
  - State management hook/store
  - State type definitions
Skip:
  - Other components
  - API files
Tokens: ~500-800
```

## Validation Checklist

Before considering the bug fixed:

- [ ] Error reproduced and understood
- [ ] Root cause identified
- [ ] Fix implemented
- [ ] Null/undefined checks added (if applicable)
- [ ] Error handling added (if applicable)
- [ ] Type issues resolved (if applicable)
- [ ] Regression test added
- [ ] Existing tests still pass
- [ ] Manual testing confirms fix
- [ ] No new errors introduced
- [ ] Context used: <1,200 tokens
- [ ] Token savings: >97% vs full codebase

## Common Pitfalls to Avoid

### Pitfall 1: Loading Entire Module
Don't load all files in a module when only one has the error.

❌ **Bad**: Load `src/components/**/*.tsx`
✅ **Good**: Load `src/components/Avatar.tsx` only

### Pitfall 2: Loading Test Files Too Early
Tests are for validation after the fix, not during investigation.

❌ **Bad**: Load test files in Phase 1
✅ **Good**: Load test files in Phase 4

### Pitfall 3: Loading Unrelated Features
Just because files are in the same directory doesn't mean they're related to the bug.

❌ **Bad**: Load `src/components/Dashboard/*` when bug is in `Avatar.tsx`
✅ **Good**: Load only files in the error trace

### Pitfall 4: Loading Configuration Files
Config is rarely the cause of runtime errors.

❌ **Bad**: Load `vite.config.ts`, `tsconfig.json`, etc.
✅ **Good**: Load only if error is in build/compile time

### Pitfall 5: Over-Investigation
Don't analyze the entire architecture. Focus on the error trace.

❌ **Bad**: Understand entire state management system
✅ **Good**: Understand why this specific value is null/undefined

## Token Budget

### Target Budget: 500-1,000 tokens

| Phase | Files | Tokens | Cumulative |
|-------|-------|--------|------------|
| Error Trace | 2-3 | 300 | 300 |
| Investigation | +2-3 | 400 | 700 |
| Fix | 0 | 0 | 700 |
| Validation | +1-2 | 300 | 1,000 |
| **Total** | **3-6** | **1,000** | - |

### Token Breakdown by Bug Type

| Bug Type | Avg Files | Avg Tokens | Example |
|----------|-----------|------------|---------|
| Null/Undefined | 2-4 | 500-700 | Avatar error |
| Type Error | 2-3 | 400-600 | Interface mismatch |
| Logic Error | 3-5 | 600-900 | Wrong calculation |
| API Error | 3-6 | 700-1,000 | Failed request |
| State Error | 3-5 | 500-800 | Invalid state |

## Debugging Workflow

### Step-by-Step Debug Process

```
1. Read Error
   ↓
2. Extract Stack Trace
   ↓
3. Load Error File (file at top of trace)
   ↓ [Stop and examine]
4. Is root cause clear?
   ├─ Yes → Implement fix
   └─ No → Load next file in trace
   ↓
5. Load dependency files (one at a time)
   ↓ [Stop and examine after each]
6. Root cause found?
   ├─ Yes → Implement fix
   └─ No → Load one more dependency
   ↓
7. Implement Fix
   ↓
8. Load test file
   ↓
9. Add regression test
   ↓
10. Validate fix
```

## Success Metrics

### Expected Outcomes
- **Token usage**: 500-1,000 tokens
- **Token savings**: >98% compared to full context
- **Files loaded**: 3-6 files
- **Debug time**: 3-8 minutes
- **Context expansions**: 1-2 phases
- **Success rate**: >95%

### Comparison to Full Context

| Metric | With Template | Without Template | Improvement |
|--------|---------------|------------------|-------------|
| Tokens | ~750 | ~50,000 | **98.5% reduction** |
| Files | ~4 | ~200 | **98% reduction** |
| Time | ~5 min | ~20 min | **75% faster** |
| Focus | Very High | Low | Clear improvement |

## Error Type Examples

### Example 1: TypeError
```
Error: Cannot read property 'avatar' of undefined
Files: Avatar.tsx, useUser.ts, user.types.ts
Tokens: ~600
Fix: Add null check and default value
```

### Example 2: ReferenceError
```
Error: 'handleSubmit' is not defined
Files: LoginForm.tsx
Tokens: ~250
Fix: Import or define handleSubmit function
```

### Example 3: Validation Error
```
Error: Email format invalid
Files: validation.ts, RegistrationForm.tsx, form.types.ts
Tokens: ~700
Fix: Update email regex pattern
```

### Example 4: API Error
```
Error: 404 Not Found - /api/posts/123/comments
Files: api/client.ts, api/posts.ts (server), posts/routes.ts
Tokens: ~900
Fix: Add missing route or fix route path
```

### Example 5: State Error
```
Error: Cannot update state on unmounted component
Files: UserProfile.tsx, useUser.ts
Tokens: ~500
Fix: Add cleanup in useEffect
```

## Related Templates

- **add-ui-component.md** - For adding new components after fixing bugs
- **add-api-endpoint.md** - For adding missing API endpoints causing errors
- **add-feature.md** - For implementing missing features
- **run-tests.md** - For comprehensive regression testing after fixes
