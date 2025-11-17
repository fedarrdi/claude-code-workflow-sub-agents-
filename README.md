# Claude Agents - Full-Stack Web Application Builder

An intelligent multi-agent system that transforms your website ideas into complete, production-ready web applications.

## 🎯 What Is This?

This is a collection of specialized AI agents that work together to build full-stack web applications from just a description. Each agent is an expert in one area (database design, backend development, frontend development, etc.) and they collaborate to create complete, working applications.

## ⚡ Quick Start

### 1. Describe Your Idea to Product Analyst

Start by invoking the **Product Analyst** agent with your website idea:

```
@product-analyst I want to build a recipe sharing website where users can post recipes and rate each other's recipes
```

The Product Analyst will:
- Ask clarifying questions about your requirements
- Create a complete technical specification (SPECIFICATION.md)
- Define database schema, API endpoints, and UI pages

### 2. Run the Orchestrator

Once you have SPECIFICATION.md, invoke the **Orchestrator**:

```
@orchestrator Build the application based on SPECIFICATION.md
```

The Orchestrator will:
- Read your specification
- Coordinate all specialized agents in the correct order
- Build your complete application
- Validate everything works

### 3. Your App is Ready!

```bash
npm run dev
# Frontend: http://localhost:5173
# Backend: http://localhost:4000
```

## 📋 Complete Agent List

### Core Agents (Required)

| Agent | Purpose | When to Use |
|-------|---------|-------------|
| **Product Analyst** | Converts ideas → specifications | ALWAYS START HERE |
| **Orchestrator** | Coordinates all agents | After Product Analyst completes |
| **Project Architect** | Creates project structure | Called by Orchestrator |
| **Database Architect** | Designs database schema | Called by Orchestrator |
| **API Designer** | Specifies API endpoints | Called by Orchestrator |
| **Backend Engineer** | Implements server code | Called by Orchestrator |
| **Frontend Engineer** | Builds UI components | Called by Orchestrator |
| **DevOps Engineer** | Configures environment | Called by Orchestrator |
| **Quality Assurance** | Validates code quality | Called by Orchestrator |

### Enhancement Agents (Optional)

| Agent | Purpose | When to Use |
|-------|---------|-------------|
| **Premium UI Designer** | Polishes UI design | After frontend is complete |
| **Design Review Agent** | Reviews UI/UX with Playwright | When you have preview URL |

## 🏗️ Architecture Overview

```
┌─────────────────────────────────────────────────────────┐
│ YOU: Describe your website idea                        │
└────────────────┬────────────────────────────────────────┘
                 ↓
┌─────────────────────────────────────────────────────────┐
│ PRODUCT ANALYST                                         │
│ • Asks clarifying questions                             │
│ • Creates SPECIFICATION.md                              │
└────────────────┬────────────────────────────────────────┘
                 ↓
          SPECIFICATION.md
                 ↓
┌─────────────────────────────────────────────────────────┐
│ ORCHESTRATOR                                            │
│ • Reads specification                                   │
│ • Invokes agents in order                               │
│ • Validates each step                                   │
└────────────────┬────────────────────────────────────────┘
                 ↓
┌──────────────────────────────────────┬──────────────────┐
│ Project Architect                    │ DevOps Engineer  │
│ Creates project structure            │ Sets up .env     │
└──────────────────┬───────────────────┴──────┬───────────┘
                   ↓                           ↓
┌──────────────────────────────────────────────────────────┐
│ Database Architect                                       │
│ Creates database schema                                  │
└────────────────┬─────────────────────────────────────────┘
                 ↓
┌──────────────────────────────────────────────────────────┐
│ API Designer                                             │
│ Specifies all API endpoints                              │
└────────────────┬─────────────────────────────────────────┘
                 ↓
┌──────────────────────┬───────────────────────────────────┐
│ Backend Engineer     │ Frontend Engineer                 │
│ Implements API       │ Builds UI                         │
└──────────────────────┴───────────────────────────────────┘
                 ↓
┌──────────────────────────────────────────────────────────┐
│ Quality Assurance                                        │
│ Validates everything                                     │
└──────────────────────────────────────────────────────────┘
                 ↓
          ✅ COMPLETE WEB APPLICATION
```

## 🛠️ Tech Stack

All agents build applications using this modern stack:

- **Frontend**: React 18 + TypeScript + Tailwind CSS + Vite
- **Backend**: Express 5 + TypeScript + Node.js
- **Database**: SQL.js (SQLite in WebAssembly)
- **Auth**: JWT + bcrypt
- **Dev Tools**: tsx, ESLint, concurrently
- **Optional**: Brevo email service

**Full details**: See `agents/tech-stack-config.md`

## 📂 File Structure

```
claude-agents/
├── agents/                   # Agent definitions
│   ├── product-analyst.md    # Requirements gathering
│   ├── orchestrator.md       # Agent coordination
│   ├── tech-stack-config.md  # Tech stack specification
│   ├── project-architect.md
│   ├── database-architect.md
│   ├── api-designer.md
│   ├── backend-engineer.md
│   ├── frontend-engineer.md
│   ├── devops-engineer.md
│   ├── quality-assurance.md
│   ├── premium-ui-designer.md
│   └── design-review-agent.md
├── examples/                 # Example specifications
│   ├── blog-platform-spec.md
│   ├── ecommerce-spec.md
│   └── social-platform-spec.md
├── templates/                # Templates for agents
│   └── SPECIFICATION_TEMPLATE.md
├── README.md                 # This file
└── AGENTS.md                 # Agent registry

Your Generated Project:
project-name/
├── src/                      # Frontend source
├── server/                   # Backend source
├── SPECIFICATION.md          # Your project spec
├── package.json
├── .env
└── ...
```

## 🎓 How It Works

### Phase 1: Requirements Gathering (Interactive)

The **Product Analyst** agent engages with you:

1. You describe your idea (brief or detailed)
2. Agent analyzes what's clear vs unclear
3. **Agent asks 8-12 clarifying questions**
4. You answer the questions
5. Agent creates complete SPECIFICATION.md

**Example Questions:**
- Do users need to create accounts?
- What information should [entity] contain?
- Who can view/edit/delete content?
- What are the top 3 must-have features?

### Phase 2: Automated Build (Hands-off)

The **Orchestrator** manages everything:

```
[1/7] Project Architect     ✅ Project structure created
[2/7] DevOps Engineer       ✅ Environment configured
[3/7] Database Architect    ✅ Database schema ready
[4/7] API Designer          ✅ API specification done
[5/7] Backend Engineer      ✅ All endpoints implemented
[6/7] Frontend Engineer     ✅ All pages/components built
[7/7] Quality Assurance     ✅ Code quality validated
```

### Phase 3: Validation & Testing

The Orchestrator runs:
- ✅ Type checking (TypeScript)
- ✅ Linting (ESLint)
- ✅ Security checks
- ✅ End-to-end user flow tests
- ✅ Production build test

## 💡 Example Usage

### Example 1: Simple Blog

```
You: @product-analyst I want a blog where I can post articles

Product Analyst: Great idea! Let me clarify a few details:
  1. Single author or multiple authors?
  2. Can readers comment?
  3. Do you need categories?
  ... (8 more questions)

You: [Answer questions]

Product Analyst: ✅ Created SPECIFICATION.md

You: @orchestrator Build it

Orchestrator:
  ✅ Project structure created
  ✅ Database ready
  ✅ API implemented
  ✅ UI built
  🎉 Blog platform complete!
```

## 📖 Documentation

- **`agents/`** - Detailed agent documentation
- **`AGENTS.md`** - Agent registry and quick reference
- **`examples/`** - Example specifications
- **`templates/`** - Template files

## 🚀 Commands Reference

### Agents
```
@product-analyst [your idea]     # Start with requirements
@orchestrator                    # Build from SPECIFICATION.md
@design-review [URL]             # Review UI/UX (optional)
```

### Development (after build)
```bash
npm install           # Install dependencies
npm run dev           # Start dev servers
npm run lint          # Run ESLint
npm run build         # Build for production
```

## ✅ What Gets Built

Every application includes:

- ✅ User authentication (register, login, JWT)
- ✅ Database with your custom schema
- ✅ RESTful API with all endpoints
- ✅ React UI with all pages/components
- ✅ Dark mode support
- ✅ Responsive design (mobile/tablet/desktop)
- ✅ Admin panel
- ✅ Input validation
- ✅ Error handling
- ✅ Loading states
- ✅ TypeScript throughout
- ✅ Production-ready build

## 🔧 Troubleshooting

| Issue | Solution |
|-------|----------|
| "SPECIFICATION.md not found" | Run Product Analyst first |
| "Port already in use" | Change PORT in `.env` |
| "Agent failed" | Check error, fix, resume from that agent |
| "Database won't persist" | Check `server/data/` exists |
| "CORS errors" | Verify CORS_ORIGIN in `.env` |

## 🎯 Best Practices

1. **Always start with Product Analyst** - Don't skip requirements
2. **Answer questions thoroughly** - More detail = better output
3. **Review SPECIFICATION.md** - Before running Orchestrator
4. **Let Orchestrator finish** - Don't interrupt the build
5. **Test incrementally** - After completion, test each feature

## 🔐 Security

**Built-in Security:**
- ✅ Passwords hashed (bcrypt, 12 rounds)
- ✅ JWT tokens with expiration
- ✅ SQL injection prevention
- ✅ Input validation
- ✅ CORS configuration
- ✅ No secrets in code

**After Build**: Change admin password in `.env`!

## 🆘 Need Help?

1. Check `AGENTS.md` for agent descriptions
2. See `examples/` for sample specifications
3. Review agent docs in `agents/` directory

---

**Version**: 1.0
**Last Updated**: 2025-11-16
**Agents**: 11 specialized agents

**Start building**: `@product-analyst I want to build...`
