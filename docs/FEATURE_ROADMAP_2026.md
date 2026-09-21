# Feature Development Roadmap 2026

**Status**: Planning Phase  
**Created**: 2026-01-23  
**Last Updated**: 2026-01-23

---

## Overview

This roadmap outlines the implementation plan for critical features and fixes that need to be addressed before completing Phase 8 (Polish & Optimization). The features are ordered based on dependencies and development priorities.

---

## Current State Assessment

### ✅ Already Implemented
- **Social Auth Infrastructure**: Database schema, models, and basic endpoints exist
- **Ticket Phase Model**: Database model exists but hardcoded to 3 phases
- **User Model**: Has all necessary fields for profiles, email verification, phone validation
- **Admin Panel**: Basic admin functionality exists

### 🔧 Needs Implementation
- Social login frontend integration (Gmail, Facebook, Instagram, Soundcloud)
- Dynamic ticket phase management
- User profile pages and management
- Email service integration (Gmail)
- Email verification system
- Phone number validation and SMS preparation
- Admin user management interface

---

## Roadmap Phases

### Phase A: Email Infrastructure & Verification (Foundation)
**Priority**: HIGH  
**Duration**: 1-2 weeks  
**Dependencies**: None

#### A1: Email Service Setup
**Tasks**:
- [ ] Set up Gmail SMTP configuration in backend settings
- [ ] Create email utility module (`app/utils/email.py`)
- [ ] Implement email sending functions (SMTP via Gmail)
- [ ] Create email template system (HTML templates)
- [ ] Add email configuration to environment variables
- [ ] Test email sending functionality

**Deliverables**:
- Email service can send emails via Gmail SMTP
- Email templates for common use cases
- Configuration documented

#### A2: Email Verification System
**Tasks**:
- [ ] Add `email_verification_token` field to User model (migration)
- [ ] Add `email_verification_token_expires_at` field to User model
- [ ] Create email verification token generation utility
- [ ] Backend: Send verification email on registration
- [ ] Backend: Email verification endpoint (`POST /api/v1/auth/verify-email`)
- [ ] Backend: Resend verification email endpoint (`POST /api/v1/auth/resend-verification`)
- [ ] Frontend: Email verification page (`/verify-email`)
- [ ] Frontend: Update registration flow to show verification message
- [ ] Frontend: Add verification status indicator in user profile
- [ ] Update `get_current_user` to optionally require email verification
- [ ] Add email verification requirement for sensitive operations

**Deliverables**:
- Users receive verification emails on registration
- Users can verify their email addresses
- Users can resend verification emails
- Email verification status tracked in database

**Estimated Complexity**: Medium

---

### Phase B: Social Login Implementation
**Priority**: HIGH  
**Duration**: 2-3 weeks  
**Dependencies**: None (can run parallel with Phase A)

#### B1: Backend Social Auth Enhancement
**Tasks**:
- [ ] Review and enhance existing social auth utilities (`app/utils/social_auth.py`)
- [ ] Implement Instagram OAuth token verification
- [ ] Implement Soundcloud OAuth token verification
- [ ] Enhance Facebook OAuth (if needed)
- [ ] Enhance Google/Gmail OAuth (if needed)
- [ ] Add provider-specific user info extraction
- [ ] Handle account linking (link social account to existing email account)
- [ ] Update social auth endpoint to handle all providers
- [ ] Add error handling for provider-specific errors

**Deliverables**:
- All 4 providers (Gmail, Facebook, Instagram, Soundcloud) supported
- Token verification working for all providers
- Account linking functionality

#### B2: Frontend Social Login Integration
**Tasks**:
- [ ] Install OAuth libraries (e.g., `react-oauth/google`, `react-facebook-login`)
- [ ] Create social login button components
- [ ] Implement Google/Gmail OAuth flow
- [ ] Implement Facebook OAuth flow
- [ ] Implement Instagram OAuth flow
- [ ] Implement Soundcloud OAuth flow
- [ ] Update login page with social login options
- [ ] Update registration page with social login options
- [ ] Handle OAuth callbacks and token exchange
- [ ] Handle account linking UI flow
- [ ] Add social provider indicators in user profile
- [ ] Error handling and user feedback

**Deliverables**:
- Users can log in with Gmail, Facebook, Instagram, Soundcloud
- Social login buttons on login/register pages
- OAuth flow working end-to-end
- Account linking UI functional

**Estimated Complexity**: Medium-High

---

### Phase C: Dynamic Ticket Phases
**Priority**: HIGH  
**Duration**: 1-2 weeks  
**Dependencies**: None

#### C1: Database Schema Updates
**Tasks**:
- [ ] Remove hardcoded phase constraint (`phase_number <= 3`)
- [ ] Remove `phase_1_start`, `phase_1_end`, `phase_2_start`, `phase_2_end`, `phase_3_start`, `phase_3_end` from Event model
- [ ] Update `current_phase` to be nullable or remove it (calculate dynamically)
- [ ] Add migration to remove phase columns from events table
- [ ] Update TicketPhase model to remove phase_number constraint
- [ ] Add unique constraint on `(event_id, phase_number)` at database level
- [ ] Update Event model relationships

**Deliverables**:
- Database schema supports unlimited phases
- Migration script ready

#### C2: Backend API Updates
**Tasks**:
- [ ] Update Event creation endpoint to not require phase dates
- [ ] Create ticket phase CRUD endpoints:
  - `POST /api/v1/admin/events/{event_id}/phases` - Add phase
  - `GET /api/v1/admin/events/{event_id}/phases` - List phases
  - `PUT /api/v1/admin/events/{event_id}/phases/{phase_id}` - Update phase
  - `DELETE /api/v1/admin/events/{event_id}/phases/{phase_id}` - Delete phase
- [ ] Update phase validation (no overlaps, sequential numbering)
- [ ] Update `current_phase` calculation logic (based on current time)
- [ ] Update purchase flow to work with dynamic phases
- [ ] Update event repository methods
- [ ] Update event schemas

**Deliverables**:
- API endpoints for phase management
- Dynamic phase calculation working

#### C3: Frontend Phase Management UI
**Tasks**:
- [ ] Update event creation form (remove hardcoded phase fields)
- [ ] Create phase management component (add/edit/delete phases)
- [ ] Add phase list to event edit page
- [ ] Phase form with: phase number, base price, start date, end date
- [ ] Phase validation (no overlaps, sequential)
- [ ] Visual phase timeline/calendar
- [ ] Update purchase page to handle dynamic phases
- [ ] Update event display to show current phase dynamically

**Deliverables**:
- Admin can add/edit/delete phases dynamically
- Event creation doesn't require phases upfront
- Purchase flow works with any number of phases

**Estimated Complexity**: Medium-High

---

### Phase D: Admin User Management Interface
**Priority**: MEDIUM  
**Duration**: 1 week  
**Dependencies**: Phase A (email verification) recommended

#### D1: Backend User Management Endpoints
**Tasks**:
- [ ] Review existing user endpoints (`GET /api/v1/admin/users` exists)
- [ ] Add user detail endpoint (`GET /api/v1/admin/users/{user_id}`)
- [ ] Add user update endpoint (`PUT /api/v1/admin/users/{user_id}`)
- [ ] Add user activation/deactivation endpoint (`POST /api/v1/admin/users/{user_id}/toggle-active`)
- [ ] Add user role management endpoint (`PUT /api/v1/admin/users/{user_id}/roles`)
- [ ] Add user search and filtering (by role, email verified, active status)
- [ ] Add user statistics endpoint (total users, by role, by provider)
- [ ] Add user deletion endpoint (soft delete or hard delete)

**Deliverables**:
- Complete CRUD API for user management
- User filtering and search working

#### D2: Frontend User Management UI
**Tasks**:
- [ ] Create admin users list page (`/admin/users`)
- [ ] User list table with: name, email, roles, provider, verified status, active status
- [ ] User search and filters (role, provider, verified, active)
- [ ] User detail page (`/admin/users/[id]`)
- [ ] User edit form (name, email, phone, roles)
- [ ] Role assignment UI (multi-select)
- [ ] Activate/deactivate toggle
- [ ] User statistics cards on admin dashboard
- [ ] Delete user functionality with confirmation

**Deliverables**:
- Admin can view, search, filter, edit, and manage users
- User management interface complete

**Estimated Complexity**: Medium

---

### Phase E: User Profile System
**Priority**: MEDIUM  
**Duration**: 3-4 weeks  
**Dependencies**: Phase A (email), Phase B (social login)

#### E1: Database Schema & Models
**Tasks**:
- [ ] Create UserProfile model (or extend User model)
- [ ] Add profile fields: avatar_url, bio, location, website, social links
- [ ] Add profile visibility settings (public/private)
- [ ] Add profile completion tracking
- [ ] Create migration for profile fields
- [ ] Add indexes for profile searches

**Deliverables**:
- Database schema supports user profiles
- Migration ready

#### E2: Backend Profile API
**Tasks**:
- [ ] Create profile endpoints:
  - `GET /api/v1/users/me/profile` - Get own profile
  - `PUT /api/v1/users/me/profile` - Update own profile
  - `GET /api/v1/users/{user_id}/profile` - Get public profile
  - `POST /api/v1/users/me/profile/avatar` - Upload avatar
- [ ] Implement file upload for avatars (image storage)
- [ ] Add image validation and processing
- [ ] Add profile visibility logic
- [ ] Add profile completion calculation
- [ ] Update user repository with profile methods

**Deliverables**:
- Profile API endpoints working
- Avatar upload functional

#### E3: Frontend Profile Pages
**Tasks**:
- [ ] Create user profile page (`/profile` or `/users/[id]`)
- [ ] Profile edit page (`/profile/edit`)
- [ ] Profile form with all fields
- [ ] Avatar upload component with preview
- [ ] Profile visibility settings
- [ ] Profile completion indicator
- [ ] Public profile view (for other users)
- [ ] Profile navigation in header
- [ ] Link to profile from various pages

**Deliverables**:
- Users can view and edit their profiles
- Public profiles viewable by others
- Avatar upload working

**Estimated Complexity**: Large (as expected)

---

### Phase F: Phone Number Validation & SMS Preparation
**Priority**: LOW  
**Duration**: 1 week  
**Dependencies**: None

#### F1: Database Schema Preparation
**Tasks**:
- [ ] Review phone field in User model (already exists)
- [ ] Add `phone_verified` boolean field to User model
- [ ] Add `phone_verification_code` field (temporary)
- [ ] Add `phone_verification_code_expires_at` field
- [ ] Create migration for phone verification fields
- [ ] Add phone number format validation utility

**Deliverables**:
- Database ready for phone verification
- Phone validation utility ready

#### F2: Phone Validation Backend
**Tasks**:
- [ ] Create phone number validation utility (format checking)
- [ ] Add phone validation endpoint (`POST /api/v1/auth/validate-phone`)
- [ ] Add phone verification code generation (for future SMS)
- [ ] Store verification codes (for future SMS integration)
- [ ] Add phone update endpoint with validation

**Deliverables**:
- Phone number format validation working
- Infrastructure ready for SMS integration

#### F3: SMS Service Preparation
**Tasks**:
- [ ] Research SMS service providers (Twilio, AWS SNS, etc.)
- [ ] Create SMS utility module structure (`app/utils/sms.py`)
- [ ] Add SMS configuration to settings (placeholder)
- [ ] Design SMS sending interface (not implemented yet)
- [ ] Document SMS integration plan

**Deliverables**:
- SMS infrastructure prepared
- Documentation for future SMS integration

**Estimated Complexity**: Low-Medium

---

## Implementation Order & Timeline

### Recommended Sequence

1. **Week 1-2**: Phase A (Email Infrastructure & Verification)
   - Foundation for email verification
   - Enables email notifications

2. **Week 2-4**: Phase B (Social Login) - Parallel with Phase A
   - Can start immediately
   - No blocking dependencies

3. **Week 3-4**: Phase C (Dynamic Ticket Phases) - Parallel with Phase B
   - Independent feature
   - High priority for event management

4. **Week 4-5**: Phase D (Admin User Management)
   - Quick win
   - Useful for managing users

5. **Week 5-8**: Phase E (User Profiles)
   - Large implementation
   - Can start after email/social login

6. **Week 8-9**: Phase F (Phone/SMS Preparation)
   - Low priority
   - Preparation for future features

### Critical Path

```
Phase A (Email) → Phase A2 (Email Verification)
                ↓
Phase B (Social Login) → Phase E (User Profiles)
                ↓
Phase C (Dynamic Phases) → (Independent)
                ↓
Phase D (User Management) → (Can start anytime)
                ↓
Phase F (Phone/SMS Prep) → (Low priority)
```

---

## Dependencies Matrix

| Phase | Depends On | Blocks |
|-------|-----------|--------|
| A1: Email Service | None | A2, E |
| A2: Email Verification | A1 | E |
| B: Social Login | None | E |
| C: Dynamic Phases | None | None |
| D: User Management | A2 (recommended) | None |
| E: User Profiles | A2, B | None |
| F: Phone/SMS Prep | None | None |

---

## Risk Assessment

### High Risk
- **Social Login**: OAuth integration complexity, provider-specific issues
- **Dynamic Phases**: Breaking changes to existing purchase flow
- **User Profiles**: Large scope, potential scope creep

### Medium Risk
- **Email Service**: Gmail SMTP configuration, rate limits
- **Email Verification**: Token management, expiration handling

### Low Risk
- **User Management**: Straightforward CRUD operations
- **Phone/SMS Prep**: Just preparation, no implementation

---

## Success Criteria

### Phase A: Email
- ✅ Emails sent successfully via Gmail SMTP
- ✅ Users receive verification emails
- ✅ Email verification flow works end-to-end

### Phase B: Social Login
- ✅ All 4 providers (Gmail, Facebook, Instagram, Soundcloud) working
- ✅ OAuth flow complete for all providers
- ✅ Account linking functional

### Phase C: Dynamic Phases
- ✅ Admin can add unlimited phases to events
- ✅ Purchase flow works with any number of phases
- ✅ Phase transitions calculated dynamically

### Phase D: User Management
- ✅ Admin can view, search, filter, edit users
- ✅ Role management working
- ✅ User statistics displayed

### Phase E: User Profiles
- ✅ Users can create and edit profiles
- ✅ Avatar upload working
- ✅ Public profiles viewable

### Phase F: Phone/SMS Prep
- ✅ Database schema ready
- ✅ Phone validation utility ready
- ✅ SMS integration plan documented

---

## Testing Strategy

### Unit Tests
- Email sending utilities
- Email verification token generation
- Social auth token verification
- Phone number validation
- Phase calculation logic

### Integration Tests
- Email sending end-to-end
- Social login flow for each provider
- Phase CRUD operations
- User management operations
- Profile CRUD operations

### E2E Tests
- Complete email verification flow
- Complete social login flow (each provider)
- Event creation with dynamic phases
- User profile creation and editing
- Admin user management flow

---

## Documentation Requirements

### Technical Documentation
- [ ] Email service setup guide
- [ ] Social login integration guide
- [ ] Dynamic phases API documentation
- [ ] User management API documentation
- [ ] User profile API documentation
- [ ] SMS integration plan document

### User Documentation
- [ ] Email verification guide
- [ ] Social login guide
- [ ] User profile guide
- [ ] Admin user management guide

---

## Notes

1. **Social Login**: Instagram and Soundcloud may have limited OAuth APIs. Research required.
2. **Dynamic Phases**: Consider backward compatibility with existing events that have 3 phases.
3. **User Profiles**: Start with MVP (basic fields) and expand later.
4. **Email Service**: Consider using email service provider (SendGrid, Mailgun) instead of direct SMTP for better deliverability.
5. **Phone/SMS**: This is preparation only. Actual SMS implementation can be Phase 9+.

---

## Next Steps

1. **Review this roadmap** with stakeholders
2. **Prioritize phases** based on business needs
3. **Assign developers** to phases
4. **Set up project tracking** (GitHub issues, Jira, etc.)
5. **Begin Phase A** (Email Infrastructure)

---

**Document Version**: 1.0  
**Status**: Ready for Review
