---
name: devops
description: "DevOps deployment and infrastructure. Actions: deploy, setup CI/CD, configure pipeline, deploy to production, setup infrastructure, provision servers, configure monitoring, setup logging, create pipeline, automate deployment. Projects: deployment, CI/CD, infrastructure, DevOps automation, cloud deployment, containerization. Topics: GitHub Actions, GitLab CI, Jenkins, Docker, Kubernetes, AWS, GCP, Azure, Terraform, infrastructure as code, blue-green deployment, canary deployment, rolling update, monitoring, Prometheus, Grafana, logging, secrets management, health checks."
---

# DevOps Skill - CI/CD & Deployment

DevOps workflow for CI/CD pipelines, deployment strategies, infrastructure, and monitoring.

## Prerequisites

- ✅ Code in Git
- ✅ Tests passing
- ✅ Cloud account ready
- ✅ Deployment requirements defined

## CI/CD Pipeline

### GitHub Actions

```yaml
# .github/workflows/ci-cd.yml
name: CI/CD

on:
  push:
    branches: [main]
  pull_request:

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
      - run: npm ci
      - run: npm run lint
      - run: npm test
  
  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: docker/build-push-action@v5
        with:
          push: true
          tags: myapp:${{github.sha}}
  
  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment: production
    steps:
      - run: echo "Deploying..."
```

### GitLab CI

```yaml
# .gitlab-ci.yml
stages:
  - test
  - build
  - deploy

test:
  script:
    - npm ci
    - npm test

build:
  script:
    - docker build -t myapp .

deploy:
  script:
    - echo "Deploying"
  when: manual
  only:
    - main
```

---

## Deployment Strategies

### Blue-Green

- Two environments: Blue (current) + Green (new)
- Deploy to Green, test, switch traffic
- Zero downtime, easy rollback

```bash
# Switch traffic to Green
aws elbv2 modify-target-group --weight 100
```

### Canary

- Gradually roll out to small % of users
- Monitor, increase % if healthy
- Minimize blast radius

```nginx
upstream backend {
    server app-v1:3000 weight=90;  # 90%
    server app-v2:3000 weight=10;  # 10%
}
```

### Rolling Update

- Update instances one-by-one
- Maintain minimum capacity
- No extra infrastructure

```yaml
# docker-compose.yml
deploy:
  update_config:
    parallelism: 1  # 1 at a time
    delay: 10s
```

---

## Infrastructure as Code

### Terraform

```hcl
# main.tf
resource "aws_ecs_cluster" "main" {
  name = "myapp-cluster"
}

resource "aws_ecs_task_definition" "app" {
  family = "myapp"
  cpu    = "256"
  memory = "512"
  
  container_definitions = jsonencode([{
    name  = "api"
    image = "myapp:latest"
    portMappings = [{ containerPort = 3000 }]
  }])
}

resource "aws_db_instance" "main" {
  identifier        = "myapp-db"
  engine            = "postgres"
  instance_class    = "db.t3.micro"
  allocated_storage = 20
}
```

**Deploy:**
```bash
terraform init
terraform plan
terraform apply
```

---

## Monitoring & Logging

### Health Check

```typescript
// health.ts
router.get('/health', async (req, res) => {
  const checks = {
    status: 'OK',
    uptime: process.uptime()
  }
  
  try {
    await database.ping()
    checks.database = 'OK'
  } catch {
    checks.database = 'ERROR'
    checks.status = 'DEGRADED'
  }
  
  res.status(checks.status === 'OK' ? 200 : 503).json(checks)
})
```

### Logging

```typescript
import winston from 'winston'

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' })
  ]
})

logger.info('User logged in', { userId: user.id })
logger.error('DB error', { error: err.message })
```

### Monitoring Stack

```yaml
# docker-compose.yml
services:
  prometheus:
    image: prom/prometheus
    ports: ["9090:9090"]
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
  
  grafana:
    image: grafana/grafana
    ports: ["3001:3000"]
```

---

## Secrets Management

### Environment Variables

```bash
# .env.production (DON'T commit!)
DATABASE_URL=postgresql://user:pass@host/db
JWT_SECRET=super-secret
STRIPE_KEY=sk_live_...
```

### AWS Secrets Manager

```typescript
import { SecretsManagerClient, GetSecretValueCommand } from '@aws-sdk/client-secrets-manager'

const client = new SecretsManagerClient()

async function getSecret(name: string) {
  const response = await client.send(
    new GetSecretValueCommand({ SecretId: name })
  )
  return JSON.parse(response.SecretString!)
}

const dbCreds = await getSecret('prod/database')
```

---

## DevOps Checklist

### Pre-Deployment
- [ ] Tests passing
- [ ] Code reviewed
- [ ] Env vars configured
- [ ] Secrets secured
- [ ] Migrations ready
- [ ] Rollback plan

### CI/CD
- [ ] Pipeline configured
- [ ] Tests automated
- [ ] Docker build automated
- [ ] Deployment automated
- [ ] Manual production approval

### Infrastructure
- [ ] IaC (Terraform)
- [ ] Multiple environments
- [ ] Auto-scaling
- [ ] Load balancer
- [ ] SSL/TLS
- [ ] CDN

### Monitoring
- [ ] Health checks
- [ ] Centralized logs
- [ ] Metrics (Prometheus)
- [ ] Dashboards (Grafana)
- [ ] Alerts configured
- [ ] Error tracking

### Security
- [ ] Secrets not in Git
- [ ] Env vars encrypted
- [ ] Auto backups
- [ ] Firewall configured
- [ ] HTTPS enforced
- [ ] Dependency scanning

---

## Deployment Platforms

**Vercel:** `vercel --prod`
**Railway:** `railway up`
**AWS EB:** `eb deploy`
**Docker:** `docker-compose up -d`

---

## Tools

**CI/CD:** GitHub Actions, GitLab CI, Jenkins
**Containers:** Docker, Kubernetes, ECS
**IaC:** Terraform, CloudFormation
**Monitoring:** Prometheus, Grafana, Datadog
**Logging:** ELK, Loki, CloudWatch
**Secrets:** Vault, AWS Secrets Manager

## Next Steps

1. Monitor first deployment
2. Document runbooks
3. Set up alerts
4. Optimize costs
5. Plan for scaling
