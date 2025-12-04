---
name: "insforge"
displayName: "InsForge"
description: "The backend built for AI-assisted development - PostgreSQL database, file storage, edge functions, and authentication through MCP"
keywords: ["insforge", "backend", "database", "postgres", "storage", "edge-functions", "auth", "mcp", "ai-development", "baas"]
---

# InsForge Power

## Overview

InsForge is the backend built for AI-assisted development. Unlike traditional BaaS platforms, InsForge is designed from the ground up for AI agents to manage full backend operations through the Model Context Protocol (MCP).

Tell your AI agent what to build, and it scaffolds the entire backend - database tables, storage buckets, edge functions, authentication - all through natural language.

**Key capabilities:**

- **Database**: PostgreSQL with RLS, raw SQL execution, schema inspection
- **Storage**: File buckets with public/private access controls
- **Edge Functions**: Serverless JavaScript on Deno runtime
- **Authentication**: Email/password + OAuth providers
- **AI Integration**: Built-in chat completions and image generation

**Perfect for:**

- AI-assisted full-stack development
- Rapid prototyping with natural language
- Self-hosted backend (Docker, cloud, on-premise)
- Applications requiring data sovereignty

**Authentication**: Requires API key from InsForge dashboard.

## Available MCP Servers

### insforge

**Package:** `@insforge/mcp`
**Connection:** NPX with API key authentication

## Tools

### Documentation

#### fetch-docs
Fetch SDK documentation for database, storage, functions, auth, or AI integration.

**Parameters:**
- `docType` (required): `"instructions"` | `"db-sdk"` | `"storage-sdk"` | `"functions-sdk"` | `"ai-integration-sdk"` | `"auth-components-react"`

#### get-anon-key
Generate anonymous JWT for client-side applications.

#### download-template
Download pre-configured starter template (React).

**Parameters:**
- `frame` (required): `"react"`
- `projectName` (optional): Project directory name

---

### Database

#### get-backend-metadata
Get system overview: all tables, buckets, functions, auth config, metrics.

#### get-table-schema
Get detailed table schema including columns, indexes, RLS policies.

**Parameters:**
- `tableName` (required): Table name to inspect

#### run-raw-sql
Execute raw SQL queries. Admin access required.

**Parameters:**
- `query` (required): SQL query
- `params` (optional): Parameterized query values

#### bulk-upsert
Import data from CSV or JSON file.

**Parameters:**
- `table` (required): Target table
- `filePath` (required): Path to data file
- `upsertKey` (optional): Unique key for upsert

---

### Storage

#### create-bucket
Create storage bucket.

**Parameters:**
- `bucketName` (required): Bucket name
- `isPublic` (optional): Public access (default: true)

#### list-buckets
List all storage buckets.

#### delete-bucket
Delete empty bucket.

**Parameters:**
- `bucketName` (required): Bucket to delete

---

### Edge Functions

#### create-function
Deploy serverless function on Deno runtime.

**Parameters:**
- `name` (required): Display name
- `slug` (optional): URL identifier
- `codeFile` (required): Path to JavaScript file
- `description` (optional): Function description
- `status` (optional): `"draft"` | `"active"`

**Function format:**
```javascript
module.exports = async function(request) {
  return new Response(JSON.stringify({ ok: true }), {
    headers: { "Content-Type": "application/json" }
  });
}
```

#### get-function
Get function details and source code.

**Parameters:**
- `slug` (required): Function identifier

#### update-function
Update function code or metadata.

**Parameters:**
- `slug` (required): Function to update
- `name`, `codeFile`, `description`, `status` (optional)

#### delete-function
Delete function permanently.

**Parameters:**
- `slug` (required): Function to delete

---

### Debugging

#### get-container-logs
Get logs from backend services.

**Parameters:**
- `source` (required): `"insforge.logs"` | `"postgREST.logs"` | `"postgres.logs"` | `"function.logs"`
- `limit` (optional): Number of logs (default: 20)

---

## Workflows

### Project Setup

```javascript
// 1. Get backend overview
get-backend-metadata()

// 2. Create schema
run-raw-sql({
  query: `
    CREATE TABLE users (
      id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
      email VARCHAR(255) UNIQUE NOT NULL,
      name VARCHAR(255),
      created_at TIMESTAMP DEFAULT NOW()
    );
  `
})

// 3. Create storage
create-bucket({ bucketName: "uploads", isPublic: true })

// 4. Get client key
get-anon-key()
```

### Deploy Edge Function

```javascript
// 1. Create function file locally
// 2. Deploy
create-function({
  name: "Send Email",
  slug: "send-email",
  codeFile: "./functions/send-email.js"
})

// 3. Check logs
get-container-logs({ source: "function.logs" })
```

### Database Migration

```javascript
// 1. Check current schema
get-table-schema({ tableName: "users" })

// 2. Migrate
run-raw-sql({
  query: `
    ALTER TABLE users ADD COLUMN avatar_url TEXT;
    CREATE INDEX idx_users_email ON users(email);
  `
})

// 3. Verify
get-table-schema({ tableName: "users" })
```

---

## Best Practices

### Do:
- Use `get-backend-metadata` before making changes
- Use parameterized queries with `run-raw-sql`
- Enable RLS on tables with user data
- Test functions in `draft` before `active`
- Store API keys in environment variables

### Don't:
- Skip schema inspection before migrations
- Run destructive SQL without backups
- Create public buckets for sensitive files
- Commit API keys to version control
- Deploy functions directly to `active`

---

## Troubleshooting

### "Unauthorized" or "Invalid API Key"
- Verify `API_KEY` environment variable
- Check key is valid in dashboard
- Ensure `API_BASE_URL` is correct

### "Table not found"
- Use `get-backend-metadata` to list tables
- Check exact name (case-sensitive)

### "Function deployment failed"
- Verify `codeFile` path exists
- Check function exports correctly
- Review `function.logs` for errors

### "SQL syntax error"
- Verify PostgreSQL syntax
- Check table/column names exist
- Use parameterized queries for values

---

## Configuration

```json
{
  "mcpServers": {
    "insforge": {
      "command": "npx",
      "args": ["-y", "@insforge/mcp@latest"],
      "env": {
        "API_KEY": "${INSFORGE_API_KEY}",
        "API_BASE_URL": "${INSFORGE_URL}"
      }
    }
  }
}
```

**Get your API key:**
1. Log in to InsForge dashboard
2. Settings > API Keys
3. Create or copy key

---

**Package:** `@insforge/mcp`
**Source:** Official InsForge
**License:** MIT
