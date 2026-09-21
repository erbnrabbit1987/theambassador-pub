# API Design and Data Flow

## API Architecture Overview

### Design Principles
- **RESTful API** with clear resource-based endpoints
- **Stateless** authentication using JWT tokens
- **Versioned API** (e.g., `/api/v1/`)
- **Consistent error handling** with standardized error responses
- **Pagination** for list endpoints
- **Filtering and sorting** support where applicable

### Base URL Structure
```
/api/v1/
├── auth/              # Authentication endpoints
├── festivals/         # Festival management
├── events/            # Event management
├── tickets/           # Ticket purchasing
├── codes/             # Ambassador code operations
├── ambassadors/       # Ambassador management
├── artists/           # Artist management
├── purchases/         # Purchase history
├── admin/             # Admin-only endpoints
└── public/            # Public endpoints (no auth)
```

---

## Authentication & Authorization

### Authentication Endpoints

#### POST `/api/v1/auth/register`
Register a new user (public role by default).

**Request Body**:
```json
{
  "email": "user@example.com",
  "password": "securePassword123",
  "full_name": "John Doe",
  "phone": "+1234567890"
}
```

**Response** (201 Created):
```json
{
  "user": {
    "id": "uuid",
    "email": "user@example.com",
    "full_name": "John Doe",
    "roles": ["public"]
  },
  "token": "jwt_token_here"
}
```

#### POST `/api/v1/auth/login`
Authenticate user and receive JWT token.

**Request Body**:
```json
{
  "email": "user@example.com",
  "password": "securePassword123"
}
```

**Response** (200 OK):
```json
{
  "user": {
    "id": "uuid",
    "email": "user@example.com",
    "full_name": "John Doe",
    "roles": ["public", "ambassador"]
  },
  "token": "jwt_token_here",
  "expires_in": 3600
}
```

#### POST `/api/v1/auth/refresh`
Refresh JWT token.

**Headers**: `Authorization: Bearer <token>`

**Response** (200 OK):
```json
{
  "token": "new_jwt_token",
  "expires_in": 3600
}
```

#### POST `/api/v1/auth/logout`
Invalidate current token.

**Headers**: `Authorization: Bearer <token>`

**Response** (200 OK):
```json
{
  "message": "Logged out successfully"
}
```

---

## Public Endpoints (No Authentication)

### GET `/api/v1/public/festivals`
Get list of active festivals.

**Query Parameters**:
- `page` (integer, default: 1)
- `limit` (integer, default: 20)
- `is_active` (boolean, optional)

**Response** (200 OK):
```json
{
  "festivals": [
    {
      "id": "uuid",
      "name": "Summer Music Festival",
      "description": "...",
      "start_date": "2024-07-01",
      "end_date": "2024-07-05",
      "location": "Budapest",
      "is_active": true
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 5,
    "pages": 1
  }
}
```

### GET `/api/v1/public/festivals/{festival_id}`
Get festival details.

**Response** (200 OK):
```json
{
  "id": "uuid",
  "name": "Summer Music Festival",
  "description": "...",
  "start_date": "2024-07-01",
  "end_date": "2024-07-05",
  "location": "Budapest",
  "events": [
    {
      "id": "uuid",
      "name": "Opening Night",
      "date": "2024-07-01T20:00:00Z",
      "current_phase": 1,
      "phase_1_price": 120.00,
      "phase_2_price": 150.00,
      "phase_3_price": 170.00
    }
  ]
}
```

### GET `/api/v1/public/events/{event_id}`
Get event details with lineup and ticket information.

**Response** (200 OK):
```json
{
  "id": "uuid",
  "festival_id": "uuid",
  "name": "Opening Night",
  "description": "...",
  "date": "2024-07-01T20:00:00Z",
  "location": "Main Stage",
  "current_phase": 1,
  "current_phase_base_price": 120.00,
  "phases": [
    {
      "phase_number": 1,
      "base_price": 120.00,
      "start_date": "2024-01-01T00:00:00Z",
      "end_date": "2024-03-31T23:59:59Z"
    },
    {
      "phase_number": 2,
      "base_price": 150.00,
      "start_date": "2024-04-01T00:00:00Z",
      "end_date": "2024-05-31T23:59:59Z"
    },
    {
      "phase_number": 3,
      "base_price": 170.00,
      "start_date": "2024-06-01T00:00:00Z",
      "end_date": "2024-07-01T23:59:59Z"
    }
  ],
  "artists": [
    {
      "id": "uuid",
      "name": "Artist Name",
      "performance_time": "2024-07-01T21:00:00Z"
    }
  ],
  "ticket_types": [
    {
      "id": "uuid",
      "name": "General Admission",
      "capacity": 1000,
      "sold_count": 250,
      "remaining_capacity": 750
    }
  ]
}
```

### POST `/api/v1/public/codes/validate`
Validate an ambassador code (public endpoint, no auth required for validation).

**Request Body**:
```json
{
  "code": "LOTUS-1111-2222-3333-4444",
  "event_id": "uuid",
  "ticket_type_id": "uuid"
}
```

**Response** (200 OK):
```json
{
  "valid": true,
  "code": {
    "id": "uuid",
    "code": "LOTUS-1111-2222-3333-4444",
    "final_price": 96.00,
    "base_price": 120.00,
    "discount_percent": 20.00,
    "expires_at": "2024-01-15T12:00:00Z",
    "code_type": "standard"
  }
}
```

**Response** (400 Bad Request - Invalid):
```json
{
  "valid": false,
  "error": "CODE_EXPIRED",
  "message": "This code has expired"
}
```

---

## Ticket Purchase Endpoints

### POST `/api/v1/tickets/purchase`
Purchase tickets (authenticated users).

**Headers**: `Authorization: Bearer <token>`

**Request Body**:
```json
{
  "event_id": "uuid",
  "ticket_type_id": "uuid",
  "quantity": 2,
  "code": "LOTUS-1111-2222-3333-4444",
  "payment_method": "card",
  "billing_address": {
    "street": "123 Main St",
    "city": "Budapest",
    "postal_code": "1011",
    "country": "Hungary"
  }
}
```

**Response** (201 Created):
```json
{
  "purchase": {
    "id": "uuid",
    "event_id": "uuid",
    "ticket_type_id": "uuid",
    "quantity": 2,
    "base_price": 120.00,
    "discount_amount": 48.00,
    "final_price": 96.00,
    "total_amount": 192.00,
    "status": "completed",
    "purchased_at": "2024-01-15T10:30:00Z"
  },
  "tickets": [
    {
      "id": "uuid",
      "ticket_number": "EVT-2024-001234",
      "status": "valid"
    },
    {
      "id": "uuid",
      "ticket_number": "EVT-2024-001235",
      "status": "valid"
    }
  ],
  "payment": {
    "id": "uuid",
    "status": "completed",
    "transaction_id": "txn_123456"
  }
}
```

### GET `/api/v1/purchases`
Get user's purchase history.

**Headers**: `Authorization: Bearer <token>`

**Query Parameters**:
- `page` (integer, default: 1)
- `limit` (integer, default: 20)
- `event_id` (uuid, optional)
- `status` (string, optional: "pending" | "completed" | "cancelled" | "refunded")

**Response** (200 OK):
```json
{
  "purchases": [
    {
      "id": "uuid",
      "event": {
        "id": "uuid",
        "name": "Opening Night",
        "date": "2024-07-01T20:00:00Z"
      },
      "ticket_type": {
        "id": "uuid",
        "name": "General Admission"
      },
      "quantity": 2,
      "final_price": 96.00,
      "total_amount": 192.00,
      "status": "completed",
      "purchased_at": "2024-01-15T10:30:00Z",
      "tickets": [
        {
          "ticket_number": "EVT-2024-001234",
          "status": "valid"
        }
      ]
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 5,
    "pages": 1
  }
}
```

### GET `/api/v1/purchases/{purchase_id}`
Get purchase details.

**Headers**: `Authorization: Bearer <token>`

**Response** (200 OK):
```json
{
  "id": "uuid",
  "event": { ... },
  "ticket_type": { ... },
  "quantity": 2,
  "base_price": 120.00,
  "discount_amount": 48.00,
  "final_price": 96.00,
  "total_amount": 192.00,
  "code_used": {
    "code": "LOTUS-1111-2222-3333-4444",
    "discount_percent": 20.00
  },
  "status": "completed",
  "purchased_at": "2024-01-15T10:30:00Z",
  "tickets": [ ... ],
  "payment": { ... }
}
```

---

## Ambassador Endpoints

### GET `/api/v1/ambassadors/me`
Get current ambassador's profile and dashboard data.

**Headers**: `Authorization: Bearer <token>`  
**Required Role**: `ambassador`

**Response** (200 OK):
```json
{
  "ambassador": {
    "id": "uuid",
    "type": "individual",
    "prefix": "LOTUS",
    "ticket_quota": 100,
    "tickets_sold": 45,
    "remaining_ticket_quota": 55,
    "guest_ticket_quota": 10,
    "guest_tickets_generated": 3,
    "remaining_guest_quota": 7
  },
  "config": {
    "min_discount_percent": 0.00,
    "max_discount_percent": 30.00,
    "commission_tiers": [
      {
        "min": 1,
        "max": 20,
        "percent": 5.00
      },
      {
        "min": 21,
        "max": 50,
        "percent": 7.00
      },
      {
        "min": 51,
        "max": null,
        "percent": 10.00
      }
    ]
  },
  "statistics": {
    "total_tickets_sold": 45,
    "total_revenue": 5400.00,
    "total_commissions": 270.00,
    "by_event": [
      {
        "event_id": "uuid",
        "event_name": "Opening Night",
        "tickets_sold": 25,
        "revenue": 3000.00
      }
    ]
  }
}
```

### POST `/api/v1/ambassadors/codes/generate`
Generate a new ambassador code.

**Headers**: `Authorization: Bearer <token>`  
**Required Role**: `ambassador`

**Request Body** (Individual Ambassador):
```json
{
  "event_id": "uuid",
  "ticket_type_id": "uuid",
  "code_type": "standard",
  "discount_percent": 20.00
}
```

**Request Body** (Ambassador Team):
```json
{
  "event_id": "uuid",
  "ticket_type_id": "uuid",
  "code_type": "standard",
  "fixed_price": 100.00
}
```

**Request Body** (Guest Ticket):
```json
{
  "event_id": "uuid",
  "ticket_type_id": "uuid",
  "code_type": "guest"
}
```

**Response** (201 Created):
```json
{
  "code": {
    "id": "uuid",
    "code": "LOTUS-1111-2222-3333-4444",
    "event_id": "uuid",
    "ticket_type_id": "uuid",
    "code_type": "standard",
    "base_price": 120.00,
    "discount_percent": 20.00,
    "final_price": 96.00,
    "status": "unused",
    "generated_at": "2024-01-15T10:00:00Z",
    "expires_at": "2024-01-16T10:00:00Z"
  },
  "quota": {
    "remaining_ticket_quota": 54,
    "remaining_guest_quota": 7
  }
}
```

### GET `/api/v1/ambassadors/codes`
Get list of ambassador's generated codes.

**Headers**: `Authorization: Bearer <token>`  
**Required Role**: `ambassador`

**Query Parameters**:
- `page` (integer, default: 1)
- `limit` (integer, default: 20)
- `status` (string, optional: "unused" | "used" | "expired")
- `code_type` (string, optional: "standard" | "guest")
- `event_id` (uuid, optional)

**Response** (200 OK):
```json
{
  "codes": [
    {
      "id": "uuid",
      "code": "LOTUS-1111-2222-3333-4444",
      "event": {
        "id": "uuid",
        "name": "Opening Night"
      },
      "ticket_type": {
        "id": "uuid",
        "name": "General Admission"
      },
      "code_type": "standard",
      "status": "used",
      "base_price": 120.00,
      "final_price": 96.00,
      "generated_at": "2024-01-15T10:00:00Z",
      "expires_at": "2024-01-16T10:00:00Z",
      "used_at": "2024-01-15T11:30:00Z"
    }
  ],
  "pagination": { ... }
}
```

### GET `/api/v1/ambassadors/statistics`
Get ambassador sales statistics.

**Headers**: `Authorization: Bearer <token>`  
**Required Role**: `ambassador`

**Query Parameters**:
- `event_id` (uuid, optional)
- `start_date` (date, optional)
- `end_date` (date, optional)

**Response** (200 OK):
```json
{
  "summary": {
    "total_tickets_sold": 45,
    "total_revenue": 5400.00,
    "total_commissions": 270.00,
    "total_guest_tickets": 3
  },
  "by_event": [
    {
      "event_id": "uuid",
      "event_name": "Opening Night",
      "tickets_sold": 25,
      "revenue": 3000.00,
      "commissions": 150.00,
      "by_phase": {
        "phase_1": { "tickets": 15, "revenue": 1800.00 },
        "phase_2": { "tickets": 10, "revenue": 1200.00 }
      }
    }
  ],
  "commission_breakdown": {
    "tier_1": { "tickets": 20, "commission": 120.00 },
    "tier_2": { "tickets": 25, "commission": 150.00 }
  }
}
```

---

## Artist Endpoints

### GET `/api/v1/artists/me`
Get current artist's profile and break-even data.

**Headers**: `Authorization: Bearer <token>`  
**Required Role**: `artist`

**Response** (200 OK):
```json
{
  "artist": {
    "id": "uuid",
    "name": "Artist Name",
    "is_team": false
  },
  "break_even_status": [
    {
      "event_id": "uuid",
      "event_name": "Opening Night",
      "contracted_fee": 5000.00,
      "revenue_toward_break_even": 3500.00,
      "remaining_amount": 1500.00,
      "break_even_percentage": 70.00,
      "break_even_achieved": false,
      "post_break_even_revenue": 0.00
    },
    {
      "event_id": "uuid",
      "event_name": "Closing Night",
      "contracted_fee": 3000.00,
      "revenue_toward_break_even": 3500.00,
      "remaining_amount": 0.00,
      "break_even_percentage": 116.67,
      "break_even_achieved": true,
      "break_even_achieved_at": "2024-01-10T15:00:00Z",
      "post_break_even_revenue": 500.00
    }
  ],
  "summary": {
    "total_events": 2,
    "total_contracted_fee": 8000.00,
    "total_revenue": 7000.00,
    "total_remaining": 1000.00,
    "events_achieved": 1,
    "total_post_break_even": 500.00
  }
}
```

### GET `/api/v1/artists/events`
Get artist's events with detailed sales data.

**Headers**: `Authorization: Bearer <token>`  
**Required Role**: `artist`

**Response** (200 OK):
```json
{
  "events": [
    {
      "event_id": "uuid",
      "event_name": "Opening Night",
      "date": "2024-07-01T20:00:00Z",
      "tickets_sold": 25,
      "revenue_toward_break_even": 3500.00,
      "contracted_fee": 5000.00,
      "break_even_status": {
        "achieved": false,
        "percentage": 70.00,
        "remaining": 1500.00
      }
    }
  ]
}
```

### GET `/api/v1/artists/reports`
Generate downloadable reports (CSV/PDF).

**Headers**: `Authorization: Bearer <token>`  
**Required Role**: `artist`

**Query Parameters**:
- `format` (string, required: "csv" | "pdf")
- `event_id` (uuid, optional)
- `start_date` (date, optional)
- `end_date` (date, optional)

**Response** (200 OK):
- Content-Type: `text/csv` or `application/pdf`
- File download with report data

---

## Admin Endpoints

### Festival Management

#### GET `/api/v1/admin/festivals`
Get all festivals (admin view).

**Headers**: `Authorization: Bearer <token>`  
**Required Role**: `admin`

#### POST `/api/v1/admin/festivals`
Create new festival.

**Request Body**:
```json
{
  "name": "Summer Music Festival",
  "description": "...",
  "start_date": "2024-07-01",
  "end_date": "2024-07-05",
  "location": "Budapest"
}
```

#### PUT `/api/v1/admin/festivals/{festival_id}`
Update festival.

#### DELETE `/api/v1/admin/festivals/{festival_id}`
Delete festival (soft delete).

### Event Management

#### POST `/api/v1/admin/events`
Create new event.

**Request Body**:
```json
{
  "festival_id": "uuid",
  "name": "Opening Night",
  "description": "...",
  "date": "2024-07-01T20:00:00Z",
  "location": "Main Stage",
  "phases": [
    {
      "phase_number": 1,
      "base_price": 120.00,
      "start_date": "2024-01-01T00:00:00Z",
      "end_date": "2024-03-31T23:59:59Z"
    },
    {
      "phase_number": 2,
      "base_price": 150.00,
      "start_date": "2024-04-01T00:00:00Z",
      "end_date": "2024-05-31T23:59:59Z"
    },
    {
      "phase_number": 3,
      "base_price": 170.00,
      "start_date": "2024-06-01T00:00:00Z",
      "end_date": "2024-07-01T23:59:59Z"
    }
  ],
  "ticket_types": [
    {
      "name": "General Admission",
      "capacity": 1000
    }
  ],
  "artist_ids": ["uuid1", "uuid2"]
}
```

### Ambassador Management

#### GET `/api/v1/admin/ambassadors`
Get all ambassadors.

#### POST `/api/v1/admin/ambassadors`
Create new ambassador.

**Request Body** (Individual):
```json
{
  "user_id": "uuid",
  "type": "individual",
  "prefix": "LOTUS",
  "ticket_quota": 100,
  "guest_ticket_quota": 10,
  "config": {
    "min_discount_percent": 0.00,
    "max_discount_percent": 30.00,
    "discount_scope": "global",
    "commission_tier_1_min": 1,
    "commission_tier_1_max": 20,
    "commission_tier_1_percent": 5.00,
    "commission_tier_2_min": 21,
    "commission_tier_2_max": 50,
    "commission_tier_2_percent": 7.00,
    "commission_tier_3_min": 51,
    "commission_tier_3_max": null,
    "commission_tier_3_percent": 10.00
  }
}
```

**Request Body** (Team):
```json
{
  "user_id": "uuid",
  "type": "team",
  "prefix": "TEAM1",
  "ticket_quota": 200,
  "guest_ticket_quota": 20,
  "config": {
    "min_ticket_price": 100.00,
    "min_price_scope": "global"
  }
}
```

#### PUT `/api/v1/admin/ambassadors/{ambassador_id}`
Update ambassador (quota, config, etc.).

#### GET `/api/v1/admin/ambassadors/{ambassador_id}`
Get ambassador details with full statistics.

### Artist Management

#### POST `/api/v1/admin/artists`
Create new artist.

**Request Body**:
```json
{
  "name": "Artist Name",
  "description": "...",
  "user_id": "uuid",
  "is_team": false,
  "contact_email": "artist@example.com"
}
```

#### POST `/api/v1/admin/events/{event_id}/artists`
Associate artist with event and set break-even target.

**Request Body**:
```json
{
  "artist_id": "uuid",
  "contracted_fee": 5000.00,
  "performance_time": "2024-07-01T21:00:00Z"
}
```

### Code Management

#### GET `/api/v1/admin/codes`
Get all codes with filters.

**Query Parameters**:
- `ambassador_id` (uuid, optional)
- `code_type` (string, optional)
- `event_id` (uuid, optional)
- `status` (string, optional)
- `start_date` (date, optional)
- `end_date` (date, optional)

### Reporting

#### GET `/api/v1/admin/reports/events/{event_id}`
Generate event-level report.

#### GET `/api/v1/admin/reports/ambassadors`
Generate ambassador performance report.

#### GET `/api/v1/admin/reports/artists`
Generate artist break-even report.

#### GET `/api/v1/admin/reports/financial`
Generate financial report (revenue, discounts, commissions).

---

## Error Handling

### Standard Error Response Format
```json
{
  "error": "ERROR_CODE",
  "message": "Human-readable error message",
  "details": {
    "field": "additional error details"
  }
}
```

### Common Error Codes
- `UNAUTHORIZED`: Authentication required
- `FORBIDDEN`: Insufficient permissions
- `NOT_FOUND`: Resource not found
- `VALIDATION_ERROR`: Request validation failed
- `CODE_EXPIRED`: Ambassador code expired
- `CODE_ALREADY_USED`: Code already redeemed
- `CODE_INVALID`: Code format or validation failed
- `QUOTA_EXCEEDED`: Ambassador quota exhausted
- `TICKET_SOLD_OUT`: No tickets available
- `PHASE_MISMATCH`: Code generated for different phase
- `PRICE_OUT_OF_RANGE`: Price outside allowed range
- `DISCOUNT_OUT_OF_RANGE`: Discount outside allowed range

### HTTP Status Codes
- `200 OK`: Successful GET/PUT request
- `201 Created`: Successful POST request
- `400 Bad Request`: Validation error
- `401 Unauthorized`: Authentication required
- `403 Forbidden`: Insufficient permissions
- `404 Not Found`: Resource not found
- `409 Conflict`: Resource conflict (e.g., duplicate code)
- `422 Unprocessable Entity`: Business logic validation failed
- `500 Internal Server Error`: Server error

---

## Data Flow Diagrams

### Code Generation Flow
```
Ambassador → POST /ambassadors/codes/generate
  ↓
Validate quota, event, phase
  ↓
Calculate final price (discount or fixed)
  ↓
Generate unique code
  ↓
Store code with 24h expiration
  ↓
Update quota counters
  ↓
Return code to ambassador
```

### Ticket Purchase Flow
```
User → POST /tickets/purchase (with code)
  ↓
Validate code (status, expiration, event match)
  ↓
Calculate final price
  ↓
Create TicketPurchase record
  ↓
Create Payment record
  ↓
Create Ticket instances
  ↓
Update code status to "used"
  ↓
Update artist revenue toward break-even
  ↓
Update ticket type sold_count
  ↓
Update ambassador tickets_sold
  ↓
Process payment
  ↓
Return purchase confirmation
```

### Break-Even Calculation Flow
```
Ticket Purchase Completed
  ↓
Identify associated Artist(s)
  ↓
Update EventArtist.revenue_toward_break_even
  ↓
Check: revenue >= contracted_fee?
  ↓
If yes and not already achieved:
  - Set break_even_achieved = true
  - Set break_even_achieved_at = now()
  - Calculate post_break_even_revenue
  ↓
Notify artist (optional, async)
```

---

## Rate Limiting

### Limits
- **Code Generation**: 10 codes per minute per ambassador
- **Ticket Purchase**: 5 purchases per minute per user
- **API Calls**: 100 requests per minute per user (general)

### Headers
- `X-RateLimit-Limit`: Maximum requests allowed
- `X-RateLimit-Remaining`: Remaining requests
- `X-RateLimit-Reset`: Time when limit resets

---

## Webhooks (Future Enhancement)

### Events
- `ticket.purchased`
- `code.generated`
- `code.used`
- `code.expired`
- `break_even.achieved`
- `phase.transitioned`

### Payload Format
```json
{
  "event": "ticket.purchased",
  "timestamp": "2024-01-15T10:30:00Z",
  "data": {
    "purchase_id": "uuid",
    "event_id": "uuid",
    "amount": 192.00
  }
}
```

---

**Document Version**: 1.0  
**Last Updated**: 2024  
**Status**: Design Phase
