# Domain Model and Entities

## Entity Relationship Overview

```
Festival
  ├── Event (1:N)
  │     ├── TicketPhase (1:3)
  │     ├── TicketType (1:N)
  │     └── EventArtist (N:M)
  │
  └── Artist (N:M via EventArtist)
        └── BreakEvenTarget (1:1)

User
  ├── PublicUser (1:1)
  ├── Ambassador (1:1, optional)
  │     ├── AmbassadorConfig (1:1)
  │     └── AmbassadorCode (1:N)
  │           └── TicketPurchase (1:1, optional)
  │
  ├── Artist (1:1, optional)
  │     └── BreakEvenTarget (1:1)
  │
  └── Admin (1:1, optional)

TicketPurchase
  ├── Ticket (1:N)
  ├── Payment (1:1)
  └── AmbassadorCode (N:1, optional)
```

---

## Core Entities

### 1. User

**Purpose**: Base authentication and user management entity.

**Attributes**:
- `id` (UUID, Primary Key)
- `email` (String, Unique, Required)
- `password_hash` (String, Required)
- `full_name` (String, Required)
- `phone` (String, Optional)
- `roles` (Array[String], Default: ["public"])
  - Possible values: `["public", "ambassador", "artist", "admin"]`
- `is_active` (Boolean, Default: true)
- `email_verified` (Boolean, Default: false)
- `created_at` (Timestamp)
- `updated_at` (Timestamp)
- `last_login` (Timestamp, Optional)

**Relationships**:
- One-to-One: `PublicUser`, `Ambassador`, `Artist`, `Admin` (optional, role-based)
- One-to-Many: `TicketPurchase` (as buyer)

**Indexes**:
- `email` (unique)
- `roles` (for role-based queries)

---

### 2. Festival

**Purpose**: Top-level container for multiple events.

**Attributes**:
- `id` (UUID, Primary Key)
- `name` (String, Required)
- `description` (Text, Optional)
- `start_date` (Date, Optional)
- `end_date` (Date, Optional)
- `location` (String, Optional)
- `is_active` (Boolean, Default: true)
- `created_at` (Timestamp)
- `updated_at` (Timestamp)
- `created_by` (UUID, Foreign Key → User.id, Admin)

**Relationships**:
- One-to-Many: `Event`

**Indexes**:
- `is_active`
- `start_date`, `end_date`

---

### 3. Event

**Purpose**: Individual festival event with its own pricing and lineup.

**Attributes**:
- `id` (UUID, Primary Key)
- `festival_id` (UUID, Foreign Key → Festival.id, Required)
- `name` (String, Required)
- `description` (Text, Optional)
- `date` (DateTime, Required)
- `location` (String, Optional)
- `current_phase` (Integer, 1-3, Default: 1)
- `phase_1_start` (DateTime, Required)
- `phase_1_end` (DateTime, Required)
- `phase_2_start` (DateTime, Required)
- `phase_2_end` (DateTime, Required)
- `phase_3_start` (DateTime, Required)
- `phase_3_end` (DateTime, Required)
- `is_active` (Boolean, Default: true)
- `created_at` (Timestamp)
- `updated_at` (Timestamp)
- `created_by` (UUID, Foreign Key → User.id, Admin)

**Relationships**:
- Many-to-One: `Festival`
- One-to-Three: `TicketPhase` (Phase 1, 2, 3)
- One-to-Many: `TicketType`
- Many-to-Many: `Artist` (via `EventArtist`)
- One-to-Many: `TicketPurchase`

**Indexes**:
- `festival_id`
- `date`
- `current_phase`
- `is_active`

**Computed Properties**:
- `current_phase_base_price`: Returns base price of current phase
- `is_sold_out`: Checks if all ticket types are sold out

---

### 4. TicketPhase

**Purpose**: Defines base prices for each of the 3 phases per event.

**Attributes**:
- `id` (UUID, Primary Key)
- `event_id` (UUID, Foreign Key → Event.id, Required)
- `phase_number` (Integer, 1-3, Required)
- `base_price` (Decimal, Required, e.g., 120.00)
- `start_date` (DateTime, Required)
- `end_date` (DateTime, Required)
- `created_at` (Timestamp)
- `updated_at` (Timestamp)

**Relationships**:
- Many-to-One: `Event`

**Indexes**:
- `event_id`, `phase_number` (unique composite)
- `start_date`, `end_date`

**Constraints**:
- `phase_number` must be 1, 2, or 3
- Phases must not overlap in time
- `base_price` must be > 0

---

### 5. TicketType

**Purpose**: Different ticket categories (e.g., General Admission, VIP).

**Attributes**:
- `id` (UUID, Primary Key)
- `event_id` (UUID, Foreign Key → Event.id, Required)
- `name` (String, Required, e.g., "General Admission")
- `description` (Text, Optional)
- `capacity` (Integer, Required)
- `sold_count` (Integer, Default: 0)
- `is_active` (Boolean, Default: true)
- `created_at` (Timestamp)
- `updated_at` (Timestamp)

**Relationships**:
- Many-to-One: `Event`
- One-to-Many: `Ticket` (via purchases)

**Indexes**:
- `event_id`
- `is_active`

**Computed Properties**:
- `remaining_capacity`: `capacity - sold_count`
- `is_sold_out`: `sold_count >= capacity`

---

### 6. Artist

**Purpose**: Represents an artist or artist team performing at events.

**Attributes**:
- `id` (UUID, Primary Key)
- `user_id` (UUID, Foreign Key → User.id, Optional, if artist has account)
- `name` (String, Required)
- `description` (Text, Optional)
- `contact_email` (String, Optional)
- `contact_phone` (String, Optional)
- `is_team` (Boolean, Default: false)
- `created_at` (Timestamp)
- `updated_at` (Timestamp)
- `created_by` (UUID, Foreign Key → User.id, Admin)

**Relationships**:
- One-to-One: `User` (optional)
- One-to-One: `BreakEvenTarget` (per event, via `EventArtist`)
- Many-to-Many: `Event` (via `EventArtist`)

**Indexes**:
- `user_id` (unique, if present)
- `name`

---

### 7. EventArtist

**Purpose**: Junction table linking events to artists with break-even targets.

**Attributes**:
- `id` (UUID, Primary Key)
- `event_id` (UUID, Foreign Key → Event.id, Required)
- `artist_id` (UUID, Foreign Key → Artist.id, Required)
- `contracted_fee` (Decimal, Required, break-even target)
- `revenue_toward_break_even` (Decimal, Default: 0.00)
- `break_even_achieved` (Boolean, Default: false)
- `break_even_achieved_at` (Timestamp, Optional)
- `performance_time` (DateTime, Optional)
- `created_at` (Timestamp)
- `updated_at` (Timestamp)

**Relationships**:
- Many-to-One: `Event`
- Many-to-One: `Artist`

**Indexes**:
- `event_id`, `artist_id` (unique composite)
- `break_even_achieved`

**Computed Properties**:
- `remaining_amount`: `max(0, contracted_fee - revenue_toward_break_even)`
- `post_break_even_revenue`: `max(0, revenue_toward_break_even - contracted_fee)`
- `break_even_percentage`: `(revenue_toward_break_even / contracted_fee) * 100`

---

### 8. Ambassador

**Purpose**: Represents an ambassador (Individual or Team) who can generate codes.

**Attributes**:
- `id` (UUID, Primary Key)
- `user_id` (UUID, Foreign Key → User.id, Required, Unique)
- `type` (Enum: "individual" | "team", Required)
- `prefix` (String, 2-5 chars, Required, Unique)
- `ticket_quota` (Integer, Default: 0)
- `tickets_sold` (Integer, Default: 0)
- `guest_ticket_quota` (Integer, Default: 0)
- `guest_tickets_generated` (Integer, Default: 0)
- `is_active` (Boolean, Default: true)
- `created_at` (Timestamp)
- `updated_at` (Timestamp)
- `created_by` (UUID, Foreign Key → User.id, Admin)

**Relationships**:
- One-to-One: `User`
- One-to-One: `AmbassadorConfig`
- One-to-Many: `AmbassadorCode`
- Many-to-One: `Artist` (optional, if ambassador is linked to artist)

**Indexes**:
- `user_id` (unique)
- `prefix` (unique)
- `type`
- `is_active`

**Computed Properties**:
- `remaining_ticket_quota`: `ticket_quota - tickets_sold`
- `remaining_guest_quota`: `guest_ticket_quota - guest_tickets_generated`

---

### 9. AmbassadorConfig

**Purpose**: Stores type-specific configuration for ambassadors.

**Attributes**:
- `id` (UUID, Primary Key)
- `ambassador_id` (UUID, Foreign Key → Ambassador.id, Required, Unique)

**For Individual Ambassadors**:
- `min_discount_percent` (Decimal, Default: 0.00)
- `max_discount_percent` (Decimal, Default: 0.00)
- `discount_scope` (Enum: "global" | "per_event", Default: "global")
- `commission_tier_1_min` (Integer, Optional, e.g., 1)
- `commission_tier_1_max` (Integer, Optional, e.g., 20)
- `commission_tier_1_percent` (Decimal, Optional, e.g., 5.00)
- `commission_tier_2_min` (Integer, Optional)
- `commission_tier_2_max` (Integer, Optional)
- `commission_tier_2_percent` (Decimal, Optional)
- `commission_tier_3_min` (Integer, Optional)
- `commission_tier_3_max` (Integer, Optional)
- `commission_tier_3_percent` (Decimal, Optional)
- (Support for additional tiers as needed)

**For Ambassador Teams**:
- `min_ticket_price` (Decimal, Optional)
- `min_price_scope` (Enum: "global" | "per_event", Default: "global")

**Relationships**:
- One-to-One: `Ambassador`

**Indexes**:
- `ambassador_id` (unique)

---

### 10. AmbassadorCode

**Purpose**: Represents a single-use, time-limited discount code.

**Attributes**:
- `id` (UUID, Primary Key)
- `code` (String, Required, Unique, Format: "PREFIX-NUMERIC")
- `ambassador_id` (UUID, Foreign Key → Ambassador.id, Required)
- `event_id` (UUID, Foreign Key → Event.id, Required)
- `ticket_type_id` (UUID, Foreign Key → TicketType.id, Required)
- `code_type` (Enum: "standard" | "guest", Required)
- `status` (Enum: "unused" | "used" | "expired", Default: "unused")
- `base_price` (Decimal, Required, phase price at generation time)
- `discount_percent` (Decimal, Optional, for Individual Ambassador)
- `final_price` (Decimal, Required)
- `generated_at` (Timestamp, Required)
- `expires_at` (Timestamp, Required, generated_at + 24 hours)
- `used_at` (Timestamp, Optional)
- `purchase_id` (UUID, Foreign Key → TicketPurchase.id, Optional)

**Relationships**:
- Many-to-One: `Ambassador`
- Many-to-One: `Event`
- Many-to-One: `TicketType`
- One-to-One: `TicketPurchase` (optional, if used)

**Indexes**:
- `code` (unique)
- `ambassador_id`
- `event_id`
- `status`
- `expires_at`
- `generated_at`

**Constraints**:
- `final_price` must be >= 0
- `expires_at` = `generated_at` + 24 hours
- `code_type = "guest"` implies `final_price = 0`

---

### 11. TicketPurchase

**Purpose**: Represents a completed ticket purchase transaction.

**Attributes**:
- `id` (UUID, Primary Key)
- `user_id` (UUID, Foreign Key → User.id, Required, buyer)
- `event_id` (UUID, Foreign Key → Event.id, Required)
- `ticket_type_id` (UUID, Foreign Key → TicketType.id, Required)
- `ambassador_code_id` (UUID, Foreign Key → AmbassadorCode.id, Optional)
- `phase_number` (Integer, 1-3, Required)
- `base_price` (Decimal, Required)
- `discount_amount` (Decimal, Default: 0.00)
- `final_price` (Decimal, Required)
- `quantity` (Integer, Default: 1)
- `status` (Enum: "pending" | "completed" | "cancelled" | "refunded", Default: "pending")
- `purchased_at` (Timestamp, Required)
- `created_at` (Timestamp)
- `updated_at` (Timestamp)

**Relationships**:
- Many-to-One: `User` (buyer)
- Many-to-One: `Event`
- Many-to-One: `TicketType`
- Many-to-One: `AmbassadorCode` (optional)
- One-to-Many: `Ticket` (individual ticket instances)
- One-to-One: `Payment`

**Indexes**:
- `user_id`
- `event_id`
- `ambassador_code_id`
- `status`
- `purchased_at`

**Computed Properties**:
- `total_amount`: `final_price * quantity`

---

### 12. Ticket

**Purpose**: Individual ticket instance within a purchase.

**Attributes**:
- `id` (UUID, Primary Key)
- `purchase_id` (UUID, Foreign Key → TicketPurchase.id, Required)
- `ticket_number` (String, Required, Unique, e.g., "EVT-2024-001234")
- `status` (Enum: "valid" | "used" | "cancelled" | "refunded", Default: "valid")
- `used_at` (Timestamp, Optional)
- `created_at` (Timestamp)

**Relationships**:
- Many-to-One: `TicketPurchase`

**Indexes**:
- `ticket_number` (unique)
- `purchase_id`
- `status`

---

### 13. Payment

**Purpose**: Payment transaction record.

**Attributes**:
- `id` (UUID, Primary Key)
- `purchase_id` (UUID, Foreign Key → TicketPurchase.id, Required, Unique)
- `payment_method` (Enum: "card" | "bank_transfer" | "other", Required)
- `amount` (Decimal, Required)
- `currency` (String, Default: "EUR")
- `status` (Enum: "pending" | "completed" | "failed" | "refunded", Default: "pending")
- `transaction_id` (String, Optional, external payment provider ID)
- `paid_at` (Timestamp, Optional)
- `created_at` (Timestamp)
- `updated_at` (Timestamp)

**Relationships**:
- One-to-One: `TicketPurchase`

**Indexes**:
- `purchase_id` (unique)
- `status`
- `transaction_id`

---

### 14. Admin

**Purpose**: Admin-specific profile and settings (optional extension of User).

**Attributes**:
- `id` (UUID, Primary Key)
- `user_id` (UUID, Foreign Key → User.id, Required, Unique)
- `permissions` (Array[String], Optional, for fine-grained permissions)
- `last_activity` (Timestamp, Optional)
- `created_at` (Timestamp)
- `updated_at` (Timestamp)

**Relationships**:
- One-to-One: `User`

**Indexes**:
- `user_id` (unique)

---

## Data Flow Examples

### Code Generation Flow
1. `Ambassador` requests code generation
2. System checks `Ambassador.ticket_quota` vs `tickets_sold`
3. System reads `Event.current_phase` and `TicketPhase.base_price`
4. System reads `AmbassadorConfig` for discount/price rules
5. System creates `AmbassadorCode` with calculated `final_price`
6. System updates `Ambassador.tickets_sold` (or `guest_tickets_generated`)

### Ticket Purchase Flow
1. `User` selects event and ticket type
2. User enters `AmbassadorCode.code`
3. System validates code (status, expiration, event match)
4. System creates `TicketPurchase` with `final_price`
5. System creates `Payment` record
6. System creates `Ticket` instances (quantity)
7. System updates `AmbassadorCode.status = "used"`
8. System updates `EventArtist.revenue_toward_break_even`
9. System updates `TicketType.sold_count`
10. System updates `Ambassador.tickets_sold`

### Break-Even Calculation
1. On ticket purchase, system identifies associated `Artist` (via code or event)
2. System updates `EventArtist.revenue_toward_break_even`
3. System checks: `revenue_toward_break_even >= contracted_fee`
4. If true and not already achieved:
   - Set `EventArtist.break_even_achieved = true`
   - Set `EventArtist.break_even_achieved_at = now()`

---

## Database Considerations

### Recommended Database
- **PostgreSQL** (recommended for relational integrity, transactions, complex queries)
- Alternative: **MySQL/MariaDB**

### Key Constraints
- Foreign key constraints for referential integrity
- Unique constraints on codes, prefixes, emails
- Check constraints for price ranges, percentages
- Transaction support for quota and code generation

### Indexing Strategy
- Index all foreign keys
- Index frequently queried fields (status, dates, codes)
- Composite indexes for common query patterns
- Consider partial indexes for active records

### Migration Strategy
- Use database migrations (e.g., Alembic for Python, Flyway for Java)
- Version control all schema changes
- Support rollback capabilities

---

**Document Version**: 1.0  
**Last Updated**: 2024  
**Status**: Design Phase
