# Build Confidence – Practices



Test With Purpose \- Practices

**Effective testing is a cornerstone of quality software, enabling teams to build with confidence and iterate quickly. The following practices detail how to implement tests that are meaningful, robust, and maintainable, ensuring they genuinely validate system behaviour.**

**Test for behaviour, not just implementation**

Tests should verify *what* a piece of code does (its externally observable behaviour) rather than *how* it does it (its internal structure or specific methods used). Behaviour-driven tests are more resilient to refactoring because they are coupled to the contract of the code, not its internal details. If the underlying implementation changes but the behaviour remains the same, the test should still pass. 

**Examples** 

**Scenario:** Testing a `UserProfileService` that fetches user preferences. 

**Bad Test (Implementation-focused):** 

This test checks if a specific internal method (`_fetchPreferencesFromDB`) is called. If that method is renamed or its role is absorbed into another, the test breaks even if the service's overall behaviour (`getPreferences`) is still correct.

JavaScript

```
// userService.js
class UserProfileService {
  constructor(db) {
    this.db = db;
  }
  async _fetchPreferencesFromDB(userId) {
    // complex logic to fetch from DB
    return this.db.query('SELECT ...');
  }
  async getPreferences(userId) {
    // ... some initial logic ...
    const prefs = await this._fetchPreferencesFromDB(userId);
    // ... some processing ...
    return prefs;
  }
}

// bad.test.js
it('should call _fetchPreferencesFromDB when getPreferences is invoked', () => {
  const mockDb = { query: jest.fn().mockResolvedValue({ theme: 'dark' }) };
  const service = new UserProfileService(mockDb);
  // Spy on the internal method
  const fetchSpy = jest.spyOn(service, '_fetchPreferencesFromDB');

  service.getPreferences('user123');

  expect(fetchSpy).toHaveBeenCalledWith('user123'); // Coupled to implementation detail
});
```

**Good Test (Behaviour-focused):** 

This test focuses on the outcome: given a user ID, the service should return the correct preference object. It doesn't care about internal method names or how the data is retrieved.

JavaScript

```
// good.test.js
it('should return user preferences for a given user ID', async () => {
  const mockDb = {
    query: jest.fn().mockResolvedValue({ theme: 'dark', notifications: 'enabled' })
  };
  const service = new UserProfileService(mockDb); // Assuming UserProfileService as defined before

  const preferences = await service.getPreferences('user123');

  expect(preferences).toEqual({
    theme: 'dark',
    notifications: 'enabled'
  });
  // We can also check if the DB was called appropriately but not specific internal methods
  expect(mockDb.query).toHaveBeenCalled(); // Or with a more specific query if that's part of the contract
});
```

**Write unit tests with clear intent** 

Each unit test should verify a single, specific piece of functionality, condition, or behaviour. Test names should be descriptive and clearly state what is being tested and what the expected outcome is. Adopting a clear structure like Arrange-Act-Assert (AAA) or Given-When-Then (GWT) helps maintain this clarity. This makes tests easier to understand, debug, and maintain. 

**Examples** 

**Scenario:** Testing a `PasswordValidator` class. 

**Bad Test:** A single test method `testPasswordValidation()` that tries to assert multiple conditions (length, special characters, uppercase) makes it hard to pinpoint which rule failed.

JavaScript

```
// Bad naming and multiple concerns
it('validates password', () => {
  const validator = new PasswordValidator();
  expect(validator.isValid('short')).toBe(false);       // Length
 expect(validator.isValid('longenough')).toBe(false);  // Missing special char
  expect(validator.isValid('LongEnough!')).toBe(true); // Valid
});
```

**Good Tests:** Separate tests for each validation rule, with descriptive names using a consistent pattern.

JavaScript

```
// passwordValidator.js (example implementation)
class PasswordValidator {
  isValid(password) {
    if (password.length < 8) return false;
    if (!/[!@#$%^&*]/.test(password)) return false;
    if (!/[A-Z]/.test(password)) return false;
    return true;
  }
}

// good.test.js
describe('PasswordValidator', () => {
  const validator = new PasswordValidator();

  it('should return false for passwords less than 8 characters', () => {
    // Arrange
    const shortPassword = 'short';
    // Act
    const result = validator.isValid(shortPassword);
    // Assert
    expect(result).toBe(false);
  });

  it('should return false for passwords without a special character', () => {
    // Arrange
    const noSpecialCharPassword = 'longenough';
    // Act
    const result = validator.isValid(noSpecialCharPassword);
    // Assert
    expect(result).toBe(false);
  });

  it('should return false for passwords without an uppercase letter', () => {
    // Arrange
    const noUppercasePassword = 'longenough!';
    // Act
    const result = validator.isValid(noUppercasePassword);
    // Assert
    expect(result).toBe(false);
  });

  it('should return true for valid passwords meeting all criteria', () => {
    // Arrange
    const validPassword = 'ValidPassword1!';
    // Act
    const result = validator.isValid(validPassword);
    // Assert
    expect(result).toBe(true);
  });
});
```

**Use integration tests to cover real system flows** 

Integration tests verify the interactions between different components, modules, or services of an application. They ensure that these parts work together as expected to achieve a specific outcome, simulating real user scenarios or API interactions. Unlike unit tests that test parts in isolation, integration tests focus on the "glue" that holds them together. 

**Examples** 

**Scenario:** An e-commerce API where creating an order involves an `OrderController`, `OrderService`, `ProductService` (to check stock), and `NotificationService`. **Test Flow:**

1. **Setup:** Ensure dependent services like `ProductService` are primed with necessary test data (e.g., product in stock) using mocks or a test database. Mock the `NotificationService` to verify it gets called, without actually sending an email.  
2. **Action:** Send an HTTP POST request to the `/orders` endpoint (simulating an API call to the `OrderController`).  
3. **Verification:**  
   * Assert that the HTTP response is `201 Created` and contains the order details.  
   * Verify that the `OrderService` created an order in the database (or its test double).  
   * Verify that the `ProductService` was queried for stock.  
   * Verify that the (mocked) `NotificationService`'s `sendOrderConfirmation` method was called with the correct order details.

TypeScript

```
// Pseudocode for an integration test
describe('/orders API endpoint', () => {
  let mockProductService;
  let mockNotificationService;
  let orderController; // Assuming this uses the services

  beforeEach(() => {
    // Setup mocks for services ProductService and NotificationService
    mockProductService = {
      checkStock: jest.fn().mockResolvedValue(true),
      reduceStock: jest.fn().mockResolvedValue(true)
    };
    mockNotificationService = {
      sendOrderConfirmation: jest.fn().mockResolvedValue(undefined)
    };
    // Initialize OrderService with real dependencies or controlled test doubles
    // Initialize OrderController with these services
  });

  it('should create an order, reduce stock, and send notification', async () => {
    // Arrange: Define order payload
    const orderPayload = { userId: 'user1', productId: 'prodABC', quantity: 1 };

    // Act: Simulate an API call to the controller/endpoint
    // const response = await orderController.createOrder(orderPayload);
    // For a real HTTP test, you'd use a library like supertest:
    // const response = await request(app).post('/orders').send(orderPayload);


    // Assert: (assuming direct controller call for simplicity here)
    // expect(response.status).toBe(201);
    // expect(response.body.orderId).toBeDefined();
    // expect(response.body.status).toBe('CONFIRMED');

    expect(mockProductService.checkStock).toHaveBeenCalledWith('prodABC', 1);
    expect(mockProductService.reduceStock).toHaveBeenCalledWith('prodABC', 1);
    expect(mockNotificationService.sendOrderConfirmation).toHaveBeenCalledWith(
      expect.objectContaining({ userId: 'user1', productId: 'prodABC' })
    );
    // Further assertions: check database state if applicable
  });
});
```

**Note:** Integration tests are typically slower and more complex to set up than unit tests, so they should focus on critical interaction points rather than exhaustive combinations.

**Avoid fragile or flaky tests** 

Fragile tests break easily due to minor, unrelated changes in the production code (e.g., renaming a private method, changing UI structure that doesn't affect behaviour). Flaky tests pass or fail inconsistently without any code changes, often due to issues like race conditions, reliance on external systems, unmanaged test data, or timing dependencies. Both erode confidence in the test suite. **Examples** **Fragile Test (UI Example):** A UI test that uses a very specific CSS selector or relies on the exact text of a button.

JavaScript

```
// Fragile selector
const submitButton = document.querySelector('div.container > form#checkoutForm > button.primary-button.large');
// If class names or structure change, test breaks.
```

**Better Approach (UI):** 

Prefer queries that mirror how a real user interacts — by visible text, accessible labels, or roles

Use `data-testid` only when there isn’t a meaningful text label or role available.

Avoid over-relying on styling hooks (class names, deep CSS paths).

JavaScript

```
<!-- HTML -->
<button aria-label="Submit order">Submit</button>
// Query by accessible role + label
const submitButton = screen.getByRole('button', { name: /submit order/i });
<!-- If no good label available -->
<button data-testid="checkout-submit-button">Submit</button>
// Fallback: query by test id
const submitButton = screen.getByTestId('checkout-submit-button');

```

**Flaky Test (Timing Dependent):** A test for an asynchronous operation that uses a fixed `setTimeout` to wait for completion.

JavaScript

```
it('updates user status after some time', (done) => {
  userService.updateStatusAsync('user123', 'active');
  setTimeout(() => {
    const status = userService.getStatus('user123');
    expect(status).toBe('active');
    done(); // Test might pass or fail depending on how long updateStatusAsync actually takes
  }, 500); // 500ms might not always be enough
});
```

**Better Approach (Async):** Use promises, async/await, or specific mechanisms from testing libraries to wait for conditions.

JavaScript

```
it('updates user status after async operation', async () => {
  await userService.updateStatusAsync('user123', 'active'); // Assuming this returns a promise
  const status = userService.getStatus('user123');
  expect(status).toBe('active');
});
```

**Other causes and solutions:**

* **Shared State:** Ensure tests are isolated and don't share mutable state. Reset state before/after each test.  
* **External Dependencies:** Mock external services reliably or use dedicated test instances.  
* **Random Data:** If tests use random data, ensure failures can be reproduced by seeding the random generator or logging the problematic data.  
* Using current date/time: This can cause similar problems to random data where you can’t guarentee the test will always pass.

**Tests fail when they should** 

Conversely to flaky tests, having a suite of tests that pass no matter what you throw at it gives a false sense of security. For a test to be considered as valuable, it needs to fail when a bug is introduced. Additionally, for each bug that’s released consider how you would have prevented that with an automated test.

Failing tests that catch bugs should be treated as a success, rather than a headache. 

**Mutation testing:**

Running mutation testing against your code base gives you insight into what areas of the code are covered by *valuable* tests. This goes a step further than code coverage, because it checks which tests fail when a code change is made, simulating what would happen when an engineer untowardly does this.

For each failure flagged by mutation testing, evaluate the likelihood and severity of the potential bug, and prioritise as appropriate.

**Align with the testing pyramid** 

The testing pyramid is a model that guides the allocation of different types of tests in a project. It advocates for having a large base of fast, isolated unit tests, a smaller layer of integration tests that verify component interactions, and an even smaller layer of end-to-end (E2E) or UI tests that validate the entire system flow. The rationale is to optimize for feedback speed, cost of writing/maintaining tests, and reliability of test results. 

**Examples** 

**Visual (Conceptual):**

* **Base (Largest): Unit Tests** \- Test individual functions, classes, or modules in isolation. Fast, numerous, easy to write and debug.  
* **Middle: Integration Tests** \- Test interactions between components/services. Slower than unit tests, fewer in number.  
* **Top (Smallest): End-to-End (E2E) / UI Tests** \- Test the entire application flow from the user's perspective. Slowest, most brittle, and most expensive to maintain; use sparingly for critical user journeys.

**Scenario:** Developing a new "Add to Cart" feature in an e-commerce application. **Test Distribution:**

* **Unit Tests (Many):**  
  * Test the `CartService` methods: `addItem(item, quantity)`, `calculateTotal()`, `removeItem(itemId)`.  
  * Test validation logic: quantity must be positive, item must exist (mocking product lookup).  
  * Test any helper functions for price calculation or discount application within the cart module.  
* **Integration Tests (Fewer):**  
  * Test the API endpoint for adding an item to the cart:  
    * Verify that calling `POST /cart/items` with valid product data correctly updates the cart (e.g., checks database or session) and interacts appropriately with a (mocked or test-instanced) `ProductService` to check availability.  
  * Test interaction between `CartService` and `InventoryService` when an item is added.  
* **End-to-End / UI Tests (Fewest):**  
  * One test to simulate a user Browse a product, clicking "Add to Cart," and verifying the item appears in the mini-cart UI and the cart page correctly reflects the new item and total.

**Anti-Patterns:**

* **Inverted Pyramid (Ice Cream Cone):** Relying heavily on slow, brittle E2E tests with few unit or integration tests. Leads to slow feedback, high maintenance, and unstable CI pipelines.  
* **Hourglass:** Many unit tests and E2E tests, but very few integration tests, leading to a gap in testing component interactions.

# Catch Issues Early \- Practices

Building high-quality, maintainable, and collaborative software requires a systematic approach that integrates quality checks and consistency into every stage of the development workflow. These practices not only reduce bugs and technical debt but also improve team efficiency and code readability. Below are some key software development best practices focused on code quality, consistency, and automated validation.

**Use pre-commit hooks for quick checks**

Pre-commit hooks are scripts that run automatically before a commit is finalised in a Git repository. They serve as an immediate feedback mechanism, catching common issues like syntax errors, formatting inconsistencies, or basic linting violations *before* code is even committed. This prevents "dirty" code from entering the version history and saves time by catching problems early.

**Benefits:**

* **Instant Feedback:** Developers get immediate notification of issues.  
* **Prevents Bad Commits:** Stops problematic code from being committed.  
* **Enforces Standards:** Helps maintain code quality and consistency across the team.

**Example:** Using `husky` (for JavaScript/Node.js projects) or `pre-commit` (for Python projects) to set up a hook that runs a linter and formatter:

```shell
# For a Node.js project using husky and lint-staged
# 1. Install husky and lint-staged
npm install husky lint-staged --save-dev

# 2. Add a 'prepare' script to package.json to enable husky
# package.json
{
  "name": "my-app",
  "version": "1.0.0",
  "scripts": {
    "prepare": "husky"
  },
  "devDependencies": {
    "husky": "^9.0.0",
    "lint-staged": "^15.0.0"
  },
  "lint-staged": {
    "*.{js,jsx,ts,tsx}": ["eslint --fix", "prettier --write"],
    "*.{json,css,md}": ["prettier --write"]
  }
}

# 3. Add a pre-commit hook via husky
npx husky add .husky/pre-commit "npx lint-staged"
```

Now, every time a developer tries to `git commit`, `lint-staged` will run ESLint (linter) and Prettier (formatter) only on the staged files, fixing issues and ensuring code quality before the commit is made.

**Run linting and formatting automatically**

Linting and formatting tools enforce consistent code style, identify potential errors, and improve readability. Automating their execution ensures that all code adheres to team standards without manual effort or subjective debates.

* **Linting:** Analyses code for programmatic errors, stylistic issues, and suspicious constructs (e.g., unused variables, unreachable code).  
* **Formatting:** Automatically adjusts code layout, indentation, spacing, and line breaks to a predefined style.

**Benefits:**

* **Code Consistency:** All code looks the same, regardless of who wrote it.  
* **Reduced Cognitive Load:** Developers can focus on logic, not style.  
* **Early Bug Detection:** Linters can catch subtle errors that compilers might miss.  
* **Improved Readability:** Consistent formatting makes code easier to read and understand.

**Example:** Integrating **ESLint** (for JavaScript/TypeScript) and **Prettier** (for formatting) into the development workflow:

1. **IDE Integration:** Configure developer IDEs (e.g., VS Code) to automatically run Prettier on save and display ESLint warnings/errors.  
2. **Pre-commit Hooks:** As shown above, use `lint-staged` to format and lint staged files before commit.  
3. **CI/CD Pipeline:** Add a step in the CI pipeline to run linters and formatters on the entire codebase or changed files, failing the build if violations are found.

YAML

```
# Example GitHub Actions step for linting and formatting check
name: CI
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - name: Use Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '20'
    - name: Install dependencies
      run: npm ci
    - name: Run ESLint
      run: npm run lint # Assumes 'lint' script in package.json runs ESLint
    - name: Check Prettier formatting
      run: npx prettier --check . # Fails if files are not formatted correctly
```

**Set up type checking and static analysis**

This practice goes beyond basic linting to perform deeper code analysis.

* **Type Checking:** Verifies type correctness in languages that support it (e.g., TypeScript, Python with MyPy). This helps prevent runtime errors caused by type mismatches.  
* **Static Analysis (Deeper Level):** Tools analyse code for more complex issues like potential null pointer dereferences, resource leaks, dead code, security vulnerabilities (SAST), and adherence to architectural patterns, without executing the code.

**Benefits:**

* **Reduced Runtime Errors:** Catches type-related bugs early.  
* **Improved Code Quality:** Identifies complex logical flaws and potential issues.  
* **Enhanced Refactoring:** Provides confidence when making changes, as the type checker will flag inconsistencies.  
* **Better Documentation:** Type annotations serve as a form of documentation.

**Example:** Using **TypeScript** for type checking and **SonarQube** for comprehensive static analysis:

1. **TypeScript Integration:**

```ts
// Example TypeScript code
interface User {
  id: number;
  name: string;
  email?: string; // optional property
}
function greetUser(user: User): string {
  return `Hello, ${user.name}!`;
}
const myUser: User = { id: 1, name: "Alice" };
console.log(greetUser(myUser));
// This would cause a type error during compilation:
// greetUser({ id: "2", name: 123 });
```

2. The TypeScript compiler (`tsc`) runs as part of the build process, catching type errors before runtime.  
3. **SonarQube Integration:** SonarQube is integrated into the CI/CD pipeline. After the code is built, a SonarQube scanner analyzes the codebase. It provides a detailed report on code quality, security vulnerabilities, and technical debt, often with a "Quality Gate" that can block deployments if critical issues are found.

**Define clear PR review standards**

Pull Request (PR) reviews are a critical human-driven quality gate. Establishing clear standards ensures that reviews are consistent, effective, and constructive, focusing on key aspects of code quality, correctness, and adherence to design.

**Benefits:**

* **Improved Code Quality:** Catches bugs, logical flaws, and design issues.  
* **Knowledge Sharing:** Spreads knowledge about the codebase and best practices.  
* **Mentorship:** Provides opportunities for less experienced developers to learn.  
* **Consistency:** Ensures adherence to coding standards, architectural patterns, and security guidelines.

**Example:** A team's PR review standards might include:

* **Checklist for Reviewers:**  
  * **Functionality:** Does the code meet the requirements? (Test locally if needed).  
  * **Correctness:** Are there any logical errors or edge cases missed?  
  * **Readability:** Is the code clear, concise, and well-commented where necessary?  
  * **Maintainability:** Is the code easy to understand and modify for future developers?  
  * **Performance:** Are there any obvious performance bottlenecks?  
  * **Security:** Are common vulnerabilities avoided (e.g., input validation, access control)?  
  * **Tests:** Are there adequate unit, integration, and (if applicable) E2E tests? Do they pass?  
  * **Error Handling:** Is error handling robust and user-friendly?  
  * **Naming Conventions:** Are variables, functions, and classes named clearly and consistently?  
  * **Documentation:** Is relevant documentation updated (e.g., README, API docs)?  
* **Process:**  
  * Minimum number of approvals required (e.g., 2).  
  * Reviewers should aim to provide feedback within 24 hours.  
  * Discussions should be constructive and focused on the code, not the person.  
  * Large PRs should be avoided; break them down into smaller, focused changes.

**Run tests in CI, not just locally**

While local testing is essential for developer productivity, relying solely on it is risky. Running tests in a Continuous Integration (CI) environment ensures that tests are executed in a clean, consistent, and standardized environment, independent of individual developer setups. This acts as a critical gatekeeper for merging code.

**Benefits:**

* **Environment Consistency:** Eliminates "works on my machine" problems.  
* **Automated Verification:** Ensures that all tests pass for every code change.  
* **Regression Prevention:** Catches regressions (new bugs in old features) early.  
* **Quality Gate:** Prevents faulty code from being merged into the main branch or deployed.  
* **Faster Feedback for Team:** Everyone knows the status of the codebase quickly.

**Example:** Using a CI service like **GitHub Actions**, **GitLab CI/CD**, **Jenkins**, or **Azure DevOps Pipelines**:

1. **Code Push/PR Creation:** A developer pushes code to a branch or opens a Pull Request.  
2. **CI Pipeline Triggered:** The CI pipeline automatically starts.  
3. **Environment Setup:** The CI environment spins up a clean, consistent environment (e.g., a Docker container with specific Node.js/Python/Java versions, database instances, etc.).  
4. **Dependency Installation:** Project dependencies are installed.  
5. **Test Execution:** All categories of tests are run:  
   * **Unit Tests:** (`npm test`, `pytest`, `mvn test`)  
   * **Integration Tests:** (e.g., tests that interact with a mock database or other services).  
   * **End-to-End (E2E) Tests:** (e.g., Cypress, Playwright, Selenium running against a deployed test environment).  
6. **Build Status Reporting:** The CI service reports the test results back to the PR or commit status. If any test fails, the build status is marked as failed, preventing the PR from being merged.

YAML

```
# Example GitHub Actions workflow snippet for running tests in CI
name: CI Tests
on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - name: Set up Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '20'
    - name: Install dependencies
      run: npm ci
    - name: Run Unit and Integration Tests
      run: npm test # Assumes 'test' script runs all unit/integration tests
    - name: Run E2E Tests (if applicable)
      # This step would typically run against a deployed test environment
      # and might require more complex setup, e.g.,
      # run: npx cypress run --record --key ${{ secrets.CYPRESS_RECORD_KEY }}
      run: echo "E2E tests would run here"
```

# Secure By Default \- Practices

In an increasingly interconnected world, software security is no longer an optional add-on but a fundamental requirement. Neglecting security best practices can lead to data breaches, reputational damage, financial losses, and legal repercussions. Below are some essential security-focused software development best practices, providing examples for each.

**Handle secrets and credentials securely**

"Secrets" refer to sensitive pieces of information like API keys, database passwords, private keys, and access tokens. Hardcoding these directly into source code, configuration files, or version control systems is a major security vulnerability. Secure handling involves storing and accessing secrets in a way that limits exposure and provides robust access control.

**Example:** Instead of:

```py
# BAD PRACTICE: Hardcoded secret
DB_PASSWORD = "my_super_secret_password"
API_KEY = "sk_live_xyz123abc"
```

Use a **Secrets Management Service** or **Environment Variables**:

* **Environment Variables (for simpler deployments/local dev):**  
   Python

```java
# GOOD PRACTICE: Read from environment variable
import os
DB_PASSWORD = os.environ.get("DB_PASSWORD")
API_KEY = os.environ.get("API_KEY")
```

* Secrets are injected into the environment at runtime, never committed to code.  
* **Dedicated Secrets Management Service (for production/complex setups):** Services like **HashiCorp Vault**, **AWS Secrets Manager**, **Google Cloud Secret Manager**, or **Azure Key Vault** allow you to centralize, encrypt, and tightly control access to secrets.  
* Applications retrieve secrets at runtime via secure APIs, often using identity-based access.

**Validate and sanitise user input**

Any data received from an untrusted source (e.g., user input from a web form, API request body, URL parameters) must be validated and sanitized.

* **Validation:** Ensuring the input conforms to expected format, type, length, and range (e.g., an email address is a valid email format, a number is within a specific range).  
* **Sanitization:** Removing or encoding potentially malicious characters or code from the input to prevent injection attacks (e.g., SQL Injection, Cross-Site Scripting \- XSS, Command Injection).

**Example:** A web application accepts user comments.

**BAD PRACTICE (Vulnerable to XSS):**

```javascript
// Node.js Express example
app.post('/comment', (req, res) => {
    const commentText = req.body.comment; // User input directly used
    // ... store commentText in database, then display it
    res.send(`<div>${commentText}</div>`); // User input rendered directly
});
// If user inputs: <script>alert('XSS!')</script>, it executes in other users' browsers.
```

**GOOD PRACTICE (Validation and Sanitization):**

```javascript
// Node.js Express example with validation (e.g., Joi) and sanitization (e.g., DOMPurify for HTML)
const Joi = require('joi');
const DOMPurify = require('dompurify'); // Server-side DOMPurify

const commentSchema = Joi.string().min(5).max(500).required();
app.post('/comment', (req, res) => {
    const { error, value } = commentSchema.validate(req.body.comment);
    if (error) {
        return res.status(400).send({ message: error.details[0].message });
    }
    // Sanitize the input to remove potentially malicious HTML/JS
    const sanitizedComment = DOMPurify.sanitize(value);
    // ... store sanitizedComment in database ...
    // When rendering: ensure proper output encoding/escaping for the context (HTML, JSON, etc.)
    res.send(`<div>${sanitizedComment}</div>`);
});
```

This ensures that only valid input is processed and any potentially harmful content is neutralized before storage or display.

**Avoid hardcoded config or tokens**

Similar to secrets, general application configuration (e.g., database connection strings, service URLs, feature flags, environment settings) should not be hardcoded directly into the application's source code. This practice makes the application less flexible, harder to deploy to different environments (development, staging, production), and increases the risk of accidental exposure of sensitive details.

**Example:** **BAD PRACTICE:**

```java
// Java example
public class AppConfig {
    public static final String API_BASE_URL = "http://dev.api.example.com";
    public static final int MAX_CONNECTIONS = 10;
}
```

**GOOD PRACTICE (Externalized Configuration):** Use configuration files (e.g., `.env` files, YAML, JSON, properties files) or dedicated configuration services.

```java
// Java example using a properties file (application.properties)
// application.properties:
// api.base.url=http://prod.api.example.com
// max.connections=50

// In your Java code:
import java.io.InputStream;
import java.util.Properties;

public class AppConfig {
    private static final Properties props = new Properties();

    static {
        try (InputStream input = AppConfig.class.getClassLoader().getResourceAsStream("application.properties")) {
            props.load(input);
        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }
    public static String getApiBaseUrl() {
        return props.getProperty("api.base.url");
    }
    public static int getMaxConnections() {
        return Integer.parseInt(props.getProperty("max.connections"));
    }
}
```

This allows you to change configurations without recompiling or redeploying the application, simply by providing a different configuration file for each environment.

**Use static security analysis tools**

Static Application Security Testing (SAST) tools analyze source code, bytecode, or binary code to find security vulnerabilities without actually executing the program. They are typically used early in the Software Development Life Cycle (SDLC), often integrated into CI/CD pipelines, to provide rapid feedback to developers.

**Benefits:**

* **Early Detection:** Catches vulnerabilities before they reach testing or production environments, reducing the cost of fixing.  
* **Scalability:** Can scan large codebases quickly and consistently.  
* **Developer Feedback:** Provides actionable insights directly to developers.

**Example:** A development team integrates **SonarQube** (a popular static analysis tool) into their **CI/CD pipeline**.

1. **Developer commits code:** Code is pushed to the version control system.  
2. **CI Pipeline Triggered:** The CI pipeline starts.  
3. **SAST Scan:** A SonarQube scan is executed on the newly committed code.  
4. **Automated Feedback:**  
   * If the scan detects high-severity vulnerabilities (e.g., potential SQL Injection, insecure cryptographic practices, hardcoded credentials), the build might automatically fail.  
   * Developers receive immediate feedback in their IDEs or on the pull request, highlighting the exact lines of code with the vulnerability and suggesting remediation steps.  
5. **Security Gate:** SonarQube's "Quality Gate" can prevent code from being merged into the main branch if it doesn't meet defined security standards.

**Follow OWASP top 10**

The OWASP (Open Web Application Security Project) Top 10 is a widely recognised standard that lists the 10 most critical web application security risks. It serves as a foundational guide for developers and security professionals to understand and mitigate common vulnerabilities. Regularly reviewing and addressing these risks is crucial for building secure applications.

**Example:** Two common OWASP Top 10 items and how to address them:

* **A01:2021 \- Broken Access Control:**  
  * **Vulnerability:** Users can access resources or perform actions they are not authorized to (e.g., a regular user accessing admin functions, or viewing another user's data by changing an ID in the URL).  
  * **Mitigation:** Implement robust server-side access control checks for every request. **Never trust client-side authorization.**  
    * *Example:* Before allowing a user to view an order, the server-side code verifies that the `orderId` belongs to the authenticated user.

```javascript
// Node.js Express example
app.get('/orders/:orderId', authenticateUser, (req, res) => {
    const orderId = req.params.orderId;
    const userId = req.user.id; // Get ID from authenticated user

    // CRITICAL: Check if the order belongs to the current user
    Order.findById(orderId, (err, order) => {
        if (err || !order || order.userId !== userId) {
            return res.status(403).send('Access Denied');
        }
        res.json(order);
    });
});
```

  * 

* **A03:2021 \- Injection (e.g., SQL Injection):**  
  * **Vulnerability:** Untrusted data is sent to an interpreter as part of a command or query, tricking the interpreter into executing unintended commands.  
  * **Mitigation:** Use parameterized queries (prepared statements) or Object-Relational Mappers (ORMs). **Never concatenate user input directly into SQL queries.**  
    * *Example (SQL Injection prevention):*

```java
// Java example using PreparedStatement (JDBC)
String username = request.getParameter("username");
String password = request.getParameter("password");

String sql = "SELECT * FROM users WHERE username = ? AND password = ?";
try (PreparedStatement stmt = connection.prepareStatement(sql)) {
    stmt.setString(1, username);
    stmt.setString(2, password);
    ResultSet rs = stmt.executeQuery();
    // ... process result ...
}
```

  * This ensures that user input is treated as data, not executable code.

**Encrypt data at rest and in transit**

Encryption is crucial for protecting sensitive data from unauthorized access, both when it's being stored (at rest) and when it's being transmitted across networks (in transit).

* **Data at Rest:** Data stored on servers, databases, backups, or storage devices.  
* **Data in Transit:** Data moving between systems, such as client-to-server communication, inter-service communication, or data transfer to third-party APIs.

**Example:**

* **Encrypting Data In Transit:**

  * **Use HTTPS (TLS/SSL):** This is the industry standard for securing communication over the internet. All web applications and APIs should enforce HTTPS.  
    * *Example:* When a user logs into your web app, their username and password are encrypted via TLS as they travel from their browser to your server. Inter-service communication within your cloud environment should also ideally use TLS (e.g., mTLS for microservices).  
  * **VPNs:** For secure access to internal resources or data transfers over public networks.  
  * **SSH:** For secure remote access to servers.  
* **Encrypting Data At Rest:**

  * **Database Encryption:** Most modern databases (e.g., PostgreSQL, MySQL, SQL Server) offer built-in encryption-at-rest capabilities. Cloud database services (e.g., AWS RDS, Google Cloud SQL, Azure SQL Database) provide this by default or as an easy-to-enable option.  
    * *Example:* A database storing customer credit card details is configured with encryption at rest, meaning the data files on the disk are encrypted. Even if someone gains unauthorized access to the raw disk volume, they cannot read the data without the encryption key.  
  * **Disk/Volume Encryption:** Encrypting the entire disk or specific volumes where data is stored.  
  * **Object Storage Encryption:** Cloud storage services (e.g., AWS S3, Google Cloud Storage, Azure Blob Storage) offer server-side encryption for files stored in buckets.  
  * **Application-Level Encryption:** For extremely sensitive data, you might encrypt specific fields within the database using keys managed by your application or a Key Management System (KMS).