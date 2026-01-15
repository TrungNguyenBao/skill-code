---
description: Business Analyst - Requirements & User Stories
auto_execution_mode: 3
---

# BA Skill - Requirements Gathering & Analysis

Comprehensive workflow for Business Analysts to gather requirements, create user stories, define acceptance criteria, and communicate with stakeholders effectively.

## How to Use This Workflow

When you need to analyze business requirements or create user stories, follow this systematic approach:

### Prerequisites

- ✅ Access to stakeholders
- ✅ Understanding of business domain
- ✅ Project goals and constraints

---

## Step 1: Requirements Gathering

### 1.1 Stakeholder Identification

Identify all stakeholders:

| Stakeholder Type | Role | Input Needed | Priority |
|-----------------|------|--------------|----------|
| **Business Owner** | Decision maker | Business goals, budget, timeline | High |
| **End Users** | Product users | Pain points, needs, workflows | High |
| **Development Team** | Implementers | Technical feasibility, effort | High |
| **Product Manager** | Strategy | Product vision, roadmap | High |
| **Support Team** | Post-launch | Common issues, user feedback | Medium |
| **Legal/Compliance** | Regulations | Compliance requirements | Medium |

### 1.2 Requirements Elicitation Techniques

**Interview Questions Template:**

```markdown
## Business Context
1. What problem are we trying to solve?
2. Who are the target users?
3. What are the goals/objectives?
4. What is the expected ROI?

## Current State
5. What is the current process/workflow?
6. What are the pain points?
7. What manual steps exist?
8. What systems are currently used?

## Desired Future State
9. What would the ideal solution look like?
10. What features are must-have vs nice-to-have?
11. What are the success criteria?
12. What are the constraints (time, budget, tech)?

## Users & Usage
13. Who will use this system? (roles, personas)
14. How often will they use it?
15. What devices will they use? (desktop, mobile, tablet)
16. What is their technical proficiency?

## Integration & Data
17. What systems need to integrate?
18. What data sources are needed?
19. What reports/analytics are needed?
20. What compliance requirements exist? (GDPR, HIPAA, etc.)
```

**Workshop Activities:**
- **Brainstorming sessions**: Generate ideas with stakeholders
- **Process mapping**: Document current vs future workflows
- **Prototyping**: Create mockups for feedback
- **User journey mapping**: Understand user touchpoints

### 1.3 Requirements Categories

**Functional Requirements:**
```
FR-001: User Registration
- User can register with email and password
- Email verification required
- Password must meet security requirements (8+ chars, 1 uppercase, 1 number)
- User receives welcome email after registration

FR-002: Product Search
- Users can search products by name, category, or SKU
- Search results display within 2 seconds
- Results show product image, name, price, availability
- Support pagination (20 items per page)
```

**Non-Functional Requirements:**
```
NFR-001: Performance
- Page load time < 3 seconds
- API response time < 500ms for 95th percentile
- Support 1000 concurrent users

NFR-002: Security
- HTTPS only
- JWT-based authentication
- Role-based access control (RBAC)
- Password encryption (bcrypt)
- Session timeout after 30 minutes inactivity

NFR-003: Usability
- Mobile-responsive design
- WCAG 2.1 AA accessibility compliance
- Support Chrome, Firefox, Safari, Edge (latest 2 versions)
- Multi-language support (English, Vietnamese)

NFR-004: Reliability
- 99.9% uptime
- Automated daily backups
- Disaster recovery plan
- Error logging and monitoring
```

---

## Step 2: User Story Creation

### 2.1 User Story Format

**Standard Template:**
```
As a [user role]
I want to [action/feature]
So that [benefit/value]
```

**Example - E-commerce:**

```
User Story #1: Product Search
As a customer
I want to search for products by keyword
So that I can quickly find items I'm interested in

Priority: High
Story Points: 5
Sprint: Sprint 2

Acceptance Criteria:
- [ ] Search bar visible on all pages
- [ ] Search by product name, description, or SKU
- [ ] Results appear within 2 seconds
- [ ] Display matching products with image, name, price
- [ ] Show "No results found" message if no matches
- [ ] Support pagination (20 items per page)
- [ ] Highlight search term in results

Technical Notes:
- Use Elasticsearch for search indexing
- Implement debouncing (300ms) for search input
- Cache popular search queries (Redis)

Dependencies:
- Product catalog must be implemented first
- Requires search index setup

Definition of Done:
- [ ] Code reviewed and approved
- [ ] Unit tests written (80%+ coverage)
- [ ] Integration tests pass
- [ ] Accessibility tested
- [ ] Product Owner demo completed
- [ ] Merged to main branch
```

### 2.2 User Story Categories

**CRUD Operations:**
```
Create: As a user, I want to create/add [entity]
Read: As a user, I want to view/list [entities]
Update: As a user, I want to edit/modify [entity]
Delete: As a user, I want to remove/delete [entity]
```

**Examples by Feature:**

**Authentication:**
- As a user, I want to register an account so I can access the platform
- As a user, I want to login with email/password so I can access my account
- As a user, I want to reset my forgotten password so I can regain access
- As a user, I want to logout so I can secure my account

**Profile Management:**
- As a user, I want to view my profile so I can see my information
- As a user, I want to update my profile so I can keep information current
- As a user, I want to upload a profile picture so others can recognize me
- As a user, I want to change my password so I can maintain security

**Admin:**
- As an admin, I want to view all users so I can manage accounts
- As an admin, I want to deactivate users so I can remove bad actors
- As an admin, I want to view analytics so I can make data-driven decisions

### 2.3 Prioritization Framework

**MoSCoW Method:**
- **Must Have**: Critical for MVP, project fails without it
- **Should Have**: Important but not critical, workarounds exist
- **Could Have**: Nice to have, adds value but not essential
- **Won't Have**: Out of scope for current release

**RICE Scoring:**
```
Score = (Reach × Impact × Confidence) / Effort

Reach: How many users per time period? (1-10)
Impact: How much value per user? (0.25 = minimal, 3 = massive)
Confidence: How confident in estimates? (50% = 0.5, 100% = 1.0)
Effort: Person-months required (1-10)

Example:
Story: User login
Reach: 9 (90% of users will use this)
Impact: 3 (massive - required for access)
Confidence: 1.0 (100% confident)
Effort: 2 (2 person-weeks)

Score = (9 × 3 × 1.0) / 2 = 13.5 (High priority)
```

---

## Step 3: Acceptance Criteria

### 3.1 Well-Defined Criteria

**Good Acceptance Criteria:**
```
✅ SPECIFIC - Clear and unambiguous
✅ TESTABLE - Can be verified
✅ COMPLETE - Covers all scenarios
✅ MEASURABLE - Has clear success metrics
```

**Example - User Registration:**

```
Given I am on the registration page
When I enter valid registration details
Then I should see a success message
And receive a verification email
And be redirected to the email verification page

Scenarios:

Scenario 1: Successful Registration
Given email "user@example.com" doesn't exist
When I submit:
  - Email: user@example.com
  - Password: SecurePass123!
  - Name: John Doe
Then account is created
And verification email sent
And I see "Check your email to verify your account"

Scenario 2: Duplicate Email
Given email "existing@example.com" already exists
When I submit registration with "existing@example.com"
Then I see error "This email is already registered"
And no account is created

Scenario 3: Invalid Email Format
Given I enter invalid email "not-an-email"
When I submit the form
Then I see error "Please enter a valid email address"

Scenario 4: Weak Password
Given I enter password "123"
When I submit the form
Then I see error "Password must be at least 8 characters with 1 uppercase and 1 number"

Scenario 5: Missing Required Fields
Given I leave name field empty
When I submit the form
Then I see error "Name is required"
```

### 3.2 Gherkin Format (BDD)

```gherkin
Feature: User Login

Background:
  Given a user with email "test@example.com" and password "Test123!" exists

Scenario: Successful login with valid credentials
  Given I am on the login page
  When I enter email "test@example.com"
  And I enter password "Test123!"
  And I click the "Login" button
  Then I should be redirected to the dashboard
  And I should see "Welcome back, Test User"

Scenario: Failed login with incorrect password
  Given I am on the login page
  When I enter email "test@example.com"
  And I enter password "WrongPassword"
  And I click the "Login" button
  Then I should remain on the login page
  And I should see error "Invalid email or password"
  And the password field should be cleared

Scenario: Account locked after 5 failed attempts
  Given I have failed login 4 times
  When I fail login the 5th time
  Then I should see "Account locked for 15 minutes"
  And an email notification should be sent
```

---

## Step 4: Stakeholder Communication

### 4.1 Requirements Document Template

```markdown
# Requirements Document - [Project Name]

## Document Control
- **Version**: 1.0
- **Date**: 2026-01-16
- **Author**: [BA Name]
- **Reviewers**: [Stakeholder Names]
- **Status**: Draft / Under Review / Approved

## 1. Executive Summary
[2-3 paragraphs describing the project, goals, and expected outcomes]

## 2. Business Objectives
- Increase user engagement by 30%
- Reduce manual processing time by 50%
- Improve customer satisfaction score to 4.5/5

## 3. Stakeholders
| Name | Role | Responsibilities | Contact |
|------|------|------------------|---------|
| ... | ... | ... | ... |

## 4. Scope

### In Scope
- User authentication and authorization
- Product catalog management
- Shopping cart functionality
- Order processing

### Out of Scope
- Mobile app (Phase 2)
- Advanced analytics (Phase 2)
- Third-party marketplace integration (Future)

## 5. Functional Requirements
[Detailed FR-001, FR-002, etc.]

## 6. Non-Functional Requirements
[Detailed NFR-001, NFR-002, etc.]

## 7. User Stories
[High-priority user stories with acceptance criteria]

## 8. Assumptions & Constraints
- Users have modern web browsers
- Payment gateway API is available
- Budget: $50,000
- Timeline: 3 months

## 9. Risks
| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| ... | ... | ... | ... |

## 10. Success Criteria
- 90% of user stories completed
- All critical bugs resolved
- User acceptance testing passed
- Performance benchmarks met

## Appendix
- Wireframes
- User personas
- Process flows
```

### 4.2 Status Reporting Template

```markdown
# Weekly BA Status Report

**Week of**: Jan 15-19, 2026
**Project**: E-commerce Platform

## Accomplishments
- ✅ Completed 12 user stories for Sprint 3
- ✅ Conducted stakeholder workshop on checkout flow
- ✅ Refined acceptance criteria for payment integration
- ✅ Updated requirements document (v2.1)

## In Progress
- 🔄 Analyzing admin dashboard requirements
- 🔄 Creating user stories for inventory management
- 🔄 Reviewing API specifications with BE team

## Upcoming
- 📅 User acceptance testing for Sprint 2 (Jan 18)
- 📅 Sprint 3 planning meeting (Jan 20)
- 📅 Stakeholder demo (Jan 22)

## Blockers
- ⚠️ Waiting on legal review for payment terms
- ⚠️ Need clarification on tax calculation rules

## Metrics
- User stories created: 12
- User stories refined: 8
- Stakeholder meetings: 3
- Backlog size: 47 stories
```

---

## BA Deliverables Checklist

### Requirements Phase
- [ ] Stakeholder interviews completed
- [ ] Requirements document drafted
- [ ] Functional requirements defined
- [ ] Non-functional requirements defined
- [ ] Requirements reviewed by stakeholders
- [ ] Requirements approved and baselined

### User Story Phase
- [ ] User stories created for MVP
- [ ] Acceptance criteria defined
- [ ] Stories prioritized (MoSCoW/RICE)
- [ ] Story points estimated
- [ ] Dependencies identified
- [ ] Product backlog organized

### Communication Phase
- [ ] Requirements doc shared with team
- [ ] Kickoff meeting conducted
- [ ] Weekly status reports sent
- [ ] Sprint planning participation
- [ ] Backlog refinement sessions
- [ ] UAT coordination

---

## BA Best Practices

### Do's ✅
- **Ask "Why"**: Understand root cause, not just symptoms
- **Document everything**: Keep clear records of decisions
- **Involve users early**: Get feedback on mockups/prototypes
- **Be specific**: Avoid ambiguous terms like "user-friendly" or "fast"
- **Prioritize ruthlessly**: Not everything can be high priority
- **Think edge cases**: What if user enters invalid data?
- **Collaborate closely**: Work with UX, Dev, QA as one team

### Don'ts ❌
- **Don't assume**: Always validate assumptions with stakeholders
- **Don't skip acceptance criteria**: Stories without AC lead to confusion
- **Don't over-specify**: Let devs decide implementation details
- **Don't ignore technical constraints**: Understand what's feasible
- **Don't work in isolation**: BA is a collaborative role
- **Don't commit without capacity**: Check with dev team first

---

## Common BA Patterns

### Feature Toggle Pattern
```
As an admin
I want to enable/disable features via config
So that I can control rollout without code deployment

Acceptance:
- [ ] Feature flags stored in database
- [ ] Admin UI to toggle features on/off
- [ ] Features hidden when disabled
- [ ] No deployment needed to toggle
```

### Audit Trail Pattern
```
As a compliance officer
I want to see who changed what and when
So that I can maintain an audit trail

Acceptance:
- [ ] All data changes logged
- [ ] Log includes: user, timestamp, old value, new value
- [ ] Logs are immutable
- [ ] Logs retained for 7 years
- [ ] Export logs to CSV
```

### Soft Delete Pattern
```
As a user
I want to "delete" items without permanent removal
So that I can recover if I delete by mistake

Acceptance:
- [ ] Delete sets deleted_at timestamp
- [ ] Deleted items hidden from normal views
- [ ] Admin can view deleted items
- [ ] Restore functionality available for 30 days
- [ ] Hard delete after 30 days
```

---

## Next Steps

After BA work complete:
1. **Handoff to UX/UI** for design (if applicable)
2. **Sprint Planning** with dev team
3. **Move to BE skill** for API design using `/be`
4. **Track progress** in weekly status reports

## BA Tools & Resources

**Documentation:**
- Confluence / Notion for requirements
- Figma / Miro for process flows
- Lucidchart for diagrams

**Backlog Management:**
- Jira / Linear / Azure DevOps
- Monday.com / Asana

**Communication:**
- Slack / Teams for daily comm
- Zoom / Meet for meetings
- Loom for async updates
