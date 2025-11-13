# Programmierstandards und Best Practices

Diese Dokumentation definiert Programmierstandards und Best Practices für qualitativ hochwertigen, wartbaren Code.

## 📋 Inhaltsverzeichnis

- [Grundprinzipien](#grundprinzipien)
- [Code-Qualität](#code-qualität)
- [Fehlerbehandlung](#fehlerbehandlung)
- [Performance](#performance)
- [Sicherheit](#sicherheit)
- [Testing](#testing)
- [Dokumentation](#dokumentation)
- [Refactoring](#refactoring)

## Grundprinzipien

### Clean Code Principles

#### 1. Aussagekräftige Namen

```javascript
// ❌ Schlecht: Unklare Namen
function calc(x, y) {
  return x * y * 0.19;
}

// ✅ Gut: Beschreibende Namen
function calculateValueAddedTax(netPrice, quantity) {
  const TAX_RATE = 0.19;
  const totalNet = netPrice * quantity;
  return totalNet * TAX_RATE;
}
```

#### 2. Funktionen sollten eine Sache tun

```javascript
// ❌ Schlecht: Mehrere Verantwortlichkeiten
function processUserData(userData) {
  // Validierung
  if (!userData.email) throw new Error('Email required');

  // Speichern
  database.save(userData);

  // Email senden
  sendEmail(userData.email, 'Welcome!');

  // Logging
  logger.info('User created');
}

// ✅ Gut: Single Responsibility
function validateUser(userData) {
  if (!userData.email) {
    throw new ValidationError('Email required');
  }
}

function saveUser(userData) {
  return database.save(userData);
}

function sendWelcomeEmail(email) {
  return emailService.send(email, 'Welcome!');
}

async function createUser(userData) {
  validateUser(userData);
  const user = await saveUser(userData);
  await sendWelcomeEmail(user.email);
  logger.info('User created', { userId: user.id });
  return user;
}
```

#### 3. DRY (Don't Repeat Yourself)

```javascript
// ❌ Schlecht: Code-Duplikation
function calculateOrderTotalForGermany(items) {
  const subtotal = items.reduce((sum, item) => sum + item.price, 0);
  const tax = subtotal * 0.19;
  return subtotal + tax;
}

function calculateOrderTotalForAustria(items) {
  const subtotal = items.reduce((sum, item) => sum + item.price, 0);
  const tax = subtotal * 0.20;
  return subtotal + tax;
}

// ✅ Gut: Wiederverwendbare Funktion
function calculateOrderTotal(items, taxRate) {
  const subtotal = items.reduce((sum, item) => sum + item.price, 0);
  const tax = subtotal * taxRate;
  return subtotal + tax;
}

const TAX_RATES = {
  DE: 0.19,
  AT: 0.20,
};

const totalDE = calculateOrderTotal(items, TAX_RATES.DE);
const totalAT = calculateOrderTotal(items, TAX_RATES.AT);
```

#### 4. KISS (Keep It Simple, Stupid)

```javascript
// ❌ Schlecht: Unnötig komplex
function isEven(number) {
  return number % 2 === 0 ? true : false;
}

// ✅ Gut: Einfach
function isEven(number) {
  return number % 2 === 0;
}
```

#### 5. YAGNI (You Aren't Gonna Need It)

```javascript
// ❌ Schlecht: Überengineering
class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
    // Features die noch niemand braucht:
    this.preferences = {};
    this.history = [];
    this.analytics = {};
  }

  // Viele Methoden die nie genutzt werden...
}

// ✅ Gut: Nur was aktuell gebraucht wird
class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }
}
```

### SOLID Principles

#### Single Responsibility Principle (SRP)

```typescript
// ❌ Schlecht: Mehrere Verantwortlichkeiten
class User {
  save() { /* Datenbanklogik */ }
  sendEmail() { /* Email-Logik */ }
  generateReport() { /* Report-Logik */ }
}

// ✅ Gut: Getrennte Verantwortlichkeiten
class User {
  constructor(public name: string, public email: string) {}
}

class UserRepository {
  save(user: User) { /* Datenbanklogik */ }
}

class EmailService {
  send(to: string, subject: string) { /* Email-Logik */ }
}

class ReportGenerator {
  generate(user: User) { /* Report-Logik */ }
}
```

#### Open/Closed Principle (OCP)

```typescript
// ❌ Schlecht: Modifikation für neue Typen
class AreaCalculator {
  calculate(shape: any) {
    if (shape.type === 'circle') {
      return Math.PI * shape.radius ** 2;
    } else if (shape.type === 'rectangle') {
      return shape.width * shape.height;
    }
    // Für jeden neuen Typ muss diese Funktion geändert werden
  }
}

// ✅ Gut: Erweiterbar ohne Modifikation
interface Shape {
  calculateArea(): number;
}

class Circle implements Shape {
  constructor(private radius: number) {}
  calculateArea() {
    return Math.PI * this.radius ** 2;
  }
}

class Rectangle implements Shape {
  constructor(private width: number, private height: number) {}
  calculateArea() {
    return this.width * this.height;
  }
}

class AreaCalculator {
  calculate(shape: Shape) {
    return shape.calculateArea();
  }
}
```

#### Liskov Substitution Principle (LSP)

```typescript
// ❌ Schlecht: Verletzt LSP
class Bird {
  fly() { /* ... */ }
}

class Penguin extends Bird {
  fly() {
    throw new Error('Penguins cannot fly!');
  }
}

// ✅ Gut: Korrekte Hierarchie
interface Bird {
  move(): void;
}

class FlyingBird implements Bird {
  move() { this.fly(); }
  fly() { /* ... */ }
}

class Penguin implements Bird {
  move() { this.swim(); }
  swim() { /* ... */ }
}
```

#### Interface Segregation Principle (ISP)

```typescript
// ❌ Schlecht: Zu großes Interface
interface Worker {
  work(): void;
  eat(): void;
  sleep(): void;
}

class Robot implements Worker {
  work() { /* ... */ }
  eat() { throw new Error('Robots do not eat'); }
  sleep() { throw new Error('Robots do not sleep'); }
}

// ✅ Gut: Kleine, spezifische Interfaces
interface Workable {
  work(): void;
}

interface Eatable {
  eat(): void;
}

interface Sleepable {
  sleep(): void;
}

class Human implements Workable, Eatable, Sleepable {
  work() { /* ... */ }
  eat() { /* ... */ }
  sleep() { /* ... */ }
}

class Robot implements Workable {
  work() { /* ... */ }
}
```

#### Dependency Inversion Principle (DIP)

```typescript
// ❌ Schlecht: Abhängigkeit von Implementierung
class MySQLDatabase {
  save(data: any) { /* MySQL specific */ }
}

class UserService {
  private db = new MySQLDatabase();

  saveUser(user: User) {
    this.db.save(user);
  }
}

// ✅ Gut: Abhängigkeit von Abstraktion
interface Database {
  save(data: any): void;
}

class MySQLDatabase implements Database {
  save(data: any) { /* MySQL specific */ }
}

class PostgreSQLDatabase implements Database {
  save(data: any) { /* PostgreSQL specific */ }
}

class UserService {
  constructor(private db: Database) {}

  saveUser(user: User) {
    this.db.save(user);
  }
}

// Dependency Injection
const db = new MySQLDatabase();
const userService = new UserService(db);
```

## Code-Qualität

### Code-Komplexität

#### Cyclomatic Complexity reduzieren

```javascript
// ❌ Schlecht: Hohe Komplexität (CC = 7)
function processOrder(order) {
  if (order.status === 'pending') {
    if (order.payment === 'paid') {
      if (order.items.length > 0) {
        if (order.shippingAddress) {
          // Process order
        } else {
          throw new Error('No address');
        }
      } else {
        throw new Error('No items');
      }
    } else {
      throw new Error('Not paid');
    }
  } else {
    throw new Error('Invalid status');
  }
}

// ✅ Gut: Niedrige Komplexität (CC = 4)
function processOrder(order) {
  validateOrderStatus(order);
  validatePayment(order);
  validateItems(order);
  validateShippingAddress(order);

  return fulfillOrder(order);
}

function validateOrderStatus(order) {
  if (order.status !== 'pending') {
    throw new Error('Invalid status');
  }
}

function validatePayment(order) {
  if (order.payment !== 'paid') {
    throw new Error('Not paid');
  }
}
```

#### Vermeidung tiefer Verschachtelung

```javascript
// ❌ Schlecht: Tiefe Verschachtelung
function process(data) {
  if (data) {
    if (data.user) {
      if (data.user.isActive) {
        if (data.user.hasPermission) {
          // Do something
        }
      }
    }
  }
}

// ✅ Gut: Early Returns
function process(data) {
  if (!data) return;
  if (!data.user) return;
  if (!data.user.isActive) return;
  if (!data.user.hasPermission) return;

  // Do something
}

// ✅ Noch besser: Optional Chaining
function process(data) {
  if (!data?.user?.isActive || !data?.user?.hasPermission) {
    return;
  }

  // Do something
}
```

### Funktionslänge

```javascript
// ✅ Funktionen sollten kurz sein (< 20 Zeilen ideal)
function createUser(userData) {
  validateUserData(userData);
  const hashedPassword = hashPassword(userData.password);
  const user = buildUserObject(userData, hashedPassword);
  return saveUser(user);
}

// Jede Hilfsfunktion ist klein und fokussiert
function validateUserData(userData) { /* ... */ }
function hashPassword(password) { /* ... */ }
function buildUserObject(data, hash) { /* ... */ }
function saveUser(user) { /* ... */ }
```

### Parameter-Anzahl

```javascript
// ❌ Schlecht: Zu viele Parameter
function createOrder(
  userId,
  productId,
  quantity,
  price,
  discount,
  shippingAddress,
  billingAddress,
  paymentMethod
) { }

// ✅ Gut: Objekt-Parameter
function createOrder(orderData) {
  const {
    userId,
    productId,
    quantity,
    price,
    discount,
    shippingAddress,
    billingAddress,
    paymentMethod
  } = orderData;
}

// ✅ Noch besser: TypeScript Interface
interface OrderData {
  userId: string;
  productId: string;
  quantity: number;
  price: number;
  discount?: number;
  shippingAddress: Address;
  billingAddress: Address;
  paymentMethod: PaymentMethod;
}

function createOrder(orderData: OrderData) { }
```

## Fehlerbehandlung

### Exceptions richtig verwenden

```javascript
// ❌ Schlecht: Generische Fehler
throw new Error('Something went wrong');

// ✅ Gut: Spezifische Error-Klassen
class ValidationError extends Error {
  constructor(message, field) {
    super(message);
    this.name = 'ValidationError';
    this.field = field;
  }
}

class NotFoundError extends Error {
  constructor(resource, id) {
    super(`${resource} with id ${id} not found`);
    this.name = 'NotFoundError';
  }
}

throw new ValidationError('Email is required', 'email');
throw new NotFoundError('User', userId);
```

### Try-Catch Best Practices

```javascript
// ❌ Schlecht: Leerer Catch
try {
  await riskyOperation();
} catch (error) {
  // Fehler wird ignoriert
}

// ❌ Schlecht: Zu breiter Catch
try {
  const user = await fetchUser();
  const orders = await fetchOrders();
  const analytics = await fetchAnalytics();
} catch (error) {
  // Welche Operation ist fehlgeschlagen?
  console.error(error);
}

// ✅ Gut: Spezifische Fehlerbehandlung
try {
  const user = await fetchUser(userId);
} catch (error) {
  if (error instanceof NotFoundError) {
    return res.status(404).json({ error: 'User not found' });
  }
  if (error instanceof ValidationError) {
    return res.status(400).json({ error: error.message });
  }
  // Unerwartete Fehler weiterwerfen
  throw error;
}

// ✅ Gut: Fehler loggen und behandeln
try {
  await criticalOperation();
} catch (error) {
  logger.error('Critical operation failed', {
    error: error.message,
    stack: error.stack,
    context: { userId, orderId }
  });
  // Fallback oder Rethrow
  throw error;
}
```

### Input Validation

```javascript
// ✅ Immer Input validieren
function createUser(userData) {
  // 1. Type Checking
  if (typeof userData !== 'object') {
    throw new TypeError('userData must be an object');
  }

  // 2. Required Fields
  if (!userData.email) {
    throw new ValidationError('Email is required', 'email');
  }

  // 3. Format Validation
  if (!isValidEmail(userData.email)) {
    throw new ValidationError('Invalid email format', 'email');
  }

  // 4. Range Validation
  if (userData.age && (userData.age < 18 || userData.age > 120)) {
    throw new ValidationError('Age must be between 18 and 120', 'age');
  }

  // Proceed with creation
}
```

## Performance

### Algorithmen-Komplexität

```javascript
// ❌ Schlecht: O(n²) Komplexität
function findDuplicates(arr) {
  const duplicates = [];
  for (let i = 0; i < arr.length; i++) {
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[i] === arr[j]) {
        duplicates.push(arr[i]);
      }
    }
  }
  return duplicates;
}

// ✅ Gut: O(n) Komplexität
function findDuplicates(arr) {
  const seen = new Set();
  const duplicates = new Set();

  for (const item of arr) {
    if (seen.has(item)) {
      duplicates.add(item);
    } else {
      seen.add(item);
    }
  }

  return Array.from(duplicates);
}
```

### Caching und Memoization

```javascript
// ✅ Memoization für teure Berechnungen
function memoize(fn) {
  const cache = new Map();

  return function(...args) {
    const key = JSON.stringify(args);
    if (cache.has(key)) {
      return cache.get(key);
    }

    const result = fn.apply(this, args);
    cache.set(key, result);
    return result;
  };
}

const expensiveCalculation = memoize((n) => {
  // Teure Berechnung
  return n * n;
});
```

### Lazy Loading

```javascript
// ✅ Lazy Loading für große Datenmengen
class DataLoader {
  constructor() {
    this._data = null;
  }

  async getData() {
    if (!this._data) {
      this._data = await this.fetchData();
    }
    return this._data;
  }

  async fetchData() {
    // Lade Daten nur wenn benötigt
  }
}
```

## Sicherheit

### Input Sanitization

```javascript
// ✅ Immer Benutzereingaben säubern
function sanitizeInput(input) {
  // Remove HTML tags
  return input
    .replace(/<[^>]*>/g, '')
    .trim();
}

// ✅ SQL Injection Prevention
// Verwende immer parametrisierte Queries
const query = 'SELECT * FROM users WHERE email = ?';
const result = await db.query(query, [email]);

// ❌ NIEMALS:
const query = `SELECT * FROM users WHERE email = '${email}'`;
```

### XSS Prevention

```javascript
// ✅ Output Encoding
function escapeHtml(unsafe) {
  return unsafe
    .replace(/&/g, "&amp;")
    .replace(/</g, "&lt;")
    .replace(/>/g, "&gt;")
    .replace(/"/g, "&quot;")
    .replace(/'/g, "&#039;");
}
```

### Authentifizierung

```javascript
// ✅ Sichere Password-Hashing
const bcrypt = require('bcrypt');

async function hashPassword(password) {
  const saltRounds = 12;
  return await bcrypt.hash(password, saltRounds);
}

async function verifyPassword(password, hash) {
  return await bcrypt.compare(password, hash);
}

// ❌ NIEMALS Passwörter im Klartext speichern!
```

## Testing

Siehe [TESTING.md](TESTING.md) für detaillierte Test-Richtlinien.

### Test-Coverage

- **Minimum**: 80% Code Coverage
- **Ziel**: 90%+ für kritische Module
- Alle Public APIs müssen getestet sein

### Testbare Code-Struktur

```javascript
// ✅ Testbar: Dependencies injected
function createUserService(database, emailService) {
  return {
    async createUser(userData) {
      const user = await database.save(userData);
      await emailService.send(user.email, 'Welcome');
      return user;
    }
  };
}

// ❌ Nicht testbar: Hard-coded Dependencies
function createUser(userData) {
  const user = database.save(userData);  // Global dependency
  emailService.send(user.email, 'Welcome');  // Global dependency
  return user;
}
```

## Dokumentation

### Code-Dokumentation

```typescript
/**
 * Berechnet die Gesamtsumme einer Bestellung.
 *
 * Diese Funktion berücksichtigt:
 * - Produktpreise
 * - Steuern basierend auf dem Land
 * - Rabatte
 *
 * @param items - Array von Order Items
 * @param country - Ländercode (ISO 3166-1 alpha-2)
 * @param discountCode - Optionaler Rabattcode
 * @returns Gesamtsumme in Cent
 * @throws {ValidationError} Wenn items leer ist
 * @throws {InvalidCountryError} Wenn Ländercode ungültig ist
 *
 * @example
 * ```typescript
 * const total = calculateOrderTotal(
 *   [{ price: 1000, quantity: 2 }],
 *   'DE',
 *   'SAVE10'
 * );
 * console.log(total); // 1980 (inkl. 19% MwSt., 10% Rabatt)
 * ```
 */
function calculateOrderTotal(
  items: OrderItem[],
  country: string,
  discountCode?: string
): number {
  // Implementation
}
```

## Refactoring

### Wann refactoren?

- **Code Smells** identifiziert
- **Duplicated Code** vorhanden
- **Komplexität** zu hoch
- **Tests** schwer zu schreiben
- **Performance** Probleme

### Refactoring-Prozess

1. **Tests schreiben** (falls nicht vorhanden)
2. **Refactoring durchführen**
3. **Tests ausführen** (müssen grün bleiben)
4. **Code Review**
5. **Deployment**

### Häufige Refactorings

#### Extract Method

```javascript
// Vorher
function printInvoice(invoice) {
  console.log('Invoice #' + invoice.number);
  console.log('Total: $' + (invoice.amount * 1.19));
}

// Nachher
function printInvoice(invoice) {
  printHeader(invoice);
  printTotal(invoice);
}

function printHeader(invoice) {
  console.log('Invoice #' + invoice.number);
}

function printTotal(invoice) {
  const total = calculateTotal(invoice);
  console.log('Total: $' + total);
}

function calculateTotal(invoice) {
  return invoice.amount * 1.19;
}
```

## Code Review Checkliste

- [ ] Code folgt SOLID Principles
- [ ] Funktionen sind klein und fokussiert
- [ ] Keine Code-Duplikation
- [ ] Fehlerbehandlung implementiert
- [ ] Input Validation vorhanden
- [ ] Security Best Practices befolgt
- [ ] Performance-Aspekte berücksichtigt
- [ ] Tests geschrieben
- [ ] Dokumentation aktualisiert

## Weitere Ressourcen

- [Style Guide](STYLE_GUIDE.md)
- [Architecture](ARCHITECTURE.md)
- [Testing Guidelines](TESTING.md)
- [Git Workflow](GIT_WORKFLOW.md)

---

**Version**: 1.0
**Zuletzt aktualisiert**: 2025-11-13
