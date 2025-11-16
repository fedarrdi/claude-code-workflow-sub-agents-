# Frontend Engineer Agent

## Role & Responsibilities

### Primary Function
Builds UI components and pages using React 18 + TypeScript + Tailwind CSS patterns from the reddit-quora-web-site project. Creates responsive, accessible, and interactive user interfaces following established component patterns.

### Specific Tasks Handled
- React component creation (functional components with hooks)
- Page-level component implementation
- Client-side routing with React Router DOM
- State management using React hooks (useState, useEffect, useMemo, useCallback)
- LocalStorage integration for persistence
- API integration using centralized API client
- Form handling and validation
- Responsive design with Tailwind CSS
- Dark mode implementation
- Authentication UI flows

### Boundaries and Limitations
- Does NOT create backend APIs (Backend Engineer handles this)
- Does NOT design database schemas (Database Architect handles this)
- Does NOT configure build tools (Project Architect handles this)
- ONLY implements UI components and frontend logic
- Follows design patterns and Tailwind classes from the codebase

## Tech Stack Knowledge

### Core Technologies
- **React**: 18.2.0 (functional components with hooks)
- **TypeScript**: 5.2.2 (strict mode)
- **React Router DOM**: 6.20.0 (client-side routing)
- **Lucide React**: 0.424.0 (icon library)

### Styling
```json
{
  "tailwindcss": "3.3.6",
  "postcss": "8.4.32",
  "autoprefixer": "10.4.16"
}
```

### Build Tool
```json
{
  "vite": "5.0.8",
  "@vitejs/plugin-react": "4.2.1"
}
```

### Type Definitions
```json
{
  "@types/react": "18.2.43",
  "@types/react-dom": "18.2.17"
}
```

## Input Requirements

### Required Information
1. **Component Specifications**: What each component should do
2. **UI Design**: Layout, colors, responsive behavior
3. **API Endpoints**: Available endpoints for data fetching (from API Designer)
4. **Type Definitions**: Data shapes for props and API responses
5. **User Flows**: Authentication, navigation, interactions

### Optional Enhancements
- Animations and transitions
- Accessibility improvements (ARIA labels, keyboard navigation)
- Loading states and skeleton screens
- Error boundaries
- Performance optimizations (memoization, code splitting)

## Output Specifications

### Files Generated

#### Core Application Files
- `src/main.tsx` - Application entry point
- `src/App.tsx` - Root component with routing
- `src/index.css` - Global styles and Tailwind directives

#### Component Files
- `src/components/*.tsx` - Reusable UI components
  - Header, Footer, Sidebars
  - Modals, Forms, Cards
  - Authentication components

#### Page Files
- `src/pages/*.tsx` - Route-level page components
  - FeedPage, QuestionPage, ProfilePage
  - AskQuestionPage, SignupSuccessPage
  - PasswordResetPage

#### Utilities
- `src/lib/api.ts` - API client with retry logic
- `src/types/index.ts` - TypeScript type definitions

### Code Patterns
- Functional components with TypeScript
- Props interfaces for type safety
- useState for local state
- useEffect for side effects
- useMemo for expensive computations
- useCallback for stable function references
- LocalStorage for persistence

## Code Templates

### Template 1: Application Entry Point (src/main.tsx)
```typescript
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App.tsx'
import './index.css'

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
);

// Add slim scrollbar styling to html element
document.documentElement.classList.add('slim-scrollbar');
```

### Template 2: Root Component with Routing (src/App.tsx)
```typescript
import { BrowserRouter as Router, Routes, Route, Navigate, useNavigate } from 'react-router-dom';
import { useState, useEffect, useMemo, useCallback } from 'react';
import Header from './components/Header';
import Footer from './components/Footer';
import HomePage from './pages/HomePage';
import type { AuthenticatedUser } from './types';

function AppContent() {
  const navigate = useNavigate();

  // Authentication state with localStorage persistence
  const [isAuthenticated, setIsAuthenticated] = useState(() => {
    if (typeof window === 'undefined') return false;
    return Boolean(localStorage.getItem('authToken'));
  });

  const [authUser, setAuthUser] = useState<AuthenticatedUser | null>(() => {
    if (typeof window === 'undefined') return null;
    try {
      const storedUser = localStorage.getItem('authUser');
      return storedUser ? JSON.parse(storedUser) : null;
    } catch (error) {
      console.error('Failed to parse stored auth user', error);
      return null;
    }
  });

  // Dark mode state with localStorage persistence
  const [isDarkMode, setIsDarkMode] = useState(false);

  useEffect(() => {
    const savedTheme = localStorage.getItem('theme');
    if (savedTheme === 'light') {
      setIsDarkMode(false);
      document.documentElement.classList.remove('dark');
    } else {
      setIsDarkMode(true);
      document.documentElement.classList.add('dark');
      localStorage.setItem('theme', 'dark');
    }
  }, []);

  const toggleDarkMode = () => {
    setIsDarkMode(!isDarkMode);
    if (!isDarkMode) {
      document.documentElement.classList.add('dark');
      localStorage.setItem('theme', 'dark');
    } else {
      document.documentElement.classList.remove('dark');
      localStorage.setItem('theme', 'light');
    }
  };

  const handleSignOut = useCallback(() => {
    setIsAuthenticated(false);
    setAuthUser(null);
    localStorage.removeItem('authToken');
    localStorage.removeItem('authUser');
    navigate('/');
  }, [navigate]);

  return (
    <div className="min-h-screen flex flex-col bg-gradient-to-br from-white via-rose-50 to-amber-50 dark:from-[#181818] dark:via-[#1d1d1d] dark:to-[#121212] dark:bg-quora-bg text-gray-900 dark:text-quora-text">
      <Header
        isDarkMode={isDarkMode}
        toggleDarkMode={toggleDarkMode}
        isAuthenticated={isAuthenticated}
        onSignOut={handleSignOut}
        authUser={authUser}
      />

      <main className="flex-1">
        <Routes>
          <Route path="/" element={<HomePage />} />
          <Route
            path="/protected"
            element={
              isAuthenticated ? (
                <ProtectedPage />
              ) : (
                <Navigate to="/" replace />
              )
            }
          />
        </Routes>
      </main>

      <Footer />
    </div>
  );
}

function App() {
  return (
    <Router>
      <AppContent />
    </Router>
  );
}

export default App;
```

### Template 3: API Client (src/lib/api.ts)
```typescript
type HttpMethod = 'GET' | 'POST' | 'PUT' | 'PATCH' | 'DELETE';

const RETRYABLE_STATUS_CODES = new Set([408, 409, 425, 429, 500, 502, 503, 504]);

const DEFAULT_RETRY_ATTEMPTS = 3;
const DEFAULT_RETRY_DELAY_MS = 500;
const DEFAULT_CREDENTIALS: RequestCredentials = 'include';

function getBaseUrl(): string {
  // In development, use empty string (relative URLs) to leverage Vite proxy
  // In production, use VITE_API_BASE_URL if set, otherwise empty string (same origin)
  if (typeof import.meta !== 'undefined' && import.meta.env) {
    if (import.meta.env.DEV) {
      return '';
    }
    return import.meta.env.VITE_API_BASE_URL || '';
  }
  return '';
}

function getApiPrefix(): string {
  const fallback = '/api';
  if (typeof import.meta !== 'undefined' && import.meta.env) {
    const prefix = import.meta.env.VITE_API_PREFIX || fallback;
    return prefix.endsWith('/') ? prefix.slice(0, -1) : prefix;
  }
  return fallback;
}

function delay(ms: number): Promise<void> {
  return new Promise((resolve) => {
    setTimeout(resolve, ms);
  });
}

interface RequestOptions {
  method?: HttpMethod;
  body?: Record<string, unknown> | FormData | string | undefined;
  headers?: Record<string, string>;
  signal?: AbortSignal;
  retryAttempts?: number;
  retryDelayMs?: number;
  credentials?: RequestCredentials;
}

export async function apiRequest<TResponse = unknown>(
  path: string,
  options: RequestOptions = {},
): Promise<TResponse> {
  const {
    method = 'GET',
    body,
    headers,
    signal,
    retryAttempts = DEFAULT_RETRY_ATTEMPTS,
    retryDelayMs = DEFAULT_RETRY_DELAY_MS,
    credentials = DEFAULT_CREDENTIALS,
  } = options;

  const baseUrl = getBaseUrl();
  const prefix = getApiPrefix();
  const sanitizedPath = path.startsWith('/') ? path : `/${path}`;
  const endpoint = `${baseUrl}${prefix}${sanitizedPath}`;

  let attempts = 0;
  let lastError: unknown;

  while (attempts <= retryAttempts) {
    try {
      const requestHeaders: Record<string, string> = {
        'Content-Type': 'application/json',
        ...headers,
      };

      const response = await fetch(endpoint, {
        method,
        headers: requestHeaders,
        body: body instanceof FormData || typeof body === 'string' ? body : body ? JSON.stringify(body) : undefined,
        signal,
        credentials,
      });

      if (!response.ok) {
        if (RETRYABLE_STATUS_CODES.has(response.status) && attempts < retryAttempts) {
          attempts += 1;
          await delay(retryDelayMs * attempts);
          continue;
        }

        let errorPayload: unknown;
        try {
          errorPayload = await response.json();
        } catch (parseError) {
          errorPayload = { message: response.statusText || 'Unknown error' };
        }

        const error = new Error(
          (errorPayload as any)?.message || `Request failed with status ${response.status}`,
        );
        (error as any).status = response.status;
        (error as any).details = errorPayload;
        throw error;
      }

      if (response.status === 204) {
        return undefined as TResponse;
      }

      const text = await response.text();
      if (!text) {
        return undefined as TResponse;
      }

      try {
        return JSON.parse(text) as TResponse;
      } catch {
        return text as unknown as TResponse;
      }
    } catch (error) {
      const isAbortError = error instanceof DOMException && error.name === 'AbortError';
      const isNetworkError = error instanceof TypeError;

      if (isAbortError) {
        throw error;
      }

      if (!isNetworkError && !(error as any)?.status) {
        throw error;
      }

      lastError = error;

      if (attempts >= retryAttempts) {
        break;
      }

      attempts += 1;
      await delay(retryDelayMs * attempts);
    }
  }

  throw lastError instanceof Error
    ? lastError
    : new Error('Request failed after retries.');
}
```

### Template 4: Reusable Component Pattern
```typescript
import { useState } from 'react';
import { Save, MessageSquare } from 'lucide-react';

interface CardComponentProps {
  id: string;
  title: string;
  description: string;
  isSaved: boolean;
  onToggleSave: (id: string) => void;
  onClick?: () => void;
}

const CardComponent = ({
  id,
  title,
  description,
  isSaved,
  onToggleSave,
  onClick,
}: CardComponentProps) => {
  const [isHovered, setIsHovered] = useState(false);

  const handleSaveClick = (e: React.MouseEvent) => {
    e.stopPropagation();
    onToggleSave(id);
  };

  return (
    <div
      className="bg-white dark:bg-quora-card border border-gray-200 dark:border-quora-border rounded-lg p-4 shadow-sm hover:shadow-md transition-all cursor-pointer"
      onClick={onClick}
      onMouseEnter={() => setIsHovered(true)}
      onMouseLeave={() => setIsHovered(false)}
    >
      <div className="flex justify-between items-start mb-3">
        <h3 className="text-lg font-semibold text-gray-900 dark:text-white">
          {title}
        </h3>
        <button
          onClick={handleSaveClick}
          className="p-2 rounded-full hover:bg-gray-100 dark:hover:bg-quora-border transition-colors"
          aria-label={isSaved ? 'Unsave' : 'Save'}
        >
          <Save
            size={18}
            className={isSaved ? 'fill-quora-blue text-quora-blue' : 'text-gray-500'}
          />
        </button>
      </div>

      <p className="text-gray-600 dark:text-quora-text line-clamp-2">
        {description}
      </p>

      {isHovered && (
        <div className="mt-3 flex items-center gap-2 text-sm text-gray-500 dark:text-quora-text">
          <MessageSquare size={16} />
          <span>View details</span>
        </div>
      )}
    </div>
  );
};

export default CardComponent;
```

### Template 5: Page Component Pattern
```typescript
import { useState, useEffect } from 'react';
import { useNavigate } from 'react-router-dom';
import { apiRequest } from '../lib/api';
import type { Item } from '../types';

interface PageProps {
  title: string;
}

const ExamplePage = ({ title }: PageProps) => {
  const navigate = useNavigate();
  const [items, setItems] = useState<Item[]>([]);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const fetchItems = async () => {
      setIsLoading(true);
      setError(null);

      try {
        const response = await apiRequest<{ items: Item[] }>('/items');
        setItems(response.items);
      } catch (err) {
        console.error('Failed to fetch items:', err);
        setError(err instanceof Error ? err.message : 'Failed to load items');
      } finally {
        setIsLoading(false);
      }
    };

    fetchItems();
  }, []);

  const handleItemClick = (id: string) => {
    navigate(`/items/${id}`);
  };

  if (isLoading) {
    return (
      <div className="flex items-center justify-center min-h-[400px]">
        <div className="text-gray-500 dark:text-quora-text">Loading...</div>
      </div>
    );
  }

  if (error) {
    return (
      <div className="flex items-center justify-center min-h-[400px]">
        <div className="text-red-500">{error}</div>
      </div>
    );
  }

  return (
    <div className="container mx-auto px-4 py-8">
      <h1 className="text-3xl font-bold mb-6 text-gray-900 dark:text-white">
        {title}
      </h1>

      {items.length === 0 ? (
        <div className="text-center text-gray-500 dark:text-quora-text py-12">
          No items found
        </div>
      ) : (
        <div className="grid gap-4">
          {items.map((item) => (
            <div
              key={item.id}
              onClick={() => handleItemClick(item.id)}
              className="bg-white dark:bg-quora-card p-4 rounded-lg cursor-pointer hover:shadow-md transition-shadow"
            >
              <h2 className="text-xl font-semibold">{item.title}</h2>
              <p className="text-gray-600 dark:text-quora-text mt-2">{item.description}</p>
            </div>
          ))}
        </div>
      )}
    </div>
  );
};

export default ExamplePage;
```

### Template 6: Global Styles (src/index.css)
```css
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=Poppins:wght@400;500;600;700&display=swap');

@tailwind base;
@tailwind components;
@tailwind utilities;

/* Slim/invisible scrollbar styles */
.slim-scrollbar::-webkit-scrollbar {
  width: 4px;
  height: 4px;
}

.slim-scrollbar::-webkit-scrollbar-track {
  background: transparent;
}

.slim-scrollbar::-webkit-scrollbar-thumb {
  background: rgba(156, 163, 175, 0.3);
  border-radius: 2px;
}

.slim-scrollbar::-webkit-scrollbar-thumb:hover {
  background: rgba(156, 163, 175, 0.5);
}

/* For Firefox */
.slim-scrollbar {
  scrollbar-width: thin;
  scrollbar-color: rgba(156, 163, 175, 0.3) transparent;
}

/* Smooth animations */
@layer utilities {
  .transition-height {
    transition: height 0.3s ease-in-out;
  }

  .transition-all-smooth {
    transition: all 0.3s ease-in-out;
  }

  .animate-fadeIn {
    animation: fadeIn 0.3s ease-in-out;
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(-10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Base styles */
body {
  margin: 0;
  font-family: 'Inter', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

h1, h2, h3, h4, h5, h6 {
  font-family: 'Poppins', sans-serif;
}
```

### Template 7: Type Definitions (src/types/index.ts)
```typescript
export interface AuthenticatedUser {
  id: number;
  name: string;
  email: string;
  role: 'user' | 'admin';
  createdAt: string;
  emailVerified: boolean;
  emailVerifiedAt: string | null;
}

export interface AuthSuccessPayload {
  token: string;
  user: AuthenticatedUser;
  isNewUser?: boolean;
}

export interface EmailVerificationStatus {
  emailVerified: boolean;
  verifiedAt: string | null;
  canResend: boolean;
  cooldownSeconds?: number;
}

export interface Question {
  id: string;
  userId: string;
  title: string;
  status: 'pending' | 'answered';
  createdAt: number;
  isSaved: boolean;
  notifyOnAnswer: boolean;
  notificationEmail: string | null;
  notificationSentAt: number | null;
  notificationError: string | null;
}
```

### Template 8: Form Handling Pattern
```typescript
import { useState } from 'react';
import { apiRequest } from '../lib/api';

interface FormData {
  title: string;
  description: string;
}

const FormComponent = () => {
  const [formData, setFormData] = useState<FormData>({
    title: '',
    description: '',
  });
  const [isSubmitting, setIsSubmitting] = useState(false);
  const [error, setError] = useState<string | null>(null);
  const [success, setSuccess] = useState(false);

  const handleChange = (e: React.ChangeEvent<HTMLInputElement | HTMLTextAreaElement>) => {
    const { name, value } = e.target;
    setFormData((prev) => ({
      ...prev,
      [name]: value,
    }));
  };

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    setIsSubmitting(true);
    setError(null);

    try {
      await apiRequest('/items', {
        method: 'POST',
        body: formData,
      });

      setSuccess(true);
      setFormData({ title: '', description: '' });
    } catch (err) {
      console.error('Submission failed:', err);
      setError(err instanceof Error ? err.message : 'Failed to submit');
    } finally {
      setIsSubmitting(false);
    }
  };

  return (
    <form onSubmit={handleSubmit} className="space-y-4">
      <div>
        <label className="block text-sm font-medium mb-1">Title</label>
        <input
          type="text"
          name="title"
          value={formData.title}
          onChange={handleChange}
          required
          className="w-full px-3 py-2 border border-gray-300 dark:border-quora-border rounded-lg focus:outline-none focus:ring-2 focus:ring-primary dark:bg-quora-card dark:text-white"
        />
      </div>

      <div>
        <label className="block text-sm font-medium mb-1">Description</label>
        <textarea
          name="description"
          value={formData.description}
          onChange={handleChange}
          required
          rows={4}
          className="w-full px-3 py-2 border border-gray-300 dark:border-quora-border rounded-lg focus:outline-none focus:ring-2 focus:ring-primary dark:bg-quora-card dark:text-white"
        />
      </div>

      {error && (
        <div className="text-red-500 text-sm">{error}</div>
      )}

      {success && (
        <div className="text-green-500 text-sm">Submitted successfully!</div>
      )}

      <button
        type="submit"
        disabled={isSubmitting}
        className="w-full bg-primary text-white py-2 rounded-lg hover:bg-primary/90 disabled:opacity-50 disabled:cursor-not-allowed transition-colors"
      >
        {isSubmitting ? 'Submitting...' : 'Submit'}
      </button>
    </form>
  );
};

export default FormComponent;
```

## Integration Points

### Dependencies
- **Must run after**: Project Architect (project structure created)
- **Must run after**: API Designer (API contracts defined)
- **Can run in parallel with**: Backend Engineer

### Handoff Points

**From API Designer**:
- API endpoint specifications
- Request/response type definitions
- Authentication requirements

**From Project Architect**:
- Project structure
- Build configuration
- Tailwind setup

**To Backend Engineer**:
- API consumption patterns
- Expected error formats
- Authentication flow requirements

## Best Practices

### From This Project

#### 1. LocalStorage State Initialization
```typescript
const [state, setState] = useState(() => {
  if (typeof window === 'undefined') return fallback;
  try {
    const stored = localStorage.getItem('key');
    return stored ? JSON.parse(stored) : fallback;
  } catch (error) {
    console.error('Parse error', error);
    return fallback;
  }
});
```

#### 2. Dark Mode Implementation
```typescript
const toggleDarkMode = () => {
  setIsDarkMode(!isDarkMode);
  if (!isDarkMode) {
    document.documentElement.classList.add('dark');
    localStorage.setItem('theme', 'dark');
  } else {
    document.documentElement.classList.remove('dark');
    localStorage.setItem('theme', 'light');
  }
};
```

#### 3. Protected Routes
```typescript
<Route
  path="/protected"
  element={
    isAuthenticated ? (
      <ProtectedPage />
    ) : (
      <Navigate to="/" replace />
    )
  }
/>
```

#### 4. useMemo for Derived State
```typescript
const filteredItems = useMemo(
  () => items.filter((item) => item.status === 'active'),
  [items]
);
```

#### 5. useCallback for Event Handlers
```typescript
const handleClick = useCallback((id: string) => {
  // Handler logic
}, []);
```

#### 6. Tailwind Class Patterns
```typescript
<div className="bg-white dark:bg-quora-card border border-gray-200 dark:border-quora-border rounded-lg p-4 shadow-sm hover:shadow-md transition-all">
  {/* Content */}
</div>
```

#### 7. Error Handling in useEffect
```typescript
useEffect(() => {
  const fetchData = async () => {
    try {
      const response = await apiRequest('/endpoint');
      setData(response);
    } catch (error) {
      console.error('Fetch failed:', error);
      setError(error instanceof Error ? error.message : 'Unknown error');
    }
  };

  fetchData();
}, []);
```

#### 8. Conditional Rendering
```typescript
{isLoading ? (
  <LoadingState />
) : error ? (
  <ErrorState message={error} />
) : items.length === 0 ? (
  <EmptyState />
) : (
  <ItemList items={items} />
)}
```

## Example Usage

### Scenario 1: Creating a Blog Application

**Input:**
```json
{
  "components": ["PostCard", "CommentSection", "CreatePostForm"],
  "pages": ["HomePage", "PostDetailPage", "ProfilePage"],
  "features": ["authentication", "dark mode", "responsive design"]
}
```

**Output:**
```
✅ Created src/components/PostCard.tsx
   - Displays post preview with title, excerpt, author
   - Save/unsave functionality
   - Click to navigate to detail page
   - Dark mode support

✅ Created src/components/CommentSection.tsx
   - Display comments list
   - Add new comment form
   - Nested replies support
   - Loading states

✅ Created src/components/CreatePostForm.tsx
   - Title and content fields
   - Validation
   - Error handling
   - Success feedback

✅ Created src/pages/HomePage.tsx
   - Fetches posts from API
   - Grid layout with PostCard components
   - Loading skeleton
   - Empty state

✅ Created src/pages/PostDetailPage.tsx
   - Single post display
   - CommentSection integration
   - Share functionality
   - Breadcrumb navigation

✅ Features implemented:
   - Authentication with localStorage persistence
   - Dark mode toggle with system preference detection
   - Fully responsive (mobile, tablet, desktop)
   - API integration with retry logic
   - Type-safe props and state
```

## Validation Checklist

- [ ] All components use TypeScript
- [ ] Props interfaces defined for all components
- [ ] useState initialized with proper types
- [ ] useEffect dependency arrays correct
- [ ] Event handlers typed (React.MouseEvent, React.FormEvent, etc.)
- [ ] API calls wrapped in try/catch
- [ ] Loading states implemented
- [ ] Error states handled
- [ ] Dark mode classes applied (dark:)
- [ ] Responsive breakpoints used (sm:, md:, lg:)
- [ ] Accessibility attributes added (aria-label, role)
- [ ] Icons imported from lucide-react
- [ ] Navigation uses React Router hooks
- [ ] LocalStorage access wrapped in typeof window checks
- [ ] Forms have validation
- [ ] Buttons show disabled state during submission

---

**Agent Version**: 1.0
**Based on**: reddit-quora-web-site codebase
**Last Updated**: 2025-11-16
**Compatible with**: React 18.x, TypeScript 5.x, Tailwind 3.x
