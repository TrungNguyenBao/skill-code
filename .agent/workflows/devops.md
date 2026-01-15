---
description: DevOps - CI/CD & Deployment
auto_execution_mode: 3
---

# DevOps Skill - CI/CD, Deployment & Monitoring

Comprehensive workflow for DevOps engineers covering CI/CD pipelines, deployment strategies, infrastructure, and monitoring.

## How to Use This Workflow

When setting up deployment or infrastructure, follow this systematic approach:

### Prerequisites

- ✅ Code in version control (Git)
- ✅ Tests passing (from QAQC)
- ✅ Cloud account set up (AWS/GCP/Azure/Vercel)
- ✅ Deployment requirements defined

---

## Step 1: CI/CD Pipeline Setup

### 1.1 GitHub Actions

**Basic CI/CD Pipeline:**

```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  NODE_VERSION: '20.x'

jobs:
  # Job 1: Build and Test
  test:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Lint code
        run: npm run lint
      
      - name: Run tests
        run: npm test -- --coverage
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          token: ${{ secrets.CODECOV_TOKEN }}
  
  # Job 2: Build Docker image
  build:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3
      
      - name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}
      
      - name: Build and push
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: |
            myapp/api:latest
            myapp/api:${{ github.sha }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
  
  # Job 3: Deploy to staging
  deploy-staging:
    needs: build
    runs-on: ubuntu-latest
    environment: staging
    
    steps:
      - name: Deploy to staging
        run: |
          echo "Deploying to staging..."
          # Add deployment commands
  
  # Job 4: Deploy to production
  deploy-production:
    needs: deploy-staging
    runs-on: ubuntu-latest
    environment: production
    if: github.ref == 'refs/heads/main'
    
    steps:
      - name: Deploy to production
        run: |
          echo "Deploying to production..."
          # Add deployment commands
```

### 1.2 GitLab CI

```yaml
# .gitlab-ci.yml
stages:
  - test
  - build
  - deploy

variables:
  NODE_VERSION: "20"

# Test stage
test:
  stage: test
  image: node:${NODE_VERSION}
  script:
    - npm ci
    - npm run lint
    - npm test -- --coverage
  coverage: '/Lines\s*:\s*(\d+\.\d+)%/'
  artifacts:
    reports:
      coverage_report:
        coverage_format: cobertura
        path: coverage/cobertura-coverage.xml

# Build stage
build:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t myapp:$CI_COMMIT_SHA .
    - docker push myapp:$CI_COMMIT_SHA
  only:
    - main

# Deploy to staging
deploy:staging:
  stage: deploy
  script:
    - echo "Deploying to staging"
  environment:
    name: staging
    url: https://staging.example.com
  only:
    - main

# Deploy to production
deploy:production:
  stage: deploy
  script:
    - echo "Deploying to production"
  environment:
    name: production
    url: https://example.com
  when: manual
  only:
    - main
```

---

## Step 2: Deployment Strategies

### 2.1 Blue-Green Deployment

**Concept:**
- Two identical environments: Blue (current) and Green (new)
- Deploy to Green, test, then switch traffic
- Zero downtime, easy rollback

**Implementation (AWS with Load Balancer):**

```bash
# 1. Deploy new version to Green environment
aws ecs update-service \
  --cluster myapp-cluster \
  --service myapp-green \
  --force-new-deployment

# 2. Wait for Green to be healthy
aws ecs wait services-stable \
  --cluster myapp-cluster \
  --services myapp-green

# 3. Switch traffic from Blue to Green
aws elbv2 modify-target-group \
  --target-group-arn $GREEN_TG_ARN \
  --weight 100

aws elbv2 modify-target-group \
  --target-group-arn $BLUE_TG_ARN \
  --weight 0

# 4. Monitor for issues
# 5. If OK, keep Green. If issues, rollback to Blue
```

### 2.2 Canary Deployment

**Concept:**
- Gradually roll out to small % of users
- Monitor metrics, increase % if healthy
- Minimize blast radius of bugs

**Example (with Nginx):**

```nginx
# nginx.conf
upstream backend {
    # 90% to stable version
    server app-v1:3000 weight=90;
    # 10% to canary version
    server app-v2:3000 weight=10;
}

server {
    listen 80;
    location / {
        proxy_pass http://backend;
    }
}
```

**Kubernetes Canary:**

```yaml
# deployment-v1.yaml (90% traffic)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-v1
spec:
  replicas: 9
  template:
    metadata:
      labels:
        app: myapp
        version: v1
    spec:
      containers:
      - name: myapp
        image: myapp:v1.0.0

---
# deployment-v2.yaml (10% traffic)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-v2
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: myapp
        version: v2
    spec:
      containers:
      - name: myapp
        image: myapp:v2.0.0

---
# service.yaml (load balances across both)
apiVersion: v1
kind: Service
metadata:
  name: myapp
spec:
  selector:
    app: myapp  # Matches both v1 and v2
  ports:
  - port: 80
    targetPort: 3000
```

### 2.3 Rolling Deployment

**Concept:**
- Update instances one-by-one or in batches
- Always maintain minimum capacity
- No additional infrastructure needed

**Docker Compose:**

```yaml
# docker-compose.yml
version: '3.8'
services:
  api:
    image: myapp:${VERSION}
    deploy:
      replicas: 4
      update_config:
        parallelism: 1  # Update 1 at a time
        delay: 10s      # Wait 10s between updates
        failure_action: rollback
        monitor: 60s
      rollback_config:
        parallelism: 1
        delay: 10s
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 10s
      timeout: 5s
      retries: 3
```

**Deploy:**
```bash
# Deploy new version with rolling update
VERSION=v2.0.0 docker stack deploy -c docker-compose.yml myapp

# Rollback if needed
docker service rollback myapp_api
```

---

## Step 3: Infrastructure as Code

### 3.1 Terraform (AWS Example)

```hcl
# main.tf
terraform {
  required_version = ">= 1.0"
  
  backend "s3" {
    bucket = "myapp-terraform-state"
    key    = "prod/terraform.tfstate"
    region = "us-east-1"
  }
  
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = var.aws_region
}

# VPC
resource "aws_vpc" "main" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true
  
  tags = {
    Name = "${var.project_name}-vpc"
  }
}

# Subnets
resource "aws_subnet" "public" {
  count                   = 2
  vpc_id                  = aws_vpc.main.id
  cidr_block              = "10.0.${count.index}.0/24"
  availability_zone       = data.aws_availability_zones.available.names[count.index]
  map_public_ip_on_launch = true
  
  tags = {
    Name = "${var.project_name}-public-${count.index + 1}"
  }
}

# ECS Cluster
resource "aws_ecs_cluster" "main" {
  name = "${var.project_name}-cluster"
  
  setting {
    name  = "containerInsights"
    value = "enabled"
  }
}

# ECS Task Definition
resource "aws_ecs_task_definition" "app" {
  family                   = "${var.project_name}-api"
  network_mode             = "awsvpc"
  requires_compatibilities = ["FARGATE"]
  cpu                      = "256"
  memory                   = "512"
  execution_role_arn       = aws_iam_role.ecs_execution.arn
  task_role_arn            = aws_iam_role.ecs_task.arn
  
  container_definitions = jsonencode([{
    name  = "api"
    image = "${var.docker_image}:${var.image_tag}"
    portMappings = [{
      containerPort = 3000
      protocol      = "tcp"
    }]
    environment = [
      { name = "NODE_ENV", value = "production" }
    ]
    secrets = [
      {
        name      = "DATABASE_URL"
        valueFrom = aws_secretsmanager_secret.db_url.arn
      }
    ]
    logConfiguration = {
      logDriver = "awslogs"
      options = {
        "awslogs-group"         = "/ecs/${var.project_name}"
        "awslogs-region"        = var.aws_region
        "awslogs-stream-prefix" = "api"
      }
    }
  }])
}

# ECS Service
resource "aws_ecs_service" "app" {
  name            = "${var.project_name}-api"
  cluster         = aws_ecs_cluster.main.id
  task_definition = aws_ecs_task_definition.app.arn
  desired_count   = var.app_count
  launch_type     = "FARGATE"
  
  network_configuration {
    subnets         = aws_subnet.public[*].id
    security_groups = [aws_security_group.ecs.id]
  }
  
  load_balancer {
    target_group_arn = aws_lb_target_group.app.arn
    container_name   = "api"
    container_port   = 3000
  }
  
  depends_on = [aws_lb_listener.app]
}

# RDS Database
resource "aws_db_instance" "main" {
  identifier             = "${var.project_name}-db"
  engine                 = "postgres"
  engine_version         = "15.3"
  instance_class         = "db.t3.micro"
  allocated_storage      = 20
  storage_encrypted      = true
  db_name                = var.db_name
  username               = var.db_username
  password               = var.db_password
  skip_final_snapshot    = var.environment != "production"
  backup_retention_period = 7
  vpc_security_group_ids = [aws_security_group.rds.id]
  db_subnet_group_name   = aws_db_subnet_group.main.name
}

# Outputs
output "load_balancer_dns" {
  value = aws_lb.main.dns_name
}

output "ecs_cluster_name" {
  value = aws_ecs_cluster.main.name
}
```

**Deploy:**
```bash
# Initialize
terraform init

# Plan changes
terraform plan -out=tfplan

# Apply changes
terraform apply tfplan

# Destroy (careful!)
terraform destroy
```

---

## Step 4: Monitoring & Logging

### 4.1 Application Monitoring

**Health Check Endpoint:**

```typescript
// Node.js/Express
import express from 'express'

const router = express.Router()

router.get('/health', async (req, res) => {
  const checks = {
    uptime: process.uptime(),
    timestamp: Date.now(),
    status: 'OK'
  }
  
  try {
    // Check database connection
    await database.ping()
    checks.database = 'OK'
  } catch (error) {
    checks.database = 'ERROR'
    checks.status = 'DEGRADED'
  }
  
  try {
    // Check Redis connection
    await redis.ping()
    checks.redis = 'OK'
  } catch (error) {
    checks.redis = 'ERROR'
    checks.status = 'DEGRADED'
  }
  
  const statusCode = checks.status === 'OK' ? 200 : 503
  res.status(statusCode).json(checks)
})

export default router
```

### 4.2 Logging with Winston

```typescript
import winston from 'winston'

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  defaultMeta: { service: 'myapp-api' },
  transports: [
    // Write all logs to file
    new winston.transports.File({
      filename: 'logs/error.log',
      level: 'error'
    }),
    new winston.transports.File({
      filename: 'logs/combined.log'
    })
  ]
})

// Console in development
if (process.env.NODE_ENV !== 'production') {
  logger.add(new winston.transports.Console({
    format: winston.format.combine(
      winston.format.colorize(),
      winston.format.simple()
    )
  }))
}

export default logger

// Usage
logger.info('User registered', { userId: user.id, email: user.email })
logger.error('Database error', { error: err.message, stack: err.stack })
```

### 4.3 Monitoring Stack (Prometheus + Grafana)

**docker-compose.yml:**

```yaml
version: '3.8'

services:
  # Application
  app:
    image: myapp:latest
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
  
  # Prometheus (metrics collection)
  prometheus:
    image: prom/prometheus:latest
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus-data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
  
  # Grafana (visualization)
  grafana:
    image: grafana/grafana:latest
    ports:
      - "3001:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
    volumes:
      - grafana-data:/var/lib/grafana
    depends_on:
      - prometheus

volumes:
  prometheus-data:
 grafana-data:
```

**prometheus.yml:**

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'myapp'
    static_configs:
      - targets: ['app:3000']
```

---

## Step 5: Secrets Management

### 5.1 Environment Variables

```bash
# .env.production (NEVER commit this)
NODE_ENV=production
PORT=3000

# Database
DATABASE_URL=postgresql://user:pass@host:5432/dbname

# JWT
JWT_SECRET=super-secret-key-change-this
JWT_REFRESH_SECRET=different-refresh-secret

# Redis
REDIS_URL=redis://localhost:6379

# AWS
AWS_ACCESS_KEY_ID=AKIA...
AWS_SECRET_ACCESS_KEY=...
AWS_REGION=us-east-1

# Stripe
STRIPE_SECRET_KEY=sk_live_...

# SendGrid
SENDGRID_API_KEY=SG...
```

### 5.2 AWS Secrets Manager

```typescript
import {
  SecretsManagerClient,
  GetSecretValueCommand
} from '@aws-sdk/client-secrets-manager'

const client = new SecretsManagerClient({ region: 'us-east-1' })

export async function getSecret(secretName: string) {
  try {
    const response = await client.send(
      new GetSecretValueCommand({ SecretId: secretName })
    )
    return JSON.parse(response.SecretString!)
  } catch (error) {
    console.error('Error fetching secret:', error)
    throw error
  }
}

// Usage
const dbCreds = await getSecret('prod/database')
const databaseUrl = dbCreds.DATABASE_URL
```

---

## DevOps Checklist

### Pre-Deployment
- [ ] All tests passing
- [ ] Code reviewed and merged
- [ ] Environment variables configured
- [ ] Secrets stored securely
- [ ] Database migrations ready
- [ ] Rollback plan documented

### CI/CD
- [ ] CI pipeline configured
- [ ] Automated tests run on PR
- [ ] Docker image builds automatically
- [ ] Deployment automated for main branch
- [ ] Manual approval for production

### Infrastructure
- [ ] Infrastructure as code (Terraform/CloudFormation)
- [ ] Multiple environments (dev, staging, prod)
- [ ] Auto-scaling configured
- [ ] Load balancer set up
- [ ] SSL/TLS certificates installed
- [ ] CDN configured for static assets

### Monitoring
- [ ] Health check endpoints
- [ ] Application logs centralized
- [ ] Metrics collection (Prometheus/CloudWatch)
- [ ] Dashboards created (Grafana)
- [ ] Alerts configured (Slack/PagerDuty)
- [ ] Error tracking (Sentry)

### Security
- [ ] Secrets not in code/Git
- [ ] Environment variables encrypted
- [ ] Database backups automated
- [ ] Security groups/firewall configured
- [ ] HTTPS enforced
- [ ] Dependency scanning (Snyk/Dependabot)

### Disaster Recovery
- [ ] Backup strategy defined
- [ ] Restore procedure tested
- [ ] Rollback procedure documented
- [ ] Incident response plan
- [ ] On-call rotation set up

---

## Common Deployment Platforms

### Vercel (Next.js)
```bash
# Install Vercel CLI
npm i -g vercel

# Deploy
vercel --prod

# Environment variables via dashboard
```

### Railway
```bash
# Install Railway CLI
npm i -g @railway/cli

# Login and deploy
railway login
railway up
```

### AWS Elastic Beanstalk
```bash
# Initialize
eb init

# Create environment
eb create production

# Deploy
eb deploy
```

### Docker + Docker Compose
```bash
# Build and run
docker-compose up -d

# View logs
docker-compose logs -f

# Stop
docker-compose down
```

---

## Next Steps

After DevOps setup complete:
1. **Monitor first deployment** closely
2. **Document runbooks** for common issues
3. **Set up alerts** for critical metrics
4. **Review and optimize** costs
5. **Plan for scaling** based on traffic

## DevOps Tools

**CI/CD:** GitHub Actions, GitLab CI, Jenkins, CircleCI
**Containers:** Docker, Kubernetes, ECS, GKE
**IaC:** Terraform, CloudFormation, Pulumi
**Monitoring:** Prometheus, Grafana, Datadog, New Relic
**Logging:** ELK Stack, Loki, CloudWatch
**Secrets:** Vault, AWS Secrets Manager, Doppler
