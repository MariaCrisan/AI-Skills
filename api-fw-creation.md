# API Test Automation Framework — Skeleton

Create a **production-quality, extensible API test automation framework** that will serve as a skeleton for future API test development.

The primary goals are:

* Easy to add new API tests
* Reusable test components and utilities
* Strong separation between tests, API clients, configuration, authentication, assertions, and test data
* Environment/configuration driven behavior
* Minimal duplication
* Clear project structure
* Good reporting and logging
* Easy execution locally and in CI/CD
* The framework should contain a small set of meaningful example tests demonstrating the intended patterns

## 1. Technology

Use:

* **Language:** [CHOOSE LANGUAGE]
* **Test framework:** [CHOOSE TEST FRAMEWORK]
* **HTTP client:** [CHOOSE HTTP LIBRARY]
* **Assertion library:** [CHOOSE ASSERTION LIBRARY]
* **Build/dependency tool:** [CHOOSE TOOL]

If these technologies are not specified, choose a mature, widely adopted solution appropriate for API automation and explain the choice briefly.

Do not introduce unnecessary frameworks or dependencies.

---

# 2. Architecture

Design the framework using clear separation of responsibilities.

A suggested structure is:

```text
api-test-framework/
│
├── tests/
│   ├── auth/
│   │   ├── login_test
│   │   └── authentication_test
│   ├── health/
│   │   └── health_test
│   └── examples/
│       └── example_api_test
│
├── clients/
│   ├── base_client
│   ├── auth_client
│   └── example_client
│
├── models/
│   ├── requests/
│   └── responses/
│
├── assertions/
│   ├── response_assertions
│   └── common_assertions
│
├── auth/
│   ├── auth_manager
│   └── token_manager
│
├── config/
│   ├── configuration
│   ├── environment
│   └── config_loader
│
├── test_data/
│
├── utilities/
│   ├── logging
│   ├── serialization
│   ├── random_data
│   └── test_helpers
│
├── fixtures/
│
├── reports/
│
├── resources/
│   └── environments/
│
└── README
```

Adapt the structure to the selected language and framework, but preserve the separation of concerns.

---

# 3. Configuration

The framework must be configurable without modifying test code.

Support configuration such as:

```text
baseUrl
environment
requestTimeout
authentication
clientId
clientSecret
username
password
verifySsl
retryCount
loggingLevel
reporting
```

Configuration should support multiple environments, for example:

```text
local
dev
test
staging
prod
```

Environment-specific configuration should be loaded dynamically.

Sensitive values such as passwords, API keys, client secrets, and tokens must **never be committed to source control**.

Use environment variables or another appropriate mechanism for secrets.

Tests should be able to access configuration through a centralized configuration object/service rather than directly reading environment variables.

Example:

```text
config.baseUrl
config.timeout
config.environment
config.credentials
```

Allow command-line or environment-variable overrides where appropriate.

---

# 4. HTTP Client Layer

Create a reusable HTTP client abstraction.

It should provide common functionality such as:

```text
GET
POST
PUT
PATCH
DELETE
```

The client should centrally handle:

* Base URL
* Headers
* Authentication
* Request serialization
* Response deserialization
* Timeouts
* Logging
* Error handling
* Optional retries
* Correlation/request IDs

Tests should **not** directly construct low-level HTTP requests unless there is a specific reason.

Example conceptual usage:

```text
response = userClient.getUser(userId)
assert response.status == 200
```

rather than:

```text
httpClient.request(...)
```

inside every test.

---

# 5. API Client Pattern

Implement API-specific clients on top of the base HTTP client.

For example:

```text
BaseApiClient
    ├── AuthClient
    ├── UserClient
    └── ExampleClient
```

Each API client should encapsulate endpoint details.

For example:

```text
AuthClient.login()
AuthClient.refreshToken()

UserClient.getUser()
UserClient.createUser()
UserClient.deleteUser()
```

This should make tests readable and focused on behavior rather than HTTP implementation details.

---

# 6. Authentication

Implement a reusable authentication mechanism.

The framework should support authenticated tests without requiring every test to manually perform login.

Create an `AuthManager` / equivalent abstraction responsible for:

* Obtaining authentication tokens
* Caching tokens when appropriate
* Refreshing expired tokens
* Adding authentication to requests
* Clearing authentication state

Design it so additional authentication mechanisms can be added later.

For example:

```text
AuthenticationProvider
    ├── BearerTokenAuthentication
    ├── BasicAuthentication
    └── ApiKeyAuthentication
```

Do not hard-code authentication logic inside individual tests.

---

# 7. Example Tests

Create a small but useful baseline suite.

The tests should demonstrate the intended framework patterns.

## Health/API availability

Create a test verifying that the API is reachable.

Verify:

* HTTP status
* Basic response structure
* Reasonable response time if appropriate

## Authentication

Create tests covering:

### Valid credentials

Verify:

* Login succeeds
* Expected HTTP status is returned
* Authentication token/session is returned
* Token has the expected structure

### Invalid credentials

Verify:

* Authentication fails
* Correct HTTP status is returned
* Response contains an appropriate error
* No valid token is returned

### Protected endpoint

Create a test that:

1. Authenticates using the reusable authentication mechanism
2. Calls a protected endpoint
3. Verifies the request succeeds

### Missing/invalid authentication

Verify that a protected endpoint rejects an unauthenticated or invalidly authenticated request.

Do not assume exact endpoint paths or response schemas if they are not provided. Make these configurable or clearly mark them as example endpoints/placeholders.

---

# 8. Reusable Assertions

Create reusable assertion helpers for common API validations.

Examples:

```text
assertStatusCode(response, 200)
assertSuccess(response)
assertUnauthorized(response)
assertBadRequest(response)
assertResponseContains(response, field)
assertResponseTimeBelow(response, threshold)
```

Avoid putting too much logic into assertions.

Assertions should produce useful failure messages containing relevant information such as:

* Expected value
* Actual value
* HTTP status
* Endpoint
* Response body when useful

---

# 9. Test Data

Separate test data from test implementation.

Support:

* Static test data
* Environment-specific test data
* Generated/random test data
* Future data providers/parameterized tests

Avoid hard-coding credentials or environment-specific values inside tests.

Create examples demonstrating how a future developer would add test data.

---

# 10. Fixtures / Setup

Create reusable fixtures/setup mechanisms for things such as:

* API clients
* Authentication
* Configuration
* Test data
* Request context

A test should ideally be concise.

For example:

```text
test "authenticated user can access profile":
    client = authenticatedClient()
    response = client.getProfile()

    assertStatusCode(response, 200)
```

The framework should hide repetitive setup where appropriate.

---

# 11. Logging

Implement centralized logging.

Logging should provide enough information to troubleshoot failures, including:

* HTTP method
* Endpoint
* Status code
* Request duration
* Correlation/request ID
* Relevant request information
* Relevant response information

Be extremely careful not to log:

* Passwords
* API keys
* Client secrets
* Authentication tokens
* Other sensitive information

Sensitive headers and fields should be masked/redacted.

---

# 12. Reporting

Configure test reporting suitable for both local development and CI/CD.

At minimum, provide:

* Pass/fail status
* Test duration
* Failure reason
* Useful request/response diagnostics where appropriate

If the selected test framework supports standard machine-readable reports such as JUnit XML, enable them.

---

# 13. Error Handling

Create consistent handling for:

* Connection failures
* Timeouts
* Invalid responses
* Serialization/deserialization errors
* Authentication failures
* Unexpected HTTP status codes

Failures should provide actionable information rather than generic errors.

---

# 14. Retries

Support configurable retries for appropriate transient failures.

Important:

* Do not blindly retry every request.
* Do not hide real product defects with retries.
* Make retry behavior configurable.
* Clearly report when a retry occurred.

Prefer retrying transient infrastructure/network failures rather than functional assertion failures.

---

# 15. CI/CD

Make the framework easy to execute from CI/CD.

Provide commands/examples for:

```text
Run all tests
Run authentication tests
Run tests against a specific environment
Run tests with a specific configuration
Generate reports
```

For example:

```text
test --environment=dev
test --environment=staging
test --tags=auth
```

Adapt these commands to the selected technology.

---

# 16. Tags / Test Categories

Implement a mechanism for categorizing tests.

At minimum:

```text
smoke
regression
auth
health
example
```

Demonstrate how to run a subset of tests.

---

# 17. README

Create a comprehensive but concise README explaining:

1. Framework purpose
2. Architecture
3. Project structure
4. Prerequisites
5. Installation
6. Configuration
7. Environment configuration
8. Authentication
9. Running tests
10. Running specific test categories
11. Reports
12. Adding a new API client
13. Adding a new test
14. Adding a new environment
15. CI/CD usage
16. Security considerations

Include concrete examples.

---

# 18. Design Principles

Follow these principles throughout the implementation:

### Reusability

If functionality will likely be used by more than one test, consider putting it into a reusable component.

### Separation of concerns

Tests should describe **what is being verified**, while framework components handle **how requests are created and executed**.

### Configuration over hard-coding

Environment-specific behavior belongs in configuration, not test code.

### DRY

Avoid duplicated authentication, request construction, headers, parsing, assertions, and setup.

### Explicitness

Do not over-engineer the framework with unnecessary abstractions.

Prefer simple, understandable components that can evolve as the test suite grows.

### Extensibility

A developer should be able to add:

```text
new environment
new authentication mechanism
new API client
new endpoint
new test
new assertion
new test data provider
```

without modifying unrelated components.

---

# 19. Quality Requirements

Before finishing:

* Ensure the project builds successfully.
* Ensure the example tests compile/run.
* Ensure configuration works.
* Ensure authentication is reusable.
* Ensure secrets are not hard-coded.
* Ensure logging masks sensitive information.
* Ensure tests do not depend unnecessarily on execution order.
* Ensure failures contain useful diagnostics.
* Remove unnecessary dependencies and duplicated code.
* Add comments only where they explain non-obvious design decisions.
* Keep the framework skeleton small enough that a new developer can understand it quickly.

Do not create hundreds of example tests.

The goal is a **clean foundation for future test development**, not a fully implemented API test suite.

At the end, provide:

1. Final project structure
2. Explanation of the architecture
3. Explanation of the reusable components
4. Configuration strategy
5. Authentication strategy
6. Example commands for running tests
7. Instructions for adding the next API endpoint/test
8. Any assumptions or placeholders that need to be replaced for a real API
