# Git Repository Structure

## Overview

This document outlines the recommended Git repository structure for the Festival Ticketing Platform. The structure emphasizes:
- **Separation of concerns** (frontend, backend, infrastructure)
- **Developer-friendly organization**
- **Clear documentation**
- **Scalable architecture**

---

## Root Directory Structure

```
festival-ticketing-platform/
├── .github/                    # GitHub-specific files
│   ├── workflows/              # CI/CD pipelines
│   │   ├── backend-ci.yml
│   │   ├── frontend-ci.yml
│   │   └── deploy.yml
│   └── ISSUE_TEMPLATE/         # Issue templates
│
├── backend/                    # Backend application
│   ├── app/
│   ├── alembic/                # Database migrations
│   ├── tests/
│   ├── scripts/
│   ├── Dockerfile
│   ├── requirements.txt
│   └── pyproject.toml
│
├── frontend/                   # Frontend application
│   ├── src/
│   ├── public/
│   ├── Dockerfile
│   ├── package.json
│   └── next.config.js
│
├── infrastructure/             # Infrastructure as Code
│   ├── docker/
│   │   ├── docker-compose.yml
│   │   └── docker-compose.prod.yml
│   ├── terraform/              # Optional: Terraform configs
│   └── kubernetes/             # Optional: K8s manifests
│
├── docs/                       # Documentation
│   ├── FESTIVAL_TICKETING_*.md # Design docs
│   ├── API.md                  # API documentation
│   ├── DEPLOYMENT.md           # Deployment guide
│   └── CONTRIBUTING.md         # Contribution guidelines
│
├── scripts/                    # Utility scripts
│   ├── setup.sh
│   ├── migrate.sh
│   └── seed.sh
│
├── .gitignore
├── .env.example
├── README.md                   # Main project README
├── LICENSE
└── docker-compose.yml          # Development environment
```

---

## Backend Structure (`/backend`)

```
backend/
├── app/
│   ├── __init__.py
│   ├── main.py                 # FastAPI application entry
│   ├── config.py                # Configuration settings
│   │
│   ├── api/                     # API routes
│   │   ├── __init__.py
│   │   ├── deps.py              # Dependencies (auth, DB)
│   │   │
│   │   ├── v1/                  # API version 1
│   │   │   ├── __init__.py
│   │   │   ├── auth.py          # Authentication endpoints
│   │   │   ├── festivals.py     # Festival endpoints
│   │   │   ├── events.py        # Event endpoints
│   │   │   ├── tickets.py       # Ticket purchase endpoints
│   │   │   ├── codes.py         # Code validation endpoints
│   │   │   ├── ambassadors.py  # Ambassador endpoints
│   │   │   ├── artists.py       # Artist endpoints
│   │   │   ├── purchases.py     # Purchase history endpoints
│   │   │   └── admin/           # Admin-only endpoints
│   │   │       ├── festivals.py
│   │   │       ├── ambassadors.py
│   │   │       ├── artists.py
│   │   │       ├── codes.py
│   │   │       └── reports.py
│   │   │
│   │   └── public/              # Public endpoints (no auth)
│   │       ├── festivals.py
│   │       ├── events.py
│   │       └── codes.py
│   │
│   ├── core/                    # Core functionality
│   │   ├── __init__.py
│   │   ├── security.py          # JWT, password hashing
│   │   ├── config.py            # Settings management
│   │   └── exceptions.py        # Custom exceptions
│   │
│   ├── models/                  # SQLAlchemy models
│   │   ├── __init__.py
│   │   ├── user.py
│   │   ├── festival.py
│   │   ├── event.py
│   │   ├── ticket.py
│   │   ├── ambassador.py
│   │   ├── artist.py
│   │   ├── code.py
│   │   └── purchase.py
│   │
│   ├── schemas/                 # Pydantic schemas
│   │   ├── __init__.py
│   │   ├── user.py
│   │   ├── festival.py
│   │   ├── event.py
│   │   ├── ticket.py
│   │   ├── ambassador.py
│   │   ├── artist.py
│   │   ├── code.py
│   │   └── purchase.py
│   │
│   ├── services/                # Business logic
│   │   ├── __init__.py
│   │   ├── auth_service.py
│   │   ├── festival_service.py
│   │   ├── event_service.py
│   │   ├── ticket_service.py
│   │   ├── code_service.py
│   │   ├── ambassador_service.py
│   │   ├── artist_service.py
│   │   ├── purchase_service.py
│   │   ├── break_even_service.py
│   │   └── commission_service.py
│   │
│   ├── repositories/           # Data access layer
│   │   ├── __init__.py
│   │   ├── base.py             # Base repository
│   │   ├── user_repository.py
│   │   ├── festival_repository.py
│   │   ├── event_repository.py
│   │   ├── ticket_repository.py
│   │   ├── ambassador_repository.py
│   │   ├── artist_repository.py
│   │   └── code_repository.py
│   │
│   ├── tasks/                  # Celery tasks
│   │   ├── __init__.py
│   │   ├── code_expiration.py  # Check expired codes
│   │   ├── email_tasks.py      # Send emails
│   │   └── report_generation.py
│   │
│   └── utils/                  # Utility functions
│       ├── __init__.py
│       ├── code_generator.py   # Generate unique codes
│       ├── price_calculator.py # Calculate prices
│       └── validators.py        # Custom validators
│
├── alembic/                     # Database migrations
│   ├── versions/
│   ├── env.py
│   └── script.py.mako
│
├── tests/                       # Test suite
│   ├── __init__.py
│   ├── conftest.py             # Pytest configuration
│   ├── unit/
│   │   ├── test_services/
│   │   ├── test_repositories/
│   │   └── test_utils/
│   ├── integration/
│   │   ├── test_api/
│   │   └── test_database/
│   └── fixtures/
│       ├── users.py
│       ├── festivals.py
│       └── ambassadors.py
│
├── scripts/                     # Utility scripts
│   ├── seed_db.py              # Seed database
│   ├── create_admin.py         # Create admin user
│   └── migrate_data.py         # Data migration
│
├── .env.example                 # Environment variables template
├── .gitignore
├── Dockerfile
├── requirements.txt             # Python dependencies
├── requirements-dev.txt         # Development dependencies
├── pyproject.toml               # Python project config
├── pytest.ini                   # Pytest configuration
└── README.md                    # Backend-specific README
```

---

## Frontend Structure (`/frontend`)

```
frontend/
├── src/
│   ├── app/                     # Next.js App Router
│   │   ├── layout.tsx           # Root layout
│   │   ├── page.tsx             # Home page
│   │   ├── (auth)/
│   │   │   ├── login/
│   │   │   └── register/
│   │   ├── (public)/
│   │   │   ├── festivals/
│   │   │   │   ├── page.tsx     # Festival list
│   │   │   │   └── [id]/
│   │   │   │       └── page.tsx # Festival detail
│   │   │   ├── events/
│   │   │   │   └── [id]/
│   │   │   │       └── page.tsx # Event detail
│   │   │   └── about/
│   │   ├── (authenticated)/
│   │   │   ├── tickets/
│   │   │   │   ├── page.tsx     # My tickets
│   │   │   │   └── purchase/
│   │   │   │       └── page.tsx # Purchase flow
│   │   │   ├── ambassadors/
│   │   │   │   ├── dashboard/
│   │   │   │   ├── codes/
│   │   │   │   └── statistics/
│   │   │   └── artists/
│   │   │       ├── dashboard/
│   │   │       └── events/
│   │   └── (admin)/
│   │       ├── admin/
│   │       │   ├── festivals/
│   │       │   ├── events/
│   │       │   ├── ambassadors/
│   │       │   ├── artists/
│   │       │   └── reports/
│   │       └── layout.tsx       # Admin layout
│   │
│   ├── components/              # React components
│   │   ├── ui/                  # shadcn/ui components
│   │   │   ├── button.tsx
│   │   │   ├── input.tsx
│   │   │   ├── card.tsx
│   │   │   └── ...
│   │   ├── layout/
│   │   │   ├── Header.tsx
│   │   │   ├── Footer.tsx
│   │   │   └── Sidebar.tsx
│   │   ├── festival/
│   │   │   ├── FestivalCard.tsx
│   │   │   ├── FestivalList.tsx
│   │   │   └── FestivalDetail.tsx
│   │   ├── event/
│   │   │   ├── EventCard.tsx
│   │   │   ├── EventDetail.tsx
│   │   │   └── TicketPhaseSelector.tsx
│   │   ├── ticket/
│   │   │   ├── TicketPurchaseForm.tsx
│   │   │   ├── CodeInput.tsx
│   │   │   └── TicketCard.tsx
│   │   ├── ambassador/
│   │   │   ├── CodeGenerator.tsx
│   │   │   ├── CodeList.tsx
│   │   │   └── Dashboard.tsx
│   │   ├── artist/
│   │   │   ├── BreakEvenProgress.tsx
│   │   │   └── ArtistDashboard.tsx
│   │   └── admin/
│   │       ├── AmbassadorForm.tsx
│   │       ├── EventForm.tsx
│   │       └── ReportGenerator.tsx
│   │
│   ├── lib/                     # Utilities and helpers
│   │   ├── api.ts               # API client
│   │   ├── auth.ts              # Auth utilities
│   │   ├── utils.ts             # General utilities
│   │   └── constants.ts         # Constants
│   │
│   ├── hooks/                   # Custom React hooks
│   │   ├── useAuth.ts
│   │   ├── useFestivals.ts
│   │   ├── useCodes.ts
│   │   └── usePurchase.ts
│   │
│   ├── store/                   # State management (Zustand)
│   │   ├── authStore.ts
│   │   ├── festivalStore.ts
│   │   └── cartStore.ts
│   │
│   ├── types/                   # TypeScript types
│   │   ├── user.ts
│   │   ├── festival.ts
│   │   ├── event.ts
│   │   ├── ticket.ts
│   │   ├── ambassador.ts
│   │   └── artist.ts
│   │
│   ├── styles/                  # Global styles
│   │   └── globals.css
│   │
│   └── middleware.ts            # Next.js middleware (auth)
│
├── public/                      # Static assets
│   ├── images/
│   ├── icons/
│   └── favicon.ico
│
├── .env.example                 # Environment variables
├── .gitignore
├── Dockerfile
├── next.config.js               # Next.js configuration
├── tailwind.config.js           # Tailwind CSS config
├── tsconfig.json                # TypeScript config
├── package.json                 # Dependencies
├── package-lock.json
└── README.md                    # Frontend-specific README
```

---

## Infrastructure Structure (`/infrastructure`)

```
infrastructure/
├── docker/
│   ├── docker-compose.yml       # Development environment
│   ├── docker-compose.prod.yml  # Production environment
│   └── Dockerfile.backend
│   └── Dockerfile.frontend
│
├── terraform/                   # Optional: Infrastructure as Code
│   ├── main.tf
│   ├── variables.tf
│   ├── outputs.tf
│   └── modules/
│       ├── database/
│       ├── compute/
│       └── networking/
│
├── kubernetes/                  # Optional: K8s manifests
│   ├── backend/
│   │   ├── deployment.yaml
│   │   ├── service.yaml
│   │   └── configmap.yaml
│   ├── frontend/
│   │   ├── deployment.yaml
│   │   └── service.yaml
│   └── database/
│       └── postgres.yaml
│
└── scripts/
    ├── deploy.sh
    └── backup.sh
```

---

## Documentation Structure (`/docs`)

```
docs/
├── FESTIVAL_TICKETING_PRODUCT_OVERVIEW.md
├── FESTIVAL_TICKETING_USER_ROLES.md
├── FESTIVAL_TICKETING_BUSINESS_LOGIC.md
├── FESTIVAL_TICKETING_DOMAIN_MODEL.md
├── FESTIVAL_TICKETING_API_DESIGN.md
├── FESTIVAL_TICKETING_TECH_STACK.md
├── FESTIVAL_TICKETING_REPOSITORY_STRUCTURE.md
├── FESTIVAL_TICKETING_IMPLEMENTATION_ROADMAP.md
│
├── API.md                       # Generated API documentation
├── DEPLOYMENT.md                # Deployment guide
├── CONTRIBUTING.md              # Contribution guidelines
├── DEVELOPMENT.md               # Development setup guide
└── ARCHITECTURE.md              # Architecture overview
```

---

## Root-Level Files

### `.gitignore`
```gitignore
# Python
__pycache__/
*.py[cod]
*.pyc
*.so
.Python
venv/
env/
.venv

# Node.js
node_modules/
.next/
out/
dist/
*.log

# Environment
.env
.env.local
.env.*.local

# IDE
.vscode/
.idea/
*.swp
*.swo

# Database
*.db
*.sqlite

# Docker
.dockerignore

# OS
.DS_Store
Thumbs.db
```

### `README.md` (Root)
```markdown
# Festival Ticketing Platform

[Project description, quick start, links to documentation]

## Quick Start
[Setup instructions]

## Documentation
- [Product Overview](docs/FESTIVAL_TICKETING_PRODUCT_OVERVIEW.md)
- [API Documentation](docs/API.md)
- [Deployment Guide](docs/DEPLOYMENT.md)
- [Contributing](docs/CONTRIBUTING.md)
```

### `docker-compose.yml` (Development)
```yaml
version: '3.8'
services:
  postgres:
    image: postgres:15
    environment:
      POSTGRES_DB: festival_tickets
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7
    ports:
      - "6379:6379"

  backend:
    build: ./backend
    ports:
      - "8000:8000"
    environment:
      DATABASE_URL: postgresql://postgres:postgres@postgres:5432/festival_tickets
      REDIS_URL: redis://redis:6379
    depends_on:
      - postgres
      - redis

  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    environment:
      NEXT_PUBLIC_API_URL: http://localhost:8000
    depends_on:
      - backend

volumes:
  postgres_data:
```

---

## Branching Strategy

### Recommended: Git Flow

```
main                    # Production-ready code
├── develop             # Development branch
│   ├── feature/ambassador-system
│   ├── feature/ticket-purchase
│   ├── feature/break-even-tracking
│   └── bugfix/code-validation
├── release/v1.0.0      # Release candidates
└── hotfix/critical-bug  # Emergency fixes
```

### Branch Naming
- `feature/description` - New features
- `bugfix/description` - Bug fixes
- `hotfix/description` - Critical production fixes
- `release/v1.0.0` - Release branches
- `docs/description` - Documentation updates

---

## Commit Message Convention

### Format
```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation
- `style`: Code style (formatting)
- `refactor`: Code refactoring
- `test`: Tests
- `chore`: Maintenance tasks

### Examples
```
feat(ambassador): add code generation endpoint

Implement POST /api/v1/ambassadors/codes/generate with
quota validation and 24-hour expiration.

Closes #123
```

```
fix(ticket): validate code expiration during purchase

Prevent purchase with expired codes by checking
expires_at timestamp.

Fixes #456
```

---

## Development Workflow

### 1. Setup
```bash
# Clone repository
git clone <repo-url>
cd festival-ticketing-platform

# Setup backend
cd backend
python -m venv venv
source venv/bin/activate  # or `venv\Scripts\activate` on Windows
pip install -r requirements.txt

# Setup frontend
cd ../frontend
npm install

# Start services
docker-compose up -d
```

### 2. Development
```bash
# Create feature branch
git checkout -b feature/new-feature

# Make changes
# ...

# Commit
git commit -m "feat(scope): description"

# Push and create PR
git push origin feature/new-feature
```

### 3. Testing
```bash
# Backend tests
cd backend
pytest

# Frontend tests
cd frontend
npm test
```

---

## File Naming Conventions

### Python (Backend)
- Files: `snake_case.py`
- Classes: `PascalCase`
- Functions/Variables: `snake_case`
- Constants: `UPPER_SNAKE_CASE`

### TypeScript/React (Frontend)
- Files: `PascalCase.tsx` (components), `camelCase.ts` (utilities)
- Components: `PascalCase`
- Functions/Variables: `camelCase`
- Constants: `UPPER_SNAKE_CASE`

### Database
- Tables: `snake_case` (plural)
- Columns: `snake_case`

---

## Environment Variables

### Backend (`.env`)
```env
# Database
DATABASE_URL=postgresql://user:password@localhost:5432/festival_tickets

# Redis
REDIS_URL=redis://localhost:6379

# JWT
SECRET_KEY=your-secret-key
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=60

# CORS
CORS_ORIGINS=http://localhost:3000

# Email
SENDGRID_API_KEY=your-api-key
FROM_EMAIL=noreply@festival.com

# Payment
STRIPE_SECRET_KEY=your-stripe-key
STRIPE_WEBHOOK_SECRET=your-webhook-secret
```

### Frontend (`.env.local`)
```env
NEXT_PUBLIC_API_URL=http://localhost:8000
NEXT_PUBLIC_STRIPE_PUBLIC_KEY=your-stripe-public-key
```

---

**Document Version**: 1.0  
**Last Updated**: 2024  
**Status**: Design Phase
