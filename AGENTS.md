# Agent Registry

Quick reference guide for all available agents in the system.

## How to Use This Guide

Each agent entry includes:
- **Purpose**: What the agent does
- **When to Use**: When to invoke this agent
- **Input**: What the agent needs
- **Output**: What the agent produces
- **Dependencies**: Which agents must run before this one

---

## 🎯 Core Workflow Agents

### 1. Product Analyst

**File**: `agents/product-analyst.md`

**Purpose**: Converts user ideas into complete technical specifications

**When to Use**: **ALWAYS START HERE** - First step for every new project

**How to Invoke**:
```
@product-analyst I want to build [your idea]
```

**Input**:
- Your website idea (brief or detailed)
- Answers to clarifying questions

**Output**:
- `SPECIFICATION.md` - Complete technical specification including:
  - Project overview
  - Database schema
  - API endpoints
  - UI pages/components
  - User flows
  - Agent execution plan

**Workflow**:
1. Receives your idea
2. Analyzes what's clear vs unclear
3. Asks 8-12 clarifying questions
4. Synthesizes your answers
5. Creates SPECIFICATION.md

**Dependencies**: None (runs first)

**Key Feature**: Interactive - will always ask questions before creating spec

---

### 2. Orchestrator

**File**: `agents/orchestrator.md`

**Purpose**: Coordinates all specialized agents to build the complete application

**When to Use**: After Product Analyst creates SPECIFICATION.md

**How to Invoke**:
```
@orchestrator Build the application from SPECIFICATION.md
```

**Input**:
- `SPECIFICATION.md` (from Product Analyst)
- `agents/tech-stack-config.md` (tech stack reference)

**Output**:
- Complete working application
- Progress reports for each agent
- Final validation report

**Workflow**:
1. Reads and validates SPECIFICATION.md
2. Extracts sections for each agent
3. Invokes agents in sequence:
   - Project Architect
   - DevOps Engineer
   - Database Architect
   - API Designer
   - Backend Engineer
   - Frontend Engineer (parallel with Backend)
   - Quality Assurance
4. Validates each step
5. Runs integration tests

**Dependencies**: Product Analyst (needs SPECIFICATION.md)

**Key Feature**: Fully automated after invocation - builds entire app

---

## 🔧 Implementation Agents

These agents are called by the Orchestrator. You typically don't invoke them directly.

### 3. Project Architect

**File**: `agents/project-architect.md`

**Purpose**: Creates initial project structure and configuration

**When to Use**: Called by Orchestrator (Step 1)

**Input**:
- Project name
- Project description
- Tech stack specification

**Output**:
- Complete project directory structure
- `package.json` with all dependencies
- TypeScript configurations (`tsconfig.json`, `server/tsconfig.json`)
- Vite configuration (`vite.config.ts`)
- Tailwind configuration (`tailwind.config.js`)
- ESLint configuration (`.eslintrc.cjs`)
- Git repository initialized
- All dependencies installed

**Dependencies**: None

---

### 4. DevOps Engineer

**File**: `agents/devops-engineer.md`

**Purpose**: Configures environment variables and deployment settings

**When to Use**: Called by Orchestrator (Step 2)

**Input**:
- Environment variables list from SPECIFICATION.md
- Project name

**Output**:
- `.env` file with all required variables
- `.env.example` template file
- `.gitignore` updated
- CORS configuration
- Port configuration

**Dependencies**: Project Architect

---

### 5. Database Architect

**File**: `agents/database-architect.md`

**Purpose**: Designs and implements database schema

**When to Use**: Called by Orchestrator (Step 3)

**Input**:
- Database schema from SPECIFICATION.md
- Entity definitions
- Relationships and constraints

**Output**:
- `server/db.ts` - Database adapter implementation
- `server/data/*.sqlite` - SQLite database file
- All tables created with constraints
- Foreign keys configured
- Indexes created
- Admin account seeded

**Dependencies**: DevOps Engineer (needs .env)

**Key Patterns**:
- SQL.js with file persistence
- Prepared statements (SQL injection prevention)
- Safe migrations with column existence checks

---

### 6. API Designer

**File**: `agents/api-designer.md`

**Purpose**: Creates comprehensive API specification

**When to Use**: Called by Orchestrator (Step 4)

**Input**:
- API endpoints list from SPECIFICATION.md
- Database schema
- Authentication requirements

**Output**:
- `API_SPECIFICATION.md` - Complete API documentation including:
  - All endpoint paths and methods
  - Request/response schemas
  - Status codes
  - Authentication requirements
  - Example requests/responses
- TypeScript type definitions

**Dependencies**: Database Architect

---

### 7. Backend Engineer

**File**: `agents/backend-engineer.md`

**Purpose**: Implements all backend functionality

**When to Use**: Called by Orchestrator (Step 5)

**Input**:
- `API_SPECIFICATION.md` (from API Designer)
- Database schema (from Database Architect)
- Business logic requirements

**Output**:
- `server/index.ts` - Express server
- `server/routes/*.ts` - All API route handlers
- Authentication middleware
- JWT token generation
- Password hashing (bcrypt)
- Input validation
- Error handling
- CORS configuration

**Dependencies**: Database Architect, API Designer

**Can Run in Parallel With**: Frontend Engineer

---

### 8. Frontend Engineer

**File**: `agents/frontend-engineer.md`

**Purpose**: Builds all UI pages and components

**When to Use**: Called by Orchestrator (Step 6)

**Input**:
- UI pages/components list from SPECIFICATION.md
- `API_SPECIFICATION.md` (for API integration)
- User flows

**Output**:
- `src/pages/*.tsx` - All page components
- `src/components/*.tsx` - All reusable components
- `src/lib/api.ts` - API client
- `src/App.tsx` - Root component with routing
- `src/main.tsx` - Application entry point
- Dark mode implementation
- Responsive design (mobile/tablet/desktop)

**Dependencies**: API Designer

**Can Run in Parallel With**: Backend Engineer

---

### 9. Quality Assurance

**File**: `agents/quality-assurance.md`

**Purpose**: Validates code quality and security

**When to Use**: Called by Orchestrator (Step 7)

**Input**:
- Complete codebase
- Success criteria from SPECIFICATION.md

**Output**:
- ESLint validation report
- TypeScript compilation report
- Security checklist validation
- Code quality report

**Dependencies**: All implementation agents

**Checks**:
- ESLint passes
- TypeScript compiles
- No hardcoded secrets
- SQL injection prevention
- Password hashing correct
- JWT tokens configured
- CORS configured

---

## 🎨 Enhancement Agents

Optional agents for improving the application after core build is complete.

### 10. Premium UI Designer

**File**: `agents/premium-ui-designer.md`

**Purpose**: Enhances UI with animations, transitions, and polish

**When to Use**: After Frontend Engineer completes, when you want premium design

**How to Invoke**:
```
@premium-ui-designer Enhance the UI
```

**Input**:
- Existing frontend code

**Output**:
- Enhanced animations
- Smooth transitions
- Micro-interactions
- Gradient effects
- Hover states
- Loading animations
- Visual polish

**Dependencies**: Frontend Engineer

---

### 11. Design Review Agent

**File**: `agents/design-review-agent.md`

**Purpose**: Comprehensive UI/UX review using Playwright

**When to Use**: When you have a live preview and want professional design review

**How to Invoke**:
```
@design-review http://localhost:5173
```

**Input**:
- Live preview URL
- Frontend codebase

**Output**:
- Design review report with:
  - Critical issues
  - Improvements
  - Nitpicks
- Screenshots of issues
- Accessibility audit
- Responsive design validation

**Dependencies**: Frontend Engineer (needs running application)

**Tools Used**: Playwright MCP for automated testing

---

## 📋 Agent Execution Order

### Standard Build Sequence

```
1. Product Analyst (Manual)
   ↓
2. Orchestrator (Manual) - Coordinates steps 3-9
   ↓
3. Project Architect
   ↓
4. DevOps Engineer
   ↓
5. Database Architect
   ↓
6. API Designer
   ↓
7. Backend Engineer ←→ Frontend Engineer (Parallel)
   ↓
8. Quality Assurance
   ↓
9. [Optional] Premium UI Designer
   ↓
10. [Optional] Design Review Agent
```

### What Runs Automatically vs Manually

**Manual (You Invoke)**:
- Product Analyst
- Orchestrator
- Premium UI Designer (optional)
- Design Review Agent (optional)

**Automatic (Orchestrator Invokes)**:
- Project Architect
- DevOps Engineer
- Database Architect
- API Designer
- Backend Engineer
- Frontend Engineer
- Quality Assurance

---

## 🔍 Quick Reference: Which Agent to Use

### Starting a New Project
→ **Product Analyst**

### Building from Specification
→ **Orchestrator**

### Enhancing UI After Build
→ **Premium UI Designer**

### Reviewing UI/UX Quality
→ **Design Review Agent**

### Troubleshooting an Agent
→ Check the agent's `.md` file in `agents/` directory

### Changing Tech Stack
→ Edit `agents/tech-stack-config.md`

---

## 📊 Agent Capabilities Matrix

| Agent | Asks Questions | Creates Files | Reads Specs | Validates | Runs Tests |
|-------|---------------|---------------|-------------|-----------|------------|
| Product Analyst | ✅ | ✅ | ❌ | ✅ | ❌ |
| Orchestrator | ❌ | ❌ | ✅ | ✅ | ✅ |
| Project Architect | ❌ | ✅ | ✅ | ✅ | ❌ |
| Database Architect | ❌ | ✅ | ✅ | ✅ | ❌ |
| API Designer | ❌ | ✅ | ✅ | ✅ | ❌ |
| Backend Engineer | ❌ | ✅ | ✅ | ✅ | ❌ |
| Frontend Engineer | ❌ | ✅ | ✅ | ✅ | ❌ |
| DevOps Engineer | ❌ | ✅ | ✅ | ✅ | ❌ |
| Quality Assurance | ❌ | ❌ | ✅ | ✅ | ✅ |
| Premium UI Designer | ❌ | ✅ | ✅ | ❌ | ❌ |
| Design Review | ❌ | ✅ | ✅ | ✅ | ✅ |

---

## 🛠️ Agent Files

All agent definitions are in the `agents/` directory:

```
agents/
├── product-analyst.md          # Requirements gathering
├── orchestrator.md             # Agent coordination
├── tech-stack-config.md        # Tech stack specification
├── project-architect.md        # Project setup
├── database-architect.md       # Database design
├── api-designer.md             # API specification
├── backend-engineer.md         # Backend implementation
├── frontend-engineer.md        # Frontend implementation
├── devops-engineer.md          # Environment config
├── quality-assurance.md        # Quality validation
├── premium-ui-designer.md      # UI enhancement
└── design-review-agent.md      # UI/UX review
```

---

## 💡 Tips for Working with Agents

### 1. Always Start with Product Analyst
Don't skip the requirements phase. A complete specification leads to a better application.

### 2. Answer Questions Thoroughly
The more detail you provide to Product Analyst, the better the specification and final product.

### 3. Let Orchestrator Run Fully
Don't interrupt the build process. Let all agents complete their work in sequence.

### 4. Review SPECIFICATION.md Before Building
After Product Analyst creates it, review SPECIFICATION.md to ensure it matches your vision.

### 5. Resume from Failures
If an agent fails, fix the issue and resume from that agent. Don't restart from the beginning.

---

## 📚 Further Reading

- **`README.md`** - Quick start guide
- **`agents/tech-stack-config.md`** - Complete tech stack details
- **`examples/`** - Example specifications
- **`templates/`** - Template files

---

**Version**: 1.0
**Last Updated**: 2025-11-16
**Total Agents**: 11
