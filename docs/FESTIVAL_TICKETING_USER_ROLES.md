# User Roles and Permissions

## Role Hierarchy

The platform supports four primary user roles with distinct permissions and access levels:

```
Public Visitor (Unauthenticated)
    ↓
Public Visitor (Authenticated)
    ↓
Ambassador (Individual or Team)
    ↓
Artist / Artist Team
    ↓
Organizer / Admin
```

## 1. Public Visitor (Unauthenticated)

### Access Level
- **Read-only** access to public content
- **No authentication** required

### Permissions
- ✅ View festival list and details
- ✅ View artist lineups
- ✅ View ticket phases and base prices
- ✅ View FAQ and about pages
- ✅ Access contact/support forms
- ❌ Cannot purchase tickets
- ❌ Cannot generate codes
- ❌ Cannot view personal data

### Use Cases
- Browsing festivals and events
- Researching ticket options
- Understanding the platform and ambassador system

---

## 2. Public Visitor (Authenticated / Ticket Buyer)

### Access Level
- **Authenticated user** with personal account
- **Customer role** for ticket purchases

### Permissions
- ✅ All unauthenticated visitor permissions
- ✅ Purchase tickets (with or without codes)
- ✅ Redeem ambassador codes
- ✅ Redeem guest ticket codes
- ✅ View "My Tickets" dashboard
- ✅ Download ticket confirmations
- ✅ Update personal profile
- ✅ View purchase history
- ❌ Cannot generate codes
- ❌ Cannot access ambassador/artist/admin areas

### Profile Data
- Email address
- Full name
- Phone number (optional)
- Billing address
- Payment methods
- Purchase history

---

## 3. Ambassador

### Access Level
- **Authenticated user** with ambassador privileges
- **Two subtypes**: Individual Ambassador or Ambassador Team
- **Type is admin-configured** and affects pricing logic

### Common Permissions (Both Types)
- ✅ Access Ambassador Dashboard
- ✅ Generate standard ticket codes
- ✅ Generate guest ticket codes
- ✅ View code list and status
- ✅ View sales statistics
- ✅ View quota usage (tickets and guest tickets)
- ✅ View personal profile and settings
- ✅ View PREFIX assignment
- ❌ Cannot modify own quotas or pricing rules
- ❌ Cannot access other ambassadors' data
- ❌ Cannot access artist or admin areas

### Individual Ambassador Specific
- ✅ Set discount percentage (within admin-defined range)
- ✅ View commission tiers and calculations
- ✅ View commission earnings
- ✅ View discount usage statistics

### Ambassador Team Specific
- ✅ Set fixed ticket price (between minimum and phase base price)
- ✅ View minimum price constraints
- ✅ View team-level sales data

### Profile Data
- Ambassador type (Individual/Team)
- Unique PREFIX (2-5 characters, admin-assigned)
- Ticket quota (max sellable tickets)
- Guest ticket quota (max free tickets)
- Discount rules (Individual) or minimum price (Team)
- Commission tiers (Individual only)
- Payout/payment details
- Notification preferences

### Code Generation Rules
- Codes are **single-use only**
- Codes expire after **24 hours** from generation
- Codes follow format: `PREFIX-NUMERIC_SEQUENCE`
- Each code is linked to:
  - Event
  - Ticket type
  - Final price (after discount or override)
  - Ambassador who generated it

---

## 4. Artist / Artist Team

### Access Level
- **Authenticated user** with artist privileges
- **May represent individual or team** of artists

### Permissions
- ✅ Access Artist Dashboard
- ✅ View tickets sold (associated with artist)
- ✅ View revenue toward break-even
- ✅ View break-even progress (% and remaining amount)
- ✅ View revenue after break-even
- ✅ View events and performances list
- ✅ View event-specific sales data
- ✅ Download reports (CSV/PDF)
- ✅ View contracted fee/break-even target (read-only)
- ✅ Update artist profile information
- ❌ Cannot modify break-even targets (admin-only)
- ❌ Cannot access ambassador or admin areas
- ❌ Cannot generate codes (unless configured by admin)

### Profile Data
- Artist/team name
- Contact information
- Contracted fee / break-even target (view-only)
- Associated events
- Performance dates and times
- Break-even status and date achieved
- Revenue and ticket sales history

### Optional Features (Admin-Configurable)
- Code generation capability (if artists can also act as ambassadors)
- Custom reporting templates
- Email notification preferences

---

## 5. Organizer / Admin

### Access Level
- **Highest privilege level**
- **Full system access** and configuration

### Permissions

#### Dashboard & Overview
- ✅ View admin dashboard
- ✅ View all festivals overview
- ✅ View tickets sold and revenue by event/phase
- ✅ View ambassador performance summaries
- ✅ View artist break-even overview
- ✅ View system-wide analytics

#### Festival & Event Management
- ✅ Create, edit, delete festivals
- ✅ Create, edit, delete events
- ✅ Configure event basic info (name, date, location)
- ✅ Configure ticket phases and base prices
- ✅ Configure ticket categories and capacities
- ✅ Manage artist lineups
- ✅ Set artist fees and break-even targets

#### Ticket & Pricing Management
- ✅ Configure global ticket phases
- ✅ Set base prices per phase per event
- ✅ Configure global discount rules
- ✅ Manage ticket categories and types
- ✅ Set capacity limits

#### Ambassador Management
- ✅ Create, edit, delete ambassadors
- ✅ Set ambassador type (Individual or Team)
- ✅ Assign unique PREFIX (2-5 characters)
- ✅ Configure Individual Ambassador settings:
  - Allowed discount ranges (% vs current phase price)
  - Commission tiers (ranges and percentages)
- ✅ Configure Ambassador Team settings:
  - Minimum ticket price
  - Max number of tickets
  - Guest ticket quota
- ✅ Modify quotas and rules at any time
- ✅ View ambassador detail pages
- ✅ View ambassador code generation history
- ✅ View ambassador sales and revenue data

#### Code Management
- ✅ View all codes globally
- ✅ Filter codes by:
  - Ambassador
  - Type (Standard/Guest)
  - Event
  - Status (unused/used/expired)
  - Date range
  - Discount level
- ✅ View code usage statistics
- ✅ Manually expire codes (if needed)

#### Artist Management
- ✅ Create, edit, delete artists
- ✅ Set contracted fees and break-even targets
- ✅ Associate artists with events
- ✅ View artist detail pages
- ✅ View tickets sold via associated codes
- ✅ View break-even status and dates
- ✅ View profit generated after break-even

#### Reporting & Analytics
- ✅ Generate event-level reports
- ✅ Generate ambassador-level reports:
  - Individual vs Team performance
  - Discount behavior analysis
  - Commission costs (Individual)
- ✅ Generate artist-level reports
- ✅ Generate break-even analysis
- ✅ Generate financial reports:
  - Revenue breakdown
  - Discounts granted
  - Guest ticket usage
  - Ambassador commissions
- ✅ Export reports (CSV, PDF)

#### System Settings
- ✅ Manage user roles and permissions
- ✅ Configure default code validity (24 hours)
- ✅ Configure payment and currency settings
- ✅ Manage email/notification templates
- ✅ Configure system-wide defaults
- ✅ Manage authentication providers
- ✅ View system logs and audit trails

### Profile Data
- Admin account information
- System preferences
- Notification settings
- Access logs

---

## Permission Matrix

| Feature | Public (Unauth) | Public (Auth) | Ambassador | Artist | Admin |
|---------|----------------|---------------|------------|--------|-------|
| View festivals | ✅ | ✅ | ✅ | ✅ | ✅ |
| Purchase tickets | ❌ | ✅ | ✅* | ✅* | ✅ |
| Generate codes | ❌ | ❌ | ✅ | ❌** | ✅ |
| View own sales | ❌ | ❌ | ✅ | ❌ | ✅ |
| View break-even | ❌ | ❌ | ❌ | ✅ | ✅ |
| Manage events | ❌ | ❌ | ❌ | ❌ | ✅ |
| Manage ambassadors | ❌ | ❌ | ❌ | ❌ | ✅ |
| Manage artists | ❌ | ❌ | ❌ | ❌ | ✅ |
| System settings | ❌ | ❌ | ❌ | ❌ | ✅ |

\* Ambassadors and Artists can purchase tickets like regular users, but typically use their own codes  
\** Artists may have code generation if configured by admin

---

## Role Assignment

### User Registration Flow
1. **Public Registration**: Users register as ticket buyers by default
2. **Ambassador Assignment**: Admin creates ambassador accounts or upgrades existing users
3. **Artist Assignment**: Admin creates artist accounts or associates existing users
4. **Admin Assignment**: System administrators assign admin roles (typically manual)

### Multi-Role Support
- A user can have **multiple roles** (e.g., Ambassador + Artist)
- Role-based UI shows relevant sections based on assigned roles
- Permissions are **additive** (user gets all permissions from all assigned roles)

---

## Authentication & Authorization

### Authentication Methods
- Email/password
- OAuth (Google, Facebook) - optional
- Social login - optional

### Authorization Checks
- **Route-level**: Middleware checks user role before accessing routes
- **API-level**: Each endpoint validates user permissions
- **Data-level**: Users can only access their own data (except admins)
- **Action-level**: UI elements hidden/shown based on permissions

### Security Considerations
- Role changes require admin approval
- Sensitive operations (quota changes, pricing rules) logged
- Audit trail for all admin actions
- Session management and token expiration
- Rate limiting on code generation and ticket purchases

---

**Document Version**: 1.0  
**Last Updated**: 2024  
**Status**: Design Phase
