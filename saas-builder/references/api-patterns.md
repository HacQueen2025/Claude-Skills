# API Patterns Reference

## REST — Full Implementation Example

```typescript
// Express + Zod validation + Prisma
import { z } from 'zod'

const CreateResourceSchema = z.object({
  name: z.string().min(1).max(100),
  description: z.string().optional(),
  status: z.enum(['ACTIVE', 'INACTIVE']).default('ACTIVE'),
})

app.post('/api/v1/resources', authenticate, async (req, res) => {
  const parsed = CreateResourceSchema.safeParse(req.body)
  if (!parsed.success) {
    return res.status(400).json({ error: parsed.error.flatten() })
  }

  const resource = await prisma.resource.create({
    data: { ...parsed.data, organizationId: req.user.orgId }
  })

  res.status(201).json({ data: resource, meta: null, error: null })
})
```

## GraphQL (with Pothos + Prisma)

```typescript
// Use when: complex data requirements, multiple clients, mobile app
import SchemaBuilder from '@pothos/core'

const builder = new SchemaBuilder({})

builder.queryType({
  fields: (t) => ({
    resources: t.field({
      type: ['Resource'],
      resolve: (_, __, ctx) =>
        prisma.resource.findMany({ where: { organizationId: ctx.user.orgId } })
    })
  })
})
```

## WebSocket (real-time features)

```typescript
// Socket.io — rooms per organization
import { Server } from 'socket.io'
const io = new Server(httpServer)

io.use((socket, next) => {
  const token = socket.handshake.auth.token
  socket.data.user = verifyToken(token)
  next()
})

io.on('connection', (socket) => {
  // Join org room — scoped broadcasts
  socket.join(`org:${socket.data.user.orgId}`)

  socket.on('resource:update', async (data) => {
    const updated = await updateResource(data)
    // Broadcast to all org members
    io.to(`org:${socket.data.user.orgId}`).emit('resource:updated', updated)
  })
})
```

## Background Jobs

```typescript
// BullMQ (Redis-backed queue)
import { Queue, Worker } from 'bullmq'

const emailQueue = new Queue('emails', { connection: redis })

// Add job
await emailQueue.add('welcome', { userId, email }, {
  attempts: 3,
  backoff: { type: 'exponential', delay: 2000 }
})

// Process job
new Worker('emails', async (job) => {
  await sendWelcomeEmail(job.data.email)
}, { connection: redis })
```

## API Versioning

```typescript
// URL versioning (simplest, most common)
app.use('/api/v1', v1Router)
app.use('/api/v2', v2Router)

// Deprecation header on old versions
v1Router.use((req, res, next) => {
  res.set('Deprecation', 'true')
  res.set('Sunset', 'Sat, 01 Jan 2026 00:00:00 GMT')
  next()
})
```

## Error Handling

```typescript
// Centralized error handler (Express)
class AppError extends Error {
  constructor(
    public message: string,
    public statusCode: number,
    public code: string
  ) { super(message) }
}

app.use((err: Error, req, res, next) => {
  if (err instanceof AppError) {
    return res.status(err.statusCode).json({
      data: null,
      error: { message: err.message, code: err.code }
    })
  }
  // Unexpected error — log it, don't expose details
  console.error(err)
  res.status(500).json({ data: null, error: { message: 'Internal server error', code: 'INTERNAL' } })
})
```
