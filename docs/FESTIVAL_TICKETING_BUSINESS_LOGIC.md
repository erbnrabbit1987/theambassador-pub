# Core Business Logic and Rules

## Table of Contents
1. [Ticket Phases and Pricing](#ticket-phases-and-pricing)
2. [Artist Break-Even Model](#artist-break-even-model)
3. [Ambassador System Rules](#ambassador-system-rules)
4. [Code Generation and Validation](#code-generation-and-validation)
5. [Guest Ticket System](#guest-ticket-system)
6. [Commission Calculation](#commission-calculation)
7. [Revenue Tracking](#revenue-tracking)
8. [Quota Management](#quota-management)
9. [Edge Cases and Validation](#edge-cases-and-validation)

---

## Ticket Phases and Pricing

### Phase Structure
Each festival event has **3 time-based ticket phases** with different base prices:

- **Phase 1**: Early bird pricing (e.g., 120 EUR)
- **Phase 2**: Standard pricing (e.g., 150 EUR)
- **Phase 3**: Late pricing (e.g., 170 EUR)

### Phase Rules
1. **Automatic Transitions**: Phases transition automatically based on:
   - Date/time thresholds (admin-configured)
   - Ticket sales milestones (optional, admin-configured)
   - Manual admin override

2. **Current Phase**: Only one phase is active at a time per event
   - System tracks current phase for each event
   - Ticket prices default to current phase base price

3. **Price Application**:
   - Base price = current phase base price for the event
   - Final price = base price - discount (Individual Ambassador) OR custom price (Team Ambassador)
   - Guest tickets = 0 EUR (regardless of phase)

4. **Phase History**: System maintains history of phase transitions for reporting

---

## Artist Break-Even Model

### Core Concept
Artists have **contracted fees** (break-even targets) that must be reached through ticket sales before the festival generates profit.

### Break-Even Calculation

#### Per Artist Tracking
- **Contracted Fee**: Target amount (set by admin)
- **Tickets Sold**: Count of tickets associated with artist
- **Revenue Toward Break-Even**: Sum of ticket prices sold for this artist
- **Break-Even Status**: 
  - `NOT_ACHIEVED`: Revenue < Contracted Fee
  - `ACHIEVED`: Revenue >= Contracted Fee
- **Remaining Amount**: `max(0, Contracted Fee - Revenue Toward Break-Even)`
- **Post Break-Even Revenue**: `max(0, Revenue Toward Break-Even - Contracted Fee)`

#### Ticket Association
- Tickets are associated with artists through:
  - Ambassador codes (if ambassador is linked to artist)
  - Direct artist code (if artists can generate codes)
  - Manual admin assignment
  - Event-artist mapping (all tickets for event count toward artist)

#### Break-Even Achievement
- **Date Achieved**: Timestamp when revenue first exceeded contracted fee
- **Locked Status**: Once achieved, status cannot revert (even if refunds occur)
- **Reporting**: Artists see real-time progress toward break-even

### Revenue Distribution Logic
1. **Before Break-Even**: All revenue counts toward artist's break-even target
2. **After Break-Even**: Revenue beyond target contributes to festival profit
3. **Multiple Artists**: If multiple artists per event, revenue is split (admin-configured):
   - Equal split
   - Percentage-based split
   - Per-artist allocation

---

## Ambassador System Rules

### Ambassador Types

#### Individual Ambassador
**Pricing Logic**:
- Sets **discount percentage** relative to current phase price
- Discount must be within admin-defined range (e.g., 0-30% off)
- Final price = `Current Phase Price × (1 - Discount %)`
- Example: Phase 2 = 150 EUR, 20% discount → Final price = 120 EUR

**Commission Structure**:
- Tiered percentage-based commissions
- Admin-configurable tiers (e.g.):
  - 1-20 tickets: 5% commission per ticket
  - 21-50 tickets: 7% commission per ticket
  - 51+ tickets: 10% commission per ticket
- Commission calculated on **final ticket price** (after discount)
- Commission tiers are **cumulative** (tickets 1-20 at 5%, tickets 21-50 at 7%, etc.)

**Discount Rules**:
- Discount range can be:
  - Global (same for all events)
  - Per-event (different ranges per event)
- Admin can modify ranges at any time
- Changes apply to **new codes only** (existing codes keep original discount)

#### Ambassador Team
**Pricing Logic**:
- Sets **fixed ticket price** (not percentage-based)
- Price must be between:
  - **Minimum price** (admin-defined, maximum discount)
  - **Current phase base price** (no markup allowed)
- Example: Phase 2 = 150 EUR, Min price = 100 EUR → Team can set 100-150 EUR

**No Commission**:
- Teams do not receive commissions
- All revenue goes to festival/artist break-even

**Minimum Price Rules**:
- Minimum price can be:
  - Global (same for all events)
  - Per-event (different minimums per event)
- Admin can modify minimum prices at any time
- Changes apply to **new codes only**

### Ambassador Quotas

#### Ticket Quota
- **Max Tickets**: Admin-set limit on total tickets ambassador can sell
- **Remaining**: `Max Tickets - Tickets Sold`
- **Enforcement**: System prevents code generation if quota exhausted
- **Modification**: Admin can increase/decrease quota at any time
- **Tracking**: Real-time quota usage visible to ambassador

#### Guest Ticket Quota
- **Max Guest Tickets**: Separate limit for 0 EUR tickets
- **Remaining**: `Max Guest Tickets - Guest Tickets Generated`
- **Enforcement**: System prevents guest code generation if quota exhausted
- **Modification**: Admin can modify quota at any time
- **Separate Flow**: Guest ticket generation is distinct from standard codes

---

## Code Generation and Validation

### Code Format
- **Pattern**: `PREFIX-NUMERIC_SEQUENCE`
- **PREFIX**: 2-5 characters, admin-assigned, fixed per ambassador
- **NUMERIC_SEQUENCE**: Random numeric string (length configurable, e.g., 12-16 digits)
- **Example**: `LOTUS-1111-2222-3333-4444`

### Code Rules

#### Single-Use
- Each code can be used **exactly once**
- Once redeemed, code status = `USED`
- Used codes cannot be reused or regenerated

#### 24-Hour Validity
- Codes expire **24 hours after generation**
- Expiration calculated from `generated_at` timestamp
- Expired codes cannot be redeemed
- Status transitions: `UNUSED` → `USED` or `EXPIRED`

#### Code States
- `UNUSED`: Generated, not yet redeemed, within validity period
- `USED`: Successfully redeemed for a ticket purchase
- `EXPIRED`: Not redeemed within 24 hours

#### Code Metadata
Each code stores:
- Ambassador ID (who generated it)
- Event ID
- Ticket type ID
- Final price (after discount or override)
- Discount percentage (Individual) or fixed price (Team)
- Generated timestamp
- Expiration timestamp
- Status (unused/used/expired)
- Redeemed timestamp (if used)
- Purchase ID (if used)

### Code Generation Flow

#### Standard Ticket Code
1. Ambassador selects event and ticket type
2. System shows current phase and base price
3. **Individual Ambassador**:
   - Ambassador selects discount % (within allowed range)
   - System calculates final price
4. **Ambassador Team**:
   - Ambassador enters fixed price (within min-max range)
   - System validates price
5. System checks:
   - Ticket quota available
   - Code generation rate limits (if any)
6. System generates unique code
7. Code stored with 24-hour expiration
8. Quota decremented (ticket quota, not guest quota)

#### Guest Ticket Code
1. Ambassador selects event and ticket type
2. System shows "0 EUR" price
3. System checks:
   - Guest ticket quota available
   - Code generation rate limits
4. System generates unique code (same format)
5. Code stored with 24-hour expiration and `GUEST` flag
6. Guest ticket quota decremented

### Code Validation (During Purchase)
1. User enters code at checkout
2. System validates:
   - Code exists
   - Code format is valid
   - Code status is `UNUSED`
   - Code is not expired (current time < expiration)
   - Code matches selected event (if event-specific)
3. If valid:
   - Apply discount/price override
   - Show final price
   - Allow purchase
4. If invalid:
   - Show error message
   - Prevent purchase with code

---

## Guest Ticket System

### Guest Ticket Characteristics
- **Price**: Always 0 EUR
- **Quota**: Separate from standard ticket quota
- **Generation**: Distinct flow from standard codes
- **Validity**: Same 24-hour expiration rule
- **Single-Use**: Same single-use rule
- **Format**: Same format as standard codes

### Guest Ticket Rules
1. **Quota Enforcement**: Cannot generate if guest quota exhausted
2. **No Revenue**: Guest tickets contribute 0 EUR to:
   - Artist break-even calculations
   - Festival revenue
   - Ambassador commissions
3. **Tracking**: Guest tickets are tracked separately for reporting
4. **Limits**: Admin can set different guest quotas per ambassador
5. **Usage**: Guest tickets count toward ambassador's total ticket count (for reporting)

---

## Commission Calculation

### Individual Ambassador Commissions

#### Tiered Structure
Commissions are calculated using **tiered percentages** based on total tickets sold:

**Example Configuration**:
- Tickets 1-20: 5% commission
- Tickets 21-50: 7% commission  
- Tickets 51+: 10% commission

#### Calculation Logic
1. **Total Tickets Sold**: Count all tickets sold by ambassador (across all events)
2. **Tier Assignment**: Determine which tier each ticket falls into
3. **Per-Ticket Commission**: 
   - Tickets 1-20: `Ticket Price × 5%`
   - Tickets 21-50: `Ticket Price × 7%`
   - Tickets 51+: `Ticket Price × 10%`
4. **Total Commission**: Sum of all per-ticket commissions

#### Example Calculation
Ambassador sold 60 tickets:
- Tickets 1-20 (20 tickets at 120 EUR avg): `20 × 120 × 0.05 = 120 EUR`
- Tickets 21-50 (30 tickets at 130 EUR avg): `30 × 130 × 0.07 = 273 EUR`
- Tickets 51-60 (10 tickets at 140 EUR avg): `10 × 140 × 0.10 = 140 EUR`
- **Total Commission**: 120 + 273 + 140 = **533 EUR**

#### Commission Rules
- Commission calculated on **final ticket price** (after discount)
- Guest tickets: **0 EUR commission** (0 EUR price)
- Commissions are **cumulative** (not retroactive tier changes)
- Admin can modify tier structure, but existing sales keep original commission rates
- Commission payable after ticket purchase (or after event, admin-configurable)

### Ambassador Team Commissions
- **No commissions**: Teams do not receive commissions
- All revenue goes to festival/artist break-even

---

## Revenue Tracking

### Revenue Components

#### Per Event
- **Total Revenue**: Sum of all ticket sales (excluding guest tickets)
- **Revenue by Phase**: Breakdown of revenue per phase (Phase 1/2/3)
- **Discounts Granted**: Total discount amount (base price - final price)
- **Guest Tickets**: Count and value (0 EUR)

#### Per Artist
- **Revenue Toward Break-Even**: Sum of ticket prices associated with artist
- **Break-Even Status**: Achieved or not, remaining amount
- **Post Break-Even Revenue**: Revenue beyond contracted fee

#### Per Ambassador
- **Total Revenue Generated**: Sum of ticket sales via ambassador codes
- **Revenue by Event**: Breakdown per event
- **Discounts Applied**: Total discount amount (Individual only)
- **Commissions Earned**: Total commission (Individual only)

### Revenue Calculation Rules
1. **Final Price**: Revenue = final ticket price (after discount/override)
2. **Guest Tickets**: Counted in ticket numbers but contribute 0 EUR to revenue
3. **Refunds**: Revenue adjusted if tickets refunded (admin-handled)
4. **Currency**: All calculations in base currency (EUR, admin-configurable)

---

## Quota Management

### Ticket Quota

#### Rules
- **Max Tickets**: Admin-set limit per ambassador
- **Enforcement**: Prevents code generation when quota exhausted
- **Modification**: Admin can change quota at any time
- **Tracking**: Real-time remaining quota = `Max - Sold`

#### Quota Exhaustion
- When quota = 0, ambassador cannot generate new codes
- Existing unused codes remain valid until expiration
- Admin can increase quota to allow more sales

### Guest Ticket Quota

#### Rules
- **Max Guest Tickets**: Separate limit per ambassador
- **Enforcement**: Prevents guest code generation when quota exhausted
- **Modification**: Admin can change quota at any time
- **Tracking**: Real-time remaining quota = `Max - Generated`

#### Quota Independence
- Guest quota is **independent** of ticket quota
- Exhausting ticket quota does not affect guest quota
- Exhausting guest quota does not affect ticket quota

### Quota Modification Impact
- **Increase**: Immediately allows more code generation
- **Decrease**: Does not affect existing codes or already-sold tickets
- **History**: System tracks quota changes for audit purposes

---

## Edge Cases and Validation

### Code Expiration Edge Cases
1. **Expiration During Checkout**: If code expires while user is in checkout, show error and prevent purchase
2. **Time Zone Handling**: All timestamps in UTC, expiration calculated consistently
3. **Clock Skew**: Account for potential server time differences

### Phase Transition Edge Cases
1. **Phase Change During Purchase**: Lock phase at code generation time, not redemption time
2. **Phase Change with Unused Codes**: Codes keep original phase price/discount
3. **Manual Phase Override**: Admin can force phase change, affecting new codes only

### Quota Edge Cases
1. **Concurrent Code Generation**: Use database transactions to prevent race conditions
2. **Quota Decrease Below Sold**: Allow decrease, but prevent new code generation until quota increased
3. **Negative Quota**: Prevent quota from going negative

### Price Validation Edge Cases
1. **Individual Ambassador Discount**: Validate discount % is within allowed range
2. **Team Price**: Validate price is between minimum and phase base price
3. **Price Changes**: Existing codes keep original prices
4. **Zero/Negative Prices**: Only guest tickets can be 0 EUR, prevent negative prices

### Break-Even Edge Cases
1. **Multiple Events per Artist**: Aggregate revenue across all events
2. **Refunds After Break-Even**: Handle refund impact on break-even status (admin decision)
3. **Fee Modification**: If admin changes contracted fee, recalculate break-even status
4. **Zero Fee**: If fee = 0, artist immediately achieves break-even

### Commission Edge Cases
1. **Tier Structure Changes**: Existing sales keep original commission rates
2. **Refunds**: Commission recalculated if ticket refunded
3. **Guest Tickets**: 0 EUR commission (already handled)
4. **Tier Boundary**: Tickets exactly at tier boundary use higher tier rate

### Data Consistency
1. **Transaction Integrity**: All code generation and ticket purchases use database transactions
2. **Audit Trail**: Log all quota changes, price modifications, and admin actions
3. **Data Validation**: Validate all inputs (prices, percentages, quotas) before saving
4. **Error Handling**: Graceful error handling with user-friendly messages

---

**Document Version**: 1.0  
**Last Updated**: 2024  
**Status**: Design Phase
