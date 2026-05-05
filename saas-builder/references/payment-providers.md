# Payment Providers — Integration Reference

## Stripe (Global Default)

```typescript
// Install: npm install stripe
import Stripe from 'stripe'
const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!)

// Create customer
const customer = await stripe.customers.create({
  email: user.email,
  metadata: { organizationId: org.id }
})

// Create subscription
const subscription = await stripe.subscriptions.create({
  customer: customer.id,
  items: [{ price: priceId }],
  payment_behavior: 'default_incomplete',
  expand: ['latest_invoice.payment_intent'],
})

// Webhook verification
const event = stripe.webhooks.constructEvent(
  req.rawBody,
  req.headers['stripe-signature'],
  process.env.STRIPE_WEBHOOK_SECRET
)
```

**Test cards:** 4242 4242 4242 4242 (success), 4000 0000 0000 0002 (decline)

---

## Paddle (EU/Global — Merchant of Record)

```typescript
// Paddle handles VAT/tax automatically — great for EU
// Install: npm install @paddle/paddle-node-sdk

import { Paddle } from '@paddle/paddle-node-sdk'
const paddle = new Paddle(process.env.PADDLE_API_KEY!)

// Create subscription
const transaction = await paddle.transactions.create({
  items: [{ priceId: 'pri_xxx', quantity: 1 }],
  customData: { organizationId: org.id }
})

// Webhook
const event = paddle.webhooks.unmarshal(rawBody, secretKey, signature)
switch (event.eventType) {
  case 'subscription.activated': break
  case 'subscription.canceled': break
  case 'transaction.payment_failed': break
}
```

---

## Lemon Squeezy (Indie SaaS)

```typescript
// Simple REST API — no SDK needed
const response = await fetch('https://api.lemonsqueezy.com/v1/checkouts', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${process.env.LEMONSQUEEZY_API_KEY}`,
    'Content-Type': 'application/vnd.api+json',
  },
  body: JSON.stringify({
    data: {
      type: 'checkouts',
      attributes: {
        checkout_data: { email: user.email, custom: { org_id: org.id } }
      },
      relationships: {
        store: { data: { type: 'stores', id: process.env.LS_STORE_ID } },
        variant: { data: { type: 'variants', id: variantId } }
      }
    }
  })
})
```

---

## Razorpay (India)

```typescript
// Install: npm install razorpay
import Razorpay from 'razorpay'
const razorpay = new Razorpay({
  key_id: process.env.RAZORPAY_KEY_ID,
  key_secret: process.env.RAZORPAY_KEY_SECRET
})

// Create subscription
const plan = await razorpay.plans.create({
  period: 'monthly', interval: 1,
  item: { name: 'Pro Plan', amount: 199900, currency: 'INR' }
})

const subscription = await razorpay.subscriptions.create({
  plan_id: plan.id, customer_notify: 1, total_count: 12
})

// Verify webhook signature
const crypto = require('crypto')
const expected = crypto
  .createHmac('sha256', process.env.RAZORPAY_WEBHOOK_SECRET)
  .update(req.rawBody).digest('hex')
if (expected !== req.headers['x-razorpay-signature']) throw new Error('Invalid')
```

**Test cards:** 4111 1111 1111 1111 (success)
