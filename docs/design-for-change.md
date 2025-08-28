# Design for Change: Practices

## Build with Change in mind

Adaptable systems are usually not the most complex ones — they're the ones that make it easy to understand, change, and extend functionality without unintended side effects. This shows up in how a codebase is structured, how responsibilities are separated, and how early decisions leave room for evolution. Below are some common patterns that support change over time, drawn from real examples across projects.

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

## Design for impact, not impressiveness

Delivering software that matters means making deliberate choices to maximise value, not chasing novelty, complexity, or shiny solutions for their own sake. Impactful engineering is about understanding the goal, making pragmatic decisions, and delivering robust, maintainable results that solve the real problem.

Clients vary widely in their size, priorities, and constraints. Some need speed and flexibility, while others require longevity and control. Building the most technically sophisticated system is not the goal — solving the right problem, for the right people, at the right scale is. Focusing on value ensures that engineering effort creates real-world outcomes, not just technical artefacts.


<a id="start-with-the-why"></a>

<details>
<summary><h3>Start with the why</h3></summary>

Before jumping to solutions, clarify **why** this problem matters and what success looks like. Avoid building features, integrations, or architectural patterns just because they're technically interesting—focus on user needs, business goals, and measurable outcomes.

How to do it:

* Kick off with user and stakeholder interviews to uncover pain points and define outcomes.  
* Write lightweight briefs or one-pagers: “This matters because… Success means…”  
* Regularly ask, “How will we know if this is working?” and “What problem are we really solving?”

**Example:** Instead of statements like:

“Let’s build an event-driven system because it’s modern.” 

**Anti-pattern:**  
Shipping features because they’re technically cool, or have been in a YouTube video somebody watched recently, not because they drive measurable value.

**Reasoning**:  
Clarify the business outcome first: “We need orders to sync to finance within 1 hour—how simply can we achieve that?”

</details>

<a id="right-sized-solution-(infrastructure-&-tech-choices)"></a>

<details>
<summary><h3>Right-sized solution (Infrastructure & tech choices)</h3></summary>

Match the solution's complexity, architecture, and technology stack to the actual scale and requirements of the problem, both current and near-future. Avoid building for hypothetical scenarios that are unlikely to materialise or add undue burden.

**How to do it:**

* Start with simple, battle-hardened patterns (e.g., monoliths, managed services, cloud platforms).  
* Only introduce complexity (microservices, custom infra) when there’s a proven need.  
* Regularly ask: “Is this the simplest thing that can possibly work?”

**Example:** Instead of statements like:

*“We’ll build it properly from the start: Docker, Kubernetes, managed Postgres, microservices split by domain, S3 for assets, full CI/CD, multi-account AWS setup, all running in the cloud. It’ll be ready for scale, even if we only have a handful of users and a single developer.”*

**Anti-pattern:**  
**Anticipating the future and dealing with problems that haven't arisen yet.** 

**Reasoning**:   
*“Let’s deploy a single Next.js app (or similar), use a managed database, and focus on the core features users need. Once we have traction or learn where scaling is needed, we can iterate.”*

**Signs of Impact-Driven Choices:**

* First deployment is live in days, not weeks.  
* Minimal ops overhead—anyone on the team can maintain it.  
* Easy to adapt or scale as real needs become clear.

</details>

<a id="prioritise-client-value"></a>

<details>
<summary><h3>Prioritise client value</h3></summary>

**How to do it:**

* Link every choice and decision to a business or user-based outcome.  
* Use effort vs. impact matrices to focus on what matters most.  
* Be willing to say “not now” to low-impact, high-effort ideas. Usually anything that starts with an Engineer saying, “We are going to need to refactor this at some point, let’s do it now”  
* Don't be scared to have a Future Problems log. Some of these may be genuine concerns and may need addressing, but adding them to a backlog and waiting till they are a significant issue is not a bad thing.

</details>

<a id="get-the-razor-out-(strive-for-simplicity)"></a>

<details>
<summary><h3>Get the Razor Out (Strive for Simplicity)</h3></summary>

Favour the simplest solution that works. With every decision and technical choice, ask if there’s a way to reduce complexity rather than add it. Avoid cleverness for its own sake — simple code and systems are easier to build, test, and change later.

**Example:** Instead of statements like:

“We have added Material UI as we needed it for components. We’ve used Dexie for local storage and are now managing state with Redux. We also need to atomise every component and follow S.O.L.I.D principles of design” 

**Anti-pattern:**  
**While these are probably solutions that may be needed at some point, starting with them before fully understanding the project could be a massive time suck and result in an over engineered solution.** 

**Reasoning**:   
*“Let’s start as simply as possible. A basic React App with Vite and Tailwind for some basic styling. We can introduce other elements only when we really have to”*

**Signs of good simplification Choices:**

* The codebase is highly readable, allowing new team members to understand and contribute quickly.  
* Frontend solutions remain lean and performant, with minimal and justified dependencies.  
* Modifications are typically localised, making debugging faster and reducing unintended side effects.  
* Developers spend less time fighting complexity and more time delivering user value.

</details>