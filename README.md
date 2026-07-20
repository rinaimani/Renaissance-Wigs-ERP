# Renaissance Wigs — Order Management System
## Build Prompt for Claude Code

> Save this as `PROJECT.md` at the root of an empty repository, then tell Claude Code:
> *"Read PROJECT.md. Do not write application code yet. Complete Phase 0 and stop."*

---

# PART ONE — BUSINESS CONTEXT

## Business profile

**Business name:** Renaissance Wigs

**What it does:** Renaissance Wigs is a wholesale wig distributor. It sells wigs in bulk to retail wig stores and salons, which then resell them to individual consumers. A significant portion of inventory sits with retail customers on **consignment** rather than in the warehouse, which makes inventory recovery a routine part of order fulfillment rather than an exception.

**Size:**
- 1 manager/owner-level reviewer (Sammy)
- 1 order intake clerk (Itzel)
- 1 operations lead handling fulfillment, communication, and returns (Anne)
- Warehouse and driver staff
- Multiple wig collections, each with its own product attribute structure

**Customer channels:** Shopify storefront (order placement), WhatsApp (all operational communication)

**Typical customer:** A retail wig store or salon that buys wholesale inventory, holds some of it on consignment, and resells to walk-in retail customers.

**How the business makes money:** Wholesale sales of wigs to retail accounts, including consignment placements that convert to sales.

**Primary business goal:** Replace the large manually maintained spreadsheet with a centralized Order Management System that tracks every order from placement through fulfillment, reduces manual work, improves visibility, and leaves QuickBooks Desktop Enterprise completely unchanged.

**Current operational problem:** Orders arrive through Shopify, pass through Jotform, land in an export spreadsheet, and are then hand-copied into a second operational spreadsheet. Order status is expressed through **row colors** and by **physically moving rows between sections**. Each wig collection lives on its own worksheet with a **different column layout**, because Jotform exports each collection differently. Customer conversations exist only as free text in a Notes column. There is no history of what happened to an order, no reliable status, and no way for management to see the operational picture without reading the spreadsheet row by row.

## People and responsibilities

**Sammy — Manager**
- Reviews customer accounts with billing issues or low sales activity.
- Approves or releases accounts held for review.
- Needs operational and account-level reporting.

**Itzel — Order intake**
- Monitors the Jotform export spreadsheet for new Shopify orders.
- Copies each new order into the operational spreadsheet under New Orders.
- In the future system, Itzel should not copy anything. Orders should arrive automatically. Itzel's role becomes exception handling: fixing failed imports, entering the occasional phone or manual order, and correcting mismatched customer or product records.

**Anne — Operations lead**
- Reviews every new order and determines availability.
- Retrieves inventory, prepares and ships orders.
- Contacts customers via WhatsApp when an item is unavailable and negotiates alternatives.
- Tracks incoming shipments and expected arrival dates.
- Identifies which retail customer is holding a needed wig on consignment, confirms it is unsold, and requests its return.
- Coordinates driver pickups.
- Records everything in the Notes column.
- In the future system, Anne should not manually determine every next step. The software should tell her what the order needs, surface candidate alternatives, surface who is holding the item, and draft her messages for approval.

**Warehouse / Drivers**
- Pick, pack, and ship outbound orders.
- Collect returned consignment inventory from retail customers.
- Grouped by geographic area for efficient pickup and delivery routing.

**Customer (retail store or salon)**
- Places wholesale orders through Shopify.
- Responds to WhatsApp messages about availability, substitutions, ETAs, and return requests.
- Holds inventory on consignment and returns it when requested and unsold.

## Product structure

Every wig is identified by a combination of attributes. These attributes drive both inventory lookup and substitution logic:

- **Collection** — Ready to Wear, Lace Top, Lace Fall, Hand Tied, Allure, and others
- **Style**
- **Color**
- **Length**
- **Cap size**

Collections are not interchangeable in the customer's mind, but within a collection, color/length/cap-size variants are often acceptable substitutes. This ordering matters for the substitution engine.

## Operating rules

- Orders originate in Shopify and must appear in the operational system without manual copying.
- Availability is checked against operational inventory first; QuickBooks is consulted only when additional detail is needed.
- When an item is unavailable, the customer is offered alternatives before the order is delayed or cancelled.
- Acceptable alternative categories: different color, different length, different cap size, different collection, similar product, discounted alternative.
- If an incoming supplier shipment containing the item is expected **within one week**, the order waits for that shipment rather than triggering a consignment recovery.
- Consignment inventory held by a retail customer for **more than 14 days** is eligible to be requested back.
- Before a return is requested, the customer must be contacted and must confirm the item is unsold. Confirmation precedes pickup scheduling.
- Accounts with **fewer than three sales in a month**, outstanding balances, or other account concerns are held for Sammy's review.
- Accounts owing money, with incomplete returns, or with unresolved billing issues are marked Not Cleared with Billing.
- Customers are grouped by geographic area so driver pickups and deliveries can be batched.
- **QuickBooks Desktop Enterprise remains the accounting and inventory system of record and is never modified by this system.**

## Current pain points

- New orders must be manually copied from the Jotform export into the operational spreadsheet.
- Employees manually move rows between sections to indicate status.
- Order progress is encoded in row colors rather than structured statuses.
- Every wig collection has a different column layout, so staff work with inconsistent formats all day.
- Inventory is tracked separately from orders, so availability and demand are never reconciled in one place.
- Customer communication exists only as free-text notes and is not searchable or attributable.
- Employees must remember previous conversations to know what an order needs next.
- Return requests and consignment inventory are difficult to track and easy to lose.
- Geographic planning for pickups and deliveries is done from memory and color coding.
- There is no centralized history of actions taken on an order.
- Management cannot see order status, aging, throughput, or account health without reading the spreadsheet manually.

---

# PART TWO — THE CURRENT MANUAL PROCESS

## Trigger

A wholesale customer places an order through the Shopify website.

## Manual step-by-step process

1. **Order lands in Jotform.** Shopify forwards the order to Jotform automatically.
2. **Jotform updates an export spreadsheet.** Each collection exports with a different column layout.
3. **Itzel opens the export.** She identifies new rows.
4. **Itzel copies the order.** She pastes it into the operational spreadsheet under New Orders.
5. **Anne reviews the order.** She reads the requested wig and its attributes.
6. **Anne checks availability.** She uses the inventory section of the operational spreadsheet, referencing QuickBooks only if more detail is needed.
7. **If available — Anne retrieves the wig.** She physically pulls it.
8. **Anne moves the row to Processing.** She changes the row color.
9. **Anne prepares and ships the order.**
10. **Anne moves the row to Fulfilled.** She changes the row color to green.
11. **If unavailable — Anne contacts the customer on WhatsApp.** She proposes alternatives: different color, length, cap size, collection, a similar product, or a discounted option.
12. **Anne records the conversation in Notes.** Free text, no structure.
13. **If the customer accepts an alternative** — Anne updates the notes, ships the replacement, and moves the order to Fulfilled.
14. **If the customer declines** — Anne checks whether the item is on an incoming supplier shipment.
15. **If a shipment arrives within one week** — the customer is given an ETA and the order waits until the inventory arrives, then ships.
16. **If no shipment is expected soon** — Anne begins consignment recovery.
17. **Anne determines which customer holds the wig.** She works from the spreadsheet and memory.
18. **Anne checks how long they have held it.** More than 14 days makes it eligible for recall.
19. **Anne contacts that customer on WhatsApp.** She confirms whether the wig has been sold.
20. **If unsold, Anne requests its return.** The order moves to Waiting on Return Requests until the customer confirms.
21. **Driver pickup is arranged.** The order moves into Waiting on Returns. Customers are color-coded by geography to plan routing.
22. **The wig arrives back at the warehouse.** Anne notifies the original customer.
23. **Anne ships the order** and moves it to Fulfilled.
24. **Separately, accounts are triaged.** Accounts with under three monthly sales or outstanding balances are moved to Hold with Sammy. Accounts owing money or with incomplete returns go to Not Cleared with Billing.
25. **Reporting is manual.** Anyone wanting operational numbers counts rows.

## What goes wrong

- An order can be lost between the Jotform export and the operational spreadsheet.
- Row color is the only status, and color is easy to misapply or overlook.
- Moving a row between sections can drop data or duplicate an order.
- Inconsistent column layouts across collections cause transcription errors.
- Availability decisions rely on an inventory section that nobody guarantees is current.
- Conversations live in one Notes cell, so nobody but Anne knows the state of a negotiation.
- Consignment aging is judged by memory, so recall requests are inconsistent and sometimes inappropriate.
- Return pickups are coordinated ad hoc; items in transit are effectively invisible.
- There is no audit trail, so a disputed order cannot be reconstructed.
- Management reporting is slow, incomplete, and stale by the time it exists.

---

# PART THREE — OPPORTUNITY MAP

Treat this map and the future-state workflow below as **hypotheses, not final requirements.** If you find a better decomposition during Phase 0, propose it and explain the tradeoff.

## What should become software

- **Central order record** — one canonical order object regardless of which collection it belongs to, normalizing away the inconsistent Jotform column layouts.
- **Structured status workflow** — explicit states replacing row colors and section moves.
- **Guarded state transitions** — a single service that validates every status change; no raw status writes anywhere in the codebase.
- **Order board** — kanban view by status with filters for collection, customer, region, and age.
- **Order detail view** — line items, inventory position, full timeline, notes, message thread, hold badges.
- **Unified inventory view** — on-hand, reserved, incoming, and out-on-consignment for every product, searchable by collection, style, color, length, and cap size.
- **Incoming shipment register** — expected products, quantities, arrival dates, supplier.
- **Consignment ledger** — which customer holds which item, since when, and its current disposition.
- **Return request workflow** — confirmation, pickup scheduling, driver assignment, in-transit tracking, receipt.
- **Geographic grouping** — customer region field driving pickup and delivery batching.
- **Account hold flags** — Hold with Sammy and Not Cleared with Billing as flags that can coexist with any order status.
- **Deterministic business rules engine** — 14-day consignment threshold, 7-day incoming-shipment window, 3-sales-per-month account flag; all configurable, none hardcoded.
- **Audit history** — every state change, edit, and action permanently recorded with actor and timestamp.
- **QuickBooks read adapter** — retrieves customer records, invoices, and inventory detail on demand; never writes.
- **Search and filters** across orders, customers, products, and consignments.
- **Role-based permissions** — Admin, Manager, Operations, Warehouse.

## What should become automation

- Create an order automatically when a Shopify order arrives; assign order number and timestamps.
- Run an inventory check on order creation and flag shortages immediately.
- Move an order to Waiting for Shipment automatically when a matching incoming shipment falls inside the configured window.
- Reserve inventory when an order enters Processing; release it if the order is cancelled or substituted.
- Resume a waiting order automatically when the awaited inventory is received.
- Nightly: flag consignments that have passed the aging threshold.
- Nightly: flag accounts meeting Sammy-review criteria.
- Nightly: flag aging orders that have sat in a status too long.
- Notify Anne when an order needs a decision; notify Sammy when an account is flagged; notify the warehouse when a pickup is scheduled or a return is received.
- Queue outbound WhatsApp messages only after human approval.

## What should use AI

- **Summarize** long or messy note threads on an order.
- **Extract** structured intent from free-text customer messages (accepted substitution, declined, asked for ETA, disputed).
- **Explain and phrase** substitution suggestions — the *ranking* is deterministic; AI writes the human-readable rationale and the customer-facing offer.
- **Draft WhatsApp messages** for alternative offers, ETA updates, return requests, and shipping notifications. Always drafts, always approved by a human before sending.
- **Suggest a reason category** when an order is cancelled or a substitution is declined.
- **Rank** among already-valid options — for example, which of several eligible consignment holders to approach first, given region, holding duration, and account standing.
- Later: an internal assistant answering questions about orders, inventory, and customer activity over approved data.

## What should NOT use AI

- Inventory quantity math and availability determination
- Which products are valid substitutes (attribute matching is deterministic; only the phrasing and ordering commentary are AI)
- Consignment eligibility (the 14-day rule)
- Incoming-shipment window logic (the 7-day rule)
- Account flag criteria (the 3-sales rule, outstanding balance)
- Order state transitions and their guards
- Reservation and double-allocation prevention
- Duplicate order detection
- Authentication and authorization
- Sending any customer message without human approval
- Anything written back to QuickBooks (nothing is written back to QuickBooks)

These are deterministic rules and must stay predictable, testable, and inspectable.

## What analytics should exist

**Operational**
- New orders today and this week
- Orders in each status
- Orders blocked on inventory
- Orders awaiting a customer decision
- Orders waiting on incoming shipments, with ETAs
- Open return requests by stage
- Order aging by status
- Fulfillment throughput and average cycle time
- Warehouse workload and pending pickups by region

**Inventory and consignment**
- Inventory levels by collection, style, color, length, cap size
- Units out on consignment, by customer and by age
- Consignment conversion rate (placed → sold)
- Consignment recall success rate and average recovery time
- Most frequently short-stocked products
- Incoming shipment pipeline

**Customer and account**
- Order volume by customer
- Substitution acceptance rate by customer and by substitution type
- Accounts on Hold with Sammy and why
- Accounts Not Cleared with Billing and total exposure
- Customers with fewer than three sales this month
- Response latency on WhatsApp threads

**Management**
- Order pipeline value by stage
- Demand by collection and attribute
- Geographic demand and pickup/delivery load by region
- Where orders stall most often

## Ownership legend

- **Human:** Makes judgments, negotiates with customers, approves messages and substitutions, resolves exceptions.
- **Software:** Captures, stores, validates, displays, and controls trusted state; applies deterministic inventory, eligibility, and transition rules; maintains the audit trail.
- **Automation:** Performs a known action when a defined event occurs.
- **AI:** Interprets messy language and produces summaries, drafts, and rankings among options that already satisfy hard rules.
- **Analytics:** Measures events and turns system history into operational insight.

---

# PART FOUR — FUTURE-STATE WORKFLOW

1. **Human — Customer:** Places a wholesale order on Shopify.
2. **Software:** Receives the webhook, creates the order, assigns an order number and timestamps, normalizes the collection-specific fields into the canonical product model.
3. **Software:** Matches the Shopify customer to an existing customer record, or flags it for Itzel if the match is ambiguous.
4. **Analytics:** Records source, collection, requested attributes, and creation time.
5. **Software:** Runs an availability check against operational inventory for every line and computes an availability verdict per line.
6. **Software:** Assigns an initial status — Ready to Fulfill, Awaiting Decision, or Needs Review.
7. **Automation:** Places the order on the New Orders board and notifies Anne if it requires a decision.
8. **AI:** Summarizes any customer notes attached to the order and extracts special requests.
9. **Software:** Displays the original Shopify submission and the AI summary **separately**, so the source data stays trustworthy.
10. **Human — Anne:** Opens the order board, filters, and selects an order.
11. **If inventory is available — Software:** Reserves the units and moves the order to Processing.
12. **Human — Warehouse:** Picks and packs. Records the shipment and tracking number.
13. **Software:** Moves the order to Shipped, then Fulfilled, releasing the reservation into a completed allocation.
14. **Automation:** Drafts a shipping notification for approval and sends it once approved.
15. **If inventory is unavailable — Software:** Computes the ranked list of valid substitutes using deterministic attribute matching, filtered to items actually in stock.
16. **AI:** Writes the rationale for each suggestion and drafts a customer-friendly offer message.
17. **Human — Anne:** Reviews the suggestions, edits the message, approves it.
18. **Automation:** Sends the WhatsApp message and sets a follow-up deadline.
19. **Human — Customer:** Accepts an alternative, declines, or asks a question.
20. **AI:** Classifies the reply; **Software:** records the outcome; **Human — Anne:** confirms before the order moves.
21. **If accepted — Software:** Substitutes the line, records the original request alongside the substitution, and moves the order to Processing.
22. **If declined — Software:** Checks the incoming shipment register for the original item.
23. **If a shipment arrives within the configured window — Software:** Moves the order to Waiting for Shipment and exposes the ETA. **Automation:** Drafts an ETA message for approval.
24. **When the shipment is received — Automation:** Resumes the order into Processing and notifies Anne.
25. **If no shipment is expected — Software:** Queries the consignment ledger for customers currently holding the item, filters to those past the eligibility threshold, and ranks them.
26. **AI:** Ranks among the *already eligible* holders by region, holding duration, and account standing, and drafts the recall request.
27. **Human — Anne:** Approves the recall request. **Automation:** Sends it and moves the order to Return Request Pending.
28. **Human — Customer:** Confirms the item is unsold, or reports it sold.
29. **If sold — Software:** Updates the consignment record and returns Anne to the eligible-holder list for the next candidate.
30. **If unsold — Software:** Creates a return request, moves the order to Waiting on Return, and exposes it on the pickup board grouped by region.
31. **Human — Warehouse/Driver:** Collects the item. **Software:** Marks it in transit, then received.
32. **Automation:** Returns the item to available inventory, resumes the order into Processing, and drafts a notification to the original customer.
33. **Software:** Ships, fulfills, and permanently attaches the entire history to the order.
34. **Automation:** Nightly, flags accounts meeting Sammy-review criteria and consignments past the aging threshold.
35. **Human — Sammy:** Reviews flagged accounts, clears or holds them, and records the reason.
36. **Analytics:** Updates pipeline, aging, inventory, consignment, substitution, and account dashboards.
37. **Human — Sammy:** Reviews analytics and adjusts purchasing, consignment policy, and account terms.

## Failure and exception paths

- A Shopify webhook that fails to process is retried, and remains visible in a failed-import queue rather than disappearing.
- An order whose customer or product cannot be matched creates a Needs Review record; it is never silently guessed.
- A duplicate Shopify order is flagged, not silently duplicated.
- Inventory is never allocated twice; reservations are atomic and a shortage surfaces immediately.
- AI output is always displayed as a suggestion and never overwrites customer-provided data.
- A substitution requires explicit human approval, always.
- No customer message is sent without human approval.
- If no eligible consignment holder exists, the system creates a visible exception rather than inventing an option.
- If an AI ranking conflicts with a hard eligibility rule, the rule wins and the option is not offered.
- A human can pause automation on any individual order.
- The original Shopify submission is preserved immutably alongside all later edits.
- If QuickBooks is unreachable, the system degrades gracefully to operational data and shows a clear staleness indicator; it never blocks fulfillment.

---

# PART FIVE — SUCCESS METRIC AND CONSTRAINTS

## Primary metric

When a wholesale order is placed on Shopify, it should appear in the Order Management System with a computed availability verdict and a recommended next action **within one minute**, with no employee copying any data by hand — and every subsequent action on that order should be recorded in a permanent, reviewable history.

## Supporting metrics

- Zero orders are hand-copied between systems.
- Every order has an explicit status and a clear next owner.
- No order sits in a status past its aging threshold without an alert.
- Every substitution records what was originally requested and what was shipped instead.
- Every consignment unit has a known holder, a known age, and a known disposition.
- Every customer message is attached to an order and attributable to a person.
- Sammy can see pipeline, aging, inventory, and account health without opening a spreadsheet.

## Hard constraints

- **QuickBooks Desktop Enterprise remains unchanged.** It stays the accounting and inventory system of record. This system reads from it on demand and never writes to it.
- Shopify remains the customer-facing sales channel.
- WhatsApp remains the customer communication channel.
- The original order submission must be preserved immutably.
- The system must prevent double-allocation of inventory.
- AI may suggest, draft, summarize, and rank, but may not overwrite source data, approve a substitution, send a message, or override a hard rule.
- Every threshold with a number in it must be configurable, not hardcoded.
- Staff must be able to pause automation on any order.
- Every state change must be auditable, with actor and timestamp.
- The system must handle collection-specific field variation without requiring separate code paths per collection.

## Preferences

- A single custom web application, self-hostable.
- Keep the first version simple. The order board and order history alone justify the project.
- Use existing Shopify, WhatsApp, and QuickBooks tools rather than replacing them.
- Build scheduling and routing in phases: first show which region a pickup belongs to; later add real route optimization.
- The users currently live in Excel and are power users. Prefer a dense, fast, keyboard-friendly interface over a wizard-heavy one.

---

# PART SIX — TECHNICAL DIRECTION

## Stack

Use this unless you have a specific, stated reason not to. Raise the objection in Phase 0, don't silently substitute.

- **Next.js (App Router) + TypeScript**
- **PostgreSQL + Prisma**
- **Tailwind + shadcn/ui**
- **NextAuth** with roles: `ADMIN`, `MANAGER`, `OPERATIONS`, `WAREHOUSE`
- **Zod** for validation, schemas shared between client and server
- **BullMQ or a Postgres-backed queue** for webhooks, nightly jobs, and outbound messages
- **Vitest** for unit tests, **Playwright** for one end-to-end smoke path
- **Docker Compose** for local and single-host deployment

## Core state machine

This is the heart of the system. Build and test it before any UI.

```
NEW
 ├─ inventory available ──────────────► PROCESSING ──► SHIPPED ──► FULFILLED
 └─ inventory short ──► AWAITING_CUSTOMER_DECISION
        ├─ accepts alternative ───────► PROCESSING ──► ...
        └─ declines
             ├─ shipment ≤ window ────► WAITING_FOR_SHIPMENT ──► PROCESSING ──► ...
             └─ no shipment
                  └─► RETURN_REQUEST_PENDING
                       ├─ holder reports sold ──► back to holder selection
                       └─ holder confirms unsold ──► WAITING_ON_RETURN
                                                      └─ received ──► PROCESSING ──► ...
Any state ──► CANCELLED (with recorded reason)
```

**Holds are flags, not states.** `HOLD_WITH_SAMMY` and `NOT_CLEARED_WITH_BILLING` can apply to an order or account in any status and must be modeled independently of the status field.

## Data model — starting point

Refine this, then show me the schema before writing application code.

```
Customer          id, name, contactName, whatsappNumber, email, region,
                  billingStatus, holdWithSammy, holdReason,
                  quickbooksCustomerId, shopifyCustomerId, notes

Collection        id, name, attributeSchema (JSON — handles per-collection variation)

Product           id, collectionId, style, color, length, capSize, sku,
                  quickbooksItemId, active

InventoryItem     productId, quantityOnHand, quantityReserved, location

Order             id, orderNumber, customerId, source (SHOPIFY|MANUAL|IMPORT),
                  status, placedAt, promisedDate, shippedAt, fulfilledAt,
                  automationPaused, rawSubmission (immutable JSON)

OrderLine         orderId, productId, quantityRequested, quantityFulfilled,
                  originalProductId, substitutionReason, availabilityVerdict

Reservation       orderLineId, productId, quantity, createdAt, releasedAt

Shipment          orderId, carrier, trackingNumber, shippedAt

IncomingShipment  productId, quantityExpected, expectedArrival, supplier, status

Consignment       customerId, productId, quantity, placedAt,
                  status (WITH_CUSTOMER|SOLD|RECALL_REQUESTED|CONFIRMED_UNSOLD
                          |IN_TRANSIT|RETURNED), lastContactedAt

ReturnRequest     consignmentId, fulfillingOrderId, status,
                  confirmedUnsoldAt, pickupScheduledAt, driverId, receivedAt

Driver            id, name, phone, regionsCovered

Note              entityType, entityId, authorId, body, createdAt

Message           orderId, customerId, channel, direction, body,
                  status (DRAFT|APPROVED|SENT|FAILED), draftedByAI,
                  approvedById, sentAt

BusinessRule      key, value, description, updatedBy, updatedAt

AuditEvent        entityType, entityId, actorId, action, before, after, createdAt
```

**Every state change writes an `AuditEvent`.** The absence of history is the single worst property of the current spreadsheet. Order detail must render a complete permanent timeline.

**On collection variation:** do not create a table or code path per collection. Normalize to `Product` with the five core attributes and put genuinely collection-specific extras in a validated JSON field described by `Collection.attributeSchema`.

## Screens, in priority order

1. **Order board** — kanban by status; filters for collection, customer, region, age, hold flags; bulk actions; drag between columns routed through the guarded transition service, never a raw status write.
2. **Order detail** — lines, availability verdict per line, substitution history, full audit timeline, notes, message thread, hold badges, pause-automation toggle.
3. **Inventory** — searchable by all five attributes; shows on-hand, reserved, incoming, and out-on-consignment side by side.
4. **Substitution panel** — inside an order line, ranked in-stock alternatives with a visible reason for each and a one-click draft-offer action.
5. **Consignment ledger** — who holds what, since when, aging highlights, recall eligibility.
6. **Returns board** — pending confirmations, scheduled pickups, in transit, received; grouped by region for driver batching.
7. **Account review queue** — Sammy's flagged accounts with the triggering reason and a clear/hold action.
8. **Dashboards** — the analytics listed in Part Three.

## Substitution engine — build it deterministically

A wrong substitution costs real money and real customer trust. The ranking must be inspectable and unit-tested.

```
1. Filter to products with quantityOnHand - quantityReserved > 0
2. Score by attribute distance, weighted in this priority order:
     same collection        (heaviest weight)
     color proximity
     length proximity
     cap size match
3. Apply configurable business rules (excluded products, discount-eligible tiers)
4. Return the ranked list WITH the per-attribute scores exposed
5. Only then: AI writes the rationale text and the customer-facing offer
```

The model never selects the candidates. It explains and phrases the ones the code selected.

## AI service boundaries

All model calls live behind a single `lib/ai/` service with typed inputs and outputs, a mockable interface, and unit tests that never hit the network. Every AI output is persisted with a `draftedByAI` marker so it is always distinguishable from human-authored content.

## QuickBooks adapter

QuickBooks Desktop Enterprise has no modern REST API. Real integration requires QuickBooks Web Connector with QBXML, or a hosted connector service. Do not attempt this early.

Build a `QuickBooksClient` interface with these read-only methods and a fully functional mock:

```
getCustomer(quickbooksCustomerId)
getInventoryQuantity(quickbooksItemId)
getCustomerBalance(quickbooksCustomerId)
getRecentInvoices(quickbooksCustomerId, since)
```

Everything else in the system must work with the mock in place. Real integration is a later phase and requires a conversation with me about the connection approach first.

---

# PART SEVEN — HOW TO WORK

## Phases

**Phase 0 — Plan.** Write `docs/PLAN.md` containing: your questions for me, the refined data model, the complete state machine with every allowed transition and its guard conditions, your critique of the opportunity map above, and your proposed phase breakdown. **Write no application code. Stop and wait for my approval.**

**Phase 1 — Foundation.** Repo scaffold, Docker Compose (app + Postgres + Redis), Prisma schema, migrations, seed script with realistic data: 5+ collections, ~200 products, ~30 customers across several regions, ~80 orders distributed across every status, consignments of varying ages, incoming shipments. Auth with all four roles. The state machine implemented and fully unit tested.

**Phase 2 — Order management.** Order board, order detail, audit timeline, notes, manual order entry, guarded transitions, hold flags. **This phase alone should let them stop using the spreadsheet for order tracking.**

**Phase 3 — Inventory, consignment, returns.** Inventory views, reservations, incoming shipments, consignment ledger with aging, return request workflow, returns board with regional grouping.

**Phase 4 — Integrations.** Shopify webhooks with a failed-import queue, CSV/Jotform import for backfill and fallback, `QuickBooksClient` interface plus mock. Real QuickBooks integration only after we discuss the connection approach.

**Phase 5 — Substitutions, AI, messaging.** Deterministic substitution engine with tests, AI rationale and drafting layer, WhatsApp draft/approve/send flow, note summarization, reply classification.

**Phase 6 — Analytics and dashboards.**

## Rules of engagement

- Ask me when a business rule is ambiguous. Do not invent policy silently. When you must assume something to keep moving, record it in `docs/ASSUMPTIONS.md` and flag it in your summary for that phase.
- Every business rule with a number in it goes in the `BusinessRule` table or a settings file, never inline.
- No raw status mutations anywhere. All transitions go through one guarded service function.
- Write real tests for: the state machine, the substitution ranking, reservation and double-allocation prevention, consignment aging, and the nightly flagging jobs. UI tests stay light.
- Commit after each phase with a clear message. Stop at each phase gate for review.
- Prefer boring, obvious code. Someone else will maintain this.

## Open questions I expect you to raise in Phase 0

These are known gaps. Surface them; do not guess.

- Daily and peak order volume, and number of concurrent users.
- Whether partial fulfillment of a multi-line order is permitted, and whether partial shipments go out or the order waits.
- Whether operational inventory or QuickBooks is the source of truth for on-hand counts, and how they reconcile.
- How consignment placements are recorded today, and whether that data exists anywhere structured.
- How WhatsApp sending actually happens: Business API, or a human copying an approved draft. **The second is a perfectly acceptable v1** and should be the assumed default until told otherwise.
- Exact criteria for Not Cleared with Billing, and who can clear it.
- Whether substitutions need to flow back to Shopify or QuickBooks in any form, or are purely operational.
- Whether returned consignment inventory re-enters stock at full value or needs an inspection step.
- Migration strategy: how to get existing spreadsheet data in, given every collection has a different column layout, and how much history is worth migrating.
- What happens to an order that can never be fulfilled — is there a cancellation and refund path, and who owns it?

