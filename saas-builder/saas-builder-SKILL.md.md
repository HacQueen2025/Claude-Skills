---
name: saas-builder
description: >
  World-class SaaS product engineering skill. Use this skill whenever the user asks to build,
  design, scaffold, or scale any SaaS product, feature, dashboard, pricing page, onboarding
  flow, subscription system, auth layer, multi-tenant architecture, API, or anything related
  to running a software-as-a-service business — in any language, any stack, any market.
  Covers frontend (React, Vue, Svelte, Next.js, Angular), backend (Node.js, Python, Go, Ruby
  on Rails, Java Spring, .NET, Rust, PHP), all major databases, and global payment providers
  (Stripe, Paddle, Razorpay, Lemon Squeezy, PayPal, Chargebee). Also triggers for: "add a
  feature to my app", "build me X", "how should I structure Y", startup architecture, B2B
  product design, API design, SaaS metrics. Use aggressively — any product-building question.
---

# SaaS Builder Skill

You are a world-class SaaS product engineer and technical architect. You help teams design,
scaffold, and ship production-grade SaaS products across any stack, any market, any scale —
from zero-to-one MVPs to enterprise-grade platforms.

---

## Step 1 — Clarify Before Building

Before writing code, extract from context or briefly ask:
1. **Who is the user?** (end-user, admin, B2B customer, API consumer, field worker)
2. **Happy path in one sentence.**
3. **Stack?** If unspecified, recommend from tables below.

---

## Step 2 — Stack Selection Guide

### Frontend Frameworks
| Use Case | Recommended | Why |
|---|---|---|
| App + marketing in one codebase | Next.js (App Router) | SSR + SSG + API routes |
| Dashboard / data-heavy SPA | React + Vite + TanStack Query | Fast dev, great caching |
| Real-time reactive | SvelteKit or Vue 3 + Nuxt | Lean bundle, reactive |
| Mobile-first PWA | React + Vite + Capacitor | Web + native from one codebase |
| Content / documentation sites | Astro + Alpine.js | Near-zero JS, fast |
| Enterprise / large teams | Angular | Opinionated, scalable |

### Backend Languages & Frameworks
| Language | Framework | Best For |
|---|---|---|
| Node.js / TypeScript | Express, Fastify, NestJS | JS teams, APIs, real-time |
| Python | FastAPI, Django, Flask | ML integration, rapid CRUD |
| Go | Gin, Echo, Fiber, Chi | High-throughput, microservices |
| Ruby | Rails | Fast MVP, convention-driven |
| Java | Spring Boot, Quarkus | Enterprise, JVM ecosystem |
| C# / .NET | ASP.NET Core, Minimal API | Microsoft stack, enterprise |
| Rust | Axum, Actix-Web | Max performance + memory safety |
| PHP | Laravel | Rapid development, wide hosting |
| Elixir | Phoenix | Real-time, high concurrency |

### Database Selection
| Type | Best Options | Use When |
|---|---|---|
| Relational | **PostgreSQL** (default), MySQL, SQLite | Structured data, transactions, default choice |
| Document | MongoDB, Firestore, CouchDB | Flexible schema, nested documents |
| Key-Value | Redis, DynamoDB, KeyDB | Caching, sessions, pub/sub, queues |
| Time-Series | TimescaleDB, InfluxDB, QuestDB | Metrics, analytics, IoT, events |
| Search | Elasticsearch, Meilisearch, Typesense | Full-text search, faceted filters |
| Vector | pgvector, Pinecone, Weaviate, Chroma | AI features, semantic search, RAG |
| Graph | Neo4j, FalkorDB | Social graphs, recommendations |
| Columnar | ClickHouse, BigQuery | Analytics, data warehousing |

### ORM / Query Builders
- **Node.js/TS:** Prisma (DX king), Drizzle (lightweight), Kysely (type-safe SQL)
- **Python:** SQLAlchemy, Django ORM, Tortoise ORM (async)
- **Go:** sqlc (type-safe from SQL), GORM, Bun
- **Java:** Hibernate/JPA, jOOQ (type-safe SQL)
- **Ruby:** ActiveRecord (Rails), Sequel
- **C#:** Entity Framework Core, Dapper
- **Rust:** sqlx, SeaORM, Diesel
- **PHP:** Eloquent (Laravel), Doctrine

---

## Step 3 — Multi-Tenancy Architecture

Choose based on isolation requirements:

**Row-Level Isolation** (default — 90% of SaaS)
```sql
-- Every table has organization_id column
-- PostgreSQL RLS enforces isolation automatically
ALTER TABLE resources ENABLE ROW LEVEL SECURITY;
CREATE POLICY tenant_isolation ON resources
  USING (organization_id = current_setting('app.org_id')::uuid);

-- Set at request start (middleware)
await db.query("SELECT set_config('app.org_id', $1, true)", [req.user.orgId])
```

**Schema-Per-Tenant** (better isolation, harder migrations)
```sql
-- Each org gets their own schema
SET search_path = 'org_abc123';
SELECT * FROM resources; -- auto-scoped, no WHERE needed
```

**Database-Per-Tenant:** Only for enterprise contracts requiring hard data isolation
(HIPAA, SOC2 Type II, financial regulations).

---

## Step 4 — Subscription & Billing

### Provider Selection
| Provider | Use When | Key Feature |
|---|---|---|
| **Stripe** | Default (global) | Best API, 135+ currencies, all billing models |
| **Paddle** | Selling to EU/global | Built-in VAT/sales tax, Merchant of Record |
| **Lemon Squeezy** | Indie SaaS, solo | Simple, flat pricing, MoR |
| **Razorpay** | India primary market | Local methods, UPI, EMI |
| **PayPal** | Emerging markets | Where cards are rare |
| **Chargebee** | Complex billing | Usage-based, metered, trials |

### Stripe Implementation
```typescript
// Create subscription
const subscription = await stripe.subscriptions.create({
  customer: customerId,
  items: [{ price: priceId }],
  payment_behavior: 'default_incomplete',
  expand: ['latest_invoice.payment_intent'],
  metadata: { organizationId: org.id },
})

// Webhook handler (verify signature first — always)
app.post('/webhooks/stripe', express.raw({ type: 'application/json' }), async (req, res) => {
  const event = stripe.webhooks.constructEvent(req.body, req.headers['stripe-signature'], process.env.STRIPE_WEBHOOK_SECRET)
  
  switch (event.type) {
    case 'customer.subscription.created':
    case 'customer.subscription.updated':
      await syncSubscription(event.data.object); break
    case 'customer.subscription.deleted':
      await handleChurn(event.data.object); break
    case 'invoice.payment_failed':
      await handleDunning(event.data.object); break
    case 'invoice.payment_succeeded':
      await grantAccess(event.data.object); break
  }
  res.json({ received: true })
})
```

### Universal Tier Template
```
Free:       Core features, usage-capped (e.g., 3 projects, 1k API calls/mo)
            No credit card required — lower friction, higher signups
Starter:    $9–29/mo — removes core limits, email support
Pro:        $49–99/mo — advanced features, API access, priority support
Enterprise: Custom — SSO, SLA, audit logs, dedicated support, compliance
```
**Pricing psychology:** Highlight middle tier. Annual = "2 months free" (20–25% off).
Show comparison table. Add social proof near CTA.

### Feature Flags
```typescript
// DB-backed flags (early stage — no third-party needed)
// org.features: { "ai_features": true, "export_pdf": false, "api_access": true }

function hasFeature(org: Organization, flag: string): boolean {
  return org.features?.[flag] === true
}

// At scale: LaunchDarkly, Flagsmith (OSS), GrowthBook, PostHog feature flags
```

---

## Step 5 — Authentication

**Rule: Never implement auth from scratch.** Pick a library or service.

| Option | Stack | Notes |
|---|---|---|
| Clerk | React/Next.js | Best DX, orgs + RBAC built-in |
| Auth0 | Any | Enterprise-grade, expensive at scale |
| Supabase Auth | Supabase stack | Free, included |
| Auth.js (NextAuth) | Next.js | OSS, OAuth + credentials |
| Lucia | Any JS/TS | Lightweight, self-hosted |
| Devise + Omniauth | Rails | Industry standard |
| Django Allauth | Django | Batteries included |
| Spring Security | Java | Enterprise standard |
| ASP.NET Identity | .NET | Built into framework |
| Keycloak | Any (enterprise) | Full IAM, self-hosted |

**JWT Pattern:**
```typescript
// Access token: 15 min. Refresh: 30 days in httpOnly cookie.
res.cookie('refresh_token', refreshToken, {
  httpOnly: true,
  secure: process.env.NODE_ENV === 'production',
  sameSite: 'strict',
  maxAge: 30 * 24 * 60 * 60 * 1000,
})
// Rotate refresh tokens on every use — detect token reuse as attack signal
```

**RBAC (Role-Based Access Control):**
```typescript
// roles: OWNER > ADMIN > MEMBER > VIEWER
const PERMISSIONS = {
  'reports:create': ['OWNER', 'ADMIN', 'MEMBER'],
  'reports:delete': ['OWNER', 'ADMIN'],
  'billing:manage': ['OWNER'],
  'members:invite': ['OWNER', 'ADMIN'],
}

function can(user: User, permission: string): boolean {
  return PERMISSIONS[permission]?.includes(user.role) ?? false
}
```

**SSO:** Use BoxyHQ (OSS) or Auth0 for SAML 2.0. Never implement SAML manually.

---

## Step 6 — API Design

### RESTful Conventions
```
GET    /api/v1/resources           list   (paginated, filterable)
POST   /api/v1/resources           create
GET    /api/v1/resources/:id       read
PATCH  /api/v1/resources/:id       partial update
PUT    /api/v1/resources/:id       full replace
DELETE /api/v1/resources/:id       delete

Nested: GET /api/v1/orgs/:orgId/members
```

### Response Format
```json
{
  "data": { "id": "abc", "name": "Example" },
  "meta": { "page": 1, "limit": 20, "total": 243, "hasMore": true },
  "error": null
}
```

### Pagination (use cursor for large datasets)
```typescript
// Cursor-based (recommended for >100k rows)
GET /api/v1/items?cursor=eyJpZCI6MTIzfQ==&limit=20

// Offset (fine for smaller datasets)
GET /api/v1/items?page=2&limit=20&sort=createdAt&order=desc
```

### Rate Limiting
```typescript
// Express
import rateLimit from 'express-rate-limit'
app.use('/api/', rateLimit({ windowMs: 60_000, max: 100, standardHeaders: true }))

// Default targets: 100 req/min (authed), 20 req/min (anon), 5 req/min (auth endpoints)
// Other stacks: slowapi (Python/FastAPI), golang.org/x/time/rate (Go),
//               django-ratelimit (Django), AspNetCoreRateLimit (.NET)
```

---

## Step 7 — Security Checklist

**Authentication:**
- [ ] Passwords hashed: bcrypt (cost 12) or Argon2id — never MD5/SHA1/unsalted SHA
- [ ] Refresh token rotation + reuse detection (treat reuse as account compromise)
- [ ] Login rate limit: 5 attempts → 15 min lockout
- [ ] MFA available: TOTP via `speakeasy` (JS), `pyotp` (Python), `rfc6238` (Go)

**Data:**
- [ ] All inputs validated server-side (Zod, Pydantic, Bean Validation, etc.)
- [ ] SQL: parameterized queries ALWAYS — string concatenation = instant SQL injection
- [ ] Sensitive data encrypted at rest: AES-256-GCM via KMS or `libsodium`
- [ ] PII fields documented and minimized

**Transport:**
- [ ] HTTPS enforced everywhere — HTTP → HTTPS redirect
- [ ] `Strict-Transport-Security: max-age=31536000; includeSubDomains`
- [ ] `Content-Security-Policy` configured
- [ ] CORS: explicit origin whitelist in production (never `*`)

**Infrastructure:**
- [ ] Secrets in env vars — never committed to Git
- [ ] `.env.example` in repo, `.env` in `.gitignore`
- [ ] Dependency scanning: Dependabot (GitHub) or Snyk
- [ ] Error monitoring: Sentry (free tier sufficient to start)

---

## Step 8 — Performance

**Caching:**
- L1: In-process (node-cache, Python `functools.lru_cache`, Go `sync.Map`) — microseconds
- L2: Redis — sub-ms for hot data; 5-min TTL for dashboard aggregates
- L3: CDN (Cloudflare) — static assets, edge-cached GET responses

**Database:**
- Index every FK column and every frequently-filtered column
- `EXPLAIN ANALYZE` every query over 100ms
- Connection pooling: PgBouncer (Postgres), HikariCP (Java), `pgxpool` (Go)
- Read replicas for analytics/reporting queries

**Frontend:**
- Bundle target: <200KB gzipped initial JS
- Images: WebP format, explicit width/height, `loading="lazy"`
- Code splitting: dynamic import at route boundaries
- Data fetching: TanStack Query or SWR for deduplication + caching

---

## Step 9 — Testing

```
Unit tests:         Business logic, utils, pure functions
                    JS: Jest/Vitest  |  Python: pytest  |  Go: testing  |  Java: JUnit 5
Integration tests:  API routes with real (test) DB
                    JS: Supertest    |  Python: httpx   |  Go: httptest  |  Java: MockMvc
E2E tests:          Critical user flows (signup → first value moment)
                    Playwright (recommended) or Cypress
Load tests:         Pre-launch on critical paths — k6, Artillery, Locust
Coverage targets:   80% unit / 60% integration / critical paths E2E
```

---

## Step 10 — Deployment

| Stage | Platform | Notes |
|---|---|---|
| MVP / $0–$10k MRR | Railway, Render, Fly.io | Zero DevOps, git push to deploy |
| Growth / $10k–$100k | AWS ECS, GCP Cloud Run | Containerized, auto-scaling |
| Scale / $100k+ | Kubernetes (EKS/GKE/AKS) | Full control, cost optimization |

**Non-negotiable for every deploy:**
- `GET /health` → `200 OK` health check endpoint
- Graceful shutdown (drain in-flight requests before exit)
- Database migrations run as separate step before new code
- Zero-downtime deploy (rolling or blue/green)

---

## Step 11 — SaaS Metrics to Track

```
Acquisition:   Signups/day, CAC, channel attribution
Activation:    Time to first value, % completing onboarding
Revenue:       MRR, ARR, ARPU, expansion MRR
Retention:     Churn rate (target <2%/mo for SMB, <1% enterprise)
Engagement:    DAU/MAU ratio (target >20% = sticky product)
Support:       Ticket volume/feature, NPS, CSAT
```

---

## Reference Files
- `references/payment-providers.md` — Stripe, Paddle, Razorpay, Lemon Squeezy code
- `references/auth-patterns.md` — JWT, OAuth2, OTP, SAML, RBAC implementations
- `references/db-patterns.md` — Schema design, migrations, RLS, indexing, sharding
- `references/api-patterns.md` — REST, GraphQL, WebSocket, gRPC, event-driven
