# Auth Patterns Reference

## JWT — Access + Refresh Token Pattern

```typescript
import jwt from 'jsonwebtoken'
import bcrypt from 'bcrypt'

// Generate token pair
function generateTokens(userId: string, orgId: string, role: string) {
  const accessToken = jwt.sign(
    { userId, orgId, role },
    process.env.JWT_SECRET!,
    { expiresIn: '15m' }
  )
  const refreshToken = jwt.sign(
    { userId },
    process.env.JWT_REFRESH_SECRET!,
    { expiresIn: '30d' }
  )
  return { accessToken, refreshToken }
}

// Auth middleware
function authenticate(req, res, next) {
  const token = req.headers.authorization?.split(' ')[1]
  if (!token) return res.status(401).json({ error: 'No token' })
  try {
    req.user = jwt.verify(token, process.env.JWT_SECRET!)
    next()
  } catch (e: any) {
    if (e.name === 'TokenExpiredError')
      return res.status(401).json({ error: 'Token expired', code: 'REFRESH_NEEDED' })
    return res.status(401).json({ error: 'Invalid token' })
  }
}

// Refresh endpoint
app.post('/auth/refresh', async (req, res) => {
  const { refreshToken } = req.cookies
  if (!refreshToken) return res.status(401).json({ error: 'No refresh token' })
  
  const payload = jwt.verify(refreshToken, process.env.JWT_REFRESH_SECRET!) as any
  
  // Check refresh token not revoked (store valid tokens in Redis or DB)
  const isValid = await redis.get(`refresh:${payload.userId}`) === refreshToken
  if (!isValid) return res.status(401).json({ error: 'Token revoked' })
  
  const user = await db.user.findUnique({ where: { id: payload.userId } })
  const tokens = generateTokens(user.id, user.orgId, user.role)
  
  // Rotate — invalidate old, store new
  await redis.set(`refresh:${user.id}`, tokens.refreshToken, 'EX', 60 * 60 * 24 * 30)
  
  res.cookie('refresh_token', tokens.refreshToken, { httpOnly: true, secure: true, sameSite: 'strict' })
  res.json({ accessToken: tokens.accessToken })
})
```

---

## OTP via Phone (SMS)

```typescript
// Using Twilio Verify
import twilio from 'twilio'
const client = twilio(process.env.TWILIO_SID, process.env.TWILIO_TOKEN)

// Send OTP
await client.verify.v2.services(process.env.TWILIO_SERVICE_SID)
  .verifications.create({ to: `+${phone}`, channel: 'sms' })

// Verify OTP
const result = await client.verify.v2.services(process.env.TWILIO_SERVICE_SID)
  .verificationChecks.create({ to: `+${phone}`, code: otp })

if (result.status === 'approved') {
  // Issue tokens
}
```

---

## OAuth2 (Google, GitHub, etc.)

```typescript
// Using Auth.js (Next.js)
// app/api/auth/[...nextauth]/route.ts
import NextAuth from 'next-auth'
import Google from 'next-auth/providers/google'
import GitHub from 'next-auth/providers/github'

export const { handlers, auth } = NextAuth({
  providers: [
    Google({ clientId: process.env.GOOGLE_ID, clientSecret: process.env.GOOGLE_SECRET }),
    GitHub({ clientId: process.env.GITHUB_ID, clientSecret: process.env.GITHUB_SECRET }),
  ],
  callbacks: {
    async signIn({ user }) {
      // Create or find user in your DB
      await upsertUser(user.email!)
      return true
    },
    async session({ session, token }) {
      session.user.id = token.sub!
      return session
    }
  }
})
```

---

## RBAC Pattern

```typescript
// Define permissions per role
const PERMISSIONS: Record<string, string[]> = {
  'resource:create': ['OWNER', 'ADMIN', 'MEMBER'],
  'resource:delete': ['OWNER', 'ADMIN'],
  'billing:manage':  ['OWNER'],
  'members:invite':  ['OWNER', 'ADMIN'],
  'members:remove':  ['OWNER'],
  'settings:update': ['OWNER', 'ADMIN'],
}

function can(user: { role: string }, permission: string): boolean {
  return PERMISSIONS[permission]?.includes(user.role) ?? false
}

// Middleware
function requirePermission(permission: string) {
  return (req, res, next) => {
    if (!can(req.user, permission)) {
      return res.status(403).json({ error: 'Insufficient permissions' })
    }
    next()
  }
}

// Usage
app.delete('/api/members/:id', authenticate, requirePermission('members:remove'), handler)
```
