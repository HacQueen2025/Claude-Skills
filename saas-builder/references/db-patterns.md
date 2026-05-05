# Database Patterns Reference

## Schema Design Principles

- Every table needs: `id` (cuid/uuid), `created_at`, `updated_at`
- Every tenant table needs: `organization_id` (FK + index)
- Use `uuid` for public-facing IDs, sequential int for internal joins (performance)
- Soft deletes: `deleted_at TIMESTAMP NULL` instead of hard deletes

## PostgreSQL RLS (Row Level Security)

```sql
-- Enable RLS on tenant tables
ALTER TABLE resources ENABLE ROW LEVEL SECURITY;

-- Policy: users only see their org's data
CREATE POLICY tenant_isolation ON resources
  FOR ALL
  USING (organization_id = current_setting('app.org_id')::uuid);

-- Set org context in middleware (runs on every request)
await db.$executeRaw`SELECT set_config('app.org_id', ${orgId}, true)`
```

## Indexing Strategy

```sql
-- Index every foreign key
CREATE INDEX idx_resources_org_id ON resources(organization_id);

-- Composite index for common filter + sort combos
CREATE INDEX idx_resources_org_status ON resources(organization_id, status);
CREATE INDEX idx_resources_org_created ON resources(organization_id, created_at DESC);

-- Partial index (index only rows matching a condition)
CREATE INDEX idx_active_subscriptions ON subscriptions(organization_id)
  WHERE status = 'active';

-- Always run CONCURRENTLY in production (non-blocking)
CREATE INDEX CONCURRENTLY idx_name ON table(column);
```

## Migration Best Practices

```sql
-- Safe column add (non-breaking)
ALTER TABLE users ADD COLUMN last_seen_at TIMESTAMP;

-- Safe column rename (use two-step over multiple deploys)
-- Step 1: Add new column, copy data
ALTER TABLE users ADD COLUMN full_name TEXT;
UPDATE users SET full_name = name;
-- Step 2 (next deploy): Remove old column
ALTER TABLE users DROP COLUMN name;

-- Never do in production: DROP COLUMN without a deprecation period
-- Never do: ALTER COLUMN type change on large tables (locks table)
```

## Prisma Schema Template

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model Organization {
  id        String   @id @default(cuid())
  name      String
  plan      Plan     @default(FREE)
  features  Json     @default("{}")
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  users     User[]
  resources Resource[]
}

model User {
  id             String       @id @default(cuid())
  email          String       @unique
  passwordHash   String?
  role           Role         @default(MEMBER)
  organizationId String
  organization   Organization @relation(fields: [organizationId], references: [id])
  createdAt      DateTime     @default(now())
  updatedAt      DateTime     @updatedAt

  @@index([organizationId])
}

model Resource {
  id             String       @id @default(cuid())
  organizationId String
  organization   Organization @relation(fields: [organizationId], references: [id])
  status         Status       @default(ACTIVE)
  deletedAt      DateTime?    // soft delete
  createdAt      DateTime     @default(now())
  updatedAt      DateTime     @updatedAt

  @@index([organizationId])
  @@index([organizationId, status])
}

enum Plan   { FREE STARTER PRO ENTERPRISE }
enum Role   { OWNER ADMIN MEMBER VIEWER }
enum Status { ACTIVE INACTIVE ARCHIVED }
```

## Query Performance

```typescript
// Avoid N+1 — always include relations you'll use
const users = await prisma.user.findMany({
  where: { organizationId },
  include: { profile: true, _count: { select: { posts: true } } }
})

// Pagination — cursor-based for large datasets
const items = await prisma.resource.findMany({
  where: { organizationId },
  take: 20,
  skip: cursor ? 1 : 0,
  cursor: cursor ? { id: cursor } : undefined,
  orderBy: { createdAt: 'desc' }
})

// Aggregation — use groupBy for counts/sums
const stats = await prisma.resource.groupBy({
  by: ['status'],
  where: { organizationId },
  _count: { id: true }
})
```
