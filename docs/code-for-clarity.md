# Code for Clarity : Practices

## Let the code do the talking

"Let the Code Do The Talking" means that our code, commit history, and pull requests should be so clear that they require minimal additional explanation to be understood. This clarity is vital for efficient collaboration, easier onboarding, and long-term maintainability. When code communicates its intent effectively, it reduces misunderstandings, speeds up reviews, and helps everyone on the project, including future engineers.
Below are some common practices that help ensure our code speaks for itself, drawn from real-world project experiences.


<a id="favour-consistency"></a>

<details>
<summary><h3>Favour consistency</h3></summary>

A consistent approach to naming, architecture, error handling etc. can help remove ambiguity when developing applications. When things are consistent, it’s much harder to be caught off guard with a certain way things have been implemented. This doesn’t only help with maintaining the application; it can help to provide a clear structure to implement new features as well.
It’s not unheard for a client to enforce a particular way of working that isn’t deemed optimal. This can be for many reasons, such as the client having their own more specific engineering principles they must enforce. Though we should be communicating these defficiencies, in these scenarios we should avoid implementing what we deem to be best practice if it were to introduce a different way of doing things.
Variable, function, class, and module names should clearly convey their purpose and how they are used. Adhering to a consistent naming strategy across a project or even a language community significantly aids readability.
Examples
Poor vs. Good Naming (Variables)
```js
let d; // What is 'd'? Days? Data?
// After
let elapsedTimeInDays;
let userData;
```
Function Naming
```js
function handle(data) { /* ... */ } // Vague
// After
function processPayment(paymentDetails) { /* ... */ }
```
```js
class Employee {
function deleteEmployee() { /* ... */ } // Unecessarily verbose
}
// After
class Employee {
function delete() { /* ... */ }
}
```
Consistent API Endpoint Naming (similar to service-based backend layout)
/users/{id}
/orders/{id}
/products/{id}
Note: Use a kebab-cased naming convention to help with readability due to URLs being case insensitive.

</details>

<a id="small-purposeful-functions"></a>

<details>
<summary><h3>Small, purposeful functions</h3></summary>

Functions and methods should be concise and focused on a single responsibility. Smaller functions are easier to understand, test, debug, and reuse.
Examples
Refactoring a large function (similar to splitting large methods)
```js
function handleSubmission(formData) {
// validation logic...
// data transformation logic...
// API call logic...
// notification logic...
}
// After
function handleSubmission(formData) {
const validatedData = validateForm(formData);
const payload = prepareApiPayload(validatedData);
const result = submitToApi(payload);
notifyUser(result);
}
```

</details>

<a id="self-explaining-pull-requests"></a>

<details>
<summary><h3>Self-explaining pull requests</h3></summary>

Pull Request (PR) descriptions should provide context, explain the "why" behind the changes, detail the "how" if complex, and guide reviewers on what to look for or how to test.
Examples
PR Description Template
```js
**Problem:**
A brief explanation of the issue being addressed.
**Solution:**
A clear description of the changes made.
- Key decision 1 and why.
- Key decision 2 and why.
**How to Test:**
- Step 1: ...
- Step 2: ...
- Expected outcome: ...
**Screenshots/GIFs (if applicable):**
```

</details>

<a id="descriptive-commit-messages"></a>

<details>
<summary><h3>Descriptive commit messages</h3></summary>

Commit messages form a historical log of the project's evolution. They should be concise yet informative, clearly stating what changed and why. Adopting a convention (e.g., Conventional Commits) can make history more navigable and automate changelog generation.
Examples
Poor vs. Good Commit Messages (similar to renaming for clarity)
```js
git commit -m "Fixed stuff"
git commit -m "WIP"
// After (Conventional Commit Style)
git commit -m "fix: Correct user login redirect issue on SSO failure"
git commit -m "feat: Add dark mode toggle to user preferences"
git commit -m "docs: Update README with setup instructions for new service"
```

</details>

<a id="readable-minimal-diff-noise"></a>

<details>
<summary><h3>Readable, minimal diff noise</h3></summary>

Code changes submitted for review should be focused. Avoid mixing unrelated changes (e.g., refactoring, feature work, and style fixes) in a single commit or PR, as this makes reviews harder and introduces noise.
Examples
Separate concerns in commits
```js
git commit -m "refactor: Extract OrderValidationService from OrderService"
// Commit 2: Feature
git commit -m "feature: Implement discount code functionality in checkout"
// Commit 3: Chore (e.g. after running a linter on changed files)
git commit -m "chore: Apply linting rules to order module"
```

</details>

<a id="comments-should-un-block"></a>

<details>
<summary><h3>Comments should un-block</h3></summary>

Comments should add extra context that can’t be obtained from the code itself. Anything else is unnecessary noise around otherwise clear and concise code.
When added, they should explain the “why” behind the code, rather than the “what”, as the “what” should be implicit from reading the code itself. Ask yourself “how is this comment going to help the reader work in this area?”.
Comments tend to be especially prevalent in AI-generated code, so be militant as always when checking the output, or tailor prompts to protect against this.
TODOs should only be left in code with details on when this will be resolved, ideally containing a reference to a ticket.
Examples

</details>

## Automate the Boring Stuff 

"Automate the Boring Stuff" means identifying repetitive, manual, and error-prone tasks within our engineering workflows and automating them. This includes activities like code formatting, running tests, performing builds, and deploying applications. Effective automation enhances quality, consistency, and speed, freeing up engineers to concentrate on complex problem-solving and innovation rather than routine chores.

Below are common practices for automation that help streamline development and reduce friction.

<a id="use-linters-and-formatters"></a>
<details>
<summary><h3>Use linters and formatters</h3></summary>

Linters automatically check code for stylistic errors, potential bugs, and adherence to coding standards. Formatters automatically apply consistent styling. Integrating these tools into the development workflow ensures consistency and catches issues early. 

**Examples**

Automated formatting on save/commit

```json
// Example: Using a pre-commit hook with Husky and Prettier
// package.json
"husky": {
  "hooks": {
    "pre-commit": "lint-staged"
  }
},
"lint-staged": {
  "*.{js,jsx,ts,tsx,json,css,scss,md}": "prettier --write"
}
```

IDE Integration for real-time feedback.

</details>

<a id="automate-test-runs"></a>
<details>
<summary><h3>Automate test runs</h3></summary>

All types of tests (unit, integration, end-to-end) should be automated and run frequently, typically as part of a Continuous Integration (CI) pipeline. This provides fast feedback on changes and helps catch regressions before they reach production. 

**Examples**

CI Pipeline Trigger (similar to feature flags for rollout logic)

```yaml
# .github/workflows/ci.yml
name: CI Pipeline
on: [push, pull_request]
jobs:
  build-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: npm install
      - run: npm run lint
      - run: npm test -- --coverage # Assuming test script generates coverage
      - run: npm run build
```

This ensures tests and linting are run for every push and pull request.

</details>

<a id="enforce-commit-standards"></a>
<details>
<summary><h3>Enforce commit standards</h3></summary>

Automate the enforcement of commit message conventions (e.g., Conventional Commits) using tools like `commitlint`. This improves the clarity and usefulness of commit history and enables automated generation of changelogs. 

**Examples**

Commitlint with Husky

```json
// package.json
"husky": {
  "hooks": {
    "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
  }
}

// commitlint.config.js
module.exports = {extends: ['@commitlint/config-conventional']};
```

If a commit message doesn't meet the standard, the commit is rejected.

</details>

<a id="run-static-analysis-in-ci"></a>
<details>
<summary><h3>Run static analysis in CI</h3></summary>

Integrate static analysis security testing (SAST) and code quality tools (e.g., SonarQube, Veracode, CodeQL) into the CI pipeline. These tools can automatically detect potential bugs, security vulnerabilities, and code smells without executing the code.

**Examples**

SonarQube scan in CI

```yaml
# Azure DevOps Pipeline example
steps:
- task: SonarQubePrepare@5
  inputs:
    SonarQube: 'SonarQube Service Connection'
    scannerMode: 'CLI'
    configMode: 'manual'
    cliProjectKey: 'my-project-key'
    cliSources: '.'
- task: Maven@3 # or Npm@1, Gradle@2 etc. for build
  inputs:
    # build inputs for your project
- task: SonarQubeAnalyze@5
- task: SonarQubePublish@5
  inputs:
    pollingTimeoutSec: '300'
```

Pull Requests can be blocked or flagged if new critical issues are detected.

</details>

<a id="script-setup-tasks-and-deployments"></a>
<details>
<summary><h3>Script setup tasks and deployments</h3></summary>

Automate the setup of development environments, build processes, and application deployments using scripts and CI/CD tools. This reduces manual effort, ensures consistency, and makes processes repeatable and reliable.

**Examples**

Development Environment Setup using Docker

```yaml
# Using Docker Compose
# docker-compose.yml
version: '3.8'
services:
  web:
    build: . # Assumes a Dockerfile in the current directory
    ports:
      - "8000:8000" # Maps port 8000 on host to 8000 on container
  db:
    image: postgres:15 # Uses official PostgreSQL image
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
# Command to run: docker-compose up -d
```

Automated Deployment Pipeline (example for GitHub Actions deploying to a generic staging)

```yaml
# .github/workflows/deploy.yml
name: Deploy to Staging
on:
  push:
    branches:
      - develop # Trigger deployment on push to develop branch
jobs:
  deploy-staging:
    # needs: build-and-test # Assumes a 'build-and-test' job exists
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      # - name: Download build artifact # If build artifact is produced in 'build-and-test' job
      #   uses: actions/download-artifact@v3
      #   with:
      #     name: my-app-build
      - name: Deploy to Staging Environment
        # This step is highly dependent on your cloud provider and setup
        # e.g., using actions/aws/cli, google-github-actions/deploy-cloud-run, azure/webapps-deploy
        run: echo "Deploying to staging..." # Placeholder for actual deployment commands
        # Example: aws s3 sync ./dist s3://my-staging-bucket --delete
```

</details>