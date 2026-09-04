# Build a TypeScript + Playwright API Test Automation Framework

Create a **clean, production-ready API test automation framework skeleton using TypeScript and Playwright**.

The purpose of this project is to provide a maintainable foundation for a growing API test suite. It should contain a small number of meaningful tests that validate general API functionality, especially authentication, while focusing heavily on **reusable components, configuration, maintainability, and extensibility**.

The framework should be simple enough for a new developer to understand quickly, but structured well enough to scale to hundreds or thousands of API tests.

---

## 1. Technology Stack

Use exactly:

* **TypeScript**
* **Playwright Test**
* **Playwright `APIRequestContext`** for API calls
* **npm** for package management

Do not introduce another API testing framework.

Use Playwright's native API testing capabilities wherever possible.

---

# 2. Project Goals

The framework must provide:

* Reusable API clients
* Centralized configuration
* Multiple environment support
* Reusable authentication
* Token management
* Common assertions
* Reusable fixtures
* Request/response logging
* Sensitive-data masking
* Test tagging/categorization
* Test data management
* CI/CD support
* HTML and JUnit reporting
* Clear documentation
* Easy creation of new API tests

The framework should follow **composition over inheritance** where practical and avoid unnecessary abstractions.

---

# 3. Recommended Project Structure

Use a structure similar to:

```text
api-test-framework/
│
├── tests/
│   ├── auth/
│   │   ├── login.spec.ts
│   │   ├── invalid-login.spec.ts
│   │   └── protected-endpoint.spec.ts
│   │
│   ├── health/
│   │   └── health.spec.ts
│   │
│   └── examples/
│       └── example-api.spec.ts
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
│   │   └── test-data.ts
│   │
│   └── utils/
│       ├── logger.ts
│       ├── random-data.ts
│       ├── serialization.ts
│       └── masking.ts
│
├── config/
│   ├── local.json
│   ├── dev.json
│   ├── test.json
│   └── staging.json
│
├── playwright.config.ts
├── package.json
├── tsconfig.json
├── .env.example
├── .gitignore
└── README.md
```

Adapt this structure if there is a better TypeScript/Playwright convention, but maintain the separation of responsibilities.

---

# 4. Playwright Configuration

Create a proper `playwright.config.ts`.

Configure:

* Test directory
* TypeScript
* Timeout
* Expect timeout
* Number of workers
* Retries
* Reporter
* Environment/configuration handling
* Trace on failure where useful
* JUnit reporter
* HTML reporter

Example reporting configuration should support both local development and CI:

```text
HTML report
JUnit XML
```

The configuration must not contain environment-specific URLs or secrets directly.

---

# 5. Environment Configuration

The framework must support multiple environments.

At minimum:

```text
local
dev
test
staging
```

Configuration should include values such as:

```text
baseUrl
apiVersion
timeout
verifySsl
auth
```

Example conceptual configuration:

```ts
config.baseUrl
config.apiVersion
config.timeout
config.auth
```

Tests must not directly read `process.env`.

Instead, provide a centralized configuration layer.

For example:

```ts
import { config } from '../src/config/config';

const baseUrl = config.baseUrl;
```

Support environment selection through an environment variable or command-line mechanism.

For example:

```bash
TEST_ENV=dev npx playwright test
```

or an equivalent clean approach.

---

# 6. Secrets

Never commit secrets.

Credentials such as:

```text
username
password
clientId
clientSecret
apiKey
accessToken
```

must come from environment variables or another secure mechanism.

Provide:

```text
.env.example
```

with placeholder values only.

Example:

```text
API_USERNAME=
API_PASSWORD=
CLIENT_ID=
CLIENT_SECRET=
```

Add `.env` to `.gitignore`.

Never print credentials or tokens in logs or reports.

---

# 7. Base API Client

Create a reusable `BaseApiClient`.

It should wrap Playwright's `APIRequestContext`.

Provide reusable methods:

```ts
get()
post()
put()
patch()
delete()
```

The client should centrally handle:

* Base URL
* Common headers
* Authentication
* Request timeout
* Request IDs/correlation IDs
* Logging
* Error handling
* Request serialization
* Response handling

For example:

```ts
await apiClient.get('/users');
await apiClient.post('/users', { data: payload });
```

Avoid repeating common request configuration in every test.

---

# 8. API Client Layer

Implement endpoint-specific API clients on top of the base client.

For example:

```text
BaseApiClient
    |
    +-- AuthClient
    |
    +-- UserClient
    |
    +-- ExampleClient
```

Example:

```ts
await authClient.login(credentials);

await userClient.getUser(userId);

await userClient.createUser(user);
```

The API client should encapsulate:

* Endpoint paths
* HTTP methods
* Request construction
* Endpoint-specific headers
* Request/response types

Tests should focus on business behavior rather than URLs and low-level HTTP implementation.

---

# 9. Authentication Architecture

Authentication must be implemented as a reusable component.

Create an abstraction such as:

```ts
AuthenticationProvider
```

and an implementation such as:

```ts
BearerTokenAuthenticationProvider
```

Create an `AuthManager` responsible for:

* Login
* Obtaining tokens
* Token caching
* Token expiration
* Token refresh where applicable
* Clearing authentication state

The design should allow additional authentication methods to be added later:

```text
AuthenticationProvider
    ├── BearerToken
    ├── ApiKey
    └── BasicAuth
```

Do not hard-code authentication logic inside tests.

---

# 10. Authentication Token Handling

Implement reusable token handling.

The framework should avoid logging tokens.

Where possible:

* Cache tokens during a test run
* Reuse valid tokens
* Refresh expired tokens
* Avoid unnecessary authentication requests

The implementation should be safe for parallel Playwright execution.

Do not create a single mutable global token that can cause race conditions between workers.

Explain the chosen token lifecycle strategy in the README.

---

# 11. Playwright Fixtures

Use Playwright fixtures to provide reusable framework components.

Create custom fixtures such as:

```ts
apiClient
authClient
authManager
userClient
authenticatedClient
```

For example, a test should be able to look approximately like:

```ts
test('authenticated user can access profile', async ({
  authenticatedClient
}) => {
  const response = await authenticatedClient.get('/profile');

  expect(response.status()).toBe(200);
});
```

The fixtures should handle repetitive setup and teardown.

Do not put business assertions inside fixtures.

---

# 12. Example Tests

Create a small baseline test suite.

Do not create a large fake API test suite.

The examples exist to demonstrate framework usage.

## Health Check

Create a health/API availability test.

Verify:

* API is reachable
* HTTP status is successful
* Basic response structure
* Optional response-time threshold

---

## Successful Authentication

Create a test that:

1. Sends valid credentials
2. Verifies successful HTTP response
3. Verifies token/session exists
4. Verifies expected response structure
5. Does not expose the token in logs

---

## Invalid Authentication

Create a test that:

1. Sends invalid credentials
2. Verifies authentication fails
3. Verifies appropriate HTTP status
4. Verifies error response structure
5. Verifies no usable token is returned

---

## Protected Endpoint

Create a test that:

1. Uses the reusable authentication mechanism
2. Obtains authentication automatically
3. Calls a protected endpoint
4. Verifies successful access

The test must not manually repeat login logic.

---

## Missing Authentication

Create a test that calls a protected endpoint without authentication.

Verify:

```text
401 / appropriate unauthorized response
```

Do not hard-code the status if the API contract has not been provided. Make it configurable or clearly identify it as an example assumption.

---

# 13. Assertions

Create reusable API assertions.

Examples:

```ts
expectStatus(response, 200);

expectSuccess(response);

expectUnauthorized(response);

expectBadRequest(response);

expectJsonResponse(response);

expectResponseContains(response, 'id');

expectResponseTimeBelow(response, 1000);
```

Use Playwright's native `expect` wherever appropriate.

Assertion failures should provide useful diagnostics.

For example:

```text
Expected status: 200
Actual status: 401
Method: GET
Endpoint: /users/me
Response body: ...
```

Do not expose secrets in assertion output.

---

# 14. Typed API Models

Use TypeScript types/interfaces for API requests and responses.

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

Keep API models separate from tests.

Where useful, use generic response typing.

For example:

```ts
ApiResponse<T>
```

Avoid creating types for every trivial object unless they provide meaningful value.

---

# 15. Test Data

Create a clean test-data strategy.

Separate:

```text
Configuration
Test data
Secrets
Generated data
```

Provide utilities for generated values:

```ts
randomEmail()
randomString()
randomUsername()
```

Tests should not contain hard-coded data unnecessarily.

For example:

```ts
const user = {
  email: randomEmail(),
};
```

Test data should be easy to replace later with fixtures, factories, or external data sources.

---

# 16. Logging

Implement centralized logging.

For API requests, logging should include:

```text
HTTP method
Endpoint
Status code
Duration
Request ID
```

Provide configurable logging levels:

```text
error
warn
info
debug
```

Mask sensitive information.

At minimum, redact:

```text
Authorization
Cookie
Set-Cookie
password
clientSecret
accessToken
refreshToken
apiKey
```

Make sure sensitive information cannot accidentally appear in Playwright reports.

---

# 17. Error Handling

Create consistent handling for:

* Network errors
* Timeout errors
* Invalid JSON
* Unexpected HTTP responses
* Authentication errors
* Configuration errors

Error messages should be actionable.

For example:

```text
Unable to connect to API.

Environment: staging
Base URL: https://example-api
Endpoint: /users
Method: GET
Timeout: 30000ms
```

Never include secrets in errors.

---

# 18. Retries

Use Playwright's retry mechanism for test-level retries.

Do not implement unnecessary custom HTTP retries initially.

If HTTP-level retries are introduced later, they should only apply to clearly transient failures and be configurable.

Document the difference between:

```text
test retry
```

and

```text
HTTP retry
```

---

# 19. Test Tags

Provide a mechanism for categorizing tests.

Use Playwright tags where appropriate.

Example:

```ts
test(
  'authenticated user can access profile',
  { tag: ['@auth', '@smoke'] },
  async ({ authenticatedClient }) => {
    // ...
  }
);
```

Use categories such as:

```text
@smoke
@auth
@health
@regression
```

Document how to execute tagged tests.

---

# 20. Parallel Execution

The framework must be safe for Playwright parallel execution.

Avoid:

* Mutable global state
* Shared authentication state that can be overwritten
* Shared test data
* Tests depending on execution order

Prefer Playwright fixtures and worker/test-scoped resources.

Explain any intentional shared state.

---

# 21. CI/CD

Provide npm scripts such as:

```json
{
  "scripts": {
    "test": "playwright test",
    "test:headed": "playwright test --headed",
    "test:smoke": "playwright test --grep @smoke",
    "test:auth": "playwright test --grep @auth",
    "test:health": "playwright test --grep @health",
    "report": "playwright show-report"
  }
}
```

Adapt as appropriate.

Support:

```bash
TEST_ENV=dev npm test
TEST_ENV=staging npm test
```

Document CI usage.

The framework should work cleanly in a headless CI environment.

---

# 22. README

Create a useful README.

Include:

### Overview

Explain what the framework is for.

### Architecture

Explain each major layer.

### Project structure

Explain where new code should go.

### Installation

Show:

```bash
npm install
npx playwright install
```

### Configuration

Explain environment selection and secrets.

### Running tests

Show common commands.

### Authentication

Explain how authentication is handled.

### Adding a new API client

Provide an example.

### Adding a new test

Provide a complete example.

### Adding a new environment

Explain the process.

### CI/CD

Explain how to execute in CI.

### Debugging

Explain:

* Playwright reports
* Traces
* Logs
* Failed API responses

### Security

Explain how secrets and sensitive request/response data are handled.

---

# 23. Example: Adding a New API

The README should demonstrate a workflow similar to:

```text
1. Create request/response types
2. Create API client
3. Add reusable fixture if necessary
4. Create test
5. Add test data
6. Add appropriate tag
7. Run locally
```

For example:

```ts
class UserClient extends BaseApiClient {
  async getUser(id: string) {
    return this.get<UserResponse>(`/users/${id}`);
  }
}
```

Then:

```ts
test(
  'user can be retrieved',
  { tag: ['@regression'] },
  async ({ userClient }) => {
    const response = await userClient.getUser('123');

    expect(response.status()).toBe(200);
  }
);
```

The exact implementation should follow the chosen architecture.

---

# 24. Important Design Constraint

**Do not over-engineer the framework.**

This is a skeleton for future development.

Avoid adding:

* Dependency injection frameworks
* Complex service locators
* Unnecessary factories
* Excessive inheritance
* Generic abstractions with no current use
* Large utility libraries
* Dozens of example endpoints
* Fake implementations that don't demonstrate real value

Every abstraction should have a clear purpose.

Prefer:

```text
simple
readable
composable
testable
extensible
```

over:

```text
clever
generic
complex
```

---

# 25. Definition of Done

The implementation is complete when:

* [ ] Project installs successfully with npm
* [ ] TypeScript compiles without errors
* [ ] Playwright tests execute successfully
* [ ] `playwright.config.ts` is properly configured
* [ ] Multiple environments are supported
* [ ] Secrets are externalized
* [ ] Base API client exists
* [ ] Endpoint-specific API clients exist
* [ ] Authentication is reusable
* [ ] Authentication tokens are handled safely
* [ ] Custom Playwright fixtures exist
* [ ] Reusable assertions exist
* [ ] Request/response logging exists
* [ ] Sensitive information is masked
* [ ] Test tags are implemented
* [ ] HTML and JUnit reports are configured
* [ ] Tests are safe for parallel execution
* [ ] Authentication tests exist
* [ ] Unauthorized access test exists
* [ ] Health check test exists
* [ ] README is complete
* [ ] No secrets are committed
* [ ] No unnecessary dependencies are included

---

# 26. Final Output Required From the Coding Agent

After implementing the framework, provide:

1. Final directory structure
2. Explanation of each architectural layer
3. Explanation of the authentication lifecycle
4. Explanation of configuration/environment handling
5. Explanation of Playwright fixtures
6. Explanation of how parallel execution is handled
7. Example commands for running tests
8. Example of adding a new API client
9. Example of adding a new test
10. List of assumptions/placeholders that must be replaced for the real API

Where the actual API contract is unknown, use clearly marked placeholders such as:

```text
<BASE_URL>
/auth/login
/health
/users/me
```

Do not pretend these endpoints exist.

The resulting repository should be a **working, clean skeleton that a team can immediately clone and begin extending**.
