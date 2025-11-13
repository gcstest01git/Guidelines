# Technische Architektur

Diese Dokumentation beschreibt die technische Architektur und Design-Entscheidungen des Projekts.

## 📋 Inhaltsverzeichnis

- [Überblick](#überblick)
- [Architektur-Prinzipien](#architektur-prinzipien)
- [System-Komponenten](#system-komponenten)
- [Datenarchitektur](#datenarchitektur)
- [Integration und APIs](#integration-und-apis)
- [Sicherheitsarchitektur](#sicherheitsarchitektur)
- [Skalierbarkeit](#skalierbarkeit)
- [Design Patterns](#design-patterns)

## Überblick

### Architektur-Stil

Das Projekt folgt einer **[modularen/mikrodienst/monolithischen]** Architektur mit klarer Trennung von Verantwortlichkeiten.

### Technologie-Stack

```
Frontend:
- Framework: [z.B. React, Vue, Angular]
- State Management: [z.B. Redux, Vuex]
- UI Library: [z.B. Material-UI, Tailwind]

Backend:
- Sprache: [z.B. Node.js, Python, Java]
- Framework: [z.B. Express, Django, Spring Boot]
- API: [REST, GraphQL, gRPC]

Datenbank:
- Primary: [z.B. PostgreSQL, MongoDB]
- Cache: [z.B. Redis]
- Search: [z.B. Elasticsearch]

Infrastructure:
- Cloud: [z.B. AWS, Azure, GCP]
- Container: [Docker, Kubernetes]
- CI/CD: [z.B. GitHub Actions, Jenkins]
```

## Architektur-Prinzipien

### 1. Separation of Concerns (SoC)
- Klare Trennung zwischen Präsentation, Geschäftslogik und Datenzugriff
- Jede Komponente hat eine definierte Verantwortlichkeit

### 2. DRY (Don't Repeat Yourself)
- Wiederverwendbare Komponenten und Module
- Zentrale Verwaltung von gemeinsam genutztem Code

### 3. SOLID Principles

#### Single Responsibility Principle
- Jede Klasse/Modul hat genau eine Verantwortung
- Änderungen betreffen nur eine Komponente

#### Open/Closed Principle
- Offen für Erweiterung, geschlossen für Modifikation
- Verwendung von Interfaces und Abstractions

#### Liskov Substitution Principle
- Vererbung nur wo semantisch korrekt
- Subklassen müssen Basisklassen ersetzen können

#### Interface Segregation Principle
- Kleine, spezifische Interfaces statt großer generischer
- Clients abhängig nur von benötigten Interfaces

#### Dependency Inversion Principle
- Abhängigkeit von Abstraktionen, nicht von Implementierungen
- Dependency Injection wo möglich

### 4. KISS (Keep It Simple, Stupid)
- Einfache Lösungen bevorzugen
- Keine überflüssige Komplexität

### 5. YAGNI (You Aren't Gonna Need It)
- Implementierung nur von aktuell benötigten Features
- Keine Spekulation über zukünftige Anforderungen

## System-Komponenten

### High-Level Architektur

```
┌─────────────────────────────────────────────────────────┐
│                      Presentation Layer                  │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐     │
│  │   Web UI    │  │  Mobile App │  │   Admin UI  │     │
│  └─────────────┘  └─────────────┘  └─────────────┘     │
└───────────────────────┬─────────────────────────────────┘
                        │
┌───────────────────────▼─────────────────────────────────┐
│                    API Gateway Layer                     │
│  ┌────────────────────────────────────────────────┐     │
│  │  Authentication │ Rate Limiting │ Routing      │     │
│  └────────────────────────────────────────────────┘     │
└───────────────────────┬─────────────────────────────────┘
                        │
┌───────────────────────▼─────────────────────────────────┐
│                   Application Layer                      │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐     │
│  │  Service A  │  │  Service B  │  │  Service C  │     │
│  └─────────────┘  └─────────────┘  └─────────────┘     │
└───────────────────────┬─────────────────────────────────┘
                        │
┌───────────────────────▼─────────────────────────────────┐
│                      Data Layer                          │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐     │
│  │  Database   │  │    Cache    │  │   Storage   │     │
│  └─────────────┘  └─────────────┘  └─────────────┘     │
└─────────────────────────────────────────────────────────┘
```

### Komponenten-Beschreibung

#### Presentation Layer
- **Verantwortung**: User Interface und User Experience
- **Technologien**: HTML, CSS, JavaScript, Framework
- **Kommunikation**: REST API, WebSocket

#### API Gateway
- **Verantwortung**: Routing, Authentication, Rate Limiting
- **Technologien**: [z.B. Kong, Nginx, AWS API Gateway]
- **Features**:
  - Request/Response Transformation
  - Caching
  - Monitoring und Logging

#### Application Layer
- **Verantwortung**: Geschäftslogik
- **Technologien**: [Backend-Sprache/-Framework]
- **Pattern**: Service-orientierte Architektur

#### Data Layer
- **Verantwortung**: Datenpersistenz und -zugriff
- **Technologien**: Datenbank, Cache, Object Storage
- **Pattern**: Repository Pattern

## Datenarchitektur

### Datenbank-Schema

```sql
-- Beispiel Entity Relationship Diagram (ERD)

User
├── id (PK)
├── username
├── email
├── created_at
└── updated_at

Role
├── id (PK)
├── name
└── permissions

UserRole (Join Table)
├── user_id (FK)
└── role_id (FK)
```

### Daten-Modellierung

#### Normalisierung
- Verwendung von 3NF (Third Normal Form) für relationale Daten
- Denormalisierung nur bei berechtigten Performance-Gründen

#### Datentypen
- Konsistente Verwendung von Datentypen
- UUIDs für Primary Keys (oder Auto-Increment IDs)
- Timestamps für created_at/updated_at

### Caching-Strategie

```
┌──────────────┐
│    Client    │
└──────┬───────┘
       │
       ▼
┌──────────────┐  Cache Miss   ┌──────────────┐
│    Cache     │ ────────────> │   Database   │
│   (Redis)    │ <──────────── │ (PostgreSQL) │
└──────────────┘  Update Cache └──────────────┘
```

#### Cache-Levels
1. **Browser Cache**: Statische Assets
2. **CDN Cache**: Globale Content Delivery
3. **Application Cache**: Session Data, API Responses
4. **Database Cache**: Query Results

#### Cache Invalidation
- Time-based (TTL)
- Event-based (on Update/Delete)
- Manual Purge

## Integration und APIs

> **📖 Vollständige API-Richtlinien**: Für detaillierte API Design Guidelines siehe [API_GUIDELINES.md](API_GUIDELINES.md) v2.0

### API Design Prinzipien

Das Projekt folgt modernen API Design Best Practices:

- **API-as-a-Product**: APIs werden als Produkte mit klarem Kundennutzen behandelt
- **API-First Design**: APIs werden vor der Implementierung spezifiziert (OpenAPI)
- **Resource-oriented**: Ressourcen-basiertes Design (Google AIP-121)
- **RESTful**: Standardisierte HTTP-Methoden und Status-Codes
- **Stateless**: Jeder Request enthält alle notwendigen Informationen

### Standard HTTP-Methoden

#### RESTful Principles
```
GET    /api/v1/users          # Liste aller User (mit Pagination)
GET    /api/v1/users/:id      # Einzelner User
POST   /api/v1/users          # Neuer User
PUT    /api/v1/users/:id      # Replace (ganze Ressource)
PATCH  /api/v1/users/:id      # Partial Update
DELETE /api/v1/users/:id      # Delete User
```

#### Idempotenz und Safety

| Method | Safe | Idempotent |
|--------|------|------------|
| GET | ✅ | ✅ |
| POST | ❌ | ❌* |
| PUT | ❌ | ✅ |
| PATCH | ❌ | ❌* |
| DELETE | ❌ | ✅ |

*Kann idempotent gestaltet werden mittels Idempotency-Key Header

### API Versioning

#### URL-basierte Versionierung
```
https://api.example.com/v1/...
https://api.example.com/v2/...
```

#### Semantic Versioning (MAJOR.MINOR.PATCH)
- **MAJOR**: Breaking changes
- **MINOR**: Backward-compatible additions
- **PATCH**: Backward-compatible fixes

#### Backward Compatibility
- Mindestens 12 Monate Support für alte Versionen
- Deprecation Policy mit Sunset-Headers
- Aktive Kommunikation bei Breaking Changes

### Request & Response Format

#### Success Response (RFC 7807)
```json
{
  "data": {
    "id": "uuid",
    "type": "user",
    "attributes": {
      "name": "John Doe",
      "email": "john@example.com"
    },
    "metadata": {
      "created_at": "2025-11-13T10:30:00Z",
      "updated_at": "2025-11-13T10:30:00Z",
      "version": 1,
      "etag": "33a64df551425fcc55e4d42a148795d9f25f89d4"
    }
  },
  "meta": {
    "request_id": "uuid",
    "timestamp": "2025-11-13T10:30:00Z"
  }
}
```

#### Problem JSON Error Handling (RFC 7807)

MUST use Problem JSON für standardisierte Fehlerbehandlung:

```json
{
  "type": "https://api.example.com/problems/invalid-request",
  "title": "Invalid Request",
  "status": 400,
  "detail": "The request body contains invalid fields",
  "instance": "/users/user-123/settings",
  "errors": [
    {
      "field": "email",
      "code": "INVALID_FORMAT",
      "message": "Email format is invalid"
    }
  ],
  "trace_id": "uuid"
}
```

**Content-Type**: `application/problem+json`

### Pagination

#### Cursor-basierte Pagination (empfohlen)
- Robuster gegen Datenmutationen
- Bessere Performance bei großen Datasets
- Pflicht wenn total > 10.000 oder häufige Änderungen

```json
{
  "data": [...],
  "pagination": {
    "cursor": "eyJpZCI6ICJwcm9kLTEwMSJ9",
    "has_more": true,
    "total": 1500
  },
  "links": {
    "self": "https://api.example.com/v1/products?cursor=abc",
    "next": "https://api.example.com/v1/products?cursor=def",
    "prev": "https://api.example.com/v1/products?cursor=xyz",
    "first": "https://api.example.com/v1/products"
  }
}
```

### Filtering, Sorting & Searching

```
GET /api/v1/products?
  filter[status]=active&
  filter[category]=electronics&
  sort=-created_at,+name&
  search=laptop&
  fields=id,name,price&
  embed=manufacturer,reviews
```

### Rate Limiting

```http
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 987
X-RateLimit-Reset: 1699862400
Retry-After: 60  # Bei 429 Status
```

### Authentication & Authorization

> Siehe auch [API_GUIDELINES.md - Authentifizierung & Autorisierung](API_GUIDELINES.md#3-authentifizierung--autorisierung)

#### Unterstützte Authentifizierungs-Mechanismen

- **OAuth2** – für Partner-Integrationen und Third-Party Apps
  - Authorization Code Flow für Web-Apps
  - Client Credentials Flow für Service-to-Service
  - Token-Gültigkeit: 1 Stunde (Access Token), 30 Tage (Refresh Token)

- **SSO (SAML 2.0 / OpenID Connect)** – für interne Benutzer
  - SAML-Endpoint: `/auth/saml/acs`
  - OIDC-Discovery: `/.well-known/openid-configuration`

- **API Keys** – für einfache Machine-to-Machine Kommunikation
  - Header: `X-API-Key: <key>`
  - Scope-basiert (z.B. `products:read`, `orders:write`)
  - Rotation: 90 Tage empfohlen

#### Authentication Flow
```
1. User → Login Credentials → Auth Service
2. Auth Service → Validates → Issues JWT Token / API Key
3. User → Request + JWT/API Key → API Gateway
4. API Gateway → Validates Token → Forwards to Service
5. Service → Response → API Gateway → User
```

#### Authorization Levels
- **Public**: Keine Authentifizierung erforderlich
- **Authenticated**: Gültiger Token erforderlich
- **Role-based**: Spezifische Rolle erforderlich (RBAC)
- **Permission-based**: Spezifische Permission erforderlich (Scope-basiert)

#### Scope Naming Convention
```
<service-name>.<resource>.<access-mode>
```

Beispiele:
- `user-service.users.read`
- `user-service.users.write`
- `content-service.articles.read`
- `payment-service.transactions.write`

## Sicherheitsarchitektur

### Security Layers

#### 1. Network Security
- HTTPS/TLS für alle Verbindungen
- Firewall-Regeln
- DDoS Protection
- VPN für interne Services

#### 2. Application Security
- Input Validation
- Output Encoding
- CSRF Protection
- SQL Injection Prevention
- XSS Prevention

#### 3. Data Security
- Encryption at Rest
- Encryption in Transit
- Secure Password Hashing (bcrypt, Argon2)
- PII Data Protection

#### 4. Access Control
- Principle of Least Privilege
- Role-Based Access Control (RBAC)
- Multi-Factor Authentication (MFA)
- Session Management

### Security Best Practices

- ✅ Regelmäßige Security Audits
- ✅ Dependency Scanning
- ✅ Penetration Testing
- ✅ Security Headers (HSTS, CSP, etc.)
- ✅ Rate Limiting
- ✅ Logging und Monitoring

## Skalierbarkeit

### Horizontal Scaling

```
         ┌──────────────┐
         │ Load Balancer│
         └──────┬───────┘
                │
      ┌─────────┼─────────┐
      ▼         ▼         ▼
┌──────────┐ ┌──────────┐ ┌──────────┐
│ Server 1 │ │ Server 2 │ │ Server 3 │
└──────────┘ └──────────┘ └──────────┘
```

### Vertical Scaling
- Erhöhung von CPU/RAM/Storage
- Anwendbar bis zu Hardware-Limits

### Database Scaling

#### Read Replicas
```
        ┌────────────┐
        │   Master   │ (Writes)
        │  Database  │
        └─────┬──────┘
              │
      ┌───────┴───────┐
      ▼               ▼
┌───────────┐   ┌───────────┐
│  Replica  │   │  Replica  │ (Reads)
│     1     │   │     2     │
└───────────┘   └───────────┘
```

#### Sharding
- Horizontale Partitionierung von Daten
- Shard Key Selection wichtig

### Performance Optimization

- **Caching**: Redis, Memcached
- **CDN**: CloudFront, Cloudflare
- **Async Processing**: Message Queues (RabbitMQ, Kafka)
- **Lazy Loading**: On-Demand Resource Loading
- **Compression**: Gzip, Brotli
- **Database Indexing**: Optimierte Queries

## Design Patterns

### Creational Patterns
- **Singleton**: Für globale Konfiguration
- **Factory**: Für Object Creation
- **Builder**: Für komplexe Object Construction

### Structural Patterns
- **Adapter**: Für Legacy System Integration
- **Decorator**: Für Feature Erweiterung
- **Facade**: Für vereinfachte Interfaces

### Behavioral Patterns
- **Observer**: Für Event Handling
- **Strategy**: Für austauschbare Algorithmen
- **Command**: Für Action Encapsulation

### Architectural Patterns
- **MVC/MVVM**: Für UI-Architektur
- **Repository**: Für Data Access
- **Service Layer**: Für Business Logic
- **Dependency Injection**: Für Loose Coupling

## Monitoring und Observability

### Metrics
- Response Time
- Throughput
- Error Rate
- Resource Utilization (CPU, Memory, Disk)

### Logging
```
Level: ERROR, WARN, INFO, DEBUG
Format: JSON Structured Logging
Storage: Elasticsearch, CloudWatch
```

### Tracing
- Distributed Tracing (Jaeger, Zipkin)
- Request ID Tracking
- Performance Bottleneck Identification

### Alerting
- Threshold-based Alerts
- Anomaly Detection
- On-call Rotation

## Disaster Recovery

### Backup Strategy
- **RPO** (Recovery Point Objective): Max. akzeptabler Datenverlust
- **RTO** (Recovery Time Objective): Max. akzeptable Downtime
- Tägliche Backups mit 30-Tage Retention

### High Availability
- Multi-Region Deployment
- Automatic Failover
- Health Checks und Auto-Recovery

## Dokumentation und Diagramme

### Tools
- **UML Diagramme**: PlantUML, Draw.io
- **API Dokumentation**: Swagger/OpenAPI, Postman
- **Architektur Diagramme**: C4 Model, ArchiMate

### Wartung
- Dokumentation bei jedem größeren Change aktualisieren
- Architektur-Review vierteljährlich
- Tech Debt Review monatlich

## Weitere Ressourcen

- [API Design Guidelines](API_GUIDELINES.md) - Vollständige API-Richtlinien v2.0
- [Coding Standards](CODING_STANDARDS.md)
- [Testing Guidelines](TESTING.md)
- [Deployment Process](DEPLOYMENT.md)
- [Style Guide](STYLE_GUIDE.md)

---

**Version**: 1.0
**Zuletzt aktualisiert**: 2025-11-13
**Maintainer**: [Team/Person]
