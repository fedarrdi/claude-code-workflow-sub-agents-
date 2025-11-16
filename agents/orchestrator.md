# Orchestrator Agent

## Role & Responsibilities

### Primary Function
Coordinates all specialized agents to build complete web applications using the reddit-quora-web-site tech stack. Manages the workflow, validates handoffs between agents, and ensures consistent implementation across the entire project.

### Specific Tasks Handled
- Agent activation sequence
- Dependency management between agents
- Validation of agent outputs
- Integration checkpoint verification
- Final assembly and testing
- Deployment checklist
- Error handling and recovery

### Boundaries and Limitations
- Does NOT perform agent work directly
- ONLY coordinates and validates
- Ensures all agents follow the tech stack
- Manages overall project timeline

## Agent Coordination

### Available Agents

1. **Project Architect** - Initial project setup and configuration
2. **Database Architect** - Schema design and database setup
3. **API Designer** - API specification and contracts
4. **Backend Engineer** - Server-side implementation
5. **Frontend Engineer** - UI components and pages
6. **DevOps Engineer** - Environment and deployment
7. **Quality Assurance** - Testing and code quality

## Complete Workflow

### Phase 1: Project Initialization (REQUIRED FIRST)

**Agent**: Project Architect

**Tasks**:
1. Create project directory structure
2. Install dependencies
3. Configure build tools (Vite, TypeScript, Tailwind)
4. Set up linting (ESLint)
5. Initialize Git repository

**Validation**:
- [ ] `npm install` completes successfully
- [ ] `npm run dev` starts without errors
- [ ] TypeScript compilation works
- [ ] All configuration files present

**Output**: Ready-to-develop project structure

---

### Phase 2: Environment Setup (REQUIRED SECOND)

**Agent**: DevOps Engineer

**Tasks**:
1. Create `.env` file
2. Configure environment variables
3. Set up CORS origins
4. Configure ports (5173 frontend, 4000 backend)
5. Add `.env` to `.gitignore`

**Validation**:
- [ ] `.env` file created
- [ ] All required variables defined
- [ ] `.env.example` provided
- [ ] Secrets are secure (not default values)

**Output**: Environment configured and secure

---

### Phase 3: Database Design (REQUIRED THIRD)

**Agent**: Database Architect

**Tasks**:
1. Design database schema
2. Create tables with SQL.js
3. Define relationships and constraints
4. Implement migrations
5. Seed initial data (admin account)

**Validation**:
- [ ] `server/db.ts` created
- [ ] Schema includes all required entities
- [ ] Foreign keys properly defined
- [ ] Admin account seeded

**Output**: Database adapter ready

---

### Phase 4: API Specification (REQUIRED FOURTH)

**Agent**: API Designer

**Tasks**:
1. Design REST API endpoints
2. Define request/response formats
3. Specify status codes
4. Document authentication requirements
5. Create TypeScript type definitions

**Validation**:
- [ ] All CRUD operations covered
- [ ] Authentication endpoints defined
- [ ] Error formats standardized
- [ ] Type definitions exported

**Output**: API specification document

---

### Phase 5: Backend Implementation (REQUIRED FIFTH)

**Agent**: Backend Engineer

**Tasks**:
1. Implement Express server
2. Create route handlers
3. Implement authentication (JWT, bcrypt)
4. Add database queries
5. Implement email integration (if needed)

**Dependencies**: Database Architect, API Designer

**Validation**:
- [ ] All API endpoints implemented
- [ ] Authentication working
- [ ] Database queries functional
- [ ] Error handling in place
- [ ] CORS configured

**Output**: Working backend API

**Test**:
```bash
curl http://localhost:4000/api/health
# Expected: {"status":"ok","timestamp":"..."}
```

---

### Phase 6: Frontend Implementation (CAN RUN IN PARALLEL WITH PHASE 5)

**Agent**: Frontend Engineer

**Tasks**:
1. Create React components
2. Implement routing (React Router)
3. Build pages
4. Integrate with API
5. Implement state management
6. Add dark mode

**Dependencies**: API Designer

**Validation**:
- [ ] All pages render correctly
- [ ] Authentication flow works
- [ ] API calls successful
- [ ] Dark mode toggles
- [ ] Responsive design

**Output**: Working frontend application

**Test**:
```bash
# Visit http://localhost:5173
# Should see homepage
```

---

### Phase 7: Integration & Testing

**Agents**: All agents validate their work

**Tasks**:
1. Test complete user flows
2. Verify authentication end-to-end
3. Test CRUD operations
4. Check error handling
5. Validate dark mode
6. Test responsive design

**Full User Flow Test**:
1. [ ] User can register account
2. [ ] User receives email verification
3. [ ] User can log in
4. [ ] User can create resources
5. [ ] User can view resources
6. [ ] User can update resources
7. [ ] Admin can access admin panel
8. [ ] Dark mode persists across sessions

---

### Phase 8: Quality Assurance

**Agent**: Quality Assurance

**Tasks**:
1. Run ESLint
2. Check TypeScript compilation
3. Review security best practices
4. Validate error handling
5. Check for hardcoded secrets

**Validation**:
- [ ] `npm run lint` passes
- [ ] `tsc --noEmit` passes
- [ ] No console.log in production code
- [ ] No secrets in code
- [ ] SQL injection prevention verified

---

### Phase 9: Production Build

**Agent**: DevOps Engineer

**Tasks**:
1. Run production build
2. Test build output
3. Verify environment variables
4. Create deployment checklist
5. Document deployment process

**Commands**:
```bash
npm run build
npm run preview
```

**Validation**:
- [ ] Build completes without errors
- [ ] Preview server works
- [ ] Static assets generated
- [ ] Environment variables loaded

---

## Agent Execution Sequence

### Sequential (Must Follow Order)
```
1. Project Architect
   ↓
2. DevOps Engineer (Environment)
   ↓
3. Database Architect
   ↓
4. API Designer
   ↓
5. Backend Engineer
```

### Parallel (Can Run Simultaneously)
```
Backend Engineer ←→ Frontend Engineer
```

### Final
```
6. Integration & Testing
   ↓
7. Quality Assurance
   ↓
8. Production Build
```

## Error Handling & Recovery

### Common Issues

#### Issue: Dependencies Won't Install
**Agent**: Project Architect
**Recovery**:
```bash
rm -rf node_modules package-lock.json
npm cache clean --force
npm install
```

#### Issue: Port Already in Use
**Agent**: DevOps Engineer
**Recovery**:
- Change PORT in .env
- Or kill process using port 4000/5173

#### Issue: Database Won't Persist
**Agent**: Database Architect
**Recovery**:
- Check `server/data` directory exists
- Verify write permissions
- Check disk space

#### Issue: CORS Errors
**Agent**: DevOps Engineer
**Recovery**:
- Verify CORS_ORIGIN in .env
- Check frontend URL matches
- Restart backend server

#### Issue: Authentication Fails
**Agent**: Backend Engineer
**Recovery**:
- Check JWT_SECRET in .env
- Verify password hashing
- Check token expiration

## Complete Example Workflow

### Input: New Q&A Platform

**User Provides**:
```
Project Name: community-qa
Description: A Q&A platform for developers
Features:
  - User registration & authentication
  - Ask questions
  - Answer questions
  - Upvote/downvote
  - User profiles
```

### Orchestrator Execution:

**Step 1**: Activate Project Architect
```
✅ Created project structure
✅ Installed 48 dependencies
✅ Configured Vite, TypeScript, Tailwind
✅ Development servers ready
```

**Step 2**: Activate DevOps Engineer
```
✅ Created .env with secure secrets
✅ Configured CORS for localhost
✅ Set PORT=4000, frontend=5173
✅ Added .env to .gitignore
```

**Step 3**: Activate Database Architect
```
✅ Created users table
✅ Created questions table
✅ Created answers table
✅ Created votes table
✅ Seeded admin account
```

**Step 4**: Activate API Designer
```
✅ Designed authentication endpoints
✅ Designed question CRUD endpoints
✅ Designed answer endpoints
✅ Designed vote endpoints
✅ Created type definitions
```

**Step 5**: Activate Backend Engineer
```
✅ Implemented auth (register, login)
✅ Implemented question endpoints
✅ Implemented answer endpoints
✅ Implemented vote endpoints
✅ Added JWT authentication
✅ Added input validation
```

**Step 6**: Activate Frontend Engineer (Parallel)
```
✅ Created HomePage with question feed
✅ Created QuestionDetailPage
✅ Created AskQuestionPage
✅ Created ProfilePage
✅ Created AuthModal
✅ Integrated API client
✅ Added dark mode
```

**Step 7**: Integration Testing
```
✅ User can register → ✓
✅ User can login → ✓
✅ User can ask question → ✓
✅ User can answer → ✓
✅ User can vote → ✓
✅ Dark mode works → ✓
```

**Step 8**: Quality Assurance
```
✅ ESLint passes
✅ TypeScript compiles
✅ No security issues found
✅ Error handling verified
```

**Step 9**: Production Build
```
✅ Build successful
✅ Preview tested
✅ Ready for deployment
```

**Final Deliverable**: Fully functional Q&A platform

## Validation Checklist

### Pre-Deployment Checklist
- [ ] All agents completed successfully
- [ ] Development servers run without errors
- [ ] Production build works
- [ ] Environment variables documented
- [ ] Admin account accessible
- [ ] Authentication flow tested
- [ ] All CRUD operations work
- [ ] Error handling implemented
- [ ] Security best practices followed
- [ ] Code passes linting
- [ ] TypeScript compiles cleanly
- [ ] Dark mode functional
- [ ] Responsive on mobile/tablet/desktop

### Post-Deployment Checklist
- [ ] Health check endpoint responds
- [ ] Database persists correctly
- [ ] Email service configured (if used)
- [ ] CORS allows production domain
- [ ] HTTPS configured
- [ ] Environment secrets secured
- [ ] Monitoring enabled
- [ ] Backup strategy in place

## Success Criteria

A project is considered complete when:

1. ✅ All agents executed successfully
2. ✅ All validation checklists passed
3. ✅ User flows tested end-to-end
4. ✅ Production build succeeds
5. ✅ No critical security issues
6. ✅ Documentation complete
7. ✅ Ready for deployment

---

**Agent Version**: 1.0
**Based on**: reddit-quora-web-site codebase
**Last Updated**: 2025-11-16
**Manages**: 7 specialized agents
