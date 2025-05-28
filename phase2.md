Here is a **more detailed and production-grade expansion of Phase 2: Foundation Implementation** tailored for a real-time Cypress project scenario in an enterprise like Hexaware:

---

## 🧩 Phase 2: Foundation Implementation (Detailed)

---

### 🎯 **Goals (Expanded)**

| Objective                      | Details                                                                                                   |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| **Project Bootstrapping**      | Install essential dependencies, ensure ESLint/Prettier integration, create branch protection rules        |
| **CI/CD Pipeline Integration** | Implement GitHub Actions pipeline with artifact storage, job matrix for environments, test tagging        |
| **Reusable Command Library**   | Build and document commands for login, navigation, validation, API utilities                              |
| **Environment Isolation**      | Support multi-environment execution (`staging`, `qa`, `prod`) using `cypress.env.json` and GitHub secrets |

---

### 🔨 **Actions (Detailed)**

| Task                     | Description                                                                                                          |
| ------------------------ | -------------------------------------------------------------------------------------------------------------------- |
| **Custom Commands**      | `cy.loginAs()`, `cy.logout()`, `cy.seedDatabase()`, `cy.interceptAPI()`, `cy.clearSession()`                         |
| **Helper Modules**       | `auth-helpers.js` for token-based auth, `api-helpers.js` for request factories, `db-utils.js` (optional) for DB prep |
| **Test Data Management** | Create `fixtures/users.json`, `fixtures/data.json` for role-based execution and form data mocking                    |
| **Git Hooks**            | Use `Husky` for lint-staged pre-commit validations                                                                   |

---

### 📁 **New/Updated Project Structure**

```
hexa-cypress-qa/
├── cypress/
│   ├── support/
│   │   ├── commands/
│   │   │   ├── login.js
│   │   │   ├── navigation.js
│   │   │   └── api.js
│   │   ├── helpers/
│   │   │   ├── auth-helpers.js
│   │   │   └── api-helpers.js
│   │   ├── commands.js           # Combines all custom command files
│   │   └── e2e.js
├── .husky/
│   ├── pre-commit                # Runs lint and format checks
├── cypress.env.json              # Role credentials, tokens, env toggles
├── .env.staging                  # Local-only staging credentials
├── .env.qa                       # Local-only QA credentials
```

---

### ⚙️ **Sample Custom Commands (Extended)**

#### `cy.loginAs()` – Token Auth Based

```js
Cypress.Commands.add('loginAs', (role) => {
  cy.fixture('users').then(users => {
    const user = users[role];
    cy.request('POST', '/api/login', user).then(res => {
      window.localStorage.setItem('auth_token', res.body.token);
    });
  });
});
```

#### `cy.logout()` – Optional

```js
Cypress.Commands.add('logout', () => {
  cy.clearCookies();
  cy.clearLocalStorage();
});
```

#### `cy.seedDatabase()` – Optional (if DB mocking possible)

```js
Cypress.Commands.add('seedDatabase', () => {
  cy.request('POST', '/api/test/setup/seed');
});
```

---

### 🔐 **Environment Configuration Strategy**

| File                      | Usage                                                            |
| ------------------------- | ---------------------------------------------------------------- |
| `cypress.env.json`        | Local fallback config; stores user credentials and base URLs     |
| `.env.staging`, `.env.qa` | Loaded via CI/CD for different pipelines (masked in repo)        |
| `cypress.config.js`       | Reads from environment to adjust `baseUrl`, `retries`, and `env` |

---

### 🔁 **GitHub Actions Pipeline (Enhanced)**

```yaml
name: Cypress CI Pipeline

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        env: [staging, qa]
        browser: [chrome]

    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: 18

      - name: Install dependencies
        run: npm ci

      - name: Run Cypress tests
        uses: cypress-io/github-action@v5
        with:
          browser: ${{ matrix.browser }}
          start: npm run start:mock
          wait-on: 'http://localhost:3000'
          env: CYPRESS_ENV=${{ matrix.env }}

      - name: Upload test artifacts
        if: failure()
        uses: actions/upload-artifact@v3
        with:
          name: cypress-reports
          path: |
            cypress/screenshots
            cypress/videos
            reports/mochawesome/
```

---

### 📦 **Final Deliverables for Phase 2**

* [x] Cypress framework bootstrapped and committed
* [x] All essential plugins installed and configured
* [x] Role-based login commands implemented
* [x] GitHub Actions CI/CD fully running for staging & QA
* [x] Artifact uploads, parallel job support, and secrets managed
* [x] Shared testing documentation: `docs/setup.md`, `docs/contributing.md`

---

