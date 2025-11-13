# Issue-Label-Guide

Dieser Guide erklärt die verwendeten Labels für Issues und Pull Requests im Repository.

## 📋 Inhaltsverzeichnis

- [Label-Kategorien](#label-kategorien)
- [Typ-Labels](#typ-labels)
- [Status-Labels](#status-labels)
- [Prioritäts-Labels](#prioritäts-labels)
- [Bereichs-Labels](#bereichs-labels)
- [Weitere Labels](#weitere-labels)
- [Label-Kombinationen](#label-kombinationen)

## Label-Kategorien

Labels sind nach folgenden Kategorien organisiert:

- **Typ**: Was für eine Art von Issue ist es?
- **Status**: In welchem Zustand ist das Issue?
- **Priorität**: Wie dringend ist es?
- **Bereich**: Welchen Teil des Projekts betrifft es?
- **Sonstige**: Weitere Meta-Informationen

## Typ-Labels

### 🐛 `bug`
**Beschreibung**: Ein Fehler oder unerwartetes Verhalten

**Wann verwenden:**
- Etwas funktioniert nicht wie erwartet
- Fehler oder Exceptions
- Unerwartetes UI-Verhalten

**Beispiele:**
- Login-Button funktioniert nicht
- Datenbankverbindung schlägt fehl
- Falsche Berechnung in Checkout

---

### ✨ `feature`
**Beschreibung**: Neue Funktionalität oder Enhancement

**Wann verwenden:**
- Neue Features
- Verbesserungen bestehender Features
- Neue Capabilities

**Beispiele:**
- OAuth2-Login hinzufügen
- Dark Mode implementieren
- Export-Funktion für Daten

---

### 📝 `documentation`
**Beschreibung**: Verbesserungen oder Ergänzungen zur Dokumentation

**Wann verwenden:**
- README Updates
- API-Dokumentation
- Code-Kommentare
- Tutorials/Guides

**Beispiele:**
- API-Endpoint dokumentieren
- Installation Guide aktualisieren
- Beispiele hinzufügen

---

### 🔧 `refactor`
**Beschreibung**: Code-Umstrukturierung ohne Funktionsänderung

**Wann verwenden:**
- Code Cleanup
- Verbesserung der Code-Struktur
- Performance-Optimierung
- Technische Schulden abbauen

**Beispiele:**
- Services extrahieren
- Duplicate Code entfernen
- Algorithmus optimieren

---

### 🧪 `test`
**Beschreibung**: Tests hinzufügen, aktualisieren oder fixen

**Wann verwenden:**
- Neue Tests schreiben
- Test Coverage verbessern
- Flaky Tests fixen
- Test-Infrastruktur

**Beispiele:**
- Unit Tests für UserService
- E2E Test für Checkout
- Test-Setup verbessern

---

### 🔨 `chore`
**Beschreibung**: Maintenance-Aufgaben, Build, Dependencies

**Wann verwenden:**
- Dependency Updates
- Build-Konfiguration
- CI/CD Änderungen
- Tooling Updates

**Beispiele:**
- Dependencies aktualisieren
- Webpack-Konfiguration anpassen
- GitHub Actions Workflow

---

### 🚀 `deployment`
**Beschreibung**: Deployment-bezogene Issues

**Wann verwenden:**
- Deployment-Probleme
- Infrastructure Changes
- Deployment-Prozess Verbesserungen

**Beispiele:**
- Deployment auf AWS konfigurieren
- Docker-Setup optimieren
- Deployment-Fehler beheben

---

### 🔒 `security`
**Beschreibung**: Sicherheitsrelevante Issues

**Wann verwenden:**
- Sicherheitslücken
- Security Audits
- Authentifizierung/Autorisierung

**Beispiele:**
- SQL Injection Vulnerability
- XSS-Schutz implementieren
- Dependency Security Update

## Status-Labels

### ⏳ `status: pending`
**Beschreibung**: Wartet auf weitere Informationen oder Entscheidungen

**Verwendung:**
- Mehr Informationen vom Reporter benötigt
- Wartet auf Entscheidung von Stakeholdern
- Blocked durch andere Issues

---

### 🚧 `status: in progress`
**Beschreibung**: Wird aktuell bearbeitet

**Verwendung:**
- Jemand arbeitet aktiv daran
- Pull Request ist in Arbeit

---

### 👀 `status: review`
**Beschreibung**: Wartet auf Code Review

**Verwendung:**
- Pull Request erstellt
- Wartet auf Reviewer Feedback

---

### ✅ `status: done`
**Beschreibung**: Erledigt und gemerged

**Verwendung:**
- Pull Request gemerged
- Issue kann geschlossen werden

---

### ❌ `status: wontfix`
**Beschreibung**: Wird nicht behoben/implementiert

**Verwendung:**
- Out of Scope
- Nicht reproduzierbar
- Working as intended

---

### 🔄 `status: blocked`
**Beschreibung**: Kann nicht fortgesetzt werden

**Verwendung:**
- Abhängig von anderem Issue
- Wartet auf externe Dependencies
- Technische Blocker

## Prioritäts-Labels

### 🔴 `priority: critical`
**Beschreibung**: Höchste Priorität - sofort beheben

**Wann verwenden:**
- Production ist down
- Kritischer Security Bug
- Datenverlust möglich
- Viele User betroffen

**SLA**: Sofort / Innerhalb 24h

---

### 🟠 `priority: high`
**Beschreibung**: Hohe Priorität - schnell beheben

**Wann verwenden:**
- Wichtiges Feature blockiert
- Performance-Probleme
- Wichtiger Bug

**SLA**: Innerhalb 1 Woche

---

### 🟡 `priority: medium`
**Beschreibung**: Normale Priorität

**Wann verwenden:**
- Reguläre Bugs
- Feature Requests
- Verbesserungen

**SLA**: Innerhalb 2-4 Wochen

---

### 🟢 `priority: low`
**Beschreibung**: Niedrige Priorität

**Wann verwenden:**
- Nice-to-have Features
- Kleine Bugs ohne große Auswirkung
- Kosmetische Issues

**SLA**: Best Effort

## Bereichs-Labels

### 🎨 `area: frontend`
**Beschreibung**: Betrifft Frontend/UI

**Beispiele:**
- React Components
- CSS/Styling
- User Interface

---

### ⚙️ `area: backend`
**Beschreibung**: Betrifft Backend/API

**Beispiele:**
- API Endpoints
- Business Logic
- Database

---

### 🗄️ `area: database`
**Beschreibung**: Betrifft Datenbank

**Beispiele:**
- Migrations
- Schema Changes
- Queries

---

### 🔐 `area: auth`
**Beschreibung**: Betrifft Authentifizierung/Autorisierung

**Beispiele:**
- Login/Logout
- Permissions
- Session Management

---

### 📱 `area: mobile`
**Beschreibung**: Betrifft mobile Apps

**Beispiele:**
- iOS App
- Android App
- Mobile Responsive

---

### 🧰 `area: infrastructure`
**Beschreibung**: Betrifft Infrastruktur

**Beispiele:**
- CI/CD
- Docker
- Kubernetes
- Monitoring

---

### 📊 `area: api`
**Beschreibung**: Betrifft API/Integration

**Beispiele:**
- REST API
- GraphQL
- Webhooks

## Weitere Labels

### 🆘 `help wanted`
**Beschreibung**: Hilfe von Community gewünscht

**Wann verwenden:**
- Gutes Issue für neue Contributors
- Externe Expertise benötigt

---

### 🎓 `good first issue`
**Beschreibung**: Gut für neue Contributors

**Wann verwenden:**
- Einfaches Issue
- Gute Dokumentation vorhanden
- Niedrige Komplexität

---

### ❓ `question`
**Beschreibung**: Frage zur Nutzung oder zum Projekt

**Wann verwenden:**
- How-to Fragen
- Klärungsbedarf
- Nicht sicher ob Bug oder Feature

---

### 💬 `discussion`
**Beschreibung**: Diskussion über Design/Implementation

**Wann verwenden:**
- Architektur-Entscheidungen
- API-Design
- Prozess-Änderungen

---

### 🔄 `duplicate`
**Beschreibung**: Duplikat eines anderen Issues

**Wann verwenden:**
- Issue existiert bereits
- Referenz zum Original Issue

---

### 🚫 `invalid`
**Beschreibung**: Ungültiges Issue

**Wann verwenden:**
- Keine valide Issue
- Spam
- Off-topic

---

### ⚠️ `breaking change`
**Beschreibung**: Enthält Breaking Changes

**Wann verwenden:**
- API-Breaking Changes
- Nicht-rückwärtskompatibel
- Migration erforderlich

---

### 🧪 `experimental`
**Beschreibung**: Experimentelles Feature

**Wann verwenden:**
- Proof of Concept
- Beta-Features
- Instabile APIs

---

### 🏷️ `needs triage`
**Beschreibung**: Muss noch bewertet werden

**Wann verwenden:**
- Neu erstelltes Issue
- Noch nicht kategorisiert
- Priorität unklar

## Label-Kombinationen

### Beispiel 1: Kritischer Production Bug
```
🐛 bug
🔴 priority: critical
⚙️ area: backend
🔒 security
```

### Beispiel 2: Feature Request für neuen Contributor
```
✨ feature
🟢 priority: low
🎨 area: frontend
🎓 good first issue
```

### Beispiel 3: Refactoring mit Breaking Change
```
🔧 refactor
🟠 priority: high
⚙️ area: backend
⚠️ breaking change
```

### Beispiel 4: Dokumentations-Verbesserung
```
📝 documentation
🟡 priority: medium
🆘 help wanted
```

## Label-Workflow

### 1. Neues Issue

```
Erstellt → [needs triage]
    ↓
Maintainer bewertet
    ↓
Labels hinzufügen:
- Typ (bug, feature, etc.)
- Priorität
- Bereich
- Status
```

### 2. In Bearbeitung

```
[pending] → [in progress] → [review] → [done]
```

### 3. Issue schließen

Wenn geschlossen mit `wontfix`, `duplicate`, oder `invalid`:
- Begründung im Kommentar
- Referenzen hinzufügen (bei duplicate)
- Issue schließen

## Best Practices

### Für Issue-Ersteller

- ✅ Verwenden Sie Issue Templates
- ✅ Seien Sie spezifisch
- ✅ Fügen Sie Kontext hinzu
- ✅ Markieren Sie mit `needs triage` wenn unsicher

### Für Maintainer

- ✅ Triagen Sie neue Issues zeitnah
- ✅ Verwenden Sie konsistente Labels
- ✅ Erkären Sie Label-Entscheidungen
- ✅ Aktualisieren Sie Labels bei Statusänderung

### Label-Pflege

- Regelmäßig Labels aktualisieren
- Alte/ungenutzte Labels entfernen
- Bei Bedarf neue Labels anlegen
- Label-Beschreibungen aktuell halten

## Label-Farben (Empfehlung)

```
Typ:
- bug:          #d73a4a (rot)
- feature:      #a2eeef (hellblau)
- documentation:#0075ca (blau)
- refactor:     #fbca04 (gelb)

Status:
- in progress:  #1d76db (blau)
- review:       #0e8a16 (grün)
- blocked:      #b60205 (rot)

Priorität:
- critical:     #b60205 (dunkelrot)
- high:         #d93f0b (orange)
- medium:       #fbca04 (gelb)
- low:          #0e8a16 (grün)

Bereich:
- frontend:     #c5def5 (hellblau)
- backend:      #f9d0c4 (rosa)
- database:     #c2e0c6 (hellgrün)
```

## Weitere Ressourcen

- [Contributing Guidelines](../CONTRIBUTING.md)
- [Pull Request Template](pull_request_template/pull_request_template.md)
- [Bug Report Template](issue_template/bug_report.md)
- [Feature Request Template](issue_template/feature_request.md)

---

**Version**: 1.0
**Zuletzt aktualisiert**: 2025-11-13
