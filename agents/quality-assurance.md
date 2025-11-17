# Quality Assurance Agent

## Role & Responsibilities

### Primary Function
Implements testing strategies, code quality standards, and linting based on patterns from the reddit-quora-web-site project. While the original project has no tests, this agent defines testing patterns that should be implemented.

### Specific Tasks Handled
- ESLint configuration and rules
- Code formatting standards
- Testing framework setup (recommended)
- Test pattern recommendations
- Code review checklist
- Quality gates

### Boundaries and Limitations
- Original project has NO testing framework
- ESLint is configured
- No Prettier configuration
- This agent provides RECOMMENDATIONS for testing

## Tech Stack Knowledge

### Linting
```json
{
  "eslint": "8.55.0",
  "@typescript-eslint/eslint-plugin": "6.14.0",
  "@typescript-eslint/parser": "6.14.0",
  "eslint-plugin-react-hooks": "4.6.0",
  "eslint-plugin-react-refresh": "0.4.5"
}
```

### Recommended Testing Stack (Not Currently Implemented)
```json
{
  "vitest": "^1.0.0",
  "@testing-library/react": "^14.0.0",
  "@testing-library/jest-dom": "^6.0.0",
  "@testing-library/user-event": "^14.0.0"
}
```

## Code Templates

### Template 1: ESLint Configuration (.eslintrc.cjs)
```javascript
module.exports = {
  root: true,
  env: { browser: true, es2020: true },
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:react-hooks/recommended',
  ],
  ignorePatterns: ['dist', '.eslintrc.cjs'],
  parser: '@typescript-eslint/parser',
  plugins: ['react-refresh'],
  rules: {
    'react-refresh/only-export-components': [
      'warn',
      { allowConstantExport: true },
    ],
    '@typescript-eslint/no-unused-vars': [
      'warn',
      { argsIgnorePattern: '^_' }
    ],
  },
}
```

### Template 2: Recommended Test Setup (vitest.config.ts)
```typescript
import { defineConfig } from 'vitest/config'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: './src/test/setup.ts',
  },
})
```

### Template 3: Component Test Example
```typescript
import { render, screen, fireEvent } from '@testing-library/react'
import { describe, it, expect, vi } from 'vitest'
import Button from './Button'

describe('Button Component', () => {
  it('renders with correct text', () => {
    render(<Button>Click Me</Button>)
    expect(screen.getByText('Click Me')).toBeInTheDocument()
  })

  it('calls onClick when clicked', () => {
    const handleClick = vi.fn()
    render(<Button onClick={handleClick}>Click Me</Button>)

    fireEvent.click(screen.getByText('Click Me'))
    expect(handleClick).toHaveBeenCalledTimes(1)
  })

  it('is disabled when disabled prop is true', () => {
    render(<Button disabled>Click Me</Button>)
    expect(screen.getByText('Click Me')).toBeDisabled()
  })
})
```

### Template 4: API Test Example
```typescript
import { describe, it, expect, beforeEach, afterEach } from 'vitest'
import { apiRequest } from './api'

describe('API Client', () => {
  beforeEach(() => {
    // Setup
  })

  afterEach(() => {
    // Cleanup
  })

  it('makes successful GET request', async () => {
    const data = await apiRequest('/test')
    expect(data).toBeDefined()
  })

  it('handles 404 errors', async () => {
    await expect(apiRequest('/nonexistent')).rejects.toThrow()
  })

  it('retries on server errors', async () => {
    // Test retry logic
  })
})
```

## Best Practices

### From This Project

#### 1. TypeScript Strict Mode
```json
{
  "strict": true,
  "noUnusedLocals": true,
  "noUnusedParameters": true,
  "noFallthroughCasesInSwitch": true
}
```

#### 2. ESLint Rules
- Warn on unused variables (except those starting with `_`)
- Enforce React Hooks rules
- Warn on components exporting non-components

#### 3. Code Review Checklist
- [ ] No console.log in production code (use console.error for errors)
- [ ] All user input validated
- [ ] SQL queries use prepared statements
- [ ] Passwords hashed (bcrypt, 12 rounds)
- [ ] JWT tokens have expiration
- [ ] CORS configured correctly
- [ ] Environment variables not hardcoded
- [ ] Error messages don't expose internal details
- [ ] Components have TypeScript types
- [ ] useEffect dependencies correct
- [ ] Database persistence after writes
- [ ] Proper HTTP status codes used

## Validation Checklist

- [ ] ESLint runs without errors (`npm run lint`)
- [ ] TypeScript compiles without errors (`tsc --noEmit`)
- [ ] Code follows project conventions
- [ ] Security best practices followed
- [ ] No secrets in code
- [ ] Error handling implemented

## Context Requirements

### Required Files
- Entire codebase (both `src/` and `server/` directories)
- `.eslintrc.cjs` - ESLint configuration
- `tsconfig.json` - TypeScript configuration (frontend and backend)
- `.env` - To verify no secrets are hardcoded
- `SPECIFICATION.md` - To validate success criteria

### Optional Context
- Test files (if any exist)
- Security checklist documentation
- Code review guidelines

### Exclude Patterns
- `node_modules/**` - Dependencies (not part of code review)
- `dist/**` - Build output (not source code)
- `.git/**` - Git history
- `*.log` - Log files
- `coverage/**` - Test coverage reports

## Context Loading Strategy

### Phase 1: Static Analysis
Run automated quality checks:
- Execute `npm run lint` to check ESLint rules
- Run `tsc --noEmit` to verify TypeScript compilation
- Check for console.log statements in production code
- Scan for hardcoded secrets or API keys

### Phase 2: Code Review
Manual code inspection:
- Review authentication implementation (JWT, bcrypt)
- Check SQL queries for prepared statements (no SQL injection)
- Verify input validation on all API endpoints
- Check CORS configuration
- Review error handling patterns

### Phase 3: Security Audit
Security-focused validation:
- Verify passwords are hashed (bcrypt with 12 rounds)
- Check JWT tokens have expiration
- Ensure environment variables used (not hardcoded)
- Validate no sensitive data in error messages
- Check .gitignore includes .env and *.sqlite files

## MCP Tools Used
None - This agent performs code quality checks using standard tools (ESLint, TypeScript compiler). No browser automation required for static analysis.

## State Management

### Quality Assurance State
```json
{
  "staticAnalysis": {
    "eslintErrors": 0,
    "eslintWarnings": 2,
    "typescriptErrors": 0,
    "codeStyleIssues": 0
  },
  "securityAudit": {
    "passwordsHashed": true,
    "jwtExpiration": true,
    "sqlInjectionPrevention": true,
    "corsConfigured": true,
    "secretsHardcoded": false
  },
  "codeQuality": {
    "unusedVariables": 0,
    "missingTypes": 0,
    "errorHandling": "complete",
    "validationCoverage": "100%"
  },
  "successCriteria": {
    "totalCriteria": 10,
    "criteriaPass ed": 10,
    "criteriaPending": 0
  }
}
```

### Agent Statistics
```json
{
  "filesReviewed": 45,
  "eslintRulesChecked": 82,
  "typescriptFilesCompiled": 38,
  "securityIssuesFound": 0,
  "codeSmellsDetected": 0,
  "bestPracticesViolations": 0,
  "accessibilityIssues": 0,
  "performanceWarnings": 0,
  "overallQualityScore": "A+",
  "readyForProduction": true
}
```

---

**Agent Version**: 1.0
**Based on**: reddit-quora-web-site codebase
**Last Updated**: 2025-11-16
