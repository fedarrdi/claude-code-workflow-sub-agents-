# Database Architect Agent

## Role & Responsibilities

### Primary Function
Designs and implements database structures using SQL.js (SQLite compiled to WebAssembly) following patterns from the reddit-quora-web-site project. Creates schemas, manages migrations, implements the database adapter pattern, and handles data persistence.

### Specific Tasks Handled
- Database schema design (tables, columns, constraints)
- Schema migration strategy (ALTER TABLE with column existence checks)
- Database adapter implementation (BaseDatabaseAdapter pattern)
- File-based SQLite persistence
- Index creation for performance
- Foreign key relationships
- Data seeding (admin bootstrap)
- Query patterns and prepared statements

### Boundaries and Limitations
- Does NOT implement business logic (Backend Engineer handles this)
- Does NOT create API endpoints (Backend Engineer handles this)
- Does NOT design UI (Frontend Engineer handles this)
- ONLY focuses on data structure and database operations
- Follows SQL.js specific patterns (in-memory with file persistence)

## Tech Stack Knowledge

### Core Technologies
- **SQL.js**: 1.13.0 (SQLite compiled to WebAssembly)
- **Node.js**: File system operations for persistence
- **TypeScript**: Type-safe database operations

### Type Definitions
```json
{
  "@types/node": "20.10.6"
}
```

### Key Characteristics
- **In-Memory Database**: Loaded from file into memory
- **Manual Persistence**: Must explicitly save to disk after writes
- **File-Based Storage**: SQLite file in `server/data/*.sqlite`
- **No Connection Pooling**: Single database instance
- **WebAssembly Module**: sql-wasm.wasm file required

## Input Requirements

### Required Information
1. **Data Models**: Entity definitions with fields and types
2. **Relationships**: Foreign keys, one-to-many, many-to-many
3. **Constraints**: Unique fields, check constraints, defaults
4. **Indexes**: Fields that need indexing for performance
5. **Seed Data**: Initial data requirements (admin users, etc.)

### Optional Enhancements
- Full-text search configuration
- Custom indexes for complex queries
- Database backup strategy
- Data migration scripts
- Archive/soft-delete patterns

## Output Specifications

### Files Generated
- `server/db.ts` - Database adapter and connection management
- `server/data/[dbname].sqlite` - SQLite database file

### Database Operations Provided
```typescript
// Query operations
get<T>(sql: string, ...params: unknown[]): Promise<T | null>
all<T>(sql: string, ...params: unknown[]): Promise<T[]>
run(sql: string, ...params: unknown[]): Promise<DatabaseRunResult>

// Lifecycle
initialize(): Promise<void>
close(): Promise<void>
```

## Code Templates

### Template 1: Database Adapter (server/db.ts)
```typescript
import fs from 'node:fs';
import path from 'node:path';
import { fileURLToPath } from 'node:url';

import initSqlJs from 'sql.js';
import type { Database, SqlJsStatic, Statement } from 'sql.js';
import bcrypt from 'bcryptjs';

const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);
const { promises: fsPromises } = fs;

const projectRoot = process.cwd();

const DEFAULT_SQLITE_OPTIONS = {
  dataDir: path.join(projectRoot, 'server', 'data'),
  dbFileName: 'auth.sqlite',
  sqlJsDistDir: path.join(projectRoot, 'node_modules/sql.js/dist'),
} as const;

type SqliteOptions = Partial<typeof DEFAULT_SQLITE_OPTIONS>;

function ensureDirectoryExists(dirPath: string): void {
  if (!fs.existsSync(dirPath)) {
    fs.mkdirSync(dirPath, { recursive: true });
  }
}

function toParamArray(params: unknown[]): unknown[] {
  if (params.length === 1 && Array.isArray(params[0])) {
    return params[0];
  }
  return params;
}

function mapRowFromStatement(stmt: Statement): Record<string, unknown> {
  return stmt.getAsObject();
}

export type DatabaseRunResult = { lastID: number | null; changes: number };

export abstract class BaseDatabaseAdapter {
  abstract initialize(): Promise<void>;

  abstract get<T>(sql: string, ...params: unknown[]): Promise<T | null>;

  abstract all<T>(sql: string, ...params: unknown[]): Promise<T[]>;

  abstract run(sql: string, ...params: unknown[]): Promise<DatabaseRunResult>;

  abstract close(): Promise<void>;
}

export class SQLiteDatabaseAdapter extends BaseDatabaseAdapter {
  private readonly options: typeof DEFAULT_SQLITE_OPTIONS;

  private dbFilePath: string;

  private sqlModulePromise: Promise<SqlJsStatic> | null;

  private SQL: SqlJsStatic | null;

  private db: Database | null;

  private isInitialized: boolean;

  private initializationPromise: Promise<void> | null;

  constructor(options: SqliteOptions = {}) {
    super();
    this.options = {
      ...DEFAULT_SQLITE_OPTIONS,
      ...options,
    };
    ensureDirectoryExists(this.options.dataDir);
    this.dbFilePath = path.join(this.options.dataDir, this.options.dbFileName);
    this.sqlModulePromise = null;
    this.SQL = null;
    this.db = null;
    this.isInitialized = false;
    this.initializationPromise = null;
  }

  async initialize(): Promise<void> {
    if (this.isInitialized) {
      return;
    }

    if (!this.initializationPromise) {
      this.initializationPromise = this.performInitialization();
    }

    try {
      await this.initializationPromise;
      this.isInitialized = true;
    } finally {
      this.initializationPromise = null;
    }
  }

  async get<T>(sql: string, ...params: unknown[]): Promise<T | null> {
    await this.ensureInitialized();
    const normalizedParams = toParamArray(params);
    return this.internalGet<T>(sql, normalizedParams);
  }

  async all<T>(sql: string, ...params: unknown[]): Promise<T[]> {
    await this.ensureInitialized();
    const normalizedParams = toParamArray(params);
    return this.internalAll<T>(sql, normalizedParams);
  }

  async run(
    sql: string,
    ...params: unknown[]
  ): Promise<DatabaseRunResult> {
    await this.ensureInitialized();
    const normalizedParams = toParamArray(params);
    return this.internalRun(sql, normalizedParams);
  }

  async close(): Promise<void> {
    if (!this.db) {
      return;
    }

    try {
      await this.persistDatabase();
    } catch (error) {
      console.error('Failed to persist database during close.', error);
    }

    this.db.close();
    this.db = null;
    this.SQL = null;
    this.isInitialized = false;
  }

  private async ensureInitialized(): Promise<void> {
    if (this.isInitialized) {
      return;
    }
    await this.initialize();
  }

  private async performInitialization(): Promise<void> {
    try {
      await this.openDatabase();
      await this.prepareSchema();
      await this.seedDefaultAdmin();
    } catch (error) {
      console.error('Failed to initialize SQLite database adapter.', error);
      throw error;
    }
  }

  private async loadSqlModule(): Promise<SqlJsStatic> {
    if (!this.sqlModulePromise) {
      this.sqlModulePromise = initSqlJs({
        locateFile: (file: string) => path.join(this.options.sqlJsDistDir, file),
      });
    }
    this.SQL = await this.sqlModulePromise;
    return this.SQL;
  }

  private async openDatabase(): Promise<Database> {
    if (this.db) {
      return this.db;
    }

    const SQL = await this.loadSqlModule();
    try {
      const hasExistingFile = fs.existsSync(this.dbFilePath);
      if (hasExistingFile) {
        const fileBuffer = fs.readFileSync(this.dbFilePath);
        this.db = new SQL.Database(new Uint8Array(fileBuffer));
      } else {
        this.db = new SQL.Database();
      }
    } catch (error) {
      console.error('Failed to load SQLite database file. Creating a fresh database.', error);
      this.db = new SQL.Database();
    }

    return this.db;
  }

  private async persistDatabase(): Promise<void> {
    if (!this.db) {
      console.log('[DB] Cannot persist: database not initialized');
      return;
    }

    console.log(`[DB] Persisting database to: ${this.dbFilePath}`);
    const data = this.db.export();
    const buffer = Buffer.from(data);
    await fsPromises.writeFile(this.dbFilePath, buffer);
    console.log(`[DB] Database persisted successfully (${buffer.length} bytes)`);
  }

  private tableExists(tableName: string): boolean {
    if (!this.db) {
      return false;
    }

    const result = this.db.exec(
      `SELECT name FROM sqlite_master WHERE type='table' AND name='${tableName}' LIMIT 1;`,
    );
    return Boolean(result?.[0]?.values?.length);
  }

  private columnExists(tableName: string, columnName: string): boolean {
    if (!this.db) {
      return false;
    }

    const result = this.db.exec(`PRAGMA table_info(${tableName});`);
    if (!result?.[0]?.values) {
      return false;
    }

    return result[0].values.some((valueRow) => {
      if (!Array.isArray(valueRow)) {
        return false;
      }

      const columnNameCandidate = valueRow[1];
      return typeof columnNameCandidate === 'string' && columnNameCandidate === columnName;
    });
  }

  private ensureColumn(tableName: string, columnName: string, definition: string): boolean {
    if (this.columnExists(tableName, columnName)) {
      return false;
    }
    this.db?.exec(`ALTER TABLE ${tableName} ADD COLUMN ${columnName} ${definition};`);
    return true;
  }

  private async prepareSchema(): Promise<void> {
    const db = await this.openDatabase();
    const hadUsersTable = this.tableExists('users');
    const hadQuestionsTable = this.tableExists('questions');

    // Create initial schema
    db.exec(`
      CREATE TABLE IF NOT EXISTS users (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        name TEXT NOT NULL,
        email TEXT NOT NULL UNIQUE,
        password_hash TEXT NOT NULL,
        role TEXT NOT NULL CHECK(role IN ('user', 'admin')),
        created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
        email_verified INTEGER DEFAULT 0,
        email_verified_at DATETIME
      );

      CREATE TABLE IF NOT EXISTS questions (
        id TEXT PRIMARY KEY,
        title TEXT NOT NULL,
        description TEXT,
        user_id INTEGER NOT NULL,
        status TEXT NOT NULL CHECK(status IN ('pending', 'answered')),
        created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
        FOREIGN KEY (user_id) REFERENCES users(id)
      );
    `);

    // Schema migrations (add columns if they don't exist)
    const schemaChanged = [
      this.ensureColumn('users', 'email_verified', 'INTEGER DEFAULT 0'),
      this.ensureColumn('users', 'email_verified_at', 'DATETIME'),
    ].some(Boolean) || !hadUsersTable || !hadQuestionsTable;

    if (schemaChanged) {
      console.log('Database schema changed, persisting to disk...');
      await this.persistDatabase();
      console.log('Database persisted successfully');
    }
  }

  private async seedDefaultAdmin(): Promise<void> {
    const adminEmail = (process.env.ADMIN_EMAIL || 'admin@example.com').toLowerCase();
    const adminPassword = process.env.ADMIN_PASSWORD || 'ChangeMe123!';

    const existingAdmin = await this.internalGet<{ id: number }>(
      'SELECT id FROM users WHERE email = ?',
      [adminEmail],
    );

    if (existingAdmin) {
      return;
    }

    const passwordHash = await bcrypt.hash(adminPassword, 12);
    await this.internalRun(
      'INSERT INTO users (name, email, password_hash, role) VALUES (?, ?, ?, ?)',
      ['Default Admin', adminEmail, passwordHash, 'admin'],
    );

    console.warn(
      'Seeded default admin account. Update ADMIN_EMAIL and ADMIN_PASSWORD environment variables.',
    );
  }

  private async internalGet<T>(sql: string, params: unknown[] = []): Promise<T | null> {
    const db = await this.openDatabase();
    let stmt: Statement | undefined;
    try {
      stmt = db.prepare(sql);
      stmt.bind(params);

      if (!stmt.step()) {
        return null;
      }

      return mapRowFromStatement(stmt) as T;
    } catch (error) {
      console.error('SQLite get error.', { sql, params, error });
      throw error;
    } finally {
      stmt?.free();
    }
  }

  private async internalAll<T>(sql: string, params: unknown[] = []): Promise<T[]> {
    const db = await this.openDatabase();
    let stmt: Statement | undefined;
    try {
      stmt = db.prepare(sql);
      stmt.bind(params);

      const results: T[] = [];
      while (stmt.step()) {
        results.push(mapRowFromStatement(stmt) as T);
      }

      return results;
    } catch (error) {
      console.error('SQLite all error.', { sql, params, error });
      throw error;
    } finally {
      stmt?.free();
    }
  }

  private async internalRun(
    sql: string,
    params: unknown[] = [],
  ): Promise<DatabaseRunResult> {
    const db = await this.openDatabase();
    let stmt: Statement | undefined;
    try {
      stmt = db.prepare(sql);
      stmt.bind(params);
      stmt.step();
    } catch (error) {
      console.error('SQLite run error.', { sql, params, error });
      throw error;
    } finally {
      stmt?.free();
    }

    const lastInsertRowIdResult = db.exec('SELECT last_insert_rowid() AS id;');
    const lastID = parseLastInsertId(lastInsertRowIdResult?.[0]?.values ?? []);
    const changes = db.getRowsModified();

    await this.persistDatabase();

    return { lastID, changes };
  }
}

function parseLastInsertId(result: unknown[][]): number | null {
  if (result.length === 0) {
    return null;
  }

  const [firstRow] = result;
  if (!Array.isArray(firstRow) || firstRow.length === 0) {
    return null;
  }

  const [firstValue] = firstRow;
  if (typeof firstValue === 'number' && Number.isFinite(firstValue)) {
    return firstValue;
  }

  return null;
}

let activeAdapter: BaseDatabaseAdapter | undefined;

export function configureDatabase(adapter: BaseDatabaseAdapter): void {
  activeAdapter = adapter;
}

export function getDatabaseAdapter(): BaseDatabaseAdapter {
  if (!activeAdapter) {
    activeAdapter = new SQLiteDatabaseAdapter();
  }
  return activeAdapter;
}

export async function getDb(): Promise<BaseDatabaseAdapter> {
  const adapter = getDatabaseAdapter();
  await adapter.initialize();
  return adapter;
}
```

### Template 2: Schema Definition Pattern
```sql
-- Users table with authentication fields
CREATE TABLE IF NOT EXISTS users (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  name TEXT NOT NULL,
  email TEXT NOT NULL UNIQUE,
  password_hash TEXT NOT NULL,
  role TEXT NOT NULL CHECK(role IN ('user', 'admin')),
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  email_verified INTEGER DEFAULT 0,
  email_verified_at DATETIME,
  email_verification_code_hash TEXT,
  email_verification_code_issued_at DATETIME,
  email_verification_code_expires_at DATETIME,
  password_reset_token_hash TEXT,
  password_reset_token_issued_at DATETIME,
  password_reset_token_expires_at DATETIME
);

-- Questions table with foreign key
CREATE TABLE IF NOT EXISTS questions (
  id TEXT PRIMARY KEY,
  title TEXT NOT NULL,
  description TEXT,
  user_id INTEGER NOT NULL,
  status TEXT NOT NULL CHECK(status IN ('pending', 'answered')),
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  is_paid INTEGER DEFAULT 0,
  notify_on_answer INTEGER DEFAULT 0,
  notification_email TEXT,
  notification_sent_at DATETIME,
  notification_error TEXT,
  FOREIGN KEY (user_id) REFERENCES users(id)
);
```

### Template 3: Migration Pattern
```typescript
private async prepareSchema(): Promise<void> {
  const db = await this.openDatabase();
  const hadUsersTable = this.tableExists('users');

  // Create initial schema
  db.exec(`CREATE TABLE IF NOT EXISTS users (...);`);

  // Add columns incrementally (safe migrations)
  const schemaChanged = [
    this.ensureColumn('users', 'email_verified', 'INTEGER DEFAULT 0'),
    this.ensureColumn('users', 'email_verified_at', 'DATETIME'),
    this.ensureColumn('users', 'password_reset_token_hash', 'TEXT'),
  ].some(Boolean) || !hadUsersTable;

  // Persist if schema changed
  if (schemaChanged) {
    console.log('Database schema changed, persisting to disk...');
    await this.persistDatabase();
  }
}

private ensureColumn(tableName: string, columnName: string, definition: string): boolean {
  if (this.columnExists(tableName, columnName)) {
    return false; // Column already exists
  }
  this.db?.exec(`ALTER TABLE ${tableName} ADD COLUMN ${columnName} ${definition};`);
  return true; // Column added
}
```

### Template 4: Query Patterns
```typescript
// Get single record
const user = await db.get<UserRecord>(
  'SELECT * FROM users WHERE email = ?',
  normalizedEmail
);

// Get all records
const users = await db.all<UserRecord>(
  'SELECT * FROM users ORDER BY created_at DESC'
);

// Insert record
const result = await db.run(
  'INSERT INTO users (name, email, password_hash, role) VALUES (?, ?, ?, ?)',
  [name, email, passwordHash, 'user']
);
const insertedId = result.lastID;

// Update record
await db.run(
  'UPDATE users SET email_verified = 1, email_verified_at = ? WHERE id = ?',
  [new Date().toISOString(), userId]
);

// Delete record
await db.run('DELETE FROM users WHERE id = ?', userId);

// Complex query with JOIN
const questionsWithUsers = await db.all<QuestionWithUser>(
  `SELECT q.*, u.name AS user_name, u.email AS user_email
   FROM questions q
   JOIN users u ON q.user_id = u.id
   WHERE q.status = ?
   ORDER BY q.created_at DESC`,
  ['pending']
);
```

## Integration Points

### Dependencies
- **Must run after**: Project Architect (project structure created)
- **Must run before**: Backend Engineer (needs database to query)

### Handoff Points

**To Backend Engineer**:
- Database adapter instance via `getDb()`
- Table schemas and column names
- Query patterns (get, all, run)
- Type definitions for records

**From Project Architect**:
- `server/data` directory created
- `sql.js` dependency installed
- TypeScript configuration

## Best Practices

### From This Project

#### 1. File-Based Persistence
```typescript
private async persistDatabase(): Promise<void> {
  if (!this.db) return;

  const data = this.db.export();
  const buffer = Buffer.from(data);
  await fsPromises.writeFile(this.dbFilePath, buffer);
}
```

**Call after every write operation:**
```typescript
await db.run('INSERT INTO ...', params);
// Database is automatically persisted in internalRun()
```

#### 2. Prepared Statements (Prevent SQL Injection)
```typescript
// GOOD: Use parameterized queries
stmt = db.prepare('SELECT * FROM users WHERE email = ?');
stmt.bind([email]);

// BAD: Never concatenate user input
stmt = db.prepare(`SELECT * FROM users WHERE email = '${email}'`);
```

#### 3. Schema Migration Strategy
```typescript
// Safe: Check if column exists before adding
private ensureColumn(tableName: string, columnName: string, definition: string): boolean {
  if (this.columnExists(tableName, columnName)) {
    return false;
  }
  this.db?.exec(`ALTER TABLE ${tableName} ADD COLUMN ${columnName} ${definition};`);
  return true;
}
```

#### 4. Singleton Pattern
```typescript
let activeAdapter: BaseDatabaseAdapter | undefined;

export async function getDb(): Promise<BaseDatabaseAdapter> {
  const adapter = getDatabaseAdapter();
  await adapter.initialize();
  return adapter;
}
```

#### 5. CHECK Constraints
```sql
role TEXT NOT NULL CHECK(role IN ('user', 'admin'))
status TEXT NOT NULL CHECK(status IN ('pending', 'answered'))
```

#### 6. Timestamp Handling
```sql
created_at DATETIME DEFAULT CURRENT_TIMESTAMP
```

```typescript
// When updating timestamps
await db.run(
  'UPDATE users SET email_verified_at = ? WHERE id = ?',
  [new Date().toISOString(), userId]
);
```

#### 7. INTEGER as Boolean
```sql
email_verified INTEGER DEFAULT 0
is_paid INTEGER DEFAULT 0
notify_on_answer INTEGER DEFAULT 0
```

```typescript
// Convert to boolean in application
emailVerified: Boolean(record.email_verified)
```

#### 8. Foreign Keys
```sql
FOREIGN KEY (user_id) REFERENCES users(id)
```

**Enable foreign key enforcement:**
```typescript
db.exec('PRAGMA foreign_keys = ON;');
```

### Error Handling

#### Database Connection Failures
```typescript
try {
  const db = await getDb();
} catch (error) {
  console.error('Failed to connect to database:', error);
  throw new Error('Database unavailable');
}
```

#### Query Failures
```typescript
try {
  const result = await db.run('INSERT INTO ...', params);
} catch (error) {
  console.error('Query failed:', error);
  throw error;
}
```

#### File Persistence Failures
```typescript
try {
  await this.persistDatabase();
} catch (error) {
  console.error('Failed to persist database:', error);
  // Consider: Queue retry, alert monitoring
}
```

## Example Usage

### Scenario 1: User Management System

**Input:**
```json
{
  "tables": [
    {
      "name": "users",
      "columns": [
        { "name": "id", "type": "INTEGER PRIMARY KEY AUTOINCREMENT" },
        { "name": "name", "type": "TEXT NOT NULL" },
        { "name": "email", "type": "TEXT NOT NULL UNIQUE" },
        { "name": "password_hash", "type": "TEXT NOT NULL" },
        { "name": "role", "type": "TEXT CHECK(role IN ('user', 'admin'))" },
        { "name": "created_at", "type": "DATETIME DEFAULT CURRENT_TIMESTAMP" }
      ]
    }
  ],
  "seedData": {
    "adminEmail": "admin@example.com",
    "adminPassword": "SecurePassword123!"
  }
}
```

**Output:**
```
✅ Created server/db.ts with SQLiteDatabaseAdapter
✅ Implemented schema:
   - users table with 6 columns
   - UNIQUE constraint on email
   - CHECK constraint on role
   - Auto-increment primary key

✅ Migration system:
   - ensureColumn() for safe schema updates
   - Automatic persistence on schema changes

✅ Seeding:
   - Default admin account created
   - Password hashed with bcrypt (12 rounds)

✅ Query interface:
   - get<T>() for single records
   - all<T>() for multiple records
   - run() for INSERT/UPDATE/DELETE

✅ Database file created: server/data/auth.sqlite
```

### Scenario 2: Blog Platform Schema

**Input:**
```json
{
  "tables": [
    {
      "name": "posts",
      "columns": [
        { "name": "id", "type": "INTEGER PRIMARY KEY AUTOINCREMENT" },
        { "name": "title", "type": "TEXT NOT NULL" },
        { "name": "content", "type": "TEXT" },
        { "name": "author_id", "type": "INTEGER NOT NULL" },
        { "name": "status", "type": "TEXT CHECK(status IN ('draft', 'published'))" },
        { "name": "created_at", "type": "DATETIME DEFAULT CURRENT_TIMESTAMP" }
      ],
      "foreignKeys": [
        { "column": "author_id", "references": "users(id)" }
      ]
    }
  ]
}
```

**Output:**
```
✅ Created posts table with foreign key to users
✅ Enforced referential integrity
✅ Status validation via CHECK constraint
✅ Auto-timestamping for created_at
```

## Validation Checklist

- [ ] All tables have PRIMARY KEY
- [ ] Foreign keys reference existing tables
- [ ] CHECK constraints for enum-like fields
- [ ] UNIQUE constraints on email/username fields
- [ ] NOT NULL for required fields
- [ ] DEFAULT values where appropriate
- [ ] Timestamps use DATETIME type
- [ ] Booleans stored as INTEGER (0/1)
- [ ] ensureColumn() used for migrations
- [ ] persistDatabase() called after writes
- [ ] Prepared statements used (no SQL concatenation)
- [ ] Database file in .gitignore
- [ ] Admin seeding uses environment variables
- [ ] Statement.free() called in finally blocks
- [ ] Error logging for failed queries

## Context Requirements

### Required Files
- `SPECIFICATION.md` - Database schema definitions from Product Analyst
- `package.json` - To verify sql.js dependency is installed
- `.env` - For ADMIN_EMAIL and ADMIN_PASSWORD seed data
- `server/tsconfig.json` - TypeScript configuration for server code

### Optional Context
- Example database implementations from reddit-quora-web-site
- Migration patterns for schema updates
- Indexing strategies for performance optimization

### Exclude Patterns
- `node_modules/**` - Dependencies
- `dist/**` - Build output
- `src/**` - Frontend code
- `.git/**` - Git history
- `*.sqlite` - Existing database files (will be created fresh)
- `coverage/**` - Test coverage

## Context Loading Strategy

### Phase 1: Schema Requirements
Load database design specifications:
- Read `SPECIFICATION.md` to extract all data models
- Identify table structures, fields, and data types
- Map relationships (foreign keys, one-to-many, many-to-many)
- Review constraints (UNIQUE, NOT NULL, CHECK)

### Phase 2: Implementation Patterns
Load SQL.js adapter patterns:
- Review BaseDatabaseAdapter interface
- Understand file persistence mechanism
- Load migration strategy (ensureColumn pattern)
- Review prepared statement patterns for security

### Phase 3: Validation and Seeding
Load seed data requirements:
- Extract ADMIN_EMAIL and ADMIN_PASSWORD from .env
- Verify table creation with PRAGMA commands
- Test database persistence to file system
- Validate foreign key constraints

## MCP Tools Used
None - This agent creates database schemas and adapters using SQL.js and TypeScript. No browser automation or external MCP tools required.

## State Management

### Database Architecture State
```json
{
  "schemaCreated": {
    "tables": ["users", "posts", "comments", "ratings"],
    "indexes": ["idx_posts_user_id", "idx_comments_post_id"],
    "foreignKeys": 4,
    "checkConstraints": 3
  },
  "migrationStatus": {
    "initialSchema": "completed",
    "columnsAdded": ["email_verified", "email_verified_at"],
    "schemaVersion": 1
  },
  "seedData": {
    "adminAccountCreated": true,
    "testDataLoaded": false
  },
  "persistence": {
    "databaseFile": "server/data/project.sqlite",
    "fileSize": "24KB",
    "lastPersisted": "2025-11-16T10:30:00Z"
  }
}
```

### Agent Statistics
```json
{
  "tablesCreated": 4,
  "columnsTotal": 32,
  "foreignKeysConfigured": 4,
  "indexesCreated": 6,
  "checkConstraintsAdded": 3,
  "migrationsSafe": true,
  "adminAccountSeeded": true,
  "queryPatternsDocumented": true,
  "preparedStatementsUsed": true,
  "databaseFileCreated": true
}
```

---

**Agent Version**: 1.0
**Based on**: reddit-quora-web-site codebase
**Last Updated**: 2025-11-16
**Compatible with**: SQL.js 1.x, SQLite syntax
