# API Design Guidelines v2.0

## Überblick

Diese Guidelines definieren Best Practices und Standards für die Entwicklung moderner RESTful APIs. Sie folgen dem API-as-a-Product Prinzip und basieren auf bewährten Industry Standards. Die Guidelines verwenden die RFC 2119 Terminologie (MUST, SHOULD, MAY) für klare Anforderungsstufen und zeigen, wie APIs optimal gestaltet werden sollten.

## 📋 Inhaltsverzeichnis

- [Changelog](#changelog)
- [1. Grundlegende Prinzipien](#1-grundlegende-prinzipien)
- [2. API Metadata](#2-api-metadata)
- [3. Authentifizierung & Autorisierung](#3-authentifizierung--autorisierung)
- [4. API-Struktur](#4-api-struktur)
- [5. Request & Response Format](#5-request--response-format)
- [6. Error Handling](#6-error-handling)
- [7. Pagination](#7-pagination)
- [8. Filtering, Sorting & Searching](#8-filtering-sorting--searching)
- [9. Rate Limiting](#9-rate-limiting)
- [10. Timeouts](#10-timeouts)
- [11. Logging & Audit Trail](#11-logging--audit-trail)
- [12. Monitoring & Alerting](#12-monitoring--alerting)
- [13. Versionierung & Deprecation](#13-versionierung--deprecation)
- [14. Compatibility Rules](#14-compatibility-rules)
- [15. Idempotenz](#15-idempotenz)
- [16. Batch Operations](#16-batch-operations)
- [17. CORS & Security](#17-cors--security)
- [18. Dokumentation](#18-dokumentation)
- [19. Datentypen & Formate](#19-datentypen--formate)
- [20. Ressourcen-Spezifikationen](#20-ressourcen-spezifikationen)
- [21. Testing Requirements](#21-testing-requirements)
- [22. API Governance](#22-api-governance)
- [23. Implementierungs-Checkliste](#23-implementierungs-checkliste)
- [Anhang A: Referenzen](#anhang-a-referenzen)
- [Anhang B: Glossar](#anhang-b-glossar)

## Changelog

### Version 2.0 (2024-11)

- Problem JSON (RFC 7807) für standardisierte Fehlerbehandlung
- Deprecation Policy mit Sunset/Deprecation Headers
- API-First Prinzip als Kernbestandteil
- Erweiterte Compatibility Rules
- Secondary Key Pattern für Idempotenz
- x-extensible-enum für erweiterbare Enumerations
- Functional Naming Schema

## 1. Grundlegende Prinzipien

### API-as-a-Product

- Behandle deine API als Produkt mit klarem Kundennutzen
- Stelle Developer Experience in den Mittelpunkt
- Biete umfassende Dokumentation und Beispiele
- Gewährleiste langfristige Stabilität und Support
- Sammle und berücksichtige Feedback der API-Konsumenten

### API-First Design

MUST APIs vor der Implementierung spezifizieren:

- Definiere APIs in OpenAPI bevor Code geschrieben wird
- Hole frühes Review-Feedback von Peers und Konsumenten ein
- Verwende API Linting für automatische Compliance-Checks
- Iteriere das Design basierend auf Feedback

### Technische Prinzipien

- Resource-oriented Design (Google AIP-121)
- Stateless, idempotent Operations
- Versionierung über URL-Pfad: `/api/v1/`
- UTF-8 Encoding für alle Requests und Responses
- JSON als Standard-Format
- MUST follow Postel's Law: "Be liberal in what you accept, be conservative in what you send"

## 2. API Metadata

### MUST provide API metadata

APIs müssen folgende OpenAPI Metainformationen enthalten:

```yaml
openapi: 3.1.0
info:
  title: Example API
  description: Comprehensive API following best practices
  version: 1.3.7  # Semantic Versioning
  x-api-id: d0184f38-b98d-11e7-9c56-68f728c1ba70  # UUID
  x-audience: company-internal  # oder external-partner
  contact:
    name: API Team
    email: api-support@example.com
    url: https://api.example.com/support
```

### API Audience Classification

MUST APIs nach Zielgruppe klassifizieren:

- `component-internal`: Team-interne APIs
- `business-unit-internal`: Innerhalb der Business Unit
- `company-internal`: Innerhalb des Unternehmens
- `external-partner`: Für Geschäftspartner
- `external-public`: Öffentlich zugänglich

## 3. Authentifizierung & Autorisierung

### Unterstützte Mechanismen

#### OAuth2 – für Partner-Integrationen und Third-Party Apps

- Authorization Code Flow für Web-Apps
- Client Credentials Flow für Service-to-Service
- Token-Gültigkeit: 1 Stunde (Access Token), 30 Tage (Refresh Token)

#### SSO (SAML 2.0 / OpenID Connect) – für interne Benutzer

- SAML-Endpoint: `/auth/saml/acs`
- OIDC-Discovery: `/.well-known/openid-configuration`

#### API Keys – für einfache Machine-to-Machine Kommunikation

- Header: `X-API-Key: <key>`
- Scope-basiert (z.B. `products:read`, `orders:write`)
- Rotation: 90 Tage empfohlen
- MUST implement key rotation reminders

### Permission (Scope) Naming Convention

MUST follow naming pattern:

```
<service-name>.<resource>.<access-mode>
```

Beispiele:
- `user-service.users.read`
- `user-service.users.write`
- `content-service.articles.read`
- `payment-service.transactions.write`
- `notification-service.messages.send`

## 4. API-Struktur

### Basis-URL

```
https://api.example.com/v1
```

**Best Practice**: Verwende eine dedizierte API-Subdomain und Versionierung im Pfad.

### Functional Naming Schema

SHOULD use functional naming for better organization:

```
<functional-name> ::= <functional-domain>-<functional-component>
```

Beispiele:
- `user-management`
- `content-delivery`
- `payment-processing`
- `notification-service`
- `analytics-engine`

### Ressourcen-Hierarchie

#### Hierarchische Organisation:

- `/organizations` – Organisationen
- `/organizations/{org_id}/projects` – Projekte einer Organisation
- `/organizations/{org_id}/members` – Mitglieder einer Organisation

#### Flache Struktur mit Referenzen:

- `/users` – Benutzer
- `/products` – Produkte
- `/orders` – Bestellungen
- `/articles` – Artikel
- `/comments` – Kommentare

**Best Practice**: Wähle zwischen hierarchischer und flacher Struktur basierend auf den tatsächlichen Beziehungen und Zugriffsmuster.

### Standard Methods (Google AIP-131)

| Method | Path | Aktion | Safe | Idempotent |
|--------|------|--------|------|------------|
| GET | /resources | List (mit Pagination) | ✅ | ✅ |
| GET | /resources/{id} | Get (einzelne Ressource) | ✅ | ✅ |
| POST | /resources | Create | ❌ | ❌* |
| PUT | /resources/{id} | Replace (ganze Ressource) | ❌ | ✅ |
| PATCH | /resources/{id} | Update (Partial) | ❌ | ❌* |
| DELETE | /resources/{id} | Delete | ❌ | ✅ |

*Kann idempotent gestaltet werden (siehe Abschnitt 15)

## 5. Request & Response Format

### Request-Header (erforderlich)

```http
Content-Type: application/json
Accept: application/json
Authorization: Bearer <token>
X-Request-ID: <uuid>  # Für Tracing
X-Flow-ID: <uuid>  # Für distributed tracing
X-API-Version: 1
```

### Success Response (200, 201)

```json
{
  "data": {
    "id": "uuid",
    "type": "resource_type",
    "attributes": { ... },
    "relationships": { ... },
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

### Problem JSON Error Response (RFC 7807)

MUST use Problem JSON for error responses:

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

## 6. Error Handling

### Standard Error Codes

| Code | HTTP Status | Bedeutung | Problem Type |
|------|-------------|-----------|--------------|
| INVALID_REQUEST | 400 | Request-Format oder Validierung fehlerhaft | /problems/invalid-request |
| UNAUTHORIZED | 401 | Authentifizierung erforderlich oder ungültig | /problems/unauthorized |
| FORBIDDEN | 403 | Berechtigung fehlt | /problems/forbidden |
| NOT_FOUND | 404 | Ressource nicht gefunden | /problems/not-found |
| CONFLICT | 409 | Datenkonflikte (z.B. Duplikat) | /problems/conflict |
| RATE_LIMIT_EXCEEDED | 429 | Rate Limit überschritten | /problems/rate-limit |
| INTERNAL_ERROR | 500 | Server-Fehler | /problems/internal-error |
| SERVICE_UNAVAILABLE | 503 | Service temporär nicht verfügbar | /problems/service-unavailable |

### Problem Type Registry

Maintain a registry of problem types at: `https://api.example.com/problems/`

## 7. Pagination

### Paginierungs-Strategie

#### Offset-basiert (nur für stabile, kleine Datenmengen mit total < 10.000):

- Einfach zu implementieren
- Geeignet für: Organizations, Partners, Addresses

#### Cursor-basiert (Pflicht für große/volatile Datenmengen):

- Robuster gegen Datenmutationen
- Bessere Performance bei großen Datasets
- Geeignet für: Products, Inventory, Orders
- Pflicht wenn: total > 10.000 oder häufige Änderungen

### Response mit Pagination

```json
{
  "data": [ ... ],
  "pagination": {
    "cursor": "eyJpZCI6ICJwcm9kLTEwMSIsICJjcmVhdGVkX2F0IjogIjIwMjUtMTEtMTNUMTA6MzE6MDBaIn0=",
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

### HATEOAS Navigation (Link Header)

MUST provide Link headers:

```http
Link: </v1/products?cursor=eyJ...>; rel="next",
      </v1/products?cursor=abc...>; rel="prev",
      </v1/products>; rel="first"
```

## 8. Filtering, Sorting & Searching

### Query-Parameter Format

```
GET /api/v1/products?
  filter[status]=active&
  filter[category]=electronics&
  sort=-created_at,+name&
  search=laptop&
  fields=id,name,price&
  embed=manufacturer,reviews
```

- `filter[field]=value` – Exakte Filterung
- `sort=field` oder `sort=-field` (- = absteigend)
- `search=query` – Volltextsuche
- `fields=field1,field2` – Partial responses
- `embed=relation1,relation2` – Expand relationships

## 9. Rate Limiting

### Headers in Response

```http
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 987
X-RateLimit-Reset: 1699862400
Retry-After: 60  # Bei 429 Status
```

### Limits pro Authentifizierungs-Typ

| Typ | Requests/Stunde | Burst/Minute | Grund |
|-----|-----------------|--------------|-------|
| OAuth2 | 10.000 | 100 | Externe Partner |
| API Key | 5.000 | 50 | Machine-to-Machine |
| SSO | 100.000 | 500 | Interne Nutzer |

## 10. Timeouts

### Standards

- **Connection Timeout**: 10 Sekunden
- **Read Timeout**: 30 Sekunden (für GET, List)
- **Read Timeout**: 60 Sekunden (für POST, PATCH, DELETE)
- **Total Request Timeout**: 90 Sekunden

## 11. Logging & Audit Trail

### Pflicht-Logging

```json
{
  "timestamp": "2025-11-13T10:30:00Z",
  "request_id": "uuid",
  "flow_id": "uuid",
  "method": "POST",
  "path": "/api/v1/orders",
  "status": 201,
  "duration_ms": 245,
  "user_id": "user-456",
  "tenant_id": "tenant-123",
  "auth_type": "oauth2",
  "ip_address": "192.168.1.1",
  "user_agent": "CustomApp/1.0"
}
```

## 12. Monitoring & Alerting

### Metriken

Tracke pro Endpoint:

- **Latency**: P50, P95, P99 (in ms)
- **Error Rate**: % fehlgeschlagener Requests
- **Throughput**: Requests/Sekunde
- **Rate Limit Usage**: % der verfügbaren Quota

### Alert-Schwellwerte

| Metrik | Schwelle | Action |
|--------|----------|--------|
| Error Rate | > 5% | Page On-Call |
| Latency P99 | > 5000ms | Investigate |
| Service Down | > 60s | Page On-Call |
| Rate Limit Hit Rate | > 10% | Notify Partner |

## 13. Versionierung & Deprecation

### URL-basiert

```
https://api.example.com/v1/...
https://api.example.com/v2/...
```

**Alternative**: Version in der Subdomain

```
https://v1.api.example.com/...
https://v2.api.example.com/...
```

### Semantic Versioning

MUST use semantic versioning (MAJOR.MINOR.PATCH):

- **MAJOR**: Breaking changes
- **MINOR**: Backward-compatible additions
- **PATCH**: Backward-compatible fixes

### Deprecation Policy

MUST follow deprecation process:

1. **Announcement**: Mindestens 6 Monate vor Deprecation
2. **Deprecation Headers**:
   ```http
   Deprecation: true
   Sunset: Wed, 13 Nov 2026 00:00:00 GMT
   Link: </api/v2/...>; rel="successor-version"
   Warning: 299 - "This API version is deprecated and will sunset on 2026-11-13"
   ```
3. **Support Period**: Mindestens 12 Monate nach neuer Version
4. **Monitoring**: Track usage of deprecated endpoints
5. **Communication**: Aktive Benachrichtigung aller Konsumenten

## 14. Compatibility Rules

### MUST maintain backward compatibility

#### Compatible changes (erlaubt ohne Versionswechsel):

- Neue optionale Request-Parameter
- Neue Response-Felder
- Neue Ressourcen oder Endpoints
- Neue optionale Header
- Neue Enum-Werte (mit x-extensible-enum)

#### Breaking changes (erfordern neue Major Version):

- Entfernen/Umbenennen von Feldern
- Ändern von Feldtypen
- Neue required Parameter
- Ändern von URL-Struktur
- Ändern von Authentifizierung

### x-extensible-enum

SHOULD use for enumerations that may grow:

```yaml
status:
  type: string
  x-extensible-enum:
    - DRAFT
    - ACTIVE
    - ARCHIVED
  description: Status may contain additional values in future
```

## 15. Idempotenz

### Idempotency Patterns

MUST implement at least one pattern for POST/PATCH:

#### 1. Conditional Key (ETag)

```http
PUT /resources/{id}
If-Match: "33a64df551425fcc55e4d42a148795d9f25f89d4"
```

#### 2. Secondary Key (Business Key)

```http
POST /orders
{
  "cart_id": "cart-789",  // Secondary key
  "items": [...]
}
```

Server prüft `cart_id` Uniqueness → 409 bei Duplikat

#### 3. Idempotency-Key Header

```http
POST /api/v1/orders
Idempotency-Key: client-generated-uuid
```

Server speichert Key und Response für 24h.

### PATCH-Einschränkung

Der Idempotency-Key bei PATCH funktioniert nur für deskriptive Updates:

- ✅ **Erlaubt**: `{ "price": 99.99, "status": "active" }`
- ❌ **Nicht erlaubt**: `{ "op": "increment", "path": "/stock", "value": 5 }`

Bei operativen PATCH-Requests:

```http
Warning: 299 - "Idempotency-Key ignored for non-descriptive PATCH operation"
```

## 16. Batch Operations

### Batch Get

```http
POST /api/v1/products:batchGet
{
  "ids": ["prod-1", "prod-2", "prod-3"]
}
```

### Batch Create/Update mit 207 Multi-Status

```http
POST /api/v1/products:batchCreate
```

Response (207 Multi-Status):

```json
{
  "succeeded": [
    { "id": "prod-1", "status": 201 }
  ],
  "failed": [
    {
      "index": 2,
      "status": 409,
      "error": { "code": "DUPLICATE", "message": "Product already exists" }
    }
  ]
}
```

## 17. CORS & Security

### CORS-Header

```http
Access-Control-Allow-Origin: https://partner-domain.de
Access-Control-Allow-Methods: GET, POST, PATCH, DELETE
Access-Control-Allow-Headers: Content-Type, Authorization, X-API-Key
Access-Control-Max-Age: 3600
```

### Security Header (alle Responses)

```http
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
Strict-Transport-Security: max-age=31536000
Content-Security-Policy: default-src 'none'
```

## 18. Dokumentation

### OpenAPI/Swagger

- MUST provide OpenAPI 3.1 specification
- Accessible unter: `/api/v1/openapi.yaml`
- Swagger UI unter: `/api/v1/docs`
- SHOULD provide API user manual with examples

### API Linting

SHOULD use automated API linting for:

- Naming conventions
- Required fields
- Security definitions
- Response formats
- Deprecation markers

## 19. Datentypen & Formate

### Standard Formate

- **IDs**: UUIDv7 (sortierbar, timestamp-basiert)
  - Alternative: UUIDv4 nur wenn maximale Zufälligkeit kritisch
  - Format: `^[a-zA-Z0-9][a-zA-Z0-9-_:]*$`
- **Datum/Zeit**: ISO 8601 mit UTC (z.B. `2025-11-13T10:30:00Z`)
- **Date**: date format für Tage ohne Uhrzeit
- **Duration**: ISO 8601 durations (z.B. `P1DT12H` = 1 Tag, 12 Stunden)
- **Geld**: Decimal mit Currency-Code
- **Phone**: E.164 Format (z.B. `+49301234567`)
- **Country**: ISO 3166-1 alpha-2 (z.B. `DE`)
- **Language**: ISO 639-1 (z.B. `de`) oder BCP 47 (z.B. `de-DE`)
- **Currency**: ISO 4217 (z.B. `EUR`)

### Common Money Object

```json
{
  "amount": 99.95,
  "currency": "EUR"
}
```

**MUST NOT** extend Money object. Use composition instead:

```json
{
  "price": { "amount": 19.99, "currency": "EUR" },
  "discounted_price": { "amount": 9.99, "currency": "EUR" }
}
```

### Enums

SHOULD use UPPER_SNAKE_CASE for enum values:

```json
{
  "status": "ACTIVE",
  "priority": "HIGH"
}
```

## 20. Ressourcen-Spezifikationen

### Beispiel: Product Resource

```json
{
  "id": "0192f8f0-5ff2-7000-8000-000000000001",
  "sku": "PROD-001",
  "name": "Example Product",
  "description": "Product description",
  "category": "electronics",
  "price": {
    "amount": 99.99,
    "currency": "EUR"
  },
  "status": "ACTIVE",
  "stock": {
    "available": 125,
    "reserved": 25,
    "total": 150
  },
  "metadata": {
    "created_at": "2025-11-13T10:30:00Z",
    "updated_at": "2025-11-13T10:30:00Z",
    "version": 1
  }
}
```

### Beispiel: User Resource

```json
{
  "id": "0192f8f0-6ff2-7000-8000-000000000002",
  "username": "john.doe",
  "email": "john.doe@example.com",
  "profile": {
    "first_name": "John",
    "last_name": "Doe",
    "avatar_url": "https://cdn.example.com/avatars/john-doe.jpg"
  },
  "roles": ["USER", "ADMIN"],
  "preferences": {
    "language": "en",
    "timezone": "UTC",
    "notifications": {
      "email": true,
      "push": false
    }
  },
  "metadata": {
    "created_at": "2025-11-13T10:30:00Z",
    "updated_at": "2025-11-13T10:30:00Z",
    "last_login": "2025-11-13T09:15:00Z",
    "version": 2
  }
}
```

## 21. Testing Requirements

### MUST implement tests

- Unit Tests für Business Logic
- Integration Tests für API Endpoints
- Contract Tests für API Consumers
- Performance Tests für kritische Endpoints
- Security Tests (OWASP API Top 10)

## 22. API Governance

### API Review Process

MUST conduct API reviews for:

- New APIs
- Breaking changes
- External/Partner APIs

#### Review-Kriterien:

- ✅ Guideline Compliance
- ✅ Security Requirements
- ✅ Performance Impact
- ✅ Documentation Quality
- ✅ Consumer Impact

### API Registry

Maintain central registry with:

- API Inventory
- Version Status
- Consumer Tracking
- Deprecation Schedule
- Contact Information

## 23. Implementierungs-Checkliste

Bei neuen Endpoints:

- [ ] Resource-ID mit UUIDv7 (oder UUIDv4 wenn Security kritisch)
- [ ] Alle Standard Methods implementiert (GET, POST, PATCH, DELETE)
- [ ] Problem JSON für Error Responses
- [ ] Pagination für List-Endpoints
- [ ] Link-Header für HATEOAS Navigation
- [ ] Request-ID und Flow-ID Tracing
- [ ] Logging (Request + Audit)
- [ ] Rate Limit Headers
- [ ] OpenAPI 3.1 Dokumentation
- [ ] Authentifizierung (OAuth2/API Key/SSO)
- [ ] Authorization Checks
- [ ] Input Validierung
- [ ] Timeout-Handling
- [ ] CORS-Header
- [ ] Security-Header
- [ ] Idempotency Support (POST/PATCH)
- [ ] Deprecation Headers (wenn applicable)
- [ ] Tests (Unit + Integration)
- [ ] Performance-Monitoring
- [ ] API Linting durchgeführt
- [ ] Review-Prozess durchlaufen

## Anhang A: Referenzen

- [RFC 2119 - Requirement Level Keywords](https://www.rfc-editor.org/rfc/rfc2119)
- [RFC 7807 - Problem Details for HTTP APIs](https://www.rfc-editor.org/rfc/rfc7807)
- [RFC 9110 - HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110)
- [OpenAPI 3.1 - Specification](https://spec.openapis.org/oas/v3.1.0)
- [JSON Schema - Validation](https://json-schema.org/)
- [Google AIP - API Improvement Proposals](https://google.aip.dev/)

## Anhang B: Glossar

- **Idempotent**: Operation hat bei mehrfacher Ausführung dasselbe Ergebnis
- **Safe**: Operation hat keine Seiteneffekte
- **HATEOAS**: Hypermedia as the Engine of Application State
- **ETag**: Entity Tag für Optimistic Locking
- **Problem JSON**: Standardisiertes Error-Response Format (RFC 7807)
- **Deprecation**: Ankündigung der zukünftigen Entfernung
- **Sunset**: Datum der tatsächlichen Abschaltung

## Weitere Ressourcen

- [Architecture Guidelines](ARCHITECTURE.md)
- [Coding Standards](CODING_STANDARDS.md)
- [Security Guidelines](../SECURITY.md)
- [Testing Guidelines](TESTING.md)

---

**Version**: 2.0
**Zuletzt aktualisiert**: 2024-11
**Maintainer**: API Team
