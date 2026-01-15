# Software Development Skills - Complete SDLC

Full bộ skills cho phát triển phần mềm chuyên nghiệp, bao gồm 4 vai trò: **BA**, **BE**, **QAQC**, **DevOps**.

## 🎯 Overview

Bộ skills này cung cấp workflows, templates, và best practices cho toàn bộ Software Development Lifecycle (SDLC) - từ phân tích yêu cầu đến deployment production.

**4 Role-Based Skills:**
- 👔 **BA (Business Analyst)** - Requirements gathering, user stories, acceptance criteria
- ⚙️ **BE (Backend Engineer)** - API design, database, authentication, security
- 🧪 **QAQC (Quality Assurance)** - Test planning, test cases, bug reports, quality metrics
- 🚀 **DevOps** - CI/CD, deployment, monitoring, infrastructure

---

## 📦 Features

### BA Skill
- ✅ Requirements gathering framework với 20 interview questions
- ✅ User story templates (As a... I want... So that...)
- ✅ Acceptance criteria guidelines (Gherkin/BDD format)
- ✅ Prioritization frameworks (MoSCoW, RICE)
- ✅ Stakeholder communication templates
- ✅ Requirements document templates

### BE Skill
- ✅ RESTful API design best practices
- ✅ Authentication patterns (JWT, OAuth, Session)
- ✅ Database optimization (indexing, query optimization, N+1 prevention)
- ✅ Caching strategies (Redis implementation)
- ✅ Error handling patterns
- ✅ Security best practices (OWASP Top 10)
- ✅ Performance optimization techniques

### QAQC Skill
- ✅ Test planning framework
- ✅ Test case templates (functional, negative, boundary, edge cases)
- ✅ Bug report templates với severity levels
- ✅ Test execution reports
- ✅ Quality metrics (coverage, defect density, pass rate)
- ✅ Testing tools recommendations (Jest, Playwright, Cypress, k6)

### DevOps Skill
- ✅ CI/CD pipeline templates (GitHub Actions, GitLab CI, Jenkins)
- ✅ Deployment strategies (Blue-Green, Canary, Rolling)
- ✅ Infrastructure as Code (Terraform examples)
- ✅ Monitoring setup (Prometheus + Grafana)
- ✅ Logging implementation (Winston, ELK Stack)
- ✅ Secrets management (AWS Secrets Manager, Vault)
- ✅ Container orchestration (Docker, Kubernetes)

---

## 🚀 Installation

### Antigravity (Gemini)

Copy các folders sau vào project của bạn:

```bash
# Clone repo
git clone https://github.com/TrungNguyenBao/skill-code.git

# Copy workflows
cp -r skill-code/.agent/workflows/*.md your-project/.agent/workflows/

# Copy skill definitions
cp -r skill-code/.gemini/skills/* your-project/.gemini/skills/
```

**File structure sau khi copy:**
```
your-project/
├── .agent/
│   └── workflows/
│       ├── ba.md
│       ├── be.md
│       ├── qaqc.md
│       └── devops.md
└── .gemini/
    └── skills/
        ├── ba/SKILL.md
        ├── be/SKILL.md
        ├── qaqc/SKILL.md
        └── devops/SKILL.md
```

---

## 💡 Usage

### Antigravity / Gemini

Sử dụng slash commands để activate skills:

```bash
# BA - Requirements gathering
/ba analyze requirements for user authentication feature

# BE - API implementation
/be design REST API for product catalog with authentication

# QAQC - Testing
/qaqc create test plan for checkout flow

# DevOps - Deployment
/devops setup CI/CD pipeline with GitHub Actions
```

### Example Workflow

**Complete feature development:**

```bash
# Step 1: BA - Gather requirements
/ba create user stories for payment integration

# Step 2: BE - Implement backend
/be implement Stripe payment API with webhook handling

# Step 3: QAQC - Test feature
/qaqc create test cases for payment flow including edge cases

# Step 4: DevOps - Deploy
/devops setup blue-green deployment for payment service
```

---

## 📚 Documentation

### BA Workflows
- **Requirements Gathering**: Stakeholder interviews, elicitation techniques
- **User Stories**: Template format, prioritization (MoSCoW, RICE)
- **Acceptance Criteria**: Gherkin/BDD format, scenario examples
- **Documentation**: Requirements doc, status reports

### BE Workflows
- **API Design**: RESTful conventions, HTTP methods, status codes
- **Authentication**: JWT implementation, RBAC, session management
- **Database**: Indexing strategies, query optimization, migrations
- **Security**: OWASP protection, input validation, rate limiting
- **Performance**: Caching (Redis), connection pooling, compression

### QAQC Workflows
- **Test Planning**: Test strategy, test pyramid, entry/exit criteria
- **Test Cases**: Positive/negative/boundary/edge case templates
- **Bug Reports**: Severity matrix, reproduction steps, logs
- **Quality Metrics**: Coverage, defect density, pass rate formulas
- **Tools**: Jest, Playwright, Cypress, k6, Postman

### DevOps Workflows
- **CI/CD**: GitHub Actions, GitLab CI pipeline templates
- **Deployment**: Blue-Green, Canary, Rolling update strategies
- **Infrastructure**: Terraform IaC examples, AWS/GCP/Azure
- **Monitoring**: Prometheus, Grafana dashboards, health checks
- **Logging**: Winston, ELK Stack, centralized logging
- **Secrets**: Environment variables, AWS Secrets Manager

---

## 🔄 Complete SDLC Flow

```
┌──────────┐     ┌──────────┐     ┌──────────┐     ┌──────────┐
│    BA    │ ──▶ │    BE    │ ──▶ │   QAQC   │ ──▶ │  DevOps  │
│ Requirements │  │ Implementation │ │  Testing   │  │ Deployment │
└──────────┘     └──────────┘     └──────────┘     └──────────┘
     │                                                    │
     └───────────────── Monitoring & Feedback ───────────┘
```

1. **BA**: Gather requirements → Create user stories → Define acceptance criteria
2. **BE**: Design API → Implement features → Optimize database → Secure endpoints
3. **QAQC**: Plan tests → Execute test cases → Report bugs → Verify quality
4. **DevOps**: Setup CI/CD → Deploy → Monitor → Scale

---

## 🛠️ Tech Stack Coverage

### Frontend
React, Next.js, Vue, Nuxt, Svelte, Angular

### Backend
Node.js (Express, Fastify, NestJS), Python (FastAPI, Django), Java (Spring Boot), Go

### Databases
PostgreSQL, MySQL, MongoDB, Redis, DynamoDB

### Testing
Jest, Mocha, Pytest, Playwright, Cypress, Selenium, k6, JMeter

### DevOps
Docker, Kubernetes, GitHub Actions, GitLab CI, Terraform, AWS, GCP, Azure

### Monitoring
Prometheus, Grafana, ELK Stack, Datadog, Sentry

---

## 📝 Templates Included

### BA Templates
- Stakeholder interview questions (20 questions)
- User story format
- Requirements document
- Weekly status report

### BE Templates
- API endpoint naming conventions
- JWT authentication implementation
- Error handling classes
- Database migration scripts

### QAQC Templates
- Test plan document
- Test case format
- Bug report template
- Test execution report

### DevOps Templates
- GitHub Actions CI/CD pipeline
- GitLab CI pipeline
- Terraform infrastructure
- Docker Compose setup
- Monitoring dashboard config

---

## 🎓 Best Practices

### BA
- Ask "Why" to understand root cause
- Document everything in writing
- Involve users early for feedback
- Prioritize ruthlessly
- Think edge cases

### BE
- Use RESTful conventions
- Implement proper auth (JWT/OAuth)
- Optimize database queries
- Cache frequently accessed data
- Handle errors gracefully
- Follow OWASP security guidelines

### QAQC
- Test early (shift-left)
- Automate repetitive tests
- Write clear bug reports
- Regression test after fixes
- 80%+ code coverage target
- Performance test critical paths

### DevOps
- Infrastructure as Code (IaC)
- Automate everything
- Monitor all services
- Centralize logging
- Secure secrets properly
- Plan for rollbacks

---

## 🤝 Contributing

Contributions welcome! Mở issue hoặc tạo pull request.

## 📄 License

MIT License

---

## 🌟 Star History

Nếu bộ skills này hữu ích, hãy cho repo một ⭐!

## 📧 Contact

Created by [TrungNguyenBao](https://github.com/TrungNguyenBao)
