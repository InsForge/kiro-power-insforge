---
inclusion: manual
---

# Get Started with InsForge

## Trigger

When user says "Get started with InsForge" or similar.

## Communication

Keep responses succinct:
- "Created users table with 4 columns"
- "Added DATABASE_URL to .env"

Avoid verbose explanations.

---

## Steps

### Step 1: Verify Connection

```javascript
get-backend-metadata()
```

**If fails:** Check `API_KEY` and `API_BASE_URL` environment variables.

**If succeeds:** Show brief summary: "Connected. Found X tables, Y buckets."

### Step 2: Understand Project

**Empty project:** Ask what they're building (1-2 questions max).

**Existing project:** Infer from codebase. Look for existing database code, ORMs, API routes.

### Step 3: Framework Setup

**New project:**
```javascript
download-template({ frame: "react", projectName: "my-app" })
```

**Existing project:** Install SDK:
```bash
npm install @insforge/js
```

Add to `.env`:
```
INSFORGE_API_URL=https://your-instance.insforge.app
INSFORGE_ANON_KEY=your-anon-key
```

Get anon key:
```javascript
get-anon-key()
```

### Step 4: Database Schema

**Check existing:**
```javascript
get-backend-metadata()
```

**If no tables, offer options:**
1. Starter schema (users table)
2. Custom schema
3. Skip for now

**Starter schema:**
```javascript
run-raw-sql({
  query: `
    CREATE TABLE users (
      id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
      email VARCHAR(255) UNIQUE NOT NULL,
      name VARCHAR(255),
      avatar_url TEXT,
      created_at TIMESTAMP DEFAULT NOW()
    );
    CREATE INDEX idx_users_email ON users(email);
  `
})
```

### Step 5: Storage (Optional)

Ask if they need file storage.

**If yes:**
```javascript
create-bucket({ bucketName: "uploads", isPublic: true })
```

### Step 6: Connect Code

**React/Next.js:**
```javascript
import { createClient } from '@insforge/js';

export const insforge = createClient(
  process.env.NEXT_PUBLIC_INSFORGE_URL,
  process.env.NEXT_PUBLIC_INSFORGE_ANON_KEY
);

// Query
const { data } = await insforge.from('users').select('*');

// Insert
await insforge.from('posts').insert({ title: 'Hello' });

// Upload
await insforge.storage.from('uploads').upload('file.png', file);
```

### Step 7: What's Next

"You're set! I can help with:
- RLS policies for security
- More database tables
- Edge functions
- Complex queries"

---

## Notes

- Be succinct, guide step-by-step
- Check codebase context before each step
- Provide working code examples
- Offer manual fallback if automated steps fail
