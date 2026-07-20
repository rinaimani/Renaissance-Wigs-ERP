# Renaissance Wigs — Order Management System
## Build Prompt for Claude Code

> Save this as `PROJECT.md` at the root of an empty repository, then tell Claude Code:
> *"Read PROJECT.md. Do not write application code yet. Complete Phase 0 and stop."*

---

# PART ONE — BUSINESS CONTEXT

## Business profile

**Business name:** Renaissance Wigs

**What it does:** Renaissance Wigs is a wholesale wig distributor. It sells wigs in bulk to retail wig stores and salons, which resell them to individual consumers. A large share of inventory sits **at customer sites on consignment** rather than in the warehouse, so recovering inventory from customers is a routine part of fulfillment, not an exception.

**Delivery model:** Orders are delivered by the company's own drivers using the **Shipday** driver app. Drivers capture a photo or a signature on delivery. This is why partial fulfillment is normal — whatever is ready goes on the truck.

**Physical inventory control:** Every wig carries **two barcodes** — one identifying the individual physical unit, and one identifying its category (collection, style, color, length, cap size). Both are scanned into QuickBooks and are linked there under the same wig. Wigs are scanned when they ship out and scanned again when they come back. Incoming wigs from suppliers arrive with **tags showing size, color, and length**, which is how receiving staff decide whether a wig goes onward to a waiting customer or into general stock.

**Size and volume:**
- 1 manager (Sammy)
- 1 order intake clerk (Itzel)
- 1 operations lead handling fulfillment, communication, and returns (Anne)
- Warehouse staff and drivers
- A billing function working from QuickBooks
- **10 to 40 orders per day, seasonally variable**

This is a low-volume, high-touch operation. **Do not over-engineer for scale.** Optimize for clarity, correctness, auditability, and speed of use by three power users. A board that shows every open order at once is not just acceptable, it is desirable.

**Customer channels:** Jotform (order placement), WhatsApp (all operational communication)

**Typical customer:** A retail wig store or salon that buys wholesale, holds inventory on consignment, and resells to walk-in retail customers.

**Primary business goal:** Replace the large manually maintained spreadsheet with a centralized Order Management System that tracks every order line from placement through delivery, reduces manual work, improves visibility, and leaves QuickBooks Desktop Enterprise completely unchanged.

**Current operational problem:** Orders are submitted through Jotform. Jotform automatically updates an export spreadsheet. Itzel hand-copies each new order from that export into a second, operational spreadsheet. Order status is expressed through **row colors** and by **physically moving rows between sections**. Each wig collection lives on its own worksheet with a **different column layout**. Customer conversations exist only as free text in a Notes column. There is no history of what happened to an order, no reliable status, and no way to see the operational picture without reading the spreadsheet row by row.

## People and responsibilities

**Sammy — Manager**
- Reviews customer accounts with low sales activity or account concerns.
- Approves or releases accounts held for review.
- Needs operational and account-level reporting.

**Itzel — Order intake**
- Today: monitors the Jotform export spreadsheet and copies each new order into the operational spreadsheet.
- In the future system: **copies nothing.** Orders arrive automatically from Jotform. Her role becomes exception handling — failed imports, the occasional phone order, and resolving customer or product records the system could not match.

**Anne — Operations lead**
- Reviews every new order and determines availability by looking up the product in QuickBooks.
- Determines **which site holds the product** — the warehouse, or a specific salon.
- Prepares outbound orders; wigs are scanned out by barcode.
- Contacts customers on WhatsApp when an item is unavailable and negotiates substitutions.
- Tracks incoming supplier shipments and expected arrival dates.
- Requests wigs back from salons over WhatsApp and records which are coming.
- Records everything in the Notes column.
- In the future system: she should not have to determine every next step manually. The software tells her what each line needs, surfaces candidate substitutions, surfaces which sites hold the item, and drafts her messages for approval.

**Warehouse / Receiving**
- Scans wigs out on dispatch and in on return.
- Reads the tag on incoming supplier wigs (size, color, length) to determine whether it goes onward to a waiting customer or into stock.
- Performs the **Transfer Inventory** entry in QuickBooks when a wig returns from a salon.

**Drivers**
- Deliver orders through the **Shipday** app, capturing a photo or signature.
- Collect returning consignment inventory on the same routes.
- Delivery confirmations currently come back from Shipday and are **updated manually**.

**Billing**
- Receives an emailed list of customers with high QuickBooks balances and contacts them about payment.

**Customer (retail store or salon)**
- Places wholesale orders through Jotform, described by attributes — e.g. *"Lace Fall, 14 inch, color #2, size M."*
- Responds on WhatsApp about availability, substitutions, ETAs, and return requests.
- Holds inventory on consignment and returns it when requested and unsold.

## Product structure

A wig is specified by attributes:

- **Collection** — Ready to Wear, Lace Top, Lace Fall, Hand Tied, Allure, and others
- **Style**
- **Length** — e.g. 14 inch
- **Color** — e.g. #2
- **Cap size** — e.g. M

**Orders are placed by attribute, not by SKU.** A customer writes "Lace Fall 14 inch color #2 size M," and staff resolve that to a QuickBooks item.

### Dual barcodes and the fungibility principle — CRITICAL

Every physical wig carries **two barcodes**:

- **Unit barcode** — unique to that one physical wig.
- **Category barcode** — shared by every wig of the same collection, style, color, length, and cap size.

Both are scanned into QuickBooks and linked there under the same wig.

**Wigs in the same category are identical and completely interchangeable.** It does not matter which individual unit ships to satisfy a line, as long as it belongs to the correct category. This is a hard business rule with direct consequences for the design:

- **Allocation, commitment, and availability are all computed at the category level.** Never reserve, commit, or promise a specific unit.
- **Never build a workflow that instructs someone to pick a particular unit.** A picker takes any wig from the correct category bin.
- **Never surface a shortage because a specific unit is unavailable.** Only the category count matters.
- Unit barcodes exist for **traceability and reconciliation**, not for allocation. They answer "where did this specific wig go and when," which is valuable for auditing and for reconciling the mirror against physical reality — but they never drive fulfillment decisions.
- **Scan screens must accept either barcode interchangeably.** Scanning a unit barcode resolves to its category through the link; scanning a category barcode resolves directly. The operator should never have to know or care which one they scanned.

Ordering is an attribute event, fulfillment is a barcode event, and both resolve to the same category.

Within a collection, color/length/cap-size variants are often acceptable substitutes. Across collections, less so. This priority ordering drives the substitution engine.

## Operating rules — CONFIRMED

These have been confirmed with the business. Treat them as requirements, not assumptions.

**Order intake**
- Orders are placed through **Jotform**, which is the record of what the customer asked for.
- Each collection has its own Jotform form with its own field set.
- Jotform updates an Excel export automatically. That export is a **fallback and migration source, not the intended intake path.**
- Orders describe products by attribute, not by item number.

**Inventory — multi-site**
- **QuickBooks Desktop Enterprise is the source of truth for all inventory and customer information.**
- QuickBooks Enterprise is used with **multiple inventory sites**. The warehouse is one site; **each salon holding consignment inventory is its own site.**
- "Who has this wig" is answered by looking at **inventory quantity by site** in QuickBooks. This is how Anne finds a wig to recall today.
- Consignment movements are recorded in QuickBooks as **Transfer Inventory** transactions — for example, Salon A → Renaissance Wigs when a wig comes back.
- **This system never writes to QuickBooks.** Every QuickBooks transaction continues to be entered manually by the people who do it today. The system's job is to make sure they know exactly what to enter.

**Physical handling**
- Every wig has **two barcodes** — one per individual unit, one per category — both scanned into QuickBooks and linked under the same wig.
- **Wigs within a category are identical and interchangeable.** Any unit satisfies a line for that category. Allocation is never unit-specific.
- Wigs are scanned on dispatch and scanned again on return.
- Incoming supplier wigs arrive tagged with size, color, and length; receiving uses the tag to decide onward-to-customer versus into-stock.

**Fulfillment**
- **Partial fulfillment is normal and expected.** Whatever is available goes on the truck. Unfulfillable lines are set aside and worked separately — substitution, waiting for a shipment, or consignment recall.
- **Fulfillment status therefore belongs to the order line, not the order.** Order status is derived from its lines.
- Delivery happens through **Shipday**, with photo or signature proof, currently reconciled manually.

**Substitutions**
- Acceptable alternatives: different color, length, cap size, collection, similar product, discounted alternative.
- Substitutions are **purely operational** — never written back to Jotform or QuickBooks.
- A substitution always requires explicit human approval.

**Incoming shipments**
- If a supplier shipment containing the item is expected **within one week**, the line waits rather than triggering a recall.

**Consignment and returns**
- Inventory held at a salon site for **more than 14 days** is eligible to be requested back.
- Recall begins with a WhatsApp conversation. The customer must confirm before anything is scheduled.
- Confirmed returns go on a pickup list recording **where each item needs to go** on arrival.
- A returned wig has two destinations: **onward to a specific waiting customer**, or **back into general stock**.
- On arrival, the wig is scanned and a **Transfer Inventory** transaction is entered in QuickBooks manually.

**Billing**
- Customers with a high QuickBooks balance go on a list that is emailed to billing for collection.

**Account review**
- Accounts with fewer than three sales in a month, or other concerns, are flagged for Sammy.

**Order completion**
- **Orders always get fulfilled.** There is no routine cancellation or refund path. A line may wait a long time, but it is worked until satisfied.

## Current pain points

- New orders must be manually copied from the Jotform export into the operational spreadsheet.
- Employees manually move rows between sections to indicate status.
- Order progress is encoded in row colors rather than structured statuses.
- Every collection has a different column layout, so staff work with inconsistent formats all day.
- Inventory truth lives in QuickBooks, outside the daily workflow, so availability is judged from a spreadsheet copy of unknown freshness.
- Finding which salon holds a wig requires manually querying QuickBooks site by site.
- Partial fulfillment is invisible — a spreadsheet row cannot express "three lines delivered, one substituted, one awaiting a recall."
- Customer communication exists only as free-text notes, unsearchable and unattributable.
- Consignment aging is judged by memory rather than by transfer dates.
- Return pickup lists are rebuilt by hand and exist only on paper.
- Shipday delivery confirmations are reconciled manually.
- There is no centralized history of actions taken on an order.
- Management cannot see status, aging, throughput, or account health without reading the spreadsheet.

---

# PART TWO — THE CURRENT MANUAL PROCESS

1. **Jotform receives the order,** describing the wig by collection, length, color, and cap size.
2. **Jotform updates an export spreadsheet automatically.** Column layout differs per collection.
3. **Itzel opens the export** and identifies new rows.
4. **Itzel copies the order** into the operational spreadsheet under New Orders.
5. **Anne reviews the order** and reads each requested wig.
6. **Anne looks the product up in QuickBooks** to find the matching item and its quantity.
7. **Anne checks which site holds it** — warehouse, or a specific salon.
8. **If it is in the warehouse,** the wig is pulled and scanned out by barcode.
9. **Anne moves the row to Processing** and changes the row color.
10. **The driver delivers via Shipday,** capturing a photo or signature.
11. **Someone updates the spreadsheet manually** from the Shipday confirmation.
12. **Anne sets aside unfulfilled lines** and works them separately.
13. **For an unavailable line, Anne contacts the customer on WhatsApp,** proposing an alternative color, length, cap size, collection, similar product, or discounted option.
14. **Anne records the conversation in Notes.** Free text, no structure.
15. **If accepted,** Anne updates notes and sends the replacement out.
16. **If declined,** Anne checks incoming supplier shipments.
17. **If a shipment arrives within a week,** the customer is given an ETA and the line waits.
18. **If not, Anne looks in QuickBooks for which salon holds the wig.**
19. **Anne judges how long they have held it.** More than 14 days makes it eligible.
20. **Anne messages that salon on WhatsApp** asking whether it has sold.
21. **If unsold, the salon confirms the return.**
22. **Anne adds it to the return list,** noting where the item goes on arrival.
23. **A driver collects it** on a delivery route.
24. **The wig arrives and is scanned in.** The list is checked and it is routed onward or into stock.
25. **Someone enters a Transfer Inventory transaction in QuickBooks** — Salon A → Renaissance Wigs.
26. **The waiting line is fulfilled,** the row moved to Fulfilled and colored green.
27. **Separately,** high-balance customers are listed from QuickBooks and emailed to billing.
28. **Separately,** Sammy reviews accounts with low sales activity.
29. **Reporting is manual.** Anyone wanting numbers counts rows.

## What goes wrong

- An order can be lost between the Jotform export and the operational spreadsheet.
- Row color is the only status, and color is easy to misapply or overlook.
- Moving rows between sections can drop or duplicate an order.
- Inconsistent column layouts cause transcription errors.
- Resolving "Lace Fall 14 inch color #2 size M" to a QuickBooks item is done by hand, every time, by a person who knows the catalog.
- Finding which salon holds a wig means checking sites one by one.
- A partially fulfilled order has no honest representation in a single row.
- Consignment aging is guessed rather than read from transfer dates.
- The return list is rebuilt by hand and items in transit are invisible.
- Routing instructions for returned wigs exist only on that list.
- Shipday confirmations arrive but reach the spreadsheet only when someone remembers.
- There is no audit trail, so a disputed order cannot be reconstructed.
- Management reporting is slow, incomplete, and stale by the time it exists.

---

# PART THREE — OPPORTUNITY MAP

Treat this map and the future-state workflow as **hypotheses, not final requirements.** If you find a better decomposition in Phase 0, propose it and explain the tradeoff.

## What should become software

- **Central order record** — one canonical order object regardless of collection, normalizing per-collection Jotform field variation.
- **Line-level status workflow** — explicit states per line; order status derived.
- **Guarded state transitions** — one service validating every change; no raw status writes.
- **Order board** — kanban by derived status with drill-down to lines; filters for collection, customer, region, age. At 10–40 orders/day, the whole active pipeline should fit on one screen.
- **Order detail** — every line with its own status and availability verdict, substitution history, timeline, notes, message thread, account flags.
- **Product resolver** — normalizes an attribute description ("Lace Fall / 14 / #2 / M") into the canonical vocabulary and looks up the single matching QuickBooks item, with a review queue for phrasings it does not yet recognize.
- **Multi-site inventory mirror** — read-only cache of QuickBooks inventory **by site**, with an explicit last-synced timestamp shown wherever a number appears.
- **"Who has this wig" view** — quantity by site for any product, with holding duration, replacing manual site-by-site lookup. This is the single highest-value screen in the system.
- **Availability engine** — per-line verdict against warehouse stock, accounting for units already committed to other open lines.
- **Consignment view derived from site inventory** — units at salon sites, aged from their transfer date, with recall eligibility computed.
- **Barcode capture** — scan-out on dispatch, scan-in on return, accepting either barcode interchangeably and resolving both to the category. Each scan produces a recorded event.
- **Incoming shipment register** — expected products, quantities, arrival dates, supplier.
- **Receiving screen** — for tagged supplier arrivals, showing whether each wig is spoken for by a waiting line or goes to stock.
- **Return workflow** — recall request, confirmation, pickup list, driver assignment, in-transit, scan-in, routing on arrival.
- **QuickBooks transaction worklist** — a queue of Transfer Inventory entries owed to QuickBooks, so nothing is missed.
- **Account flags** — Hold with Sammy and billing-list membership, independent of order status.
- **Deterministic rules engine** — 14-day threshold, 7-day shipment window, 3-sales flag, balance threshold; all configurable.
- **Audit history** — every state change, edit, scan, and action, with actor and timestamp.
- **Role-based permissions** — Admin, Manager, Operations, Warehouse.

## What should become automation

- Create an order automatically when a Jotform submission arrives.
- Resolve products and compute an availability verdict on every line at creation.
- Sync the multi-site QuickBooks mirror on a schedule; surface failures loudly.
- Move a line to Waiting for Shipment automatically when a matching incoming shipment falls inside the window.
- Resume a waiting line automatically when the awaited inventory is received.
- Ingest Shipday delivery confirmations and advance delivered lines automatically.
- Generate the driver's pickup list from confirmed returns, grouped by region.
- On scan-in of a returned wig, route it per its recorded destination and add the Transfer Inventory entry to the QuickBooks worklist.
- Nightly: flag consignments past the aging threshold, accounts meeting Sammy's criteria, and lines aging in status.
- Generate and email the high-balance billing list on a schedule.
- Notify Anne when a line needs a decision; notify the warehouse when a pickup is scheduled or a return arrives.

## What should use AI

- **Summarize** long or messy note threads.
- **Extract** structured intent from pasted customer messages — accepted, declined, asked for ETA, confirmed return, reported sold.
- **Suggest a normalization** when a phrasing is unrecognized — proposing which known attribute value the customer likely meant, always for human confirmation, never auto-applied. AI never picks a product; it only proposes how to read an attribute.
- **Explain and phrase** substitution suggestions. Ranking is deterministic; AI writes the rationale and the customer-facing offer.
- **Draft WhatsApp messages** for substitution offers, ETA updates, recall requests, and delivery notifications — always drafts for a human to send.
- **Rank** among already-eligible options, such as which of several eligible salons to approach first, given region, holding duration, and account standing.
- **Suggest a reason category** when a substitution is declined.
- Later: an internal assistant answering questions over approved data.

## What should NOT use AI

- Inventory quantity math and availability determination
- Site-level quantity lookups
- Which products are valid substitutes — attribute matching is deterministic; only phrasing is AI
- Consignment eligibility (the 14-day rule)
- Incoming-shipment window logic (the 7-day rule)
- Account flag criteria
- Line state transitions and their guards
- Barcode identity and unit-to-category resolution — a scan is a fact, never an inference
- Product lookup itself — one item per attribute combination means this is a deterministic table lookup, never a model call
- Which specific unit ships — units are interchangeable, so there is no decision to make
- Commitment tracking and double-allocation prevention
- Return routing destinations
- Duplicate order detection
- Authentication and authorization
- Sending any customer message without human approval
- Anything touching QuickBooks — the system only reads

## What analytics should exist

**Operational**
- New orders today and this week; lines in each status
- Lines blocked on inventory, awaiting a customer decision, or waiting on shipments
- Open recalls by stage; line aging by status
- Partial-fulfillment rate, and which products cause it
- Throughput and cycle time from order to fully delivered
- Pending pickups and deliveries by region

**Inventory and consignment**
- Inventory by product **and by site**
- Units out on consignment by customer and by age
- Consignment conversion rate (placed → sold)
- Recall success rate and average recovery time
- Most frequently short-stocked products
- Incoming shipment pipeline
- QuickBooks transaction backlog and mirror staleness
- Product resolution failure rate — how often an order needs manual matching

**Customer and account**
- Order volume by customer
- Substitution acceptance rate by customer and type
- Accounts on Hold with Sammy and why
- Accounts on the billing list and total exposure
- Customers with fewer than three sales this month

**Management**
- Pipeline by stage; demand by collection and attribute
- Geographic demand and route load by region
- Where lines stall most often
- Seasonal volume trend

## Ownership legend

- **Human:** Judgment, negotiation, approvals, exception resolution, and all QuickBooks data entry.
- **Software:** Captures, stores, validates, displays, and controls trusted operational state; applies deterministic availability, eligibility, and transition rules; maintains the audit trail.
- **Automation:** Performs a known action when a defined event occurs.
- **AI:** Interprets messy language and produces summaries, drafts, and rankings among options that already satisfy hard rules.
- **Analytics:** Measures events and turns system history into operational insight.

---

# PART FOUR — FUTURE-STATE WORKFLOW

1. **Customer:** Submits an order through the collection's Jotform form, describing wigs by attribute.
2. **Automation:** Jotform webhook fires immediately on submission.
3. **Software:** Creates the order, assigns a number and timestamps, normalizes collection-specific fields, and stores the raw submission immutably.
4. **Software:** Normalizes each line's attributes and looks up the single matching QuickBooks item. Unrecognized phrasings go to a resolution queue for Itzel.
5. **Software:** Matches the submitter to a customer record, or flags it if ambiguous.
6. **Software:** Computes an availability verdict per **line** against warehouse stock in the mirror, accounting for units already committed. Displays the mirror's last-sync time alongside.
7. **Software:** Assigns each line an initial status — Ready to Fulfill, Needs Substitution, or Needs Review.
8. **Automation:** Places the order on the board; notifies Anne if any line needs a decision.
9. **AI:** Summarizes customer notes and extracts special requests.
10. **Software:** Displays the original submission and the AI summary **separately**.
11. **Human — Anne:** Opens the board and selects an order.
12. **Software:** Commits inventory for Ready to Fulfill lines and moves them to Picking.
13. **Human — Warehouse:** Picks **any wig from the correct category** and **scans it out** — either barcode is accepted. Software resolves it to the category, records the scan against the line, and records the unit for traceability.
14. **Automation:** The delivery is dispatched through Shipday.
15. **Automation:** Shipday returns proof of delivery; software advances those lines to Delivered. **The order stays open** if other lines are unresolved; derived status becomes Partially Fulfilled.
16. **For each unfulfilled line — Software:** Computes ranked substitutes deterministically, filtered to genuinely available units.
17. **AI:** Writes the rationale and drafts a customer-friendly offer.
18. **Human — Anne:** Reviews, edits, sends on WhatsApp, marks it sent.
19. **Human — Customer:** Replies. **Anne:** Pastes the reply in. **AI:** Classifies it. **Anne:** Confirms before the line moves.
20. **If accepted — Software:** Substitutes the line, preserving the original request. No writeback anywhere.
21. **If declined — Software:** Checks the incoming shipment register.
22. **If a shipment arrives within the window — Software:** Moves the line to Waiting for Shipment and exposes the ETA. **AI:** Drafts an ETA message.
23. **When the shipment arrives — Human — Receiving:** Reads the tag, and the software shows whether that wig is spoken for by a waiting line or goes to stock. Scans it in.
24. **Automation:** Resumes the waiting line into Ready to Fulfill.
25. **If no shipment is expected — Software:** Shows **which sites hold the product**, each with its holding duration and eligibility, ranked.
26. **AI:** Ranks among already-eligible salons and drafts the recall request.
27. **Human — Anne:** Sends the recall on WhatsApp and records the outcome.
28. **If reported sold — Software:** Records it and offers the next eligible site.
29. **If the return is confirmed — Software:** Creates a return record with its **destination**: onward to the waiting customer, or back to stock.
30. **Software:** Adds it to the pickup list, grouped by region.
31. **Human — Driver:** Collects it on a route. **Software:** Marks it in transit.
32. **Human — Warehouse:** **Scans the wig in.** Software routes it per the recorded destination and creates a **QuickBooks Transfer Inventory task** — Salon A → Renaissance Wigs.
33. **Human — Warehouse/Billing:** Enters the transfer in QuickBooks and marks the task done.
34. **Software:** Moves the waiting line to Ready to Fulfill for the next route. When all lines are Delivered, the order becomes Fulfilled.
35. **Automation:** Nightly flagging; generates and emails the high-balance billing list.
36. **Human — Sammy:** Reviews flagged accounts, clears or holds, records the reason.
37. **Analytics:** Updates all dashboards.
38. **Human — Sammy:** Reviews analytics and adjusts purchasing, consignment policy, and account terms.

## Failure and exception paths

- A failed Jotform webhook is retried and stays in a failed-import queue. A scheduled reconciliation job pulls the Jotform API for missed submissions.
- **An attribute description that cannot be resolved to a QuickBooks item creates a resolution task. It is never guessed.**
- An unmatched customer creates a Needs Review record.
- A duplicate submission is flagged, not duplicated.
- Inventory is never committed twice; commitment is atomic.
- **If the mirror is stale beyond a configured threshold, every quantity is visibly marked stale.** The system does not block work — it tells the truth about what it knows.
- A failed QuickBooks sync is an operational alert, not a silent log line.
- **A scanned barcode that matches neither a known unit nor a known category creates an exception.** Scans are facts and are never silently discarded.
- **A scan of the wrong category against a line is rejected at the scan screen**, immediately and visibly. A scan of a different unit within the correct category is always accepted without comment.
- A unit barcode with no category link is an exception for Anne, not a blocker on the line — the category can be confirmed manually.
- A Shipday confirmation that cannot be matched to an order goes to a reconciliation queue; manual marking remains available as a fallback.
- AI output is always a suggestion and never overwrites source data.
- A substitution always requires explicit human approval.
- No customer message is sent automatically.
- If no eligible site holds the product, the system creates a visible exception rather than inventing an option.
- If an AI ranking conflicts with a hard rule, the rule wins.
- A human can pause automation on any order or line.
- The original Jotform submission is preserved immutably.
- A returned wig received without a recorded destination creates a routing exception.
- **A QuickBooks transfer task left pending beyond a threshold is escalated** — this is where the mirror silently drifts from reality.
- **There is no routine cancellation path.** Provide an admin-only, reason-required close-out and report on how often it fires.

---

# PART FIVE — SUCCESS METRIC AND CONSTRAINTS

## Primary metric

When a customer submits an order through Jotform, it should appear in the system **within one minute**, with every line resolved to a product, an availability verdict computed, and — where stock is short — **the sites holding that product already identified**. No employee copies any data by hand, and every subsequent action is recorded in a permanent, reviewable history.

## Supporting metrics

- Zero orders are hand-copied between systems.
- Nobody looks up "who has this wig" site by site in QuickBooks again.
- Every order line has an explicit status and a clear next owner.
- No line sits past its aging threshold without an alert.
- Partial fulfillment is fully represented at all times.
- Every substitution records what was requested and what was delivered instead.
- Every wig movement has a scan behind it, and every scanned unit is traceable to a site and a date.
- No one is ever asked to find a specific unit; picking is always category-level.
- Every returned wig has a recorded destination before it arrives.
- No wig returns without a QuickBooks transfer task being created.
- Delivery confirmations reach the system without manual re-entry.
- Sammy can see pipeline, aging, inventory, and account health without opening a spreadsheet.

## Hard constraints

- **QuickBooks Desktop Enterprise is the source of truth for inventory and customer data, and this system never writes to it.** All QuickBooks entry remains manual and human.
- QuickBooks multi-site inventory is the mechanism for consignment. The system mirrors it; it does not replace or duplicate it.
- Jotform remains the customer-facing order channel.
- WhatsApp remains the customer communication channel, sent by humans in v1.
- Shipday remains the delivery mechanism.
- The original Jotform submission must be preserved immutably.
- Partial fulfillment must be a first-class concept.
- The system must prevent double-commitment of inventory.
- AI may suggest, draft, summarize, and rank, but may not overwrite source data, approve a substitution, send a message, resolve a product unilaterally, or override a hard rule.
- Every threshold with a number in it must be configurable.
- Staff must be able to pause automation on any order or line.
- Every state change must be auditable, with actor and timestamp.
- Per-collection field variation must not require a separate code path per collection.

## Preferences

- A single custom web application, self-hostable.
- **Keep v1 simple.** At 10–40 orders/day, the order board with line-level status, the "who has this wig" view, and full history alone justify the project.
- Use existing Jotform, WhatsApp, QuickBooks, and Shipday rather than replacing them.
- Build routing in phases: group pickups and deliveries by region first; consider real optimization later.
- The users live in Excel today and are power users. Prefer a dense, fast, keyboard-friendly interface over a wizard-heavy one.
- Barcode scanning should work with a standard USB/Bluetooth scanner acting as a keyboard. Do not build a custom mobile scanning app in v1.

---

# PART SIX — TECHNICAL DIRECTION

## Stack

Use this unless you have a specific, stated reason not to. Raise objections in Phase 0.

- **Next.js (App Router) + TypeScript**
- **PostgreSQL + Prisma**
- **Tailwind + shadcn/ui**
- **NextAuth** with roles: `ADMIN`, `MANAGER`, `OPERATIONS`, `WAREHOUSE`
- **Zod** for validation, shared client and server
- **BullMQ or a Postgres-backed queue** for webhooks, syncs, and nightly jobs
- **Vitest** for unit tests, **Playwright** for one end-to-end smoke path
- **Docker Compose** for local and single-host deployment

Given the volume, a single modest server is sufficient. Do not introduce microservices, sharding, caching layers, or event sourcing.

## Dual barcodes — CONFIRMED design directive

This question is answered. Build to it directly.

Every wig has **two barcodes, both scanned into QuickBooks and linked there under the same wig**:

| | Identifies | Cardinality | Role in this system |
|---|---|---|---|
| **Unit barcode** | one physical wig | unique per unit | traceability, reconciliation, audit |
| **Category barcode** | a style/color/length/size combination | shared across all units in that category | **allocation, availability, fulfillment** |

**Model both.** `Product` is the category and carries the category barcode. `WigUnit` is the individual wig and carries the unit barcode plus a foreign key to its `Product`.

**The fungibility rule is a hard constraint on the design:**

- Availability, commitment, and shortage are computed **at the `Product` level only**.
- `Commitment` references `productId`, never `wigUnitId`. There is no unit reservation table and no unit allocation logic. Do not build one.
- Pick lists name a category and a quantity. They never name a unit.
- A scan screen resolves whichever barcode it receives to a `Product` and validates against the line at the **category** level.
- `WigUnit` is written to by scans and read for history. It is never read by the availability engine.

Getting this wrong in either direction is costly. Building unit-level allocation creates phantom shortages and impossible pick instructions. Discarding unit barcodes entirely loses the traceability the business already has. Model both; let only the category drive decisions.

**On reconciliation:** because both barcodes are scanned, unit-level history can be compared against category-level site quantities from QuickBooks. A discrepancy — a unit last scanned into Salon A while the mirror shows zero units of that category at Salon A — is a useful operational signal. Surface it as a reconciliation report in a later phase; do not let it block anything.

## Order intake — Jotform

**Primary path: Jotform webhooks**, which POST the submission on completion. **Reconciliation:** on a schedule, pull recent submissions from the Jotform API (`/form/{formId}/submissions`) and create anything the webhook missed. Webhook delivery is not guaranteed; reconciliation is what makes intake trustworthy.

**Excel export: fallback and migration only.** Build a CSV/XLSX importer mapping a collection's export columns to the canonical model. Use it for backfill and manual recovery. **Do not build the live workflow on it.**

**Per-collection mapping as configuration.** Store a `FormMapping` per Jotform form describing how its fields map to canonical attributes. Adding a collection means adding mapping rows, not writing code.

**Always store `rawSubmission`** exactly as received, immutably, before normalization.

## Product resolution — CONFIRMED: one QuickBooks item per attribute combination

**This is settled.** QuickBooks uses **one item per full attribute combination** — a distinct item for Lace Fall / 14" / #2 / M, another for Lace Fall / 14" / #2 / L, and so on. Every combination has its own item, its own quantity, and its own category barcode.

This means resolution is a **lookup, not a matching problem.** Four normalized attributes go in, exactly one `Product` comes out. Do not build fuzzy matching, similarity scoring, or candidate ranking for resolution. That machinery belongs only in the substitution engine, which is a different problem with a different purpose.

The only real work is **normalizing the customer's phrasing into the canonical attribute vocabulary** before the lookup:

```
1. Normalize each submitted attribute against a controlled vocabulary:
     collection  — "lace fall", "Lace-Fall", "LF"     → LACE_FALL
     length      — "14", "14 inch", "14\"", "14in"     → 14
     color       — "#2", "2", "color 2", "no. 2"       → 2
     cap size    — "M", "med", "medium"                → M
2. Look up the exact normalized tuple in Product
3. Exactly one match  → resolved. This is the overwhelmingly common case.
4. No match           → ProductResolutionTask for Itzel
5. Human resolution is stored as a ProductAlias, so that exact raw
   phrasing normalizes automatically next time
```

Step 5 is what makes this improve with use. Unrecognized phrasings should become rare within weeks.

**A no-match means one of three things**, and the resolution task should help Itzel tell them apart:
- an unrecognized way of writing a known attribute → add an alias
- a genuinely new product not yet in QuickBooks → needs a QuickBooks item first
- a customer error or typo → correct the line, contact the customer if needed

**Do not auto-create products.** A missing combination is a signal about QuickBooks, not something this system fixes on its own.

Track the resolution failure rate as an operational metric. If it stays high after the alias table matures, the vocabularies are wrong and need attention.

## QuickBooks — the central technical dependency

QuickBooks Desktop Enterprise holds all inventory truth, by site, and has no modern REST API. Every availability and recall decision depends on it.

Build a `QuickBooksSource` interface, read-only, with a fully functional mock so all development proceeds without a live connection:

```
getItems()                                  → item list with attributes
getInventoryBySite()                        → item, site, quantity on hand
getSites()                                  → warehouse and salon sites
getTransferHistory(since)                   → transfers, with dates, for aging
getCustomer(quickbooksCustomerId)
getCustomerBalance(quickbooksCustomerId)
getRecentInvoices(quickbooksCustomerId, since)
```

Two implementations, in this order:

**1. Scheduled report import (recommended v1).** QuickBooks Enterprise can export inventory-by-site and customer-balance reports to CSV. A scheduled export into a watched folder, or a manual upload, feeds the mirror. Unglamorous, near-zero burden on whoever administers QuickBooks, no writeback risk, achievable immediately. **Every screen showing a quantity must show how old the data is.**

**2. Live connection (later).** QuickBooks Web Connector with QBXML, or a hosted connector. Only after we discuss the approach.

Design the availability engine so mirror freshness is an explicit input, never an assumption.

**On consignment aging — CONFIRMED:** the QuickBooks inventory-by-site export **includes transfer dates**. Holding duration is computed from the actual Transfer Inventory date, not from a date this system invents. Aging is exact and the 14-day eligibility rule can be applied with confidence. Do not build a snapshot-estimation fallback; use the real dates.

## Delivery — Shipday

Shipday provides an API and webhooks for order status and proof of delivery. Ingest confirmations to advance lines automatically. **Always retain a manual mark-delivered fallback**, since today's process is manual and the integration must not become a single point of failure. Unmatched confirmations go to a reconciliation queue.

## Barcode scanning

Assume a standard scanner in keyboard-wedge mode. Build focused scan-input screens for dispatch and receiving that accept rapid input, echo what was scanned, and are usable without a mouse. Every scan writes a `ScanEvent` — an immutable fact, independent of any status it triggers.

**Barcode resolution order.** A scan screen receives a string and does not know which barcode it is. Resolve deterministically:

```
1. Look up the string in WigUnit.unitBarcode
     → found: resolve to that unit AND its product
2. Otherwise look up in Product.categoryBarcode
     → found: resolve to that product, no unit recorded
3. Otherwise: unrecognized-barcode exception, scan still recorded
```

The operator is never asked which barcode they scanned. Echo back the resolved **category** in large text — "Lace Fall / 14 / #2 / M" — because that is what matters for correctness. Show the unit identifier smaller, as secondary information.

**Validation at scan time** is against the category only. Right category, wrong unit is a normal, silent success. Wrong category is an immediate visible rejection.

## Core state machine — LINE LEVEL

Build and test this before any UI.

```
LINE STATUS
───────────
NEW
 ├─ available ────────► READY_TO_FULFILL ──► PICKING ──► OUT_FOR_DELIVERY ──► DELIVERED
 └─ short ──► NEEDS_SUBSTITUTION
        ├─ accepted ─────────► READY_TO_FULFILL ──► ...
        └─ declined
             ├─ shipment ≤ window ──► WAITING_FOR_SHIPMENT ──► READY_TO_FULFILL ──► ...
             └─ no shipment ──► RECALL_PENDING
                                ├─ reported sold ──► back to site selection
                                └─ confirmed ──► AWAITING_RETURN ──► RETURN_RECEIVED
                                                                     └─► READY_TO_FULFILL ──► ...

UNRESOLVED  ← any line whose product could not be resolved; blocks all of the above
```

**Order status is derived, never stored:**

```
all lines DELIVERED              → FULFILLED
some DELIVERED, some open        → PARTIALLY_FULFILLED
no lines DELIVERED, all open     → OPEN
any line UNRESOLVED or excepted  → NEEDS_ATTENTION  (badge, not a status)
```

**Holds are flags, not states.** `HOLD_WITH_SAMMY` and `ON_BILLING_LIST` apply to accounts and coexist with any status.

## Data model — starting point

Refine this, then show me the schema before writing application code.

```
Site                 id, name, type (WAREHOUSE|CUSTOMER), customerId (nullable),
                     quickbooksSiteName

Customer             id, name, contactName, whatsappNumber, email, region,
                     quickbooksCustomerId, siteId, lastKnownBalance,
                     balanceSyncedAt, onBillingList, holdWithSammy,
                     holdReason, notes

Collection           id, name, jotformFormId, attributeSchema (JSON)
FormMapping          collectionId, jotformFieldId, canonicalField, transform

Product              id, collectionId, style, color, length, capSize,
                     quickbooksItemId, categoryBarcode, active
                     -- the CATEGORY. all units of a product are interchangeable.
                     -- availability and commitment operate here and nowhere else.
ProductAlias         productId, rawText, normalizedTuple, createdBy, createdAt

WigUnit              id, productId, unitBarcode, currentSiteId, lastScanAt
                     -- traceability only. NEVER referenced by Commitment,
                     -- the availability engine, or any pick instruction.

InventoryMirror      productId, siteId, quantityOnHand, firstObservedAt,
                     lastTransferAt, sourceSyncId, syncedAt
InventorySync        id, source, startedAt, completedAt, status,
                     recordCount, errorDetail

Commitment           orderLineId, productId, quantity, createdAt, releasedAt
                     -- productId only. there is deliberately no wigUnitId here.

Order                id, orderNumber, customerId, source (JOTFORM|MANUAL|IMPORT),
                     jotformSubmissionId, placedAt, automationPaused,
                     rawSubmission (immutable JSON)
                     -- no status field; derived from lines

OrderLine            id, orderId, requestedText, productId (nullable),
                     quantityRequested, quantityDelivered,
                     status, originalProductId, substitutionReason,
                     availabilityVerdict, verdictComputedAt, statusChangedAt

ProductResolutionTask  orderLineId, rawText, normalizedAttempt (JSON),
                       failureReason (UNKNOWN_PHRASING|NO_SUCH_PRODUCT|
                                      CUSTOMER_ERROR),
                       status, resolvedProductId, resolvedById, resolvedAt

AttributeVocabulary    attributeName (COLLECTION|LENGTH|COLOR|CAP_SIZE),
                       canonicalValue, alias, createdBy, createdAt

ScanEvent            id, scannedString, barcodeType (UNIT|CATEGORY|UNKNOWN),
                     productId (nullable), wigUnitId (nullable), siteId,
                     direction (OUT|IN), orderLineId (nullable),
                     scannedById, scannedAt

Delivery             id, orderId, driverId, shipdayOrderId, dispatchedAt,
                     deliveredAt, proofType (PHOTO|SIGNATURE), proofUrl
DeliveryLine         deliveryId, orderLineId, quantity

IncomingShipment     id, productId, quantityExpected, expectedArrival,
                     supplier, status
ReceivingEvent       incomingShipmentId, productId, quantity,
                     destination (ONWARD|STOCK), receivedById, receivedAt

ReturnItem           id, productId, wigUnitId (nullable), fromSiteId,
                     triggeringOrderLineId (nullable),
                     destination (ONWARD_TO_CUSTOMER|BACK_TO_STOCK),
                     destinationCustomerId (nullable), status,
                     confirmedAt, pickupScheduledAt, driverId, receivedAt

QuickBooksTask       id, type (TRANSFER_INVENTORY), returnItemId,
                     fromSiteId, toSiteId, productId, quantity,
                     status (PENDING|ENTERED), enteredById, enteredAt

Driver               id, name, phone, regionsCovered, shipdayDriverId
Note                 entityType, entityId, authorId, body, createdAt
Message              orderLineId, customerId, channel, direction, body,
                     status (DRAFT|SENT|RECEIVED), draftedByAI,
                     sentById, sentAt, loggedAt
BusinessRule         key, value, description, updatedBy, updatedAt
AuditEvent           entityType, entityId, actorId, action, before, after, createdAt
```

**Every state change writes an `AuditEvent`.** The absence of history is the worst property of the current spreadsheet.

**On `ScanEvent`:** a scan is an immutable fact recorded independently of whatever status change it triggers. Always store the raw scanned string, even when resolution fails. If the status logic is wrong, the scan history still tells you what physically happened.

**On `WigUnit` and fungibility:** the presence of a unit table is a standing temptation to allocate by unit. Resist it. Add a test asserting that `Commitment` has no unit reference and that the availability engine never queries `WigUnit`. If a future requirement genuinely needs unit-level allocation, that is a deliberate design change to discuss, not something to drift into.

**On collection variation:** no table or code path per collection. Normalize to `Product`, drive intake through `FormMapping`, keep collection-specific extras in validated JSON.

**On messaging:** v1 is human-sent. `Message` records what was drafted, what was sent, and what came back, pasted in by Anne. WhatsApp conversations are genuinely back-and-forth, so model a **thread per line**. When a Business API arrives, only the send/receive adapters change.

## Substitution engine — deterministic

```
1. Filter to products with (warehouse quantity - open commitments) > 0
2. Score by attribute distance, weighted:
     same collection    (heaviest)
     color proximity
     length proximity
     cap size match
3. Apply configurable rules (exclusions, discount tiers)
4. Return the ranked list WITH per-attribute scores exposed
5. Only then: AI writes the rationale and the customer-facing offer
```

The model never selects candidates. It explains and phrases what the code selected.

## AI service boundaries

All model calls behind a single `lib/ai/` service with typed inputs and outputs, a mockable interface, and unit tests that never hit the network. Every AI output persists with a `draftedByAI` marker.

---

# PART SEVEN — HOW TO WORK

## Phases

**Phase 0 — Plan.** Write `docs/PLAN.md` containing: your questions for me, the refined data model, the complete line-level state machine with guards, order-status derivation rules, an explicit statement of how you will enforce category-level-only allocation, your critique of this opportunity map, and your phase breakdown. **No application code. Stop and wait for approval.**

**Phase 1 — Foundation.** Scaffold, Docker Compose, Prisma schema, migrations, and a seed script with realistic data: 5+ collections, ~200 products, ~30 customers each with a site, a warehouse site, inventory distributed across sites with varied transfer dates, ~80 orders with lines across every status including partially fulfilled, incoming shipments. Auth with four roles. **The line state machine and order-status derivation fully unit tested.**

**Phase 2 — Order management.** Order board, order detail with line-level status, audit timeline, notes, manual order entry, guarded transitions, account flags. **This alone should let them stop using the spreadsheet for order tracking.**

**Phase 3 — Jotform intake and product resolution.** Webhook receiver with verification and a failed-import queue, `FormMapping` configuration, per-collection normalization, the reconciliation job, duplicate detection, the attribute vocabulary with its alias-learning loop, the exact-match product lookup and resolution queue, and the Excel importer for backfill. **Resolution is a normalization layer over an exact lookup — do not build fuzzy matching here.**

**Phase 4 — QuickBooks mirror and availability.** `QuickBooksSource` interface, mock, report-import implementation, **multi-site** mirror with staleness indicators, the availability engine, commitment tracking, the **"who has this wig" view**, and consignment aging from transfer dates.

**Phase 5 — Scanning, dispatch, delivery, receiving.** Scan-out and scan-in screens, `ScanEvent` capture, Shipday integration with manual fallback and a reconciliation queue, the receiving screen for tagged supplier arrivals.

**Phase 6 — Recalls and returns.** Recall workflow from the site view, return records with destinations, pickup lists grouped by region, driver assignment, scan-in routing, and the QuickBooks transfer task worklist with escalation.

**Phase 7 — Substitutions, AI, messaging.** Deterministic substitution engine with tests, AI rationale and drafting, message threads with copy-to-WhatsApp and paste-reply, note summarization, reply classification.

**Phase 8 — Analytics, dashboards, and the billing list report.**

## Rules of engagement

- Ask when a business rule is ambiguous. Do not invent policy. Record unavoidable assumptions in `docs/ASSUMPTIONS.md` and flag them in the phase summary.
- Every business rule with a number in it lives in `BusinessRule` or settings, never inline.
- No raw status mutations. All transitions go through one guarded service.
- **Never write to QuickBooks.** Not in a helper, not in a test fixture, not behind a flag. The interface is read-only by construction.
- Scans and Shipday confirmations are recorded as immutable events, separate from the status changes they cause.
- Write real tests for: the line state machine, order-status derivation, product resolution, substitution ranking, commitment and double-commitment prevention, consignment aging from transfer dates, return routing, and the nightly jobs. UI tests stay light.
- Commit after each phase. Stop at each phase gate for review.
- Prefer boring, obvious code. Someone else will maintain this.

## Open questions to raise in Phase 0

**Blocking — schema-shaping**
- Where do the two barcodes come from — printed by Renaissance Wigs, or applied by the supplier before arrival? This determines whether the system needs to generate or register barcodes at receiving, or only read them.
- Are the barcode-to-wig links already exportable from QuickBooks, or does the unit-to-category relationship need to be built up from scans over time?
- Is cap size handled consistently with color and length in the QuickBooks item naming, or treated differently? An inconsistency here breaks the normalization layer.
- Can the full item list be exported once to seed the `Product` table and the attribute vocabularies, and how are item names structured for parsing?
- Are the salon sites in QuickBooks named consistently enough to map reliably to customer records?

**Needed by Phase 4**
- How often can these reports realistically be exported — hourly, daily? This sets how stale availability data will be and therefore how much the system can be trusted to promise.
- Who administers the QuickBooks installation, and what are they willing to set up?

**Needed by Phase 5**
- Does Renaissance Wigs have Shipday API access, and at what plan level?
- Does the driver app expose which specific items were on a delivery, or only the delivery as a whole?
- Are pickups entered into Shipday as jobs, or handled informally?

**Needed by Phase 6**
- Are delivery routes fixed by region and day, or built ad hoc? This shapes the pickup list.
- Who performs the Transfer Inventory entry in QuickBooks — warehouse or billing?

**Needed by Phase 8**
- What balance threshold puts a customer on the billing list, and who sets it?
- Who receives the billing list email, and should the system send it or only produce it?
- Can Sammy clear a Hold with Sammy flag directly, or must something change in QuickBooks first?

**Migration — needed by Phase 3**
- How much order history is worth migrating, and from when?
- Do the collection worksheets have consistent enough structure to map, or does each need a hand-written mapping?
