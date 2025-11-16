# Tech Stack Reference

Complete technology stack and configuration details from the reddit-quora-web-site project.

## Frontend Stack

### Core Framework
- **React**: 18.2.0
- **ReactDOM**: 18.2.0
- **TypeScript**: 5.2.2

### Routing
- **React Router DOM**: 6.20.0

### Styling
- **Tailwind CSS**: 3.3.6
- **PostCSS**: 8.4.32
- **Autoprefixer**: 10.4.16

### Icons
- **Lucide React**: 0.424.0

### Build Tool
- **Vite**: 5.0.8
- **@vitejs/plugin-react**: 4.2.1

## Backend Stack

### Server Framework
- **Express.js**: 5.1.0
- **Node.js**: ES Modules (type: "module")
- **TypeScript**: 5.2.2

### Database
- **SQL.js**: 1.13.0 (SQLite compiled to WebAssembly)
- **Location**: `server/data/auth.sqlite`

### Authentication
- **bcryptjs**: 3.0.2 (password hashing)
- **jsonwebtoken**: 9.0.2 (JWT tokens)

### Middleware
- **CORS**: 2.8.5
- **body-parser**: 2.2.0
- **dotenv**: 16.3.1

## Development Tools

### TypeScript Configuration
- **Target**: ES2020 (frontend), ES2022 (backend)
- **Module**: ESNext (frontend), NodeNext (backend)
- **Strict Mode**: Enabled
- **Source Maps**: Enabled

### Code Quality
- **ESLint**: 8.55.0
- **@typescript-eslint/eslint-plugin**: 6.14.0
- **@typescript-eslint/parser**: 6.14.0
- **eslint-plugin-react-hooks**: 4.6.0
- **eslint-plugin-react-refresh**: 0.4.5

### Task Running
- **concurrently**: 9.2.1
- **tsx**: 4.19.0 (TypeScript execution for Node.js)
- **npm-run-all**: 4.1.5

### Type Definitions
```json
{
  "@types/bcryptjs": "2.4.6",
  "@types/body-parser": "1.19.5",
  "@types/cors": "2.8.17",
  "@types/express": "5.0.0",
  "@types/jsonwebtoken": "9.0.7",
  "@types/node": "20.10.6",
  "@types/react": "18.2.43",
  "@types/react-dom": "18.2.17"
}
```

## Project Structure

```
project-root/
├── src/                          # Frontend source
│   ├── components/               # Reusable components
│   ├── pages/                    # Route-level pages
│   ├── lib/                      # Utilities (api.ts)
│   ├── types/                    # TypeScript types
│   ├── mock/                     # Mock data
│   ├── App.tsx                   # Root component
│   ├── main.tsx                  # Entry point
│   └── index.css                 # Global styles
│
├── server/                       # Backend source
│   ├── routes/                   # Express routes
│   ├── types/                    # Server types
│   ├── data/                     # SQLite database
│   ├── db.ts                     # Database adapter
│   ├── config.ts                 # Configuration
│   ├── index.ts                  # Server entry
│   └── tsconfig.json             # Server TS config
│
├── public/                       # Static assets
├── dist/                         # Build output
├── index.html                    # HTML template
├── package.json                  # Dependencies
├── tsconfig.json                 # Frontend TS config
├── vite.config.ts                # Vite configuration
├── tailwind.config.js            # Tailwind config
├── postcss.config.js             # PostCSS config
├── .eslintrc.cjs                 # ESLint config
├── .env                          # Environment variables
└── .gitignore                    # Git exclusions
```

## Configuration Files

### package.json Scripts
```json
{
  "dev": "concurrently --kill-others-on-fail \"npm run dev:types\" \"tsx watch server/index.ts\" \"vite\"",
  "dev:types": "tsc --noEmit --watch",
  "build:server": "tsc -p server/tsconfig.json",
  "build": "npm run build:server && tsc && vite build",
  "lint": "eslint . --ext ts,tsx --report-unused-disable-directives --max-warnings 0",
  "preview": "vite preview"
}
```

### Tailwind Theme Colors
```javascript
{
  primary: '#FF4500',
  secondary: '#0079D3',
  'quora-red': '#B92B27',
  'quora-blue': '#2E69FF',
  'quora-bg': '#F7F7F8',
  'quora-card': '#1F1F1F',
  'quora-border': '#393939',
  'quora-text': '#939598'
}
```

### Fonts
- **Body**: Inter (Google Fonts)
- **Headings**: Poppins (Google Fonts)

## Environment Variables

### Application
```bash
PRODUCT_NAME=YourAppName
PRODUCT_LOGO_URL=https://example.com/logo.png
PUBLIC_BASE_URL=http://localhost:5173
```

### Server
```bash
PORT=4000
API_PREFIX=/api
CORS_ORIGIN=http://localhost:5173,http://localhost:4000
```

### Security
```bash
SESSION_SECRET=random-secret-here
JWT_SECRET=random-secret-here
JWT_EXPIRES_IN=1h
```

### Email Service (Brevo)
```bash
BREVO_API_KEY=your-key
BREVO_SENDER_EMAIL=noreply@domain.com
BREVO_SUPPORT_EMAIL=support@domain.com
BREVO_SENDER_NAME="App Name"
BREVO_VERIFY_TEMPLATE_ID=2
BREVO_QUESTION_ANSWERED_TEMPLATE_ID=3
BREVO_PASSWORD_RESET_TEMPLATE_ID=4
```

### Admin Bootstrap
```bash
ADMIN_EMAIL=admin@example.com
ADMIN_PASSWORD=ChangeMe123!
```

## Database Schema

### Users Table
```sql
CREATE TABLE users (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  name TEXT NOT NULL,
  email TEXT NOT NULL UNIQUE,
  password_hash TEXT NOT NULL,
  role TEXT NOT NULL CHECK(role IN ('user', 'admin')),
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  email_verified INTEGER DEFAULT 0,
  email_verified_at DATETIME
);
```

### Questions Table
```sql
CREATE TABLE questions (
  id TEXT PRIMARY KEY,
  title TEXT NOT NULL,
  description TEXT,
  user_id INTEGER NOT NULL,
  status TEXT NOT NULL CHECK(status IN ('pending', 'answered')),
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  notify_on_answer INTEGER DEFAULT 0,
  notification_email TEXT,
  FOREIGN KEY (user_id) REFERENCES users(id)
);
```

## Common Patterns

### Component Pattern
```typescript
import { useState } from 'react';

interface Props {
  title: string;
  onAction: () => void;
}

const Component = ({ title, onAction }: Props) => {
  const [state, setState] = useState<Type>(initialValue);

  return (
    <div className="bg-white dark:bg-quora-card">
      {/* JSX */}
    </div>
  );
};

export default Component;
```

### API Request Pattern
```typescript
const response = await apiRequest<ResponseType>('/endpoint', {
  method: 'POST',
  body: { data },
});
```

### Route Handler Pattern
```typescript
router.post('/endpoint', async (req, res) => {
  const { field } = (req.body ?? {}) as RequestBody;

  if (!field) {
    return res.status(400).json({ message: 'Field required' });
  }

  try {
    const db = await getDb();
    const result = await db.run('SQL', [params]);
    return res.status(200).json({ data: result });
  } catch (error) {
    console.error('Error:', error);
    return res.status(500).json({ message: 'Internal server error.' });
  }
});
```

### Database Query Pattern
```typescript
// Single row
const user = await db.get<UserRecord>('SELECT * FROM users WHERE email = ?', email);

// Multiple rows
const users = await db.all<UserRecord>('SELECT * FROM users ORDER BY created_at DESC');

// Insert
const result = await db.run('INSERT INTO users (...) VALUES (?)', [values]);
const id = result.lastID;
```

## Ports

- **Frontend (Vite)**: 5173
- **Backend (Express)**: 4000
- **Proxy**: Vite proxies `/api` to `http://localhost:4000`

## Dark Mode

- **Strategy**: Class-based (`dark` class on `<html>`)
- **Persistence**: LocalStorage (`theme` key)
- **Default**: Dark mode

---

**Version**: 1.0
**Based on**: reddit-quora-web-site codebase
**Last Updated**: 2025-11-16
