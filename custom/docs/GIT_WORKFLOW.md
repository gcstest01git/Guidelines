# Git-Workflow und Branch-Strategie

Diese Dokumentation beschreibt den Git-Workflow, Branch-Strategien und Best Practices für die Versionskontrolle.

## 📋 Inhaltsverzeichnis

- [Branch-Strategie](#branch-strategie)
- [Workflow](#workflow)
- [Commit-Richtlinien](#commit-richtlinien)
- [Pull Request Prozess](#pull-request-prozess)
- [Code Review](#code-review)
- [Merge-Strategien](#merge-strategien)
- [Git Best Practices](#git-best-practices)
- [Troubleshooting](#troubleshooting)

## Branch-Strategie

Wir verwenden eine **Git Flow** inspirierte Branch-Strategie.

### Branch-Typen

```
main (oder master)
  ↓
develop
  ↓
feature/*, bugfix/*, hotfix/*, release/*
```

#### Main Branch

- **Name**: `main` (oder `master`)
- **Zweck**: Produktions-Code
- **Schutz**: Geschützt, nur via Pull Request
- **Deployment**: Automatisch nach Merge

```bash
# Direkte Commits sind nicht erlaubt!
# Nur Merges von release/* und hotfix/* Branches
```

#### Develop Branch

- **Name**: `develop`
- **Zweck**: Integration Branch für Features
- **Basis**: Von `main` abgezweigt
- **Merges**: Feature- und Bugfix-Branches

```bash
# Erstellen (nur einmalig)
git checkout -b develop main
```

#### Feature Branches

- **Name**: `feature/<kurze-beschreibung>`
- **Zweck**: Neue Features entwickeln
- **Basis**: Von `develop` abgezweigt
- **Merge**: Zurück in `develop`

```bash
# Erstellen
git checkout -b feature/user-authentication develop

# Beispiele
feature/user-authentication
feature/payment-integration
feature/dark-mode
```

#### Bugfix Branches

- **Name**: `bugfix/<kurze-beschreibung>`
- **Zweck**: Bugs in `develop` fixen
- **Basis**: Von `develop` abgezweigt
- **Merge**: Zurück in `develop`

```bash
# Erstellen
git checkout -b bugfix/login-validation develop

# Beispiele
bugfix/login-validation
bugfix/memory-leak
bugfix/broken-navigation
```

#### Hotfix Branches

- **Name**: `hotfix/<version-oder-beschreibung>`
- **Zweck**: Kritische Bugs in Production fixen
- **Basis**: Von `main` abgezweigt
- **Merge**: In `main` UND `develop`

```bash
# Erstellen
git checkout -b hotfix/1.2.1 main

# Beispiele
hotfix/1.2.1
hotfix/critical-security-fix
hotfix/payment-gateway-down
```

#### Release Branches

- **Name**: `release/<version>`
- **Zweck**: Release vorbereiten
- **Basis**: Von `develop` abgezweigt
- **Merge**: In `main` und `develop`

```bash
# Erstellen
git checkout -b release/1.3.0 develop

# Beispiele
release/1.3.0
release/2.0.0-beta
```

### Branch-Übersicht Diagramm

```
main        ─●────────────────●────●──────────→
             │                 ↑    ↑
             │               merge merge
             ↓                 │    │
develop     ─●─────●───●──────●────●──────────→
             │     ↑   ↑       │    │
             │   merge merge   │    │
             ↓     │   │       │    │
feature/*       ─●─●   │     release/1.0
                       │       │
bugfix/*              ─●       │
                               │
hotfix/*                      ─●
```

## Workflow

### Typischer Feature-Workflow

#### 1. Branch erstellen

```bash
# Sicherstellen, dass develop aktuell ist
git checkout develop
git pull origin develop

# Feature Branch erstellen
git checkout -b feature/my-awesome-feature
```

#### 2. Entwicklung

```bash
# Arbeiten und committen
git add .
git commit -m "feat: implement user authentication"

# Weitere Commits
git commit -m "test: add authentication tests"
git commit -m "docs: update API documentation"
```

#### 3. Branch aktuell halten

```bash
# Regelmäßig von develop pullen
git checkout develop
git pull origin develop
git checkout feature/my-awesome-feature
git rebase develop

# Oder merge (wenn rebase nicht gewünscht)
git merge develop
```

#### 4. Push zum Remote

```bash
# Erstmaliges Push
git push -u origin feature/my-awesome-feature

# Weitere Pushes
git push
```

#### 5. Pull Request erstellen

- Auf GitHub/GitLab/Bitbucket gehen
- Pull Request von `feature/my-awesome-feature` → `develop` erstellen
- Template ausfüllen (siehe [Pull Request Template](../.github/pull_request_template/pull_request_template.md))
- Reviewer zuweisen

#### 6. Code Review und Merge

- Review-Kommentare bearbeiten
- Nach Approval: Merge in `develop`
- Branch löschen

```bash
# Nach Merge: Lokalen Branch löschen
git checkout develop
git pull origin develop
git branch -d feature/my-awesome-feature
```

### Hotfix-Workflow

#### 1. Hotfix Branch erstellen

```bash
git checkout main
git pull origin main
git checkout -b hotfix/1.2.1
```

#### 2. Fix implementieren

```bash
git add .
git commit -m "fix: critical security vulnerability in auth"
```

#### 3. Version bumpen

```bash
# package.json, version files, etc. aktualisieren
git commit -m "chore: bump version to 1.2.1"
```

#### 4. In main mergen

```bash
git checkout main
git merge --no-ff hotfix/1.2.1
git tag -a v1.2.1 -m "Version 1.2.1 - Security Hotfix"
git push origin main --tags
```

#### 5. Auch in develop mergen

```bash
git checkout develop
git merge --no-ff hotfix/1.2.1
git push origin develop
```

#### 6. Branch löschen

```bash
git branch -d hotfix/1.2.1
git push origin --delete hotfix/1.2.1
```

### Release-Workflow

#### 1. Release Branch erstellen

```bash
git checkout develop
git pull origin develop
git checkout -b release/1.3.0
```

#### 2. Release vorbereiten

```bash
# Version bumpen
npm version 1.3.0
git commit -m "chore: bump version to 1.3.0"

# CHANGELOG aktualisieren
git commit -m "docs: update CHANGELOG for v1.3.0"

# Letzte Bugfixes (nur kritische!)
git commit -m "fix: minor issue before release"
```

#### 3. In main mergen

```bash
git checkout main
git merge --no-ff release/1.3.0
git tag -a v1.3.0 -m "Version 1.3.0"
git push origin main --tags
```

#### 4. Zurück in develop mergen

```bash
git checkout develop
git merge --no-ff release/1.3.0
git push origin develop
```

#### 5. Branch löschen

```bash
git branch -d release/1.3.0
git push origin --delete release/1.3.0
```

## Commit-Richtlinien

### Conventional Commits

Wir folgen dem [Conventional Commits](https://www.conventionalcommits.org/) Standard.

#### Format

```
<type>(<scope>): <subject>

<body>

<footer>
```

#### Types

| Type       | Beschreibung                                    |
|------------|-------------------------------------------------|
| `feat`     | Neue Funktion                                   |
| `fix`      | Fehlerbehebung                                  |
| `docs`     | Nur Dokumentationsänderungen                    |
| `style`    | Formatierung (keine Code-Logik-Änderungen)     |
| `refactor` | Code-Umstrukturierung                           |
| `perf`     | Performance-Verbesserung                        |
| `test`     | Tests hinzufügen/korrigieren                    |
| `chore`    | Build, Dependencies, etc.                       |
| `ci`       | CI/CD Konfiguration                             |
| `build`    | Build-System Änderungen                         |
| `revert`   | Commit zurücknehmen                             |

#### Scope (Optional)

```
feat(auth): add OAuth2 login
fix(api): handle null values in user endpoint
docs(readme): update installation instructions
```

#### Subject

- Imperativ ("add" nicht "added" oder "adds")
- Kein Punkt am Ende
- Maximal 50 Zeichen
- Kleinbuchstaben nach dem Doppelpunkt

```
✅ feat: add user authentication
❌ feat: Added user authentication.
❌ feat: Adds user authentication
```

#### Body (Optional)

- Erklärt das "Was" und "Warum", nicht das "Wie"
- Leerzeile nach Subject
- Wrap bei 72 Zeichen

```
feat: add user authentication

This commit implements OAuth2 authentication flow.
Users can now log in using their Google or GitHub accounts.

The implementation uses the passport library and follows
the OAuth2 standard.
```

#### Footer (Optional)

```
feat: add user authentication

Implements OAuth2 flow with Google and GitHub providers.

BREAKING CHANGE: Old session-based auth is removed.
Closes #123, #456
```

### Commit Best Practices

#### 1. Atomare Commits

```bash
# ✅ Gut: Ein logischer Change pro Commit
git commit -m "feat: add login form"
git commit -m "feat: add login validation"
git commit -m "test: add login form tests"

# ❌ Schlecht: Alles in einem Commit
git commit -m "add login feature with tests and docs"
```

#### 2. Regelmäßig committen

```bash
# ✅ Commit nach jeder abgeschlossenen Einheit
# Nicht stundenlang ohne Commit arbeiten

# Small, frequent commits:
- Einfacher zu reviewen
- Einfacher zu revertieren
- Bessere Historie
```

#### 3. Sinnvolle Commit-Messages

```bash
# ❌ Schlecht
git commit -m "fix"
git commit -m "update"
git commit -m "WIP"
git commit -m "asdf"

# ✅ Gut
git commit -m "fix: handle null values in user profile"
git commit -m "refactor: extract validation logic into util"
git commit -m "docs: add API endpoint documentation"
```

#### 4. Tests committen

```bash
# ✅ Tests mit dem Feature committen (oder separat)
git add src/auth.js src/auth.test.js
git commit -m "feat: add OAuth2 authentication"
```

## Pull Request Prozess

### Vor dem PR

- [ ] Branch ist auf dem neuesten Stand
- [ ] Alle Tests laufen durch
- [ ] Code ist entsprechend Style Guide formatiert
- [ ] Keine Linter-Warnings
- [ ] Dokumentation aktualisiert
- [ ] Selbst-Review durchgeführt

### PR erstellen

1. **Beschreibender Titel**
   ```
   ✅ feat: Add user authentication with OAuth2
   ❌ Update auth
   ```

2. **Template ausfüllen**
   - Was wurde geändert?
   - Warum wurde es geändert?
   - Wie wurde es getestet?
   - Screenshots (bei UI-Änderungen)
   - Breaking Changes?

3. **Labels zuweisen**
   - `feature`, `bugfix`, `hotfix`, etc.
   - `documentation`
   - `breaking-change`

4. **Reviewer zuweisen**
   - Mindestens 1-2 Reviewer
   - Code Owner (falls definiert)

### PR-Beschreibung Template

```markdown
## Beschreibung
Kurze Beschreibung der Änderungen.

## Änderungen
- Feature A implementiert
- Bug B behoben
- Dokumentation aktualisiert

## Testing
- Unit Tests hinzugefügt
- Integration Tests bestehen
- Manuell getestet auf Chrome, Firefox, Safari

## Screenshots (falls UI-Änderungen)
![Before](url)
![After](url)

## Breaking Changes
- [ ] Ja
- [x] Nein

Falls ja, beschreiben Sie die Breaking Changes.

## Checklist
- [x] Tests hinzugefügt/aktualisiert
- [x] Dokumentation aktualisiert
- [x] Code entspricht Style Guide
- [x] Keine Linter-Warnings
- [x] Selbst-Review durchgeführt
```

## Code Review

### Reviewer-Verantwortlichkeiten

- **Zeitnah reviewen** (innerhalb 24h)
- **Konstruktives Feedback** geben
- **Code verstehen**, nicht nur überfliegen
- **Tests prüfen**
- **Dokumentation prüfen**

### Review-Checkliste

#### Funktionalität
- [ ] Code macht was er soll
- [ ] Keine offensichtlichen Bugs
- [ ] Edge Cases berücksichtigt

#### Code-Qualität
- [ ] Code ist lesbar und verständlich
- [ ] Folgt Coding Standards
- [ ] Keine unnötige Komplexität
- [ ] DRY Prinzip befolgt

#### Tests
- [ ] Tests vorhanden und sinnvoll
- [ ] Test Coverage ausreichend
- [ ] Tests sind wartbar

#### Sicherheit
- [ ] Keine Sicherheitslücken
- [ ] Input Validation vorhanden
- [ ] Keine hardcoded Secrets

#### Performance
- [ ] Keine offensichtlichen Performance-Probleme
- [ ] Algorithmen effizient
- [ ] Keine Memory Leaks

#### Dokumentation
- [ ] Komplexe Logik dokumentiert
- [ ] API-Änderungen dokumentiert
- [ ] README aktualisiert (falls nötig)

### Review-Kommentare

```markdown
# ✅ Konstruktiv
"Hier könnte man die Methode `extractUserData()` nutzen,
um Code-Duplikation zu vermeiden. Was denkst du?"

"Guter Ansatz! Hast du auch den Edge Case bedacht, wenn
der User kein Profil hat?"

# ❌ Nicht konstruktiv
"Das ist schlecht."
"Warum hast du das so gemacht?"
```

### Arten von Review-Kommentaren

```markdown
# Kritisch (muss behoben werden)
🔴 Critical: Diese SQL-Query ist anfällig für Injection!

# Vorschlag (sollte berücksichtigt werden)
🟡 Suggestion: Könnten wir hier async/await verwenden?

# Frage (zur Klärung)
🔵 Question: Warum wurde dieser Ansatz gewählt?

# Lob (positive Verstärkung)
🟢 Nice: Gute Lösung für dieses komplexe Problem!

# Nitpick (optional)
⚪ Nitpick: Typo in Kommentar (nicht blockierend)
```

## Merge-Strategien

### Merge Commit (--no-ff)

```bash
git merge --no-ff feature/my-feature
```

**Vorteile:**
- Erhält vollständige Historie
- Zeigt klar, wo Features gemerged wurden

**Nachteile:**
- Viele Merge Commits
- Historie kann unübersichtlich werden

**Wann verwenden:**
- Feature Branches → develop
- Release/Hotfix → main

### Squash Merge

```bash
git merge --squash feature/my-feature
git commit -m "feat: add new feature"
```

**Vorteile:**
- Saubere, lineare Historie
- Ein Commit pro Feature

**Nachteile:**
- Verliert Detail-Historie

**Wann verwenden:**
- Viele kleine WIP Commits
- Feature mit vielen Iterations

### Rebase and Merge

```bash
git rebase develop
git checkout develop
git merge feature/my-feature
```

**Vorteile:**
- Lineare Historie
- Keine Merge Commits

**Nachteile:**
- Ändert Historie
- Kann bei Konflikten komplex sein

**Wann verwenden:**
- Persönliche Feature Branches
- Vor dem Merge in develop

## Git Best Practices

### .gitignore

```bash
# Dependencies
node_modules/
vendor/

# Environment
.env
.env.local
*.env

# IDE
.vscode/
.idea/
*.swp

# OS
.DS_Store
Thumbs.db

# Build
dist/
build/
*.log
```

### .gitattributes

```bash
# Auto detect text files and perform LF normalization
* text=auto

# Force LF for source code
*.js text eol=lf
*.ts text eol=lf
*.jsx text eol=lf
*.tsx text eol=lf

# Force CRLF for Windows files
*.bat text eol=crlf

# Denote binary files
*.png binary
*.jpg binary
```

### Git Hooks

```bash
# .husky/pre-commit
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npm run lint
npm run test
```

### Sensitive Data

```bash
# ❌ NIEMALS committen:
- Passwörter
- API Keys
- Private Keys
- Credentials
- .env Dateien

# ✅ Stattdessen:
- .env.example committen
- Secrets in Environment Variables
- Verwenden Sie Secret Management Tools
```

### Git Konfiguration

```bash
# User Config
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Editor
git config --global core.editor "code --wait"

# Line Endings
git config --global core.autocrlf input  # Mac/Linux
git config --global core.autocrlf true   # Windows

# Default Branch Name
git config --global init.defaultBranch main

# Aliases
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.lg "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

## Troubleshooting

### Merge Conflicts lösen

```bash
# 1. Conflict erkennen
git merge feature/my-feature
# CONFLICT (content): Merge conflict in file.js

# 2. Konflikt-Dateien anzeigen
git status

# 3. Manuell lösen
# Öffne die Datei und löse die Konflikte
# <<<<<<< HEAD
# Current changes
# =======
# Incoming changes
# >>>>>>> feature/my-feature

# 4. Als gelöst markieren
git add file.js

# 5. Merge abschließen
git commit
```

### Commit rückgängig machen

```bash
# Letzten Commit rückgängig (behält Änderungen)
git reset --soft HEAD~1

# Letzten Commit und Änderungen rückgängig
git reset --hard HEAD~1

# Commit revertieren (erstellt neuen Commit)
git revert HEAD
```

### Branch aufräumen

```bash
# Lokale Branches anzeigen
git branch

# Gemergete Branches löschen
git branch --merged | grep -v "\*" | xargs -n 1 git branch -d

# Remote Branches die lokal nicht mehr existieren
git fetch --prune
```

### Versehentlich auf falschem Branch

```bash
# Änderungen in richtigen Branch verschieben
git stash
git checkout correct-branch
git stash pop
```

## Weitere Ressourcen

- [Contributing Guidelines](../CONTRIBUTING.md)
- [Coding Standards](CODING_STANDARDS.md)
- [Testing Guidelines](TESTING.md)

---

**Version**: 1.0
**Zuletzt aktualisiert**: 2025-11-13
