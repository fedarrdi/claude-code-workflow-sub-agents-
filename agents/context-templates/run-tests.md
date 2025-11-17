# Context Template: Run Tests

## Use Case
Running tests with Playwright using only affected components - focused testing with minimal context.

## Context Level
**Minimal to Module** (500-2,000 tokens depending on test scope)

## Required Context

### Phase 1: Test Scope Analysis (Load First)
```
SPECIFICATION.md                      # Feature specifications (if available)
[test-plan-or-description]           # Test requirements
```
**Tokens**: ~200

### Phase 2: Component Test Context (Load for Each Test)
```
src/components/[ComponentName].tsx    # Component being tested
src/types/component.ts                # Component prop types
```
**Tokens**: ~400 per component

### Phase 3: Feature Test Context (Load for Integration Tests)
```
src/components/[Feature]/*.tsx        # All components in feature (3-5 files)
src/hooks/[useFeature].ts             # Feature hooks
src/types/[feature].ts                # Feature types
src/routes.tsx                        # Route configuration (if needed)
```
**Tokens**: ~1,200 per feature

### Phase 4: E2E Test Context (Load for Full Workflows)
```
src/pages/[Page].tsx                  # Pages in workflow
src/components/[Nav].tsx              # Navigation components
src/components/[Feature]/*.tsx        # Feature components used
src/api/[module].ts                   # API client (if testing API calls)
```
**Tokens**: ~1,800 per workflow

## Context to Exclude

### Always Exclude for Component Tests
```
src/pages/**                     # Pages (unless testing page integration)
server/**                        # Backend code (testing UI only)
src/api/**                       # API client (unless testing API integration)
Other components/**              # Unrelated components
node_modules/**                  # Dependencies
dist/**                          # Build output
*.log                            # Logs
coverage/**                      # Old test coverage
tests/**/*.test.ts               # Other test files
```

### Always Exclude for All Tests
```
Configuration files              # Build configs not needed
Database files                   # Backend DB not needed for UI tests
Migration files                  # Historical DB changes
Documentation                    # Docs not needed for testing
.git/**                          # Git history
```

## Execution Steps

### Step 1: Determine Test Type
1. Component unit test (single component isolation)
2. Feature integration test (multiple related components)
3. E2E workflow test (full user journey)
4. Regression test (previously failing scenario)
5. Visual regression test (screenshot comparison)

### Step 2: Load Test-Specific Context
1. For component test: Load component + types only
2. For feature test: Load feature module files
3. For E2E test: Load workflow pages + components
4. **Don't load more than needed**

### Step 3: Initialize Playwright
1. Start browser (or reuse existing session)
2. Navigate to test URL
3. Set viewport size (if testing responsiveness)
4. Clear cookies/storage (if fresh state needed)

### Step 4: Execute Test Scenarios
1. Take initial snapshot (accessibility tree)
2. Perform interactions (clicks, inputs, navigation)
3. Assert expected outcomes
4. Capture screenshots (on failure or for visual validation)
5. Check console for errors

### Step 5: Report Results
1. Collect test results (pass/fail)
2. Capture screenshots/traces on failure
3. Report execution time
4. Document any issues found

## Test Type Context Strategies

### Type 1: Component Unit Test
```yaml
context_level: minimal
test_target: Single component in isolation
load:
  - src/components/[Component].tsx
  - src/types/component.ts
tokens: 400-600
example: Test Button component renders correctly
```

### Type 2: Component Interaction Test
```yaml
context_level: minimal
test_target: Component with user interactions
load:
  - src/components/[Component].tsx
  - src/hooks/[useHook].ts (if component uses it)
  - src/types/component.ts
tokens: 600-900
example: Test LoginForm submits correctly
```

### Type 3: Feature Integration Test
```yaml
context_level: module
test_target: Multiple components working together
load:
  - src/components/[Feature]/*.tsx (3-5 components)
  - src/hooks/[useFeature].ts
  - src/types/[feature].ts
tokens: 1,200-1,800
example: Test comment system (list, form, comment components)
```

### Type 4: E2E Workflow Test
```yaml
context_level: module
test_target: Complete user workflow across pages
load:
  - src/pages/[Pages].tsx (2-4 pages)
  - src/components/[Nav].tsx
  - src/components/[Feature]/*.tsx
  - src/routes.tsx
tokens: 1,800-2,500
example: Test user registration → verification → dashboard flow
```

### Type 5: Visual Regression Test
```yaml
context_level: minimal
test_target: UI appearance and layout
load:
  - src/components/[Component].tsx
  - src/styles/* (if component has custom styles)
tokens: 500-800
example: Test button styles match design system
```

## Example Test Scenarios

### Example 1: Test Login Button

#### Test Requirements
- Button renders with correct text
- Button navigates to /login on click
- Button has correct ARIA label

#### Context Loading
```yaml
Phase 1 - Load Component:
  files:
    - src/components/LoginButton.tsx
    - src/types/component.ts
  tokens: ~350

Phase 2 - Execute Test:
  playwright_commands:
    - browser_navigate to homepage
    - browser_snapshot to see page structure
    - browser_click on LoginButton
    - Assert URL is /login
  no_additional_context_needed: true

Result:
  tokens: ~350
  time: ~1 minute
  status: PASSED
```

### Example 2: Test Registration Form

#### Test Requirements
- Form renders all fields
- Empty field validation works
- Invalid email validation works
- Successful submission redirects to verification page

#### Context Loading
```yaml
Phase 1 - Load Component:
  files:
    - src/components/RegistrationForm.tsx
    - src/utils/validation.ts
    - src/types/form.ts
  tokens: ~800

Phase 2 - Execute Tests:
  test_1_empty_fields:
    - Navigate to /register
    - Click submit button
    - Assert error messages displayed
  test_2_invalid_email:
    - Fill invalid email
    - Click submit button
    - Assert email error displayed
  test_3_successful_submission:
    - Fill valid data
    - Click submit button
    - Assert redirect to /verify-email

Result:
  tokens: ~800
  time: ~3 minutes
  status: PASSED (3/3 tests)
```

### Example 3: Test Comment System (Feature Test)

#### Test Requirements
- Comment list displays comments
- Comment form accepts input
- New comment appears in list
- Delete button removes comment

#### Context Loading
```yaml
Phase 1 - Load Feature:
  files:
    - src/components/CommentList.tsx
    - src/components/CommentForm.tsx
    - src/components/Comment.tsx
    - src/hooks/useComments.ts
    - src/types/comment.ts
  tokens: ~1,600

Phase 2 - Execute Tests:
  test_1_display_comments:
    - Navigate to post with comments
    - Assert comment list visible
    - Assert comments rendered
  test_2_add_comment:
    - Type in comment form
    - Click submit
    - Assert new comment in list
  test_3_delete_comment:
    - Click delete on own comment
    - Assert comment removed from list

Result:
  tokens: ~1,600
  time: ~5 minutes
  status: PASSED (3/3 tests)
```

### Example 4: E2E Registration Flow

#### Test Requirements
- User can register
- User receives verification email (mocked)
- User can verify email
- User redirected to dashboard
- Dashboard shows user info

#### Context Loading
```yaml
Phase 1 - Load Workflow:
  files:
    - src/pages/Register.tsx
    - src/pages/VerifyEmail.tsx
    - src/pages/Dashboard.tsx
    - src/components/RegistrationForm.tsx
    - src/components/Navigation.tsx
    - src/hooks/useAuth.ts
    - src/routes.tsx
  tokens: ~2,200

Phase 2 - Execute E2E Test:
  step_1_register:
    - Navigate to /register
    - Fill registration form
    - Submit form
    - Assert redirect to /verify-email
  step_2_verify:
    - Enter verification code (mocked)
    - Click verify button
    - Assert redirect to /dashboard
  step_3_dashboard:
    - Assert user name displayed
    - Assert navigation shows logged-in state

Result:
  tokens: ~2,200
  time: ~8 minutes
  status: PASSED
```

## Playwright MCP Commands Reference

### Navigation Commands
```
browser_navigate(url)              # Navigate to URL
browser_navigate_back()            # Go back
```

### Inspection Commands
```
browser_snapshot()                 # Capture accessibility tree (preferred)
browser_take_screenshot(element)   # Capture visual screenshot
browser_console_messages()         # Get console logs
browser_network_requests()         # Get network activity
```

### Interaction Commands
```
browser_click(element, ref)        # Click element
browser_type(element, ref, text)   # Type text
browser_press_key(key)             # Press keyboard key
browser_hover(element, ref)        # Hover over element
browser_select_option(element, ref, values) # Select dropdown option
browser_fill_form(fields)          # Fill multiple form fields
browser_drag(startElement, endElement) # Drag and drop
```

### Evaluation Commands
```
browser_evaluate(function)         # Run JavaScript in browser
browser_wait_for(text)             # Wait for text to appear
```

### Session Commands
```
browser_tabs(action)               # Manage tabs (list, new, close, select)
browser_resize(width, height)      # Resize viewport
browser_close()                    # Close browser
```

## Optimization Tips

### Tip 1: Test Components in Isolation First
Test individual components before testing them together:
1. Test Button alone (~300 tokens)
2. Test Form alone (~600 tokens)
3. Test Page with Button + Form (~1,000 tokens)

### Tip 2: Reuse Browser Sessions
Keep browser open across multiple tests:
- Initialize browser once
- Run all component tests
- Close browser at end
- Saves ~5-10 seconds per test

### Tip 3: Use Snapshots Over Screenshots
Accessibility snapshots are faster and contain more information:
- `browser_snapshot()` returns element tree
- Can locate elements without visual parsing
- Faster than screenshot analysis

### Tip 4: Test Only Changed Components
If running regression tests:
1. Identify which files changed (git diff)
2. Load only components that use changed files
3. Run tests for affected components only
4. Skip unaffected component tests

### Tip 5: Parallel Test Execution
Run independent tests in parallel:
- Component A test + Component B test (parallel)
- Don't wait for Component A to finish
- Reduces total test time by 50%+

## Validation Checklist

For each test execution:

- [ ] Correct test type identified
- [ ] Minimal context loaded for test type
- [ ] Browser initialized successfully
- [ ] Test URL accessible
- [ ] Test scenarios executed
- [ ] Expected outcomes validated
- [ ] Screenshots captured (if needed)
- [ ] Console errors checked
- [ ] Test results documented
- [ ] Context used: <2,500 tokens
- [ ] Token savings: >95% vs full codebase

## Common Pitfalls to Avoid

### Pitfall 1: Loading Backend Code
UI tests don't need backend context.

❌ **Bad**: Load `server/**` for UI tests
✅ **Good**: Load only `src/components/**` being tested

### Pitfall 2: Loading All Components
Don't load components not in the test.

❌ **Bad**: Load `src/components/**/*.tsx`
✅ **Good**: Load `src/components/LoginButton.tsx` only

### Pitfall 3: Loading Test Files
Playwright tests UI, not test code.

❌ **Bad**: Load `*.test.tsx` files
✅ **Good**: Load implementation files only

### Pitfall 4: Not Reusing Browser
Closing and reopening browser for each test is slow.

❌ **Bad**: Open → Test → Close, Open → Test → Close
✅ **Good**: Open → Test → Test → Test → Close

### Pitfall 5: Testing Everything
Don't run all tests when only one component changed.

❌ **Bad**: Run 100 tests when 1 component changed
✅ **Good**: Run 3 tests for affected component

## Token Budget by Test Type

### Component Unit Test
```
Target: 400-600 tokens
Files: 1-2
Time: 1-2 minutes
Example: Test Button renders
```

### Component Interaction Test
```
Target: 600-900 tokens
Files: 2-3
Time: 2-4 minutes
Example: Test Form submission
```

### Feature Integration Test
```
Target: 1,200-1,800 tokens
Files: 4-6
Time: 4-6 minutes
Example: Test comment system
```

### E2E Workflow Test
```
Target: 1,800-2,500 tokens
Files: 6-10
Time: 6-10 minutes
Example: Test registration flow
```

### Regression Test Suite
```
Target: 2,000-3,000 tokens (testing multiple components)
Files: 8-12
Time: 15-20 minutes
Example: Test all auth flows
```

## Success Metrics

### Expected Outcomes by Test Type

| Test Type | Tokens | Files | Time | Tests |
|-----------|--------|-------|------|-------|
| Component | 400-600 | 1-2 | 1-2 min | 1-3 |
| Interaction | 600-900 | 2-3 | 2-4 min | 2-5 |
| Feature | 1,200-1,800 | 4-6 | 4-6 min | 3-8 |
| E2E | 1,800-2,500 | 6-10 | 6-10 min | 1-2 |
| Regression | 2,000-3,000 | 8-12 | 15-20 min | 10-20 |

### Comparison to Full Context

| Metric | With Template | Without Template | Improvement |
|--------|---------------|------------------|-------------|
| Tokens | ~1,500 | ~50,000 | **97% reduction** |
| Files | ~5 | ~200 | **97.5% reduction** |
| Time | ~5 min | ~15 min | **67% faster** |
| Focus | Very High | Low | Clear improvement |

## Test Organization Strategy

### Strategy 1: By Component
```
Test each component independently:
- Button test (1-2 min, 400 tokens)
- Form test (2-3 min, 700 tokens)
- Card test (1-2 min, 500 tokens)
Total: 5-7 min, 1,600 tokens
```

### Strategy 2: By Feature
```
Test feature as a whole:
- Comment feature test (5-6 min, 1,600 tokens)
Total: 5-6 min, 1,600 tokens
```

### Strategy 3: By User Journey
```
Test complete workflows:
- Registration journey (8-10 min, 2,200 tokens)
- Post creation journey (6-8 min, 1,800 tokens)
Total: 14-18 min, 4,000 tokens
```

### Strategy 4: Changed Files Only
```
Test only affected components:
- Get git diff
- Identify changed components
- Test those + direct dependencies
- Skip unaffected components
Typical: 3-5 min, 1,000 tokens
```

## Related Templates

- **add-ui-component.md** - For implementing components before testing
- **fix-bug.md** - For fixing bugs found during testing
- **add-feature.md** - For implementing features before integration testing
- **add-api-endpoint.md** - For implementing APIs tested by E2E tests
