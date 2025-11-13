# Test-Richtlinien

Diese Dokumentation beschreibt Test-Strategien, Best Practices und Qualitätssicherungsmaßnahmen.

## 📋 Inhaltsverzeichnis

- [Test-Philosophie](#test-philosophie)
- [Test-Arten](#test-arten)
- [Test-Pyramide](#test-pyramide)
- [Unit Tests](#unit-tests)
- [Integration Tests](#integration-tests)
- [E2E Tests](#e2e-tests)
- [Test Coverage](#test-coverage)
- [Test-Frameworks und Tools](#test-frameworks-und-tools)
- [Best Practices](#best-practices)
- [CI/CD Integration](#cicd-integration)

## Test-Philosophie

### Warum testen?

- ✅ **Qualität**: Code funktioniert wie erwartet
- ✅ **Vertrauen**: Änderungen führen nicht zu Regressionen
- ✅ **Dokumentation**: Tests zeigen, wie Code verwendet wird
- ✅ **Design**: Testbarer Code ist besserer Code
- ✅ **Wartbarkeit**: Einfacher zu refactorn mit Tests

### Test-Driven Development (TDD)

```
1. Red   → Test schreiben (fails)
2. Green → Minimal-Code schreiben (test passes)
3. Refactor → Code verbessern
```

#### TDD-Beispiel

```javascript
// 1. RED: Test schreiben
describe('calculateTotal', () => {
  test('should sum up item prices', () => {
    const items = [
      { price: 10 },
      { price: 20 },
      { price: 30 }
    ];
    expect(calculateTotal(items)).toBe(60);
  });
});

// 2. GREEN: Minimal-Code
function calculateTotal(items) {
  return items.reduce((sum, item) => sum + item.price, 0);
}

// 3. REFACTOR: Verbessern (falls nötig)
function calculateTotal(items) {
  if (!Array.isArray(items)) {
    throw new TypeError('items must be an array');
  }
  return items.reduce((sum, item) => sum + (item.price || 0), 0);
}
```

## Test-Arten

### 1. Unit Tests

**Was:** Testen einzelne Funktionen/Methoden isoliert

**Eigenschaften:**
- Schnell (Millisekunden)
- Isoliert (keine Dependencies)
- Deterministisch (immer gleiches Ergebnis)
- Viele Tests (hunderte bis tausende)

### 2. Integration Tests

**Was:** Testen Zusammenspiel mehrerer Komponenten

**Eigenschaften:**
- Mittelschnell (Sekunden)
- Reale Dependencies (DB, APIs)
- Weniger als Unit Tests

### 3. End-to-End (E2E) Tests

**Was:** Testen komplette User-Workflows

**Eigenschaften:**
- Langsam (Minuten)
- Gesamtes System
- Wenige Tests (kritische Pfade)

### 4. Weitere Test-Arten

- **Performance Tests**: Geschwindigkeit und Skalierbarkeit
- **Security Tests**: Sicherheitslücken
- **Load Tests**: Systemverhalten unter Last
- **Smoke Tests**: Basis-Funktionalität nach Deployment
- **Regression Tests**: Keine alten Bugs wieder auftauchen
- **Accessibility Tests**: Barrierefreiheit

## Test-Pyramide

```
        ╱╲
       ╱  ╲    E2E Tests (wenige, langsam)
      ╱────╲   ~10%
     ╱      ╲
    ╱────────╲ Integration Tests (mittel)
   ╱          ╲ ~20%
  ╱────────────╲
 ╱              ╲ Unit Tests (viele, schnell)
╱────────────────╲ ~70%
```

### Verhältnis

- **70%** Unit Tests
- **20%** Integration Tests
- **10%** E2E Tests

## Unit Tests

### Struktur

#### AAA-Pattern

```javascript
test('should calculate tax correctly', () => {
  // Arrange (Setup)
  const price = 100;
  const taxRate = 0.19;

  // Act (Execute)
  const result = calculateTax(price, taxRate);

  // Assert (Verify)
  expect(result).toBe(19);
});
```

#### Given-When-Then

```javascript
test('user login', () => {
  // Given: Ein registrierter User
  const user = { email: 'test@example.com', password: 'secret' };

  // When: User versucht sich einzuloggen
  const result = login(user.email, user.password);

  // Then: Login ist erfolgreich
  expect(result.success).toBe(true);
  expect(result.token).toBeDefined();
});
```

### Test-Naming

```javascript
// ✅ Beschreibend
test('should return empty array when no items found', () => {});
test('should throw ValidationError when email is invalid', () => {});
test('should calculate discount for premium users', () => {});

// ❌ Unklar
test('returns array', () => {});
test('throws error', () => {});
test('calculates discount', () => {});
```

### Mocking und Stubbing

#### Mocking Dependencies

```javascript
// Module mocken
jest.mock('./database');

test('should save user to database', async () => {
  // Mock setup
  const mockSave = jest.fn().mockResolvedValue({ id: 1 });
  database.save = mockSave;

  // Test
  const user = { name: 'John', email: 'john@example.com' };
  await userService.create(user);

  // Verify
  expect(mockSave).toHaveBeenCalledWith(user);
  expect(mockSave).toHaveBeenCalledTimes(1);
});
```

#### Spies

```javascript
test('should call logger when error occurs', () => {
  const loggerSpy = jest.spyOn(logger, 'error');

  try {
    riskyOperation();
  } catch (error) {
    // Verify logger was called
    expect(loggerSpy).toHaveBeenCalledWith(
      expect.stringContaining('Error')
    );
  }

  loggerSpy.mockRestore();
});
```

#### Stubs

```javascript
test('should use cached data when available', () => {
  // Stub cache.get
  const cacheStub = jest.spyOn(cache, 'get')
    .mockReturnValue({ data: 'cached' });

  const result = dataService.getData('key');

  expect(result).toEqual({ data: 'cached' });
  expect(cacheStub).toHaveBeenCalled();
});
```

### Testfälle

#### Happy Path

```javascript
test('should create user with valid data', async () => {
  const userData = {
    name: 'John Doe',
    email: 'john@example.com'
  };

  const user = await userService.create(userData);

  expect(user).toBeDefined();
  expect(user.id).toBeDefined();
  expect(user.name).toBe(userData.name);
});
```

#### Edge Cases

```javascript
test('should handle empty array', () => {
  expect(calculateTotal([])).toBe(0);
});

test('should handle null values', () => {
  expect(formatName(null)).toBe('');
});

test('should handle very large numbers', () => {
  const result = calculate(Number.MAX_SAFE_INTEGER);
  expect(result).toBeLessThan(Number.MAX_VALUE);
});
```

#### Error Cases

```javascript
test('should throw error when email is missing', () => {
  const userData = { name: 'John' }; // email fehlt

  expect(() => {
    userService.create(userData);
  }).toThrow(ValidationError);
});

test('should throw specific error message', () => {
  expect(() => {
    validateEmail('invalid');
  }).toThrow('Invalid email format');
});
```

### Asynchroner Code

```javascript
// Async/Await
test('should fetch user data', async () => {
  const user = await userService.getUser(1);
  expect(user.id).toBe(1);
});

// Promises
test('should fetch user data', () => {
  return userService.getUser(1).then(user => {
    expect(user.id).toBe(1);
  });
});

// Callbacks
test('should call callback with data', (done) => {
  fetchData((data) => {
    expect(data).toBeDefined();
    done();
  });
});
```

## Integration Tests

### Database Integration

```javascript
describe('User Repository Integration', () => {
  let db;

  beforeAll(async () => {
    // Setup Test-Datenbank
    db = await setupTestDatabase();
  });

  afterAll(async () => {
    // Cleanup
    await db.close();
  });

  beforeEach(async () => {
    // Reset DB vor jedem Test
    await db.clear();
  });

  test('should save and retrieve user', async () => {
    // Arrange
    const userData = { name: 'John', email: 'john@example.com' };

    // Act
    const savedUser = await userRepository.save(userData);
    const retrievedUser = await userRepository.findById(savedUser.id);

    // Assert
    expect(retrievedUser).toMatchObject(userData);
  });
});
```

### API Integration

```javascript
describe('API Integration Tests', () => {
  let server;

  beforeAll(async () => {
    server = await startTestServer();
  });

  afterAll(async () => {
    await server.close();
  });

  test('POST /api/users should create user', async () => {
    const userData = {
      name: 'John Doe',
      email: 'john@example.com'
    };

    const response = await request(server)
      .post('/api/users')
      .send(userData)
      .expect(201);

    expect(response.body).toMatchObject(userData);
    expect(response.body.id).toBeDefined();
  });

  test('GET /api/users/:id should return user', async () => {
    // Setup: Create user first
    const user = await createTestUser();

    // Test: Retrieve user
    const response = await request(server)
      .get(`/api/users/${user.id}`)
      .expect(200);

    expect(response.body.id).toBe(user.id);
  });
});
```

### External Services

```javascript
describe('Payment Service Integration', () => {
  test('should process payment via Stripe', async () => {
    // Verwende Test-API-Key
    const stripe = new Stripe(process.env.STRIPE_TEST_KEY);

    const payment = await paymentService.processPayment({
      amount: 1000,
      currency: 'eur',
      source: 'tok_visa' // Test token
    });

    expect(payment.status).toBe('succeeded');
  });
});
```

## E2E Tests

### Browser-basierte Tests

```javascript
describe('User Registration E2E', () => {
  let page;

  beforeAll(async () => {
    // Browser starten
    page = await browser.newPage();
  });

  afterAll(async () => {
    await page.close();
  });

  test('should register new user', async () => {
    // Navigate
    await page.goto('http://localhost:3000/register');

    // Fill form
    await page.type('#name', 'John Doe');
    await page.type('#email', 'john@example.com');
    await page.type('#password', 'SecurePass123!');

    // Submit
    await page.click('button[type="submit"]');

    // Wait for redirect
    await page.waitForNavigation();

    // Verify
    expect(page.url()).toBe('http://localhost:3000/dashboard');

    const welcomeMessage = await page.$eval(
      '.welcome',
      el => el.textContent
    );
    expect(welcomeMessage).toContain('Welcome, John Doe');
  });

  test('should show error for invalid email', async () => {
    await page.goto('http://localhost:3000/register');

    await page.type('#email', 'invalid-email');
    await page.click('button[type="submit"]');

    const errorMessage = await page.$eval(
      '.error',
      el => el.textContent
    );
    expect(errorMessage).toContain('Invalid email');
  });
});
```

### Kritische User-Flows

```javascript
describe('E-Commerce Checkout Flow', () => {
  test('complete purchase flow', async () => {
    // 1. Browse products
    await page.goto('/products');
    await page.click('.product-card:first-child');

    // 2. Add to cart
    await page.click('#add-to-cart');
    await page.waitForSelector('.cart-badge');

    // 3. Go to cart
    await page.click('.cart-icon');
    expect(await page.$('.cart-item')).toBeTruthy();

    // 4. Checkout
    await page.click('#checkout-button');

    // 5. Fill shipping info
    await page.type('#address', '123 Main St');
    await page.type('#city', 'Berlin');
    await page.type('#zip', '10115');

    // 6. Payment
    await page.type('#card-number', '4242424242424242');
    await page.type('#card-expiry', '12/25');
    await page.type('#card-cvc', '123');

    // 7. Place order
    await page.click('#place-order');

    // 8. Verify success
    await page.waitForSelector('.order-confirmation');
    const confirmation = await page.$eval(
      '.order-number',
      el => el.textContent
    );
    expect(confirmation).toMatch(/^ORDER-\d+$/);
  });
});
```

## Test Coverage

### Coverage-Ziele

- **Minimum**: 80% Code Coverage
- **Empfohlen**: 90%+ für kritische Module
- **Ziel**: 100% für Core Business Logic

### Coverage-Metriken

#### Line Coverage
```
Percentage of executed lines
Target: 90%
```

#### Branch Coverage
```
Percentage of executed branches (if/else)
Target: 85%
```

#### Function Coverage
```
Percentage of called functions
Target: 95%
```

#### Statement Coverage
```
Percentage of executed statements
Target: 90%
```

### Coverage Reports

```bash
# Generate coverage report
npm test -- --coverage

# Coverage report
--------------------|---------|----------|---------|---------|
File                | % Stmts | % Branch | % Funcs | % Lines |
--------------------|---------|----------|---------|---------|
All files           |   92.5  |   88.2   |   94.1  |   92.8  |
 src/               |   95.2  |   90.5   |   96.3  |   95.5  |
  user-service.js   |   100   |   100    |   100   |   100   |
  order-service.js  |   90.5  |   82.3   |   88.9  |   91.2  |
--------------------|---------|----------|---------|---------|
```

### Uncovered Code analysieren

```javascript
// Istanbul/NYC comments
/* istanbul ignore next */
if (process.env.NODE_ENV === 'development') {
  // Debug code - nicht production-relevant
  console.log('Debug info');
}

/* istanbul ignore else */
if (criticalCondition) {
  handleCritical();
} // else-Zweig nicht erreichbar in Tests
```

## Test-Frameworks und Tools

### JavaScript/TypeScript

#### Testing Frameworks
```bash
# Jest (React, Node.js)
npm install --save-dev jest @types/jest

# Vitest (Vite-Projekte)
npm install --save-dev vitest

# Mocha + Chai
npm install --save-dev mocha chai
```

#### Assertion Libraries
```javascript
// Jest (eingebaut)
expect(value).toBe(expected);
expect(array).toContain(item);
expect(obj).toMatchObject(partial);

// Chai
expect(value).to.equal(expected);
expect(array).to.include(item);
expect(obj).to.deep.equal(expected);
```

#### Test Runners
- Jest
- Vitest
- Mocha
- Ava
- Jasmine

### E2E Testing

```bash
# Playwright
npm install --save-dev @playwright/test

# Cypress
npm install --save-dev cypress

# Puppeteer
npm install --save-dev puppeteer
```

### Mocking Tools

```bash
# MSW (Mock Service Worker)
npm install --save-dev msw

# Sinon
npm install --save-dev sinon

# Jest (eingebaut)
```

### Coverage Tools

```bash
# Istanbul/NYC
npm install --save-dev nyc

# Jest (eingebaut)
npm test -- --coverage
```

## Best Practices

### 1. Test-Isolation

```javascript
// ✅ Gut: Tests sind unabhängig
describe('UserService', () => {
  let userService;

  beforeEach(() => {
    // Fresh instance für jeden Test
    userService = new UserService();
  });

  test('test 1', () => { /* ... */ });
  test('test 2', () => { /* ... */ });
});

// ❌ Schlecht: Shared State
let sharedUser = null;

test('create user', () => {
  sharedUser = createUser(); // Test 2 hängt von Test 1 ab
});

test('update user', () => {
  updateUser(sharedUser); // Fails wenn Test 1 nicht läuft
});
```

### 2. Deterministische Tests

```javascript
// ❌ Schlecht: Non-deterministic
test('should create user', () => {
  const user = createUser({
    createdAt: new Date() // Ändert sich bei jedem Lauf
  });
  expect(user.createdAt).toBe('2025-11-13'); // Fails morgen
});

// ✅ Gut: Deterministic
test('should create user', () => {
  const fixedDate = new Date('2025-11-13');
  jest.useFakeTimers().setSystemTime(fixedDate);

  const user = createUser();
  expect(user.createdAt).toEqual(fixedDate);

  jest.useRealTimers();
});
```

### 3. Aussagekräftige Fehlermeldungen

```javascript
// ❌ Schlecht
expect(result).toBe(true);

// ✅ Gut
expect(result).toBe(true);
// oder besser mit Custom Message
expect(result).toBe(true, 'User should be authenticated after login');
```

### 4. Test-Daten

```javascript
// ✅ Factory Pattern für Test-Daten
function createTestUser(overrides = {}) {
  return {
    id: 1,
    name: 'Test User',
    email: 'test@example.com',
    role: 'user',
    ...overrides
  };
}

test('admin users can delete', () => {
  const admin = createTestUser({ role: 'admin' });
  expect(canDelete(admin)).toBe(true);
});

test('regular users cannot delete', () => {
  const user = createTestUser({ role: 'user' });
  expect(canDelete(user)).toBe(false);
});
```

### 5. Test-Organisation

```javascript
describe('UserService', () => {
  describe('create', () => {
    test('should create user with valid data', () => {});
    test('should throw when email exists', () => {});
    test('should hash password', () => {});
  });

  describe('update', () => {
    test('should update user fields', () => {});
    test('should throw when user not found', () => {});
  });

  describe('delete', () => {
    test('should delete user', () => {});
    test('should throw when user not found', () => {});
  });
});
```

### 6. Nicht testen

```javascript
// ❌ Nicht testen: Third-Party Code
test('express routing works', () => {
  // Express selbst muss nicht getestet werden
});

// ❌ Nicht testen: Triviale Getter/Setter
test('getName returns name', () => {
  user.name = 'John';
  expect(user.getName()).toBe('John'); // Zu trivial
});

// ✅ Testen: Eigene Business Logic
test('calculateDiscount applies correct rate for premium users', () => {
  const premium = { role: 'premium' };
  expect(calculateDiscount(100, premium)).toBe(90);
});
```

## CI/CD Integration

### GitHub Actions

```yaml
name: Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install dependencies
        run: npm ci

      - name: Run linter
        run: npm run lint

      - name: Run tests
        run: npm test -- --coverage

      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/lcov.info
```

### Pre-commit Hooks

```javascript
// package.json
{
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged"
    }
  },
  "lint-staged": {
    "*.{js,ts,tsx}": [
      "eslint --fix",
      "jest --bail --findRelatedTests"
    ]
  }
}
```

## Weitere Ressourcen

- [Coding Standards](CODING_STANDARDS.md)
- [Architecture](ARCHITECTURE.md)
- [Git Workflow](GIT_WORKFLOW.md)
- [Deployment](DEPLOYMENT.md)

---

**Version**: 1.0
**Zuletzt aktualisiert**: 2025-11-13
