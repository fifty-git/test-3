# 🔗 Webhook Delivery Manager

> Take-home assessment — Senior Fullstack Engineer

## What you're building

A single-page app where a merchant:

1. **Registers webhook endpoints** — adds URLs and picks which event types each one listens to
2. **Fires test events** — picks an event type, the system POSTs to all matching endpoints
3. **Sees a delivery log** — every attempt shown with status, response code, and a retry button for failures

That's the whole task. One feature, built well.

---

## Tech stack

- **Remix v3** (React Router) with TypeScript
- **JSON file** on disk for data (no database)
- **Styling:** your choice

**The rule:** `git clone` → `npm install` → `npm run dev` → working app.

---

## Getting started

```bash
npx create-remix@latest webhook-delivery-manager
cd webhook-delivery-manager
git init && git remote add origin <your-repo>
npm run dev
```

---

## How it works

```
┌─────────────────────────────────────────────────────┐
│                   SINGLE PAGE UI                    │
│                                                     │
│  ┌─────────────────┐  ┌──────────────────────────┐  │
│  │  Register        │  │  Trigger Event           │  │
│  │  Endpoint        │  │                          │  │
│  │  ┌────────────┐  │  │  Event Type: [dropdown]  │  │
│  │  │ URL        │  │  │                          │  │
│  │  │ Events: ☑☑ │  │  │  [ Fire Event ]          │  │
│  │  │ [ Add ]    │  │  │                          │  │
│  │  └────────────┘  │  └──────────────────────────┘  │
│  │                   │                               │
│  │  Registered:      │                               │
│  │  • https://a.io ✕│                               │
│  │  • https://b.io ✕│                               │
│  └─────────────────┘                                │
│                                                     │
│  ┌─────────────────────────────────────────────────┐│
│  │  Delivery Log                  Filter: [Status] ││
│  │  ─────────────────────────────────────────────  ││
│  │  URL          Event          Status   Action    ││
│  │  a.io/hook    ORDER_PLACED   ✅ SENT    —       ││
│  │  b.io/hook    ORDER_PLACED   ❌ FAILED  [Retry] ││
│  │  b.io/hook    PAYMENT_FAILED ✅ SENT    —       ││
│  └─────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────┘
```

### The dispatch flow

```
POST /api/events  { type: "ORDER_PLACED", payload: { orderId: "123" } }
  │
  ├─ Find all endpoints subscribed to ORDER_PLACED
  ├─ POST JSON payload to each endpoint URL
  │   └─ If it fails → retry once automatically
  ├─ Log every attempt (URL, status, response code, timestamp)
  │
  └─ Return summary to the UI
```

---

## Suggested structure

```
app/
├── routes/
│   ├── _index.tsx                    # The single page UI
│   └── api/
│       ├── endpoints.ts              # CRUD for webhook endpoints
│       ├── events.ts                 # POST — trigger event + dispatch
│       └── delivery-logs.ts          # GET log, POST retry
├── services/
│   ├── endpoints.server.ts           # Endpoint CRUD logic
│   ├── dispatch.server.ts            # Core dispatch + retry logic
│   └── delivery-log.server.ts        # Log read/write
├── data/
│   ├── store.server.ts               # JSON file read/write helpers
│   └── db.json                       # Auto-created "database"
├── types/
│   └── index.ts                      # Shared types
└── components/
    ├── EndpointForm.tsx
    ├── EventTrigger.tsx
    └── DeliveryLog.tsx
```

**Key principle:** routes call services, services call the data layer. No business logic in routes.

---

## Event types

Use these for the exercise:

```typescript
type EventType = "ORDER_PLACED" | "PAYMENT_FAILED" | "LOW_INVENTORY";
```

---

## What "done" looks like

- [ ] Register a webhook endpoint with a URL and event subscriptions
- [ ] Remove an endpoint
- [ ] Fire a test event and see it dispatched to matching endpoints
- [ ] Failed deliveries are auto-retried once
- [ ] Delivery log shows every attempt with status
- [ ] Retry a failed delivery manually from the log
- [ ] Filter the log by status

**Tip for testing webhooks:** use [https://webhook.site](https://webhook.site) for a free URL that receives and displays POSTs. To test failures, use a URL that doesn't exist.

---

## Bonus points

The core task above is all we need. These are your chance to show extra depth — pick whatever interests you.

- [ ] **SQLite** — Replace JSON with SQLite + proper schema/migrations
- [ ] **Docker** — One-command startup with Dockerfile
- [ ] **Tests** — Unit tests on dispatch logic, integration test on the API, or E2E with Playwright
- [ ] **Webhook signatures** — HMAC-SHA256 signed payloads with per-endpoint secrets
- [ ] **Real-time log** — Push new deliveries via SSE or WebSockets
- [ ] **Async dispatch** — Queue-based processing with PENDING → SENT/FAILED transitions

---

## DECISIONS.md

Create a short `DECISIONS.md` covering:

- How you structured the project and why
- Trade-offs you made for the time budget
- What you'd do differently with more time
- Any bonus features you implemented

---

## Evaluation

| Criteria | Weight |
|----------|--------|
| Architecture & Code Quality | 35% |
| Functionality | 30% |
| Bonus Features | 20% |
| Communication (README, DECISIONS.md, commits) | 15% |

---

## Submission checklist

- [ ] `npm install && npm run dev` works with zero issues
- [ ] README updated with your actual instructions
- [ ] DECISIONS.md written
- [ ] Clean commit history
- [ ] Tested the happy path at least once

Good luck! 🚀