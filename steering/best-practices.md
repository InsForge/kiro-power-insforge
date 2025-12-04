---
inclusion: always
---

# InsForge Best Practices

## Database Design

### Schema Fundamentals
- UUID primary keys: `id UUID PRIMARY KEY DEFAULT gen_random_uuid()`
- Always include timestamps: `created_at TIMESTAMP DEFAULT NOW()`
- Index frequently queried columns
- Use `ON DELETE CASCADE` for related data
- Prefer `VARCHAR(n)` over `TEXT` for bounded fields

### Naming
- Tables: lowercase, plural (`users`, `posts`, `order_items`)
- Columns: snake_case (`created_at`, `user_id`)
- Indexes: `idx_{table}_{column}`
- Foreign keys: `{table}_id`

### Multi-Tenancy
- Include `user_id` or `organization_id` on user-scoped tables
- Enable RLS on every table with user data
- Validate tenant context on every operation

---

## Row Level Security

### Enable RLS
```sql
ALTER TABLE posts ENABLE ROW LEVEL SECURITY;
```

### Common Policies

**Users own their data:**
```sql
CREATE POLICY "Users manage own data" ON posts
  FOR ALL USING (user_id = current_user_id());
```

**Public read, authenticated write:**
```sql
CREATE POLICY "Public read" ON posts FOR SELECT USING (published = true);
CREATE POLICY "Auth write" ON posts FOR INSERT WITH CHECK (current_user_id() IS NOT NULL);
```

**Organization access:**
```sql
CREATE POLICY "Org access" ON projects FOR ALL
  USING (org_id IN (
    SELECT org_id FROM org_members WHERE user_id = current_user_id()
  ));
```

---

## Storage

### Bucket Strategy
- `avatars` - Profile pictures (public)
- `uploads` - General uploads (public)
- `documents` - Private files (private)

### File Uploads
- Validate file types before upload
- Enforce size limits (5MB recommended for images)
- Generate unique paths: `{user_id}/{timestamp}-{random}.{ext}`
- Never trust user-provided filenames

---

## Edge Functions

### Structure
```javascript
module.exports = async function(request) {
  // Validate input
  // Process
  // Return Response
  return new Response(JSON.stringify({ ok: true }), {
    headers: { "Content-Type": "application/json" }
  });
}
```

### Guidelines
- Validate all input
- Use `Deno.env.get('SECRET')` for secrets
- Return appropriate HTTP status codes
- Test in `draft` before `active`
- Check `function.logs` for debugging

---

## Security Checklist

### Database
- [ ] RLS enabled on user-data tables
- [ ] Policies for SELECT, INSERT, UPDATE, DELETE
- [ ] Parameterized queries (no string concatenation)
- [ ] UUID primary keys

### Auth
- [ ] API keys in environment variables
- [ ] Anon key for client, admin key server-only
- [ ] HTTPS everywhere

### Storage
- [ ] Private buckets for sensitive files
- [ ] File type validation
- [ ] Size limits enforced

---

## Common Patterns

### User + Profile
```sql
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email VARCHAR(255) UNIQUE NOT NULL,
  created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE profiles (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID UNIQUE REFERENCES users(id) ON DELETE CASCADE,
  display_name VARCHAR(255),
  avatar_url TEXT
);
```

### Content with Ownership
```sql
CREATE TABLE posts (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  title VARCHAR(255) NOT NULL,
  content TEXT,
  published BOOLEAN DEFAULT false,
  created_at TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_posts_user_id ON posts(user_id);
CREATE INDEX idx_posts_published ON posts(published) WHERE published = true;
```

### Organizations
```sql
CREATE TABLE organizations (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(255) NOT NULL,
  slug VARCHAR(100) UNIQUE NOT NULL
);

CREATE TABLE org_members (
  org_id UUID REFERENCES organizations(id) ON DELETE CASCADE,
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  role VARCHAR(50) DEFAULT 'member',
  PRIMARY KEY (org_id, user_id)
);
```

---

## Performance

- Create indexes for WHERE clause columns
- Use pagination: `.range(0, 9)`
- Select only needed columns (avoid `SELECT *`)
- Use connection pooling for high traffic
