
# Build Prompt: Renaissance Wigs Order Management System

> Paste this whole file into Claude Code as your first message (or save it as `PROJECT.md` in an empty repo and tell Claude Code "read PROJECT.md and start with Phase 0").

---

## Your role

You are building a production web application for **Renaissance Wigs**, a wholesale wig distributor. You are the sole engineer. Work in phases, commit after each phase, and stop for my review at each phase gate. Do not attempt to build the entire system in one pass.

## The business (context you need)

Renaissance Wigs sells wigs wholesale to retail wig stores and salons. Orders come in through Shopify. Today the entire operation runs on one large Excel workbook with a separate worksheet per wig collection, each with a **different column layout** (because Jotform exports each collection differently). Order status is communicated by **row color** and by **manually moving rows between sections**.

**People:**
- **Itzel** — watches incoming Jotform orders, copy-pastes them into the operational spreadsheet
- **Anne** — the operational core: fulfillment, inventory checks, customer comms (WhatsApp), returns, shipping
- **Sammy** — manager; reviews accounts with billing issues or low sales activity
- **Customers** — retail stores/salons; they hold inventory on **consignment**

**Systems:**
- Shopify → Jotform → Excel (order intake)
- Excel workbook (all daily operations)
- QuickBooks Desktop Enterprise (accounting + inventory of record)

### Hard constraint: do not touch QuickBooks

QuickBooks Desktop Enterprise stays exactly as it is. It remains the accounting and inventory system of record. The new system **reads from** QuickBooks when extra detail is needed (customer records, inventory quantities, invoice history). It never writes to it, and it never replaces it. Build QuickBooks access behind an interface (`QuickBooksClient`) with a mock implementation for development — real integration is a later phase and will likely need QuickBooks Web Connector / QBXML or a hosted connector.

## The workflow to model

Orders move through these states. This state machine is the heart of the app — get it right before anything else.

```
NEW → (inventory available?) 
   ├─ yes → PROCESSING → SHIPPED → FULFILLED
   └─ no  → AWAITING_CUSTOMER_DECISION  (Anne offers alternatives via WhatsApp)
              ├─ customer accepts alternative → PROCESSING → ... → FULFILLED
              └─ customer declines → check incoming shipments
                    ├─ arriving ≤ 7 days → WAITING_FOR_SHIPMENT → PROCESSING → ...
                    └─ not soon → customer inventory recovery
                          → RETURN_REQUEST_PENDING   (confirm customer still has it)
                          → WAITING_ON_RETURN        (pickup scheduled, driver assigned)
                          → item received → PROCESSING → ... → FULFILLED
```

Two orthogonal flags that can apply to any order (they are **not** states — model them as holds):
- `HOLD_WITH_SAMMY` — account needs manager review (< 3 sales this month, outstanding balance, other concerns)
- `NOT_CLEARED_WITH_BILLING` — money owed, returns incomplete, unresolved billing

**Alternatives Anne offers when stock is out:** different color, different length, different cap size, different collection, similar product, discounted alternative.

**Consignment recovery rule:** if a retail customer has held a wig for **more than 14 days**, Anne may contact them to confirm it's unsold and request it back. Make the 14-day threshold a configurable business rule, not a hardcoded number.

**Geography:** customers are color-coded by geographic area so driver pickups and deliveries can be batched. Model a `region`/`zone` field on customers and let return-pickup views group by it.

## What to build

### Tech stack (use this unless you have a strong, stated reason not to)
- **Next.js (App Router) + TypeScript**
- **PostgreSQL + Prisma**
- **Tailwind + shadcn/ui**
- **NextAuth** with role-based access (`ADMIN`, `MANAGER`, `OPERATIONS`, `WAREHOUSE`)
- **Zod** for all input validation, shared between client and server
- **Vitest** for unit tests, **Playwright** for a smoke path
- Background jobs: a simple queue (BullMQ or Postgres-backed) for webhook processing and aging checks
- Deployable to a single Docker Compose stack

### Core data model (starting point — refine it, then show me the schema before writing app code)

```
Customer        id, name, contactName, phone (WhatsApp), email, region, 
                billingStatus, holdWithSammy, quickbooksCustomerId, notes
Product         id, collection, style, color, length, capSize, sku, 
                quickbooksItemId, active
InventoryItem   productId, quantityOnHand, quantityReserved, location
Order           id, orderNumber, customerId, source (SHOPIFY|MANUAL), status, 
                placedAt, promisedDate, shippedAt, fulfilledAt, holds[]
OrderLine       orderId, productId, quantityRequested, quantityFulfilled, 
                substitutedForProductId
Shipment        orderId, carrier, trackingNumber, shippedAt
IncomingShipment  productId, quantityExpected, expectedArrival, supplier, status
Consignment     customerId, productId, quantity, placedWithCustomerAt, 
                status (WITH_CUSTOMER|SOLD|RETURN_REQUESTED|IN_TRANSIT|RETURNED)
ReturnRequest   consignmentId, orderId (the order it's fulfilling), status, 
                confirmedWithCustomerAt, pickupScheduledAt, driverId, receivedAt
Driver          id, name, phone, regionsCovered
Note            polymorphic: entityType, entityId, authorId, body, createdAt
Message         orderId, customerId, channel (WHATSAPP), direction, body, 
                status (DRAFT|APPROVED|SENT), draftedByAI, approvedById, sentAt
AuditEvent      entityType, entityId, actorId, action, before, after, createdAt
```

**Every state change writes an `AuditEvent`.** The single biggest failure of the current process is that there's no history of what happened to an order. Order detail pages must show a complete, permanent timeline.

### Screens (in priority order)
1. **Order board** — kanban-style columns by status, with filters (collection, customer, region, age). This replaces the colored spreadsheet rows. Drag between columns triggers validated state transitions, never raw status writes.
2. **Order detail** — line items, inventory position, full timeline, notes, message thread, hold badges.
3. **Inventory** — searchable by collection / style / color / length / cap size; shows on-hand, reserved, incoming, and out-on-consignment for each product.
4. **Alternatives panel** — inside an order, when a line is short: ranked suggestions with a visible reason for each.
5. **Returns / recovery board** — pending confirmations, scheduled pickups, in-transit, grouped by region for driver routing.
6. **Account review queue** — Sammy's view of flagged accounts with the reason they were flagged.
7. **Manager dashboard** — order pipeline counts, order aging, inventory levels, consignment aging, return activity, fulfillment throughput, billing holds.

### Automation
- Shopify webhook → create order automatically, assign order number, land it in `NEW`. Also support a Jotform/CSV import path as a fallback and for backfill.
- Auto-check inventory on order creation and flag shortages.
- Auto-advance to `WAITING_FOR_SHIPMENT` when a matching incoming shipment lands within the configured window.
- Nightly job: flag accounts for Sammy, flag consignments past the 14-day threshold, flag aging orders.

### AI features (all human-in-the-loop — nothing sends or commits automatically)
- **Alternative product suggestions.** Rank substitutes by attribute closeness (collection → color → length → cap size), filtered to items actually in stock. Do the ranking deterministically in code first; use the model only to explain the choice and phrase it for the customer. A wrong substitute costs real money, so the ranking must be inspectable and testable.
- **WhatsApp message drafting** for: alternative offers, ETA updates, return requests, shipping notifications. Always saved as `DRAFT` and shown to Anne for edit and explicit approval before sending.
- **Note summarization** on long order threads.

Put every model call behind a single `lib/ai/` service with typed inputs/outputs, a mockable interface, and unit tests that don't hit the network.

## How I want you to work

**Phase 0 — Plan.** Read this document. Then write `docs/PLAN.md` containing: your questions for me, the refined data model, the state machine with allowed transitions, and the phase breakdown. **Stop and wait for my approval.**

**Phase 1 — Foundation.** Repo scaffold, Docker Compose (app + Postgres), Prisma schema, migrations, seed script with realistic fake data (~5 collections, ~200 products, ~30 customers, ~80 orders spread across every status, consignments of varying age). Auth with the four roles. Tests for the state machine.

**Phase 2 — Order management.** Order board, order detail, timeline, notes, manual order entry, transitions with validation. This alone should be enough to stop using the spreadsheet for order tracking.

**Phase 3 — Inventory, consignment, and returns.** Inventory views, incoming shipments, consignment tracking with aging, the return-request workflow, driver/region grouping.

**Phase 4 — Integrations.** Shopify webhooks + CSV/Jotform import. `QuickBooksClient` interface with a mock; real QBWC/QBXML integration only after we discuss the connection approach.

**Phase 5 — AI + messaging.** Alternatives engine, WhatsApp draft/approve flow, note summarization.

**Phase 6 — Dashboards and reporting.**

### Rules
- Ask me questions when a business rule is ambiguous. Do not invent policy silently. When you must assume something to keep moving, write it in `docs/ASSUMPTIONS.md` and flag it in your summary.
- Every business rule with a number in it (14 days, 7 days, 3 sales/month) goes in a config table or settings file, not inline in code.
- No raw status mutations anywhere — all transitions go through a single guarded service function.
- Write tests for the state machine, the alternatives ranking, and the aging/flagging jobs. UI tests can stay light.
- Keep the UI dense and fast. The users are power users who currently live in Excel; they will resent a slow, wizard-heavy interface. Keyboard shortcuts and bulk actions on the order board are worth real effort.
- Prefer boring, obvious code. Someone else will maintain this.

### Open questions I expect you to raise in Phase 0
These are known gaps in the spec — surface them, don't guess:
- Order volume per day, and how many concurrent users
- Whether partial fulfillment of a multi-line order is allowed
- Whether QuickBooks or this system becomes the source of truth for operational inventory counts
- How the WhatsApp send actually happens (Business API vs. a human copy-pasting the approved draft — the second is a perfectly acceptable v1)
- What the exact criteria are for "cleared with billing"
- How consignment placements get recorded today
- Migration: how we get the existing spreadsheet data in, given every collection has a different column layout
