# New Website Specification

> Fill out this template and provide it to the Orchestrator agent to build your website using the reddit-quora-web-site tech stack.

## Project Name

**Name**: [lowercase-with-hyphens]

**Display Name**: [Your App Name]

**Description**: [Brief one-sentence description]

---

## Purpose

[What does this website do? What problem does it solve?]

**Example**:
```
A platform for developers to ask technical questions and get expert answers
from the community, similar to Stack Overflow.
```

---

## Key Features

List the main features your website needs:

1. [Feature 1 - e.g., User authentication with email verification]
2. [Feature 2 - e.g., Create and edit posts]
3. [Feature 3 - e.g., Comment system]
4. [Feature 4 - e.g., Search functionality]
5. [Feature 5 - e.g., User profiles]

---

## User Types

Define who will use your website and what they can do:

### [User Type 1 - e.g., Guest Users]
**Capabilities**:
- [Can view posts]
- [Can search content]
- [Cannot create posts]

### [User Type 2 - e.g., Registered Users]
**Capabilities**:
- [All guest capabilities]
- [Can create posts]
- [Can comment]
- [Can like/save content]

### [User Type 3 - e.g., Admins]
**Capabilities**:
- [All registered user capabilities]
- [Can delete any post]
- [Can manage users]
- [Access admin panel]

---

## Data Models

Define the main entities/resources in your application:

### [Entity 1 - e.g., User]
**Fields**:
- id (auto-generated)
- name (string, required)
- email (string, required, unique)
- password (hashed, required)
- role (user/admin)
- created_at (timestamp)
- [Add more fields as needed]

### [Entity 2 - e.g., Post]
**Fields**:
- id (auto-generated)
- title (string, required)
- content (text, required)
- author_id (foreign key to User)
- status (draft/published)
- created_at (timestamp)
- updated_at (timestamp)
- [Add more fields as needed]

### [Entity 3 - e.g., Comment]
**Fields**:
- id (auto-generated)
- content (text, required)
- post_id (foreign key to Post)
- author_id (foreign key to User)
- created_at (timestamp)

[Add more entities as needed]

---

## Pages Needed

List all pages/routes your website needs:

### Public Pages (No login required)
- **/** - [Home/Landing page - Shows featured posts]
- **/posts** - [List all posts]
- **/posts/:id** - [Single post detail page]
- **/about** - [About page]

### Protected Pages (Login required)
- **/dashboard** - [User dashboard]
- **/posts/new** - [Create new post]
- **/posts/:id/edit** - [Edit post]
- **/profile** - [User profile page]
- **/settings** - [User settings]

### Admin Pages (Admin only)
- **/admin** - [Admin panel]
- **/admin/users** - [Manage users]
- **/admin/posts** - [Manage all posts]

[Add more pages as needed]

---

## Special Requirements

### Authentication
- [ ] Email/password authentication
- [ ] Email verification required
- [ ] Password reset functionality
- [ ] Social login (Google, GitHub, etc.) - **Not supported yet**

### Email Notifications
- [ ] Welcome email on signup
- [ ] Email verification code
- [ ] Password reset email
- [ ] Notification when someone comments on my post
- [Add more as needed]

### UI/UX Requirements
- [ ] Dark mode support
- [ ] Responsive design (mobile, tablet, desktop)
- [ ] Accessible (ARIA labels, keyboard navigation)
- [Add more as needed]

### Performance
- [ ] Pagination for long lists
- [ ] Image optimization
- [ ] Lazy loading
- [Add more as needed]

### Security
- [ ] JWT authentication
- [ ] Password hashing (bcrypt)
- [ ] CORS protection
- [ ] SQL injection prevention (prepared statements)
- [ ] XSS prevention
- [Add more as needed]

---

## Additional Features (Optional)

List any extra features not covered above:

- [Feature - e.g., File upload for profile pictures]
- [Feature - e.g., Real-time notifications]
- [Feature - e.g., Search with filters]
- [Feature - e.g., Export data to PDF]

---

## Excluded Features

List features you explicitly DON'T want:

- [No payment integration]
- [No chat/messaging]
- [No social sharing]

---

## Customization

### Colors (Tailwind Theme)
- **Primary Color**: [#FF4500 or keep default]
- **Secondary Color**: [#0079D3 or keep default]
- [Specify custom colors if needed]

### Fonts
- **Body Font**: [Inter or specify alternative]
- **Heading Font**: [Poppins or specify alternative]

### Branding
- **Logo URL**: [URL or "None"]
- **Favicon URL**: [URL or "None"]
- **App Name**: [Your App Name]

---

## Success Criteria

How will you know the project is successful?

- [ ] [Users can register and login]
- [ ] [Users can create and view posts]
- [ ] [Dark mode works]
- [ ] [Responsive on all devices]
- [ ] [Admin can manage content]
- [Add more criteria]

---

## Example Filled Template

For reference, here's what a filled template might look like:

```markdown
# New Website Specification

## Project Name
**Name**: dev-qa-platform
**Display Name**: DevQ&A
**Description**: A Q&A platform for developers

## Purpose
A platform where developers can ask technical questions and receive expert
answers from the community. Features voting, reputation, and topic tags.

## Key Features
1. User registration with email verification
2. Ask and answer questions
3. Upvote/downvote questions and answers
4. Tag questions by topic
5. User reputation system
6. Search questions

## User Types

### Guest Users
**Capabilities**:
- View questions and answers
- Search questions

### Registered Users
**Capabilities**:
- Ask questions
- Answer questions
- Vote on questions/answers
- Earn reputation points

### Moderators
**Capabilities**:
- Edit any question/answer
- Delete inappropriate content
- Manage tags

## Data Models

### User
- id, name, email, password_hash, role
- reputation_points, created_at

### Question
- id, title, content, author_id, status
- views_count, votes_count, created_at

### Answer
- id, content, question_id, author_id
- votes_count, is_accepted, created_at

### Vote
- id, user_id, target_id, target_type (question/answer)
- vote_type (up/down)

## Pages Needed

### Public
- / - Home with question feed
- /questions/:id - Question detail
- /search - Search questions

### Protected
- /ask - Ask new question
- /profile - User profile
- /questions/:id/answer - Answer question

### Admin
- /admin - Moderation dashboard
```

---

## Ready to Build?

Once you've filled out this template, provide it to Claude Code with:

```
Please build the website described in this specification using all specialized
agents from the reddit-quora-web-site tech stack.
```

The Orchestrator will coordinate all agents to build your complete application!

## Context Requirements

### Required Files
None - This is a template for users to fill out. It becomes input for the Product Analyst agent.

### Optional Context
- Example filled templates for reference
- Feature inspiration from existing projects

### Exclude Patterns
Not applicable - this is a user-facing template.

## Context Loading Strategy

### Phase 1: Template Usage
User fills out this template with their website requirements.

### Phase 2: Handoff to Product Analyst
Completed template is provided to Product Analyst agent for analysis.

### Phase 3: Not Applicable
This template doesn't execute; users complete it.

## MCP Tools Used
None - This is a user-facing template document, not an executable agent.

## State Management

### Template Document State
```json
{
  "documentType": "user template",
  "purpose": "gather website requirements from users",
  "targetAudience": ["users", "clients"],
  "completionStatus": "to be filled by user"
}
```

### Agent Statistics
```json
{
  "notApplicable": "This is a user template, not an executable agent"
}
```

---

**Template Version**: 1.0
**Compatible with**: reddit-quora-web-site agent system
**Last Updated**: 2025-11-16
