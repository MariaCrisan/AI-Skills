# Build a Configurable TypeScript + Playwright BDD UI Test Framework Generator

I want to build a **CLI-based test framework generator** that scaffolds a production-ready UI automation framework using **TypeScript, Playwright, and BDD**.

The goal is not just to create a basic Playwright project. The CLI should generate a **maintainable, configurable, extensible test framework** with sensible defaults, generic example scenarios, CI support, reporting, authentication, API support, and a clear separation between framework infrastructure and application-specific tests.

## 1. Core technology

Use:

* TypeScript
* Playwright
* A mature BDD implementation compatible with Playwright
* Node.js
* npm/npx
* ESLint
* Prettier
* TypeScript strict mode

The generated project should follow modern TypeScript and Playwright best practices.

---

# 2. CLI

Create a CLI such as:

```bash
npx create-ui-bdd-framework
```

It should support both:

### Interactive mode

```bash
npx create-ui-bdd-framework
```

Prompt the user for configuration options such as:

* Project name
* Base URL
* BDD framework
* Browsers
* Headless/headed mode
* Environment strategy
* Authentication strategy
* Reporting
* CI provider
* API support
* Accessibility checks
* Parallel execution
* Retry configuration

### Non-interactive mode

Support flags such as:

```bash
npx create-ui-bdd-framework my-project \
  --base-url=https://my-app.com \
  --browsers=chromium,firefox \
  --bdd \
  --ci=github
```

The CLI should validate all configuration values and provide useful error messages.

---

# 3. Generated project structure

Generate a structure similar to:

```text
my-ui-tests/
├── features/
│   ├── authentication/
│   │   └── login.feature
│   └── common/
│       └── navigation.feature
│
├── tests/
│   ├── steps/
│   │   ├── common.steps.ts
│   │   └── login.steps.ts
│   │
│   ├── hooks/
│   │   └── hooks.ts
│   │
│   └── support/
│       ├── world.ts
│       └── context.ts
│
├── pages/
│   ├── LoginPage.ts
│   ├── DashboardPage.ts
│   └── components/
│       ├── Header.ts
│       ├── Modal.ts
│       └── DataTable.ts
│
├── actions/
│   └── authentication.actions.ts
│
├── fixtures/
│
├── data/
│   ├── environments/
│   └── test-data/
│
├── api/
│   └── ApiClient.ts
│
├── config/
│   └── test.config.ts
│
├── utils/
│
├── auth/
│   ├── login.setup.ts
│   └── storage/
│
├── reports/
│
├── .github/
│   └── workflows/
│       └── playwright.yml
│
├── playwright.config.ts
├── package.json
├── tsconfig.json
├── eslint.config.*
├── prettier.config.*
├── .env.example
├── .gitignore
└── README.md
```

Adapt the exact structure if there is a better architecture, but maintain a clear distinction between:

```text
framework/infrastructure
```

and:

```text
application-specific tests
```

The generated project must make it obvious where users should add their own tests.

---

# 4. Architecture

Use the following conceptual architecture:

```text
Gherkin Feature
      ↓
Step Definition
      ↓
Application Action
      ↓
Page Object / Component / API
      ↓
Playwright
```

BDD should act primarily as a **specification layer**, rather than coupling Gherkin directly to Playwright selectors.

For example:

```gherkin
Scenario: User can log in with valid credentials
  Given I am on the login page
  When I log in with valid credentials
  Then I should see the dashboard
```

The step definition should delegate to application actions/page objects instead of directly manipulating selectors.

Avoid putting large amounts of Playwright code inside step definitions.

---

# 5. Page Object Model

Implement Page Objects and reusable components.

Example:

```text
pages/
├── LoginPage.ts
├── DashboardPage.ts
└── components/
    ├── Header.ts
    ├── Modal.ts
    └── DataTable.ts
```

A step should look conceptually like:

```typescript
When('I log in with valid credentials', async function () {
  await this.loginPage.login(
    this.testData.username,
    this.testData.password
  );
});
```

Avoid:

```typescript
await page.locator('#username').fill(...);
await page.locator('#password').fill(...);
await page.locator('button').click();
```

inside step definitions.

---

# 6. Selector strategy

Document and enforce a sensible Playwright selector strategy.

Preferred order:

1. `getByRole()`
2. `getByLabel()`
3. `getByTestId()`
4. `getByText()`
5. CSS selectors when necessary
6. Avoid XPath unless there is a strong reason

Configure the test ID attribute, for example:

```typescript
use: {
  testIdAttribute: 'data-testid'
}
```

Document this strategy in the generated README.

---

# 7. BDD World / Test Context

Create a reusable test context/World containing:

```typescript
interface TestWorld {
  page: Page;
  browser: Browser;
  context: BrowserContext;
  pages: PageObjects;
  testData: TestData;
  api: ApiClient;
}
```

The exact implementation should follow the selected BDD framework's recommended architecture.

Support the lifecycle:

```text
BeforeAll
    ↓
Before
    ↓
Given / When / Then
    ↓
After
    ↓
AfterAll
```

Hooks should be centralized and configurable.

---

# 8. Generic example scenarios

Include a small number of generic scenarios demonstrating best practices.

Do NOT generate dozens of fake tests.

Include examples for:

* Authentication
* Navigation
* Form validation
* Search
* Table filtering
* Basic CRUD
* API-backed test setup

For example:

```gherkin
@smoke
Feature: Authentication

  Scenario: User can log in with valid credentials
    Given I am on the login page
    When I log in with valid credentials
    Then I should see the dashboard
```

The examples should clearly demonstrate how users should create their own scenarios.

---

# 9. Tags

Support BDD tags such as:

```text
@smoke
@regression
@critical
@auth
@api
@ui
@slow
```

Allow filtering by tags:

```bash
npm run test -- --tags @smoke
```

Make tag handling configurable.

---

# 10. Environment configuration

Make environment management a first-class feature.

Support:

```text
.env
.env.dev
.env.test
.env.staging
```

Create a typed configuration abstraction:

```typescript
export interface TestConfig {
  baseUrl: string;
  apiUrl?: string;
  username?: string;
  timeout: number;
}
```

Allow execution against different environments, for example:

```bash
npm test -- --env=staging
```

Never generate real credentials or secrets.

Generate:

```text
.env.example
```

and document required environment variables.

---

# 11. Authentication

Support reusable authenticated browser state.

Provide a mechanism conceptually similar to:

```text
auth/
├── login.setup.ts
└── storage/
    └── user.json
```

Avoid logging in through the UI before every test when authenticated state can safely be reused.

Allow configurable authentication strategies, such as:

* None
* Storage state
* Login setup
* Multiple users/roles

Potential configuration:

```yaml
authentication:
  strategy: storage-state
  users:
    - name: admin
      role: admin
```

---

# 12. API support

Include an optional API client abstraction.

For example:

```typescript
await world.api.users.create(user);
await world.pages.dashboard.open();
```

This should allow tests to create/setup data through APIs instead of using the UI whenever appropriate.

A scenario should be able to express:

```gherkin
Given a customer exists
When I open the customer page
Then I should see the customer
```

without requiring the customer to be created through the UI.

The API layer should be optional and configurable.

---

# 13. Test data

Create a clear test-data strategy.

Support:

```text
data/
├── environments/
└── test-data/
```

Avoid hard-coding test data inside step definitions.

Allow configuration for:

* Static test data
* Environment-specific data
* Generated data
* Fixtures
* API-created data

Make it easy to extend later.

---

# 14. Playwright configuration

Generate a production-ready `playwright.config.ts`.

Support configuration for:

* Base URL
* Browser projects
* Timeouts
* Expect timeout
* Retries
* Workers
* Parallel execution
* Headless/headed
* Screenshots
* Video
* Trace
* Authentication state
* Test artifacts
* Reporters

Default artifact strategy should be sensible for local development and CI.

For example:

* Screenshot on failure
* Trace on retry/failure
* Video configurable

---

# 15. Reporting

Support multiple reporting formats where appropriate:

* Playwright HTML
* JUnit XML
* JSON
* BDD report
* Optional Allure or similar reporting integration

A failed test should make debugging easy and expose:

```text
Scenario
Status
Error
Screenshot
Trace
Video
Console information
Relevant logs
```

Do not over-engineer reporting in the initial implementation; make it modular.

---

# 16. CI/CD

Allow optional CI generation.

For example:

```bash
--ci=github
```

should generate:

```text
.github/
└── workflows/
    └── playwright.yml
```

Potential CI providers:

* GitHub Actions
* GitLab CI
* Azure DevOps
* Jenkins

Do not generate configurations for providers the user did not select.

CI should:

1. Install dependencies
2. Install Playwright browsers
3. Configure environment
4. Execute tests
5. Store reports/artifacts
6. Fail correctly when tests fail

---

# 17. Accessibility

Consider optional accessibility smoke testing.

Allow something such as:

```bash
--accessibility
```

to enable accessibility testing infrastructure.

Keep this modular so projects that don't need it don't receive unnecessary dependencies.

---

# 18. Modular generator architecture

Design the generator so features can be enabled/disabled.

Conceptually:

```text
core
 ├── playwright
 ├── typescript
 ├── bdd
 ├── reporting
 ├── authentication
 ├── api
 ├── accessibility
 └── ci
```

For example:

```bash
create-ui-bdd-framework \
  --bdd=cucumber \
  --reporter=allure \
  --auth=storage-state \
  --api \
  --ci=github
```

The architecture should make adding future modules straightforward.

---

# 19. Generator commands

The CLI should eventually support commands such as:

```bash
ui-bdd create my-project
ui-bdd add page LoginPage
ui-bdd add feature authentication
ui-bdd add component DatePicker
ui-bdd add api-client users
ui-bdd generate
ui-bdd doctor
ui-bdd update
```

For the first version, prioritize:

```bash
ui-bdd create
```

but structure the codebase so the other commands can be added without major refactoring.

---

# 20. Doctor command

Plan for a diagnostic command:

```bash
ui-bdd doctor
```

It should eventually check:

```text
✔ Node version
✔ Playwright installation
✔ Browser binaries
✔ TypeScript configuration
✔ BDD configuration
✔ Environment variables
✔ CI configuration
⚠ Missing BASE_URL
```

Make the architecture extensible enough to support this later.

---

# 21. Framework versioning

The generated framework must be versioned.

Avoid silently changing the generated architecture in incompatible ways.

Support something conceptually like:

```bash
npx create-ui-bdd-framework@1
```

or:

```bash
ui-bdd create --template-version=1
```

The generated project should contain its framework/template version, for example:

```json
{
  "frameworkVersion": "1.0.0"
}
```

Plan for future migrations:

```bash
ui-bdd update
```

Do not implement a complicated migration system unless necessary for the first version, but design for it.

---

# 22. Developer experience

The generated project should work immediately after creation.

Ideally:

```bash
npx create-ui-bdd-framework my-project
cd my-project
npm install
npx playwright install
npm test
```

should run the included example test successfully.

Provide npm scripts such as:

```json
{
  "scripts": {
    "test": "...",
    "test:ui": "...",
    "test:headed": "...",
    "test:debug": "...",
    "test:smoke": "...",
    "test:report": "...",
    "lint": "...",
    "format": "...",
    "typecheck": "..."
  }
}
```

Use appropriate commands for the selected BDD framework.

---

# 23. README

Generate a useful README explaining:

* What the framework is
* Installation
* Project structure
* How to create a feature
* How to create a step definition
* How to create a Page Object
* How to create a reusable component
* How to configure environments
* How authentication works
* How API setup works
* How to run smoke tests
* How to run regression tests
* How to debug failures
* How to view reports
* How to run in CI
* Selector guidelines
* Tagging conventions
* How to extend the framework

The README should contain real examples from the generated project.

---

# 24. Code quality

The generated code must:

* Use TypeScript strict mode
* Avoid `any` unless genuinely necessary
* Have strong typing
* Follow SOLID principles where appropriate
* Avoid unnecessary abstractions
* Avoid duplicated configuration
* Keep step definitions small
* Keep Page Objects focused
* Keep framework infrastructure separate from application tests
* Avoid hard-coded credentials
* Avoid brittle selectors
* Include useful error messages
* Be easy for another developer to understand

Do not over-engineer the initial implementation.

Prefer simple, maintainable patterns.

---

# 25. Configuration schema

Create a typed configuration model for the generator itself.

For example:

```typescript
interface GeneratorConfig {
  projectName: string;
  baseUrl: string;
  bdd: {
    framework: string;
  };
  browsers: string[];
  environment: string;
  authentication?: {
    strategy: string;
  };
  api: boolean;
  accessibility: boolean;
  reporting: string[];
  ci?: string;
}
```

The exact schema can be improved based on implementation requirements.

Configuration should be validated before project generation.

---

# 26. Testing the generator

The generator itself must have automated tests.

Test:

### Unit tests

* Configuration validation
* CLI argument parsing
* Template selection
* File generation
* Conditional modules

### Integration tests

Generate projects with different configurations and verify:

* Expected files exist
* Unselected modules aren't generated
* `package.json` is correct
* Configuration is valid
* Generated TypeScript compiles

### Smoke test

Generate a complete project and verify that the example BDD test can execute successfully.

---

# 27. Important design principle

Do not make assumptions about the application being tested.

The generated framework should be **generic**.

The generic scenarios exist only to demonstrate patterns.

The generator should provide:

```text
good defaults
+
configuration
+
extension points
+
clear architecture
```

rather than forcing a specific application's architecture onto every project.

---

# 28. Expected deliverables

Implement the solution incrementally.

First provide:

1. Recommended architecture
2. Technology choices and rationale
3. CLI architecture
4. Configuration schema
5. Generated project structure
6. Template/module architecture
7. Example generated files
8. Example BDD scenarios
9. Testing strategy
10. Implementation plan

Then implement the generator.

When making implementation decisions, explain important trade-offs briefly.

If an existing library is a better choice than implementing functionality from scratch, prefer the established library.

Keep dependencies to a reasonable minimum.

The final result should be something a team could realistically use as the starting point for multiple Playwright + TypeScript + BDD projects.
