# Engineering Principles

This is a practical guide to what good engineering looks like at CreateFuture. It sets out a shared understanding of quality across all teams, projects, and levels of experience — something we can all refer to, build on, and hold each other to.
Whether you're writing code, reviewing it, or shaping a solution for a client, these principles help us stay consistent, intentional, and collaborative — without being rigid or prescriptive.
They aren’t rules for the ‘best’ system — they’re guidance for making balanced trade-offs that fit each client’s context and deliver value for their users.”

## How to Use This Framework

Each principle is presented in a simple, layered format. You’ll see a one-line summary to get the core idea fast. You can expand it to explore why it matters and what it means in practice — or go deeper into real-world examples on the dedicated Practice page.
You’ll also find links to any relevant tools, community guidelines, or standards if they exist — but they’re always optional and contextual.

## What the Terms Mean

**Principles** are the core concepts that define good engineering at CreateFuture. They apply to every team and every project, regardless of technology or experience level. Principles explain *why* we do things a certain way — they don’t change often.

**Practices** show how those principles are applied in real work. They include examples, behaviours, and patterns that help you understand what the principle looks like in action.

**Community guidelines and standards** are specific tools, templates, or rules used by different teams or language groups. They vary depending on the client or tech stack — things like naming conventions, folder structures, or linting rules.

## How it all fits together

Projects vary. Clients use different tools, standards, and ways of working — but the core principles of good engineering stay the same. Principles provide the foundation: they guide how we build, and how we work with clients — helping us know when to adapt, when to flag issues, and when to challenge decisions. Practices show what those principles look like in everyday work. Community guidelines and standards are created by our engineering communities to share knowledge, establish strong defaults, and give people confidence — especially when starting something new or advising clients. They help us stay up to date, make consistent decisions, and bring best practice into real-world situations — always applied with client context in mind.

---

## Design for Change
_Covers: Architecture and planning, with client and future context in mind._

<details>
<summary><h3>Build with change in mind</h3></summary>

Good systems evolve. Prioritise flexibility over premature optimisation, and design with the assumption that requirements will shift. Choose patterns and architectures that are easy to understand and adapt, even if it means starting simpler — or consciously cutting corners now, with a clear plan for when and how to revisit later.. This means delivering value fast — but leaving room to grow without rewriting everything later.

**Why it matters**

Different clients have different levels of technical maturity, shifting goals, and changing constraints. Prioritising adaptability — in people and in systems — makes it easier to respond, scale, and support long-term outcomes without waste.

**Practices**
- [Modularise where it makes sense](docs/design-for-change.md)
- [Separation of concerns](docs/design-for-change.md)
- [Composition over inheritance](/docs/design-for-change.md)
- [DRY](docs/design-for-change.md)
- [Clean-up early (or agree on what debt you’ll consciously carry)](docs/design-for-change.md)
- [Design for iteration, not premature componentisation](docs/design-for-change.md)

</details>

<details>
<summary><h3>Design for impact, not impressiveness</h3></summary>

Design decisions should always be driven by the value they deliver — not by novelty, scale, or complexity for its own sake. A well-designed solution is one that solves the right problem, fits the client's context, and supports the real needs of users. This means making intentional trade-offs: avoiding unnecessary scalability, overengineering, or technical abstraction when it's not needed.

**Why it matters**

Clients vary widely in their size, priorities, and constraints. Some need speed and flexibility; others need longevity and control. Building the most technically sophisticated system is not the goal — solving the right problem, for the right people, at the right scale is. Focusing on value ensures that engineering effort creates real-world outcomes, not just technical artefacts.

**Practices**
- [Start with the why](docs/design-for-change.md)
- [Right-size the solution](docs/design-for-change.md)
- [Prioritise client value](docs/design-for-change.md)
- [Focus on user needs](docs/design-for-change.md)
- [Include accessibility and inclusivity](docs/design-for-change.md)
- [Challenge over-engineering](docs/design-for-change.md)

</details>

---

## Code for Clarity
_Covers: Implementation choices, readability, simplicity, communication._

<details>
<summary><h3>Let the code do the talking</h3></summary>

Code, commits, and pull requests should communicate intent clearly without needing extra explanation. Clear naming, focused functions, meaningful commit messages, and well-structured PRs all contribute to code that’s easy to read, understand, and maintain — even for someone joining the project fresh. Code comments should be used sparingly, and only to provide information that you’re unable to ariculate with the code itself. Think about the next engineer: from the code alone, can they get all the infromation they need to be able to work in that area? How easy is it for them to surface this, and how useful would they find it?

**Why it matters**

Readable code saves time, reduces onboarding friction, and helps teams collaborate more effectively. It also prevents misunderstandings that can lead to bugs. Good naming and structure don’t just help engineers now — they also help those who come after.

**Practices**
- [Favour consistency](docs/code-for-clarity.md)
- [Small, purposeful functions](docs/code-for-clarity.md)
- [Self-explaining pull requests](docs/code-for-clarity.md)
- [Descriptive commit messages](docs/code-for-clarity.md)
- [Readable, minimal diff noise](docs/code-for-clarity.md)
- [Comments should un-block](docs/code-for-clarity.md)

</details>

<details>
<summary><h3>Automate the boring stuff</h3></summary>

Repetitive, error-prone tasks should be automated wherever possible. This includes formatting, tests, builds, and checks — anything that can be made consistent and efficient through tooling. Automation enforces quality without requiring constant attention.

**Why it matters**

Manual steps are fragile, inconsistent, and time-consuming. Automation helps teams move faster with less risk, freeing up time for problem-solving and reducing friction in delivery.

**Practices**
- [Use linters and formatters](docs/code-for-clarity.md)
- [Automate test runs](docs/code-for-clarity.md)
- [Enforce commit standards](docs/code-for-clarity.md)
- [Run static analysis in CI](docs/code-for-clarity.md)
- [Script setup tasks and deployments](docs/code-for-clarity.md)
</details>

---

## Build Confidence
_Covers: Quality, testing, and security hardening that builds trust._

<details>
<summary><h3>Test with purpose</h3></summary>

Tests should validate behaviour, not just pass checks. Good testing covers the right paths, edge cases, and system behaviours in a way that supports fast feedback and safe iteration. It’s not about hitting 100% coverage — it’s about writing tests that matter.

**Why it matters**



**Practices**
- [Test for behaviour, not just implementation](docs/build-confidence.md)
- [Write unit tests with clear intent](docs/build-confidence.md)
- [Use integration tests to cover real system flows](docs/build-confidence.md)
- [Avoid fragile or flaky tests](docs/build-confidence.md)
- [Tests fail when they should](docs/build-confidence.md)
- [Align with the testing pyramid](docs/build-confidence.md)

</details>

<details>
<summary><h3>Catch issues early</h3></summary>

Quality should be built in from the start — not added at the end. Surface problems as early as possible using linting, static checks, pre-commit hooks, review processes, and CI. The earlier an issue is caught, the less time and effort it takes to resolve.

**Why it matters**



**Practices**
- [Use pre-commit hooks for quick checks](docs/build-confidence.md#catch-issues-early-use-pre-commit-hooks-for-quick-checks)
- [Run linting and formatting automatically](docs/build-confidence.md#catch-issues-early-run-linting-and-formatting-automatically)
- [Set up type checking and static analysis](docs/build-confidence.md#catch-issues-early-set-up-type-checking-and-static-analysis)
- [Define clear PR review standards](docs/build-confidence.md#catch-issues-early-define-clear-pr-review-standards)
- [Run tests in CI, not just locally](docs/build-confidence.md#catch-issues-early-run-tests-in-ci-not-just-locally)

</details>

<details>
<summary><h3>Secure by default</h3></summary>

Security should be built into the system from the start — not bolted on later. Default to secure patterns, handle sensitive data with care, and use the tools and checks that help prevent common vulnerabilities.

**Why it matters**



**Practices**
- [Handle secrets and credentials securely](docs/build-confidence.md#secure-by-default-handle-secrets-and-credentials-securely)
- [Validate and sanitise user input](docs/build-confidence.md#secure-by-default-validate-and-sanitise-user-input)
- [Avoid hardcoded config or tokens](docs/build-confidence.md#secure-by-default-avoid-hardcoded-config-or-tokens)
- [Use static security analysis tools](docs/build-confidence.md#secure-by-default-use-static-security-analysis-tools)
- [Follow OWASP top 10](docs/build-confidence.md#secure-by-default-follow-owasp-top-10)
- [Encrypt data at rest and in transit](docs/build-confidence.md#secure-by-default-encrypt-data-at-rest-and-in-transit)

</details>

---

## Ship Responsibly
_Covers: Deployment, rollback, operability, and incident response._

<details>
<summary><h3>Ship safely, roll back quickly</h3></summary>

Releasing software should be predictable and low-risk. Whether deploying every day or once a month, teams should aim for automation, visibility, and reversibility. Good deployment pipelines catch issues before they hit users and allow quick rollback when needed.

**Why it matters**



**Practices**
- [Use CI/CD pipelines with automated tests and checks](docs/ship-responsibly.md)
- [Deploy small changes regularly](docs/ship-responsibly.md)
- [Use feature flags or toggles to control exposure](docs/ship-responsibly.md)
- [Automate rollback where possible](docs/ship-responsibly.md)
- [Monitor deployments for performance and errors](docs/ship-responsibly.md)

</details>

<details>
<summary><h3>Fail loud, fail safe</h3></summary>

When systems break — and they will — failure should be obvious and contained. Build in clear logging, monitoring, and alerting to detect issues quickly, and use design patterns that localise failure without cascading effects.

**Why it matters**



**Practices**
- [Add logging and monitoring from the start](docs/ship-responsibly.md)
- [Use structured, actionable error messages](docs/ship-responsibly.md)
- [Create alerts for key health indicators](docs/ship-responsibly.md)
- [Design services to fail independently](docs/ship-responsibly.md)
- [Use circuit breakers and retry logic](docs/ship-responsibly.md)

</details>

---
