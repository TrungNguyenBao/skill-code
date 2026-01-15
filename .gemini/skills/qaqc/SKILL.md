---
name: qaqc
description: "Quality assurance and testing. Actions: test, verify, validate, qa, qc, quality assurance, quality control, create test cases, write tests, plan testing, execute tests, report bugs, track defects. Projects: testing, quality assurance, test automation, manual testing, regression testing. Topics: test cases, test plan, bug report, defect tracking, unit testing, integration testing, E2E testing, end-to-end, acceptance testing, performance testing, load testing, security testing, test coverage, regression, Selenium, Playwright, Cypress, Jest, Pytest, test metrics, quality gates."
---

# QAQC Skill - Quality Assurance & Testing

Quality assurance workflow covering test planning, test case creation, bug reporting, and quality metrics.

## Prerequisites

- ✅ Requirements from BA
- ✅ Feature implementation complete
- ✅ Test environment configured
- ✅ Testing tools ready

## Test Strategy

**Test Pyramid:**
```
     E2E (5-10%)      Few, slow, expensive
   Integration (20-30%) Some, medium speed
  Unit Tests (60-75%)   Many, fast, cheap
```

**Test Types:**
- **Unit**: Individual functions/methods
- **Integration**: Component interactions, API endpoints
- **E2E**: Complete user workflows
- **Performance**: Load, stress testing
- **Security**: OWASP vulnerabilities

---

## Test Planning

### Test Plan Template

```markdown
# Test Plan - [Feature]

## Scope
- In: Registration form, email verification
- Out: Social login, password reset

## Strategy
| Type | Coverage | Tools |
|------|----------|-------|
| Unit | Backend | Jest |
| Integration | API | Supertest |
| E2E | User flow | Playwright |

## Entry Criteria
- [ ] Feature deployed to staging
- [ ] Test data prepared

## Exit Criteria
- [ ] 95%+ pass rate
- [ ] No critical bugs open
```

---

## Test Case Creation

### Template

```
ID: TC-[Feature]-[Number]
Title: [Description]
Priority: Critical/High/Medium/Low
Type: Functional/Regression/Performance

Preconditions: [Setup required]
Steps: [Numbered list]
Test Data: [Input values]
Expected: [What should happen]
Actual: [Filled during execution]
Status: Pass/Fail/Blocked
```

### Example

```
ID: TC-REG-001
Title: Successful registration with valid data
Priority: Critical

Steps:
1. Navigate to /register
2. Enter email: test@example.com
3. Enter password: SecurePass123!
4. Enter name: John Doe
5. Click "Register"

Expected:
- Success message shown
- Verification email sent
- User created in database
- Redirected to verify page

Status: Not Run
```

### Test Case Types

**Positive (Happy Path):**
- Valid inputs, expected flow

**Negative:**
- Invalid email format
- Weak password
- Duplicate email
- SQL injection attempt
- XSS payload

**Boundary:**
- Min/max lengths
- Edge values

**Edge Cases:**
- Special characters
- Empty inputs
- Very long inputs

---

## Bug Reporting

### Bug Report Template

```markdown
# Bug ID: BUG-2026-01-16-001

## Summary
[One-line description]

## Severity
- **Critical**: System crash, data loss
- **High**: Major feature broken
- **Medium**: Partial issue, has workaround
- **Low**: Minor, cosmetic

## Environment
- OS: Windows 11
- Browser: Chrome 120
- URL: https://staging.example.com

## Steps to Reproduce
1. Go to /register
2. Enter email: test@example.com
3. Click Register

## Expected vs Actual
Expected: Success message
Actual: Error "Database timeout"

## Screenshots/Logs
[Attach evidence]

## Status
Open / In Progress / Fixed
```

### Severity Matrix

| Severity | Impact | SLA |
|----------|--------|-----|
| Critical | App unusable | &lt; 4 hours |
| High | Major feature broken | &lt; 24 hours |
| Medium | Degraded function | &lt; 3 days |
| Low | Cosmetic | Next sprint |

---

## Test Execution Report

```markdown
# Test Execution - Sprint 5

## Summary
| Status | Count | % |
|--------|-------|---|
| ✅ Pass | 45 | 90% |
| ❌ Fail | 3 | 6% |
| ⏸️ Blocked | 2 | 4% |

## Failed Tests
| ID | Title | Severity | Assignee |
|----|-------|----------|----------|
| TC-015 | Long name crash | High | Dev1 |

## Bugs
| ID | Summary | Severity | Status |
|----|---------|----------|--------|
| BUG-001 | DB timeout | Critical | Fixed |

## Metrics
- Coverage: 95%
- Pass Rate: 90%
- Defect Density: 0.6
```

---

## Quality Metrics

**Test Coverage:**
```
Coverage = (Lines Covered / Total Lines) × 100%
Target: > 80%
```

**Defect Metrics:**
```
Defect Density = Defects / KLOC
Defect Leakage = (Prod Defects / Total) × 100%
Target: < 5%
```

**Test Effectiveness:**
```
Pass Rate = (Passed / Total) × 100%
Target: > 95%

Automation Rate = (Automated / Total) × 100%
Target: > 70%
```

---

## Testing Tools

**Unit:**
- JavaScript: Jest, Vitest, Mocha
- Python: Pytest, unittest
- Java: JUnit, TestNG

**E2E:**
- Playwright, Cypress, Selenium

**API:**
- Postman, Insomnia, Supertest

**Performance:**
- k6, JMeter, Artillery

**Bug Tracking:**
- Jira, Linear, GitHub Issues

---

## QA Checklist

### Planning
- [ ] Test plan created
- [ ] Environment configured
- [ ] Test data prepared

### Design
- [ ] Test cases for all requirements
- [ ] Positive & negative cases
- [ ] Edge cases covered

### Execution
- [ ] All tests executed
- [ ] Results documented
- [ ] Bugs reported with details

### Release
- [ ] 95%+ pass rate
- [ ] No critical bugs
- [ ] Performance met
- [ ] QA sign-off

---

## Best Practices

**Do's ✅**
- Test early (shift-left)
- Automate repetitive tests
- Write clear bug reports
- Regression test after fixes
- Test edge cases
- Document everything

**Don'ts ❌**
- Don't skip exploratory testing
- Don't test only happy paths
- Don't test in production
- Don't ignore minor bugs
- Don't report without verification

---

## Next Steps

1. Sign off if quality gates met
2. Coordinate deployment (`/devops`)
3. Monitor production
4. Create regression suite
