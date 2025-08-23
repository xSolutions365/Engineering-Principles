# Design for Change: Practices

## Build with change in mind

### Modular structure
<details>
<summary>View guidance & examples</summary>

A modular system is organised into components or services that each focus on a single area. This limits the surface area of change and allows functionality to evolve independently.

**Examples**

**Service‑based backend layout**
```
/services
  /users
  /orders
  /payments
```
Each service contains only what it needs (logic, validation, DB queries) and exposes a clear API.

**Frontend feature folders**
```
/features
  /profile
  /checkout
  /dashboard
```
Group related files (components, tests, APIs, styles) to improve cohesion and reduce cross‑dependencies.
</details>

### Separation of concerns
<details>
<summary>View guidance & examples</summary>

Keep responsibilities like business logic, data access, and presentation distinct. Each layer/component has a focused role.

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

### Composition over inheritance
<details>
<summary>View guidance & examples</summary>

Prefer composing small, purpose‑specific pieces over deep inheritance chains.

**Examples**

**Express middleware stacking**
```javascript
app.use(authenticate);
app.use(limitRequests);
app.use(logUsage);
```

**React hooks composition**
```jsx
function Checkout() {
  const form = useForm();
  const theme = useTheme();
  const layout = useResponsiveLayout();
  // ...
}
```
</details>

### DRY — but time it
<details>
<summary>View guidance & examples</summary>

Reduce duplication where it’s clearly the **same behaviour**. Early on, a little duplication is fine; extract when patterns emerge (the “rule of three” is a good guardrail).

**Examples**

**Shared logic extraction**
```javascript
// Before
const fullName1 = `${u.firstName} ${u.lastName}`;
const fullName2 = `${u.firstName} ${u.lastName}`;

// After
const getFullName = (u) => `${u.firstName} ${u.lastName}`;
```

**UI reuse**
```jsx
// Repetition
<PrimaryButton label="Save" />
<PrimaryButton label="Submit" />

// DRY'd
<Button type="primary" label="Save" />
```
</details>

### Clean‑up early
<details>
<summary>View guidance & examples</summary>

Small refactors close to delivery prevent complexity from building up.

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
  const payload   = formatPayload(validated);
  api.submit(payload);
}
```
</details>

### Design for iteration (avoid premature componentisation)
<details>
<summary>View guidance & examples</summary>

Favour simple code (even duplicated) while exploring. Extract components once patterns are real or change pressure justifies it. Make changes small and reversible.

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
- Services deploy separately  
- Backwards‑compatible schema changes  
- Toggle new paths instead of long‑lived branches

**Back‑out plans in ADRs**
- How we’d revert  
- Cost to change later  
- Deprecation path if we keep it
</details>

---

## Design for impact, not impressiveness

### Start with the why
<details>
<summary>View guidance & examples</summary>

Anchor decisions to the problem and the outcome we want for users and the client.

**Do this**
- Write a one‑liner problem statement and success signal (e.g. “Reduce checkout drop‑off from 42% → 30%”).
- Capture constraints (time/budget/risk appetite).  
- Identify the *minimum* behaviour or metric we need to see in the next 2–4 weeks.

**Example**
```text
Problem: Users abandon on payment step.
Outcome: +12% successful payments (30 days).
Constraint: 1 sprint, no gateway migration.
Approach: Inline errors, save cardholder name, retry flow.
Measure: Success rate, time‑to-complete, error types.
```
</details>

### Right‑size the solution
<details>
<summary>View guidance & examples</summary>

Choose the lightest approach that works now; increase investment when risk or scale demands it.

**Examples**
- **Prototype (≤12 weeks):** Monolith, minimal infra, smoke + 1–2 critical E2E tests, basic logging.
- **Product‑market fit:** Modular monolith/small services, unit + contract + critical E2E, blue/green or canary.
- **Regulated/scale:** Clear domain boundaries, full test pyramid incl. perf & security, SLOs and automated rollback.

**Rule of thumb**
- If a cheaper/simpler option gets ~80% of the value, prefer it and **name what you’ll revisit**.
</details>

### Prioritise client value
<details>
<summary>View guidance & examples</summary>

Favour choices that the client can run, staff, and afford.

**Consider**
- Time‑to‑first‑value vs. long‑term ROI
- Total cost of ownership (licenses, cloud, ops, people)
- Hiring/onboarding reality for chosen tech
- Vendor lock‑in and exit paths

**Example**
- Prefer a managed database your client’s team already knows over a trendy, niche alternative with higher ops burden.
</details>

### Focus on user needs
<details>
<summary>View guidance & examples</summary>

Start from user jobs and real behaviour.

**Do this**
- Map the happy path + 1 painful edge case.
- Instrument key steps (events/telemetry) before optimising.
- Validate with a tiny slice (feature flag, % rollout), then expand.

**Example**
```text
Observed: 24% of users fail card entry on mobile.
Change: Move expiry/CVV to separate fields; add card type hint.
Measure: Error rate by field, completion time.
```
</details>

### Include accessibility and inclusivity
<details>
<summary>View guidance & examples</summary>

Accessibility is part of “done”. Ship inclusive defaults and test like a real user.

**Do this**
- Use semantic HTML and proper labels/roles.
- Keyboard and screen‑reader basic pass (focus order, visible focus).
- Colour contrast and motion preferences respected.
- Localisation basics where relevant (dates, numbers, copy length).

**Snippet**
```html
<label for="email">Email address</label>
<input id="email" name="email" type="email" autocomplete="email" />
```
</details>

### Challenge over‑engineering
<details>
<summary>View guidance & examples</summary>

Avoid building for hypothetical scale or edge cases.

**Checks**
- Would we regret not doing this in the next quarter?
- What breaks if we ship the simpler version now?
- Can we make this easy to remove/replace later?

**Example**
- Use a scheduled job + queue for weekly reports before introducing a full event bus; add the bus only if jobs back up or cross‑team integration is proven.
</details>
