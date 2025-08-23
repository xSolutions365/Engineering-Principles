## Let the code do the talking

<a id="practice-let-the-code-do-the-talking-favour-consistency"></a>

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

<a id="practice-let-the-code-do-the-talking-small-purposeful-functions"></a>

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

<a id="practice-let-the-code-do-the-talking-self-explaining-pull-requests"></a>

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

<a id="practice-let-the-code-do-the-talking-descriptive-commit-messages"></a>

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

<a id="practice-let-the-code-do-the-talking-readable-minimal-diff-noise"></a>

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

<a id="practice-let-the-code-do-the-talking-comments-should-un-block"></a>

<details>
<summary><h3>Comments should un-block</h3></summary>

Comments should add extra context that can’t be obtained from the code itself. Anything else is unnecessary noise around otherwise clear and concise code.
When added, they should explain the “why” behind the code, rather than the “what”, as the “what” should be implicit from reading the code itself. Ask yourself “how is this comment going to help the reader work in this area?”.
Comments tend to be especially prevalent in AI-generated code, so be militant as always when checking the output, or tailor prompts to protect against this.
TODOs should only be left in code with details on when this will be resolved, ideally containing a reference to a ticket.
Examples

</details>
