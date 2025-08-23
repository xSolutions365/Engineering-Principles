# Design for Change: Practices

## Build with Change in mind

<a id="practice-modular-structure"></a>
<details>
<summary><h3>Modular structure</h3></summary>

A modular system is organised into components or services that each focus on a single area. This limits the surface area of change and allows functionality to evolve independently.

**Examples**

**Service-based backend layout**
```
/services
  /users
  /orders
  /payments
```
Each service contains only what it needs — logic, validation, DB queries — and exposes a clear API to other parts of the system.

**Frontend feature folders**
```
/features
  /profile
  /checkout
  /dashboard
```
Related files (components, tests, APIs, styles) are grouped together to improve cohesion and reduce cross-dependencies.
</details>

<a id="practice-separation-of-concerns"></a>
<details>
<summary><h3>Separation of concerns</h3></summary>

Responsibilities such as business logic, data access, and presentation are kept distinct. Each layer or component has a focused role.

**Examples**

**Service layer in Node**
```javascript
function createOrder(data) {
  validate(data);
  saveToDB(data);
}
```

**React component delegates to logic**
```jsx
// Component only renders
<OrderSummary order={order} />
// Logic handled elsewhere
const order = orderService.getSummary(cart);
```
</details>

<a id="practice-composition-over-inheritance"></a>
<details>
<summary><h3>Composition over inheritance</h3></summary>

Complex inheritance chains make change harder. Composition — combining small, purpose-specific functions or components — keeps things more flexible.

**Examples**

**Middleware stacking**
```javascript
app.use(authenticate);
app.use(limitRequests);
app.use(logUsage);
```

**Frontend example**
```jsx
useForm();
useTheme();
useResponsiveLayout();
```
</details>

<a id="practice-dry"></a>
<details>
<summary><h3>DRY</h3></summary>

Where code does the same thing in multiple places, extract it into a shared function, utility, or module. DRY code improves maintainability, reduces bugs, and makes systems easier to evolve.

**Examples**

**Shared logic extraction**
```javascript
// Before
const fullName1 = `${user.firstName} ${user.lastName}`;
const fullName2 = `${user.firstName} ${user.lastName}`;

// After
const getFullName = (user) => `${user.firstName} ${user.lastName}`;
```

**Reusing UI components**
```jsx
// Repetition:
<SaveButton label="Save" />
<SubmitButton label="Submit" />

// DRY'd out:
<Button type="primary" label="Save" />
<Button type="submit" label="Save" />
```

**Note**  
Some duplication is fine early on. It's better to wait for patterns to emerge before creating abstractions that might not fit future needs. DRY doesn’t mean extract everything — it means recognise when something is becoming a pattern. The ‘Rule of three’ can be used as a guide to protect against abstracting too early.
</details>

<a id="practice-clean-up-early"></a>
<details>
<summary><h3>Clean-up Early</h3></summary>

Clean-up is most effective when done while the work is still fresh. Small refactors after delivery prevent complexity from accumulating unnoticed.

**Examples**

**Rename after understanding evolves**
```diff
- const handle = ...
+ const generateMonthlyInvoice = ...
```

**Split large methods**
```javascript
function submitForm(data) {
  const validated = validate(data);
  const payload = formatPayload(validated);
  api.submit(payload);
}
```
</details>

<a id="practice-design-for-iteration"></a>
<details>
<summary><h3>Design for iteration</h3></summary>

Projects evolve. Designs that support small, reversible changes allow for faster progress and fewer large rewrites.

**Examples**

**Feature flags for rollout**
```javascript
if (features.isEnabled('newFlow')) {
  runNew();
} else {
  runLegacy();
}
```

**Independent deployments**
- Services deployed separately
- Schema changes made backwards-compatible
- Feature toggles used instead of long-lived branches
</details>
