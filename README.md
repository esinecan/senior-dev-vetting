# senior-dev-vetting
A curated list of technical interview questions and answers focused on real-world backend engineering scenarios, including system design, microservices, CI/CD, observability, API versioning, and more. Answers are first person as these were picked from IRL interviews. Therefore each of them is Valid for some developer somewhere but maybe not for you. This is more of a "what does a good answer look like" situation.

---

## 1) Monorepo or Microrepo?
**Question:** “Do you prefer monorepo or microrepo? Which one did you use at your last workplace and why?”  
**Answer:**  
“At my last workplace, we used a monorepo. Common libraries, dependency management (Gradle), and CI/CD workflows were easier to maintain under a unified structure. Monorepos offer advantages in managing shared code and enforcing organization-wide standards. 

Microrepos, on the other hand, boost team autonomy but introduce challenges like dependency versioning and potential code duplication. In short, microrepos suit smaller or decoupled teams, while monorepos serve well in tightly integrated environments.”

---

## 2) Standardizing Microrepos with GitHub Actions
**Question:** “There are many microrepos. How do you standardize CI/CD processes with GitHub Actions?”  
**Answer:**  
“I create a central repository like `org-ci-templates` and define reusable `.github/workflows/*.yml` files within it. Each microrepo then uses a lightweight workflow file referencing the shared action via `uses: org-ci-templates/...@main`.

This allows me to propagate CI changes centrally. I also enforce consistent rules such as branch protection, code review requirements, and test execution policies from this shared layer.”

---

## 3) Mentoring a Junior Opening a Microrepo
**Question:** “The junior engineer under you is starting a new microrepo. How will you guide them?”  
**Answer:**  
“I provide a scaffold via a template repo or cookiecutter structure that includes workflows, Dockerfile, configs, and basic test scaffolding. PR and Issue templates ensure consistency.

For the first few PRs, I review thoroughly and guide them to research independently before helping. I focus on balancing mentorship with accountability to foster learning and productivity.”

---

## 4) Schemas and Versioning in Kafka
**Question:** “Is schema compatibility important in Kafka? What if the same event is in multiple topics?”  
**Answer:**  
“Kafka emphasizes **contract compatibility**. We register event schemas using Avro or Protobuf and version them explicitly (e.g., `UserCreatedV1`, `UserCreatedV2`).

Even if the event is routed to different topics, a **canonical schema** must remain consistent. Consumers fetch schemas from the registry, and backward compatibility is maintained to prevent breakages.”

---

## 5) API Versioning Strategy (V1 → V2)
**Question:** “A customer requires an API change. How do you version it?”  
**Answer:**  
“For major changes, I prefer **path-based versioning** (`/v1/`, `/v2/`). For minor non-breaking changes, I may use header-based versioning or a query toggle (e.g., `?include=newField`).

Different versions are isolated via controllers or strategy patterns, and I maintain separate OpenAPI specs and test coverage for each.”

---

## 6) Managing Simultaneous Changes in V1 and V2
**Question:** “You need to modify the response object in both V1 and V2. How do you manage this in Git?”  
**Answer:**  
“I use a unified **feature branch** (e.g., `feature/add-middleName`) and organize commits using prefixes like `feat(v1)` and `feat(v2)`.

Swagger definitions (`swagger/v1.yaml`, `swagger/v2.yaml`) are updated in parallel. CI pipelines test both versions, and I apply appropriate semantic version tags like `v1.3.0` and `v2.5.0` upon merge.”

---

## 7) Hotfixes and Semantic Versioning
**Question:** “How do you handle hotfixes for a previously released version?”  
**Answer:**  
“I create a **hotfix branch** from the tagged commit (e.g., `hotfix/v1.3.1`) and apply the fix. After passing tests, I push a new version tag.

If needed, I cherry-pick the change to `main`. For long-term support, I maintain dedicated branches like `release/v1.3`.”

---

## 8) Kafka Partition Scaling
**Question:** “Kafka topic has performance issues due to load. What do you do?”  
**Answer:**  
“I increase the number of **partitions**. This enables parallel consumption within the same consumer group, reducing lag.

Important: Kafka partitions can be **increased** but not **decreased**. Proper initial planning is crucial to avoid future scaling issues.”

---

## 9) SOLID Principles
**Question:** “Can you summarize SOLID? They said you got stuck on Liskov.”  
**Answer:**  
- **S**: Single Responsibility — A class should have only one reason to change.  
- **O**: Open/Closed — Open for extension, closed for modification.  
- **L**: Liskov Substitution — Subclasses must be substitutable without breaking functionality.  
- **I**: Interface Segregation — Clients should not be forced to implement unused methods.  
- **D**: Dependency Inversion — Depend on abstractions, not on concretions.

---

## 10) Version Control in API Controller Layer
**Question:** “How do you manage multiple API versions in your controller layer?”  
**Answer:**  
“I typically maintain separate controllers for each version (`/v1/`, `/v2/`). If code duplication becomes an issue, I apply a **strategy pattern** to encapsulate logic and share underlying services and DTOs.”

---

## 11) Observability & Tracing
**Question:** “How do you implement request tracing? Where does it start?”  
**Answer:**  
“I propagate `trace_id` via Kafka headers and use a Log4j wrapper with **MDC** to include it in logs automatically.

While traces currently start at the API layer, I prefer assigning the trace ID at the **API Gateway** for full lifecycle correlation across distributed systems.”

---

## 12) Kubernetes Multi-Tenancy Management
**Question:** “How do you manage multi-tenancy in Kubernetes?”  
**Answer:**  
“For intra-org separation, I use **namespaces**, **RBAC**, **resource quotas**, and **network policies**. For strict tenant isolation, I provision separate clusters per tenant.

Mid-level isolation can be handled via tools like **vcluster** or **Kiosk**, simulating virtual clusters on a single physical Kubernetes environment.”

---

## 13) Helm Chart Management
**Question:** “How do you write and reuse Helm Charts?”  
**Answer:**  
“I draft the initial chart manually and ensure correctness. After that, I abstract common templates using tools like **Cyclops** for consistency and reuse.

I don’t need to memorize Helm grammar entirely—automation and reuse reduce cognitive overhead.”

---

## 14) SLA and Contract Handling
**Question:** “Do you manage SLAs? How do you enforce them?”  
**Answer:**  
“Yes. We sign detailed SLAs with major clients, including metrics like latency and uptime with penalties for breaches.

Internally, we maintain **inter-domain operational SLAs** and tie both internal and external agreements to **SLOs** via metrics, monitoring, and alerting infrastructure.”

---

## 15) Circuit Breakers & Resilience
**Question:** “How do you handle service outages or degraded upstream services?”  
**Answer:**  
“I implement the **circuit breaker pattern** using libraries like **Resilience4j**. If failures exceed thresholds, the circuit opens and halts traffic temporarily.

Fallbacks such as cached data or user messages are employed. Health probes test recovery while in half-open state, and traffic resumes once the service stabilizes.”

---

## 16) Caching and Invalidation Strategy
**Question:** “How do you keep cache data fresh and consistent?”  
**Answer:**  
“I use **write-through** or **write-behind** strategies to sync cache on data changes. Relying solely on TTL may be inadequate.

For distributed cache, I use **Redis Pub/Sub** to publish **invalidation events** when updates occur, ensuring consistency across nodes.”

---

## 17) DLQ Handling in Async Systems
**Question:** “How do you manage unprocessable messages in Kafka or RabbitMQ?”  
**Answer:**  
“I configure a **Dead Letter Queue (DLQ)**. After N retry attempts, failed messages are routed to DLQ.

I monitor DLQ volume and set alerts if thresholds are crossed. I also build tooling to inspect, fix, and re-publish failed messages when applicable.”

---

## 18) Zero-Downtime Data Migration
**Question:** “How do you add a column to a live production table?”  
**Answer:**  
“I follow a **non-blocking migration** strategy. Adding a column in Postgres or MySQL is generally online-safe.

For larger changes (e.g., adding indexes), I use **pt-online-schema-change** or similar tools. I introduce changes in phases, and if application logic depends on the column, I implement feature flags and verify backward compatibility.”

---

## 19) Feature Flags & Safe Deployments
**Question:** “How do you release new features incrementally?”  
**Answer:**  
“I use **feature flags** to keep features dormant in production until explicitly enabled. For deployment, I follow **canary** or **blue-green** strategies.

Canary exposes features to a subset of users. If stable, I increase exposure. In case of errors, I disable the flag or revert traffic to the previous stable version.”

---

## 20) Chaos Engineering
**Question:** “Do you apply chaos testing? How?”  
**Answer:**  
“Yes. I use tools like **Chaos Monkey** to simulate failures such as latency spikes or service outages in non-prod or isolated environments.

Chaos Engineering reveals systemic weaknesses early. The key is to perform controlled, observable experiments and turn findings into actionable improvements.”

---

## 21) Code Review Practices
**Question:** “What do you prioritize during code review?”  
**Answer:**  
“I check for architectural correctness, readability, and test coverage. I often frame questions to provoke thought rather than prescribe solutions.

Feedback should be constructive and focused. I also prefer small, focused PRs—large PRs lead to shallow reviews.”

---

## 22) Domain Driven Design & Bounded Contexts
**Question:** “How do you apply DDD and handle bounded contexts?”  
**Answer:**  
“I model subdomains as distinct **bounded contexts**. For instance, in e-commerce, `Payment`, `Order`, and `Inventory` are isolated modules or services.

Each context owns its models and contracts. Communication is event-driven or via stable API boundaries. This modular approach helps with scalability, maintainability, and team autonomy.”
