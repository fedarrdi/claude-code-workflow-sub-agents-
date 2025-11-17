# Agent Workflow Guide

How to use the specialized agent system to build new websites using the reddit-quora-web-site tech stack.

## Quick Start

### Step 1: Describe Your Website

Fill out the `website-prompt-template.md` with your project details:
- Project name
- Description
- Key features
- User types
- Data models
- Pages needed

### Step 2: Activate the Orchestrator

The Orchestrator agent will manage all other agents in the correct sequence.

**Command**:
```
Please build the website described in website-prompt-template.md using all specialized agents.
```

### Step 3: Wait for Completion

The Orchestrator will:
1. Validate your requirements
2. Activate agents in correct order
3. Verify each agent's output
4. Report progress
5. Deliver final working application

## Manual Agent Activation (Advanced)

If you prefer to run agents individually:

### 1. Project Architect Agent

**When to use**: Starting a new project

**Input**:
```json
{
  "projectName": "my-app",
  "description": "A social platform",
  "productName": "MyApp",
  "adminEmail": "admin@example.com",
  "adminPassword": "SecurePass123!"
}
```

**Command**:
```
Please run the Project Architect agent to create a new project called "my-app" for a social platform.
```

**Output**: Complete project structure with dependencies installed

---

### 2. DevOps Engineer Agent

**When to use**: After project creation, before coding

**Command**:
```
Please run the DevOps Engineer agent to configure environment variables and deployment settings.
```

**Output**: `.env` file configured, CORS setup, build scripts ready

---

### 3. Database Architect Agent

**When to use**: After environment setup

**Input**:
```json
{
  "tables": [
    {
      "name": "posts",
      "columns": [
        { "name": "id", "type": "INTEGER PRIMARY KEY AUTOINCREMENT" },
        { "name": "title", "type": "TEXT NOT NULL" },
        { "name": "content", "type": "TEXT" },
        { "name": "user_id", "type": "INTEGER NOT NULL" }
      ]
    }
  ]
}
```

**Command**:
```
Please run the Database Architect agent to create a database schema for posts and users.
```

**Output**: `server/db.ts` with schema, migrations, and admin seeding

---

### 4. API Designer Agent

**When to use**: After database design

**Input**:
```
I need REST API endpoints for:
- User authentication (register, login)
- Posts (create, read, update, delete)
- Comments (create, read, delete)
```

**Command**:
```
Please run the API Designer agent to create API specifications for user authentication, posts, and comments.
```

**Output**: Complete API specification with request/response formats

---

### 5. Backend Engineer Agent

**When to use**: After API design

**Command**:
```
Please run the Backend Engineer agent to implement all API endpoints defined in the specification.
```

**Output**: Working Express server with all routes implemented

---

### 6. Frontend Engineer Agent

**When to use**: Can run in parallel with Backend Engineer

**Input**:
```
I need the following pages:
- Home page with post feed
- Post detail page
- Create post page
- User profile page
- Login/signup modal
```

**Command**:
```
Please run the Frontend Engineer agent to create all pages and components for the blog platform.
```

**Output**: Complete React application with routing and API integration

---

### 7. Quality Assurance Agent

**When to use**: After backend and frontend complete

**Command**:
```
Please run the Quality Assurance agent to check code quality and security.
```

**Output**: Validation report with any issues found

---

## Agent Communication Pattern

### Example Conversation Flow

**User**:
```
I want to build a recipe sharing website where users can:
- Create accounts
- Post recipes
- Rate recipes
- Save favorites
```

**Assistant (Orchestrator)**:
```
I'll coordinate all agents to build your recipe sharing website.

Activating Project Architect...
✅ Project structure created
✅ Dependencies installed
✅ Build tools configured

Activating DevOps Engineer...
✅ Environment variables configured
✅ CORS setup for localhost
✅ Build scripts ready

Activating Database Architect...
✅ Created users table
✅ Created recipes table
✅ Created ratings table
✅ Created favorites table
✅ Seeded admin account

Activating API Designer...
✅ Designed authentication endpoints
✅ Designed recipe CRUD endpoints
✅ Designed rating endpoints
✅ Designed favorites endpoints

Activating Backend Engineer...
✅ Implemented all API routes
✅ Added JWT authentication
✅ Validated all inputs

Activating Frontend Engineer...
✅ Created HomePage with recipe feed
✅ Created RecipeDetailPage
✅ Created CreateRecipePage
✅ Created ProfilePage
✅ Integrated API calls
✅ Added dark mode

Running integration tests...
✅ All user flows tested
✅ Authentication working
✅ CRUD operations verified

Project complete! Your recipe sharing website is ready.

Run: npm run dev
Visit: http://localhost:5173
```

## Common Workflows

### Workflow 1: Simple Blog

```
1. Project Architect → Create project
2. DevOps Engineer → Configure environment
3. Database Architect → Create posts, users tables
4. API Designer → Design blog API
5. Backend Engineer → Implement API
6. Frontend Engineer → Build blog UI
7. Quality Assurance → Validate
```

**Time Estimate**: 2-3 hours (for AI agent execution)

---

### Workflow 2: Social Platform

```
1. Project Architect → Create project
2. DevOps Engineer → Configure environment
3. Database Architect → Create users, posts, comments, likes tables
4. API Designer → Design social API with auth
5. Backend Engineer → Implement API + email verification
6. Frontend Engineer → Build feed, profiles, notifications
7. Quality Assurance → Security audit
```

**Time Estimate**: 4-6 hours

---

### Workflow 3: E-commerce Site

```
1. Project Architect → Create project
2. DevOps Engineer → Configure environment
3. Database Architect → Create products, orders, cart tables
4. API Designer → Design shop API
5. Backend Engineer → Implement API + payment integration
6. Frontend Engineer → Build shop, cart, checkout
7. Quality Assurance → Security & payment testing
```

**Time Estimate**: 6-8 hours

---

## Validation Checkpoints

### After Project Architect
- [ ] `npm install` works
- [ ] `npm run dev` starts servers
- [ ] No TypeScript errors

### After DevOps Engineer
- [ ] `.env` file exists
- [ ] All secrets set
- [ ] `.env` in `.gitignore`

### After Database Architect
- [ ] `server/db.ts` exists
- [ ] Tables created
- [ ] Admin account works

### After API Designer
- [ ] API spec document exists
- [ ] All endpoints defined
- [ ] Types exported

### After Backend Engineer
- [ ] Health check responds: `curl localhost:4000/api/health`
- [ ] Registration works
- [ ] Login returns JWT

### After Frontend Engineer
- [ ] Homepage loads: http://localhost:5173
- [ ] Auth modal appears
- [ ] Navigation works

### Final Validation
- [ ] Complete user flow tested
- [ ] Production build works: `npm run build`
- [ ] No linting errors: `npm run lint`

## Troubleshooting

### Issue: Agent gives incomplete output

**Solution**: Provide more specific requirements
```
Bad:  "Create a blog"
Good: "Create a blog with user authentication, posts (title, content, author),
       comments, and a dark mode UI"
```

### Issue: Agents conflict

**Solution**: Follow the orchestrator sequence. Never skip agents.

### Issue: Build fails

**Solution**:
1. Check all agents completed
2. Verify `.env` configured
3. Run `npm install` again
4. Check Node version (should be 18+)

## Best Practices

1. **Always start with Project Architect** - Never skip project setup
2. **Use the Orchestrator** - Let it manage agent sequence
3. **Provide detailed requirements** - More detail = better output
4. **Validate at each step** - Check agent output before continuing
5. **Test incrementally** - Don't wait until the end to test
6. **Keep tech stack consistent** - All agents use same stack

## Next Steps

After your website is built:

1. Customize styling (Tailwind classes)
2. Add your branding
3. Configure email service (Brevo)
4. Set production environment variables
5. Deploy to hosting platform
6. Set up monitoring

## Context Requirements

### Required Files
- All agent documentation files in `/agents` directory
- `website-prompt-template.md` - For understanding user input format
- `SPECIFICATION.md` (when created) - For understanding project requirements

### Optional Context
- Example project specifications
- Previous successful agent executions

### Exclude Patterns
- `node_modules/**`
- `dist/**`
- `.git/**`

## Context Loading Strategy

### Phase 1: Workflow Understanding
Read workflow documentation:
- Understand agent activation sequence
- Review dependencies between agents
- Learn validation checkpoints

### Phase 2: Agent Coordination
Reference agent-specific documentation:
- Load individual agent capabilities
- Understand input/output formats
- Review handoff points between agents

### Phase 3: Execution Planning
Plan project execution:
- Map user requirements to agent sequence
- Identify parallel vs sequential execution
- Plan validation at each step

## MCP Tools Used
None - This is a workflow guide document, not an executable agent. The Orchestrator agent uses this as reference material.

## State Management

### Workflow Guide State
```json
{
  "documentType": "workflow guide",
  "purpose": "explain agent coordination process",
  "targetAudience": ["developers", "orchestrator agent"],
  "maintained": true
}
```

### Agent Statistics
```json
{
  "notApplicable": "This is a workflow guide document, not an executable agent"
}
```

---

**Version**: 1.0
**Based on**: reddit-quora-web-site agent system
**Last Updated**: 2025-11-16
