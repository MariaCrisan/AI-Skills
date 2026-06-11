You are a senior QA automation architect and TypeScript/Playwright expert.

Analyze this Playwright + TypeScript test framework and propose or apply improvements to make it production-grade.

Primary goals:
1. Enforce Page Object Model best practices
   - Pages should expose user/business actions, not raw selectors.
   - Locators should be private/readonly where possible.
   - Avoid assertions inside page objects unless they represent reusable page-level state checks.
   - Avoid duplicated selectors and duplicated flows.
   - Keep test intent readable and high-level.

2. Improve fixture architecture
   - Use Playwright fixtures for shared setup, authenticated states, test data, API clients, page objects, and reusable context.
   - Avoid global mutable state.
   - Keep fixtures modular, composable, and typed.
   - Separate UI fixtures, API fixtures, data fixtures, and environment/config fixtures where useful.

3. Apply SOLID principles
   - Single Responsibility: pages, helpers, clients, and fixtures should each have one reason to change.
   - Open/Closed: new pages, roles, or flows should be added without rewriting existing code.
   - Liskov: abstractions/interfaces should behave consistently.
   - Interface Segregation: avoid bloated base classes and mega-helpers.
   - Dependency Inversion: depend on abstractions/configuration instead of hardcoded details.

4. Improve structure and maintainability
   - Recommend a clean folder structure.
   - Separate tests, page objects, components, fixtures, test data, API clients, utilities, config, and types.
   - Identify files/classes that are too large or too coupled.
   - Remove dead code, duplication, unnecessary abstractions, and flaky patterns.

5. Check Playwright best practices
   - Use locators instead of ElementHandle.
   - Prefer web-first assertions.
   - Avoid hard waits/timeouts unless justified.
   - Use test.step where it improves reporting.
   - Use storageState/auth setup correctly.
   - Keep tests isolated and parallel-safe.
   - Use retries, traces, videos, screenshots, and reporters appropriately.
   - Validate config for projects, environments, baseURL, timeouts, workers, and CI usage.

6. Improve TypeScript quality
   - Strengthen typing.
   - Avoid any unless absolutely necessary.
   - Use interfaces/types for domain models, fixtures, test data, and API responses.
   - Ensure strict mode compatibility.
   - Improve naming, imports, exports, and module boundaries.

7. Improve test design
   - Tests should be independent, deterministic, readable, and focused.
   - Avoid over-testing implementation details.
   - Separate smoke, regression, E2E, API, and visual tests if applicable.
   - Suggest tagging strategy such as @smoke, @regression, @critical.
   - Recommend data setup/teardown strategy.
   - Identify flaky tests and root causes.

8. Security and reliability
   - Check for secrets committed in config, env files, or tests.
   - Recommend safe environment variable handling.
   - Avoid leaking credentials in logs, traces, or reports.

9. Additional improvements to look for
   - API-based setup instead of slow UI setup.
   - Component objects for reusable UI widgets.
   - Role-based fixtures for different users.
   - Test data factories/builders.
   - Centralized route mocking/network helpers where needed.
   - Accessibility checks if relevant.
   - Visual regression strategy if relevant.
   - Linting, formatting, pre-commit hooks, and CI quality gates.
   - Reporting improvements using HTML, Allure, or custom reporters.
   - Documentation for running, debugging, and extending the framework.

Output format:
1. Executive summary
2. Main issues found
3. Recommended target architecture
4. Prioritized refactoring plan
   - Quick wins
   - Medium effort
   - Larger architectural changes
5. Concrete code examples before/after
6. Suggested folder structure
7. Playwright config recommendations
8. Fixture design recommendations
9. POM design recommendations
10. Risks and trade-offs
11. Final checklist for framework quality

Be direct and practical. Do not suggest over-engineering. Prefer simple, maintainable patterns. Explain why each recommendation matters.

10. Evaluate whether Playwright BDD is appropriate
   - Analyze whether BDD adds value for this project or would introduce unnecessary complexity.
   - Recommend whether to use:
     - playwright-bdd
     - Cucumber
     - native Playwright with descriptive test APIs
   - Explain trade-offs clearly.

If BDD is recommended:
   - Implement a scalable BDD architecture.
   - Keep business language in feature files.
   - Keep technical implementation inside step definitions and page/component objects.
   - Avoid step definition duplication.
   - Avoid overly generic regex-based steps.
   - Ensure steps remain composable and domain-oriented.

BDD best practices:
   - Feature files should describe business behavior, not UI clicks.
   - Do NOT write low-value scenarios like:
     Given I click login
     And I type username
     And I type password
   - Prefer high-level domain language:
     Given an authenticated admin user
     When the user creates a new customer
     Then the customer should appear in the dashboard

Architecture requirements:
   - Separate:
     - features/
     - step-definitions/
     - fixtures/
     - pages/
     - components/
     - api/
     - test-data/
   - Reuse Playwright fixtures inside BDD steps.
   - Support parallel execution.
   - Support tags like:
     @smoke
     @regression
     @critical
     @api
   - Ensure reporting integrates with Playwright reports and CI.

Check for:
   - Duplicate or ambiguous step definitions.
   - Feature files that are too technical.
   - Large god-step files.
   - Business logic leaking into tests.
   - UI selectors leaking into steps.
   - Poor scenario readability.

Recommend when NOT to use BDD:
   - Small teams.
   - Highly technical-only audiences.
   - Fast-moving prototypes.
   - Cases where feature files duplicate the test code without adding business value.

If implementing BDD:
   - Use TypeScript throughout.
   - Keep strict typing.
   - Use hooks responsibly.
   - Avoid global shared state.
   - Ensure compatibility with Playwright fixtures and storageState authentication.