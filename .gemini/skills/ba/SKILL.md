---
name: ba
description: "Business Analyst requirements gathering and user stories. Actions: analyze requirements, gather, elicit, define user stories, create acceptance criteria, document, prioritize, refine backlog, communicate stakeholders. Projects: new features, MVP planning, backlog creation, requirements analysis. Topics: functional requirements, non-functional requirements, user stories, acceptance criteria, as a user I want, given when then, BDD, Gherkin, MoSCoW, RICE, stakeholder management, requirements document."
---

# BA Skill - Requirements Gathering & Analysis

Comprehensive skill for Business Analysts to gather requirements, create user stories, define acceptance criteria, and communicate with stakeholders.

## How to Use This Skill

When analyzing business requirements or creating user stories, follow this systematic approach.

## Requirements Gathering Framework

### Stakeholder Identification

| Type | Role | Priority |
|------|------|----------|
| Business Owner | Decision maker, budget | High |
| End Users | Product users, pain points | High |
| Dev Team | Implementers, feasibility | High |
| Product Manager | Vision, roadmap | High |

### Interview Questions Template

**Business Context:**
- What problem are we solving?
- Who are the target users?
- What are the goals/objectives?

**Current State:**
- What is the current process?
- What are the pain points?
- What systems are currently used?

**Desired Future State:**
- What would the ideal solution look like?
- Must-have vs nice-to-have features?
- Success criteria?
- Constraints (time, budget, tech)?

### Requirements Categories

**Functional Requirements:**
```
FR-001: User Registration
- User can register with email and password
- Email verification required
- Password security requirements (8+ chars, 1 uppercase, 1 number)
```

**Non-Functional Requirements:**
```
NFR-001: Performance
- Page load time < 3 seconds
- API response time < 500ms
- Support 1000 concurrent users

NFR-002: Security
- HTTPS only
- JWT authentication
- Role-based access control
- Password encryption
```

---

## User Story Format

**Template:**
```
As a [user role]
I want to [action/feature]
So that [benefit/value]

Acceptance Criteria:
- [ ] Criterion 1
- [ ] Criterion 2
```

**Example:**
```
User Story: Product Search

As a customer
I want to search for products by keyword
So that I can quickly find items I'm interested in

Priority: High
Story Points: 5

Acceptance Criteria:
- [ ] Search bar visible on all pages
- [ ] Search by name, description, or SKU
- [ ] Results appear within 2 seconds
- [ ] Display matching products with image, name, price
- [ ] Show "No results found" if no matches
- [ ] Support pagination (20 items/page)

Technical Notes:
- Use Elasticsearch for indexing
- Implement debouncing (300ms)
- Cache popular queries (Redis)

Definition of Done:
- [ ] Code reviewed
- [ ] Unit tests (80%+ coverage)
- [ ] Integration tests pass
- [ ] PO demo completed
```

### Story Categories

**CRUD:**
- Create: As a user, I want to create/add [entity]
- Read: As a user, I want to view/list [entities]
- Update: As a user, I want to edit/modify [entity]
- Delete: As a user, I want to remove/delete [entity]

**Authentication:**
- Register, login, logout, password reset

**Profile Management:**
- View, update profile, upload picture, change password

---

## Prioritization Frameworks

**MoSCoW:**
- **Must Have**: Critical for MVP
- **Should Have**: Important but not critical
- **Could Have**: Nice to have
- **Won't Have**: Out of scope

**RICE Scoring:**
```
Score = (Reach × Impact × Confidence) / Effort

Reach: Users per period (1-10)
Impact: Value per user (0.25-3)
Confidence: Estimate confidence (0.5-1.0)
Effort: Person-months (1-10)
```

---

## Acceptance Criteria

### Gherkin Format (BDD)

```gherkin
Feature: User Login

Scenario: Successful login
  Given I am on the login page
  When I enter email "test@example.com"
  And I enter password "Test123!"
  And I click "Login"
  Then I should be redirected to dashboard
  And I should see "Welcome back"

Scenario: Failed login
  Given I am on the login page
  When I enter wrong password
  Then I should see "Invalid email or password"
  And password field should be cleared
```

### Criteria Checklist

Good acceptance criteria is:
- ✅ SPECIFIC - Clear and unambiguous
- ✅ TESTABLE - Can be verified
- ✅ COMPLETE - Covers all scenarios
- ✅ MEASURABLE - Has success metrics

---

## Requirements Document Template

```markdown
# Requirements - [Project Name]

## Executive Summary
[Project overview, goals, outcomes]

## Business Objectives
- Increase engagement by 30%
- Reduce manual work by 50%
- Improve satisfaction to 4.5/5

## Scope
### In Scope
- User authentication
- Product catalog
- Shopping cart

### Out of Scope
- Mobile app (Phase 2)
- Advanced analytics (Phase 2)

## Functional Requirements
[FR-001, FR-002, etc.]

## Non-Functional Requirements
[NFR-001, NFR-002, etc.]

## User Stories
[High-priority stories]

## Success Criteria
- 90% stories completed
- All critical bugs resolved
- UAT passed
```

---

## BA Best Practices

**Do's ✅**
- Ask "Why" to understand root cause
- Document all decisions
- Involve users early for feedback
- Be specific, avoid vague terms
- Prioritize ruthlessly
- Think edge cases
- Collaborate with UX, Dev, QA

**Don'ts ❌**
- Don't assume - validate everything
- Don't skip acceptance criteria
- Don't over-specify implementation
- Don't ignore technical constraints
- Don't work in isolation
- Don't commit without team capacity check

---

## Common Patterns

**Feature Toggle:**
```
As an admin
I want to enable/disable features via config
So that I can control rollout

AC:
- Feature flags in database
- Admin UI to toggle
- No deployment needed
```

**Audit Trail:**
```
As a compliance officer
I want to see who changed what and when
So that I maintain audit trail

AC:
- All changes logged
- Log: user, timestamp, old/new value
- Logs immutable
- 7 year retention
```

---

## Tools & Resources

**Documentation:** Confluence, Notion, Miro
**Backlog:** Jira, Linear, Azure DevOps
**Communication:** Slack, Zoom, Loom

## Next Steps

After BA work:
1. Handoff to UX/UI for design
2. Sprint Planning with dev team
3. Move to BE skill using `/be`
