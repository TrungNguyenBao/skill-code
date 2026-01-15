---
description: QA & QC - Testing & Quality Assurance
auto_execution_mode: 3
---

# QAQC Skill - Quality Assurance & Testing

Comprehensive workflow for QA engineers covering test planning, test case creation, bug reporting, and quality metrics.

## How to Use This Workflow

When testing features or ensuring quality, follow this systematic approach:

### Prerequisites

- ✅ Requirements and acceptance criteria from BA
- ✅ Feature implementation completed by BE/FE
- ✅ Test environment configured
- ✅ Access to testing tools

---

## Step 1: Test Planning

### 1.1 Test Strategy

**Test Types:**
```
Unit Tests (Developer)
├─ Test individual functions/methods
├─ Fast, isolated, no dependencies
└─ Coverage goal: 80%+

Integration Tests  
├─ Test component interactions
├─ API endpoints, database connections
└─ Coverage: Critical paths

E2E Tests (End-to-End)
├─ Test complete user workflows
├─ Browser automation (Playwright, Selenium)
└─ Coverage: Main user journeys

Performance Tests
├─ Load testing (many concurrent users)
├─ Stress testing (find breaking points)
└─ Tools: k6, JMeter, Artillery

Security Tests
├─ Penetration testing
├─ OWASP Top 10 vulnerabilities
└─ Tools: OWASP ZAP, Burp Suite
```

### 1.2 Test Plan Template

```markdown
# Test Plan - [Feature Name]

## 1. Introduction
**Feature:** User Registration
**Version:** 1.0
**Test Lead:** [QA Engineer Name]
**Test Period:** Jan 16-20, 2026

## 2. Scope

### In Scope
- User registration form
- Email verification
- Password validation
- Error handling

### Out of Scope
- Social login (OAuth)
- Password reset (separate feature)

## 3. Test Strategy

| Test Type | Coverage | Tools | Timeline |
|-----------|----------|-------|----------|
| Unit | Backend services | Jest | Jan 16 |
| Integration | API endpoints | Supertest | Jan 17 |
| E2E | Registration flow | Playwright | Jan 18-19 |
| Manual | UI/UX Review | Manual | Jan 20 |

## 4. Test Environment
- **URL:** https://staging.example.com
- **Database:** PostgreSQL (staging DB)
- **Credentials:** test_user@example.com

## 5. Test Data
- Valid emails: test1@example.com, test2@example.com
- Invalid emails: invalid-email, @example.com
- Test passwords: Test123!, WeakPass

## 6. Entry Criteria
- [ ] Feature deployed to staging
- [ ] Test environment ready
- [ ] Test data prepared
- [ ] All blockers resolved

## 7. Exit Criteria
- [ ] All test cases executed
- [ ] 95%+ test cases passing
- [ ] No critical/high bugs open
- [ ] Performance benchmarks met

## 8. Risks
- Database connection issues on staging
- Email service delays
- Third-party API downtime

## 9. Deliverables
- Test cases document
- Test execution report
- Bug reports
- Test metrics dashboard
```

---

## Step 2: Test Case Creation

### 2.1 Test Case Template

**Format:**
```
Test Case ID: TC-[Feature]-[Number]
Title: [Brief description]
Priority: Critical / High / Medium / Low
Type: Functional / Non-Functional / Regression
Preconditions: [Setup required]
Test Steps: [Numbered steps]
Test Data: [Input values]
Expected Result: [What should happen]
Actual Result: [What actually happened]
Status: Pass / Fail / Blocked / Not Run
```

**Example - User Registration:**

```
Test Case ID: TC-REG-001
Title: Successful registration with valid data
Priority: Critical
Type: Functional
Author: QA Engineer
Date: 2026-01-16

Preconditions:
- User is on registration page
- Email "newuser@example.com" doesn't exist in database

Test Steps:
1. Navigate to /register
2. Enter email: newuser@example.com
3. Enter password: SecurePass123!
4. Enter name: John Doe
5. Click "Register" button

Test Data:
- Email: newuser@example.com
- Password: SecurePass123!
- Name: John Doe

Expected Result:
1. Registration form submits successfully
2. User sees success message: "Check your email to verify"
3. Verification email sent to newuser@example.com
4. User record created in database
5. Password is hashed (not stored in plain text)
6. User redirected to email verification page

Actual Result:
[To be filled during execution]

Status: Not Run
```

### 2.2 Test Case Categories

**Positive Test Cases (Happy Path):**
```
TC-REG-001: Valid registration
TC-LOG-001: Valid login
TC-PROD-001: Search with valid keyword
```

**Negative Test Cases:**
```
TC-REG-002: Registration with duplicate email
TC-REG-003: Registration with invalid email format
TC-REG-004: Registration with weak password
TC-REG-005: Registration with missing required fields
TC-REG-006: Registration with SQL injection attempt
TC-REG-007: Registration with XSS payload
```

**Boundary Test Cases:**
```
TC-REG-008: Password exactly 8 characters
TC-REG-009: Password 7 characters (should fail)
TC-REG-010: Email 254 characters (max length)
TC-REG-011: Email 255 characters (should fail)
```

**Edge Cases:**
```
TC-REG-012: Special characters in name field
TC-REG-013: Multiple spaces in input
TC-REG-014: Emoji in name field
TC-REG-015: Very long name (1000 chars)
```

### 2.3 BDD Test Cases (Gherkin)

```gherkin
Feature: User Registration

Background:
  Given I am on the registration page

Scenario: Successful registration
  When I fill in the following:
    | field    | value                |
    | email    | test@example.com     |
    | password | SecurePass123!       |
    | name     | Test User            |
  And I click "Register"
  Then I should see "Check your email to verify"
  And a verification email should be sent to "test@example.com"
  And I should be redirected to "/verify-email"

Scenario Outline: Invalid registration attempts
  When I fill in "email" with "<email>"
  And I fill in "password" with "<password>"
  And I click "Register"
  Then I should see error "<error_message>"

  Examples:
    | email           | password     | error_message                |
    | invalid-email   | Test123!     | Enter a valid email          |
    | test@test.com   | weak         | Password must be 8+ chars    |
    | existing@.com   | Test123!     | Email already registered     |
    |                 | Test123!     | Email is required            |
```

---

## Step 3: Bug Reporting

### 3.1 Bug Report Template

```markdown
# Bug Report

## Bug ID
BUG-[YYYY-MM-DD]-[Number] (e.g., BUG-2026-01-16-001)

## Summary
[One-line description of the bug]

## Severity
- **Critical**: System crash, data loss, security breach
- **High**: Major feature broken, no workaround
- **Medium**: Feature partially broken, workaround exists
- **Low**: Minor issue, cosmetic

## Priority
- **P0**: Fix immediately (blocks release)
- **P1**: Fix before release
- **P2**: Fix in next release
- **P3**: Fix when possible

## Environment
- OS: Windows 11
- Browser: Chrome 120
- URL: https://staging.example.com
- Build: v1.2.3

## Steps to Reproduce
1. Navigate to /register
2. Enter email: test@example.com
3. Enter password: Test123!
4. Click "Register"

## Expected Behavior
User should see success message and receive verification email

## Actual Behavior
User sees error "Database connection failed"
No email sent

## Screenshots/Videos
[Attach screenshot or screen recording]

## Logs
```
[2026-01-16 00:00:00] ERROR: Connection timeout
Database: PostgreSQL connection pool exhausted
```

## Additional Context
- Happens intermittently (~30% of attempts)
- Only on staging,not on local dev
- Started after deployment of v1.2.3

## Assignee
[Developer name]

## Status
Open / In Progress / Fixed / Wont Fix / Duplicate
```

### 3.2 Bug Severity Matrix

| **Severity** | **Impact** | **Example** | **SLA** |
|------------|----------|----------|-------|
| **Critical** | App unusable, data loss | Cannot login, payment fails | < 4 hours |
| **High** | Major feature broken | Search doesn't work | < 24 hours |
| **Medium** | Feature degraded | Slow loading, minor errors | < 3 days |
| **Low** | Cosmetic, typos | Button misaligned, typo | Next sprint |

---

## Step 4: Test Execution

### 4.1 Test Execution Report

```markdown
# Test Execution Report

**Feature:** User Registration
**Test Cycle:** Sprint 5
**Test Period:** Jan 16-20, 2026
**Tested By:** QA Team

## Summary

| Status | Count | Percentage |
|--------|-------|------------|
| ✅ Pass | 45 | 90% |
| ❌ Fail | 3 | 6% |
| ⏸️ Blocked | 2 | 4% |
| ⏭️ Not Run | 0 | 0% |
| **Total** | **50** | **100%** |

## Test Results by Type

| Type | Total | Pass | Fail |
|------|-------|------|------|
| Unit | 20 | 20 | 0 |
| Integration | 15 | 14 | 1 |
| E2E | 10 | 8 | 2 |
| Performance | 5 | 3 | 2 |

## Failed Test Cases

| ID | Title | Severity | Assignee | Status |
|----|-------|----------|----------|--------|
| TC-REG-015 | Long name crash | High | Dev1 | In Progress |
| TC-REG-020 | Concurrent registration | Medium | Dev2 | Fixed |
| TC-PERF-001 | Load test 1000 users | High | DevOps | Open |

## Bugs Found

| ID | Summary | Severity | Status |
|----|---------|----------|--------|
| BUG-2026-01-16-001 | Database timeout | Critical | Fixed |
| BUG-2026-01-16-002 | Email delay | Medium | Open |
| BUG-2026-01-16-003 | UI misalignment | Low | Won't Fix |

## Metrics

- **Test Coverage**: 95%
- **Defect Density**: 0.6 bugs/test case
- **Pass Rate**: 90%
- **Automation Coverage**: 80%

## Recommendations

- ✅ Feature ready for release (3 minor bugs acceptable)
- ⚠️ Fix BUG-001 and BUG-002 before release
- 📈 Add more performance test coverage
```

---

## Step 5: Quality Metrics

### 5.1 Key QA Metrics

**Test Coverage:**
```
Code Coverage = (Lines Covered / Total Lines) × 100%
Target: > 80%

Branch Coverage = (Branches Covered / Total Branches) × 100%
Target: > 75%
```

**Defect Metrics:**
```
Defect Density = Total Defects / Size (KLOC or function points)
Defect Removal Efficiency = (Defects Found Before Release / Total Defects) × 100%
Defect Leakage = (Defects Found in Production / Total Defects) × 100%
Target: < 5%
```

**Test Effectiveness:**
```
Pass Rate = (Passed Tests / Total Tests) × 100%
Target: > 95%

Test Automation Rate = (Automated Tests / Total Tests) × 100%
Target: > 70%
```

### 5.2 Quality Dashboard

```markdown
## Quality Dashboard - Sprint 5

### Test Execution
- Total Tests: 250
- Pass: 238 (95.2%)
- Fail: 8 (3.2%)
- Blocked: 4 (1.6%)

### Defects
- Critical: 0
- High: 2
- Medium: 5
- Low: 8
- Total: 15

### Coverage
- Unit Test: 85%
- Integration: 78%
- E2E: 65%
- Overall: 82%

### Trends (vs Last Sprint)
- Defect Density: 0.6 → 0.4 ⬇️ 33% improvement
- Pass Rate: 92% → 95% ⬆️ 3% improvement
- Automation: 65% → 70% ⬆️ 5% improvement
```

---

## QA Best Practices

### Do's ✅
- **Test early**: Start testing during development (shift-left)
- **Automate repetitive tests**: Save time, increase coverage
- **Write clear bug reports**: Include steps to reproduce
- **Regression test**: Ensure fixes don't break other features
- **Test edge cases**: Empty inputs, max lengths, special chars
- **Performance test**: Don't just test functionality
- **Document everything**: Test plans, cases, results

### Don'ts ❌
- **Don't skip exploratory testing**: Automation doesn't catch everything
- **Don't test only happy paths**: Negative testing is critical
- **Don't assume**: Verify every requirement
- **Don't report duplicate bugs**: Check existing issues first
- **Don't test in production**: Use staging/QA environment
- **Don't ignore minor bugs**: They accumulate into bigger issues

---

## Testing Tools

### Unit Testing
- **JavaScript/TypeScript**: Jest, Mocha, Vitest
- **Python**: Pytest, unittest
- **Java**: JUnit, TestNG

### Integration Testing
- **API Testing**: Postman, Insomnia, Supertest
- **Database**: SQL test fixtures

### E2E Testing
- **Web**: Playwright, Cypress, Selenium
- **Mobile**: Appium, Detox

### Performance Testing
- **Load Testing**: k6, JMeter, Artillery
- **Monitoring**: Lighthouse, WebPageTest

### Bug Tracking
- **Tools**: Jira, Linear, GitHub Issues, Azure DevOps

### Test Management
- **Tools**: TestRail, Zephyr, qTest

---

## QAQC Checklist

### Test Planning
- [ ] Test plan created and reviewed
- [ ] Test environment configured
- [ ] Test data prepared
- [ ] Entry/exit criteria defined

### Test Design
- [ ] Test cases written for all requirements
- [ ] Positive & negative cases included
- [ ] Edge cases covered
- [ ] Test cases reviewed by team

### Test Execution
- [ ] All test cases executed
- [ ] Results documented
- [ ] Screenshots/logs attached
- [ ] Bugs reported with details

### Regression Testing
- [ ] Regression test suite updated
- [ ] All critical paths tested
- [ ] No new bugs introduced

### Release Readiness
- [ ] 95%+ pass rate achieved
- [ ] No critical/high bugs open
- [ ] Performance benchmarks met
- [ ] Sign-off from QA lead

---

## Next Steps

After QAQC complete:
1. **Sign off on release** if quality gates met
2. **Coordinate with DevOps** for deployment using `/devops`
3. **Monitor production** for any issues
4. **Create regression test suite** for future sprints

## QA Resources

**Learning:**
- ISTQB Certification
- Test Automation University
- Ministry of Testing

**Communities:**
- Quality Assurance subreddit
- Test Automation Slack
- QA Stack Exchange
