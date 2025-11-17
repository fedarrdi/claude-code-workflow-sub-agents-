# Quick Commands for Context-Aware Agent Execution

## Overview
Fast reference commands for common operations with the context management system.

## Loading Context

### Load Minimal Context for Component
```
Query: "Load context for [ComponentName] component"
Returns: Component file + types + design system (~800 tokens)
Use: Small UI changes, component additions
```

### Load Module Context
```
Query: "Load [module-name] module context"
Returns: All files in module + dependencies (~2,000 tokens)
Use: Feature additions, module refactoring
```

### Load Full Context
```
Query: "Load full context for [feature-name]"
Returns: Multiple modules + integration points (~5,000 tokens)
Use: Major features, architectural changes
```

### Load Trace Context (Bug Fixes)
```
Query: "Load context for error in [filename]:[line]"
Returns: Error file + stack trace files (~600 tokens)
Use: Bug fixes, error resolution
```

## Running Agents

### Execute with Minimal Context
```
Command: "Execute [agent-name] with minimal context for [task]"
Example: "Execute frontend-engineer with minimal context for adding logout button"
Result: Agent loads <1,200 tokens, focused execution
```

### Execute with Module Context
```
Command: "Execute [agent-name] with module context for [module]"
Example: "Execute backend-engineer with module context for authentication"
Result: Agent loads module files (~2,000 tokens)
```

### Execute with Cached Context
```
Command: "Execute [agent-name] using cached pattern [pattern-name]"
Example: "Execute frontend-engineer using cached pattern add-ui-component"
Result: Instant context load from cache, fast execution
```

## Playwright Testing

### Test Single Component
```
Command: "Test [ComponentName] with Playwright"
Context: Component file + types only (~400 tokens)
Execution: Navigate → Snapshot → Interact → Assert
Time: 1-2 minutes
```

### Test Feature
```
Command: "Test [feature-name] feature with Playwright"
Context: Feature module files (~1,500 tokens)
Execution: Multi-component integration tests
Time: 4-6 minutes
```

### Test E2E Workflow
```
Command: "Test [workflow-name] E2E flow with Playwright"
Context: Workflow pages + components (~2,000 tokens)
Execution: Complete user journey test
Time: 6-10 minutes
```

### Test Changed Components Only
```
Command: "Test components changed in [git-branch/commit]"
Context: Only changed files + dependencies (~1,000 tokens)
Execution: Focused regression testing
Time: 3-5 minutes
```

## Context Management

### View Current Context
```
Command: "Show current context"
Returns: List of loaded files and token count
Use: Monitor context usage
```

### View Module Registry
```
Command: "Show modules in registry"
Returns: All registered modules and their file patterns
Use: Understand project structure
```

### View Context Cache
```
Command: "Show context cache"
Returns: Cached patterns and usage stats
Use: Check cache hit rate, optimize patterns
```

### Clear Context Cache
```
Command: "Clear context cache"
Effect: Removes all cached context slices
Use: After major refactoring or file restructuring
```

### Update Context Registry
```
Command: "Update context registry"
Effect: Rescans project and updates module mappings
Use: After adding new modules or restructuring
```

## Context Optimization

### Check Token Usage
```
Command: "Show token usage for [task-type]"
Returns: Historical token usage for task type
Use: Optimize future executions
```

### Optimize Context for Task
```
Command: "Optimize context for [task-description]"
Returns: Recommended context level and file list
Use: Plan execution before starting
```

### Suggest Context Level
```
Command: "Suggest context level for [task]"
Returns: Minimal, Module, or Full recommendation
Use: Determine appropriate context before loading
```

## Workflow Commands

### Add UI Component
```
Command: "Add component [ComponentName] to [ParentComponent]"
Workflow:
  1. Load: Similar component + design system
  2. Create: New component file
  3. Integrate: Add to parent
  4. Test: Playwright component test
Context: Minimal (~1,200 tokens)
Time: 5-10 minutes
```

### Add API Endpoint
```
Command: "Add API endpoint [METHOD] [path]"
Workflow:
  1. Load: Routes + models
  2. Design: Endpoint signature
  3. Implement: Controller + route
  4. Test: API tests
Context: Module (~2,000 tokens)
Time: 10-15 minutes
```

### Fix Bug
```
Command: "Fix bug: [error-description]"
Workflow:
  1. Load: Error trace files
  2. Investigate: Root cause
  3. Fix: Implement solution
  4. Test: Regression test
Context: Minimal (~800 tokens)
Time: 3-8 minutes
```

### Add Feature
```
Command: "Add feature [feature-name]"
Workflow:
  1. Load: Module entry points
  2. Plan: Break into sub-tasks
  3. Backend: Implement API
  4. Frontend: Implement UI
  5. Integrate: Connect pieces
  6. Test: E2E tests
Context: Module → Full (~3,500 tokens peak)
Time: 45-90 minutes
```

### Refactor Module
```
Command: "Refactor [module-name] module"
Workflow:
  1. Load: All module files
  2. Analyze: Find usages
  3. Refactor: Incremental changes
  4. Update: Tests and types
Context: Module (~4,000 tokens)
Time: 30-60 minutes
```

## Cache Management

### Cache Current Context
```
Command: "Cache context as [pattern-name]"
Effect: Saves current context for reuse
Use: After successful task execution
```

### Load Cached Pattern
```
Command: "Load cached pattern [pattern-name]"
Returns: Previously cached context
Use: Repeat similar tasks quickly
```

### View Pattern Usage
```
Command: "Show usage for pattern [pattern-name]"
Returns: Usage count, success rate, avg tokens
Use: Evaluate pattern effectiveness
```

### Update Cached Pattern
```
Command: "Update cached pattern [pattern-name]"
Effect: Refreshes cache with current file versions
Use: After files in pattern have changed
```

## Common Patterns Quick Reference

### Pattern: add-ui-component
```
Files: Component + theme + types
Tokens: ~1,200
Time: 5-10 minutes
Command: "Use pattern add-ui-component"
```

### Pattern: add-api-endpoint
```
Files: Routes + controllers + models + types
Tokens: ~2,000
Time: 10-15 minutes
Command: "Use pattern add-api-endpoint"
```

### Pattern: fix-bug
```
Files: Error trace files + dependencies
Tokens: ~800
Time: 3-8 minutes
Command: "Use pattern fix-bug"
```

### Pattern: add-feature
```
Files: Multiple modules + integration points
Tokens: ~3,000
Time: 45-90 minutes
Command: "Use pattern add-feature"
```

### Pattern: run-tests
```
Files: Test targets + types
Tokens: ~1,500
Time: 5-10 minutes
Command: "Use pattern run-tests"
```

## Agent-Specific Commands

### Project Architect
```
Command: "Initialize project [project-name]"
Context: Tech stack references + templates
Tokens: ~1,000
Creates: Complete project structure
```

### Backend Engineer
```
Command: "Implement backend for [feature-name]"
Context: Backend module files
Tokens: ~2,000
Implements: API endpoints, controllers, models
```

### Frontend Engineer
```
Command: "Implement UI for [feature-name]"
Context: Frontend module files
Tokens: ~1,800
Implements: Components, hooks, styling
```

### Quality Assurance
```
Command: "Test [target] with Playwright"
Context: Test target + types
Tokens: ~1,500
Executes: Unit, integration, or E2E tests
```

### Database Architect
```
Command: "Design schema for [resource-name]"
Context: Database models + types
Tokens: ~1,500
Creates: Schema, migrations, models
```

### API Designer
```
Command: "Design API for [module-name]"
Context: API specs + types
Tokens: ~800
Creates: API documentation and types
```

### Orchestrator
```
Command: "Orchestrate [feature-name]"
Context: SPEC + all agent definitions
Tokens: ~1,000
Coordinates: All agents for complete feature
```

## MCP Tool Commands

### Playwright: Navigate
```
Command: "playwright navigate to [url]"
Use: Start browser test
```

### Playwright: Snapshot
```
Command: "playwright snapshot"
Returns: Accessibility tree
Use: Inspect page structure
```

### Playwright: Click
```
Command: "playwright click [element-name]"
Use: Interact with UI element
```

### Playwright: Type
```
Command: "playwright type [text] in [element-name]"
Use: Fill form inputs
```

### Playwright: Screenshot
```
Command: "playwright screenshot [element-name]"
Returns: Visual screenshot
Use: Visual validation
```

### Playwright: Assert
```
Command: "playwright assert [condition]"
Use: Validate test expectations
```

## Context Diff Commands

### Add Files to Context
```
Command: "Add [file-pattern] to context"
Effect: Expands current context
Use: Load additional files as needed
```

### Remove Files from Context
```
Command: "Remove [file-pattern] from context"
Effect: Unloads files from context
Use: Clean up completed tasks
```

### Replace Context
```
Command: "Replace context with [module-name]"
Effect: Unload all, load new module
Use: Switch between backend/frontend
```

### Keep Files in Context
```
Command: "Keep only [file-list] in context"
Effect: Unload all except specified
Use: Minimize context for specific task
```

## Performance Commands

### View Token Savings
```
Command: "Show token savings"
Returns: Total and percentage savings vs full context
Use: Monitor optimization effectiveness
```

### View Execution Stats
```
Command: "Show execution stats"
Returns: Task counts, avg times, success rates
Use: Track system performance
```

### View Cache Stats
```
Command: "Show cache stats"
Returns: Hit rate, entry count, usage patterns
Use: Optimize caching strategy
```

### View Hot Modules
```
Command: "Show hot modules"
Returns: Most frequently accessed modules
Use: Identify optimization opportunities
```

## Shortcuts

### Quick Component Add
```
Command: "qc [ComponentName]"
Executes: Add component with minimal context
```

### Quick Bug Fix
```
Command: "qf [error-description]"
Executes: Fix bug with trace context
```

### Quick API Add
```
Command: "qa [METHOD] [path]"
Executes: Add API endpoint with module context
```

### Quick Test
```
Command: "qt [target]"
Executes: Test with Playwright
```

### Quick Context Load
```
Command: "ql [module-name]"
Executes: Load module context
```

## Usage Examples

### Example 1: Add Logout Button
```
Command: "qc LogoutButton"
or
Full: "Add component LogoutButton to Navigation"

Execution:
  1. Load: Navigation.tsx, Button.tsx, theme
  2. Create: LogoutButton component
  3. Integrate: Add to Navigation
  4. Test: Click button → assert logout

Tokens: ~900
Time: ~6 minutes
```

### Example 2: Fix Auth Error
```
Command: "qf Token validation failing in auth.ts"
or
Full: "Fix bug: Token validation failing in auth.ts"

Execution:
  1. Load: auth.ts, jwt.ts
  2. Find: Validation logic error
  3. Fix: Update validation
  4. Test: Auth flow

Tokens: ~600
Time: ~4 minutes
```

### Example 3: Add Comments API
```
Command: "qa POST /api/posts/:id/comments"
or
Full: "Add API endpoint POST /api/posts/:id/comments"

Execution:
  1. Load: posts routes, comment model
  2. Design: Endpoint signature
  3. Implement: Controller + route
  4. Test: API tests

Tokens: ~1,800
Time: ~12 minutes
```

### Example 4: Test Registration Form
```
Command: "qt RegistrationForm"
or
Full: "Test RegistrationForm with Playwright"

Execution:
  1. Load: RegistrationForm.tsx, validation.ts
  2. Navigate: /register
  3. Test: Empty fields, invalid data, valid submission
  4. Assert: All validations work

Tokens: ~800
Time: ~3 minutes
```

## Best Practices

### DO:
- Start with minimal context
- Use cached patterns when available
- Load tests after implementation
- Use context diff for multi-step tasks
- Monitor token usage
- Cache successful patterns

### DON'T:
- Load entire codebase "just in case"
- Load tests during implementation
- Cross module boundaries unnecessarily
- Ignore cache
- Skip context optimization
- Load unrelated files

## Quick Reference Card

| Command | Context | Tokens | Time |
|---------|---------|--------|------|
| Add Component | Minimal | ~1,200 | 5-10 min |
| Add API | Module | ~2,000 | 10-15 min |
| Fix Bug | Minimal | ~800 | 3-8 min |
| Add Feature | Module-Full | ~3,000 | 45-90 min |
| Test Component | Minimal | ~500 | 1-2 min |
| Test Feature | Module | ~1,500 | 4-6 min |
| Test E2E | Module | ~2,000 | 6-10 min |
| Refactor | Module | ~4,000 | 30-60 min |

## Getting Help

### View All Commands
```
Command: "Show all quick commands"
Returns: This document
```

### View Command Help
```
Command: "Help with [command-name]"
Returns: Detailed command documentation
```

### View Context System Help
```
Command: "Help with context system"
Returns: Context management overview
```

### View Agent Help
```
Command: "Help with [agent-name]"
Returns: Agent capabilities and usage
```

## Related Documentation

- **smart-context-prompt.md**: Context-aware execution instructions
- **execute-with-context.md**: Detailed execution patterns
- **context-manager.md**: Context management system
- **workflow-optimizer.md**: Workflow optimization strategies
- **context-templates/**: Specific templates for tasks
- **context/README.md**: Context directory overview
