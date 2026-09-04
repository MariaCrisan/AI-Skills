# Build a TypeScript + Playwright BDD API Test Automation Framework

Create a **production-quality, extensible API test automation framework using TypeScript, Playwright, and Playwright BDD**.

The purpose of this project is to provide a clean, maintainable **BDD API testing skeleton** that can be used as the foundation for a growing API automation suite.

The framework must prioritize:

* BDD/Gherkin as the readable specification layer
* Playwright as the API execution engine
* Reusable step definitions
* Reusable API clients
* Reusable authentication
* Centralized configuration
* Multiple environment support
* Strong separation of concerns
* Type safety
* Parallel-safe execution
* CI/CD support
* Useful reporting
* Minimal duplication
* Easy extension as the API test suite grows

The result should be a **working framework skeleton**, not a large collection of example tests.

---

# 1. Technology Stack

Use exactly:

* **TypeScript**
* **Playwright Test**
* **Playwright BDD**
* **Playwright `APIRequestContext`**
* **npm**

Use Playwright's native API testing capabilities for HTTP communication.

Use Playwright BDD to provide the Gherkin/BDD layer.

Do not introduce another API testing framework.

Do not use Cucumber, Cypress, REST Assured, SuperTest, Axios, or another competing API testing framework.

Use the current stable version of Playwright BDD compatible with the selected Playwright version.

Pin compatible dependency versions where appropriate.

---

# 2. BDD Architecture

The framework must use **Gherkin feature files as the specification layer**.

The architecture should conceptually be:

```text
                    Gherkin Feature Files
                           │
                           ▼
                    BDD Step Definitions
                           │
                           ▼
                    Playwright Fixtures
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
        AuthManager   API Clients    Assertions
             │             │             │
             └─────────────┼─────────────┘
                           ▼
                   Base API Client
                           │
                           ▼
                 Playwright APIRequestContext
                           │
                           ▼
                          API
```

The responsibilities must remain clearly separated.

### Feature files

Describe **what behavior is being tested**.

### Step definitions

Translate Gherkin steps into reusable automation actions.

### Fixtures

Provide reusable test context and framework components.

### API clients

Encapsulate endpoint-specific API operations.

### Base API client

Encapsulate common HTTP behavior.

### Authentication

Handle obtaining and managing credentials/tokens.

### Assertions

Provide reusable API validation behavior.

### Configuration

Handle environment-specific settings.

---

# 3. Project Structure

Use a structure similar to:

```text
api-test-framework/
│
├── features/
│   ├── auth/
│   │   ├── authentication.feature
│   │   └── authorization.feature
│   │
│   ├── health/
│   │   └── health.feature
│   │
│   └── examples/
│       └── example-api.feature
│
├── steps/
│   ├── auth.steps.ts
│   ├── health.steps.ts
│   └── common.steps.ts
│
├── src/
│   ├── clients/
│   │   ├── base-api-client.ts
│   │   ├── auth-client.ts
│   │   └── example-client.ts
│   │
│   ├── auth/
│   │   ├── auth-manager.ts
│   │   ├── token-manager.ts
│   │   └── authentication-provider.ts
│   │
│   ├── config/
│   │   ├── config.ts
│   │   ├── config-loader.ts
│   │   └── environment.ts
│   │
│   ├── assertions/
│   │   ├── response-assertions.ts
│   │   └── common-assertions.ts
│   │
│   ├── fixtures/
│   │   └── api-fixtures.ts
│   │
│   ├── models/
│   │   ├── requests/
│   │   └── responses/
│   │
│   ├── test-data/
│   │   ├── factories/
│   │   └── test-data.ts
│   │
│   └── utils/
│       ├── logger.ts
│       ├── masking.ts
│       ├── random-data.ts
│       └── serialization.ts
│
├── config/
│   ├── local.json
│   ├── dev.json
│   ├── test.json
│   └── staging.json
│
├── playwright.config.ts
├── playwright-bdd.config.ts
├── package.json
├── tsconfig.json
├── .env.example
├── .gitignore
└── README.md
```

Adapt the structure to the actual Playwright BDD conventions where necessary.

Do not force a structure that conflicts with Playwright BDD's required generated test architecture.

The important requirement is that **Gherkin, step definitions, fixtures, API clients, authentication, configuration, and utilities remain clearly separated**.

---

# 4. Playwright BDD Integration

Configure Playwright BDD correctly.

The framework must support the normal Playwright BDD workflow:

```text
.feature files
      │
      ▼
BDD step definitions
      │
      ▼
generated Playwright tests
      │
      ▼
Playwright Test runner
```

Use the official/current Playwright BDD approach for:

* Feature discovery
* Step definition discovery
* Playwright test generation
* Fixture integration
* Tags
* Scenario execution
* Reporting

Do not manually parse Gherkin files.

Do not build a custom BDD engine.

The generated Playwright tests should be treated as an implementation detail rather than something developers manually edit.

Document the generation process and ensure it works as part of the normal test workflow.

---

# 5. Gherkin Standards

Feature files must be written using clear BDD conventions.

Use:

```gherkin
Feature:
Background:
Scenario:
Scenario Outline:
Given
When
Then
And
```

Where appropriate.

Feature files should describe **business/API behavior**, not implementation details.

Prefer:

```gherkin
When I authenticate with valid credentials
Then the authentication request should succeed
And an access token should be returned
```

over:

```gherkin
When I send a POST request to "/auth/login"
And I set header "Content-Type" to "application/json"
And I set body to ...
```

Avoid exposing low-level implementation details in Gherkin unless they are actually part of the behavior being verified.

---

# 6. Gherkin Feature Examples

Create a small baseline BDD suite.

The examples should demonstrate how future tests should be written.

Do not create a large fake API suite.

---

## Authentication Feature

Create:

```text
features/auth/authentication.feature
```

It should contain scenarios for successful and unsuccessful authentication.

Example structure:

```gherkin
Feature: API Authentication

  As an API consumer
  I want to authenticate with the API
  So that I can access protected resources

  @smoke @auth
  Scenario: Authenticate with valid credentials
    Given the API is available
    When I authenticate with valid credentials
    Then the authentication request should succeed
    And an access token should be returned

  @auth
  Scenario: Authenticate with invalid credentials
    When I authenticate with invalid credentials
    Then the authentication request should fail
    And an appropriate authentication error should be returned
```

Use placeholders for endpoint-specific behavior where the real API contract is unknown.

Do not invent an API contract.

---

# 7. Authorization Feature

Create:

```text
features/auth/authorization.feature
```

Include scenarios such as:

### Authenticated access

```gherkin
@auth
Scenario: Authenticated user can access a protected resource
  Given I am authenticated
  When I access a protected resource
  Then the request should succeed
```

### Missing authentication

```gherkin
@auth
Scenario: Unauthenticated user cannot access a protected resource
  Given I am not authenticated
  When I access a protected resource
  Then the request should be unauthorized
```

The protected endpoint should be configurable or clearly marked as a placeholder.

---

# 8. Health Feature

Create:

```text
features/health/health.feature
```

Example:

```gherkin
Feature: API Health

  @smoke @health
  Scenario: API is available
    When I check the API health
    Then the health request should succeed
    And the API should return a valid health response
```

---

# 9. Step Definitions

Step definitions must be **thin**.

They should translate Gherkin language into reusable framework calls.

Avoid putting HTTP implementation directly into step definitions.

Bad:

```ts
When('I authenticate with valid credentials', async () => {
  await request.post('/auth/login', {
    data: ...
  });
});
```

Prefer:

```ts
When('I authenticate with valid credentials', async ({ authManager }) => {
  await authManager.authenticateWithValidCredentials();
});
```

The step definition should orchestrate the behavior.

The underlying implementation should live in reusable components.

---

# 10. Reusable Step Definitions

Design step definitions to be reusable across features.

Avoid creating steps that are specific to one scenario when a more general step can be used.

For example, prefer:

```gherkin
Given I am authenticated
```

over:

```gherkin
Given I have logged in as test-user-1 using password test-password-1
```

Credentials should come from configuration/test data.

Similarly, prefer:

```gherkin
When I access the protected resource
```

over embedding endpoint implementation in the feature.

---

# 11. Playwright Fixtures

Use Playwright fixtures as the primary mechanism for providing framework components to step definitions.

Provide reusable fixtures for:

```text
apiClient
authClient
authManager
authenticatedClient
testContext
configuration
```

Step definitions should consume these fixtures rather than instantiate framework components manually.

The fixture design must be safe for:

* Scenario isolation
* Parallel execution
* Multiple Playwright workers
* Multiple users/credentials

Do not use mutable global state for scenario-specific data.

---

# 12. Scenario Context

Provide a clean way for steps within a scenario to share state.

For example, a scenario may need to retain:

```text
lastResponse
accessToken
requestId
createdResource
testData
```

Use the appropriate Playwright BDD/Playwright fixture mechanism rather than module-level global variables.

Scenario state must not leak between scenarios.

Parallel scenarios must not overwrite each other's state.

Document the chosen approach.

---

# 13. Base API Client

Create:

```text
src/clients/base-api-client.ts
```

It should wrap Playwright's:

```ts
APIRequestContext
```

Provide reusable operations:

```text
GET
POST
PUT
PATCH
DELETE
```

Common behavior should include:

* Base URL
* Headers
* Authentication
* Timeout
* Request IDs
* Logging
* Request serialization
* Response handling
* Error diagnostics

API calls should ultimately be executed through Playwright's `APIRequestContext`.

---

# 14. API Client Layer

Create endpoint-specific API clients.

For example:

```text
BaseApiClient
    │
    ├── AuthClient
    ├── UserClient
    └── ExampleClient
```

Example:

```ts
await authClient.login(credentials);

await userClient.getProfile();

await userClient.getUser(userId);
```

The clients should encapsulate endpoint implementation.

Gherkin scenarios should not know how HTTP requests are constructed.

---

# 15. Authentication

Implement reusable authentication using:

```text
AuthenticationProvider
AuthManager
TokenManager
```

The design should support future authentication mechanisms.

For example:

```text
AuthenticationProvider
    ├── BearerTokenAuthentication
    ├── ApiKeyAuthentication
    └── BasicAuthentication
```

Initially implement the authentication mechanism required by the example API.

If the actual authentication mechanism is unspecified, use a bearer-token example while clearly marking it as a placeholder.

---

# 16. Authentication Lifecycle

The authentication lifecycle should be:

```text
Scenario
   │
   ▼
AuthManager
   │
   ▼
AuthenticationProvider
   │
   ▼
AuthClient
   │
   ▼
BaseApiClient
   │
   ▼
API
```

The framework should support:

* Login
* Token retrieval
* Token caching where appropriate
* Token expiration
* Token refresh where supported
* Authentication headers
* Logout/cleanup where applicable

Do not require every scenario to manually perform login.

For example:

```gherkin
Given I am authenticated
```

should trigger the reusable authentication mechanism.

---

# 17. Token Safety

Tokens must never be logged.

Mask:

```text
Authorization
accessToken
refreshToken
clientSecret
password
apiKey
```

The framework must prevent credentials and tokens from appearing in:

* Console logs
* Playwright reports
* Error messages
* Debug output

unless explicitly required and safely masked.

---

# 18. Configuration

Configuration must be centralized.

Support:

```text
local
dev
test
staging
```

Configuration should include:

```text
baseUrl
apiVersion
timeout
verifySsl
authentication configuration
feature-specific endpoints
```

Tests and step definitions must not directly access `process.env`.

Use a centralized configuration service:

```ts
config.baseUrl
config.timeout
config.auth
```

Environment selection should be possible using something like:

```bash
TEST_ENV=dev npm test
```

or an equivalent clean mechanism.

---

# 19. Secrets

Never commit secrets.

Support environment variables for:

```text
API_USERNAME
API_PASSWORD
CLIENT_ID
CLIENT_SECRET
API_KEY
```

Provide:

```text
.env.example
```

with placeholder values.

Add `.env` to `.gitignore`.

Never place real credentials inside:

* `.feature` files
* Step definitions
* Test data committed to Git
* Configuration files
* README examples

---

# 20. API Models

Use TypeScript interfaces/types for request and response models.

For example:

```ts
interface LoginRequest {
  username: string;
  password: string;
}

interface LoginResponse {
  accessToken: string;
  tokenType: string;
  expiresIn?: number;
}
```

Keep models separate from:

* Gherkin
* Step definitions
* API clients

Avoid unnecessary models for trivial payloads.

---

# 21. Assertions

Create reusable API assertion helpers.

Examples:

```ts
expectStatus(response, 200);

expectSuccess(response);

expectUnauthorized(response);

expectBadRequest(response);

expectJsonResponse(response);

expectResponseContains(response, 'id');
```

Use Playwright's native `expect` wherever practical.

Assertions should generate useful diagnostics.

For example:

```text
Expected status: 200
Actual status: 401
Method: GET
Endpoint: /users/me
Response body: ...
```

Sensitive values must be masked.

---

# 22. BDD Assertion Style

The Gherkin scenarios should remain readable.

Prefer:

```gherkin
Then the authentication request should succeed
And an access token should be returned
```

rather than:

```gherkin
Then the response status code should be 200
And the response JSON property "access_token" should not be null
```

Both styles can be supported, but the primary examples should demonstrate behavior-focused BDD.

Reusable technical assertion steps may be provided when useful.

---

# 23. Test Data

Separate test data from implementation.

Support:

* Configuration data
* Credentials
* Static test data
* Generated test data
* Scenario-specific data
* Future data factories

Provide utilities such as:

```ts
randomEmail()
randomString()
randomUsername()
```

Do not put credentials directly in feature files.

For BDD examples, use meaningful test-data abstractions.

---

# 24. Scenario Outlines

Demonstrate a `Scenario Outline` where it provides real value.

For example, invalid authentication scenarios could eventually be parameterized.

Use examples only where they improve maintainability.

Do not turn every scenario into a Scenario Outline.

---

# 25. Background

Use `Background` where a common precondition genuinely improves readability.

For example:

```gherkin
Background:
  Given the API is available
```

Do not use Background to hide excessive setup.

Authentication should normally be explicit:

```gherkin
Given I am authenticated
```

when authentication is part of the scenario behavior.

---

# 26. Tags

Use Playwright BDD/Gherkin tags for test categorization.

At minimum:

```text
@smoke
@auth
@health
@regression
```

Example:

```gherkin
@smoke @auth
Scenario: Authenticate with valid credentials
```

Configure execution so tags can be filtered.

Provide examples such as:

```bash
npm run test:smoke
npm run test:auth
npm run test:health
```

Use the appropriate Playwright BDD filtering mechanism.

---

# 27. Logging

Implement centralized logging.

Log useful API diagnostics:

```text
HTTP method
Endpoint
Status code
Duration
Request ID
```

Support:

```text
error
warn
info
debug
```

Mask sensitive headers and payload fields.

Logging must work correctly when scenarios execute in parallel.

Where possible, associate logs with the current scenario/test.

---

# 28. Error Handling

Handle:

* Network failures
* Connection failures
* Timeouts
* Invalid JSON
* Unexpected responses
* Authentication failures
* Configuration failures

Provide actionable failure information.

Do not leak credentials or tokens into errors.

---

# 29. Retries

Use Playwright's retry mechanism for test/scenario retries.

Do not implement broad custom HTTP retries initially.

Do not retry functional assertion failures.

If HTTP retries are introduced later, restrict them to clearly transient failures and make them configurable.

Document the distinction between:

```text
Playwright test retry
```

and:

```text
HTTP retry
```

---

# 30. Parallel Execution

The framework must be safe for Playwright parallel execution.

Do not use:

* Mutable module-level scenario state
* Shared authentication tokens across unrelated scenarios
* Shared mutable test data
* Test-order dependencies

Use fixtures and scenario-scoped state appropriately.

Two scenarios running simultaneously must be completely isolated.

---

# 31. Reporting

Configure Playwright reporting.

Support:

```text
HTML
JUnit XML
```

The report should make it possible to understand:

* Feature
* Scenario
* Step
* Pass/fail status
* Duration
* Error
* Relevant API diagnostics

Use Playwright BDD's integration with Playwright reporting rather than creating a custom reporting system.

---

# 32. CI/CD

Provide npm scripts for:

```text
install
test
test:smoke
test:auth
test:health
test:regression
report
```

Examples:

```bash
TEST_ENV=dev npm test
TEST_ENV=staging npm test
npm run test:smoke
npm run test:auth
```

Ensure the framework can run headlessly in CI.

Document required environment variables/secrets.

---

# 33. README

Create a comprehensive README containing:

## Overview

What the framework does and why BDD is used.

## Architecture

Explain:

```text
Feature
  ↓
Step Definition
  ↓
Fixture
  ↓
API Client
  ↓
Base API Client
  ↓
Playwright APIRequestContext
  ↓
API
```

## Project structure

Explain where developers should place:

* Features
* Steps
* API clients
* Models
* Assertions
* Fixtures
* Test data
* Configuration

## Installation

Include:

```bash
npm install
npx playwright install
```

and any required Playwright BDD setup/generation command.

## Configuration

Explain environments and secrets.

## Running tests

Show:

```bash
npm test
npm run test:smoke
npm run test:auth
```

and environment selection.

## BDD workflow

Explain:

```text
Write feature
    ↓
Create/reuse step
    ↓
Use fixture
    ↓
Call API client
    ↓
Assert behavior
```

## Authentication

Explain the authentication lifecycle.

## Adding a new API client

Provide a concrete example.

## Adding a new feature

Provide a complete example.

## Adding a new step

Explain when a new step is appropriate.

## Reporting

Explain HTML/JUnit reports.

## Debugging

Explain logs, reports, traces, and failed API responses.

## CI/CD

Explain CI execution.

## Security

Explain secret handling and log masking.

---

# 34. Example Developer Workflow

The README should demonstrate the intended development workflow.

A developer adding a new endpoint should do approximately this:

### Step 1 — Define API models

```text
src/models/requests/
src/models/responses/
```

### Step 2 — Create or extend an API client

```text
src/clients/UserClient.ts
```

### Step 3 — Reuse an existing step

If an appropriate step exists, use it.

### Step 4 — Create a new step only when necessary

Add the step to the appropriate step-definition file.

### Step 5 — Create the feature

```text
features/users/users.feature
```

### Step 6 — Tag the scenario

```gherkin
@regression
```

### Step 7 — Execute

```bash
TEST_ENV=dev npm test
```

This workflow should be demonstrated in the README.

---

# 35. BDD Anti-Patterns to Avoid

Do not create:

### Giant step definitions

Avoid steps containing large amounts of HTTP logic.

### Low-level Gherkin

Do not expose every header, URL, serialization detail, and implementation detail in feature files.

### Duplicate steps

Before creating a step, check whether an existing reusable step can be parameterized or reused.

### Global state

Do not use global variables to share scenario data.

### Business logic in feature files

Gherkin describes behavior, not implementation.

### Business logic hidden inside generic steps

Avoid steps so generic that their actual behavior becomes difficult to understand.

### Over-abstraction

Do not create unnecessary layers just because this is a framework.

---

# 36. Design Principles

Follow these principles:

## Reusability

Reusable functionality belongs in framework components rather than individual step definitions.

## BDD readability

Feature files should be understandable by both technical and non-technical stakeholders.

## Separation of concerns

Each layer should have one clear responsibility.

## Configuration over hard-coding

Environment-specific values belong in configuration.

## DRY

Avoid duplicate steps, authentication logic, HTTP handling, assertions, and setup.

## Composition

Prefer composition over deep inheritance.

## Type safety

Use TypeScript types where they provide meaningful safety.

## Simplicity

Do not over-engineer.

The framework is a skeleton for future development, not a framework-building exercise.

---

# 37. Definition of Done

The implementation is complete when:

* [ ] TypeScript project is configured
* [ ] Playwright is configured
* [ ] Playwright BDD is correctly integrated
* [ ] `.feature` files execute through Playwright
* [ ] Step definitions are working
* [ ] Generated BDD tests execute successfully
* [ ] Playwright `APIRequestContext` is used for API communication
* [ ] Base API client exists
* [ ] Endpoint-specific API client exists
* [ ] Authentication manager exists
* [ ] Token management exists
* [ ] Authentication is reusable through fixtures
* [ ] Scenario state is isolated
* [ ] Multiple environments are supported
* [ ] Secrets are externalized
* [ ] Sensitive information is masked
* [ ] Reusable assertions exist
* [ ] TypeScript request/response models exist
* [ ] Test data is separated from implementation
* [ ] Tags are supported
* [ ] Parallel execution is safe
* [ ] HTML reporting works
* [ ] JUnit reporting works
* [ ] Health feature exists
* [ ] Successful authentication scenario exists
* [ ] Invalid authentication scenario exists
* [ ] Authenticated protected-resource scenario exists
* [ ] Unauthenticated protected-resource scenario exists
* [ ] README is complete
* [ ] npm scripts are provided
* [ ] CI/CD execution is documented
* [ ] No secrets are committed
* [ ] No unnecessary dependencies are introduced

---

# 38. Final Validation

Before considering the implementation complete:

1. Install all dependencies.
2. Verify TypeScript compilation.
3. Verify Playwright BDD generation/configuration.
4. Execute the example feature files.
5. Verify all example scenarios can run.
6. Verify tags can filter scenarios.
7. Verify environment configuration works.
8. Verify authentication is reusable.
9. Verify parallel execution does not cause state leakage.
10. Verify sensitive information is masked.
11. Verify HTML and JUnit reports are generated.
12. Verify the README commands actually work.
13. Remove dead code and unnecessary dependencies.

If the real API contract is not provided, clearly identify all assumed endpoints and payloads as placeholders.

Do not pretend that placeholder endpoints are real.

---

# 39. Final Response From the Coding Agent

After implementing the framework, provide:

1. Final project structure
2. Explanation of the BDD architecture
3. Explanation of how Playwright BDD integrates with Playwright Test
4. Explanation of the feature → step → fixture → API client flow
5. Explanation of authentication and token lifecycle
6. Explanation of scenario isolation and parallel execution
7. Configuration/environment strategy
8. Logging and security strategy
9. Commands for running the suite
10. Example of adding a new feature
11. Example of adding a new API client
12. Example of adding a reusable step
13. Any assumptions/placeholders that must be replaced for the real API

The final implementation should be a **clean, working, BDD-oriented TypeScript + Playwright foundation that a team can immediately begin extending**.
