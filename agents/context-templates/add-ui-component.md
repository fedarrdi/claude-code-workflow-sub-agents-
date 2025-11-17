# Context Template: Add UI Component

## Use Case
Adding a new React component to the frontend with minimal context loading.

## Context Level
**Minimal** (800-1,200 tokens)

## Required Context

### Phase 1: Design System Check (Load First)
```
src/components/[similar-component].tsx    # Similar existing component for patterns
src/theme/index.ts                        # Theme configuration and design tokens
src/types/component.ts                    # Component prop type definitions
tailwind.config.js                        # Tailwind configuration (if styling needed)
```
**Tokens**: ~500

### Phase 2: Component Creation (Load Next)
```
src/components/[parent-component].tsx     # Parent component (if integrating)
src/hooks/[relevant-hook].ts              # Custom hooks needed by component
src/utils/[utility].ts                    # Utility functions needed
src/types/[data-types].ts                 # Data types for component props
```
**Tokens**: ~600 (cumulative ~1,100)

### Phase 3: Testing with Playwright (Load Last)
```
src/components/[new-component].tsx        # The newly created component
src/types/component.ts                    # Type definitions
```
**Tokens**: ~400 (cumulative ~1,500 for testing phase)

## Context to Exclude

### Always Exclude
```
server/**                          # Backend code
src/pages/**                       # Page components (unless integrating)
src/api/**                         # API client code (unless component needs it)
node_modules/**                    # Dependencies
dist/**                            # Build output
*.log                              # Logs
coverage/**                        # Test coverage
tests/**                           # Other test files
```

### Usually Exclude (load only if needed)
```
src/App.tsx                        # Main app component
src/main.tsx                       # Entry point
src/routes.tsx                     # Routing (unless adding to nav)
vite.config.ts                     # Build configuration
package.json                       # Dependencies list
```

## Execution Steps

### Step 1: Analyze Component Requirements
1. Identify component purpose (button, form, card, modal, etc.)
2. Determine component type (presentational vs container)
3. Identify required props and state
4. Check if similar component exists
5. Determine styling approach (Tailwind, CSS modules, styled)

### Step 2: Load Minimal Context
1. Find and load a similar existing component
2. Load theme/design system files
3. Load component type definitions
4. **Stop here** - don't load parent or page files yet

### Step 3: Design Component Interface
1. Define component props (TypeScript interface)
2. Define component state (if needed)
3. Identify reusable sub-components
4. Plan accessibility (ARIA labels, roles)

### Step 4: Implement Component
1. Create component file
2. Implement JSX structure
3. Apply styling (Tailwind classes)
4. Add interactivity (event handlers)
5. Add accessibility attributes
6. Handle edge cases (loading, error states)

### Step 5: Integrate Component (if needed)
1. Load parent component file
2. Import new component
3. Pass required props
4. Test integration manually

### Step 6: Test with Playwright
1. Navigate to component in browser
2. Test interactions (clicks, inputs, etc.)
3. Validate accessibility
4. Capture screenshot if needed
5. Test responsive behavior

## Example: Add ShareButton Component

### Context Loading Sequence

```yaml
Phase 1 - Design (Initial Load):
  files:
    - src/components/Button.tsx (similar component)
    - src/theme/index.ts (design tokens)
    - src/types/component.ts (prop types)
  tokens: ~450
  action: Review button patterns and design system

Phase 2 - Implementation (Context Expansion):
  context_diff:
    add:
      - src/hooks/useShare.ts (new hook for share functionality)
      - src/types/share.ts (share types)
      - src/utils/clipboard.ts (clipboard utility)
  tokens: ~1,000 total
  action: Create ShareButton component

Phase 3 - Integration (Minimal Expansion):
  context_diff:
    add:
      - src/components/Post.tsx (parent component)
    remove:
      - src/components/Button.tsx (no longer needed)
  tokens: ~1,100 total
  action: Add ShareButton to Post component

Phase 4 - Testing with Playwright (Test Context):
  context_diff:
    remove:
      - src/components/Post.tsx (implementation complete)
    keep:
      - src/components/ShareButton.tsx (testing this)
  tokens: ~600 (test context only)
  action: Test ShareButton interactions
```

### Implementation Code Pattern

```typescript
// src/components/ShareButton.tsx
import React from 'react';
import { Share2 } from 'lucide-react';
import { useShare } from '../hooks/useShare';
import type { ShareButtonProps } from '../types/component';

export const ShareButton: React.FC<ShareButtonProps> = ({
  url,
  title,
  className = ''
}) => {
  const { share, canShare } = useShare();

  const handleShare = async () => {
    await share({ url, title });
  };

  if (!canShare) return null;

  return (
    <button
      onClick={handleShare}
      className={`flex items-center gap-2 px-4 py-2 bg-secondary text-white
                  rounded-lg hover:bg-secondary/90 transition-colors ${className}`}
      aria-label="Share this content"
    >
      <Share2 size={18} />
      <span>Share</span>
    </button>
  );
};
```

## Optimization Tips

### Tip 1: Find Similar Components First
Always search for a similar existing component to match patterns:
- Button → Look at existing Button, IconButton
- Form → Look at existing LoginForm, RegisterForm
- Card → Look at existing PostCard, UserCard
- Modal → Look at existing Modal, Dialog

### Tip 2: Leverage Design System
Load theme configuration early to use consistent:
- Colors
- Spacing
- Typography
- Shadows
- Transitions

### Tip 3: Keep Components Small
If component is getting complex (>200 lines), break into smaller sub-components.
Load context for one component at a time.

### Tip 4: Use Playwright Efficiently
For component testing:
- Load only the component being tested
- Load prop types to understand interface
- Don't load entire page context
- Test in isolation when possible

### Tip 5: Defer Parent Integration
Implement and test the component in isolation first.
Only load parent component when ready to integrate.

## Validation Checklist

Before considering the task complete:

- [ ] Component file created in appropriate directory
- [ ] TypeScript interface defined for props
- [ ] Component implemented with proper JSX structure
- [ ] Styling applied (Tailwind classes or CSS)
- [ ] Interactivity added (event handlers, state)
- [ ] Accessibility attributes added (ARIA labels, roles)
- [ ] Edge cases handled (loading, error, empty states)
- [ ] Component integrated into parent (if applicable)
- [ ] Playwright tests passed
- [ ] Screenshot captured (if visual component)
- [ ] Context used: <1,500 tokens
- [ ] Token savings: >97% vs full codebase

## Common Pitfalls to Avoid

### Pitfall 1: Loading Entire Component Library
Don't load all components, only the similar one you need.

❌ **Bad**: Load `src/components/**/*.tsx`
✅ **Good**: Load `src/components/Button.tsx` only

### Pitfall 2: Loading Page Components
Pages are complex and often unnecessary for component development.

❌ **Bad**: Load `src/pages/Home.tsx` to see usage
✅ **Good**: Load only parent component if integrating

### Pitfall 3: Loading API Code
Most presentational components don't need API context.

❌ **Bad**: Load `src/api/posts.ts` for data structure
✅ **Good**: Load `src/types/post.ts` for types only

### Pitfall 4: Loading Backend Code
Frontend components never need backend context.

❌ **Bad**: Load `server/routes/posts.ts`
✅ **Good**: Exclude all `server/**` files

### Pitfall 5: Testing Too Early
Don't load test setup until component implementation is complete.

❌ **Bad**: Load Playwright test context during development
✅ **Good**: Load test context only in Phase 4

## Token Budget

### Target Budget: 800-1,200 tokens

| Phase | Files | Tokens | Cumulative |
|-------|-------|--------|------------|
| Design | 3-4 | 450 | 450 |
| Implementation | +2-3 | 550 | 1,000 |
| Integration | +1-2 | 300 | 1,300 |
| Testing | -2 +1 | 600 | 600 (test only) |
| **Total** | **4-6** | **1,300** | - |

### Token Breakdown by File Type

| File Type | Avg Tokens | Example |
|-----------|------------|---------|
| Component file | 200-400 | `src/components/Button.tsx` |
| Hook file | 100-200 | `src/hooks/useShare.ts` |
| Types file | 50-150 | `src/types/component.ts` |
| Theme file | 100-200 | `src/theme/index.ts` |
| Utility file | 100-150 | `src/utils/clipboard.ts` |
| Config file | 100-150 | `tailwind.config.js` |

## Playwright Testing Guide

### Test Context (Load Separately)
```yaml
files:
  - src/components/[ComponentName].tsx
  - src/types/component.ts
tokens: ~400-600
```

### Test Workflow
```javascript
// MCP Playwright commands
1. Navigate to component page/story
2. Take snapshot (accessibility tree)
3. Click button/interact with component
4. Assert expected behavior
5. Take screenshot (if visual validation)
```

### Example Test Scenario
```
Test: ShareButton component

Context:
  - src/components/ShareButton.tsx
  - src/types/component.ts

MCP Commands:
  1. browser_navigate to http://localhost:5173/components/share-button
  2. browser_snapshot to see component structure
  3. browser_click on ShareButton
  4. browser_console_messages to check for errors
  5. browser_take_screenshot for visual validation

Tokens: ~500
Time: ~2 minutes
```

## Success Metrics

### Expected Outcomes
- **Token usage**: 800-1,300 tokens
- **Token savings**: >97% compared to full context
- **Files loaded**: 4-6 files
- **Implementation time**: 5-10 minutes
- **Context expansions**: 2-3 phases
- **Success rate**: >98%

### Comparison to Full Context

| Metric | With Template | Without Template | Improvement |
|--------|---------------|------------------|-------------|
| Tokens | ~1,200 | ~50,000 | **98% reduction** |
| Files | ~5 | ~200 | **97.5% reduction** |
| Time | ~8 min | ~25 min | **68% faster** |
| Focus | Very High | Low | Clear improvement |

## Related Templates

- **add-api-endpoint.md** - For adding backend API endpoints
- **fix-bug.md** - For fixing component bugs
- **add-feature.md** - For larger UI features with multiple components
- **run-tests.md** - For comprehensive component testing with Playwright
