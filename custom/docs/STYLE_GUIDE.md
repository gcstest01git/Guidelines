# Code-Stil und Formatierung

Dieser Style Guide definiert die Code-Formatierung und stilistischen Konventionen für das Projekt.

## 📋 Inhaltsverzeichnis

- [Allgemeine Prinzipien](#allgemeine-prinzipien)
- [Dateien und Verzeichnisse](#dateien-und-verzeichnisse)
- [Namenskonventionen](#namenskonventionen)
- [Formatierung](#formatierung)
- [Kommentare und Dokumentation](#kommentare-und-dokumentation)
- [Sprachspezifische Richtlinien](#sprachspezifische-richtlinien)
- [Tools und Automation](#tools-und-automation)

## Allgemeine Prinzipien

### Konsistenz
> **Konsistenz ist wichtiger als persönliche Präferenz.**

- Folgen Sie dem bestehenden Stil des Projekts
- Bei Änderungen: Gesamte Datei konsistent anpassen
- Verwenden Sie automatische Formatierungs-Tools

### Lesbarkeit
```javascript
// ❌ Schlecht: Unleserlich
const x=a>b?c+d:e-f;

// ✅ Gut: Klar und lesbar
const result = condition
  ? calculatePositive(a, b)
  : calculateNegative(c, d);
```

### Einfachheit
- Bevorzugen Sie einfache, klare Lösungen
- Vermeiden Sie unnötige Komplexität
- Code sollte selbsterklärend sein

## Dateien und Verzeichnisse

### Datei-Struktur

```
src/
├── components/       # UI Komponenten
├── services/        # Business Logic
├── utils/           # Helper Funktionen
├── models/          # Daten-Modelle
├── config/          # Konfiguration
└── tests/           # Test-Dateien
```

### Dateinamen

#### Allgemeine Regeln
```
✅ Gut:
- user-service.js
- UserService.ts
- user_service.py
- user-profile.component.tsx

❌ Schlecht:
- UserService.JS (falsche Extension-Case)
- user service.js (Leerzeichen)
- userService (fehlende Extension)
```

#### Konventionen nach Typ

**JavaScript/TypeScript**:
- Komponenten: `PascalCase` → `UserProfile.tsx`
- Services: `camelCase` oder `kebab-case` → `userService.ts`, `user-service.ts`
- Utils: `kebab-case` → `date-formatter.ts`
- Tests: `*.test.ts` oder `*.spec.ts`

**Python**:
- Module: `snake_case` → `user_service.py`
- Tests: `test_*.py` oder `*_test.py`

**CSS/SCSS**:
- Stylesheets: `kebab-case` → `user-profile.scss`

### Datei-Größe
- **Maximum**: 300-400 Zeilen pro Datei
- Bei Überschreitung: Refactoring in mehrere Dateien
- Eine logische Einheit pro Datei

### Imports/Exports

#### Import-Reihenfolge
```javascript
// 1. Standard Library Imports
import fs from 'fs';
import path from 'path';

// 2. External Dependencies
import React from 'react';
import axios from 'axios';

// 3. Internal Imports (absolute)
import { UserService } from '@/services/user-service';
import { formatDate } from '@/utils/date-formatter';

// 4. Relative Imports
import { Component } from './Component';
import styles from './styles.module.css';

// 5. Type Imports (TypeScript)
import type { User } from '@/types';
```

#### Export-Stil
```javascript
// ✅ Bevorzugt: Named Exports
export const UserService = { ... };
export function formatDate() { ... }

// ⚠️  Vorsichtig verwenden: Default Exports
export default UserService;
```

## Namenskonventionen

### Variablen

```javascript
// ✅ Beschreibend und spezifisch
const userEmailAddress = 'user@example.com';
const totalOrderCount = 42;
const isUserAuthenticated = true;

// ❌ Zu kurz oder unklar
const e = 'user@example.com';
const cnt = 42;
const flag = true;
```

#### Konventionen nach Typ

**Boolean**:
```javascript
// Präfix: is, has, can, should
const isVisible = true;
const hasPermission = false;
const canEdit = true;
const shouldValidate = false;
```

**Zahlen/Zähler**:
```javascript
// Suffix: Count, Index, Total, Max, Min
const userCount = 10;
const currentIndex = 5;
const totalAmount = 1000;
const maxRetries = 3;
```

**Arrays/Listen**:
```javascript
// Plural-Form
const users = [];
const emailAddresses = [];
const orderItems = [];
```

**Funktionen**:
```javascript
// Verb + Noun
function getUserById(id) { }
function calculateTotalPrice(items) { }
function validateEmail(email) { }
function transformData(data) { }
```

### Klassen und Interfaces

```typescript
// Klassen: PascalCase
class UserService { }
class OrderProcessor { }

// Interfaces: PascalCase (kein I-Präfix in TypeScript)
interface User { }
interface OrderItem { }

// Type Aliases
type UserId = string;
type OrderStatus = 'pending' | 'completed' | 'cancelled';
```

### Konstanten

```javascript
// SCREAMING_SNAKE_CASE für echte Konstanten
const MAX_RETRY_ATTEMPTS = 3;
const API_BASE_URL = 'https://api.example.com';
const DEFAULT_TIMEOUT = 5000;

// camelCase für konfigurierbare "Konstanten"
const config = {
  apiUrl: process.env.API_URL,
  timeout: 5000
};
```

### Enums

```typescript
// PascalCase für Enum-Namen, SCREAMING_SNAKE_CASE für Werte
enum OrderStatus {
  PENDING = 'PENDING',
  PROCESSING = 'PROCESSING',
  COMPLETED = 'COMPLETED',
  CANCELLED = 'CANCELLED'
}

// Oder mit numerischen Werten
enum Priority {
  LOW = 0,
  MEDIUM = 1,
  HIGH = 2
}
```

## Formatierung

### Einrückung

```javascript
// Verwenden Sie 2 Spaces (oder 4, aber konsistent!)
function example() {
  if (condition) {
    doSomething();
  }
}
```

### Zeilenlänge

- **Maximum**: 80-120 Zeichen
- Bei Überschreitung: Umbrechen

```javascript
// ❌ Zu lang
const result = someVeryLongFunctionName(parameter1, parameter2, parameter3, parameter4, parameter5);

// ✅ Umgebrochen
const result = someVeryLongFunctionName(
  parameter1,
  parameter2,
  parameter3,
  parameter4,
  parameter5
);
```

### Leerzeilen

```javascript
// Verwenden Sie Leerzeilen für logische Gruppierung
function processOrder(order) {
  // Validierung
  if (!order) {
    throw new Error('Order is required');
  }

  // Berechnung
  const total = calculateTotal(order.items);
  const tax = calculateTax(total);

  // Rückgabe
  return {
    total,
    tax,
    grandTotal: total + tax
  };
}
```

### Klammern und Spacing

```javascript
// ✅ Spaces um Operatoren
const sum = a + b;
const isValid = x > 0 && y < 10;

// ✅ Space nach Keywords
if (condition) { }
for (let i = 0; i < 10; i++) { }
while (running) { }

// ✅ Keine Spaces innerhalb von Klammern
const arr = [1, 2, 3];
const obj = { key: 'value' };
functionCall(arg1, arg2);

// ❌ Inkonsistentes Spacing
const sum=a+b;
if(condition){ }
const arr = [ 1, 2, 3 ];
```

### Funktions-Deklarationen

```javascript
// ✅ Konsistente Formatierung
function shortFunction(arg) {
  return arg * 2;
}

function longerFunction(
  firstArgument,
  secondArgument,
  thirdArgument
) {
  // Implementation
}

// Arrow Functions
const multiply = (a, b) => a * b;

const processData = (data) => {
  // Mehrzeilige Implementation
  const processed = transform(data);
  return validate(processed);
};
```

### Objekt-Literale

```javascript
// ✅ Einzeilig für kurze Objekte
const user = { id: 1, name: 'John' };

// ✅ Mehrzeilig für längere Objekte
const user = {
  id: 1,
  name: 'John Doe',
  email: 'john@example.com',
  role: 'admin',
  createdAt: new Date()
};

// ✅ Trailing Comma bei mehrzeiligen Definitionen
const config = {
  api: 'https://api.example.com',
  timeout: 5000,
  retries: 3,  // Trailing comma!
};
```

### Array-Literale

```javascript
// ✅ Einzeilig für kurze Arrays
const numbers = [1, 2, 3, 4, 5];

// ✅ Mehrzeilig für lange Arrays
const longArray = [
  'first-very-long-item',
  'second-very-long-item',
  'third-very-long-item',
];

// ✅ Ein Item pro Zeile für Objekt-Arrays
const users = [
  { id: 1, name: 'John' },
  { id: 2, name: 'Jane' },
  { id: 3, name: 'Bob' },
];
```

### Ternäre Operatoren

```javascript
// ✅ Einzeilig für einfache Fälle
const value = condition ? trueValue : falseValue;

// ✅ Mehrzeilig für komplexe Fälle
const value = someComplexCondition
  ? calculateComplexTrueValue()
  : calculateComplexFalseValue();

// ❌ Verschachtelte Ternäre vermeiden
const value = cond1 ? val1 : cond2 ? val2 : val3;

// ✅ Stattdessen if-else verwenden
let value;
if (cond1) {
  value = val1;
} else if (cond2) {
  value = val2;
} else {
  value = val3;
}
```

## Kommentare und Dokumentation

### Wann kommentieren?

```javascript
// ✅ Kommentar erklärt WARUM, nicht WAS
// Verwenden wir setTimeout, da die API Rate-Limiting hat
await delay(1000);

// ❌ Kommentar wiederholt nur den Code
// Schleife über users
for (const user of users) { }

// ✅ Code ist selbsterklärend - kein Kommentar nötig
const activeUsers = users.filter(user => user.isActive);
```

### Kommentar-Typen

#### Single-Line Comments
```javascript
// Das ist ein einzelner Kommentar
const value = 42;
```

#### Multi-Line Comments
```javascript
/*
 * Dies ist ein mehrzeiliger Kommentar.
 * Er wird für längere Erklärungen verwendet.
 */
```

#### JSDoc/TSDoc Comments
```javascript
/**
 * Berechnet die Summe zweier Zahlen.
 *
 * @param {number} a - Die erste Zahl
 * @param {number} b - Die zweite Zahl
 * @returns {number} Die Summe von a und b
 * @throws {TypeError} Wenn a oder b keine Zahlen sind
 *
 * @example
 * const result = add(2, 3);
 * console.log(result); // 5
 */
function add(a, b) {
  if (typeof a !== 'number' || typeof b !== 'number') {
    throw new TypeError('Both arguments must be numbers');
  }
  return a + b;
}
```

#### TODO Comments
```javascript
// TODO: Implementiere Caching für bessere Performance
// FIXME: Bug bei null-Werten beheben
// HACK: Temporäre Lösung, bis API v2 verfügbar ist
// NOTE: Diese Funktion ist deprecated, verwenden Sie calculateV2()
```

### Dokumentations-Standard

```typescript
/**
 * Service für Benutzer-Verwaltung.
 *
 * Dieser Service verwaltet alle Operationen rund um Benutzer,
 * einschließlich CRUD-Operationen und Authentifizierung.
 *
 * @class UserService
 * @since 1.0.0
 */
class UserService {
  /**
   * Erstellt einen neuen Benutzer.
   *
   * @param {UserInput} userData - Die Benutzerdaten
   * @returns {Promise<User>} Der erstellte Benutzer
   * @throws {ValidationError} Bei ungültigen Eingabedaten
   * @throws {DuplicateError} Wenn Email bereits existiert
   */
  async createUser(userData: UserInput): Promise<User> {
    // Implementation
  }
}
```

## Sprachspezifische Richtlinien

### JavaScript/TypeScript

```typescript
// ✅ Const/Let statt Var
const constant = 'value';
let variable = 0;

// ✅ Template Literals statt String Concatenation
const message = `Hello, ${name}!`;

// ✅ Destructuring
const { firstName, lastName } = user;
const [first, second] = array;

// ✅ Spread Operator
const newArray = [...oldArray, newItem];
const newObject = { ...oldObject, newKey: 'value' };

// ✅ Optional Chaining
const value = user?.profile?.email;

// ✅ Nullish Coalescing
const name = user.name ?? 'Unknown';

// ✅ Async/Await statt Callbacks
async function fetchData() {
  const response = await fetch(url);
  return response.json();
}
```

### Python

```python
# ✅ PEP 8 Style Guide befolgen

# Imports
import os
import sys
from typing import List, Optional

# Konstanten
MAX_CONNECTIONS = 100
DEFAULT_TIMEOUT = 30

# Klassen
class UserService:
    """Service für Benutzer-Verwaltung."""

    def __init__(self, config: dict) -> None:
        """Initialisiert den UserService."""
        self.config = config

    def get_user(self, user_id: int) -> Optional[dict]:
        """
        Holt einen Benutzer anhand der ID.

        Args:
            user_id: Die ID des Benutzers

        Returns:
            Benutzer-Daten oder None wenn nicht gefunden
        """
        pass

# Funktionen
def calculate_total(items: List[dict]) -> float:
    """Berechnet die Gesamtsumme."""
    return sum(item['price'] for item in items)

# List Comprehensions
squared = [x**2 for x in range(10)]

# Context Managers
with open('file.txt', 'r') as f:
    content = f.read()
```

### CSS/SCSS

```css
/* ✅ BEM Naming Convention */
.user-card { }
.user-card__header { }
.user-card__title { }
.user-card--highlighted { }

/* ✅ Gruppierung verwandter Properties */
.element {
  /* Positioning */
  position: absolute;
  top: 0;
  left: 0;

  /* Display & Box Model */
  display: flex;
  width: 100%;
  padding: 1rem;
  margin: 0 auto;

  /* Typography */
  font-size: 1rem;
  line-height: 1.5;
  color: #333;

  /* Visual */
  background-color: #fff;
  border: 1px solid #ddd;
  border-radius: 4px;

  /* Misc */
  cursor: pointer;
}

/* ✅ Mobile First Media Queries */
.element {
  width: 100%;
}

@media (min-width: 768px) {
  .element {
    width: 50%;
  }
}
```

## Tools und Automation

### Linters

```json
// .eslintrc.json
{
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended"
  ],
  "rules": {
    "indent": ["error", 2],
    "quotes": ["error", "single"],
    "semi": ["error", "always"]
  }
}
```

### Formatters

```json
// .prettierrc
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 80,
  "tabWidth": 2
}
```

### EditorConfig

```ini
# .editorconfig
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
indent_style = space
indent_size = 2

[*.md]
trim_trailing_whitespace = false

[*.py]
indent_size = 4
```

### Pre-Commit Hooks

```bash
# Mit Husky und lint-staged
npm install --save-dev husky lint-staged

# package.json
{
  "lint-staged": {
    "*.{js,ts,tsx}": ["eslint --fix", "prettier --write"],
    "*.{css,scss}": ["stylelint --fix", "prettier --write"]
  }
}
```

## Checkliste für Code Reviews

- [ ] Code folgt dem definierten Style Guide
- [ ] Naming Conventions werden eingehalten
- [ ] Kommentare sind sinnvoll und aktuell
- [ ] Keine Linter-Warnings
- [ ] Formatierung ist konsistent
- [ ] Imports sind korrekt sortiert
- [ ] Keine toten Code-Segmente
- [ ] Magic Numbers sind als Konstanten definiert

## Weitere Ressourcen

- [Coding Standards](CODING_STANDARDS.md)
- [Architecture](ARCHITECTURE.md)
- [Testing Guidelines](TESTING.md)

---

**Version**: 1.0
**Zuletzt aktualisiert**: 2025-11-13
