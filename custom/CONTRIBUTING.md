# Beitragen zum Projekt

Vielen Dank für Ihr Interesse, zu diesem Projekt beizutragen! Wir freuen uns über jeden Beitrag.

## 📋 Inhaltsverzeichnis

- [Verhaltenskodex](#verhaltenskodex)
- [Wie kann ich beitragen?](#wie-kann-ich-beitragen)
- [Entwicklungsprozess](#entwicklungsprozess)
- [Coding Standards](#coding-standards)
- [Commit-Richtlinien](#commit-richtlinien)
- [Pull Request Prozess](#pull-request-prozess)

## Verhaltenskodex

Dieses Projekt folgt unserem [Code of Conduct](CODE_OF_CONDUCT.md). Durch Ihre Teilnahme verpflichten Sie sich, diese Richtlinien einzuhalten.

## Wie kann ich beitragen?

### Fehler melden

Bevor Sie einen Fehler melden:
- Überprüfen Sie, ob der Fehler bereits gemeldet wurde
- Stellen Sie sicher, dass Sie die neueste Version verwenden
- Sammeln Sie relevante Informationen (Logs, Screenshots, etc.)

Verwenden Sie unser [Bug Report Template](.github/issue_template/bug_report.md) für Ihre Meldung.

### Features vorschlagen

Feature-Vorschläge sind willkommen! Bitte:
- Beschreiben Sie das Problem, das gelöst werden soll
- Erklären Sie Ihren Lösungsvorschlag
- Diskutieren Sie mögliche Alternativen

Verwenden Sie unser [Feature Request Template](.github/issue_template/feature_request.md).

### Code beitragen

1. **Fork** das Repository
2. **Clone** Ihren Fork lokal
3. **Erstellen** Sie einen Feature-Branch (siehe [Git Workflow](docs/GIT_WORKFLOW.md))
4. **Implementieren** Sie Ihre Änderungen
5. **Testen** Sie Ihre Änderungen (siehe [Testing Guidelines](docs/TESTING.md))
6. **Committen** Sie Ihre Änderungen
7. **Push** zum Fork
8. **Erstellen** Sie einen Pull Request

## Entwicklungsprozess

### Einrichtung der Entwicklungsumgebung

```bash
# Repository klonen
git clone https://github.com/your-username/Guidelines.git
cd Guidelines

# Dependencies installieren (falls vorhanden)
# npm install oder pip install -r requirements.txt

# Branch für Ihre Arbeit erstellen
git checkout -b feature/meine-neue-funktion
```

### Entwicklungsrichtlinien

- Folgen Sie unseren [Coding Standards](docs/CODING_STANDARDS.md)
- Halten Sie sich an den [Style Guide](docs/STYLE_GUIDE.md)
- Schreiben Sie Tests für neuen Code (siehe [Testing](docs/TESTING.md))
- Dokumentieren Sie Ihren Code angemessen
- Halten Sie Commits atomar und fokussiert

## Coding Standards

Detaillierte Informationen finden Sie in:
- [CODING_STANDARDS.md](docs/CODING_STANDARDS.md) - Allgemeine Programmierstandards
- [STYLE_GUIDE.md](docs/STYLE_GUIDE.md) - Formatierung und Stil
- [ARCHITECTURE.md](docs/ARCHITECTURE.md) - Architektonische Richtlinien

### Wichtigste Punkte

- ✅ Schreiben Sie sauberen, lesbaren Code
- ✅ Kommentieren Sie komplexe Logik
- ✅ Verwenden Sie aussagekräftige Variablennamen
- ✅ Folgen Sie dem DRY-Prinzip (Don't Repeat Yourself)
- ✅ Schreiben Sie testbaren Code

## Commit-Richtlinien

Wir folgen dem [Conventional Commits](https://www.conventionalcommits.org/) Standard.

### Format

```
<typ>(<scope>): <beschreibung>

[optionaler body]

[optionale footer]
```

### Typen

- `feat`: Neue Funktion
- `fix`: Fehlerbehebung
- `docs`: Nur Dokumentationsänderungen
- `style`: Formatierung, fehlende Semikolons, etc.
- `refactor`: Code-Umstrukturierung ohne Funktionsänderung
- `test`: Tests hinzufügen oder korrigieren
- `chore`: Wartungsarbeiten, Build-Prozess, etc.

### Beispiele

```bash
feat(auth): Benutzer-Authentifizierung implementiert
fix(api): Null-Pointer-Exception bei User-Abfrage behoben
docs(readme): Installationsanleitung aktualisiert
```

Weitere Details in [GIT_WORKFLOW.md](docs/GIT_WORKFLOW.md).

## Pull Request Prozess

### Vor dem Erstellen eines PR

1. ✅ Stellen Sie sicher, dass alle Tests bestehen
2. ✅ Code ist entsprechend dem Style Guide formatiert
3. ✅ Dokumentation ist aktualisiert
4. ✅ Commit-Messages folgen den Richtlinien
5. ✅ Branch ist auf dem neuesten Stand mit `main`/`master`

### PR erstellen

1. Verwenden Sie unser [Pull Request Template](.github/pull_request_template/pull_request_template.md)
2. Geben Sie eine klare Beschreibung der Änderungen
3. Verlinken Sie relevante Issues
4. Fügen Sie Screenshots hinzu (bei UI-Änderungen)
5. Markieren Sie den PR als "Ready for Review"

### Code Review

- Seien Sie offen für Feedback
- Reagieren Sie zeitnah auf Review-Kommentare
- Diskutieren Sie konstruktiv über Änderungsvorschläge
- Markieren Sie gelöste Kommentare

### Nach dem Merge

- Löschen Sie Ihren Feature-Branch
- Schließen Sie verlinkte Issues (falls nicht automatisch geschlossen)

## Weitere Ressourcen

- [Git Workflow](docs/GIT_WORKFLOW.md) - Detaillierter Git-Prozess
- [Testing Guidelines](docs/TESTING.md) - Test-Strategien
- [Architecture](docs/ARCHITECTURE.md) - Technische Architektur
- [Deployment](docs/DEPLOYMENT.md) - Deployment-Prozess

## Fragen?

Bei Fragen:
- Erstellen Sie ein Issue mit dem Label `question`
- Lesen Sie die [Issue Label Guide](.github/ISSUE_LABEL_GUIDE.md)

---

Vielen Dank für Ihren Beitrag! 🎉
