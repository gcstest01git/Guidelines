# CLAUDE.md - AI Assistant Guide for Guidelines Repository

> **Last Updated:** 2025-11-14
> **Version:** 1.1
> **Repository:** Guidelines - Comprehensive Software Development Standards
> **Language:** Primary documentation is in German (Deutsch)

## 📋 Quick Reference

- **Repository Type:** Documentation/Guidelines Repository
- **Primary Focus:** Software development best practices, coding standards, API design
- **Documentation Language:** German (Deutsch)
- **License:** MIT License
- **Main Branch:** `main`
- **Repository Description:** Guidelines for coding. Generic are those copied from other well-known entities like Google or Zalando. Custom is a own version. Most files in "custom" are high quality summarization of the originals and in most cases reflect a mix of several generic guidelines.

## 🎯 Repository Purpose

This repository contains comprehensive guidelines and best practices for software development. It serves as a central reference for:

- **Coding Standards** and clean code principles
- **API Design Guidelines** (v2.0) following modern RESTful practices
- **Architecture Patterns** (SOLID, DRY, KISS, YAGNI)
- **Git Workflow** and version control best practices
- **Testing Strategies** and quality assurance
- **Security Guidelines** and responsible disclosure
- **Deployment Processes** and CI/CD practices

The repository is divided into:
- **`/custom/`** - High-quality summaries mixing multiple industry standards (primary content)
- **`/generic/`** - Placeholder for original guidelines from well-known entities (currently empty)

## 📝 Changelog

### Version 1.1 (2025-11-14)

- Erweiterte Quick Reference mit detaillierter Repository-Beschreibung
- Präzisierte Versionsinformationen für Hauptdokumente
- Hinzugefügt: Dokumenten-Versionsinformationen (API Guidelines v2.0, andere v1.0)
- Verbesserte Struktur-Dokumentation
- Aktualisierte Referenzen zu allen Hauptdokumenten

### Version 1.0 (2025-11-14)

- Initiale Version der CLAUDE.md
- Vollständige Repository-Struktur-Dokumentation
- Git Workflow und Conventions
- API Design Standards Referenzen
- Sicherheitsrichtlinien
- AI Assistant Guidelines und Best Practices

## 📂 Repository Structure

```
/Guidelines/
├── README.md                           # Main overview (German)
├── CLAUDE.md                           # This file - AI assistant guide
│
├── /custom/                            # Primary content directory
│   ├── README.md                       # Detailed guide (German)
│   ├── CODE_OF_CONDUCT.md             # Community behavior standards
│   ├── CONTRIBUTING.md                # Contribution guidelines
│   ├── LICENSE                        # MIT License
│   ├── SECURITY.md                    # Security policy & vulnerability reporting
│   │
│   ├── /docs/                         # Technical documentation
│   │   ├── ARCHITECTURE.md            # System architecture & design patterns
│   │   ├── API_GUIDELINES.md          # API Design Guidelines v2.0 ⭐
│   │   ├── CODING_STANDARDS.md        # Programming standards & clean code
│   │   ├── STYLE_GUIDE.md             # Code style & formatting
│   │   ├── TESTING.md                 # Test strategies & TDD
│   │   ├── GIT_WORKFLOW.md            # Git workflow & branch strategy
│   │   └── DEPLOYMENT.md              # Deployment & CI/CD
│   │
│   └── /.github/                      # GitHub templates
│       ├── ISSUE_LABEL_GUIDE.md       # Issue label descriptions
│       ├── /issue_template/
│       │   ├── bug_report.md
│       │   └── feature_request.md
│       └── /pull_request_template/
│           └── pull_request_template.md
│
└── /generic/                          # Empty (reserved for future use)
```

## 📚 Document Overview

### Core Documentation Files

| Document | Version | Updated | Description |
|----------|---------|---------|-------------|
| **API_GUIDELINES.md** | v2.0 | 2024-11 | Complete API design standards with Problem JSON (RFC 7807) |
| **ARCHITECTURE.md** | v1.0 | 2025-11-13 | System architecture, microservices, authentication patterns |
| **CODING_STANDARDS.md** | v1.0 | 2025-11-13 | Clean Code, SOLID principles, best practices |
| **STYLE_GUIDE.md** | v1.0 | 2025-11-13 | Code formatting and style conventions |
| **TESTING.md** | v1.0 | 2025-11-13 | TDD, test pyramid, coverage requirements (80%+) |
| **GIT_WORKFLOW.md** | v1.0 | 2025-11-13 | Git Flow, Conventional Commits, code review |
| **DEPLOYMENT.md** | v1.0 | 2025-11-13 | CI/CD, deployment strategies, Kubernetes |
| **SECURITY.md** | - | 2025-11-13 | Security policy, vulnerability reporting |
| **CONTRIBUTING.md** | - | 2025-11-13 | Contribution guidelines and workflow |
| **CODE_OF_CONDUCT.md** | - | 2025-11-13 | Community behavior standards |

### Key Features by Document:

**API_GUIDELINES.md (v2.0)** - The most comprehensive document:
- 23 main sections covering all aspects of API design
- Implementation checklist with 100+ items
- Problem JSON (RFC 7807) for standardized error handling
- Comprehensive pagination, filtering, sorting guidelines
- Rate limiting, timeouts, batch operations
- API governance and testing requirements

**ARCHITECTURE.md** - System design foundation:
- Multiple architecture patterns (microservices, event-driven, layered)
- Authentication methods (OAuth2, SSO, API Keys)
- Integration patterns and best practices
- API versioning strategies

**CODING_STANDARDS.md** - Code quality bible:
- Clean Code principles with practical examples
- SOLID principles explained with code samples
- Error handling patterns
- Performance optimization guidelines
- Security best practices

## 🔑 Key Conventions & Standards

### Git Workflow (Git Flow Inspired)

**Branch Types:**
- **`main`** - Production-ready code (protected, merge via PR only)
- **`develop`** - Integration branch for features
- **`feature/<description>`** - New features (branch from `develop`)
- **`bugfix/<description>`** - Bug fixes for `develop`
- **`hotfix/<version>`** - Critical production fixes (branch from `main`, merge to both `main` and `develop`)
- **`release/<version>`** - Release preparation (branch from `develop`, merge to `main` and `develop`)

**Branch Naming Examples:**
```bash
feature/user-authentication
bugfix/login-validation
hotfix/1.2.1
release/1.3.0
```

### Commit Message Convention (Conventional Commits)

**Format:**
```
<type>(<scope>): <subject>

[optional body]

[optional footer]
```

**Types:**
- `feat` - New feature
- `fix` - Bug fix
- `docs` - Documentation only changes
- `style` - Formatting (no code logic changes)
- `refactor` - Code restructuring
- `perf` - Performance improvement
- `test` - Adding/correcting tests
- `chore` - Maintenance, build, dependencies
- `ci` - CI/CD configuration
- `build` - Build system changes
- `revert` - Revert a commit

**Examples:**
```bash
feat(auth): add OAuth2 login
fix(api): handle null values in user endpoint
docs(readme): update installation instructions
```

**Rules:**
- Use imperative mood ("add" not "added" or "adds")
- No period at the end
- Max 50 characters for subject
- Lowercase after colon

### Code Quality Standards

**SOLID Principles:**
- **S**ingle Responsibility Principle
- **O**pen/Closed Principle
- **L**iskov Substitution Principle
- **I**nterface Segregation Principle
- **D**ependency Inversion Principle

**Clean Code Principles:**
- **DRY** (Don't Repeat Yourself)
- **KISS** (Keep It Simple, Stupid)
- **YAGNI** (You Aren't Gonna Need It)
- Meaningful naming
- Functions should do one thing
- Keep functions small (<20 lines ideal)
- Limit parameters (use object parameters)
- Avoid deep nesting (use early returns)

**Test Requirements:**
- Minimum 80% code coverage
- Target 90%+ for critical modules
- All public APIs must be tested
- Follow TDD when possible

### API Design Standards (v2.0)

**Key Principles:**
- **API-as-a-Product** - Treat APIs as products with clear customer value
- **API-First Design** - Specify APIs in OpenAPI before implementation
- **Resource-oriented** - Follow Google AIP-121 standards
- **RESTful** - Standard HTTP methods and status codes
- **Stateless** - Each request contains all necessary information

**HTTP Methods & Idempotency:**

| Method | Safe | Idempotent | Usage |
|--------|------|------------|-------|
| GET | ✅ | ✅ | Retrieve resources |
| POST | ❌ | ❌* | Create resources |
| PUT | ❌ | ✅ | Replace entire resource |
| PATCH | ❌ | ❌* | Partial update |
| DELETE | ❌ | ✅ | Delete resource |

*Can be made idempotent using Idempotency-Key header

**URL Structure:**
```
https://api.example.com/v1/resources
https://api.example.com/v1/resources/:id
https://api.example.com/v1/resources/:id/sub-resources
```

**Error Handling:**
- MUST use **Problem JSON (RFC 7807)** for errors
- Content-Type: `application/problem+json`
- Include: type, title, status, detail, instance, errors[], trace_id

**Response Format:**
```json
{
  "data": {
    "id": "uuid",
    "type": "resource-type",
    "attributes": {},
    "metadata": {
      "created_at": "2025-11-14T10:30:00Z",
      "updated_at": "2025-11-14T10:30:00Z",
      "version": 1,
      "etag": "hash"
    }
  },
  "meta": {
    "request_id": "uuid",
    "timestamp": "2025-11-14T10:30:00Z"
  }
}
```

**Pagination:**
- Prefer **cursor-based pagination** for large datasets
- Required when total > 10,000 or frequent changes
- Include: cursor, has_more, total, links (self, next, prev, first)

**Versioning:**
- URL-based: `/api/v1/`, `/api/v2/`
- Semantic Versioning (MAJOR.MINOR.PATCH)
- Support old versions for minimum 12 months
- Use Deprecation/Sunset headers

### Security Standards

**Authentication Methods:**
- OAuth2 (Authorization Code Flow, Client Credentials Flow)
- SSO (SAML 2.0 / OpenID Connect)
- API Keys (with scope-based permissions)

**Security Requirements:**
- HTTPS/TLS for all connections
- Input validation and sanitization
- Output encoding (prevent XSS)
- Parameterized queries (prevent SQL injection)
- Secure password hashing (bcrypt, Argon2)
- Rate limiting
- CORS configuration
- Security headers (HSTS, CSP, etc.)
- Never commit secrets (.env files, API keys, passwords)

## 🤖 AI Assistant Guidelines

### When Working with This Repository

1. **Language Awareness:**
   - Primary documentation is in **German**
   - Maintain German language for documentation updates unless requested otherwise
   - Code comments should follow existing language patterns

2. **Documentation Standards:**
   - All documentation must be comprehensive and well-structured
   - Use proper markdown formatting
   - Include table of contents for long documents
   - Add examples for clarity
   - Update "Zuletzt aktualisiert" (Last Updated) dates

3. **Git Operations:**
   - Always follow the Git Flow branch strategy
   - Use Conventional Commits format
   - Create atomic commits (one logical change per commit)
   - Never commit directly to `main` or `develop`
   - Update relevant documentation when making changes

4. **Code Quality:**
   - Follow SOLID principles
   - Apply Clean Code practices
   - Write tests for new functionality
   - Ensure code coverage meets standards
   - Perform security checks
   - Validate against coding standards

5. **Pull Request Process:**
   - Use the PR template in `.github/pull_request_template/`
   - Provide clear, descriptive titles (Conventional Commits format)
   - Include: Description, Changes, Testing, Screenshots (if UI), Breaking Changes checklist
   - Assign appropriate labels
   - Request reviewers
   - Ensure all tests pass before requesting review

6. **API Development:**
   - Always follow API Guidelines v2.0 in `custom/docs/API_GUIDELINES.md`
   - Use OpenAPI specification
   - Implement Problem JSON (RFC 7807) for errors
   - Include proper versioning
   - Document all endpoints
   - Consider idempotency

### Common Tasks & Workflows

#### Creating a New Feature

```bash
# 1. Ensure develop is up-to-date
git checkout develop
git pull origin develop

# 2. Create feature branch
git checkout -b feature/my-feature

# 3. Make changes, following coding standards
# 4. Write tests
# 5. Commit using Conventional Commits
git commit -m "feat(scope): add new feature"

# 6. Push and create PR
git push -u origin feature/my-feature
```

#### Updating Documentation

- Maintain German language unless requested otherwise
- Update "Zuletzt aktualisiert" date
- Update version number if applicable
- Ensure internal links are correct
- Verify markdown formatting
- Update table of contents if structure changes

#### Reviewing Code

Checklist (from `custom/docs/GIT_WORKFLOW.md`):
- [ ] Code does what it should
- [ ] No obvious bugs
- [ ] Edge cases considered
- [ ] Code is readable and maintainable
- [ ] Follows coding standards
- [ ] No unnecessary complexity
- [ ] DRY principle followed
- [ ] Tests present and meaningful
- [ ] Test coverage adequate
- [ ] No security vulnerabilities
- [ ] Input validation present
- [ ] No hardcoded secrets
- [ ] No performance issues
- [ ] Documentation updated

#### Security Vulnerability Response

**NEVER create public issues for security vulnerabilities!**

Follow process in `SECURITY.md`:
1. Contact privately: security@your-project.com
2. Provide: type, affected components, impact, reproduction steps, PoC, suggested fixes
3. Wait for coordinated disclosure

## 📚 Key Documents to Reference

### For API Development:
- **`custom/docs/API_GUIDELINES.md`** (v2.0, 2024-11) - Complete API design standards
  - API-as-a-Product principle, API-First design
  - Problem JSON (RFC 7807) error handling
  - Resource-oriented design (Google AIP-121)
  - Pagination, filtering, sorting, searching
  - Rate limiting, timeouts, idempotency
  - Deprecation policy with Sunset/Deprecation headers
  - Comprehensive implementation checklist
- **`custom/docs/ARCHITECTURE.md`** (v1.0, 2025-11-13) - System architecture and integration patterns
  - Microservices, event-driven, layered architectures
  - Authentication & authorization patterns (OAuth2, SSO, API Keys)
  - API versioning strategies
  - Integration patterns and best practices

### For Code Quality:
- **`custom/docs/CODING_STANDARDS.md`** (v1.0, 2025-11-13) - Programming standards and best practices
  - Clean Code principles (DRY, KISS, YAGNI)
  - SOLID principles with examples
  - Error handling, performance, security
  - Refactoring guidelines
- **`custom/docs/STYLE_GUIDE.md`** (v1.0, 2025-11-13) - Code formatting and style conventions
  - Language-specific style guides
  - Naming conventions
  - Code organization and structure

### For Testing:
- **`custom/docs/TESTING.md`** (v1.0, 2025-11-13) - Test strategies and quality assurance
  - Test-Driven Development (TDD)
  - Test pyramid (Unit, Integration, E2E)
  - Code coverage requirements (80% minimum, 90%+ for critical)
  - Testing frameworks and CI/CD integration

### For Git Workflow:
- **`custom/docs/GIT_WORKFLOW.md`** (v1.0, 2025-11-13) - Git workflow and branch strategy
  - Git Flow inspired branch strategy
  - Conventional Commits specification
  - Code review checklist
  - Merge strategies and best practices

### For Contributing:
- **`custom/CONTRIBUTING.md`** (2025-11-13) - Contribution guidelines
  - How to contribute to the project
  - Development setup and workflow
  - Pull request process
- **`custom/CODE_OF_CONDUCT.md`** (2025-11-13) - Community behavior standards
  - Expected behavior and responsibilities
  - Enforcement and reporting

### For Security:
- **`custom/SECURITY.md`** (2025-11-13) - Security policy and vulnerability reporting
  - Supported versions
  - Vulnerability disclosure process
  - Security best practices
  - Contact information for security issues

### For Deployment:
- **`custom/docs/DEPLOYMENT.md`** (v1.0, 2025-11-13) - Deployment processes and CI/CD
  - Deployment strategies (blue-green, canary, rolling)
  - CI/CD pipelines
  - Container orchestration (Docker, Kubernetes)
  - Monitoring and rollback procedures

## 🔍 Finding Information

### Quick Search Patterns:

**For API patterns:**
```bash
# Search API_GUIDELINES.md for specific topics
grep -n "pagination" custom/docs/API_GUIDELINES.md
grep -n "error handling" custom/docs/API_GUIDELINES.md
```

**For code examples:**
```bash
# Search CODING_STANDARDS.md for patterns
grep -A 10 "SOLID" custom/docs/CODING_STANDARDS.md
```

**For Git workflow:**
```bash
# Check GIT_WORKFLOW.md for branch strategies
grep -n "hotfix" custom/docs/GIT_WORKFLOW.md
```

## ⚠️ Important Reminders

### DO:
✅ Follow Conventional Commits format
✅ Maintain German language in documentation
✅ Update timestamps when modifying documents
✅ Write tests for new code
✅ Use Problem JSON (RFC 7807) for API errors
✅ Implement proper authentication and security
✅ Follow Git Flow branch strategy
✅ Keep functions small and focused
✅ Validate all inputs
✅ Use meaningful variable names
✅ Document complex logic
✅ Review security implications

### DON'T:
❌ Commit directly to `main` or `develop`
❌ Use generic commit messages like "fix" or "update"
❌ Create public issues for security vulnerabilities
❌ Commit secrets or credentials
❌ Ignore test coverage requirements
❌ Skip code reviews
❌ Create overly complex solutions (violate KISS)
❌ Repeat code (violate DRY)
❌ Ignore existing conventions
❌ Hard-code configuration values
❌ Use deprecated patterns

## 🎓 Learning Resources

### Within This Repository:

#### 🚀 For Quick Start (Schnellstart):
1. **`custom/CODE_OF_CONDUCT.md`** - Start here to understand community standards
2. **`custom/CONTRIBUTING.md`** - Learn how to contribute to the project
3. **`custom/docs/CODING_STANDARDS.md`** - Understand code quality expectations
4. **`custom/docs/GIT_WORKFLOW.md`** - Master the Git workflow

#### 💡 For New Contributors (Neue Mitwirkende):
Recommended reading order:
1. **`custom/CONTRIBUTING.md`** - Basic contribution rules
2. **`custom/CODE_OF_CONDUCT.md`** - Community guidelines
3. **`custom/docs/GIT_WORKFLOW.md`** - How we work with Git
4. **`custom/docs/CODING_STANDARDS.md`** - How we write code

#### 🔌 For API Developers (API-Entwickler):
Focus on these documents:
1. **`custom/docs/API_GUIDELINES.md`** (v2.0) - Complete API Design Guidelines
2. **`custom/docs/ARCHITECTURE.md`** - Architecture and integration patterns
3. **`custom/SECURITY.md`** - API-specific security aspects

#### 📖 Full Documentation Path:
1. Start with **`custom/README.md`** for repository overview
2. Read **`custom/CONTRIBUTING.md`** for contribution process
3. Study **`custom/docs/CODING_STANDARDS.md`** for code quality standards
4. Review **`custom/docs/GIT_WORKFLOW.md`** for Git best practices
5. Reference **`custom/docs/API_GUIDELINES.md`** for API development
6. Consult **`custom/docs/TESTING.md`** for testing requirements
7. Check **`custom/docs/DEPLOYMENT.md`** for deployment strategies

### External Standards Referenced:
- **Conventional Commits:** https://www.conventionalcommits.org/
- **Google AIP-121:** Resource-oriented design standards
- **RFC 7807:** Problem JSON format for API errors
- **RFC 2119:** Requirement level keywords (MUST, SHOULD, MAY)
- **OpenAPI Specification:** API documentation standard
- **Semantic Versioning:** Version numbering scheme (MAJOR.MINOR.PATCH)
- **Git Flow:** Branch management workflow
- **SOLID Principles:** Object-oriented design principles
- **Clean Code:** Robert C. Martin's clean code principles

## 🔄 Keeping This Document Updated

This CLAUDE.md file should be updated when:
- Major structural changes occur in the repository
- New guidelines or standards are adopted
- Workflow processes change significantly
- New tools or frameworks are standardized
- Important conventions are modified

**Maintainers:** Ensure this file remains the definitive guide for AI assistants working with this repository.

---

**Version:** 1.1
**Created:** 2025-11-14
**Last Updated:** 2025-11-14
**For Questions:** Refer to specific guideline documents in `/custom/docs/` or create an issue following the templates in `.github/issue_template/`

**Document Status:** ✅ Current and actively maintained
