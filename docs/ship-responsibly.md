# Ship Responsibly – Practices

## Ship Safely, Roll Back Quickly 

<a id="use-ci/cd-pipelines-with-automated-tests-and-checks"></a>

<details>
<summary><h3>Use CI/CD pipelines with automated tests and checks</h3></summary>

A Continuous Integration/Continuous Delivery (CI/CD) pipeline automates the steps from code commit to deployment. This automation includes compiling code, running tests, performing quality checks, and preparing the software for release. The core idea is to integrate code changes frequently and automatically verify their correctness and quality.

* **Automated Tests**: These include:  
  * **Unit Tests**: Verify individual components or functions in isolation.  
  * **Integration Tests:** Verify interactions between different components or services.  
  * **End-to-End (E2E) Tests**: Simulate real user scenarios to ensure the entire system works as expected.  
* **Automated Checks**: These go beyond functional testing and include:  
  * **Linting/Static Code Analysis**: Enforce coding standards and identify potential bugs or security vulnerabilities without running the code.  
  * **Security Scans**: Automatically check for known vulnerabilities in dependencies or code.  
  * **Code Quality Metrics**: Track metrics like code complexity, test coverage, and duplication.

Example using GitHub Actions for CI/CD pipelines.

1. **Developer commits code:** A developer pushes changes to a feature branch.  
2. **CI Trigger**: GitHub Actions detects the push and triggers a workflow.  
3. **Build:** The code is compiled and dependencies are installed.  
4. **Automated Tests**:  
   * Unit tests run immediately (e.g., using Jest for JavaScript).  
   * Integration tests run against a test database (e.g., using Cypress for a web app's API interactions).  
5. **Automated Checks:**  
   * ESLint runs to check code style and potential errors.  
   * OWASP Dependency-Check scans for known vulnerabilities in third-party libraries.  
6. **Deployment to Staging:** If all tests and checks pass, the pipeline automatically deploys the build to a staging environment.  
7. **E2E Tests on Staging:** Automated E2E tests (e.g., using Selenium or Playwright) run against the deployed staging environment.  
8. **Manual QA/Review:** After E2E tests pass, a human QA or product owner performs a final review.  
9. **Deployment to Production:** Upon approval, the pipeline can be manually or automatically triggered to deploy to production.

</details>

<a id="deploy-small-changes-regularly"></a>

<details>
<summary><h3>Deploy small changes regularly</h3></summary>

Instead of accumulating a large number of features and bug fixes for a massive, infrequent "big bang" release, this practice advocates for deploying smaller, incremental changes more frequently.

Benefits:

* **Reduced Risk:** Smaller changes are easier to test, understand, and debug. If an issue arises, it's simpler to pinpoint the cause.  
* **Faster Feedback:** Users and stakeholders get new features and fixes sooner, leading to quicker feedback cycles.  
* **Easier Debugging and Rollback:** With fewer changes in each deployment, identifying the source of a bug is faster, and rolling back to a previous stable version is less disruptive.  
* **Improved Team Morale:** Developers see their work deployed faster, leading to a sense of accomplishment.

Example: A new e-commerce website needs to add a "wishlist" feature. Instead of building the entire feature (add to wishlist, view wishlist, share wishlist, notifications) and releasing it at once, the team breaks it down:

* **Deployment 1:** Implement "Add to Wishlist" button functionality, storing items in the user's profile database.  
* **Deployment 2:** Create a basic "View My Wishlist" page showing added items.  
* **Deployment 3:** Add "Remove from Wishlist" functionality.  
* **Deployment 4:** Implement "Share Wishlist" via email.

Each deployment is small, tested, and released independently, providing value incrementally and reducing the risk of a large failure.

</details>

<a id="use-feature-flags-or-toggles-to-control-exposure"></a>

<details>
<summary><h3>Use feature flags or toggles to control exposure</h3></summary>

Feature flags (also known as feature toggles or feature switches) are software development techniques that allow you to turn functionality on or off during runtime without deploying new code. They decouple deployment from release, providing granular control over who sees new features and when.

Benefits:

* **Decouple Deployment from Release:** You can deploy code containing new features to production without immediately exposing them to all users.  
* **Gradual Rollouts (Canary Releases):** Roll out features to a small percentage of users, then gradually increase the audience.  
* **A/B Testing:** Test different versions of a feature with different user segments to determine which performs better.  
* **Kill Switches:** Quickly disable a problematic feature in production if it causes issues, without needing a full rollback.  
* **Targeted Releases:** Enable features for specific user groups (e.g., beta testers, internal employees, premium users).

Example: A social media app wants to introduce a new "Stories" feature.

1. **Development:** The "Stories" code is developed and integrated into the main codebase.  
2. **Deployment:** The entire app, including the "Stories" code (which is initially hidden by a feature flag), is deployed to production.  
3. **Internal Testing:** The feature flag is enabled only for internal employees.  
4. **Beta Rollout:** The feature flag is enabled for 5% of users. Performance and error rates are closely monitored.  
5. **Phased Rollout:** If the beta is successful, the rollout percentage is gradually increased (e.g., 25%, 50%, 100%).  
6. **Kill Switch:** If a critical bug is discovered during the rollout, the feature flag for "Stories" can be immediately flipped off, making the feature invisible to all users without requiring an emergency code rollback.

</details>

<a id="automate-rollback-where-possible"></a>

<details>
<summary><h3>Automate rollback where possible</h3></summary>

Automated rollback is the ability for a system to automatically revert to a previous stable version of the software if a new deployment fails or introduces critical issues. This is a crucial safety net in a fast-paced release environment.

**How it works:**

* Deployment pipelines are configured with health checks and monitoring thresholds.  
* If a deployment fails to meet these health checks (e.g., increased error rates, service unavailability, high latency), the automated rollback process is triggered.  
* The system automatically redeploys the last known good version of the application.

**Benefits:**

* **Minimises Downtime:** Reduces the time users are impacted by a faulty deployment.  
* **Reduces Manual Error:** Eliminates the need for human intervention in a high-stress situation.  
* **Increases Confidence:** Teams can deploy more frequently knowing there's an automated safety net.

**Example:** A microservice responsible for user authentication is deployed.

1. **Deployment Triggered:** A new version of the authentication service is deployed to a Kubernetes cluster.  
2. **Health Checks:** Immediately after deployment, the Kubernetes deployment controller starts health checks (e.g., HTTP probes to `/health` endpoint).  
3. **Monitoring Alerts:** Simultaneously, the monitoring system (e.g., Prometheus with Grafana) detects a sudden spike in 5xx errors from the authentication service, exceeding a predefined threshold.  
4. **Automated Rollback:** The CI/CD pipeline, integrated with the monitoring system or the deployment tool (like Kubernetes' rolling updates with `minReadySeconds`), detects the failure. It automatically triggers a rollback, reverting the authentication service to the previous stable version.  
5. **Alert Notification:** The team is notified of the automated rollback and the reason for it, allowing them to investigate the root cause offline.

</details>

<a id="monitor-deployments-for-performance-and-errors"></a>

<details>
<summary><h3>Monitor deployments for performance and errors</h3></summary>

Post-deployment monitoring is essential for understanding the real-world impact of a release. It involves collecting and analysing metrics related to application performance, error rates, resource utilisation, and user experience in real-time.

Key Metrics to Monitor:

* **Performance:** Latency (response times), throughput (requests per second), resource utilisation (CPU, memory, disk I/O).  
* **Errors:** Error rates (e.g., 5xx HTTP errors), specific error logs, exceptions.  
* **Availability:** Uptime of services and endpoints.  
* **User Experience (UX) Metrics:** Page load times, core web vitals, conversion rates (if applicable).  
* **Business Metrics:** Sales, sign-ups, feature adoption (especially when using feature flags).

Tools:

* **Application Performance Monitoring (APM) Tools:** Datadog, New Relic, Dynatrace, AppDynamics.  
* **Logging Platforms:** ELK Stack (Elasticsearch, Logstash, Kibana), Splunk, Sumo Logic.  
* **Monitoring and Alerting Systems:** Prometheus, Grafana, Cloud Monitoring (Google Cloud), Azure Monitor, AWS CloudWatch.  
* **Real User Monitoring (RUM) Tools:** For front-end performance and user experience.

Example: After deploying a new version of their mobile app's backend API:

1. **Dashboards:** The operations team monitors a Grafana dashboard showing real-time latency, error rates, and CPU utilisation for the new API version.  
2. **Alerts:** An alert is configured to trigger if the 95th percentile latency for a critical API endpoint exceeds 500ms for more than 5 minutes, or if the error rate climbs above 1%.  
3. **Log Analysis:** Developers can use a logging platform (e.g., Kibana) to search for specific error messages or stack traces related to the new deployment.  
4. **User Feedback:** Product managers track A/B test results (if feature flags were used) and monitor user feedback channels for any reported issues or changes in engagement.

If any of these metrics deviate from expected baselines, it signals a potential issue with the new deployment, prompting investigation and potentially a rollback.

</details>


## Fail Loud, Fail Safe

<a id="add-logging-and-monitoring-from-the-start"></a>

<details>
<summary><h3>Add logging and monitoring from the start</h3></summary>

Logging and monitoring are not afterthoughts; they are fundamental aspects of building observable software. Integrating them from the initial stages of development ensures that when issues arise (and they will), you have the necessary data to diagnose, troubleshoot, and understand system behavior.

* **Logging:** Recording events, states, and data points within the application's execution. To avoid too much noise, different log levels (DEBUG, INFO, WARN, ERROR, FATAL) can help categorise severity as follows:  
  * DEBUG (/TRACE): Detailed information that will only be used when diagnosing bugs.  
  * INFO: Highlight the progress of the executing code.  
  * WARN: Events which are potentially harmful, or could lead to harmful situations.  
  * ERROR: An error in the currently running process.  
  * FATAL (/CRITICAL): Errors that reflect application-wide problems.  
* **Monitoring:** Collecting metrics (e.g., CPU usage, memory, request rates, error counts, latency) over time to observe system performance and health trends.

**Example:** **Logging:** Instead of just `console.log("User logged in")`, implement structured logging that includes context.

```javascript
// Example in Node.js with a logging library like Winston or Pino
const logger = require('./logger'); // Custom logger setup
function handleUserLogin(userId, ipAddress) {
    try {
        // ... login logic ...
        logger.info({
            message: 'User successfully logged in',
            userId: userId,
            ip: ipAddress,
            event: 'USER_LOGIN_SUCCESS',
            timestamp: new Date().toISOString()
        });
    } catch (error) {
        logger.error({
            message: 'Failed to log in user',
            userId: userId,
            ip: ipAddress,
            error: error.message,
            stack: error.stack,
            event: 'USER_LOGIN_FAILURE',
            timestamp: new Date().toISOString()
        });
    }
}
```

**Monitoring:** From day one, instrument your code to emit metrics. For example, using Prometheus client libraries:

```py
# Example in Python with Prometheus client
from prometheus_client import Counter, Histogram

# Define metrics globally or within a module
REQUEST_COUNT = Counter('http_requests_total', 'Total HTTP Requests', ['method', 'endpoint'])
REQUEST_LATENCY = Histogram('http_request_duration_seconds', 'HTTP Request Latency', ['method', 'endpoint'])
def process_api_request(method, endpoint):
    with REQUEST_LATENCY.labels(method, endpoint).time():
        # Simulate processing time
        import time
        time.sleep(0.1)
        REQUEST_COUNT.labels(method, endpoint).inc()
        # ... actual request processing ...
```

</details>

<a id="use-structured,-actionable-error-messages"></a>

<details>
<summary><h3>Use structured, actionable error messages</h3></summary>

Generic error messages like "An error occurred" are useless for debugging and frustrating for users. Actionable error messages provide context, identify the problem, and ideally suggest a solution or next steps. Structured errors (e.g., JSON format for APIs) make them machine-readable and easier for monitoring systems to parse and alert on.

**Example:**

**Bad Error Message:**

```json
{
  "status": 500,
  "message": "An internal server error occurred."}
```

**Good (Structured and Actionable) Error Message:**

```json
{
  "status": 400,
  "code": "INVALID_INPUT_DATA",
  "message": "Validation failed for user registration.",
  "details": {
    "fields": {
      "email": "Email address is already registered.",
      "password": "Password must be at least 8 characters long and contain a number."
    },
    "action": "Please review the highlighted fields and try again."
  },
  "timestamp": "2024-06-02T14:30:00Z",
  "traceId": "abc123xyz" // Correlation ID for tracing logs
}
```

This message immediately tells the client what went wrong, where, and how to fix it. For internal teams, `code` and `traceId` are invaluable for quickly finding relevant logs.

</details>

<a id="create-alerts-for-key-health-indicators"></a>

<details>
<summary><h3>Create alerts for key health indicators</h3></summary>

Monitoring data is only useful if it triggers action when something goes wrong. Alerts transform raw metrics into actionable notifications, allowing teams to respond proactively to issues before they impact a large number of users. Key health indicators are metrics that directly reflect the user experience or critical system functionality.

**Example:** Setting up alerts in a monitoring system like Grafana, Prometheus, or a cloud provider's monitoring service (e.g., Google Cloud Monitoring, AWS CloudWatch):

* **Error Rate Alert:**  
  * **Metric:** `http_requests_total_error_rate` (percentage of 5xx responses).  
  * **Threshold:** Alert if `error_rate > 5%` for 5 minutes.  
  * **Action:** Send notification to Slack channel `#ops-alerts` and PagerDuty for on-call engineer.  
* **Latency Alert:**  
  * **Metric:** `http_request_duration_seconds_p99` (99th percentile response time).  
  * **Threshold:** Alert if `p99_latency > 1.5s` for 10 minutes for critical endpoints.  
  * **Action:** Email to development team, create a low-priority ticket.  
* **Resource Utilisation Alert:**  
  * **Metric:** `cpu_utilization_percentage` on production servers/containers.  
  * **Threshold:** Alert if `cpu_utilization > 80%` for 15 minutes.  
  * **Action:** Scale up resources (if auto-scaling isn't configured/effective) or investigate potential bottlenecks.  
* **Business Metric Alert:**  
  * **Metric:** `new_user_registrations_per_hour`.  
  * **Threshold:** Alert if `registrations < 10` (or 50% of typical baseline) for 30 minutes during peak hours.  
  * **Action:** Notify product and marketing teams for potential funnel issues.

</details>

<a id="design-services-to-fail-independently"></a>

<details>
<summary><h3>Design services to fail independently</h3></summary>

In distributed systems (especially microservices architectures), a failure in one service should ideally not cause a cascading failure across the entire system. This principle, often called "bulkheading" or "isolation," ensures that the blast radius of a failure is contained.

**Example:** Consider an e-commerce application with separate services for:

* **Product Catalog Service:** Displays product information.  
* **User Profile Service:** Manages user accounts.  
* **Payment Gateway Service:** Handles transactions.  
* **Recommendation Service:** Provides personalized product suggestions.

If the **Recommendation Service** experiences an outage:

* **Without independent failure design:** The main product page might hang or fail to load because it's waiting for recommendations, leading to a poor user experience or even a full page crash.  
* **With independent failure design:**  
  * The Product Catalog Service continues to display products.  
  * The User Profile Service still allows users to log in.  
  * The Payment Gateway Service can still process orders.  
  * The Recommendation Service's failure is detected (e.g., by a **circuit breaker** \- see next point), and the application gracefully degrades. The product page might simply show a message like "Recommendations currently unavailable" or display a default set of popular products instead of personalized ones. The core functionality of browsing and buying remains intact.

This is achieved by:

* **Loose Coupling:** Services communicate via well-defined APIs, not direct database access.  
* **Asynchronous Communication:** Using message queues (e.g., Kafka, RabbitMQ) for non-critical communications.  
* **Resource Isolation:** Each service has its own dedicated compute, memory, and database resources.  
* **Timeouts:** Services have strict timeouts when calling other services.

</details>

<a id="use-circuit-breakers-and-retry-logic"></a>

<details>
<summary><h3>Use circuit breakers and retry logic</h3></summary>

These are patterns used in distributed systems to improve resilience and prevent cascading failures when dependent services become unavailable or slow.

* **Retry Logic:**  
  * **Purpose:** Allows a client to re-attempt a failed operation, assuming the failure is transient (e.g., network glitch, temporary service overload).  
  * **Best Practices:** Implement exponential backoff (increasing delay between retries) and a maximum number of retries to avoid overwhelming the failing service.  
* **Circuit Breaker:**  
  * **Purpose:** Prevents a client from repeatedly calling a failing service, giving the failing service time to recover and preventing the client from wasting resources on doomed requests.  
  * **States:**  
    * **Closed:** Normal operation; requests go through.  
    * **Open:** If failures exceed a threshold, the circuit opens, and subsequent requests immediately fail (or return a fallback) without hitting the failing service.  
    * **Half-Open:** After a timeout, the circuit allows a limited number of test requests to see if the service has recovered. If successful, it closes; otherwise, it returns to open.

**Example:**

```java
// Example in Java using Resilience4j (a popular resilience library) 

// Define a CircuitBreaker configuration
CircuitBreakerConfig config = CircuitBreakerConfig.custom()
    .failureRateThreshold(50) // 50% of calls must fail
    .waitDurationInOpenState(Duration.ofSeconds(60)) // Stay open for 60 seconds
    .ringBufferSizeInHalfOpenState(5) // Allow 5 calls in half-open state
    .ringBufferSizeInClosedState(100) // Evaluate last 100 calls in closed state
    .build();
CircuitBreaker circuitBreaker = CircuitBreaker.of("paymentService", config);
// Define a Retry configuration
RetryConfig retryConfig = RetryConfig.custom()
    .maxAttempts(3)
    .waitDuration(Duration.ofMillis(500)) // Initial wait 500ms
    .intervalFunction(IntervalFunction.exponentialBackoff()) // Exponential backoff
    .build();
Retry retry = Retry.of("paymentServiceRetry", retryConfig);
// Function to call the potentially failing payment service
public String processPayment(PaymentDetails details) {
    Supplier<String> paymentCall = () -> {
        // Simulate calling an external payment gateway
        if (Math.random() < 0.6) { // Simulate 60% failure rate
            throw new RuntimeException("Payment gateway unavailable");
        }
        return "Payment successful for " + details.amount;
    };
    // Apply Circuit Breaker and Retry logic
    return Decorators.ofSupplier(paymentCall)
        .withCircuitBreaker(circuitBreaker)
        .withRetry(retry)
        .withFallback(throwable -> {
            // Fallback logic: e.g., queue payment for later, notify user
            System.err.println("Payment failed after retries and circuit breaker: " + throwable.getMessage());
            return "Payment temporarily unavailable. We'll try again shortly.";
        })
        .get();
}
```

In this example, if the `paymentService` starts failing frequently, the circuit breaker will "trip," causing subsequent payment attempts to immediately return the fallback message without even trying to call the external service, thus protecting both the client and the overloaded payment gateway. Retry logic ensures transient issues are overcome.

</details>

