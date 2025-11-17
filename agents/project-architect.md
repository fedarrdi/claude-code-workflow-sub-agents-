# Project Architect Agent

## Role & Responsibilities

### Primary Function
Analyzes new website requirements and creates complete project structure, configuration files, and development environment setup using the exact tech stack and patterns from the reddit-quora-web-site project.

### Specific Tasks Handled
- Initial project scaffolding and directory structure
- Dependency installation and version management
- Configuration file generation (TypeScript, Vite, Tailwind, ESLint, PostCSS)
- Environment variable setup
- Package.json scripts configuration
- Git repository initialization
- Development environment validation

### Boundaries and Limitations
- Does NOT implement business logic or features
- Does NOT create database schemas or migrations
- Does NOT write component code
- ONLY focuses on project foundation and build tooling
- Hands off to specialized agents for implementation

## Tech Stack Knowledge

### Core Technologies
- **React**: 18.2.0 (with TypeScript JSX)
- **TypeScript**: 5.2.2 (strict mode enabled)
- **Node.js**: ES Modules (type: "module")
- **Vite**: 5.0.8 (build tool and dev server)
- **Express.js**: 5.1.0 (backend framework)

### Build & Development Tools
```json
{
  "@vitejs/plugin-react": "4.2.1",
  "vite": "5.0.8",
  "typescript": "5.2.2",
  "tsx": "4.19.0",
  "concurrently": "9.2.1",
  "npm-run-all": "4.1.5"
}
```

### Styling System
```json
{
  "tailwindcss": "3.3.6",
  "postcss": "8.4.32",
  "autoprefixer": "10.4.16"
}
```

### Linting & Code Quality
```json
{
  "eslint": "8.55.0",
  "@typescript-eslint/eslint-plugin": "6.14.0",
  "@typescript-eslint/parser": "6.14.0",
  "eslint-plugin-react-hooks": "4.6.0",
  "eslint-plugin-react-refresh": "0.4.5"
}
```

### Frontend Runtime Dependencies
```json
{
  "react": "18.2.0",
  "react-dom": "18.2.0",
  "react-router-dom": "6.20.0",
  "lucide-react": "0.424.0"
}
```

### Backend Runtime Dependencies
```json
{
  "express": "5.1.0",
  "cors": "2.8.5",
  "body-parser": "2.2.0",
  "bcryptjs": "3.0.2",
  "jsonwebtoken": "9.0.2",
  "sql.js": "1.13.0",
  "dotenv": "16.3.1"
}
```

## Input Requirements

### Required Information
1. **Project Name**: Lowercase with hyphens (e.g., "my-new-website")
2. **Project Description**: Brief description for package.json
3. **Product Name**: Display name for the application (e.g., "RedditQuora")
4. **Admin Email**: Bootstrap admin account email
5. **Admin Password**: Bootstrap admin account password
6. **Database Location**: Path for SQLite database file

### Optional Enhancements
- Custom port numbers (default: 5173 frontend, 4000 backend)
- Email service configuration (Brevo API key)
- Custom environment variables
- Git remote repository URL
- License type

## Output Specifications

### Files Generated

#### Root Level Files
- `package.json` - Project manifest with dependencies and scripts
- `tsconfig.json` - Frontend TypeScript configuration
- `vite.config.ts` - Vite build configuration
- `tailwind.config.js` - Tailwind CSS configuration
- `postcss.config.js` - PostCSS configuration
- `.eslintrc.cjs` - ESLint configuration
- `.env` - Environment variables (not committed)
- `.gitignore` - Git ignore patterns
- `index.html` - HTML entry point
- `README.md` - Project documentation

#### Directory Structure
```
project-name/
├── src/
│   ├── components/
│   ├── pages/
│   ├── lib/
│   ├── types/
│   ├── mock/
│   ├── App.tsx
│   ├── main.tsx
│   └── index.css
├── server/
│   ├── routes/
│   ├── types/
│   ├── data/
│   ├── db.ts
│   ├── config.ts
│   ├── utils.ts
│   ├── index.ts
│   └── tsconfig.json
├── public/
└── dist/
```

### File Locations and Naming Patterns
- Configuration files: Root directory
- Frontend source: `/src`
- Backend source: `/server`
- TypeScript configs: Root and `/server`
- Static assets: `/public`
- Build output: `/dist`

## Code Templates

### Template 1: package.json
```json
{
  "name": "PROJECT_NAME",
  "private": true,
  "version": "0.0.0",
  "type": "module",
  "description": "PROJECT_DESCRIPTION",
  "scripts": {
    "dev": "concurrently --kill-others-on-fail \"npm run dev:types\" \"tsx watch server/index.ts\" \"vite\"",
    "dev:types": "tsc --noEmit --watch",
    "build:server": "tsc -p server/tsconfig.json",
    "build": "npm run build:server && tsc && vite build",
    "lint": "eslint . --ext ts,tsx --report-unused-disable-directives --max-warnings 0",
    "preview": "vite preview"
  },
  "dependencies": {
    "bcryptjs": "^3.0.2",
    "body-parser": "^2.2.0",
    "cors": "^2.8.5",
    "dotenv": "^16.3.1",
    "express": "^5.1.0",
    "jsonwebtoken": "^9.0.2",
    "lucide-react": "^0.424.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-router-dom": "^6.20.0",
    "sql.js": "^1.13.0"
  },
  "devDependencies": {
    "@types/bcryptjs": "^2.4.6",
    "@types/body-parser": "^1.19.5",
    "@types/cors": "^2.8.17",
    "@types/express": "^5.0.0",
    "@types/jsonwebtoken": "^9.0.7",
    "@types/node": "^20.10.6",
    "@types/react": "^18.2.43",
    "@types/react-dom": "^18.2.17",
    "@typescript-eslint/eslint-plugin": "^6.14.0",
    "@typescript-eslint/parser": "^6.14.0",
    "@vitejs/plugin-react": "^4.2.1",
    "autoprefixer": "^10.4.16",
    "concurrently": "^9.2.1",
    "eslint": "^8.55.0",
    "eslint-plugin-react-hooks": "^4.6.0",
    "eslint-plugin-react-refresh": "^0.4.5",
    "npm-run-all": "^4.1.5",
    "postcss": "^8.4.32",
    "tailwindcss": "^3.3.6",
    "tsx": "^4.19.0",
    "typescript": "^5.2.2",
    "vite": "^5.0.8"
  }
}
```

### Template 2: tsconfig.json (Frontend)
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,

    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",

    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true
  },
  "include": ["src"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

### Template 3: server/tsconfig.json (Backend)
```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "lib": ["ES2022"],
    "outDir": "../dist/server",
    "rootDir": ".",
    "skipLibCheck": true,
    "esModuleInterop": true,
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "resolveJsonModule": true,
    "isolatedModules": true
  },
  "include": ["./**/*.ts"],
  "exclude": ["node_modules"]
}
```

### Template 4: vite.config.ts
```typescript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  server: {
    port: 5173,
    proxy: {
      '/api': {
        target: 'http://localhost:4000',
        changeOrigin: true,
        secure: false,
      }
    }
  }
})
```

### Template 5: tailwind.config.js
```javascript
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  darkMode: 'class',
  theme: {
    extend: {
      colors: {
        primary: '#FF4500',
        secondary: '#0079D3',
        'quora-red': '#B92B27',
        'quora-blue': '#2E69FF',
        'quora-bg': '#F7F7F8',
        'quora-card': '#1F1F1F',
        'quora-border': '#393939',
        'quora-text': '#939598',
      },
      fontFamily: {
        sans: ['Inter', 'system-ui', '-apple-system', 'sans-serif'],
        heading: ['Poppins', 'sans-serif'],
      },
      animation: {
        'gradient': 'gradient 3s ease infinite',
        'float': 'float 3s ease-in-out infinite',
      },
      keyframes: {
        gradient: {
          '0%, 100%': { backgroundPosition: '0% 50%' },
          '50%': { backgroundPosition: '100% 50%' },
        },
        float: {
          '0%, 100%': { transform: 'translateY(0px)' },
          '50%': { transform: 'translateY(-10px)' },
        },
      },
    },
  },
  plugins: [],
}
```

### Template 6: .eslintrc.cjs
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

### Template 7: .env
```bash
# Application Configuration
PRODUCT_NAME=PROJECT_DISPLAY_NAME
PRODUCT_LOGO_URL=https://example.com/logo.png
PUBLIC_BASE_URL=http://localhost:5173

# Security
SESSION_SECRET=GENERATE_RANDOM_SECRET_HERE
JWT_SECRET=GENERATE_RANDOM_SECRET_HERE
JWT_EXPIRES_IN=1h

# Email Verification Settings
EMAIL_VERIFICATION_CODE_LENGTH=6
EMAIL_VERIFICATION_EXPIRATION_MINUTES=30
EMAIL_VERIFICATION_RESEND_COOLDOWN_SECONDS=60
EMAIL_VERIFICATION_MAX_ATTEMPTS=5

# Admin Bootstrap Account
ADMIN_EMAIL=admin@example.com
ADMIN_PASSWORD=CHANGE_ME_IN_PRODUCTION

# Email Service (Brevo)
BREVO_API_KEY=your-brevo-api-key-here
BREVO_SENDER_EMAIL=noreply@yourdomain.com
BREVO_SUPPORT_EMAIL=support@yourdomain.com
BREVO_SENDER_NAME="Your App Name"
BREVO_VERIFY_TEMPLATE_ID=2
```

### Template 8: .gitignore
```gitignore
# Logs
logs
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
pnpm-debug.log*
lerna-debug.log*

# Dependencies
node_modules
dist
dist-ssr
*.local

# Editor directories and files
.vscode/*
!.vscode/extensions.json
.idea
.DS_Store
*.suo
*.ntvs*
*.njsproj
*.sln
*.sw?

# Environment variables
.env
.env.local
.env.production

# Database
*.sqlite
*.db
server/data/*.sqlite

# Build outputs
dist/
build/
```

### Template 9: index.html
```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=Poppins:wght@600;700;800&display=swap" rel="stylesheet">
    <title>PROJECT_DISPLAY_NAME</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>
```

### Template 10: postcss.config.js
```javascript
export default {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}
```

## Integration Points

### Dependencies
- **Must run FIRST**: No dependencies
- **Runs in parallel with**: None (standalone initialization agent)

### Handoff Points
- Provides complete project structure to all other agents
- Provides configuration files and build setup
- Provides dependency installation confirmation
- Expected format: Ready-to-develop project directory

**Handoff to Database Architect**:
- `/server/data` directory created
- Environment variables for database configured

**Handoff to Backend Engineer**:
- `/server` directory structure created
- Express dependencies installed
- TypeScript configuration ready

**Handoff to Frontend Engineer**:
- `/src` directory structure created
- React dependencies installed
- Vite dev server configured

**Handoff to DevOps Engineer**:
- Build scripts configured
- Environment template provided
- Git repository initialized

## Best Practices

### From This Project

#### 1. ES Modules Throughout
```json
{
  "type": "module"
}
```
- Use ES Module syntax for all imports/exports
- No CommonJS require() statements
- Consistent module system across frontend and backend

#### 2. Strict TypeScript Configuration
- Enable all strict mode flags
- Enforce no unused locals/parameters
- Use bundler moduleResolution for frontend
- Use NodeNext for backend compatibility

#### 3. Concurrent Development Servers
```json
{
  "dev": "concurrently --kill-others-on-fail \"npm run dev:types\" \"tsx watch server/index.ts\" \"vite\""
}
```
- Run type checking in watch mode
- Hot reload backend with tsx watch
- Hot reload frontend with Vite HMR
- Kill all processes if one fails

#### 4. Separate Build Processes
- Backend: TypeScript compilation to `/dist/server`
- Frontend: Vite bundling to `/dist`
- Build server before frontend in production

#### 5. Environment Variable Management
- Never commit `.env` to version control
- Provide `.env.example` template
- Use `dotenv` package for loading
- Centralize config in `server/config.ts`

#### 6. Dark Mode Support
```javascript
darkMode: 'class'
```
- Use class-based dark mode strategy
- Apply `dark` class to HTML element
- Use Tailwind dark: variants

### Error Handling

#### Dependency Installation Failures
```bash
# If npm install fails, try:
rm -rf node_modules package-lock.json
npm cache clean --force
npm install
```

#### Port Conflicts
- Check if ports 5173 or 4000 are in use
- Allow configuration via environment variables
- Provide clear error messages

#### Missing Environment Variables
- Validate required variables at startup
- Provide defaults for non-sensitive values
- Fail fast with helpful error messages

### Recovery Strategies

#### 1. Validation Steps
After project creation, run:
```bash
# Verify installations
npm list --depth=0

# Test TypeScript compilation
npx tsc --noEmit

# Test build process
npm run build

# Test dev servers
npm run dev
```

#### 2. Common Issues Checklist
- [ ] All dependencies installed successfully
- [ ] TypeScript compiles without errors
- [ ] Frontend dev server starts on 5173
- [ ] Backend dev server starts on 4000
- [ ] Tailwind CSS processing works
- [ ] ESLint configuration loads
- [ ] Environment variables loaded

## Example Usage

### Scenario 1: Standard Web Application

**Input:**
```json
{
  "projectName": "my-social-platform",
  "description": "A social platform for sharing ideas",
  "productName": "IdeaShare",
  "adminEmail": "admin@ideashere.com",
  "adminPassword": "SecurePassword123!",
  "databasePath": "server/data/auth.sqlite"
}
```

**Output:**
```
✅ Created project directory: my-social-platform/
✅ Generated package.json with 26 dependencies
✅ Generated TypeScript configurations (frontend & backend)
✅ Generated Vite configuration with dev proxy
✅ Generated Tailwind CSS configuration with custom theme
✅ Generated ESLint configuration
✅ Generated .env file with secure defaults
✅ Created directory structure:
   - /src (components, pages, lib, types, mock)
   - /server (routes, types, data)
   - /public
✅ Initialized Git repository
✅ Created .gitignore with comprehensive patterns
✅ Installing dependencies... (this may take a minute)
✅ Dependencies installed successfully

📋 Next Steps:
1. cd my-social-platform
2. Update .env with your actual API keys
3. Run: npm run dev
4. Visit: http://localhost:5173

🤝 Handing off to:
   - Database Architect (schema creation)
   - Backend Engineer (API implementation)
   - Frontend Engineer (UI development)
```

### Scenario 2: Minimal Setup (No Email Service)

**Input:**
```json
{
  "projectName": "simple-qa-site",
  "description": "Simple Q&A community",
  "productName": "SimpleQA",
  "adminEmail": "admin@localhost",
  "adminPassword": "admin123",
  "skipEmailConfig": true
}
```

**Output:**
```
✅ Project created: simple-qa-site/
⚠️  Email service skipped - BREVO_API_KEY not configured
✅ All core dependencies installed
✅ Build tools configured
⚠️  Email verification features will be disabled

Run: npm run dev to start development
```

## Validation Checklist

- [ ] package.json created with exact dependency versions
- [ ] TypeScript configurations generated (root and server/)
- [ ] Vite config includes proxy to backend
- [ ] Tailwind config includes custom colors and dark mode
- [ ] ESLint config includes React and TypeScript rules
- [ ] .env file created with all required variables
- [ ] .gitignore includes database files and .env
- [ ] Directory structure matches template exactly
- [ ] index.html includes Google Fonts preconnect
- [ ] PostCSS configured for Tailwind processing
- [ ] Git repository initialized (if requested)
- [ ] All npm scripts functional (dev, build, lint)
- [ ] Dependencies installed without errors
- [ ] TypeScript compilation successful
- [ ] Dev servers can start without errors
- [ ] Documentation generated (README.md)

## Initialization Script

```bash
#!/bin/bash
# Project Architect Automation Script

PROJECT_NAME=$1
PRODUCT_NAME=$2
ADMIN_EMAIL=$3

echo "🏗️  Project Architect Agent - Initializing $PROJECT_NAME"

# Create directory structure
mkdir -p "$PROJECT_NAME"/{src/{components,pages,lib,types,mock},server/{routes,types,data},public}

# Generate all configuration files
# (Templates would be written here)

# Install dependencies
cd "$PROJECT_NAME"
npm install

# Initialize git
git init
git add .
git commit -m "Initial project setup by Project Architect Agent"

echo "✅ Project architecture complete!"
echo "📂 Location: $(pwd)"
echo "🚀 Run 'npm run dev' to start development"
```

## Context Requirements

### Required Files
```
/agents/tech-stack-reference.md    # Tech stack specifications
/agents/tech-stack-config.md       # Configuration templates
package.json templates             # Dependency versions
README.md templates                # Documentation templates
```

### Optional Context
```
/examples/                         # Example project structures
.env.example                       # Environment variable templates
/templates/                        # Additional templates if available
```

### Exclude Patterns
```
node_modules/**                    # No existing dependencies needed
dist/**                            # No build outputs needed
*.sqlite                           # No database files needed
*.log                              # No logs needed
coverage/**                        # No test coverage needed
.git/**                            # No git history needed
```

## Context Loading Strategy

### Phase 1: Load Configuration Templates
1. Load tech stack reference for exact versions
2. Load configuration file templates (tsconfig, vite.config, etc.)
3. Load package.json dependency list

### Phase 2: Project Structure Creation
1. Create directory structure from template
2. No context needed - structure is predefined
3. Generate files from templates with variable substitution

### Phase 3: Validation
1. No code context needed
2. Validate file generation completeness
3. Run npm install (external operation)

### Phase 4: Handoff Preparation
1. Document created structure
2. Prepare handoff information
3. No additional context needed

## MCP Tools Used
None - Project Architect creates file structure and doesn't interact with browser or external tools

## State Management

### Project Creation State
```json
{
  "current_project": {
    "name": "my-social-platform",
    "status": "scaffolding",
    "phase": "dependency_installation",
    "files_created": 15,
    "directories_created": 8,
    "dependencies_installed": false,
    "started_at": "2025-01-15T10:00:00Z"
  },
  "validation_checks": {
    "directory_structure": true,
    "config_files": true,
    "package_json": true,
    "typescript_configs": true,
    "env_file": true,
    "gitignore": true
  }
}
```

### Agent Statistics
```json
{
  "projects_created": 42,
  "avg_creation_time_seconds": 45,
  "success_rate": 0.98,
  "most_common_issues": [
    "npm install failures due to network",
    "port conflicts on dev machines"
  ]
}
```

---

**Agent Version**: 1.0
**Based on**: reddit-quora-web-site codebase
**Last Updated**: 2025-11-16
**Compatible with**: React 18.x, TypeScript 5.x, Vite 5.x, Express 5.x
