# Deployment-Prozesse und CI/CD

Diese Dokumentation beschreibt Deployment-Prozesse, CI/CD-Pipelines und Infrastructure-as-Code Praktiken.

## 📋 Inhaltsverzeichnis

- [Deployment-Strategie](#deployment-strategie)
- [Umgebungen](#umgebungen)
- [CI/CD Pipeline](#cicd-pipeline)
- [Build-Prozess](#build-prozess)
- [Deployment-Arten](#deployment-arten)
- [Rollback-Strategien](#rollback-strategien)
- [Monitoring und Alerting](#monitoring-und-alerting)
- [Infrastructure as Code](#infrastructure-as-code)
- [Security](#security)

## Deployment-Strategie

### Deployment-Philosophie

- **Automatisiert**: Manuelle Deployments vermeiden
- **Reproduzierbar**: Gleicher Prozess für alle Umgebungen
- **Sicher**: Tests und Validierung vor Production
- **Schnell**: Kurze Deployment-Zyklen
- **Rollback-fähig**: Schnelles Zurückrollen bei Problemen

### Deployment-Frequenz

- **Development**: Bei jedem Push
- **Staging**: Bei jedem Merge in `develop`
- **Production**: Bei jedem Merge/Tag in `main`

## Umgebungen

### Environment-Übersicht

```
Development → Staging → Production
    ↓           ↓          ↓
  Feature    Integration  Live
  Testing     Testing    Monitoring
```

### 1. Development (dev)

**Zweck**: Entwicklung und erste Tests

```yaml
URL: https://dev.example.com
Branch: feature/*, develop
Database: dev-database
APIs: Mock/Sandbox APIs
Deployment: Automatisch bei Push
Logging: Verbose/Debug
```

**Eigenschaften:**
- Schnelle Iteration
- Experimentell
- Kann instabil sein
- Fake/Mock-Daten

### 2. Staging (stage)

**Zweck**: Production-ähnliche Testing-Umgebung

```yaml
URL: https://staging.example.com
Branch: develop
Database: staging-database (Production-Kopie)
APIs: Test/Sandbox APIs
Deployment: Automatisch bei Merge in develop
Logging: Info/Warning
```

**Eigenschaften:**
- Production-ähnlich
- Integration Testing
- Performance Testing
- User Acceptance Testing (UAT)

### 3. Production (prod)

**Zweck**: Live-Umgebung für End-User

```yaml
URL: https://example.com
Branch: main
Database: production-database
APIs: Production APIs
Deployment: Automatisch bei Release/Tag
Logging: Warning/Error
```

**Eigenschaften:**
- Hochverfügbar
- Monitoring 24/7
- Backups
- Disaster Recovery

### Environment Variables

```bash
# .env.development
NODE_ENV=development
API_URL=https://api-dev.example.com
DATABASE_URL=postgresql://localhost:5432/myapp_dev
LOG_LEVEL=debug
FEATURE_FLAG_NEW_UI=true

# .env.staging
NODE_ENV=staging
API_URL=https://api-staging.example.com
DATABASE_URL=postgresql://staging-db:5432/myapp_staging
LOG_LEVEL=info
FEATURE_FLAG_NEW_UI=true

# .env.production
NODE_ENV=production
API_URL=https://api.example.com
DATABASE_URL=postgresql://prod-db:5432/myapp_prod
LOG_LEVEL=error
FEATURE_FLAG_NEW_UI=false
```

## CI/CD Pipeline

### Pipeline-Übersicht

```
┌─────────────┐
│  Git Push   │
└──────┬──────┘
       │
       ▼
┌─────────────────┐
│   Build Stage   │ (Compile, Bundle)
└──────┬──────────┘
       │
       ▼
┌─────────────────┐
│   Test Stage    │ (Unit, Integration, E2E)
└──────┬──────────┘
       │
       ▼
┌─────────────────┐
│  Quality Stage  │ (Linting, Security Scan)
└──────┬──────────┘
       │
       ▼
┌─────────────────┐
│  Deploy Stage   │ (Dev/Staging/Prod)
└──────┬──────────┘
       │
       ▼
┌─────────────────┐
│ Verify & Monitor│ (Health Checks, Smoke Tests)
└─────────────────┘
```

### GitHub Actions Beispiel

```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  push:
    branches: [develop, main]
  pull_request:
    branches: [develop, main]

env:
  NODE_VERSION: '18'

jobs:
  # ==================== BUILD ====================
  build:
    name: Build Application
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Build
        run: npm run build

      - name: Upload build artifacts
        uses: actions/upload-artifact@v3
        with:
          name: build-artifacts
          path: dist/

  # ==================== TEST ====================
  test:
    name: Run Tests
    runs-on: ubuntu-latest
    needs: build

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}

      - name: Install dependencies
        run: npm ci

      - name: Run unit tests
        run: npm run test:unit -- --coverage

      - name: Run integration tests
        run: npm run test:integration

      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/lcov.info

  # ==================== QUALITY ====================
  quality:
    name: Code Quality
    runs-on: ubuntu-latest
    needs: build

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}

      - name: Install dependencies
        run: npm ci

      - name: Lint code
        run: npm run lint

      - name: Type check
        run: npm run type-check

      - name: Security audit
        run: npm audit --audit-level=moderate

  # ==================== DEPLOY STAGING ====================
  deploy-staging:
    name: Deploy to Staging
    runs-on: ubuntu-latest
    needs: [build, test, quality]
    if: github.ref == 'refs/heads/develop'

    environment:
      name: staging
      url: https://staging.example.com

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Download build artifacts
        uses: actions/download-artifact@v3
        with:
          name: build-artifacts
          path: dist/

      - name: Deploy to staging
        run: |
          # Deployment commands hier
          echo "Deploying to staging..."

      - name: Run smoke tests
        run: npm run test:smoke -- --env=staging

  # ==================== DEPLOY PRODUCTION ====================
  deploy-production:
    name: Deploy to Production
    runs-on: ubuntu-latest
    needs: [build, test, quality]
    if: github.ref == 'refs/heads/main'

    environment:
      name: production
      url: https://example.com

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Download build artifacts
        uses: actions/download-artifact@v3
        with:
          name: build-artifacts
          path: dist/

      - name: Deploy to production
        run: |
          # Deployment commands hier
          echo "Deploying to production..."

      - name: Run smoke tests
        run: npm run test:smoke -- --env=production

      - name: Notify team
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          text: 'Production deployment completed!'
          webhook_url: ${{ secrets.SLACK_WEBHOOK }}
```

## Build-Prozess

### Build-Schritte

1. **Clean**: Alte Build-Artefakte löschen
2. **Install**: Dependencies installieren
3. **Lint**: Code-Qualität prüfen
4. **Test**: Tests ausführen
5. **Compile**: TypeScript/ES6+ → JavaScript
6. **Bundle**: Module zusammenfassen
7. **Minify**: Code komprimieren
8. **Optimize**: Assets optimieren
9. **Generate**: Sourcemaps, Dokumentation

### Build-Konfiguration

```json
// package.json
{
  "scripts": {
    "clean": "rm -rf dist",
    "prebuild": "npm run clean",
    "build": "npm run build:compile && npm run build:bundle",
    "build:compile": "tsc",
    "build:bundle": "webpack --mode production",
    "build:analyze": "webpack-bundle-analyzer dist/stats.json"
  }
}
```

### Docker Build

```dockerfile
# Dockerfile
FROM node:18-alpine AS builder

WORKDIR /app

# Dependencies
COPY package*.json ./
RUN npm ci --only=production

# Build
COPY . .
RUN npm run build

# Production image
FROM node:18-alpine

WORKDIR /app

COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY package*.json ./

EXPOSE 3000

CMD ["node", "dist/index.js"]
```

## Deployment-Arten

### 1. Rolling Deployment

**Beschreibung**: Schrittweise Aktualisierung der Instanzen

```
Server 1: v1 → v2
Server 2: v1 → v2
Server 3: v1 → v2
```

**Vorteile:**
- Kein Downtime
- Einfach zu implementieren

**Nachteile:**
- Zwei Versionen gleichzeitig aktiv
- Langsamer Rollout

### 2. Blue-Green Deployment

**Beschreibung**: Zwei identische Umgebungen (Blue & Green)

```
Blue (v1) ← 100% Traffic
Green (v2) ← 0% Traffic

Deploy to Green → Test → Switch Traffic

Blue (v1) ← 0% Traffic
Green (v2) ← 100% Traffic
```

**Vorteile:**
- Instant Rollback
- Zero Downtime
- Testing vor Switch

**Nachteile:**
- Doppelte Ressourcen
- Database-Migrations komplex

### 3. Canary Deployment

**Beschreibung**: Neue Version an kleine User-Gruppe

```
v1: 90% Traffic
v2: 10% Traffic (Canary)

↓ Monitoring ↓

v1: 50% Traffic
v2: 50% Traffic

↓ Monitoring ↓

v1: 0% Traffic
v2: 100% Traffic
```

**Vorteile:**
- Geringes Risiko
- Echtes User-Feedback
- Schrittweiser Rollout

**Nachteile:**
- Komplexere Infrastruktur
- Längerer Deployment-Prozess

### 4. Feature Flags

**Beschreibung**: Features per Flag aktivieren/deaktivieren

```javascript
if (featureFlags.newCheckout) {
  renderNewCheckout();
} else {
  renderOldCheckout();
}
```

**Vorteile:**
- Deploy ≠ Release
- A/B Testing
- Instant Rollback

**Implementation:**

```javascript
// Feature Flag Service
class FeatureFlags {
  constructor(environment) {
    this.flags = {
      newUI: environment === 'production' ? false : true,
      betaFeature: this.isUserInBeta(),
      maintenance: false
    };
  }

  isEnabled(flag) {
    return this.flags[flag] || false;
  }

  isUserInBeta() {
    // Check user permission, percentage rollout, etc.
  }
}

// Usage
const flags = new FeatureFlags(process.env.NODE_ENV);

if (flags.isEnabled('newUI')) {
  renderNewUI();
}
```

## Rollback-Strategien

### Automatischer Rollback

```yaml
# .github/workflows/deploy.yml
- name: Deploy to production
  id: deploy
  run: ./deploy.sh

- name: Run health check
  id: health
  run: ./health-check.sh

- name: Rollback on failure
  if: steps.health.outcome != 'success'
  run: ./rollback.sh
```

### Manueller Rollback

```bash
# Zu vorheriger Version
kubectl rollout undo deployment/myapp

# Zu spezifischer Version
kubectl rollout undo deployment/myapp --to-revision=3

# Docker
docker service update --rollback myapp

# Git-basiert
git revert HEAD
git push origin main
```

### Database Rollback

```javascript
// Migrations sollten immer reversibel sein
exports.up = async (knex) => {
  await knex.schema.createTable('users', (table) => {
    table.increments('id');
    table.string('email');
  });
};

exports.down = async (knex) => {
  await knex.schema.dropTable('users');
};
```

## Monitoring und Alerting

### Health Checks

```javascript
// Express Health Check Endpoint
app.get('/health', async (req, res) => {
  const health = {
    status: 'ok',
    timestamp: new Date().toISOString(),
    uptime: process.uptime(),
    checks: {
      database: await checkDatabase(),
      redis: await checkRedis(),
      externalAPI: await checkExternalAPI()
    }
  };

  const isHealthy = Object.values(health.checks)
    .every(check => check.status === 'ok');

  res.status(isHealthy ? 200 : 503).json(health);
});

async function checkDatabase() {
  try {
    await db.raw('SELECT 1');
    return { status: 'ok' };
  } catch (error) {
    return { status: 'error', message: error.message };
  }
}
```

### Metrics

```javascript
// Prometheus metrics
const promClient = require('prom-client');

const httpRequestDuration = new promClient.Histogram({
  name: 'http_request_duration_seconds',
  help: 'Duration of HTTP requests in seconds',
  labelNames: ['method', 'route', 'status_code']
});

app.use((req, res, next) => {
  const start = Date.now();

  res.on('finish', () => {
    const duration = (Date.now() - start) / 1000;
    httpRequestDuration
      .labels(req.method, req.route?.path, res.statusCode)
      .observe(duration);
  });

  next();
});

// Metrics endpoint
app.get('/metrics', (req, res) => {
  res.set('Content-Type', promClient.register.contentType);
  res.end(promClient.register.metrics());
});
```

### Logging

```javascript
// Structured logging with Winston
const winston = require('winston');

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  defaultMeta: {
    service: 'my-app',
    environment: process.env.NODE_ENV
  },
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' })
  ]
});

// Usage
logger.info('User logged in', { userId: 123 });
logger.error('Database connection failed', { error: err.message });
```

### Alerting

```yaml
# Prometheus Alerting Rules
groups:
  - name: application
    rules:
      - alert: HighErrorRate
        expr: rate(http_requests_total{status="500"}[5m]) > 0.05
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "High error rate detected"
          description: "Error rate is {{ $value }} requests/sec"

      - alert: HighResponseTime
        expr: http_request_duration_seconds{quantile="0.99"} > 2
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "High response time"
          description: "99th percentile is {{ $value }}s"
```

## Infrastructure as Code

### Terraform Beispiel

```hcl
# main.tf
provider "aws" {
  region = "eu-central-1"
}

resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t3.micro"

  tags = {
    Name        = "web-server"
    Environment = var.environment
  }
}

resource "aws_db_instance" "postgres" {
  identifier        = "myapp-db"
  engine            = "postgres"
  engine_version    = "14.5"
  instance_class    = "db.t3.micro"
  allocated_storage = 20

  db_name  = var.db_name
  username = var.db_username
  password = var.db_password

  skip_final_snapshot = var.environment == "development"
}
```

### Kubernetes Deployment

```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
  labels:
    app: myapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
        - name: myapp
          image: myapp:1.0.0
          ports:
            - containerPort: 3000
          env:
            - name: NODE_ENV
              value: "production"
            - name: DATABASE_URL
              valueFrom:
                secretKeyRef:
                  name: db-secret
                  key: url
          resources:
            requests:
              memory: "128Mi"
              cpu: "100m"
            limits:
              memory: "256Mi"
              cpu: "200m"
          livenessProbe:
            httpGet:
              path: /health
              port: 3000
            initialDelaySeconds: 30
            periodSeconds: 10
          readinessProbe:
            httpGet:
              path: /ready
              port: 3000
            initialDelaySeconds: 5
            periodSeconds: 5

---
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    app: myapp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
  type: LoadBalancer
```

## Security

### Secrets Management

```bash
# ❌ NIEMALS in Git committen
DATABASE_PASSWORD=secret123

# ✅ Environment Variables
export DATABASE_PASSWORD=secret123

# ✅ Secret Management Tools
# AWS Secrets Manager
# HashiCorp Vault
# Kubernetes Secrets
```

### CI/CD Secrets

```yaml
# GitHub Actions
- name: Deploy
  env:
    API_KEY: ${{ secrets.API_KEY }}
    DB_PASSWORD: ${{ secrets.DB_PASSWORD }}
```

### Security Scanning

```yaml
- name: Security scan
  run: |
    npm audit
    npx snyk test

- name: Container scan
  uses: aquasecurity/trivy-action@master
  with:
    image-ref: myapp:latest
    severity: 'CRITICAL,HIGH'
```

## Checkliste Deployment

### Pre-Deployment

- [ ] Code Review abgeschlossen
- [ ] Alle Tests bestehen
- [ ] Security Scan durchgeführt
- [ ] Dokumentation aktualisiert
- [ ] Database Migrations vorbereitet
- [ ] Rollback-Plan erstellt
- [ ] Stakeholder informiert

### During Deployment

- [ ] Monitoring aktiv
- [ ] Health Checks laufen
- [ ] Logs werden überwacht
- [ ] Team ist verfügbar

### Post-Deployment

- [ ] Smoke Tests durchgeführt
- [ ] Metrics normal
- [ ] User Feedback eingeholt
- [ ] Deployment dokumentiert
- [ ] Post-Mortem (bei Problemen)

## Weitere Ressourcen

- [Git Workflow](GIT_WORKFLOW.md)
- [Testing Guidelines](TESTING.md)
- [Architecture](ARCHITECTURE.md)
- [Security](../SECURITY.md)

---

**Version**: 1.0
**Zuletzt aktualisiert**: 2025-11-13
