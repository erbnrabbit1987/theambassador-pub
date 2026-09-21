# Festival Ticketing Platform - Documentation Index

## Welcome

This directory contains comprehensive documentation for the **Festival Ticketing Platform** - a multi-event ticketing system with an integrated Ambassador program designed for low-budget festivals where artist fees are tied to ticket sales performance.

---

## Documentation Overview

### 📋 Core Documentation

1. **[Product Overview](FESTIVAL_TICKETING_PRODUCT_OVERVIEW.md)**
   - Executive summary and value proposition
   - Key features and business model
   - Target users and success metrics

2. **[User Roles and Permissions](FESTIVAL_TICKETING_USER_ROLES.md)**
   - Detailed role definitions (Public, Ambassador, Artist, Admin)
   - Permission matrix
   - Authentication and authorization

3. **[Business Logic and Rules](FESTIVAL_TICKETING_BUSINESS_LOGIC.md)**
   - Ticket phases and pricing
   - Artist break-even model
   - Ambassador system rules (Individual vs Team)
   - Code generation and validation
   - Commission calculation
   - Revenue tracking

4. **[Domain Model and Entities](FESTIVAL_TICKETING_DOMAIN_MODEL.md)**
   - Complete entity relationship diagram
   - Database schema design
   - Entity attributes and relationships
   - Data flow examples

5. **[API Design](FESTIVAL_TICKETING_API_DESIGN.md)**
   - RESTful API endpoints
   - Request/response schemas
   - Authentication flows
   - Error handling
   - Data flow diagrams

6. **[Technology Stack](FESTIVAL_TICKETING_TECH_STACK.md)**
   - Recommended frontend stack (Next.js, React, TypeScript)
   - Recommended backend stack (FastAPI, Python, PostgreSQL)
   - Infrastructure recommendations
   - Justification and alternatives

7. **[Repository Structure](FESTIVAL_TICKETING_REPOSITORY_STRUCTURE.md)**
   - Git repository organization
   - Folder structure (backend, frontend, infrastructure)
   - File naming conventions
   - Branching strategy

8. **[Implementation Roadmap](FESTIVAL_TICKETING_IMPLEMENTATION_ROADMAP.md)**
   - Phased implementation plan (MVP → Production)
   - Timeline and milestones
   - Task breakdown
   - Risk mitigation

---

## Quick Start Guide

### For Project Managers
1. Start with **[Product Overview](FESTIVAL_TICKETING_PRODUCT_OVERVIEW.md)** to understand the platform
2. Review **[Business Logic](FESTIVAL_TICKETING_BUSINESS_LOGIC.md)** for core rules
3. Check **[Implementation Roadmap](FESTIVAL_TICKETING_IMPLEMENTATION_ROADMAP.md)** for timeline

### For Developers
1. Read **[Technology Stack](FESTIVAL_TICKETING_TECH_STACK.md)** for tech decisions
2. Review **[Domain Model](FESTIVAL_TICKETING_DOMAIN_MODEL.md)** for database design
3. Study **[API Design](FESTIVAL_TICKETING_API_DESIGN.md)** for endpoints
4. Follow **[Repository Structure](FESTIVAL_TICKETING_REPOSITORY_STRUCTURE.md)** for organization
5. Use **[Implementation Roadmap](FESTIVAL_TICKETING_IMPLEMENTATION_ROADMAP.md)** as a guide

### For Designers
1. Review **[User Roles](FESTIVAL_TICKETING_USER_ROLES.md)** for user personas
2. Study **[Product Overview](FESTIVAL_TICKETING_PRODUCT_OVERVIEW.md)** for feature requirements
3. Check **[API Design](FESTIVAL_TICKETING_API_DESIGN.md)** for data structures

### For Stakeholders
1. **[Product Overview](FESTIVAL_TICKETING_PRODUCT_OVERVIEW.md)** - High-level vision
2. **[Business Logic](FESTIVAL_TICKETING_BUSINESS_LOGIC.md)** - How the system works
3. **[Implementation Roadmap](FESTIVAL_TICKETING_IMPLEMENTATION_ROADMAP.md)** - Timeline and phases

---

## Key Concepts

### Ambassador System
The platform supports two types of ambassadors:
- **Individual Ambassadors**: Set percentage discounts, earn tiered commissions
- **Ambassador Teams**: Set fixed prices within a range, no commissions

### Break-Even Model
Artists have contracted fees that must be reached through ticket sales. The system tracks:
- Revenue toward break-even
- Break-even status (achieved/not achieved)
- Post break-even revenue (festival profit)

### Ticket Phases
Each event has 3 time-based phases with increasing base prices:
- Phase 1: Early bird (e.g., 120 EUR)
- Phase 2: Standard (e.g., 150 EUR)
- Phase 3: Late (e.g., 170 EUR)

### Code System
- Format: `PREFIX-NUMERIC_SEQUENCE` (e.g., `LOTUS-1111-2222-3333-4444`)
- Single-use only
- 24-hour expiration
- Two types: Standard tickets and Guest tickets (0 EUR)

---

## Architecture Overview

```
┌─────────────────┐
│   Frontend      │  Next.js + React + TypeScript
│   (Next.js)     │  Tailwind CSS + shadcn/ui
└────────┬────────┘
         │ HTTP/REST
         │
┌────────▼────────┐
│   Backend       │  FastAPI + Python
│   (FastAPI)     │  SQLAlchemy + Pydantic
└────────┬────────┘
         │
    ┌────┴────┐
    │         │
┌───▼───┐ ┌──▼────┐
│  DB   │ │ Redis │
│(PostgreSQL)│ │(Cache)│
└───────┘ └───────┘
```

---

## Technology Stack Summary

### Frontend
- **Framework**: Next.js 14+ (App Router)
- **Language**: TypeScript
- **UI**: React 18+, Tailwind CSS, shadcn/ui
- **State**: Zustand / TanStack Query
- **Forms**: React Hook Form + Zod

### Backend
- **Framework**: FastAPI
- **Language**: Python 3.11+
- **ORM**: SQLAlchemy 2.0 (async)
- **Database**: PostgreSQL 15+
- **Cache**: Redis 7+
- **Tasks**: Celery

### Infrastructure
- **Containerization**: Docker
- **Cloud**: AWS / GCP / DigitalOcean
- **CI/CD**: GitHub Actions
- **Monitoring**: Sentry
- **Payments**: Stripe

---

## Implementation Phases

### Phase 0-1: Foundation (Weeks 1-4)
- Setup, authentication, public pages

### Phase 2: Ticket Purchase (Weeks 5-6)
- Purchase flow, payment integration

### Phase 3: Ambassador System (Weeks 7-9)
- Code generation, redemption, dashboard

### Phase 4: Admin Panel (Weeks 10-11)
- Festival/event/ambassador management

### Phase 5: Break-Even (Weeks 12-13)
- Artist break-even tracking

### Phase 6: Commissions (Week 14)
- Tiered commission calculation

### Phase 7: Reporting (Weeks 15-16)
- Analytics and report generation

### Phase 8: Polish (Weeks 17-18)
- Optimization, security, testing

### Phase 9: Production (Weeks 19-20)
- Deployment, monitoring, launch

**Total MVP Timeline: ~20 weeks (~5 months)**

---

## Next Steps

1. **Review Documentation**: Read through all documentation files
2. **Validate Requirements**: Confirm business logic with stakeholders
3. **Tech Stack Approval**: Review and approve technology choices
4. **Team Setup**: Assign developers to frontend/backend
5. **Begin Phase 0**: Start with foundation and setup

---

## Questions or Issues?

For questions about:
- **Business Logic**: See [Business Logic](FESTIVAL_TICKETING_BUSINESS_LOGIC.md)
- **API Design**: See [API Design](FESTIVAL_TICKETING_API_DESIGN.md)
- **Implementation**: See [Implementation Roadmap](FESTIVAL_TICKETING_IMPLEMENTATION_ROADMAP.md)
- **Technology**: See [Technology Stack](FESTIVAL_TICKETING_TECH_STACK.md)

---

## Document Status

All documents are in **Design Phase** and ready for review. They will be updated as implementation progresses.

**Last Updated**: 2024  
**Version**: 1.0

---

## License

[Specify license if applicable]
