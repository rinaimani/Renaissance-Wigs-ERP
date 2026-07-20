# Renaissance Wigs — Order-to-Delivery Process Redesign

## Business profile

**Business name:** Renaissance Wigs

**What it does:** Renaissance Wigs is a wholesale wig distributor. It sells wigs in bulk to retail wig stores and salons, which resell them to individual consumers. A large share of its inventory sits at customer locations on consignment rather than in the warehouse, so recovering inventory from customers is a routine part of filling orders.

**Size:**

- 1 manager
- 1 order intake clerk
- 1 operations lead
- Warehouse staff and drivers
- A billing function working out of QuickBooks
- 10 to 40 orders per day, seasonally variable

**Customer channels:** Jotform for order placement, WhatsApp for all operational communication.

**Typical customer:** A retail wig store or salon that buys wholesale, holds inventory on consignment, and resells to walk-in retail customers.

**How the business makes money:** Wholesale sales of wigs to retail accounts, including consignment placements that convert to sales.

**Primary business goal:** Replace the large manually maintained spreadsheet with a centralized order management system that tracks every order from placement through delivery, reduces manual work, and improves visibility — without changing QuickBooks.

**Current operational problem:** Orders arrive through Jotform, which automatically updates an export spreadsheet, from which they are hand-copied into a second operational spreadsheet. Order status is expressed through row colors and by moving rows between sections. Each wig collection has its own worksheet with a different column layout. Customer conversations exist only as free text in a notes column. There is no history of what happened to an order, and management cannot see the operational picture without reading the spreadsheet row by row.

### People and responsibilities

**Sammy — Manager**

- Reviews customer accounts with low sales activity or account concerns.
- Approves or releases accounts held for review.
- Reviews business performance.

**Itzel — Order intake**

- Monitors the Jotform export spreadsheet for new orders.
- Copies each new order into the operational spreadsheet.

In the future system, Itzel should not copy anything. Her role should become exception handling — failed imports, occasional phone orders, and records the system could not match.

**Anne — Operations lead**

- Reviews every new order and determines whether each requested wig is available.
- Looks the product up in QuickBooks and determines which location holds it.
- Prepares outbound orders and hands them to drivers.
- Contacts customers on WhatsApp when an item is unavailable and negotiates alternatives.
- Tracks incoming supplier shipments and expected arrival dates.
- Requests wigs back from salons and records which ones are coming and where they need to go.
- Records everything in the notes column.

In the future system, Anne should not have to work out every next step herself. The system should tell her what each order needs, surface candidate alternatives, surface who is holding a wig, and prepare her customer messages for approval.

**Warehouse and receiving**

- Scan wigs out on dispatch and in on return.
- Read the tags on incoming supplier wigs to decide whether each goes onward to a waiting customer or into stock.
- Enter inventory transfers into QuickBooks when a wig comes back from a salon.

**Drivers**

- Deliver orders through the Shipday app, capturing a photo or signature.
- Collect returning consignment inventory on the same routes.

**Billing**

- Receives an emailed list of customers with high QuickBooks balances and contacts them about payment.

**Customer**

- Places wholesale orders through Jotform, describing wigs by attribute.
- Responds on WhatsApp about availability, substitutions, arrival dates, and return requests.
- Holds inventory on consignment and returns it when asked, if it has not sold.

### Products

A wig is specified by five attributes: collection, style, length, color, and cap size. Collections include Ready to Wear, Lace Top, Lace Fall, Hand Tied, Allure, and others.

Orders are placed by attribute, not by item number. A customer writes something like "Lace Fall, 14 inch, color #2, size M."

Every physical wig carries two barcodes: one unique to that individual wig, and one shared by every wig of the same collection, style, color, length, and cap size. Both are scanned into QuickBooks and linked there under the same wig.

**Wigs in the same category are identical and completely interchangeable.** It does not matter which individual wig is sent to fill an order, as long as it is the right category.

### Operating rules

- Orders are placed through Jotform. Each collection has its own form producing its own field set.
- QuickBooks Desktop Enterprise is the source of truth for all inventory and customer information, and nothing in the new system may change it. All QuickBooks entry stays manual.
- QuickBooks is used with multiple inventory sites. The warehouse is one site, and each salon holding consignment inventory is its own site. "Who has this wig" is answered by looking at quantity by site.
- QuickBooks holds one item per full attribute combination, each with its own quantity and its own category barcode.
- Consignment movements are recorded in QuickBooks as inventory transfers, and those transfers carry dates.
- Partial fulfillment is normal. Whatever is available goes out with the driver, and unfilled items are worked separately.
- When an item is unavailable, the customer is offered an alternative before the order is delayed: a different color, length, cap size, collection, a similar product, or a discounted option.
- If a supplier shipment containing the item is expected within one week, the order waits for it rather than recalling from a salon.
- Inventory held at a salon for more than 14 days is eligible to be requested back.
- A recall always begins with a WhatsApp conversation. The customer must confirm the item is unsold before a pickup is arranged.
- A returned wig either goes onward to the specific customer whose order triggered the recall, or back into general stock.
- Customers with a high QuickBooks balance go on a list that is emailed to billing for collection.
- Accounts with fewer than three sales in a month, or other concerns, are flagged for the manager's review.
- Orders always get fulfilled. There is no routine cancellation or refund path.

### Current pain points

- New orders must be manually copied from the Jotform export into the operational spreadsheet.
- Employees manually move rows between sections to indicate status, and progress is encoded in row colors.
- Every collection has a different column layout, so staff work with inconsistent formats all day.
- Inventory truth lives in QuickBooks, outside the daily workflow, so availability is judged from a spreadsheet copy of unknown freshness.
- Resolving a requested wig to a QuickBooks item is done by hand on every order line.
- Finding which salon holds a wig means checking sites one at a time.
- A partially filled order has no honest representation in a single spreadsheet row.
- Customer communication exists only as free-text notes, unsearchable and unattributable.
- Consignment aging is judged from memory rather than from the transfer dates QuickBooks already records.
- Return pickup lists are rebuilt by hand and exist only on paper, and the instruction for where each returned wig needs to go exists nowhere else.
- Delivery confirmations from the driver app are entered into the spreadsheet manually, when someone remembers.
- There is no centralized history of actions taken on an order, so a disputed order cannot be reconstructed.
- Management cannot see status, aging, throughput, consignment exposure, or account health without reading the spreadsheet.

## The current manual process without custom software

### Trigger

A retail wig store or salon submits an order through the Jotform form for a specific wig collection, describing each wig by collection, length, color, and cap size.

### Manual step-by-step process

1. **Receive the order.** Jotform captures the submission and automatically updates an export spreadsheet.
2. **Copy the order.** Itzel opens the export, finds new rows, and pastes them into the operational spreadsheet under New Orders.
3. **Review the order.** Anne reads each requested wig.
4. **Identify the product.** She works out which QuickBooks item matches the description.
5. **Check availability.** She looks up the quantity in QuickBooks.
6. **Find the location.** She checks which site holds it — the warehouse, or a specific salon.
7. **Pull what is available.** Warehouse stock is picked and the wig is scanned out.
8. **Update the spreadsheet.** Anne moves the row to Processing and changes the row color.
9. **Send the delivery.** A driver delivers through Shipday, capturing a photo or signature. Partial deliveries are normal.
10. **Record the delivery.** Someone updates the spreadsheet manually from the driver app confirmation.
11. **Set aside what could not be filled.** Anne works those items separately.
12. **Offer an alternative.** For an unavailable item, Anne messages the customer on WhatsApp proposing a different color, length, cap size, collection, similar product, or discounted option.
13. **Record the conversation.** She types what was said into the notes column.
14. **Ship the substitute.** If the customer accepts, Anne updates the notes and sends the replacement.
15. **Check incoming shipments.** If the customer declines, she checks whether the item is arriving from a supplier soon.
16. **Give an arrival date.** If a shipment is due within a week, the customer is told when, and the item waits.
17. **Find who has one.** If no shipment is expected, Anne looks in QuickBooks for a salon holding the wig.
18. **Judge how long they have had it.** More than 14 days makes it eligible to request back.
19. **Ask for it back.** Anne messages that salon on WhatsApp to ask whether it has sold.
20. **Get confirmation.** If unsold, the salon confirms they will return it.
21. **Build the return list.** Anne adds it to a list recording every item coming back and where each one needs to go.
22. **Collect it.** A driver picks it up on a delivery route.
23. **Receive it.** The wig arrives, is scanned in, and the list is checked to route it onward or into stock.
24. **Record the transfer.** Someone enters the inventory transfer into QuickBooks by hand.
25. **Fill the waiting order.** The item goes out and the row is moved to Fulfilled and colored green.
26. **Chase payment separately.** High-balance customers are listed out of QuickBooks and emailed to billing.
27. **Review accounts separately.** Sammy looks at accounts with low sales activity.
28. **Count results manually.** Anyone wanting operational numbers counts rows in the spreadsheet.

### What goes wrong

- An order can disappear between the Jotform export and the operational spreadsheet.
- Row color is the only status, and it is easy to misapply or overlook.
- Moving rows between sections can drop or duplicate an order.
- Inconsistent column layouts across collections cause transcription errors.
- Translating a customer's description into a QuickBooks item depends on someone who knows the catalog.
- Finding which salon holds a wig means checking sites one by one.
- A partially filled order cannot be honestly represented in one row.
- Consignment aging is guessed at, even though QuickBooks records the actual transfer dates.
- The return list is rebuilt by hand, items in transit are invisible, and routing instructions exist only on that list.
- Delivery confirmations reach the spreadsheet only when someone remembers to enter them.
- Nobody but Anne knows the state of an ongoing customer negotiation.
- There is no audit trail, so a disputed order cannot be reconstructed.
- Management reporting is slow, incomplete, and stale by the time it exists.

## Opportunity map

### What should become software

- **Structured order record** — every order stored in one format regardless of which collection it came from.
- **Order-line-level status** — because partial fulfillment is normal, each item on an order needs its own status, with the order's overall status following from its items.
- **Status workflow** — clear states replacing row colors and section moves.
- **Order board** — every open order visible with status, age, customer, and next action.
- **Order detail view** — the original request, every item's status, substitution history, notes, messages, and full history.
- **Product identification** — translating a customer's attribute description into the correct QuickBooks item, consistently and without a person doing it each time.
- **Inventory visibility by location** — current quantities for each product at the warehouse and at each salon, drawn from QuickBooks.
- **"Who has this wig" view** — for any product, which locations hold it and how long they have held it. This replaces checking QuickBooks site by site.
- **Availability checking** — determining what can be filled now, accounting for items already promised to other orders.
- **Consignment visibility** — what is out at customer locations, how old it is, and what is eligible to be recalled.
- **Barcode capture** — recording wigs going out and coming back, accepting either barcode.
- **Incoming shipment tracking** — what is expected, when, and who is waiting for it.
- **Return workflow** — the recall request, the customer's confirmation, the pickup list, the collection, and the routing decision on arrival.
- **QuickBooks entry worklist** — a running list of transfers owed to QuickBooks so nothing is missed.
- **Geographic grouping** — customers grouped by area so pickups and deliveries can be batched.
- **Account flags** — accounts held for manager review or on the billing list, independent of any order.
- **Activity history** — a permanent record of every status change, edit, scan, and action.
- **Search and filters** — find orders by customer, status, date, collection, and product.
- **Permissions** — limit what intake, operations, warehouse, and management staff can see or change.

### What should become automation

- Create the order automatically when a Jotform submission arrives, with no copying.
- Check availability on every item as soon as the order lands.
- Keep the local view of QuickBooks inventory refreshed on a schedule, and make failures visible.
- Move an item to a waiting state automatically when a matching supplier shipment is due soon.
- Resume a waiting item automatically when the shipment it needs arrives.
- Pull delivery confirmations from the driver app instead of having someone enter them.
- Build the driver's pickup list from confirmed returns, grouped by area.
- On receiving a returned wig, route it per the recorded instruction and add the QuickBooks transfer to the worklist.
- Flag consignments past the eligibility threshold, accounts meeting review criteria, and orders aging in place.
- Generate and send the high-balance billing list.
- Alert the right person when an order needs a decision, when a pickup is scheduled, or when a return arrives.

### What should use AI

- Summarize long or messy note histories on an order.
- Interpret free-text customer replies — accepted, declined, asked for a date, confirmed a return, reported an item sold.
- Suggest how to read an unfamiliar way of writing a product attribute, for a person to confirm.
- Explain why a suggested alternative is a good match, in language a customer will understand.
- Draft WhatsApp messages for substitution offers, arrival dates, recall requests, and delivery notices.
- Rank options that already satisfy the business rules — for instance, which of several eligible salons to approach first.
- Suggest a reason category when a customer declines a substitution.
- Later, answer internal questions about orders, inventory, and customer activity.

### What should not use AI

- Inventory quantity math and availability determination
- Location-level quantity lookups
- Product identification, since one QuickBooks item exists per attribute combination
- Which products count as valid alternatives
- Consignment eligibility and the 14-day threshold
- The incoming-shipment timing rule
- Account flag criteria
- Order and item status transitions
- Barcode identity — a scan is a fact, not an interpretation
- Which individual wig ships, since wigs in a category are interchangeable
- Preventing the same stock from being promised twice
- Where a returned wig is routed
- Duplicate order detection
- Authentication and permissions
- Sending any customer message without human approval
- Anything written back to QuickBooks — the system only reads

These are deterministic rules and should remain predictable.

### What analytics should exist

#### Operational analytics

- New orders today and this week
- Items in each status
- Items blocked on inventory, awaiting a customer decision, or waiting on a shipment
- Open recalls by stage
- How long items have been sitting in each status
- How often orders ship incomplete, and which products cause it
- Time from order placed to fully delivered
- Pending pickups and deliveries by area

#### Inventory and consignment analytics

- Inventory by product and by location
- Units out on consignment by customer and by age
- How often consignment placements convert to sales
- Recall success rate and average time to recover a wig
- Most frequently out-of-stock products
- Incoming shipment pipeline
- Backlog of transfers awaiting manual QuickBooks entry
- How often an order needs manual product identification

#### Customer and account analytics

- Order volume by customer
- How often each customer accepts a substitution, and which kinds
- Accounts held for manager review, and why
- Accounts on the billing list and total exposure
- Customers with fewer than three sales this month

#### Management analytics

- Order pipeline by stage
- Demand by collection and by attribute
- Geographic demand and route load
- Where orders stall most often
- Seasonal volume trend

## Ownership legend

- **Human:** Makes judgments, negotiates with customers, approves substitutions and messages, resolves exceptions, and performs all QuickBooks data entry.
- **Software:** Captures, stores, validates, displays, and controls trusted operational state; applies deterministic availability and eligibility rules; maintains history.
- **Automation:** Performs a known action when a defined event occurs.
- **AI:** Interprets messy language and produces summaries, drafts, or rankings among options that already satisfy the rules.
- **Analytics:** Measures events and turns system history into operational insight.

## Full workflow after we build this system

1. **Human — Customer:** Submits an order through Jotform, describing each wig by attribute.
2. **Software:** Creates the order automatically, with an identifier and timestamp, and preserves the original submission.
3. **Software:** Identifies which QuickBooks item each requested wig corresponds to. Anything it cannot identify goes to a person, never a guess.
4. **Software:** Matches the submitter to a customer record, or flags it when unclear.
5. **Analytics:** Records the collection, requested attributes, and time received.
6. **Software:** Checks availability for each item and assigns it an initial status.
7. **Automation:** Puts the order on the board and alerts Anne when an item needs a decision.
8. **AI:** Summarizes any customer notes and extracts special requests.
9. **Software:** Shows the original request and the AI summary separately, so the source data stays trustworthy.
10. **Human — Anne:** Opens the board and reviews the order.
11. **Software:** Marks the available items as committed so they are not promised to another order.
12. **Human — Warehouse:** Picks any wig from the correct category and scans it out.
13. **Automation:** The delivery goes out through the driver app and the confirmation comes back into the system automatically. The order stays open if other items are unresolved.
14. **Software:** For each unfilled item, assembles the valid alternatives that are actually in stock.
15. **AI:** Explains why each is a good match and drafts an offer to the customer.
16. **Human — Anne:** Reviews, adjusts, approves, and sends the message.
17. **Human — Customer:** Accepts, declines, or asks a question. **AI:** Interprets the reply. **Human — Anne:** Confirms before anything moves.
18. **Software:** If accepted, records the substitution alongside the original request and the item proceeds to fulfillment.
19. **Software:** If declined, checks whether the item is arriving from a supplier soon and gives the customer a date if so.
20. **Software:** If nothing is arriving, shows which salons hold the wig, how long each has had it, and which are eligible to be asked.
21. **AI:** Ranks the eligible salons and drafts the recall request.
22. **Human — Anne:** Sends the request and records the outcome.
23. **Software:** If the salon confirms, records the return along with where the wig needs to go when it arrives.
24. **Automation:** Adds it to the pickup list for that area.
25. **Human — Driver:** Collects it on a delivery route.
26. **Human — Warehouse:** Scans it in. **Software:** Routes it per the recorded instruction and adds the transfer to the QuickBooks worklist.
27. **Human — Warehouse or billing:** Enters the transfer into QuickBooks and marks it done.
28. **Software:** Releases the waiting item for the next delivery, and closes the order when every item has been delivered.
29. **Automation:** Flags accounts for review, flags aging consignments, and sends the billing list.
30. **Human — Sammy:** Reviews flagged accounts and records decisions.
31. **Analytics:** Updates pipeline, aging, inventory, consignment, substitution, and account reporting.
32. **Human — Sammy:** Reviews the reporting and adjusts purchasing, consignment policy, and account terms.

## Failure and exception paths

- An order that fails to arrive from Jotform stays visible and can be retried, and a scheduled check catches anything missed.
- A requested wig that cannot be matched to a QuickBooks item becomes a task for a person, never a guess.
- An unmatched customer is flagged for review.
- A duplicate submission is flagged rather than silently creating two orders.
- The same stock is never promised to two orders.
- When the local view of QuickBooks inventory is out of date, every quantity is visibly marked as such. The system does not block work; it is honest about what it knows.
- A failed inventory refresh is surfaced as an alert, not buried in a log.
- A scanned barcode that matches nothing creates an exception, and the scan is still recorded.
- Scanning a different wig of the correct category is always fine. Scanning the wrong category is rejected immediately and visibly.
- A delivery confirmation that cannot be matched to an order goes to a queue for review, and marking a delivery by hand remains possible.
- AI output is always shown as a suggestion and never overwrites what the customer said.
- A substitution always requires explicit human approval.
- No customer message is ever sent automatically.
- If no eligible salon holds the wig, the system raises a visible exception rather than inventing an option.
- If an AI recommendation conflicts with a business rule, the rule wins and the recommendation is not available.
- A person can pause automation on any order.
- A returned wig arriving with no recorded destination becomes a routing exception.
- A QuickBooks transfer left unentered too long is escalated, because that is where the system's view quietly drifts from reality.
- There is no routine cancellation path. Closing an order unfilled should require a manager and a recorded reason, and should be reported on.

## Renaissance Wigs Rules

- Orders are placed through Jotform, one form per collection, describing wigs by attribute.
- QuickBooks Desktop Enterprise is the source of truth for inventory and customer data, and the new system never writes to it.
- QuickBooks uses multiple inventory sites; the warehouse is one and each salon is its own.
- QuickBooks holds one item per full attribute combination.
- Inventory transfers in QuickBooks carry dates, and those dates are what consignment aging should be based on.
- Every wig has a unit barcode and a category barcode, both linked in QuickBooks.
- Wigs within a category are identical and interchangeable; which individual wig ships never matters.
- Partial fulfillment is normal and must be represented honestly.
- A human confirms every substitution and approves every customer message.
- Items held at a salon more than 14 days are eligible to be requested back, and a recall requires the customer's confirmation first.
- A supplier shipment due within one week takes precedence over recalling from a salon.
- Returned wigs are routed either onward to a waiting customer or back into stock, and that decision is recorded before the wig arrives.
- Orders always get fulfilled.

## Renaissance Wigs Pain Statement

Renaissance Wigs receives 10 to 40 wholesale orders per day, submitted through Jotform and then hand-copied from an export spreadsheet into a second operational spreadsheet where every collection has a different column layout.

Order status exists only as row colors and as rows moved between sections, so a partially filled order — three items delivered, one substituted, one waiting on a wig to come back from a salon — has no honest representation anywhere.

Inventory truth lives in QuickBooks, outside the daily workflow, so each requested wig is matched to a QuickBooks item by hand and then located by checking sites one at a time. Consignment aging is judged from memory even though QuickBooks records the actual transfer dates. Return pickup lists are rebuilt by hand and exist only on paper, and the instruction for where each returned wig must go exists nowhere else. Delivery confirmations arrive in the driver app but reach the spreadsheet only when someone remembers.

Every customer conversation lives as free text in a notes cell, so the state of a negotiation is known only to one person. There is no history of what happened to an order, so a disputed order cannot be reconstructed. And management cannot see order status, aging, throughput, partial-fulfillment rates, consignment exposure, or account health without reading the spreadsheet row by row.

## Renaissance Wigs Metric

When a customer submits an order through Jotform, it should appear in the system within one minute, with every requested wig identified, its availability determined, and — where stock is short — the locations holding that wig already surfaced. No employee copies any data by hand, and every action taken afterward is permanently recorded.

### Supporting Metrics

- Zero orders are copied by hand between systems.
- Nobody looks up "who has this wig" site by site in QuickBooks again.
- Every item on every order has a clear status and a clear next owner.
- No item sits past its threshold without an alert.
- Partial fulfillment is visible at all times: what shipped, what was substituted, and what is waiting on what.
- Every substitution records what was originally asked for and what was actually sent.
- Every wig movement has a scan behind it.
- Every returned wig has a recorded destination before it arrives.
- Nothing returns to the warehouse without appearing on the QuickBooks entry worklist.
- Delivery confirmations reach the system without manual re-entry.
- Management can see pipeline, aging, inventory, and account health without opening a spreadsheet.

## Renaissance Wigs Hard Constraints

- QuickBooks Desktop Enterprise stays the source of truth for inventory and customer data, and the new system only reads from it. All QuickBooks entry remains manual and human.
- QuickBooks multi-site inventory is the existing mechanism for consignment. The new system reflects it rather than replacing or duplicating it.
- Jotform stays the customer-facing order channel.
- WhatsApp stays the customer communication channel, sent by people for now. A business API number is being explored, but the conversations are genuinely back-and-forth and how that will work is not yet settled.
- Shipday stays the delivery mechanism.
- The original customer submission must be preserved unchanged.
- Partial fulfillment must be a first-class concept, not a workaround.
- The same stock must never be promised to two orders.
- Wigs in a category are interchangeable; nothing may depend on shipping a specific individual wig.
- AI may suggest, draft, summarize, and rank, but may not overwrite what the customer said, approve a substitution, send a message, or override a business rule.
- Every threshold with a number in it must be adjustable without a code change.
- Staff must be able to pause automation on any order.
- Every change must be recorded with who did it and when.
- Differences between collections must not require the system to be rebuilt per collection.

## Possible Preferences

- A single central custom web application.
- Keep the first version simple.
- Use the existing Jotform, WhatsApp, QuickBooks, and Shipday tools rather than replacing them.
- Barcode scanning should work with an ordinary scanner; no custom mobile app in the first version.
- Build in phases: order tracking and history first, since that alone would replace the spreadsheet; inventory visibility and the "who has this wig" view next; recalls, returns, and the substitution and messaging assistance after that.
- Add richer reporting, route optimization, and any customer-facing access later.
- The people using this live in Excel today and are fast at it. A dense, quick, keyboard-friendly interface will be adopted; a slow, wizard-heavy one will not.

---

## What I am asking for

I want to redesign Renaissance Wigs' order-to-delivery process and determine what should be handled by humans, traditional software, automation, AI, and analytics.

I am providing:

- The business profile
- People and responsibilities
- Products, including the two-barcode setup and the fact that wigs in a category are interchangeable
- Current operating rules
- The current manual process
- Current pain points
- My initial opportunity map
- The primary success metric
- Hard constraints
- An initial future-state workflow

**Treat my opportunity map and future-state workflow as hypotheses, not final requirements.** The facts about the business — how QuickBooks is set up, how the barcodes work, the 14-day and one-week rules, how returns are handled — are confirmed and not open to reinterpretation. My decomposition of the work is a guess, and I want it challenged.

I would specifically like to know:

- Where my split between software, automation, and AI is wrong.
- What I have treated as simple that is actually hard, and what I have treated as hard that is actually simple.
- What I have left out entirely.
- What the smallest first version could be that would genuinely let them stop using the spreadsheet.
- Which parts of this carry the most risk, and what would reduce that risk before anything is built.

Do not start designing or building yet. I want the thinking first.

The final goal is to build an app.
