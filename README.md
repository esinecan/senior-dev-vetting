# senior-dev-vetting (IN PROGRESS BUT ALSO USABLE)

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

---
# System Design Vetting

   ## 1. **What is the CAP Theorem?**

   **Short Answer**  
   The CAP Theorem states that in any distributed data store, you can only guarantee two of these three qualities at the same time: Consistency, Availability, and Partition Tolerance (C, A, P).

   **Details**  
   - **Consistency**: Every read receives the most recent write or an error.  
   - **Availability**: Every request receives a response—success or failure—without guaranteed recentness of data.  
   - **Partition Tolerance**: The system continues operating despite message loss or partial failures in the network.

   Put simply, when your cluster is dealing with network partitions, you often have to choose between strict consistency and availability. Examples:
   - **CA (Consistency + Availability)**: Traditional relational databases (without partition tolerance in a distributed sense).  
   - **CP (Consistency + Partition Tolerance)**: Systems like MongoDB or HBase often prioritize data correctness when a partition occurs.  
   - **AP (Availability + Partition Tolerance)**: Systems like Cassandra focus on ensuring the system remains up, accepting possible temporary data discrepancies.

   ---

   ## 2. **How is Horizontal Scaling different from Vertical Scaling?**

   **Short Answer**  
   - **Horizontal Scaling**: Adding more machines (nodes) to distribute load.  
   - **Vertical Scaling**: Adding more power (CPU/RAM) to a single machine.

   **Details**  
   - **Horizontal Scaling**  
     - You spin up more server instances and balance requests among them.  
     - More resilient: if one instance fails, others can handle traffic.  
     - Requires a load balancer.  
     - Often the go-to approach for massive, internet-scale applications.

   - **Vertical Scaling**  
     - You keep the same machine but beef it up: add more CPU cores, RAM, or faster disks.  
     - Simpler to manage but limited by hardware constraints and potential single-point-of-failure risk.  
     - Great for smaller-scale or when you can’t easily distribute your workload.

   ---

   ## 3. **What is Load Balancing and Why is it Important?**

   **Short Answer**  
   Load balancing is the process of distributing incoming traffic across multiple backend servers to optimize resource use, reduce response times, and avoid overload on any single node.

   **Details**  
   - Acts like a traffic cop, routing client requests to the least-burdened or best-performing server.  
   - Improves fault tolerance: if one server goes down, the load balancer redirects to healthy servers.  
   - Helps scale seamlessly: adding new servers can happen behind the load balancer without disturbing live traffic.  
   - Can handle tasks like SSL termination, reducing overhead on application servers.  

   Ultimately, robust load balancing is critical for high-availability systems.

   ---

   ## 4. **What Are Latency, Throughput, and Availability in a System?**

   **Short Answer**  
   - **Latency**: The time to process a single request, typically in milliseconds.  
   - **Throughput**: The volume of data (or number of operations) the system can handle per second.  
   - **Availability**: The proportion of time the system can respond successfully.

   **Details**  
   - **Latency** measures responsiveness. Lower latency = faster system responses.  
   - **Throughput** reflects capacity. High throughput = many requests handled per second.  
   - **Availability** is uptime percentage. A “five nines” system is up 99.999% of the time.

   High-performance architectures optimize all three, though trade-offs can exist (e.g., ultra-low latency might reduce throughput or complicate availability).

   ---

   ## 5. **What Is Sharding?**

   **Short Answer**  
   Sharding (a form of horizontal partitioning) splits a large dataset across multiple machines or database nodes so each node holds only a portion (“shard”) of the data.

   **Details**  
   - Increases **capacity**: Instead of one massive server, you spread data across smaller servers.  
   - Boosts **performance**: Shards can handle queries in parallel, improving read/write throughput.  
   - Enhances **scalability**: You can add more shards as data grows rather than scaling a single giant machine.  

   Imagine you have 1TB of data. Instead of stacking up a massive server, you split it into four shards of 256GB each on different machines—each piece is smaller, faster to query, and more efficient.

   ---

   ## 6. **How Is a NoSQL Database Different from a SQL Database?**

   | **Category** | **SQL**                                                    | **NoSQL**                                                          |
   |--------------|------------------------------------------------------------|---------------------------------------------------------------------|
   | **Model**    | Relational                                                 | Non-relational (key-value, document, column, graph)                 |
   | **Schema**   | Rigid (tables, columns, fixed schema)                      | Flexible or dynamic schema                                          |
   | **Data**     | Primarily structured data                                  | Suited for semi-structured, unstructured, or variable data          |
   | **Transactions** | ACID (Atomic, Consistent, Isolated, Durable)           | BASE (Basically Available, Soft state, Eventual consistency)        |

   **Details**  
   - **SQL Databases** are great for structured queries, strong consistency, and well-defined schemas (think financial transactions).  
   - **NoSQL Databases** are often chosen for scalable, highly distributed workloads with fluid data models (e.g., social networks, real-time analytics).

   ---

   ## 7. **How Is Sharding Different from Partitioning?**

   **Short Answer**  
   - **Sharding** typically refers to splitting and distributing the entire data set across multiple machines (horizontal scaling).  
   - **Partitioning** often refers to dividing data at the table/index level, usually within a single database instance, for manageability and performance optimization.

   **Details**  
   - **Database Partitioning**:  
     - Splits large objects (tables, indexes, etc.) into distinct segments, often within the same database cluster.  
     - Aids performance by localizing related data and can reduce index sizes.

   - **Sharding (Horizontal Partitioning)**:  
     - Another layer up: replicates the schema onto multiple nodes and distributes subsets of data to each node.  
     - Primarily about scaling out by adding more independent database servers.

   Both approaches slice up data, but sharding is specifically geared toward distributing data across multiple machines to handle massive load, whereas partitioning can also occur entirely within a single RDBMS node for performance or management reasons.

   ---

   ## 8. **How Are Performance and Scalability Related?**

   **Short Answer**  
   Performance is about how fast a system can serve a single user or request; scalability is about how well that performance holds up as more users or data are added.

   **Details**  
   - **Performance**: Measured in metrics like response time or latency for a single request.  
   - **Scalability**: Measures the system’s capacity to handle increased load proportionally to resources added.  
     - If your app is slow for one user, that’s a performance issue.  
     - If it’s fine for one user but slow with many users, that’s a scalability issue.

   ---

   ## 9. **What Is Caching, and What Are the Various Cache Update Strategies?**

   **Short Answer**  
   Caching involves storing frequently accessed data in a fast storage layer (e.g., in-memory) to reduce latency and improve throughput.

   **Strategies**  
   1. **Cache-aside (Lazy Loading)**  
      - Application looks up data in the cache; if missing, fetch from DB and populate cache.  
      - Pros: Only requested data is cached.  
      - Cons: First request after a miss can be slow; stale data risk if DB changes and cache isn’t refreshed quickly.

   2. **Write-through**  
      - Application writes data to the cache, which synchronously writes to the underlying DB.  
      - Pros: Data in the cache is always consistent with the DB.  
      - Cons: Slower writes due to synchronous DB update; possible overhead for data that might never be read.

   3. **Write-behind (Write-back)**  
      - Data is written to the cache first; the cache writes to DB asynchronously.  
      - Pros: Faster write performance.  
      - Cons: Risk of data loss if the cache fails before it syncs with the DB.

   4. **Refresh-ahead**  
      - Cache proactively refreshes data before expiry.  
      - Pros: Reduces cache-miss latency for predictable access patterns.  
      - Cons: Can lead to wasted work if data isn’t requested again soon.

   ---

   ## 10. **What Are the Various Consistency Patterns in System Design?**

   **Short Answer**  
   They refer to how a system replicates and synchronizes data across nodes to deliver the “latest” version on reads.

   **Patterns**  
   - **Weak Consistency**: System does not guarantee updated data on reads immediately after a write. Suited for real-time streaming (e.g., gaming, voice chat) where occasional data gaps are tolerable.  
   - **Eventual Consistency**: After a write, the system eventually becomes consistent, often within milliseconds. Common in distributed stores (e.g., DNS, email systems).  
   - **Strong Consistency**: Reads always return the latest write, requiring synchronous replication or coordination. Common in traditional RDBMS or file systems needing strict correctness.

   ---

   ## 11. **What Is a Content Delivery Network (CDN)?**

   **Short Answer**  
   A CDN is a network of geographically distributed servers that cache and serve content (images, scripts, videos, etc.) from locations closer to end users, reducing latency and offloading traffic from origin servers.

   **Details**  
   - **Benefits**:  
     - Reduced load on origin servers.  
     - Faster content delivery to users globally.  
   - **Types**:  
     - **Push CDN**: You proactively upload new content to the CDN when it changes.  
     - **Pull CDN**: The CDN fetches content from your server on the first request, caching it for future users.

   ---

   ## 12. **What Is Leader Election?**

   **Short Answer**  
   Leader Election is a process in distributed systems to designate one node (server) as the “leader” responsible for specific tasks—like coordinating writes to a shared resource—while other nodes follow.  

   **Details**  
   - Ensures only one node handles certain duties, preventing conflicts or double updates.  
   - Must detect leader failure and re-elect seamlessly (often via consensus algorithms like Raft or Paxos).  
   - Useful in high-availability systems and strongly consistent clusters.

   ---

   ## 13. **How Do You Approach Answering a System Design Interview Question?**

   1. **Clarify Requirements**  
      - Ask questions to narrow scope and confirm constraints (e.g., “What’s the scale?” “Which features are must-have?”).  
   2. **Outline Key Requirements and Constraints**  
      - Consider performance targets, storage needs, availability, etc.  
   3. **Propose a High-Level Design**  
      - Sketch the main components: load balancers, service layers, databases, caches, etc.  
   4. **Dive into Details (Trade-Offs)**  
      - Explain data models, partition strategies, consistency models, etc.  
   5. **Iterate / Optimize**  
      - Brainstorm improvements or alternatives, noting pros and cons.  

   **Goal**: Show your problem-solving thought process and your ability to reason about design decisions, not just memorize solutions.

   ---

   ## 14. **What Are Some Design Issues in Distributed Systems?**

   1. **Heterogeneity**  
      - Different hardware, networks, OS, programming languages—requiring standard protocols and interoperable designs.  
   2. **Openness**  
      - Ability to extend, replace, or improve components easily without breaking the system.  
   3. **Security**  
      - Ensuring confidentiality, integrity, and availability across multiple machines and networks.  
   4. **Scalability**  
      - Handling growing traffic and data volumes without performance degradation.  
   5. **Failure Handling**  
      - Partial failures (some nodes fail, others are up). Detecting and isolating the problem is trickier than in a single-host setup.

   ---

   ## System Design Interview Questions for Experienced

   ## 15. Design a global chat service like Whatsapp or a facebook messenger.

   What are some of the required features?
   Allow users to chat over the internet.
   Provide support for one-on-one and group chats. Messages need to be stored for better viewing.
   Messages need to be encrypted for security purposes.
   What are some of the common problems that can be encountered? What would happen to a message if it is sent without an internet connection?
   Will encrypting and decrypting increase the latency?
   How are the messages sent and notified to the device?
   Possible Tips for consideration:
   Split database schema into multiple tables such as user table, chat table, massage table etc.
   Make use of web sockets for bi-directional communication between the device and the server.
   Make use of push notifications for notifying the members even if they are online.

   ### 1. **Core Requirements**

   1. **One-on-One and Group Chats**  
      - Users can chat individually or join group conversations.  
      - Group size can vary from a few members to hundreds or thousands.

   2. **Message Persistence**  
      - Store messages in a way that users can view them later, even if they come online after a delay.  
      - Enable message history, including attachments (images, documents).

   3. **Real-Time Updates**  
      - Messages should arrive in near real-time.  
      - Typing indicators, read receipts, and presence (online/offline status) are often desired features.

   4. **Security and Encryption**  
      - End-to-end encryption (E2EE) or at least transport-level encryption (TLS) to ensure data privacy.  
      - Must handle key management efficiently if using E2EE.

   5. **High Availability & Global Scale**  
      - The system should handle millions (or billions) of users across different regions.  
      - Must ensure low latency via regional deployments and possibly CDNs for supporting media files.

   6. **Offline Support**  
      - Messages must queue or buffer until a recipient reconnects.  
      - Provide push notifications to alert users when new messages arrive.

   ### 2. **High-Level Architecture**

   ```
   [ User Device ] <--- WebSockets/HTTP ---> [ Front-End Service ] <--->
                    |                                        |
                    v                                        v
           [ Messaging Service ] <--> [ Message Database ] <--> [ Encryption/Key Store ]
                    |                   (Queues, Storage)               
                    v
           [ Notification Service ] <--> [ Push Notification Gateways ]
                    |
                    v
           [ Presence Service ]
   ```

   ### 2.1 **Front-End Service (Gateway Layer)**
   - Receives incoming connections (via WebSockets or HTTP).
   - Authenticates the user using session tokens or OAuth/OpenID Connect.
   - Routes traffic to the appropriate backend service (e.g., Chat/Messaging, User Profile).

   ### 2.2 **Messaging Service**
   - Handles core chat logic—creating chat sessions, processing sent messages, storing them.
   - For **one-on-one** chats: typically store each conversation with metadata (participants, encryption keys, timestamps).
   - For **group** chats: maintain a separate entity for group membership, group keys (if end-to-end encrypted), and group metadata (name, description).
   - Manages distribution of new messages to recipients and pushes notifications.

   ### 2.3 **Message Storage**
   - **Schema** could be split into:
     1. **User Table** (user_id, name, profile info, public encryption keys).
     2. **Chat Table** (chat_id, chat_type [one-on-one/group], participant list).
     3. **Message Table** (msg_id, chat_id, sender_id, content, timestamp).
   - A NoSQL solution (e.g., Cassandra, DynamoDB) often works well for high write throughput.  
   - Alternatively, an SQL solution can be used with effective partitioning/sharding strategies.

   ### 2.4 **Encryption & Key Management**
   - **Transport Encryption**: Always use TLS for data in transit.  
   - **End-to-End Encryption** (optional but recommended):  
     - Store each user’s public key in a Key Store (or dedicated Key Management Service).  
     - Sender encrypts messages with the recipient’s public key (one-on-one) or the group’s shared secret (group chats).  
     - The server primarily handles message forwarding; cannot decrypt if truly E2E.  
     - For group chats, a secure group key distribution mechanism is necessary (e.g., via sender-signed updates).

   ### 2.5 **Notification Service**
   - Sends push notifications via APNs (iOS), FCM (Android), or other channels (web push).  
   - Alerts users who are offline.  
   - Requires minimal data in the push payload for security; full content is fetched upon app open or background service request.

   ### 2.6 **Presence Service**
   - Tracks user online/offline status, often stored in an in-memory datastore (Redis) for quick lookups.  
   - Sends presence updates to participants in a chat (“typing…”, “online”, etc.).

   ### 2.7 **Offline Message Handling**
   - If a recipient is offline (e.g., no internet connection):
     - **Store** the messages in the message database.  
     - When the user reconnects or the mobile app opens, the server pushes the queued messages.  
     - Push notification tokens ensure the user’s device is alerted even if the app is in the background.

   ### 3. **Common Challenges & Considerations**

   1. **Offline Sending and Receiving**  
      - If a user sends a message with no internet, the app locally queues it until connectivity is restored.  
      - The server must handle potential duplicates from retries if an acknowledgment is lost.

   2. **Encryption Overhead and Latency**  
      - **Transport-level** encryption (TLS) is generally efficient.  
      - **End-to-end** encryption can add CPU overhead on client devices for key exchange and message encryption/decryption.  
      - Typically still acceptable for short text messages; large attachments can be symmetrically encrypted for efficiency.

   3. **Ordering and Delivery Guarantees**  
      - High-scale chats require careful handling of message order (especially for group chats with many participants).  
      - You can use sequence numbers or server timestamps to keep messages in order.  
      - The system might provide at-least-once or exactly-once delivery semantics.

   4. **Scalability**  
      - **Sharding** messages by user_id or chat_id helps scale horizontally.  
      - **Partition** user data regionally to reduce cross-region latency.  
      - Use **microservices** to separate core components (presence, messaging, push notifications).

   5. **Reliability & Fault Tolerance**  
      - Use **replication** in the data layer to handle node failures.  
      - Have a robust monitoring setup to detect partial failures quickly.  
      - Leader election for any critical coordination tasks (if needed for group chat or ephemeral messaging logic).

   6. **Push Notification Throttling**  
      - Large group chats might require batching or throttling notifications to avoid spam or hitting rate limits.  
      - For example, when a group has thousands of members, a single message might trigger thousands of push requests.

   ### 4. **Sample Workflow (One-on-One Messaging)**

   1. **User A** sends a message to **User B** via a WebSocket connection.  
   2. The **Messaging Service** receives the message, stores it in the **Message Database**.  
   3. If E2E encryption is enabled, the server sees only the ciphertext.  
   4. The service forwards the encrypted message to **User B** if online.  
      - If **User B** is offline, it queues the message and sends a push notification via the **Notification Service**.  
   5. **User B** comes online, receives the message from the queue, decrypts using a local private key, and can now read the message.

   ### 5. **Final Thoughts**

   Designing a global chat system requires balancing **low latency**, **high throughput**, and **robust security**. Careful partitioning, consistent data replication, asynchronous queues, and real-time communication protocols (like WebSockets) are central to ensuring scalable, fault-tolerant, and secure messaging.

   ---

   ## 16. How do you design a URL shortening service like TinyURL or bit.ly?
   TinyURL or bit.ly takes a long URL and generates a new unique short URL. These systems are also capable of taking the shortened URL and returning the original full URL.

   What are some of the Required Features?
   Generate a short URL having a length shorter than the original URL. Store the original URL and map it to the shortened one.
   Allow redirects in the shortened URLs. Support custom names for short URLs. Handle multiple requests at the same time.
   What are some of the Common Problems encountered?
   What if two users input the same custom URL?
   What happens if there are more user load than expected? How do you regulate the database storage space?
   Possible tips for consideration:
   The concept of hashing can be used for linking original and new URLs. REST API can be used for balancing high traffic and handling front-end communication.
   Multithreading concept for handling multiple requests at the same time. NoSQL databases for storing original URLs.


   ### 1. **Core Requirements**

   1. **Short URL Generation**  
      - Transform a long URL into a shortened version, typically under a fixed domain (e.g., `tinyurl.com/abc123`).
      - The short link length should be much smaller than the original URL.

   2. **Mapping & Redirection**  
      - When users visit the short URL, they are redirected (HTTP 301/302) to the original URL.
      - Must handle a high volume of redirects quickly.

   3. **Custom (Vanity) URLs**  
      - Users may request a custom short code, e.g., `tinyurl.com/myEvent` instead of random hash.

   4. **Scalability & Reliability**  
      - Handle millions of URL shortenings and redirects daily.
      - Service must remain available under heavy load (global usage).

   5. **Concurrency**  
      - Support many simultaneous requests for short URL creation and redirection.

   ### 2. **High-Level Architecture**

   ```
               ┌───────────────┐
               │     Client    │
               └───────────────┘
                       |
        (Create Short URL)  (Visit Short URL)
                       v
             ┌────────────────────────┐
             │  Load Balancer / API   │
             └────────────────────────┘
                       |
                       v
        ┌─────────────────────────────────┐
        │ URL Shortener Service (Backend)│
        └─────────────────────────────────┘
             |        |             |
             |        |             |
             v        v             v
      [ Database ]  [ Cache ]  [ Analytics ]
   ```

   ### **Frontend / API Layer**
   - Provides a REST endpoint (e.g., `POST /create` or `POST /shorten`) for creating short URLs.
   - Receives HTTP `GET` requests for short URLs (e.g., `GET /<shortId>`).
   - Scales horizontally behind a load balancer (e.g., using round-robin or least-connections).

   ### **URL Shortener Service**
   - Processes requests from the API layer:
     - **Shorten**: Generates and stores a short code for a given long URL.
     - **Redirect**: Looks up the original URL from the short code and returns a 301/302 redirect.
   - Handles concurrency through multi-threading or async I/O, ensuring quick response.

   ### **Data Storage**
   - **Primary DB**: Stores mappings from short codes to original URLs.
     - Can be a NoSQL store (e.g., Cassandra, DynamoDB) or a relational DB with careful sharding.  
     - Key = short code, Value = original URL, plus metadata (creation date, expiry, usage count).
   - **Cache (Optional)**: Frequently accessed short codes can be cached in Redis or Memcached for faster lookups.

   ### **Analytics (Optional)**
   - Tracks usage (how many clicks, from which region, etc.).
   - May involve a separate microservice or data pipeline to handle large-scale analytics.

   ### 3. **Data Model**

   A minimal table or NoSQL structure might look like:

   | **Field**        | **Description**                                          |
   |------------------|----------------------------------------------------------|
   | `short_code`     | A unique string key (short ID)                           |
   | `original_url`   | Full original URL                                        |
   | `created_at`     | Timestamp or epoch time                                  |
   | `expiration`     | Optional expiration date/time for the short code         |
   | `custom_user_id` | Optional user reference if you track user ownership      |
   | `hit_count`      | Number of times the short link was accessed (optional)   |

   #### **Choosing the Short Code**
   1. **Hashing Approach**: Hash the original URL (e.g., MD5/SHA) and encode a portion in Base62 (characters `0-9A-Za-z`).  
   2. **Random ID Generation**: Generate a random short string (e.g., 6–10 chars). Collisions can be managed by re-generating if the key already exists.  
   3. **Auto-Increment / Sequence**: Use a global counter, convert the counter’s numeric value to Base62. Must handle concurrency to avoid collisions.

   ### 4. **Key Workflows**

   ### 4.1 **Creating a Short URL**
   1. **Receive** the original (long) URL and optional custom code from the user.
   2. **Check Custom Code**  
      - If provided, confirm it’s unique and not already taken.  
      - If taken, return a conflict/error or prompt the user for a different one.
   3. **Generate** a short code if none is provided (via hashing or random ID).
   4. **Store** the (short code → long URL) mapping in the database.
   5. **Return** the short code (combined with the domain) to the user.

   ### 4.2 **Redirecting**
   1. **User requests** the short URL, e.g., `GET /abc123`.
   2. **Look up** `abc123` in the DB or cache to find the corresponding original URL.
   3. **Redirect** (HTTP 301/302) the user’s browser to the original URL.
   4. (Optional) **Update** usage stats or analytics counters.

   ### 5. **Common Problems & Their Solutions**

   1. **Duplicate Custom URLs**  
      - Enforce uniqueness in the DB. If a new custom code is requested and it already exists, return an error or prompt for a new one.

   2. **Handling Excessive Load**  
      - **Scale horizontally**: Use stateless backend containers behind a load balancer.  
      - **Partition / Shard** the data store (key-based sharding by short code).
      - **Use caching** to speed up frequent lookups.

   3. **Database Storage Growth**  
      - Potentially billions of mappings over time.  
      - Use **NoSQL** stores known for large-scale key-value lookups.  
      - Implement data **retention policies** or TTL for expired / rarely used links.

   4. **Service Reliability**  
      - Replicate DB or use multi-region setups for fault tolerance.  
      - Load balancer health checks keep the service up if some nodes fail.

   5. **Collisions in Hashing / Random**  
      - If collision occurs, re-generate a new short code or use a robust approach to ensure near-zero collision (e.g., checking the DB for existence before finalizing).

   6. **Security / Malicious URLs**  
      - Some short links lead to malware or spam.  
      - Optionally implement link checking or scanning, but this can add overhead.  
      - Provide an abuse reporting feature to handle malicious links.

   ### 6. **Potential Enhancements**

   1. **Analytics & Tracking**  
      - Track clicks, user agents, referrers, countries.  
      - Show usage charts to link owners.

   2. **Link Expiration**  
      - Allow short URLs to expire after a certain time or number of uses, reducing storage.

   3. **Custom Domains**  
      - Enterprise users or individuals might want branded domains.

   4. **API Rate Limits**  
      - Prevent misuse or spam by limiting requests per minute, especially for anonymous users.

   ### 7. **Example Sequence: Shortening a URL**

   1. **`POST /shorten`**  
      ```json
      {
        "original_url": "https://very-long-url.com/docs/...",
        "custom_alias": "mydocs"
      }
      ```
   2. **Backend** checks if `mydocs` exists in the DB.  
      - If not, insert record: `{ "short_code": "mydocs", "original_url": "https://..." }`.  
   3. **Response**: `tinyurl.com/mydocs`

   **Visiting** `tinyurl.com/mydocs` will:
   1. Look up `mydocs` in the DB → `https://very-long-url.com/docs/...`
   2. **Redirect** (HTTP 302) to the original URL.

   ### **Final Thoughts**
   A URL shortening service heavily relies on **fast key-value lookups** and **compact data storage** strategies. By employing load balancing, distributed databases, caching, and robust conflict-handling, you can reliably serve millions (or billions) of redirects and new short link creations with minimal latency.

   ---

   ## 17. Design a forum-like systems like Quora, Reddit or HackerNews.
   These sites are meant for posting questions and answering them, showing newsfeed highlighting popular questions based on tags and related topics.

   What are some of the Required Features?
   Users should be able to create public posts and apply tags to them. Posts should be sortable based on tags.
   Post comments in real-time by users.
   Display posts on newsfeed based on followed tags.
   What are some of the Common Problems encountered?
   Should it be just a web application?
   Where to store the uploaded images and links? How can you determine the related tags?
   How can you distribute posts across a server network?
   Possible tips for consideration:
   Check on using SQL database for mapping relational data between users, posts, comments, likes, tags, posts etc.
   Incorporate multithreading and load balancer for supporting high traffic. Make use of sharding for distributing the data across different systems.
   Incorporate machine learning algorithms for finding correlations between the tags.

   ### 1. **Core Requirements**

   1. **Public Posting**  
      - Users create posts (questions or topics) and apply relevant tags.
      - Optionally allow rich text, links, images, or embedded media.

   2. **Tagging & Categorization**  
      - Posts must be easily searchable or filterable by tags/topics.
      - The system can suggest related tags automatically.

   3. **Newsfeed or Front Page**  
      - Display relevant or popular posts to users based on followed tags or general popularity.
      - Sort by upvotes, recency, or a custom ranking algorithm.

   4. **Commenting & Real-Time Updates**  
      - Users can comment on posts, with nested or flat discussion threads.
      - Real-time comment updates or near real-time (long polling / WebSockets).

   5. **User Engagement Features**  
      - Voting (upvotes/downvotes), likes, or “helpful” flags.
      - Subscriptions/following for tags or individual posts.

   6. **High Scalability & Availability**  
      - Handle millions of daily active users, with spikes in traffic after big posts go viral.
      - Maintain low-latency access to content globally.

   ### 2. **High-Level Architecture**

   ```
             ┌───────────────┐
             │    Frontend    │
             └───────────────┘
                    |
           (HTTP / WebSockets)
                    v
         ┌────────────────────────┐
         │   API Gateway / LB     │
         └────────────────────────┘
                    |
                    v
   ┌────────────────────────────────────────────────┐
   │  Forum Service (Posts/Comments/Tags/Feeds)     │
   ├────────────────────────────────────────────────┤
   │  - Handles core logic for creating posts,      │
   │    comments, tagging, feed generation.         │
   └────────────────────────────────────────────────┘
        |            |                   |
        v            v                   v
   [ Relational  ] [ Cache ]        [ Search/Index ]
    [ DB (SQL)    ] (Redis)          (e.g., Elastic)
        |
     (Media Store?)
   ```

   ### **Frontend**
   - Could be web-based, mobile, or both.  
   - Uses REST/GraphQL APIs or WebSockets for real-time updates.

   ### **API Gateway / Load Balancer**
   - Distributes inbound requests across multiple backend instances.  
   - Handles authentication tokens and routes calls to microservices if the system is decomposed further.

   ### **Forum Service**
   - Central logic for creating/fetching posts, comments, tags, upvotes.  
   - Interacts with database(s) to store and retrieve data.

   ### **Database**
   - **SQL** (e.g., Postgres, MySQL) fits well for relational data:  
     - Tables: `users`, `posts`, `comments`, `tags`, `votes`, etc.  
     - Many-to-many relationships (posts ↔ tags, posts ↔ users, etc.).  
     - Transactions ensure data consistency (especially for votes and comments).
   - Consider **NoSQL** for certain high-volume data (e.g., logs, real-time analytics).

   ### **Caching Layer**
   - Caches hot content: popular posts, trending tags, etc.  
   - Redis or Memcached can handle fast key-value lookups.

   ### **Search & Indexing**
   - Elasticsearch or Solr for full-text search across posts, especially if you want robust search by content or tags.  
   - Facilitates advanced queries and text-based ranking.

   ### **Media / File Storage**
   - Images, attachments, or user-uploaded content stored in a separate service (e.g., Amazon S3, Google Cloud Storage).  
   - Store references/URLs in your main database.

   ### 3. **Data Model**

   Below is a possible minimal SQL schema outline:

   1. **users**  
      - `user_id (PK)`, `username`, `email`, `password_hash`, etc.

   2. **posts**  
      - `post_id (PK)`, `user_id (FK)`, `title`, `content`, `creation_time`, `score` (or upvote_count), etc.

   3. **comments**  
      - `comment_id (PK)`, `post_id (FK)`, `user_id (FK)`, `parent_comment_id` (null if top-level), `content`, `score`, `creation_time`.

   4. **tags**  
      - `tag_id (PK)`, `tag_name`.

   5. **post_tags**  
      - **Composite Key**: `(post_id, tag_id)`.

   6. **votes**  
      - **Composite Key**: `(voter_id, post_or_comment_id, type_of_vote)`.  
      - Could separate for post votes vs. comment votes or unify in one table with an extra column.

   **Note**: For real-time feed generation, you might store aggregated counters (e.g., upvote counts) in denormalized columns for fast retrieval.

   ### 4. **Key Workflows**

   ### 4.1 **Posting & Tagging**
   1. **User** submits a post with a title, body, and a list of tags.  
   2. **Forum Service** inserts the new post into the `posts` table, the relevant tags into `post_tags`.  
   3. **Search Index** (async) updates its index with the new post content.

   ### 4.2 **Commenting**
   1. **User** adds a comment (parent can be either the post or another comment).  
   2. **Forum Service** stores the comment and the parent relationship (tree or nested set).  
   3. **Real-time**: Uses WebSockets or SSE (Server-Sent Events) to notify other users viewing the same post.

   ### 4.3 **Feed Generation**
   1. **User** opens the homepage or a “tag feed.”  
   2. **Forum Service** queries posts sorted by recency, popularity, or a hybrid rank.  
   3. **Cache** popular items if necessary.  
   4. Return the list to the user.

   ### 4.4 **Voting / Like System**
   1. **User** clicks upvote/downvote.  
   2. **Forum Service** updates the `votes` table and increments/decrements `score` in the `posts` or `comments` table.  
   3. Real-time or near real-time feed refresh can show updated scores.

   ### 5. **Common Challenges & Solutions**

   1. **Massive Traffic & Load Balancing**  
      - Scale horizontally: multiple app server instances behind a load balancer.  
      - Leverage caching heavily (e.g., Redis) to store and quickly return frequently accessed posts.

   2. **Distributed Data Storage / Sharding**  
      - For **SQL**: shard by `user_id` or `post_id`.  
      - For **NoSQL**: choose a partition key for large tables, ensuring uniform distribution.

   3. **High-Volume Writes** (e.g., upvotes, comments)  
      - Might need asynchronous or batched updates to reduce write contention.  
      - Use message queues or event streams (Kafka) if processing large amounts of analytics or notifications.

   4. **Search & Indexing**  
      - Full-text search can be handled by an external engine (Elasticsearch) that updates indexes asynchronously.  
      - Ensures queries remain performant even with large text volumes.

   5. **ML-Based Tag Suggestions**  
      - As the system grows, incorporate a machine learning model to suggest relevant tags or to cluster similar content.  
      - This can be done offline using a data pipeline to build embeddings or classification models, then served via an inference endpoint.

   6. **Handling Media Uploads**  
      - Store images, videos, or attachments in object storage (S3).  
      - DB only stores file references/URLs.

   7. **Real-Time Notifications**  
      - For comments or answers, notify relevant users (post authors, watchers) using WebSockets, push notifications, or email.

   8. **Spam & Moderation**  
      - Tools to detect spam or abuse (keyword filters, user reputation scores).  
      - Possibly human moderators plus user-driven flagging system.

   ### 6. **Potential Enhancements**

   1. **Personalized Feeds**  
      - Use a recommendation engine to rank or personalize the feed based on user history, interests, and upvote patterns.

   2. **Content Ranking**  
      - Weighted algorithms like Reddit’s “hot” ranking formula or Hacker News’ ranking approach (time decay + upvote counts).

   3. **Offline Data Analysis**  
      - Leverage analytics pipelines (e.g., Apache Kafka + Spark/Flink) to glean insights about popular topics, user engagement, etc.

   4. **Microservices**  
      - Split out the monolith into specialized services: Feeds, Posts, Comments, Tag/Topic, Search, etc.

   ### 7. **Final Thoughts**

   Designing a forum-like system involves balancing **rich relational data**, **scalable read/write patterns**, and **real-time user interactions**. A combination of **SQL** for structured data, **NoSQL** or specialized search engines, and **caching** layers typically yields the performance needed. By carefully sharding data, implementing efficient real-time communication, and employing a robust ranking algorithm, you can support millions of users posting, commenting, and voting—while preserving a user-friendly experience.

   ---

   ## 18. Design Facebook's newsfeed system.
   Facebook's newsfeed allows users to see what is happening in their friend's circle, liked pages and groups followed.

   What are some of the Required Features?
   Generate newsfeed using posts from other system entities that the user follows.
   Newsfeed posts can be of text, image, audio or video format. Append new posts to the user's newsfeed in close to real-time.
   What are some of the Common Problems encountered?
   What happens if the new post sees a lot of latency to get appended to the news feed?
   Can the algorithm handle sudden user load?
   What posts should take priority for displaying in the news feed?
   Possible tips for consideration:
   Evaluate the process of fanout for publishing posts to the followers Check how sharding can be achieved e?iciently for handling heavy user
   load. The feed data of a user shouldn't be put into multiple servers. Instead, sharding can be done on user ids.

   Below is a concise yet robust outline for designing Facebook’s newsfeed system. We’ll explore how data flows from content creation to personalized feed generation, as well as strategies to handle heavy user load, real-time updates, and ranking logic.

   ### 1. **Core Requirements**

   1. **Real-time (or Near Real-time) Feed Updates**  
      - Users see new posts from their followed pages, groups, and friends almost instantly.

   2. **Rich Media Support**  
      - Posts can include text, images, videos, live streams, or links.

   3. **Scalable Feed Generation**  
      - Must handle hundreds of millions (or billions) of daily active users.

   4. **Ranking & Personalization**  
      - Decide which posts appear at the top of each user’s feed based on relevance (e.g., user interests, recency, engagement, etc.).

   5. **Low Latency**  
      - The system should remain performant even under sudden spikes in user load (e.g., viral posts).

   ### 2. **High-Level Architecture**

   There are two general paradigms for generating feeds at scale: **push-based (fan-out on write)** and **pull-based (fan-out on read)**. Facebook historically uses a hybrid approach—partial precomputation plus on-demand ranking to balance efficiency and personalization.

   ```
     ┌────────────────────┐          ┌────────────────────┐
     │  Content Creation  │          │   Feed Generation   │
     └────────────────────┘          └────────────────────┘
              |                            |
              v                            v
   ┌──────────────────────────┐     ┌─────────────────────────┐
   │  Post/Edge Data Storage  │ --> │  Feed Publishing Layer  │
   └──────────────────────────┘     └─────────────────────────┘
                                          |
                                          v
                              ┌─────────────────────────┐
                              │   Feed Retrieval API    │
                              └─────────────────────────┘
                                          |
                                          v
                                 ┌────────────────┐
                                 │  User Device   │
                                 └────────────────┘
   ```

   ### 2.1 **Content Creation (Producers)**
   - Friends, Pages, Groups create posts (text, images, videos).
   - Posts go into a **Post/Edge Data Store** (relational or NoSQL) that tracks:
     - Post metadata (creator, timestamp, visibility, etc.).
     - Engagement data (likes, shares, comments).

   ### 2.2 **Feed Generation Layer**
   - **Push/Fan-out**: When a user creates a post, the system “pushes” references to that post into the feeds of all followers. Useful when you have moderate user-fanout.
   - **Pull/Fan-out on Read**: Each user’s feed is computed on-demand by pulling relevant posts from the user’s network of friend/follow relationships. More efficient if the average user has a high fanout or if not all followers check frequently.

   ### 2.3 **Feed Publishing Layer**
   - Applies ranking algorithms, checks user’s privacy settings, and fetches top posts for each user.
   - Uses **caching** or partial precomputation to speed up feed assembly.

   ### 2.4 **Feed Retrieval API**
   - The client (mobile/web) calls this API to fetch the user’s newsfeed.
   - The service returns a curated list of posts, typically paginated (e.g., “load more” as user scrolls).

   ### 3. **Data Model**

   1. **Users**  
      - `user_id`, `name`, `relationships` (friends, groups joined, pages followed).
   2. **Posts**  
      - `post_id`, `author_id`, `content_type` (text, image, video), `timestamp`.
   3. **Edges** (Follow/Like/Group membership)  
      - `follower_id`, `followee_id`, or `(user_id, group_id)`.
   4. **Feed Entries**  
      - If using a push model: store references of `post_id`s in a feed collection keyed by `user_id`.
      - If using a pull model: no separate feed store—fetch recent posts from the user’s network on demand.

   **Note**: Facebook also maintains a specialized graph storage for user–page–group relationships, plus a dedicated indexing system for real-time queries.

   ### 4. **Key Workflows**

   ### 4.1 **Publishing a New Post**
   1. **User** creates a post.  
   2. **Post Service** stores it in the main data store with metadata (author, timestamp, etc.).  
   3. **Fan-out Logic**:
      - **Push (Fan-out-on-Write)**: The system identifies all followers and inserts a feed entry (`user_id`, `post_id`) for each.  
      - **Pull (Fan-out-on-Read)**: Just store the post in the data store. When followers request their feed, they query the relevant posts.

   ### 4.2 **Real-time Updates**
   - Use **WebSockets** or push notifications to notify users of new posts.  
   - If push-based, the feed is already updated in the DB. If pull-based, the client triggers a refresh call to fetch the latest.

   ### 4.3 **Feed Retrieval & Ranking**
   1. **User** opens their newsfeed.  
   2. **Feed Service** queries feed entries (push model) or fetches recent posts from user’s follows (pull model).  
   3. **Ranking**:
      - Evaluate each candidate post for relevance using signals like:
        - Post recency.
        - User affinity (prior interactions with the author/page).
        - Post engagement (likes, comments, shares).
        - Content type preference (video vs. text).
      - Sort posts based on a ranking score.  
   4. **Return** top-ranked posts to the client.

   ### 5. **Common Problems & Solutions**

   1. **High Latency in New Post Delivery**
      - **Push** approach: possible huge write overhead if a user has millions of followers (celebrity).  
      - **Pull** approach: possible high latency if you fetch on read.  
      - **Hybrid**: Precompute partial feed entries for frequently active users, while less active users pull on read.

   2. **Sudden User Load / Viral Posts**
      - **Caching** frequently accessed feed data.  
      - **Asynchronous queue** for fan-out updates.  
      - Break down feed generation logic into microservices to handle partial computations (e.g., a dedicated ranking microservice).

   3. **Post Ranking Priority**
      - Use a **machine learning ranking model** (EdgeRank was an early version at Facebook).  
      - Factor in recency, user engagement, content type, and user’s past interactions.  
      - Possibly store or compute real-time engagement stats for weighting.

   4. **Sharding & Partitioning**
      - Common approach: **shard by user_id**.  
      - Keep each user’s feed data on a single shard to avoid cross-shard queries.  
      - For push-based, the feed entries for a user go into the same shard as the user.

   5. **Privacy & Visibility**
      - Ensure only posts that the user has permission to see are shown.  
      - Could store privacy settings in an access control list or in the user–post relationship.  
      - The feed service must filter out unauthorized posts.

   6. **Media Storage & Delivery**
      - Large media (images/videos) stored in a separate distributed storage (e.g., CDN like Akamai, AWS S3).  
      - The feed only references media URLs, reducing DB load.

   ### 6. **Potential Enhancements**

   1. **Feed Personalization**  
      - Advanced ML-based algorithms to personalize feed order (time-decay, collaborative filtering, content-based filtering).
   2. **Notifications**  
      - Real-time push notifications for important posts or user interactions.
   3. **Offline Processing**  
      - Use data pipelines (Kafka, Spark, Flink) to feed user activity logs into machine learning models for relevance scoring, spam detection, etc.
   4. **Content Moderation**  
      - Automated systems to detect inappropriate or harmful posts, plus user-reporting and moderator review tools.

   ### 7. **Final Thoughts**

   Designing a Facebook-style newsfeed at massive scale involves **balancing real-time fan-out, feed ranking, and storage complexity**. A hybrid push–pull solution often offers the best trade-off:
   - **Push** for moderately connected users, keeping their feeds fresh.  
   - **Pull** for highly connected “celebrity” users to avoid enormous fan-out writes.  

   By incorporating **sharding** strategies, **caching**, and **asynchronous** data pipelines, the system can handle huge user bases and remain performant, ensuring users get relevant, up-to-date content with minimal latency.

   ---

   ## 19. Design a parking lot system?
   What are some of the Required Features?
   The parking lot can have multiple levels where each level has multiple rows for parking spots.
   The parking lot can support parking for cars, buses, motorcycles hence spots can be of multiple sizes.
   Consider the parking lot capacity at the time of designing the system. Design appropriate pricing for each parking spot.
   What are some of the Common Problems encountered?
   What should happen to the parking lot system if every spot is occupied? Assigning parking lot spots of smaller size to vehicles of a bigger size.
   Possible tips for consideration:
   Think of an algorithm for assigning an appropriate parking spot to a vehicle.
   Think of different entities required for designing the system.

   ### 1. **Core Requirements**

   1. **Multiple Levels and Rows**  
      - Each level has multiple rows.  
      - Each row has multiple parking spots.  

   2. **Vehicle Types**  
      - Different spot sizes to accommodate **motorcycles**, **cars**, and **buses** (or trucks).  
      - Must ensure a larger vehicle doesn’t occupy a smaller spot.

   3. **Capacity Management**  
      - Track the number of available spots per level (and possibly per row).  
      - If all spots are filled, new arrivals must be turned away or wait (if a waiting/queuing system is allowed).

   4. **Pricing**  
      - Different rates for different vehicle types or different spot sizes.  
      - Optionally support dynamic pricing or time-based fees.

   5. **Basic Operations**  
      - **Enter** the parking lot:  
        - Find an appropriate spot for the vehicle.  
        - Generate a ticket if a spot is found; otherwise, indicate the lot is full.  
      - **Exit** the parking lot:  
        - Calculate parking fee based on duration (and/or vehicle type).  
        - Mark the spot as available again.

   ### 2. **High-Level Architecture**

   ```
   ┌─────────────────────────┐
   │  Entrance Kiosk/System  │
   └─────────────────────────┘
             |   (Vehicle info)
             v
   ┌─────────────────────────┐
   │  ParkingLot Controller  │
   │ (Assignment, Capacity)  │
   └─────────────────────────┘
            |        \
            |         \
            v          v
   ┌─────────────────────────┐   ┌─────────────────────────────┐
   │ Spot/Level Mgmt Service │   │ Pricing & Payment Service    │
   │   (DB of spots, status) │   └─────────────────────────────┘
   └─────────────────────────┘
             |
             v
   ┌─────────────────────────┐
   │ Parking Spot Database   │
   └─────────────────────────┘
   ```

   - **Entrance Kiosk/System**: Where a driver enters vehicle info or where the system automatically scans license plates.
   - **ParkingLot Controller**: Core logic that determines if a spot is available. Interacts with spot-level data to update status, create tickets, etc.
   - **Pricing & Payment Service**: Calculates fees. Could be integrated into the controller or exist as a separate microservice.
   - **Spot/Level Mgmt**: Database or in-memory representation of each spot on each level, its size/type, and whether it’s free or occupied.

   ### 3. **OOP Entities (Conceptual)**
   A typical approach is to define classes that represent each logical component:

   1. **ParkingLot**  
      - Attributes: `levels`, `address`, `totalSpots`.  
      - Methods:  
        - `getAvailableSpot(vehicleType) → ParkingSpot?`  
        - `updateAvailability(levelId, spotId, status)`  
        - `calculateFee(ticket)`  

   2. **Level**  
      - Attributes: `levelNumber`, `rows`, `availableSpotsCount`.  
      - Methods:  
        - `findSpotForVehicle(vehicleType) → ParkingSpot?`  
        - `updateSpot(spotId, isOccupied)`  

   3. **Row** (optional granularity)  
      - Attributes: `rowNumber`, `spots[]`.  
      - Method: `findSpot(vehicleType) → ParkingSpot?`  

   4. **ParkingSpot**  
      - Attributes:  
        - `spotId`, `size` (SMALL, MEDIUM, LARGE), `isOccupied`, `levelNumber`, `rowNumber`.  
      - Methods:  
        - `canFitVehicle(vehicle) → boolean`  
        - `assignVehicle(vehicle)`  
        - `removeVehicle()`  

   5. **Vehicle**  
      - Abstract Class: `Vehicle` (attributes: `licensePlate`, `vehicleType`).  
      - Subclasses: `Car`, `Motorcycle`, `Bus`.

   6. **Ticket**  
      - Attributes: `ticketId`, `vehicle`, `spot`, `entryTime`, `exitTime`, `paymentDue`.  
      - Methods: `calculateDuration()`, `updateExitTime()`, etc.

   7. **Payment**  
      - Could be integrated into the `Ticket` or a separate class.  
      - Stores payment method, amount, timestamp, etc.

   ### 3.1 **Assigning Spots**

   - **Algorithm**: 
     1. When a vehicle arrives, identify the smallest available spot type that can accommodate it (e.g., a `Car` can fit in a MEDIUM or LARGE spot, a `Bus` needs LARGE, etc.).  
     2. Search from the first level to the last, or use an index that tracks free spots for each spot size.  
     3. Once found, mark it as occupied, generate a `Ticket`.

   - **Why the smallest possible spot?**  
     - This helps maintain efficiency so that a SMALL spot is not wasted on a motorcycle if bigger vehicles are waiting.  
     - Implementation can vary based on business logic.

   ### 4. **Key Workflows**

   ### 4.1 **Vehicle Entering**
   1. **User/Driver** arrives at the entrance, provides or is scanned for:  
      - Vehicle type, license plate.  
   2. **System**:  
      - Searches for an available spot (via the `ParkingLot` or `Level` method).  
      - If found, **reserve/occupy** the spot, create a `Ticket`.  
      - Print or display the ticket info (spot location, time).

   ### 4.2 **Vehicle Parking**  
   - The driver proceeds to the designated spot.  
   - **System** might show signage or have an automated gate that lifts if the user has a valid assignment.

   ### 4.3 **Vehicle Exiting**  
   1. **Driver** presents the ticket at an exit kiosk or an automatic gate scans the license plate.  
   2. **System** calculates total fee based on entry time, exit time, or vehicle type-based pricing (or a combination).  
   3. **Driver** pays the fee.  
   4. **System** marks the spot as free and updates the capacity counters.

   ### 5. **Pricing Considerations**

   1. **Flat Rate vs. Hourly**  
      - A simple approach is a per-hour charge.  
      - Some systems do the first 30 minutes free, then incremental rates.

   2. **Vehicle-based**  
      - Different rates for car, bike, bus.  
      - Possibly combine with time-based or daily rate.

   3. **Dynamic or Peak Pricing** (Optional)  
      - Increase the cost if the lot is nearing capacity to encourage turnover.

   ### 6. **Common Problems & Solutions**

   1. **Lot is Full**  
      - If **no spot** can accommodate the arriving vehicle, the system rejects entry (shows “Lot Full”).  
      - Could maintain a **waiting queue** if required (beyond scope of many designs).

   2. **Assigning Smaller Spots to Bigger Vehicles**  
      - The `canFitVehicle()` method in `ParkingSpot` ensures a bigger vehicle cannot park in a smaller spot.  
      - This check is performed before a vehicle is assigned.

   3. **Concurrent Access**  
      - Two vehicles arrive at the same time for the last available spot.  
      - Use synchronization (in-memory locks, DB locks, or an atomic check) to ensure only one ticket is issued for a single spot.

   4. **Keeping Track of Levels, Spots**  
      - Maintain in-memory data structures (maps) keyed by `spotId` or `(level, row, spotIndex)` for quick lookups.  
      - Update them in real-time upon assignment and release.

   5. **System Scalability**  
      - Typically not extremely large scale (it’s a single parking lot).  
      - But if you manage a network of parking lots, you could distribute them, each with its own database, and unify via an aggregator service.

   ### 7. **Possible Extensions**

   1. **Multiple Exits / Entrances**  
      - Each entrance updates a shared data store on capacity.  
      - Consistent concurrency checks to avoid double-booking spots.

   2. **Automated Payment & Gates**  
      - Integrate automatic license plate recognition for frictionless entry/exit.

   3. **Online Booking & Reservations**  
      - Drivers reserve a spot in advance. The system blocks that spot from walk-ins.

   4. **Electric Vehicle (EV) Charging Spots**  
      - Additional metadata for spots that have charging stations.

   5. **Integration with Mobile App**  
      - Let users see real-time occupancy, directions to an available spot, or manage their tickets from a smartphone.

   ### 8. **Example Flow**

   1. **Car Arrives**  
      - Kiosk sees a `Car`.  
      - `ParkingLot` queries the system for an available `MEDIUM` or `LARGE` spot on any level.  
      - The system finds `Level 1, Spot #15 (MEDIUM)` is free.  
      - A `Ticket` is created with `entryTime=9:00 AM`, `spotId=15`, `vehicleType=Car`.  
   2. **Car Exits** at `10:30 AM`  
      - System calculates `1.5 hours * Car Rate`.  
      - The driver pays; the system marks `Spot #15` as free.  
   3. **Lot Capacity** updated accordingly.

   ### **Final Thoughts**
   A well-designed parking lot system combines straightforward spot assignments, real-time occupancy tracking, and a flexible pricing model. It’s typically structured with **classes** representing the lot, levels, spots, and vehicles, plus a central controller for assignment logic. The solution is robust to concurrency, ensures correct vehicle-to-spot matching, and supports varied pricing strategies.

   ---

   ## 20. How do you design a recommendation system?
   Recommendation systems are used for helping users identify what they want e?iciently by assisting them by o?ering various choices and alternatives based on their history or interests.
   What are some of the Required Features?
   Discuss what kind of recommendation system is required - whether it is for movies, e-commerce websites, songs etc.
   What are some of the common problems encountered?
   Figure out how to recommend fresh and relevant content in real-time.
   Possible tips for consideration:
   Discuss how to use the Eval component for understanding the working of the system.
   Discuss how to train a collaborative filtering approach.

   ### 1. **Core Questions / Requirements**

   1. **Domain & Data**  
      - Are we recommending movies, products, songs, or something else?  
      - What data do we have? (user behavior logs, ratings, clicks, watch time, purchase history, etc.)

   2. **Real-Time vs. Batch Recommendations**  
      - Must the system generate recommendations in near real-time (e.g., “people also bought…”)?  
      - Or can it be updated periodically (e.g., daily offline processing)?

   3. **Personalization**  
      - To what extent should recommendations differ by user?  
      - Are we providing purely personalized suggestions or also popular/trending items?

   4. **Scalability & Throughput**  
      - How many users/items do we have?  
      - How frequently are we adding new items or receiving new user interactions?

   ### 2. **High-Level Architecture**

   ```
   ┌─────────────┐    ┌─────────────────────────┐
   │ User Device  │ -> │  Recommender Service    │
   └─────────────┘    └─────────────────────────┘
                         |           ^
         (API requests)  |           |
                         v           |
               ┌────────────────────────────────┐
               │  Model(s):                    │
               │  - Collaborative Filtering    │
               │  - Content-Based Filtering    │
               │  - Hybrid / Deep Learning     │
               └────────────────────────────────┘
                         |
                         v
        ┌───────────────────────────────────────────┐
        │ Data Pipeline / ETL / Feature Engineering │
        └───────────────────────────────────────────┘
                         |
                         v
           ┌─────────────────────────────────┐
           │ Datastore(s):                   │
           │  - User Behavior Logs           │
           │  - Item Metadata                │
           │  - Ratings / Interactions       │
           └─────────────────────────────────┘
   ```

   1. **Data Collection & Storage**  
      - Collect user interactions (clicks, views, purchases, ratings, watch time, etc.) in a scalable system (e.g., Kafka for streaming, Hadoop or cloud data warehouse for storage).  
      - Store item metadata (title, genre, tags, etc.).

   2. **Feature Engineering & ETL**  
      - Batch or streaming pipelines transform raw logs into features needed by the recommender models (e.g., user-item rating matrices, user profiles, item embeddings).

   3. **Modeling**  
      - Typically involves collaborative filtering (CF), content-based filtering (CBF), or a hybrid approach.  
      - May use matrix factorization (e.g., ALS, SVD), neighborhood-based methods, or deep neural networks.

   4. **Serving Layer (Recommender Service)**  
      - Exposes APIs for “Get top N recommendations for user X.”  
      - Uses the trained model(s) to retrieve or score items for each user on-demand.  
      - May incorporate caching to reduce inference latency (store precomputed recommendations for each user).

   ### 3. **Types of Recommendation Techniques**

   1. **Collaborative Filtering**  
      - Leverages user-to-item interaction patterns.  
      - **User-based** CF: “Users similar to you liked…”  
      - **Item-based** CF: “Items similar to what you liked…”  
      - **Model-based**: Matrix factorization or latent factor models.

   2. **Content-Based Filtering**  
      - Uses item attributes (genre, description, text, tags) and user profiles (keywords, preferences).  
      - Recommends items whose attributes match a user’s past consumed items.

   3. **Hybrid Approaches**  
      - Combine CF and CBF, or incorporate additional signals (popularity, user demographics, time decay).  
      - Often used to handle cold-start items or new users where data is sparse.

   4. **Deep Learning / Representation Learning**  
      - Embedding-based models (e.g., YouTube’s DNN for candidate generation, wide-and-deep networks).  
      - Can handle large-scale, complex data (images, text) and discover intricate patterns.

   ### 4. **Training & Evaluation**

   1. **Data Split**  
      - Typically a train/validation/test split.  
      - For time-sensitive data (like user interactions), ensure the splits respect chronological order.

   2. **Collaborative Filtering Training**  
      - If using matrix factorization:  
        - **Implicit feedback** approach (e.g., purchase or not) or **explicit feedback** (ratings).  
        - Optimize a loss function (e.g., MSE or logistic loss).

   3. **Eval Component**  
      - **Offline Metrics**: Root Mean Square Error (RMSE), Mean Average Precision (MAP), Normalized Discounted Cumulative Gain (NDCG), Recall@K, etc.  
      - **Online Metrics** (A/B Testing): Click Through Rate (CTR), conversion rate, watch time.

   4. **Cold Start**  
      - New user with no history: default to popular items or content-based approach (user’s stated interests).  
      - New item with no interactions: rely on item metadata or short-term promotion logic.

   ### 5. **Real-Time vs. Batch Updates**

   1. **Batch**  
      - Periodically (daily or hourly) retrain the model on new data, then push updated embeddings or recommendations to the serving layer.  
      - Simpler to implement but less responsive to immediate user actions.

   2. **Real-Time / Near Real-Time**  
      - Use streaming data pipelines (e.g., Kafka, Spark Streaming) to update user/item profiles quickly.  
      - Models can partially update via online learning or re-ranking algorithms that adapt to newly collected user behavior.

   ### 6. **Common Problems & Solutions**

   1. **Data Sparsity**  
      - If most items aren’t rated frequently, CF models may struggle.  
      - Solution: incorporate item content features, popularity signals, or cross-user similarities.

   2. **Scalability**  
      - Large user–item matrices can be huge (millions of users × millions of items).  
      - Use efficient factorization techniques, distributed computation frameworks (Spark, GPU-based training), approximate nearest neighbor search for item retrieval.

   3. **Cold Start**  
      - New users: gather minimal information (user registration questionnaire, or top trending items).  
      - New items: rely on item attributes and initial user feedback (content-based or short “exploration” period).

   4. **Filter Bubbles / Diversity**  
      - Recommenders can trap users in narrow content.  
      - Include diversity constraints or explore new items in addition to purely “most relevant” ones.

   5. **Latency**  
      - Serving recommendations must be fast (sub-100ms).  
      - Precompute top candidates per user or leverage a fast approximate nearest neighbor (ANN) index.

   ### 7. **Example Workflow: Movie Recommendation**

   1. **Collect Data**: Users’ watch histories, ratings, reviews, and metadata about each movie.  
   2. **Feature Engineering**: Build a user–movie rating matrix, or gather user embeddings from watch logs (e.g., watch time).  
   3. **Train Model**:  
      - Collaborative filtering with matrix factorization or a neural approach.  
      - Possibly incorporate movie attributes (genre, actors) for content-based signals.  
   4. **Evaluate**:  
      - Use offline metrics (NDCG, Recall@K) on a hold-out set.  
      - Validate improvements with online A/B testing on a subset of users.  
   5. **Serve**:  
      - An API that, given a user ID, returns top movies.  
      - Possibly combine real-time signals (e.g., a user just watched an action movie) to re-rank the initial recommendation list.

   ### 8. **Final Thoughts**

   Building an effective recommendation system often involves:
   - **Data collection and cleaning** at scale.
   - **Choosing the right modeling technique** (collaborative filtering, content-based, hybrid).
   - **Handling real-world constraints** like latency, cold start, and data sparsity.
   - **Iterative evaluation** using both offline metrics and A/B testing for real-world performance.

   ---

   ## 21. Design an API Rate Limiter system for GitHub or Firebase sites.
   API Rate Limiters limit the API calls that a service receives in a given time period for avoiding request overload. This question can start with the coding algorithm on a single machine to the distributed network.

   What are some of the Required Features?
   What is the required request count per hour or second? Let us assume that the requirement can be 10 requests per second.
   Should the limiter notify the user if the requests are blocked? The limiter should handle traffic suitable according to the scale.
   What are some of the common problems encountered?
   How to measure the requests per given time?
   How to design the rate limiter for the distributed systems when compared to a local system?
   Possible tips for consideration:
   Evaluate the usage of sliding time windows for avoiding hourly resets. Try using a counter integer instead of a request for saving space.

   ### 1. **Core Requirements**

   1. **Fixed Request Quota**  
      - For example, a user or client can make **10 requests per second** or 1000 requests per hour.

   2. **Throttling Behavior**  
      - If a user exceeds the rate limit, subsequent requests are blocked (HTTP 429 Too Many Requests), or delayed (in the case of a token-bucket approach).

   3. **Notification**  
      - The system should return a clear error or status code when requests are throttled to inform the client they have hit the limit.

   4. **Scalability**  
      - The solution must handle traffic from thousands or millions of users in real time.  
      - Work across multiple servers if needed.

   ### 2. **Local (Single-Machine) Design**

   When all requests are processed by a single node:

   ### 2.1 **Data Structures**

   1. **Counters or Buckets**  
      - For each user (or IP, API key), store the current count of requests in the current time window.

   2. **Time Window Approaches**  
      - **Fixed Window**: Each window is a fixed duration (e.g., 1 second). All requests in that window count toward the limit.  
      - **Sliding Window**: A more precise approach that counts requests over the last X seconds (constantly updated).  
      - **Token Bucket or Leaky Bucket**: Request tokens accumulate at a constant rate; each request consumes a token.

   ### 2.2 **Algorithm Examples**

   1. **Fixed Window Counter**  
      - Keep a map `{ user_id -> (window_start, request_count) }`.  
      - If the current time is within `window_start + 1s` (or whichever interval), increment `request_count`. If it exceeds the limit, block.  
      - Reset `window_start` and `request_count` when moving to the next window.  
      - **Issue**: Creates possible “burst” usage around boundary edges.

   2. **Sliding Window Counter**  
      - More accurate. For each request, record its timestamp in a queue/list. Remove timestamps older than the window size (e.g., 1 second).  
      - If the queue size exceeds the limit, block.  
      - **Space**: Potentially large if many requests. Storing only timestamps might be memory-heavy if not carefully managed.

   3. **Token Bucket**  
      - A bucket refills tokens at a fixed rate (e.g., 10 tokens/sec).  
      - Each request consumes a token if available. If no token is available, block.  
      - Ensures even distribution while allowing short bursts up to the bucket capacity.

   ### 2.3 **Implementation Notes**

   - Use an **in-memory** store (e.g., a dictionary or a local cache) if you only have one server.  
   - For **thread safety**, use locks or atomic operations to increment counters.  
   - Return HTTP 429 if the rate limit is hit, optionally with headers indicating next available time.

   ### 3. **Distributed Design**

   In a multi-server setup (common for large APIs like GitHub or Firebase):

   ### 3.1 **Challenges**

   1. **Consistency**: Multiple servers must coordinate user request counts.  
   2. **Latency**: Each request rate-limit check must be fast, typically under a few milliseconds.  
   3. **Scalability**: The data store for counters must handle high concurrency and throughput.

   ### 3.2 **Common Approaches**

   1. **Centralized Data Store**  
      - Use Redis or a similar in-memory datastore that’s accessible by all servers.  
      - Each server increments the user’s counter or token bucket in Redis.  
      - Redis supports atomic operations (like `INCR`, `DECR`, Lua scripts) to ensure consistency.

   2. **Distributed Cache / Database**  
      - If Redis or Memcached is not an option, use a sharded NoSQL store like Cassandra. But typically, Redis is preferred for rate-limiting due to low latency and atomic increments.

   3. **Local Caches + Periodic Sync**  
      - Each server maintains a partial local cache. Periodically sync or reconcile with a central authority.  
      - Risk of short-term inconsistencies if a user hits different servers quickly.

   ### 3.3 **Algorithm Variations (Distributed)**

   1. **Token Bucket in Redis**  
      - Each user’s bucket is stored as a key in Redis.  
      - The bucket “refills” at a known rate. Implementation usually involves storing the last refill time and the current token count in the key’s value.  
      - On each request, you run a Lua script or a pipeline:
        1. Compute how many tokens to add based on elapsed time.  
        2. Increment the token count up to a maximum capacity.  
        3. If any tokens remain, consume one and allow the request; otherwise, block.

   2. **Sliding Window with Atomic Increments**  
      - Store a sorted set of timestamps or a running counter for each user in Redis.  
      - The set or list approach can become large if you have extremely high request volumes. Prune old entries.  
      - Alternatively, store a rolling count in short intervals (e.g., each second in a small histogram).

   ### 3.4 **Performance & Optimization**

   - **Shard** keys by user ID if traffic is very large, or distribute across multiple Redis clusters.  
   - Use **pipelines** or **Lua scripts** to avoid multiple round trips for each request check.  
   - For extremely large scale, consider an **approximate** approach, e.g., combining a minimal perfect hash with counters.

   ### 4. **Notification & User Feedback**

   - Return **HTTP 429 (Too Many Requests)** or a relevant error code.  
   - Optionally include `Retry-After` header to indicate when the client can safely retry.  
   - Provide a user-friendly message, e.g., “You have exceeded 10 requests per second. Please slow down.”

   ### 5. **Monitoring & Metrics**

   - Track how often users are throttled.  
   - Log rate-limit violations, which can help identify abusive patterns or misconfigured clients.  
   - Provide usage dashboards or API usage stats to users if needed (e.g., GitHub’s personal token usage page).

   ### 6. **Common Problems & Solutions**

   1. **Edge Cases with Fixed Windows**  
      - Users can “burst” at the boundary, making the limit effectively higher over short periods.  
      - **Solution**: sliding window or token bucket for smoother enforcement.

   2. **Spikes in Traffic**  
      - If many users exceed limits simultaneously, the system might generate many 429 responses.  
      - **Solution**: ensure enough server capacity to handle normal traffic and carefully set thresholds.

   3. **Synchronization Delays**  
      - In a distributed system, server A might let a request pass after incrementing a counter, but server B might not see it immediately if replication or syncing is slow.  
      - **Solution**: rely on a single authoritative store (like Redis) with atomic operations to ensure immediate consistency on the rate-limiting counters.

   4. **Overhead / Latency**  
      - Checking and updating counters for every request can add overhead.  
      - **Solutions**: local caching for short intervals, minimal scripts in Redis, batching requests in certain scenarios.

   ### 7. **Illustrative Example: Token Bucket in Redis**

   1. **Setup**:  
      - Each user has a Redis key: `ratelimit:{user_id}` storing `last_refill_timestamp` and `current_tokens`.

   2. **On Each Request**:  
      - Compute `now = current_time()`.  
      - `elapsed = now - last_refill_timestamp`.  
      - **tokens_to_add** = `(elapsed * refill_rate)`.  
      - `new_token_count = min(current_tokens + tokens_to_add, max_capacity)`.  
      - If `new_token_count > 0`,  
        - `new_token_count -= 1` (consume one token).  
        - request is **allowed**.  
      - Otherwise, request is **blocked** (HTTP 429).  
      - Update `ratelimit:{user_id}` in Redis with new values.

   3. **Implementation**:  
      - Typically done via a **Lua script** or atomic pipeline to ensure read-modify-write is consistent.

   ### 8. **Final Thoughts**

   A well-designed Rate Limiter:

   1. Uses **atomic, low-latency data stores** (like Redis) to keep track of counters or tokens.  
   2. Chooses a window or bucket algorithm that matches the usage pattern.  
   3. Returns clear feedback (HTTP 429 + headers) when clients are throttled.  
   4. Scales horizontally by distributing counters across multiple shards if needed.  

   ---

   ## 22. How do you design global file storage and file sharing services like Google Drive, Dropbox etc?
   What are some of the Required Features?
   Users should be able to upload, delete, share and download files over the web.
   File updates should be synced across multiple devices.
   What are some of the common problems encountered?
   Where to store the files?
   How can you handle updates? Should the files be re-uploaded or does just the modified version has to be updated?
   How to handle updation of two documents at the same time?
   Possible tips for consideration:
   Consider using chunking for splitting files into multiple sections for supporting re-uploads of a particular section rather than the whole file. Make use of cloud storage for storing the files.

   ### 1. **Core Requirements**

   1. **File Management**  
      - **Upload**: Users can upload files via web or mobile apps.  
      - **Delete**: Users can delete their files.  
      - **Download**: Users can retrieve their files or shared files.  
      - **Share**: Users can grant view/edit access to others.  

   2. **Synchronization**  
      - Edits should propagate across devices in near real-time (or at least swiftly).  
      - Maintain version history to roll back changes if needed.

   3. **Scalability & Global Accessibility**  
      - Handle potentially billions of files from millions of users.  
      - Low-latency access across different regions, possibly via edge caches or multi-region architecture.

   4. **Concurrent Updates & Version Control**  
      - Prevent or gracefully resolve conflicts when multiple clients edit the same file simultaneously.  
      - Possibly keep a versioning system or a conflict resolution mechanism.

   5. **Security & Privacy**  
      - Encrypted storage at rest (server-side encryption).  
      - Encrypted transmission (HTTPS/TLS).  
      - Access control (ACLs or sharing permissions).

   6. **Reliability & Durability**  
      - Files must be safely stored even if multiple datacenters have issues.  
      - Automatic backups, replication, or erasure coding to prevent data loss.

   ### 2. **High-Level Architecture**

   ```
       ┌───────────────┐
       │  User Device  │
       └───────────────┘
              |   (API calls / sync client)
              v
     ┌─────────────────────────┐
     │  Gateway / Load Balancer│
     └─────────────────────────┘
              |
              v
     ┌───────────────────────────────┐
     │ File Service (Metadata, ACLs) │
     └───────────────────────────────┘
              |
              v
    ┌─────────────────────────────────┐
    │ File Storage (Object Store)     │
    │   - Chunking, Replication, etc. │
    └─────────────────────────────────┘
              |
              v
      ┌─────────────────────────┐
      │  Sync/Change Service    │
      │ (notifications, updates)│
      └─────────────────────────┘
   ```

   1. **User Device**  
      - Can be a web browser, mobile app, or desktop sync client.  
      - Communicates with the backend via REST/GraphQL/WebSockets.

   2. **Gateway / Load Balancer**  
      - Distributes incoming requests among multiple backend instances for horizontal scaling.

   3. **File Service (Metadata & ACLs)**  
      - Responsible for storing file metadata (filename, size, version, owner, permissions).  
      - Tracks who can access/edit files.  
      - Could be backed by a relational DB (e.g., MySQL/Postgres) or a NoSQL DB (e.g., DynamoDB).

   4. **Object Store for Actual File Data**  
      - Files are stored in a distributed storage system (e.g., AWS S3, Google Cloud Storage, or an on-premises solution).  
      - Supports chunked uploads, replication, and data durability.

   5. **Sync/Change Notification Service**  
      - Monitors updates.  
      - Notifies relevant clients (WebSockets, push notifications, or polling) that a file has changed or a new version is available.

   ### 3. **Data Model & Chunking**

   ### 3.1 **Metadata Storage**

   - **Files Table**  
     - `file_id`, `owner_id`, `file_name`, `created_at`, `updated_at`, `size`, `version_count`, `current_version_id`  
   - **Versions Table**  
     - `version_id`, `file_id`, `version_number`, `timestamp`, `chunks[]` (list of chunk references), `checksum`  
   - **ACL / Sharing Table**  
     - `(file_id, shared_with_user_id, access_level)`  

   ### 3.2 **Chunking**

   - Large files are split into chunks (e.g., 4 MB each).  
   - Only changed chunks need to be re-uploaded or downloaded.  
   - Minimizes network usage for incremental changes.  
   - Each chunk stored in an object store with a unique ID or path.

   ### 4. **Key Workflows**

   ### 4.1 **File Upload**

   1. **Initiate Upload**: Client requests an upload token or session from the File Service.  
   2. **Chunking**: The client splits the file into smaller chunks.  
   3. **Upload Chunks**: The client uploads each chunk to the object store (S3 or equivalent) using pre-signed URLs or direct API calls.  
   4. **Assemble Metadata**: The File Service saves the file metadata (including chunk references).  
   5. **Publish Event**: The Sync Service notifies other devices that a new file or version is available.

   ### 4.2 **File Download / Sync**

   1. **Request File**: Client requests the file from the File Service.  
   2. **Retrieve Chunk Info**: The service returns chunk locations and version details.  
   3. **Download Chunks**: The client downloads each chunk from the object store.  
   4. **Assemble File** locally.  
   5. **Sync**: If changes occur while downloading, the client might fetch newer chunks or versions.

   ### 4.3 **Sharing a File**

   1. **User** grants access to another user (or group).  
   2. **File Service** updates the ACL / Sharing table with read or edit permissions.  
   3. **Sync Service** can notify that a new file is shared with the user.

   ### 4.4 **Concurrency & Conflict Resolution**

   - If two users edit the same file simultaneously, either:  
     1. **Locking**: The service locks the file or merges changes. (Used in collaborative editing, with real-time merges, e.g., Google Docs).  
     2. **Versioning**: The service creates separate versions. The system (or user) merges them later, or flags a conflict (“conflicted copy”).  

   ### 5. **Synchronization & Real-Time Updates**

   1. **Notification**:  
      - Use a pub/sub system (e.g., Kafka, RabbitMQ, or cloud-based service) to dispatch “file updated” events.  
      - Clients subscribe to these events, refreshing their local state upon receiving updates.

   2. **Local Caching & Conflict Handling**  
      - The client may maintain a local cache of the user’s files.  
      - If offline changes occur, they sync back to the server next time the device is online.

   3. **Partial Sync**  
      - For large file sets, clients may only sync certain folders to save bandwidth and storage.

   ### 6. **Scaling & Distribution**

   1. **Multi-Region Object Stores**  
      - Leverage cloud providers’ multi-region replication or content delivery network (CDN) for quick global file access.

   2. **Load Balancing & Horizontal Scaling**  
      - Multiple application servers behind a load balancer handle metadata queries.  
      - Use microservices to split concerns (e.g., separate metadata service from a sync service).

   3. **CDN for Download Optimization**  
      - Particularly for static or read-heavy files (e.g., videos, images).  
      - Minimizes latency by serving files from edge nodes.

   4. **Indexing & Search** (Optional)  
      - If advanced search is needed, store metadata or file content indexes in systems like Elasticsearch or Solr.

   ### 7. **Security Considerations**

   1. **Authentication & Authorization**  
      - Users must authenticate (OAuth, SSO, etc.).  
      - The file service checks ACL or permissions for read/write access.

   2. **Encryption**  
      - **In Transit**: All traffic is HTTPS/TLS.  
      - **At Rest**: Server-side encryption in the object store (cloud KMS keys) or end-to-end encryption for zero-knowledge solutions.

   3. **Audit Logs**  
      - Track all file access, sharing changes, deletions for compliance or debugging.

   ### 8. **Common Problems & Solutions**

   1. **Large File Uploads / Timeouts**  
      - Solve via chunked uploads with resumable functionality.  
      - If a chunk fails, only re-upload that chunk.

   2. **Simultaneous Edits**  
      - Basic approach: create multiple versions or a “conflicted copy.”  
      - Advanced approach (Google Docs style): real-time collaborative editing with operational transforms or CRDTs to merge changes.

   3. **Metadata Scalability**  
      - Storing billions of files requires a carefully partitioned or sharded metadata DB.  
      - Use user ID or file ID as partition/shard key to balance load.

   4. **Deleted Files & Restore**  
      - Keep a trash / recycle bin for soft deletes, store them for X days.  
      - Maintain older versions for accidental overwrites.

   ### 9. **Example Workflow**

   1. **User Uploads File**:  
      - Splits into chunks, each chunk sent to object store.  
      - File Service records a new file entry with chunk references.  

   2. **Another User Gets Access**:  
      - The owner updates ACL to share with user B.  
      - Sync service notifies user B’s devices that a new file is accessible.  

   3. **User B Edits**  
      - A new version is created if changes are made.  
      - The original file version is still preserved in history.  

   4. **Conflicts**  
      - If user A was editing offline and user B online, the next sync might detect a mismatch.  
      - The system either merges automatically or keeps both versions labeled.  

   ### 10. **Final Thoughts**

   Designing a global file storage/sharing service requires careful attention to:

   1. **Scalable Object Storage**: For storing file contents reliably with replication or erasure coding.  
   2. **Robust Metadata Management**: For tracking file ownership, versions, and sharing permissions.  
   3. **Sync & Conflict Resolution**: Handling concurrent edits gracefully.  
   4. **Security & Privacy**: Ensuring all data is encrypted and properly access-controlled.  

   ---

   ## 23. Design a type-ahead search engine service.

   This service partially completes the search queries by displaying n number of suggestions for completing the query that the user intended to search.
   What are some of the Required Features?
   Service has to match partial queries with popularly searched queries. The system has to display n number of suggestions (say 5, for example) based on the written query.
   The suggestions have to be updated based on the query updation.
   What are some of the common problems encountered?
   How to update the suggestions without much latency? How to determine the most likely suggestion?
   Are the suggestions adapting to the user's search results?
   When do the suggestions appear? Is it updated on the fly or once the user stops writing?
   Possible tips for consideration:
   Evaluate the usage of natural language processing for anticipating the next characters.
   Markov chain rule for ranking the probabilities of top queries.

   ### 1. **Core Requirements**

   1. **Real-Time Suggestions**  
      - Display suggestions as the user types.  
      - Must be low latency (e.g., sub-100ms response).

   2. **Popular Query Ranking**  
      - Show frequently searched or high-probability queries first.  
      - Possibly adapt suggestions based on user behavior or context.

   3. **Dynamic Updating**  
      - If the user types a new character, the list of suggestions updates.  
      - Handling new trending queries or updated search logs without a significant delay.

   4. **Scalability**  
      - Large volume of user queries across multiple geographic regions.  
      - Possibly billions of stored search terms or logs.

   ### 2. **High-Level Architecture**

   ```
           ┌─────────────┐
           │ User Client │
           └─────────────┘
                |  (HTTP / WebSockets)
                v
       ┌─────────────────────────┐
       │  API Gateway / Load Bal.│
       └─────────────────────────┘
                |
                v
       ┌──────────────────────────────┐
       │  Type-Ahead Service (Logic)  │
       └──────────────────────────────┘
                |
                v
     ┌───────────────────────────────────┐
     │   Data Store / Index (Trie /     │
     │   Prefix Tree / Search Engine)   │
     └───────────────────────────────────┘
                |
                v
       ┌───────────────────────────────┐
       │  Analytics & Sync Pipeline    │
       │  (Logs, Popularity Tracking)  │
       └───────────────────────────────┘
   ```

   1. **User Client**  
      - Browser or mobile app that sends partial queries as the user types.  
      - Requests suggestions from the Type-Ahead API after each keystroke (or some debounced interval).

   2. **API Gateway / Load Balancer**  
      - Distributes requests among multiple Type-Ahead Service instances.

   3. **Type-Ahead Service**  
      - Core logic to query the data store (trie or search engine) and return the top suggestions.  
      - Ranks results by popularity, recency, or user personalization (if required).

   4. **Data Store / Index**  
      - Could be an in-memory trie, prefix-based structure, or an inverted index in a text search engine (e.g., Elasticsearch, Solr) specialized for autocompletion.  
      - Stores queries or phrases along with popularity/frequency metadata.

   5. **Analytics & Sync Pipeline**  
      - Periodically processes logs of user queries to update popularity rankings.  
      - Could be a batch or real-time pipeline (e.g., Kafka + Spark/Flink).  
      - The processed results (updated frequencies or new top queries) are fed back into the index.

   ### 3. **Data Structures & Approaches**

   1. **Trie / Prefix Tree**  
      - **Structure**: Each node represents a character. Paths down the tree represent full queries.  
      - **Advantage**: Very fast prefix lookups.  
      - **Enhancement**: Store popularity (or frequency) at each terminal node to quickly retrieve the top suggestions for a given prefix.

   2. **In-Memory Key-Value Index**  
      - Key: query string.  
      - Value: popularity metrics, plus references for partial matching.  
      - Could be used if the dataset is not too large, or combined with a specialized prefix map.

   3. **Search Engine Autocomplete**  
      - Tools like **Elasticsearch** have built-in “completion suggester” features, which effectively store prefix-based indexes internally.  
      - **Pros**: Scalable, out-of-the-box features for ranking, partial matching, synonyms, etc.  
      - **Cons**: Might be more heavyweight than a custom trie.

   ### 4. **Ranking & Popularity**

   1. **Historical Frequency**  
      - Keep track of how often a query is searched, weighting heavier for more recent searches.  
      - Could be a simple integer count or a decayed count (more recent queries get higher weights).

   2. **User Personalization** (Optional)  
      - If the user is logged in, incorporate personalization signals (previous searches, region, language).  
      - Weighted approach: combined global popularity with user’s search history.

   3. **Contextual / Session-Based** (Optional)  
      - If the user has typed certain queries in the same session, adapt suggestions to that context.  
      - For instance, if searching for “apple…”, might suggest “apple stock,” “apple store,” etc. based on user’s prior queries or location.

   4. **N-grams or Markov Chains** (NLP Approach)  
      - For phrase suggestions, advanced solutions use N-gram models or Markov chains to predict next words.  
      - E.g., from partial phrase “New York,” might suggest “New York Times,” “New York Yankees,” etc.  
      - Typically used for natural language completion.

   ### 5. **Updating Suggestions**

   1. **Batch Updating**  
      - Periodically (e.g., hourly or daily), recalculate popularity stats from logs and rebuild or update the trie/index.  
      - Simpler to implement but introduces some delay for emerging or trending queries.

   2. **Near Real-Time Updates**  
      - Stream new queries into a pipeline (Kafka, Kinesis), increment counters in a distributed store (Redis, Cassandra), and update the data structure or search engine index in near real-time.  
      - More complex but ensures suggestions reflect new trends quickly.

   3. **Partial vs. Full Index Rebuild**  
      - For large sets, a full rebuild can be time-consuming.  
      - Use incremental merges or segment-based approaches if employing a search engine like Elasticsearch.

   ### 6. **Query Flow Example**

   1. **User** starts typing: “new y…”  
   2. **Client** sends partial string “new y” (after a small debounce, e.g., 200 ms).  
   3. **Service** looks up the top 5 completions in the data store:  
      - “new york” (popularity 10M)  
      - “new year” (popularity 8M)  
      - “new york times” (popularity 4M)  
      - …  
   4. **Service** returns these suggestions.  
   5. **Client** displays them.  
   6. If the user types more characters (“new yo”), the process repeats.

   ### 7. **Performance & Latency Considerations**

   1. **In-Memory** vs. **On-Disk**  
      - For extremely large dictionaries, not all data can be loaded into memory.  
      - Hybrid approach: load top queries or top prefixes in memory. Less frequent queries can be in a disk-based index.

   2. **Sharding & Replication**  
      - Partition data by prefix range or alphabetical splits for high availability and to handle large volumes.  
      - For instance, shard A handles “a–f,” shard B handles “g–m,” etc.

   3. **Caching**  
      - Popular partial queries can be cached.  
      - E.g., “new y” might be typed by many users, so store top suggestions in an in-memory cache.

   ### 8. **Adapting to User Behavior**

   1. **Contextual vs. Non-contextual**  
      - Some systems only rely on static popularity stats. Others adapt in real-time to the user’s session or past searches.

   2. **A/B Testing**  
      - Test different suggestion ranking or personalization strategies to improve click-through rates or user satisfaction.

   ### 9. **Edge Cases & Common Issues**

   1. **Very Long Queries**  
      - Some solutions limit prefix length for performance. E.g., only index up to 20–30 characters.  
   2. **Misspellings**  
      - Provide fuzzy matching or suggestions for potential typos (“Did you mean…?”).  
      - This is more complex and can be integrated with a spelling-correction pipeline or fuzzy search in Elasticsearch.  
   3. **Multi-lingual**  
      - Store separate indices for different languages or detect the user’s language before searching.  
   4. **Rate Limiting**  
      - High-volume type-ahead calls can overload the service if you get a request for every keystroke.  
      - Debounce or throttle calls from the client side.

   ### 10. **Final Thoughts**

   Designing a type-ahead search engine involves:

   1. **Efficient Data Structures** for fast prefix lookups (tries, specialized indexes, or search engine suggesters).  
   2. **Ranking Mechanisms** that consider global popularity, user context, or advanced NLP (Markov chains).  
   3. **Scalable Architecture** with possible real-time data pipelines and caching to handle massive concurrent requests.  
   4. **Low Latency** through in-memory lookups, caching, and distributed deployment.  

   ---

   ## 24. Design Netflix.
   Netflix is a video streaming service.

   What are some of the Required Features?
   Uninterrupted video streaming to be made available for the users. Likes and reviews of videos.
   Recommend new videos. Support high traffic of users.
   What are some of the common problems encountered?
   Is it acceptable to have lags while uploading videos?
   What happens if many users are accessing the same video concurrently?
   Possible tips for consideration:
   Make use of cloud technology to store and transmit video data
   There are three components of Netflix: OC (Content Delivery Network), Backend database, Client device for accessing the application.

   ### 1. **Core Requirements**

   1. **Uninterrupted Streaming**  
      - Adaptive bitrate streaming so the video can play smoothly even under fluctuating network conditions.  
      - Minimal buffering and fast startup times.

   2. **High Concurrency**  
      - Millions of users streaming simultaneously.  
      - Distribute load effectively via CDNs (Content Delivery Networks) and multiple regions.

   3. **Content Management**  
      - Support for large volumes of video data.  
      - Provide an efficient upload pipeline for new content.  
      - Handle metadata (titles, descriptions, genres, thumbnails).

   4. **Recommendations**  
      - Personalized suggestions based on user watch history, ratings, or likes.  
      - Possibly a sophisticated recommendation system (collaborative filtering, content-based, or hybrid).

   5. **Social Features**  
      - Users can rate, like, or review content.  
      - Possibly show aggregated ratings or curated top picks.

   6. **Scalability & Reliability**  
      - Handle traffic spikes (e.g., new show release or prime-time hours).  
      - Redundancy and fault-tolerance across multiple data centers or cloud regions.

   ### 2. **High-Level Architecture**

   Netflix commonly breaks down into three major components (as mentioned): **Open Connect (CDN)**, **Backend (Databases, Services)**, and the **Client (User Device)**. We can illustrate a more granular view:

   ```
                     ┌─────────────────────┐
                     │   User Devices      │
                     │ (TV, Mobile, Web)   │
                     └─────────────────────┘
                             |
                      (HTTP / TLS)
                             v
             ┌───────────────────────────────┐
             │  API Gateway / Edge Services  │
             └───────────────────────────────┘
                             |
                             v
        ┌───────────────────────────────────────────┐
        │   Backend Services (Metadata, Auth, etc.)│
        │   - Microservices for user profiles,     │
        │     recommendations, billing, etc.       │
        └───────────────────────────────────────────┘
                             |
                             v
        ┌───────────────────────────────────────────┐
        │  Databases (SQL/NoSQL) & Caches          │
        │  - Storing user data, preferences,       │
        │    watch histories, video metadata, etc. │
        └───────────────────────────────────────────┘
                             |
                             v
        ┌───────────────────────────────────────────┐
        │  Video Storage & Encoding Pipeline       │
        └───────────────────────────────────────────┘
                             |
                             v
               ┌───────────────────────────┐
               │    CDN / Open Connect     │
               │(Global edge servers)      │
               └───────────────────────────┘
   ```

   1. **User Devices**  
      - Could be smart TVs, mobile apps, web browsers.  
      - Playback logic with adaptive bitrate streaming (e.g., HLS, MPEG-DASH).

   2. **API Gateway / Edge Services**  
      - A stateless layer that routes requests to internal microservices.  
      - Manages authentication tokens, request throttling, etc.

   3. **Backend Services**  
      - **User Service**: Manages profiles, subscriptions, watchlists.  
      - **Recommendation Service**: Suggests shows or movies based on user data.  
      - **Billing & Account**: Payment processing, subscription tiers.  
      - **Content Management**: Manages metadata, licensing, region restrictions.

   4. **Databases & Caches**  
      - Could be a mix of relational (Postgres/MySQL) for transactions and NoSQL (Cassandra, DynamoDB) for large-scale data.  
      - In-memory caches (Redis) for hot data (e.g., user session info, trending titles).

   5. **Video Storage & Encoding**  
      - Raw video uploads are transcoded into multiple resolutions and bitrates (1080p, 720p, 480p, etc.).  
      - Stored in highly durable object stores (e.g., AWS S3 or equivalent).  
      - Each version chunked into segments for adaptive streaming.

   6. **CDN / Open Connect**  
      - Edge servers geographically distributed.  
      - Hosts the actual video segments so users can stream from a nearby location, reducing latency and buffering.  
      - Netflix has its own CDN (Open Connect) but third-party CDNs can also be used.

   ### 3. **Video Streaming Details**

   1. **Adaptive Bitrate**  
      - Files are encoded at multiple bitrates.  
      - Client player automatically switches to higher or lower quality depending on real-time network conditions.

   2. **Chunked Segments**  
      - Videos are split into small segments (e.g., 2–10 seconds each).  
      - The player requests segments in sequence from the CDN.

   3. **Caching at the Edge**  
      - Popular content is cached close to users for faster start times.  
      - Less popular content might be pulled from a parent node or origin store if not in the edge cache.

   ### 4. **Handling High Traffic**

   1. **Load Balancing**  
      - Multiple microservices instances behind load balancers.  
      - Autoscaling in cloud environments to handle spikes.

   2. **Regional Deployment**  
      - Deploy application servers and CDNs in multiple regions.  
      - Direct user traffic to the nearest region for lower latency.

   3. **Data Partitioning**  
      - Shard user data based on user ID or region to distribute load across databases.  
      - Keep a microservice architecture that isolates responsibilities (e.g., user data in one DB, content metadata in another).

   4. **Fault Tolerance**  
      - If a region fails, traffic can fail over to another.  
      - Use circuit breakers, retries, and fallback strategies to degrade gracefully.

   ### 5. **Recommendations & Personalization**

   1. **Data Collection**  
      - Track watch history, likes, ratings, watch duration, search queries.  
      - Aggregate data in a data lake (e.g., S3 + Hadoop or cloud equivalents).

   2. **Offline Modeling**  
      - Apply machine learning / collaborative filtering to find similarities among users and content.  
      - Update recommendation models periodically (e.g., daily or even hourly for popular shows).

   3. **Online Serving**  
      - A specialized service ranks or personalizes titles at request time.  
      - Could incorporate real-time signals (e.g., user recently watched an action movie → show more action titles).

   ### 6. **Social Features (Likes & Reviews)**

   1. **Ratings/Reviews**  
      - Store in a separate microservice or table for user feedback.  
      - Aggregated stats displayed to other users (e.g., 4.5/5 stars, top reviews).  
      - This data can feed into the recommendation engine.

   2. **Concurrency Control**  
      - Ensure user can’t spam or repeatedly like the same title.  
      - Possibly moderate or filter reviews for spam or abuse.

   ### 7. **Common Challenges & Solutions**

   1. **Lag in Video Upload / Processing**  
      - **Solution**: Async pipeline for ingestion. Once uploaded, videos are transcoded in parallel. Only after encoding completes do they become available to stream.  
      - End users rarely notice because content typically arrives well ahead of release time.

   2. **Concurrent Streams on Same Content**  
      - **Solution**: CDN caching. Multiple users watch from the same local edge server. The origin store or further upstream servers aren’t overloaded.

   3. **Latency and Buffering**  
      - **Solution**: Use adaptive bitrate streaming and robust CDNs.  
      - The system monitors user’s bandwidth in real time and adjusts video quality to keep playback smooth.

   4. **Global Availability**  
      - **Solution**: Multi-region infrastructure with failover.  
      - Partnerships or peering with ISPs to host servers closer to end users.

   5. **Scalability**  
      - **Solution**: Horizontal scaling of microservices, data sharding, event-driven architecture for analytics.  
      - Netflix is known for its microservices approach and chaos engineering to ensure reliability under unpredictable circumstances.

   ### 8. **Typical User Flow**

   1. **User** logs into Netflix on a device (mobile, smart TV, or browser).  
   2. **API** fetches user profile, watch history, recommended titles.  
   3. **User** picks a title to watch.  
   4. **CDN** streams the video segments. The device’s player adjusts quality to network conditions.  
   5. **User** rates or reviews the content (optional).  
   6. **Recommendation** engine updates user’s preferences for future suggestions.

   ### 9. **Final Thoughts**

   Designing a Netflix-style service involves:

   1. **Robust Streaming Infrastructure** using adaptive bitrate, chunked segments, and global edge caching.  
   2. **Cloud-Backed or On-Prem** Solutions with high scalability to handle massive concurrency and data volumes.  
   3. **Microservices** for user management, content metadata, recommendations, billing, etc.  
   4. **Continuous Ingestion & Processing** of new content, including transcoding to multiple bitrates.  
   5. **Resiliency & Performance** through multi-region deployments, load balancing, and failover strategies.

   ---

   ## 25. Design Tic-Tac-Toe game.
   Tic-tac-toe game involves two players where one player chooses 0 and the other player chooses X for marking the cells. The player who fills a row/column/diagonal with their selected character wins.

   What are some of the Required Features?
   Support 2 player game where one player can be a computer. Design algorithm to calculate the win and loss results.
   What are some of the common problems encountered?
   What happens if both players play optimally? How to decide the winning strategy?
   Possible tips for consideration:
   If one player is a computer, then make use of the rand() method for ensuring moves are completely random.

   ### 1. **Core Requirements**

   1. **3×3 Board**  
      - Each cell can be empty, marked by “X,” or marked by “O.”  
      - Keep track of move validity (i.e., ensuring players can’t overwrite existing marks).

   2. **Two Players**  
      - Player 1 (X) and Player 2 (O).  
      - Optionally, one of them can be the computer.

   3. **Win Condition**  
      - A player wins by placing three identical marks in a row, column, or diagonal.  
      - Detect draw if all 9 cells are filled without a winner.

   4. **Gameplay Flow**  
      - Players alternate turns.  
      - On each turn, the current player chooses a cell to mark.  
      - Check for a winner or draw after each move.

   5. **Optional AI**  
      - Computer can play randomly or use a minimax/optimal strategy.

   ### 2. **High-Level Design**

   ```
   ┌────────────────────────────────────┐
   │         TicTacToe Game            │
   │  - Board (3x3)                    │
   │  - Players (X and O)              │
   │  - Turn management                │
   │  - Result detection (win/draw)    │
   └────────────────────────────────────┘
                  |
                  v
   ┌────────────────────────────────────┐
   │  Player (Human or Computer)       │
   │  - Symbol (X or O)                │
   │  - getMove(board) -> (row, col)   │
   │    (may be user input or AI)      │
   └────────────────────────────────────┘
                  |
                  v
   ┌────────────────────────────────────┐
   │         Board Class               │
   │  - 3x3 matrix                     │
   │  - placeMark(row, col, symbol)    │
   │  - checkWin() / checkDraw()       │
   │  - printBoard() [for console UI]  │
   └────────────────────────────────────┘
   ```

   ### 2.1 **Board Representation**
   - A simple approach: a 2D array `board[3][3]`, or a list of length 9.  
   - Methods: 
     - `placeMark(row, col, symbol)`  
     - `isCellEmpty(row, col)`  
     - `checkWin(symbol)` checks rows, columns, diagonals for that symbol.  
     - `checkDraw()` sees if all cells are occupied and no winner.

   ### 2.2 **Player**
   - Attributes:
     - `symbol` (X or O).
     - `isHuman` (boolean) or a reference to an AI method.
   - Methods:
     - `getMove(board)`: returns `(row, col)`.  
       - For a human, prompt input.  
       - For a computer, generate or compute a move.

   ### 2.3 **Game Orchestration**
   - **TicTacToeGame** class or module:
     1. Initialize board and players.  
     2. While no winner and not a draw:  
        - Current player’s `getMove()` → `(row, col)`.  
        - Validate the move.  
        - Place the mark.  
        - Check for win/draw.  
        - Switch players.  
     3. Announce the winner or draw.

   ### 3. **Detecting Wins**

   Check all 8 possible winning lines after each move:
   1. **3 Rows**: `(r, 0), (r, 1), (r, 2)`  
   2. **3 Columns**: `(0, c), (1, c), (2, c)`  
   3. **2 Diagonals**: `(0,0), (1,1), (2,2)` and `(0,2), (1,1), (2,0)`

   Pseudo-code for `checkWin(symbol)`:
   ```python
   def checkWin(symbol):
       ## Rows
       for r in range(3):
           if board[r][0] == symbol and board[r][1] == symbol and board[r][2] == symbol:
               return True

       ## Columns
       for c in range(3):
           if board[0][c] == symbol and board[1][c] == symbol and board[2][c] == symbol:
               return True

       ## Diagonals
       if board[0][0] == symbol and board[1][1] == symbol and board[2][2] == symbol:
           return True
       if board[0][2] == symbol and board[1][1] == symbol and board[2][0] == symbol:
           return True

       return False
   ```

   For a **draw**, if no winning line but the board is full, it’s a draw.

   ### 4. **Computer (AI) Player**

   1. **Random Moves**  
      - For each turn, pick a random empty cell.  
      - Simple to implement but not “smart.”

   2. **Minimax Algorithm**  
      - Explore all possible moves up to the end of the game.  
      - Score outcomes: +1 for AI win, -1 for AI loss, 0 for draw.  
      - The AI chooses a move that maximizes its minimum possible loss (i.e., ensures the best outcome).  
      - Tic-Tac-Toe has a small enough search tree (max 9 moves) that minimax is feasible to compute quickly.

   3. **Optimality**  
      - If both sides use minimax, Tic-Tac-Toe always ends in a draw (assuming no mistakes).  

   ### 5. **Common Challenges & Strategies**

   1. **Draw vs. Ongoing**  
      - Must differentiate between “board is full” and “winner found.” A well-structured check handles this.

   2. **Input Validation**  
      - Prevent invalid moves (out of bounds, cell already occupied).  
      - If invalid, prompt again (for a human) or pick a different random cell (for the AI).

   3. **Both Players Optimal**  
      - With minimax, the game is likely to be a draw unless the opponent makes a mistake.  
      - This is normal for Tic-Tac-Toe.

   4. **Scalability**  
      - Tic-Tac-Toe is inherently small—no major scaling concerns.  
      - If developing for an online environment with multiple concurrent games, simply replicate the same logic for each session.

   ### 6. **Step-by-Step Gameplay Flow**

   1. **Initialize**  
      - Create an empty 3×3 board.  
      - Create Player 1 (X) and Player 2 (O). If Player 2 is a computer, define the AI method.

   2. **Game Loop** (until winner or draw):
      1. Current player obtains `(row, col)` from either user input or AI logic.  
      2. Check if `(row, col)` is valid (`0 <= row < 3`, etc.).  
      3. `board.placeMark(row, col, currentPlayer.symbol)`.  
      4. If `board.checkWin(currentPlayer.symbol)` is True, announce “Player [X or O] wins!” and end.  
      5. Else if `board.isFull()` is True, announce “Draw!” and end.  
      6. Switch currentPlayer to the other player.

   3. **End of Game**  
      - Possibly display final board state.  
      - Optionally ask if the players want to play again.

   ### 7. **Example Pseudocode**

   ```python
   class TicTacToeGame:
       def __init__(self):
           self.board = Board()  # 3x3 grid
           self.playerX = Player(symbol='X', isHuman=True)
           self.playerO = Player(symbol='O', isHuman=False)  # or True for second human
           self.currentPlayer = self.playerX

       def play(self):
           while True:
               row, col = self.currentPlayer.getMove(self.board)
               if self.board.isCellEmpty(row, col):
                   self.board.placeMark(row, col, self.currentPlayer.symbol)

                   if self.board.checkWin(self.currentPlayer.symbol):
                       print(f"Player {self.currentPlayer.symbol} wins!")
                       self.board.display()
                       break
                   elif self.board.isFull():
                       print("Game is a draw!")
                       self.board.display()
                       break

                   ## Switch player
                   self.currentPlayer = self.playerO if self.currentPlayer == self.playerX else self.playerX
               else:
                   print("Invalid move. Try again.")

   class Player:
       def __init__(self, symbol, isHuman=True):
           self.symbol = symbol
           self.isHuman = isHuman

       def getMove(self, board):
           if self.isHuman:
               row = int(input("Enter row: "))
               col = int(input("Enter col: "))
               return row, col
           else:
               return self.computerMove(board)

       def computerMove(self, board):
           ## Random or minimax approach
           ## Example random approach:
           validMoves = board.getEmptyCells()
           return random.choice(validMoves)

   class Board:
       def __init__(self):
           self.grid = [[' ' for _ in range(3)] for _ in range(3)]

       def placeMark(self, row, col, symbol):
           self.grid[row][col] = symbol

       def isCellEmpty(self, row, col):
           return self.grid[row][col] == ' '

       def checkWin(self, symbol):
           ## Implementation from above
           ...

       def isFull(self):
           for row in range(3):
               for col in range(3):
                   if self.grid[row][col] == ' ':
                       return False
           return True

       def getEmptyCells(self):
           emptyCells = []
           for r in range(3):
               for c in range(3):
                   if self.grid[r][c] == ' ':
                       emptyCells.append((r, c))
           return emptyCells

       def display(self):
           for row in self.grid:
               print("|".join(row))
               print("-" * 5)
   ```

   ### 8. **Final Thoughts**

   - **Tic-Tac-Toe** is straightforward: a 3×3 grid, two players, and a quick check for three-in-a-line.  
   - The main tasks involve:
     1. **Board Management** (marking cells, validating moves, checking for win/draw).  
     2. **Game Orchestration** (turn-taking, user/computer input).  
     3. **Optional AI** using random moves or a minimax approach for optimal play.

   ---

   ## 26. Design a traffic control system.
   Generally, in a traffic control system, we see that the lights transition from RED To GREEN, GREEN to ORANGE and then to RED.
   What are some of the Required Features?
   Transition traffic lights based on the conventions.
   What are some of the common problems encountered?
   Determine the time interval for which the state of the traffic lights has to change.
   What happens in worst-case scenarios where the state is wrongly shown?
   Possible tips for consideration:
   Make use of state design patterns and scheduling algorithms for the transition of the state from one colour to another.

   ### 1. **Core Requirements**

   1. **Traffic Light States**  
      - **Red**: Cars must stop.  
      - **Green**: Cars can go.  
      - **Yellow (Orange)**: A short “caution” interval before switching to Red, warning drivers the light is about to change.

   2. **Cyclic Transitions**  
      - The light transitions in a fixed sequence: **Red → Green → Yellow → Red**.  
      - Each color is shown for a set amount of time or based on adaptive logic (e.g., traffic density).

   3. **Timing Control**  
      - Define the duration of each light (e.g., Red for 60s, Green for 45s, Yellow for 5s).  
      - Possibly adapt the duration dynamically to reduce congestion.

   4. **Safety & Reliability**  
      - Must ensure fail-safe behavior if hardware or software malfunctions (e.g., default to flashing red if the controller fails).  
      - Provide ways to handle emergency vehicles or manual overrides.

   ### 2. **High-Level Architecture**

   ```
   ┌───────────────────────────┐
   │ Traffic Light Controller  │
   │  - State machine logic    │
   │  - Timer / scheduling     │
   └───────────────────────────┘
              |
              v
   ┌───────────────────────────┐
   │  Sensors (optional)       │
   │  - Vehicle detection      │
   │  - Pedestrian push button │
   └───────────────────────────┘
              |
              v
   ┌───────────────────────────┐
   │  Actuator / Light Control │
   │  - Changes lamp states    │
   │  - Monitors malfunctions  │
   └───────────────────────────┘
   ```

   1. **Traffic Light Controller (Central Logic)**  
      - Implements a state machine (e.g., Red, Green, Yellow).  
      - A scheduling or timing component triggers state changes after the appropriate interval.

   2. **Sensors** (Optional)  
      - Vehicle sensors in the road or cameras measuring traffic density can inform adaptive timing.  
      - Pedestrian crosswalk buttons may signal the need to switch to a “walk” cycle.

   3. **Actuator / Light Control**  
      - The physical interface that turns the actual lights on/off.  
      - Should have fallback or default states if the controller loses connection.

   ### 3. **State Machine Design**

   A standard **Finite State Machine (FSM)** or **State Design Pattern** can handle transitions:

   1. **Red**  
      - Next state: **Green** after a preset or computed delay.  
   2. **Green**  
      - Next state: **Yellow** after a preset or computed delay.  
   3. **Yellow**  
      - Next state: **Red** after a short (warning) delay.

   You might represent each state as a class or entity with an `onEnter()`, `onExit()`, and `onTimer()` method:

   ```pseudocode
   class RedState:
       def onEnter():
           light.setRed(true)
           light.setGreen(false)
           light.setYellow(false)
           scheduleTimer(redDuration)

       def onTimer():
           controller.changeState(GreenState)

   class GreenState:
       def onEnter():
           light.setGreen(true)
           light.setRed(false)
           light.setYellow(false)
           scheduleTimer(greenDuration)

       def onTimer():
           controller.changeState(YellowState)

   class YellowState:
       def onEnter():
           light.setYellow(true)
           light.setGreen(false)
           light.setRed(false)
           scheduleTimer(yellowDuration)

       def onTimer():
           controller.changeState(RedState)
   ```

   The **controller** transitions between these states based on a timer or sensor input.

   ### 4. **Timing & Scheduling**

   ### 4.1 **Fixed Timing**
   - Each state has a predefined duration:
     - For instance: Red = 60s, Green = 45s, Yellow = 5s.
   - **Pros**: Simple to implement, predictable.  
   - **Cons**: Not efficient if traffic volumes fluctuate.

   ### 4.2 **Adaptive Timing**  
   - The system adjusts durations based on real-time traffic or pedestrian demands.  
   - Could use **traffic flow data** from sensors:
     - If congestion is heavy on one approach, keep Green on that side longer.
   - More complex logic or algorithms (e.g., fuzzy control, AI-based) can help reduce wait times overall.

   ### 5. **Edge Cases & Problems**

   1. **Wrong State Displayed**  
      - If a hardware malfunction shows Red and Green simultaneously, it can cause accidents.  
      - **Solution**: Fail-safe design with mechanical or software interlocks (only one color circuit can be active at a time).

   2. **Power Failure**  
      - Light might go dark or flash red (common fallback).  
      - **Solution**: Battery backups or uninterruptible power supplies for critical intersections.  
      - Controller resets to a safe default state upon reboot.

   3. **Emergency Vehicle Override**  
      - Some systems let emergency vehicles or buses trigger a priority signal.  
      - **Solution**: If an ambulance arrives, the system quickly switches to Green in that lane.

   4. **Pedestrian Crosswalk Integration**  
      - Pedestrians might have a “walk” signal that fits into the cycle.  
      - “Walk” often corresponds to traffic in the same direction having Green, then a brief “flashing don’t walk” overlap.

   ### 6. **Scheduling Algorithms & Patterns**

   1. **Simple Timer**  
      - Keep a single loop or thread that sleeps for the current state’s duration, then transitions states.

   2. **Event-Driven**  
      - A state machine that receives events from sensors or timers.  
      - E.g., “TIMER_EXPIRED” event triggers the next state.

   3. **Real-Time OS** (Advanced)  
      - For large-scale or city-level systems, a real-time scheduler or a PLC (Programmable Logic Controller) can handle multiple intersections, scheduling them for optimal traffic flow.

   4. **Coordination Among Intersections** (Traffic Flow)  
      - In a city grid, controlling signals in sync can reduce stop-and-go traffic.  
      - Might require a central system that synchronizes multiple lights based on traffic models or time-of-day schedules.

   ### 7. **Implementation Steps (Simple Intersection)**

   1. **Initialize**: Start the controller in **RedState**.  
   2. **Loop**:
      - `onEnter(RedState)`: Turn red light on, schedule a timer for `redDuration` seconds.  
      - After `redDuration` expires, transition to `GreenState`.  
      - `onEnter(GreenState)`: Turn green light on, schedule a timer for `greenDuration` seconds.  
      - After `greenDuration` expires, transition to `YellowState`.  
      - `onEnter(YellowState)`: Turn yellow light on, schedule a timer for `yellowDuration` seconds.  
      - After `yellowDuration` expires, transition back to **RedState**.  

   At each step, you can incorporate sensors or override logic if needed.

   ### 8. **Possible Extensions**

   1. **Multiple Lanes & Directions**  
      - Real intersections often handle left-turn lanes, right-turn signals, arrow lights.  
      - Each direction can be a sub-state, or you can define more states (e.g., left-turn green, all-red clearance, etc.).

   2. **Centralized Monitoring System**  
      - Collect data from multiple intersections to adjust timings.  
      - Provide remote control override for special events (parades, emergencies).

   3. **Adaptive or AI-Based**  
      - Use historical data and real-time sensors to dynamically optimize green times.  
      - Employ machine learning to reduce congestion automatically.

   4. **Integration with IoT**  
      - Real-time updates sent to a cloud system for predictive analytics.  
      - Automatic alerts if hardware is failing or traffic is unusually heavy.

   ### 9. **Final Thoughts**

   A traffic control system can be as simple as a **fixed-time state machine** or as advanced as an **adaptive multi-intersection network**. Key points include:

   - **Reliable State Transitions**: Ensuring only one light is green at a time, with a short yellow interval as a buffer.  
   - **Safe Defaults & Fail-Safes**: If hardware or software fails, the system defaults to a safe state (often flashing red).  
   - **Scalability**: For a city-wide system, coordinate multiple intersections, factor in real-time sensor data, and possibly leverage advanced algorithms to optimize traffic flow overall.  

   ---

   ## 27. Design Web Crawler.
   The Web crawler is a search engine-related service like Google, DuckDuckGo and is used for indexing website contents over the Internet for making them available for every result.

   What are some of the Required Features?
   Design and develop a Scalable service for collecting information from the entire web and fetching millions of web documents.
   Fresh data has to be fetched for every search query.
   What are some of the common problems encountered?
   How to handle the updates when users are typing very fast? How to prioritize dynamically changing web pages?
   Possible tips for consideration:
   Look into URL Frontier Architecture for implementing this system. Know how crawling is different from scraping.

   ### 1. **Core Requirements**

   1. **Massive Scale**  
      - Capable of crawling billions of URLs across the internet, storing metadata or page content.
      - Must run in a distributed, fault-tolerant manner.

   2. **Freshness**  
      - Needs to periodically re-crawl pages to capture updates or new content.
      - Prioritize pages that change more often.

   3. **Prioritization & Politeness**  
      - Respect robots.txt and domain crawl-delay settings.
      - Avoid overloading websites with too many requests in a short time.
      - Maintain a queue (frontier) that orders URLs by importance, discovery time, or refresh schedule.

   4. **De-duplication & Data Storage**  
      - Avoid re-crawling the same content repeatedly (handle canonical URLs, near-duplicates, or identical copies).
      - Store crawled data (HTML content, metadata) in a scalable repository for further indexing.

   5. **Crawl vs. Scrape**  
      - **Crawling** is discovering, fetching, and indexing pages en masse (breadth or depth).  
      - **Scraping** is more targeted data extraction from specific pages or domains.

   ### 2. **High-Level Architecture**

   ```
                    ┌─────────────────────────┐
                    │  URL Frontier          │
                    │ (Queue / Priority Q)   │
                    └─────────────────────────┘
                             |
                             v
                ┌─────────────────────────────┐
                │ Distributed Crawler Workers │
                └─────────────────────────────┘
            (Fetchers, Parsers, Robots Checking)
                             |
                             v
                ┌─────────────────────────────┐
                │  Document Store / Index     │
                │   - HTML, metadata, etc.    │
                └─────────────────────────────┘
                             |
                             v
                ┌─────────────────────────────┐
                │   Scheduler & Analytics     │
                │   (Re-crawling logic)       │
                └─────────────────────────────┘
   ```

   1. **URL Frontier (Queue)**  
      - Central or distributed queue containing URLs to be crawled, possibly with priority scoring (e.g., high PageRank or known frequent updates).
      - Each entry includes scheduling info, domain constraints, last crawled time.

   2. **Crawler Workers**  
      - Multiple nodes (threads/processes) that fetch pages in parallel.
      - Check **robots.txt** compliance before fetching.  
      - Parse HTML to discover new links, extract metadata, detect canonical URLs, etc.
      - Add newly discovered links to the Frontier with appropriate scheduling or priority.

   3. **Document Store / Index**  
      - Stores fetched pages’ HTML or structured data.
      - Possibly a large distributed file system (HDFS, S3) or a specialized document DB (Cassandra, Elasticsearch).
      - The search engine’s index is built on top of this store (e.g., inverted indexes for full-text search).

   4. **Scheduler & Analytics**  
      - Analyzes link popularity, domain update frequency, or external signals to decide re-crawl intervals.
      - Periodically updates the Frontier with pages that need refreshing (e.g., news sites updated frequently, static pages less so).

   ### 3. **URL Frontier & Prioritization**

   1. **Scoring**  
      - Each URL gets an initial score based on factors like domain authority, link popularity, or estimated change frequency.
      - Higher score = earlier fetch from the frontier.

   2. **Politeness & Throttling**  
      - Group URLs by domain or host to avoid hammering a single site too quickly.
      - Maintain a delay or concurrency limit per domain (e.g., at most 2 fetches/sec).

   3. **Distributed Queue**  
      - In large-scale crawlers, the frontier is partitioned across multiple servers.  
      - A shard might hold all URLs for a subset of domains or a hashed partition of the URL string.

   ### 4. **Crawling Workflow**

   1. **Pop URL from Frontier**: A worker takes the highest-priority URL from the queue.  
   2. **Check robots.txt**: Ensure the URL is allowed and fetch any domain-specific crawl-delay.  
   3. **Fetch & Parse**: Worker downloads HTML or other content.  
      - If 200 OK, parse for new links, metadata, or redirects.  
      - If 404/410, remove or lower priority for that URL in future.  
   4. **Store Content**: Save raw HTML or structured data in the Document Store.  
   5. **Link Extraction**: Identify new links or unvisited pages.  
      - Deduplicate them (avoid repeating the same URL or near-duplicates).  
      - Insert them into the Frontier with an appropriate score or next-crawl time.  
   6. **Rescheduling**: Once successfully crawled, the page is scheduled for a future re-crawl based on heuristics (e.g., if the site’s known to update daily).

   ### 5. **Freshness & Dynamic Content**

   1. **Scheduling Heuristics**  
      - Frequent updates = shorter re-crawl interval.  
      - Infrequent updates = longer re-crawl interval.  
   2. **Change Detection**  
      - Compare new fetch’s checksum or ETag with the last version.  
      - If unchanged, possibly back off further.

   3. **Real-Time Index Updates** (Optional)  
      - Some crawlers do near-real-time indexing for breaking news or social media.  
      - Others operate primarily in batch cycles.

   ### 6. **Common Challenges & Solutions**

   1. **Bandwidth & Performance**  
      - Large-scale crawlers can overwhelm a network if not throttled.  
      - **Solution**: distribute the crawler geographically, use politeness constraints, and adopt efficient concurrency models (async fetch).

   2. **Duplicate Content & Canonicalization**  
      - Many URLs may point to the same resource or differ by query parameters.  
      - **Solution**: parse `<link rel="canonical" />` tags, use hashing or dedup checks to identify repeated content.

   3. **Dynamic Pages & JavaScript**  
      - Modern websites often load content via JavaScript; raw HTML might be incomplete.  
      - **Solution**: headless browsers (e.g., Puppeteer, Selenium) or partial JS rendering. This is expensive, so typically done selectively.

   4. **Malicious/Spam Sites**  
      - Crawler might encounter malicious pages or spam.  
      - **Solution**: safe browsing checks, ignoring certain domains after repeated spam signals, or whitelisting known good sources.

   5. **Scaling**  
      - Billions of pages require robust, partitioned data stores and distributed queues.  
      - Worker nodes must handle millions of requests daily, storing tens/hundreds of TB of data.

   ### 7. **Crawling vs. Scraping**

   - **Crawling**:  
     - Broad coverage, discovering and indexing the entire (or large portions of) the web.  
     - Typically BFS or priority-based approach across many domains.

   - **Scraping**:  
     - Focus on extracting specific data from targeted pages or domains.  
     - Usually domain-specific logic (like scraping e-commerce product details).

   ### 8. **Summary / Example Flow**

   1. **Initialize Frontier** with seed URLs (major websites, known domain lists).  
   2. **Worker** takes a URL, checks robots.txt, fetches page, extracts links.  
   3. **Store** the page’s content + metadata in the Document Store.  
   4. **Add** new URLs to the frontier or update existing ones’ priority.  
   5. **Scheduler** decides next re-crawl intervals for updated pages.  
   6. **Indexer** (separate from the crawler) processes fetched data for searching.  

   **Outcome**: This continuous loop ensures a wide swath of the web is traversed, updated content is re-fetched, and data is kept fresh for the search engine index.

   ### **Final Thoughts**
   Designing a robust web crawler involves careful **frontier management** (deciding the next URLs to fetch), **respect for site policies** (robots.txt, rate limits), **duplication checks**, and a **scalable distributed architecture** to handle billions of pages. By systematically fetching and parsing pages, storing content, and re-crawling them over time, the crawler ensures the search index remains both broad and up-to-date.

   ---

   ## 28. Design ATM system.
   ATMs are used for depositing and withdrawing money from customers. It is also useful for checking the account balance.
   What are some of the required features?
   Each user should have at least one bank account that is linked to the card for performing transactions.
   ATM to authenticate the user based on 4 digit PIN associated with the card. User to perform only one transaction at a given time.
   What are some of the common problems encountered?
   What happens during transaction timeout?
   What happens if the money is deducted from the bank account but the user hasn't received it from the machine?
   Possible tips for consideration:
   Divide the problem into different entities like Card, Card Reader etc and establish a relationship between each of the entities.

   ### 1. **Core Requirements**

   1. **Authentication**  
      - Each user has a card (linked to a bank account) and a PIN.  
      - The ATM checks the PIN for correctness before allowing any transaction.

   2. **Transactions**  
      - **Withdraw**: User can withdraw funds in multiples constrained by the machine’s denominations (e.g., \$20 bills).  
      - **Deposit**: User can deposit cash/checks (if supported).  
      - **Balance Inquiry**: Display account balance on-screen or print a receipt.  

   3. **Transaction Constraints**  
      - Only one transaction can be in progress at a time per ATM session.  
      - Check account balance before allowing withdrawals.  
      - Enforce daily withdrawal limits or per-transaction limits.

   4. **Reliability & Consistency**  
      - Ensure the user is not charged if the machine fails to dispense cash.  
      - Handle timeouts or canceled transactions gracefully.  

   5. **Security**  
      - PIN encryption, secure network to the bank’s backend systems.  
      - Camera, anti-skimming hardware, etc., on the physical ATM (though that’s more hardware).

   ### 2. **High-Level Architecture**

   ```
             ┌───────────────────────────┐
             │       User with Card      │
             └───────────────────────────┘
                        |
                        | (Insert card, enter PIN)
                        v
         ┌──────────────────────────────────────┐
         │ ATM Machine (Frontend Controller)   │
         │ - Card Reader                       │
         │ - PIN Keypad                        │
         │ - Screen / UI                       │
         │ - Cash Dispenser / Depositor        │
         └──────────────────────────────────────┘
                        |
                        v
            ┌───────────────────────────────┐
            │ Bank Backend System           │
            │ - Account Service             │
            │ - Transaction Service         │
            │ - Ledger/Database             │
            └───────────────────────────────┘
   ```

   1. **ATM Machine (Frontend Controller)**  
      - Hardware components: Card reader, keypad, display, dispenser, deposit slot.  
      - ATM software orchestrates user interactions, authentication, and transaction processing requests to the bank backend.

   2. **Bank Backend System**  
      - **Account Service**: Maintains account balances, daily limits, and transaction history.  
      - **Transaction Service**: Authorizes or declines requests (withdraw, deposit, etc.) and updates the ledger.  
      - **Database**: Stores persistent data (account info, transaction logs).

   ### 3. **Key Entities & Their Relationships**

   1. **Card**  
      - **Attributes**: CardNumber, ExpiryDate, AssociatedAccount(s), possibly an encrypted PIN reference.  
      - **Methods**: `validatePIN(enteredPIN)`, `isExpired()`, etc.

   2. **User**  
      - Typically not directly manipulated by the ATM software beyond card identification.  
      - Bank’s backend verifies if a user is authorized for an account.

   3. **Account**  
      - **Attributes**: AccountNumber, Balance, DailyWithdrawalLimit, etc.  
      - **Methods**: `debit(amount)`, `credit(amount)`, `checkBalance()`, `checkLimits()`.  
      - One account could be linked to multiple cards, or each card could link to multiple user accounts (like checking/savings).

   4. **ATM**  
      - **Attributes**: ATM ID, location, cashAvailable, depositCapacity.  
      - **Methods**: `authenticateCard(card, PIN)`, `withdraw(amount)`, `deposit(amount)`, `showBalance()`, `ejectCard()`.  
      - Interacts with the **Bank System** to confirm each transaction.

   5. **BankSystem**  
      - **Methods**: `validatePIN(cardNumber, enteredPIN)`, `processTransaction(transaction)`.  
      - Might involve multiple microservices or a single monolithic backend.

   6. **Transaction**  
      - **Attributes**: transactionID, type (withdraw, deposit, inquiry), accountNumber, amount, status (pending, completed, failed).  
      - **Methods**: `execute()`, `rollback()` if necessary.

   ### 4. **Workflow: Withdraw Cash Example**

   1. **User inserts card**  
      - ATM reads the card’s info (cardNumber, expiration).  
      - Prompts user for a PIN.

   2. **Authentication**  
      - ATM sends `(cardNumber, enteredPIN)` to the bank’s backend for validation.  
      - If valid, ATM allows user to pick a transaction; otherwise, eject card after some attempts.

   3. **User chooses “Withdraw”**  
      1. **ATM prompts** for amount.  
      2. **User enters** amount.  
      3. ATM calls `BankSystem.processTransaction()` with a new transaction object:  
         - `transactionType=WITHDRAW, amount, cardNumber/accountNumber`.
      4. **BankSystem** checks:  
         - Sufficient balance?  
         - Daily limit?  
         - If OK, the system reserves or deducts the amount from the account, sets `transaction.status = authorized`.
      5. **ATM dispenses** cash physically (if it has enough bills).  
      6. If successful, the ATM confirms with the bank to finalize the transaction.  
      7. If the machine fails to dispense, the transaction is reverted or flagged.  

   4. **Receipt & Eject**  
      - If user wants a receipt, print transaction summary.  
      - Eject the card, session ends.

   ### 5. **Common Issues & Solutions**

   1. **Transaction Timeout**  
      - If user doesn’t respond within X seconds (e.g., 30s), **ATM** cancels the session.  
      - If a transaction was partially authorized, ensure the bank or the ATM can revert or finalize properly (depending on dispensing status).

   2. **Cash Deducted but Not Dispensed**  
      - **ATM** must confirm actual dispensing.  
      - If an error occurs mid-dispense, the ATM can detect jam or error and signal the backend to **rollback** the transaction.  
      - If the ATM records partial dispense, any discrepancy must be reconciled in daily logs (the leftover bills remain in the dispenser, or an engineer empties jammed bills).

   3. **Hardware Failures**  
      - If the card reader fails, the user can’t insert a card → ATM goes offline or partial service.  
      - If the dispenser is out of cash, the ATM must only allow other transactions (balance inquiries, deposits).  

   4. **Security**  
      - **Encrypted PIN** entry. ATM never stores raw PIN, only validates via the backend or secure hardware module.  
      - Tamper-proof hardware to prevent skimming or altering transactions.  
      - **Logging & Audit**: Each transaction logged to mitigate disputes.

   ### 6. **Extended Features**

   1. **Multi-Account Selection**  
      - If a single card is linked to checking, savings, or credit accounts, user can select which account to debit/credit.

   2. **Check Deposits**  
      - Some ATMs can scan checks and deposit to the user’s account.  
      - Need additional hardware (check scanner) and logic to handle scanning, verification.

   3. **Online Banking Integration**  
      - Synchronize with the user’s online banking—ATM changes reflect instantly in the user’s account.

   4. **Multi-lingual Interface**  
      - UI prompts in multiple languages.

   5. **Advanced Transaction Limits**  
      - Some ATMs can handle currency conversions if traveling abroad (involves exchange rates from the backend).

   ### 7. **Example Class Diagram (Simplified)**

   ```
                   ┌─────────────┐
                   │     Card    │
                   │─────────────│
                   │+ number     │
                   │+ expiryDate │
                   └─────────────┘
                        ^
                        |
                        |
    ┌───────────────────────────┐       ┌────────────────────────────┐
    │        BankSystem         │       │        ATM Machine         │
    │───────────────────────────│       │────────────────────────────│
    │+ validatePIN(card, pin)   │<----->│+ authenticate(card, pin)   │
    │+ processTransaction(txn)  │       │+ withdraw(amount)          │
    └───────────────────────────┘       │+ deposit(amount)           │
                                        │+ showBalance()             │
                                        └────────────────────────────┘

           ┌─────────────┐        ┌─────────────┐
           │   Account   │        │ Transaction │
           │─────────────│        │─────────────│
           │+ balance    │        │+ txnId      │
           │+ dailyLimit │        │+ type       │
           │+ ...        │        │+ amount     │
           └─────────────┘        │+ status     │
                                  └─────────────┘
   ```

   ### 8. **Final Thoughts**

   An ATM system design focuses on:

   1. **Hardware–Software Interaction**: Card reader, keypad, cash dispenser, deposit intake, receipt printer.  
   2. **Transaction Reliability**: Ensuring funds are debited/credited only if the physical action (dispense/deposit) succeeds.  
   3. **Security & Authentication**: Safe handling of PINs, encryption, tamper-resistant hardware.  
   4. **Error Handling**: Timeouts, partial disbursements, hardware faults all require robust fail-safe logic.  

   ---

   ## 29. Design Uber, Ola or Lyft type of systems.
   These platforms help user request rides and the driver picks them up from the location and drop them at the destination selected by the user.

   What are some of the required features?
   Real-time service for booking rides
   Should have the capability of assigning rides that lets the user reach the destination fast.
   Show the ETA (Estimated Time of Arrival) of the driver after booking the ride and once the ride has been started, show the ETA of the vehicle arriving at the destination.
   What are some of the common problems encountered?
   How to store geographical locations for drivers always on move? How to assign drivers to the customers e?iciently?
   How do you calculate the ETA of the driver arrival or the destination arrival?
   Possible tips for consideration:
   Make use of the microservices concept with fast databases for booking rides faster.
   Evaluate Dispatch System for assigning drivers to the users.

   ### 1. **Core Requirements**

   1. **Real-Time Ride Booking**  
      - Users request a ride, the system finds a nearby driver.  
      - Users get immediate confirmation and status updates.

   2. **Driver Assignment & Dispatch**  
      - Match user requests to drivers based on proximity, availability, and possibly driver ratings or car types.  
      - Must be low-latency to ensure a good user experience.

   3. **ETA Calculations**  
      - **Pickup ETA**: Time for the driver to reach the passenger’s location.  
      - **Destination ETA**: Time to arrive at the destination once the ride starts.  
      - Use real-time traffic data and routing algorithms.

   4. **Real-Time Location Tracking**  
      - Drivers continuously send location updates.  
      - Passengers see the driver’s current position on a map.

   5. **Payment & Rating**  
      - Calculate fare (distance, time, surge pricing).  
      - Allow in-app payment (credit card, wallets).  
      - Let passengers rate drivers and vice versa.

   ### 2. **High-Level Architecture**

   ```
             ┌─────────────────────────┐
             │      User Device       │
             │(Passenger, Driver Apps)│
             └─────────────────────────┘
                      |   (HTTPS, WebSockets, etc.)
                      v
       ┌────────────────────────────────────────────────┐
       │   API Gateway / Load Balancer                  │
       └────────────────────────────────────────────────┘
                      |
                      v
       ┌────────────────────────────────────────────────┐
       │   Microservices (Ride, Dispatch, Billing, etc.)│
       └────────────────────────────────────────────────┘
        |            |                 |               |
        v            v                 v               v
    ┌─────────┐  ┌───────────┐   ┌─────────────┐  ┌──────────────┐
    │  DBs     │  │  Cache    │   │ Location     │  │ Payment/Gateway│
    │ (SQL/NoSQL)│  │ (Redis)  │   │ Service/Maps │  │ Service        │
    └─────────┘  └───────────┘   └─────────────┘  └──────────────┘
   ```

   1. **User Devices**  
      - **Passenger App**: Request rides, track driver location, pay, rate.  
      - **Driver App**: Receive ride requests, navigate to pickup, continuously share location.

   2. **API Gateway / Load Balancer**  
      - Routes requests from mobile apps to microservices.  
      - Manages authentication tokens, rate-limiting, etc.

   3. **Microservices** (typical decomposition)  
      - **Ride Service**: Handles ride creation, status, tracking from `REQUESTED → IN_PROGRESS → COMPLETED`.  
      - **Dispatch Service**: Core logic for driver-passenger matching, scheduling, and push notifications.  
      - **Payment/Billing Service**: Fare calculation, trip invoicing, payment workflows.  
      - **User/Driver Management**: Manages profiles, ratings, documents, background checks.

   4. **Data Stores**  
      - **Relational or NoSQL DB** (e.g., Postgres, Cassandra) for rides, transactions.  
      - **Cache** (e.g., Redis) for quick lookups (e.g., driver availability, ephemeral data).  
      - **Location Service**: Integration with external mapping APIs (e.g., Google Maps, Mapbox) or an internal geospatial database for routing/ETA.

   ### 3. **Key Components & Flow**

   ### 3.1 **Driver Location Tracking**

   1. **Driver App** periodically (every few seconds) sends GPS coordinates to the backend.  
   2. A **Location Service** or a specialized geospatial index stores driver coordinates, possibly in memory (Redis with geospatial extensions, or specialized DB).  
   3. The system can quickly query “all drivers within X distance of a passenger’s location.”

   ### 3.2 **Ride Request & Dispatch**

   1. **Passenger** requests a ride, providing pickup and destination.  
   2. **Dispatch Service** receives request, queries the location store for nearby available drivers.  
   3. **Matching Logic**:  
      - Could be a straightforward nearest-driver approach, or a more complex algorithm (taking into account traffic, driver acceptance rates, or surge pricing).  
   4. **Driver Notification**: The chosen driver sees a push notification.  
      - If they accept within a timeframe, the ride is assigned.  
      - If not, the system tries another driver.

   ### 3.3 **ETA Calculation**

   1. **Pickup ETA**:  
      - From driver’s current location to passenger’s pickup.  
      - Use real-time traffic data from mapping APIs.  
   2. **Destination ETA**:  
      - Once ride starts, calculate from pickup to final destination.  
      - Update frequently for real-time changes (congestion, rerouting).

   ### 3.4 **Trip Lifecycle**

   1. **Ride Created** → **REQUESTED**  
   2. **Driver Accepts** → **ASSIGNED**  
   3. **Driver Arrives** → **DRIVER_ARRIVED**  
   4. **Passenger Onboard** → **IN_PROGRESS**  
   5. **Drop-off** → **COMPLETED** (fare calculated, payment processed)  
   6. **Rating & Feedback** → optional step

   ### 4. **Data Modeling**

   1. **Users** (Passengers)  
      - `user_id`, `name`, `phone`, `default_payment_method`, rating, etc.  
   2. **Drivers**  
      - `driver_id`, `vehicle_id`, `license_info`, status (AVAILABLE, BUSY, OFFLINE), rating, etc.  
   3. **Rides**  
      - `ride_id`, `passenger_id`, `driver_id`, `pickup_location`, `dropoff_location`, `status`, `start_time`, `end_time`, `fare`.  
   4. **Locations**  
      - Real-time location data for drivers. Often ephemeral, stored in a geospatial index.

   ### 5. **Core Challenges & Solutions**

   1. **High Concurrency**  
      - Potentially millions of requests/second.  
      - **Solution**: Horizontal scaling of microservices, distributed caches for driver location queries.

   2. **Efficient Driver Dispatch**  
      - Minimizing wait times, balancing driver workload, and optimizing overall usage.  
      - **Solution**: Weighted algorithms (e.g., nearest driver, surge in high-demand areas).  
      - Possibly use machine learning to predict demand and proactively position drivers.

   3. **Accurate ETA**  
      - Traffic is dynamic; road closures can shift quickly.  
      - **Solution**: Real-time data from mapping services, frequent re-calculation.  
      - Combine historical data + current conditions for better accuracy.

   4. **Driver-Client Communication**  
      - Real-time updates to passenger app on driver’s location, arrival.  
      - **Solution**: Use persistent connections (WebSockets, MQTT) or repeated poll-based approach with short intervals.

   5. **Payment & Security**  
      - Secure storage of payment details (PCI compliance).  
      - Manage potential fraud or disputes if ride data is incorrect.  
      - **Solution**: Payment gateway integration, robust logging.

   6. **Geolocation Data Management**  
      - Continuously updating thousands of drivers’ coordinates.  
      - Must handle concurrency and track only relevant drivers for a given passenger request.  
      - **Solution**: Geospatial indexing in Redis or specialized DB (GeoHash/QuadTree).

   7. **Edge Cases**  
      - Driver acceptance timeout.  
      - Passenger cancellation, driver cancellation.  
      - Surge pricing logic (high-demand areas).

   ### 6. **Scalability & Reliability**

   1. **Load Balancing**  
      - Multiple dispatch microservice instances behind an API gateway.  
   2. **Message Queues**  
      - For asynchronous tasks, e.g. updating user location logs, sending receipts.  
   3. **Microservice Isolation**  
      - Payment, Dispatch, and Ride are separate services. If Payment experiences an outage, it shouldn’t bring down location tracking, for example.
   4. **Data Replication**  
      - For failover across regions, ensuring minimal downtime if a data center fails.

   ### 7. **Additional Features**

   1. **Surge Pricing**  
      - Increase prices in high-demand times or areas to balance supply and demand.  
      - Real-time updates in the passenger’s quote before confirmation.

   2. **Pooling** (Carpool / Shared Rides)  
      - Match multiple passengers traveling similar routes.  
      - More complex dispatch logic but lower individual cost.

   3. **Driver Incentives & Gamification**  
      - Drivers can earn bonuses if they complete a certain number of trips or operate in a high-demand zone.

   4. **Analytics & ML**  
      - Demand prediction, route optimization, dynamic pricing.  
      - Historical trip data and real-time conditions.

   ### 8. **Example Request Flow**

   1. **Passenger** opens the app, enters destination, sees a fare estimate.  
   2. **Passenger** requests a ride.  
      - A request is sent to the **Dispatch Service**.  
   3. **Dispatch** queries the location store for available drivers within a certain radius.  
      - Chooses the best match (lowest ETA, driver rating, or minimal idle time).  
   4. **Driver** receives a push notification: “Ride request from location X.”  
      - If driver accepts, the ride is assigned, and the passenger is notified.  
   5. **Pickup & Trip**  
      - The driver’s location is periodically shared with the passenger.  
      - The system calculates real-time ETAs using the **Location Service** + external map APIs.  
   6. **Completion**  
      - The passenger is dropped off. The system calculates the final fare based on time, distance, and surge multipliers.  
      - Payment is auto-charged, driver and passenger can rate each other.  
   7. **Data Logging**  
      - The ride details are stored for analytics and future dispute resolution.

   ### **Final Thoughts**

   Designing a ride-sharing service involves coordinating **real-time location tracking** of drivers, **fast and efficient dispatch** algorithms, and **robust microservices** for payments, ride management, and analytics. By leveraging scalable data stores, geospatial queries, and real-time communication (WebSockets or short polling), you can deliver an experience that quickly pairs riders with the nearest available drivers, accurately projects ETAs, and smoothly processes payments while handling massive concurrent user loads.

   ---

   ## 30. Design a URL Shortening Service (TinyURL)
   URL shortening service allows users to enter a long URL, and then it returns a shorter, unique URL. These services generate a short URL if the user gives a long URL and if the user gives a short URL then it returns the original long URL.

   Things to discuss and analyze:
   Given a long URL, the service should generate a shorter and unique alias for it.
   When the user hits a short link, the service should redirect to the original link.
   Consider scalability if 1000’s URL shortening requests come every second.
   Service handle redirects.
   Support for custom short URLs.
   Track click stats.
   Delete expired URLs.
   The system should be highly available.
   You need to consider three things while designing this service. 

   API(REST API) – Discuss how the client will follow an approach to communicate with the service along with the load balancer which is the front end of the service.
   Application Layer – Discuss how the worker thread or hosts that will take the long URL, generate the tiny URL and how it will store both of the URLs in the database.
   Persistence Layer – Database
   ### 1. **Core Requirements**

   1. **Short URL Generation**  
      - Input: a long URL.  
      - Output: a unique short URL (e.g., `tinyurl.com/abc123`).  
      - (Optional) Users can specify a **custom alias** instead of a random hash (e.g., `tinyurl.com/myProject`).

   2. **Redirection**  
      - When users hit the short URL, the service **redirects** them to the original (long) URL.

   3. **Scalability & High Availability**  
      - Potentially thousands of requests per second (both new short URL requests and redirect hits).  
      - Redundancy and load balancing to handle traffic spikes.

   4. **Additional Features** (Optional)  
      - **Click Stats**: Track number of hits, unique visitors, geolocation, referrers, etc.  
      - **Expiration**: URLs can be set to expire after a certain time or usage count.  
      - **Deletion**: Inactive or expired short URLs can be purged.

   ### 2. **API Layer (REST API)**

   ### 2.1 **Endpoints**

   1. **Create Short URL**  
      - **POST** `/shorten`  
      - **Request Body**:  
        ```json
        {
          "original_url": "https://some-very-long-url.com/...",
          "custom_alias": "myProject"   // optional
        }
        ```  
      - **Response**:  
        ```json
        {
          "short_url": "https://tinyurl.com/myProject"
        }
        ```
      - **Errors**:
        - 400 if `original_url` is invalid or if `custom_alias` is taken.

   2. **Redirect**  
      - **GET** `/{shortId}`  
      - The system looks up the corresponding original URL and responds with **HTTP 301/302** to redirect.  
      - If the short URL doesn’t exist or expired, return **404** or an error page.

   3. **Statistics** (Optional)  
      - **GET** `/stats/{shortId}`  
      - Returns usage statistics (click count, last accessed time, etc.).

   4. **Delete / Expire** (Optional)  
      - **DELETE** `/{shortId}`  
      - Marks the short URL as expired or removes it.

   ### 2.2 **Load Balancer / Front End**

   - Incoming requests from clients or browsers pass through a **load balancer** (e.g., NGINX, HAProxy, or cloud LB).  
   - The LB distributes requests among multiple **application servers** hosting the URL Shortener service.

   ### 2.3 **Authentication / Rate Limiting** (Optional)

   - If your service is public, you may need **API keys** or **OAuth** to prevent abuse.  
   - **Rate limits** protect from excessive or malicious requests (e.g., spamming short URL creations).

   ### 3. **Application Layer**

   This layer implements the **business logic** for creating short URLs, handling redirection, and performing any optional features (stats, expiration, etc.).

   ### 3.1 **Workflow for Creating a Short URL**

   1. **Validate Input**  
      - Check if the original URL is well-formed (possibly check domain blacklists).
      - If a `custom_alias` is provided, check availability.

   2. **Generate Unique Key** (if no custom alias)  
      - Could use a **hashing** function (e.g., MD5/SHA-256 truncated + Base62-encoded).  
      - Or **random** short code generation (e.g., 6-8 characters from `[0-9A-Za-z]`).  
      - Check for collisions (if short code already exists) – if so, re-generate.

   3. **Store Mapping**  
      - Insert `(short_code, original_url, metadata)` into the database.  
      - If `custom_alias` is used, store that in place of the random code.

   4. **Return** the short URL (combining your domain + the short code).

   ### 3.2 **Redirect Workflow**

   1. **Extract Short Code** from the incoming path `/abc123`.  
   2. **Lookup** the short code in the database or cache.  
   3. If it **exists and not expired**, retrieve the `original_url`.  
      - Optionally increment a **click count**.  
      - Optionally record analytics (timestamp, IP, user-agent).  
      - Respond with an HTTP **301** or **302** redirect to `original_url`.  
   4. If not found or expired, return **404** or error page.

   ### 3.3 **Analytics & Expiration Logic** (Optional)

   - For **stats**: increment counters, store logs in a queue or analytics DB for further processing.  
   - For **expiry**: if a short URL has a `ttl` or `maxClicks`, once it’s reached, mark it expired in the DB or remove it entirely.

   ### 3.4 **Caching**

   - Frequent lookups (redirects) happen more than short-URL creation.  
   - A **cache** (e.g., Redis) can store `(short_code -> original_url)` for fast retrieval.  
   - If data is in cache, it bypasses a DB read. Otherwise, fetch from DB and populate cache.

   ### 4. **Persistence Layer**

   ### 4.1 **Database Schema**

   Minimal table or collection might have:

   | **Field**         | **Description**                                                          |
   |-------------------|--------------------------------------------------------------------------|
   | `id` / `short_id` | Unique identifier (e.g., random code or custom alias)                    |
   | `original_url`    | The full original link                                                   |
   | `created_at`      | Timestamp of creation                                                    |
   | `expires_at`      | Timestamp of expiry (optional)                                          |
   | `click_count`     | Number of times the short link has been accessed (if tracking is needed) |
   | `owner_user_id`   | If you track user ownership / authentication (optional)                  |

   ### 4.2 **Choosing the DB**

   1. **Relational (MySQL, Postgres)**  
      - Simple approach, easy for small to medium scale.  
      - Might need **sharding** or partitioning if billions of records.

   2. **NoSQL (e.g., DynamoDB, Cassandra)**  
      - Great for high write/read throughput at scale.  
      - Typically store `short_code` as the partition key.

   3. **In-Memory / Cache** (Redis, Memcached)  
      - For near-instant lookups.  
      - But still need persistent storage for durability.

   ### 4.3 **Handling Massive Scale**

   - Potential for billions of short URLs: use **sharding** or a high-throughput NoSQL store.  
   - Partition data by short code or creation date.  
   - If collisions are a concern, ensure quick re-generation or a robust key-generation scheme.

   ### 5. **Scalability & High Availability**

   1. **Multiple Application Servers**  
      - Use stateless microservices that handle API requests behind a load balancer.  
      - Shared DB or cluster for storing short-link mappings.

   2. **Distributed Caches**  
      - Keep the most accessed short code mappings in-memory to reduce DB load.

   3. **Redundancy & Replication**  
      - DB replication across data centers for fault tolerance.  
      - If one region or server fails, traffic can fail over.

   4. **CDN** (Optional for static assets)  
      - If the service has a web front end with static content, store it in a CDN.

   5. **Backup & Monitoring**  
      - Back up DB frequently to ensure data recoverability.  
      - Log metrics (number of requests, DB read/write times, response latency) to catch issues early.

   ### 6. **Additional Considerations**

   1. **Security**  
      - Possibly scan or block malicious or phishing URLs.  
      - Rate-limit link creation from suspicious IPs to prevent spam.

   2. **Custom Domains**  
      - Enterprise users or customers might want brand-specific short URLs (e.g., `brand.co/abc`).

   3. **Analytics**  
      - Detailed analytics might require a separate pipeline (e.g., Kafka → Spark → analytics DB).

   4. **UI / Management**  
      - Provide a web interface where users can sign up, manage their short URLs, see stats, set expiry, etc.

   ### 7. **Example Flow**

   1. **User** sends a **POST** to `/shorten` with:
      ```json
      { "original_url": "https://some-long-url.com/articles/...", 
        "custom_alias": "myProject" }
      ```
   2. **Service** checks if `myProject` is free. If not, errors out; otherwise:
      - Insert record into DB: `(short_code="myProject", original_url="https://...")`.
      - Return `{"short_url": "https://tinyurl.com/myProject"}`.

   3. **Later** someone visits: `GET https://tinyurl.com/myProject`:
      - The service looks up "myProject" in cache or DB → original URL is `https://some-long-url.com/articles/...`.
      - Optionally increment counters, track analytics.
      - **Redirect** with **HTTP 302** to the long URL.

   4. **Expiration**: If the record’s `expires_at` is in the past, or usage hits max clicks, the service can respond with **404**.

   ### 8. **Final Architecture Recap**

   1. **API Layer**:  
      - REST endpoints for creating short links, retrieving stats, performing redirects.  
      - Sits behind a load balancer to handle high traffic.

   2. **Application Layer**:  
      - Business logic for short-code generation, collisions, custom aliases, caching, analytics.  
      - Possibly broken into microservices (e.g., `ShortURLService`, `RedirectService`, `AnalyticsService`).

   3. **Persistence Layer**:  
      - Database (SQL or NoSQL) to store `(short_id, original_url, optional_metadata)`.  
      - In-memory cache (Redis) for frequent lookups.

   4. **Scale & Availability**:  
      - Horizontal scaling of stateless app servers.  
      - DB partitioning or NoSQL for billions of short URLs.  
      - Replication for fault tolerance, global load balancing for worldwide coverage.

   ### **Conclusion**

   A robust URL Shortening Service requires careful planning around **API design**, **high-volume read and write** patterns, **fast lookups** (caching + efficient DB), and features like custom aliases, analytics, and expiration. With a distributed architecture—load balancing at the front, stateless microservices in the middle, and a partitioned/replicated datastore at the back—such a service can scale to handle thousands of requests per second while remaining responsive and highly available.

   ---

   ## 31. Design YouTube/Netflix (A Global Live Video Streaming Service)
   Design a video streaming service like Youtube/Netflix where users can upload/view/search videos. The service should be scalable where a large number of users can watch and share the videos simultaneously. It will be storing and transmitting petabytes and petabytes of data. 

   Things to discuss and analyze: 
   Approach to record stats about videos e.g. the total number of views, up-votes/down-votes, etc.
   Adding comments on videos in real-time.
   ### 1. **Core Requirements**

   1. **Upload and Store Videos**  
      - Users can upload large video files.  
      - The system must store petabytes of data durably.

   2. **Transcode and Adaptive Streaming**  
      - Convert uploaded content into multiple resolutions/bitrates for various devices.  
      - Provide adaptive bitrate streaming (HLS, DASH, etc.).

   3. **Global Content Delivery**  
      - Deliver smooth video playback worldwide, handle high concurrency.  
      - Use CDNs to minimize latency.

   4. **Search and Discovery**  
      - Users can search videos by title, tags, metadata.  
      - Personalized recommendations, trending lists.

   5. **Engagement Features**  
      - Track views, likes/dislikes, watch times, real-time comments.  
      - Possibly real-time chat for live streaming or near-live events.

   6. **Scalability & High Availability**  
      - Potentially millions of concurrent viewers.  
      - Redundant, fault-tolerant infrastructure.

   ### 2. **High-Level Architecture**

   ```
                  ┌─────────────────────────┐
                  │  Client Devices (Apps)  │
                  └─────────────────────────┘
                          |    (HTTPS/WebSockets/...) 
                          v
             ┌─────────────────────────────────────────┐
             │  API Gateway / Load Balancer           │
             └─────────────────────────────────────────┘
                          |
                          v
         ┌───────────────────────────────────────────────┐
         │   Microservices (Upload, Stream, Search, etc)│
         └───────────────────────────────────────────────┘
             |              |              |
             v              v              v
   ┌────────────────┐   ┌───────────────┐   ┌──────────────────┐
   │ Video Storage   │   │ Metadata DB   │   │ Analytics / Stats │
   │  (S3/HDFS, etc.)│   │ (SQL/NoSQL)   │   │   (Real-time)      │
   └────────────────┘   └───────────────┘   └──────────────────┘
                          |
                          v
                     ┌─────────┐
                     │   CDN   │
                     │ (Edge)  │
                     └─────────┘
   ```

   1. **Client Devices**  
      - Web browsers, mobile apps, smart TVs.  
      - Request videos, upload content, post comments, etc.

   2. **API Gateway / Load Balancer**  
      - Routes requests to microservices.  
      - Handles authentication tokens, rate limiting, etc.

   3. **Microservices** (common decomposition)  
      - **Upload Service**: Ingests user-uploaded videos, triggers transcoding.  
      - **Transcoding Service**: Converts raw video to multiple resolutions (1080p, 720p, etc.).  
      - **Stream Service**: Handles video streaming paths, token validation, redirect to CDN.  
      - **Search & Recommendations**: Indexing, full-text search, personalized suggestions.  
      - **Comment/Engagement Service**: Real-time comments, likes/dislikes, view counters.  
      - **Analytics Service**: Aggregates watch times, user interactions, trending data.

   4. **Data Stores**  
      - **Video Storage**: Large distributed file/object store (e.g., AWS S3, Hadoop HDFS).  
      - **Metadata DB**: Could be SQL or NoSQL, storing video metadata (title, tags, user info).  
      - **Analytics/Stats**: Real-time pipelines (Kafka/Spark/Flink) or a specialized timeseries DB.

   5. **CDN**  
      - Edge servers around the globe to cache and deliver video segments quickly.  
      - Minimizes latency and offloads traffic from origin servers.

   ### 3. **Video Upload & Transcoding**

   1. **Upload**  
      - User uploads raw video to a pre-signed URL or the Upload Service.  
      - The service stores the raw file in an object store, then notifies a message queue (e.g., Kafka) that a new file is ready.

   2. **Transcoding Pipeline**  
      - Transcoding Service picks up the job, converts the video into multiple bitrates/resolutions (e.g., 480p, 720p, 1080p).  
      - Splits them into small segments (2–10s) for adaptive streaming protocols (HLS, MPEG-DASH).  
      - Stores transcoded segments in the Video Storage system.  
      - Updates metadata DB to indicate the video’s readiness for streaming.

   ### 4. **Streaming Workflow**

   1. **Request Stream**  
      - Client requests video by ID.  
      - **Stream Service** authenticates user, checks if the video is ready or any access controls (e.g., private, paywall).

   2. **Adaptive Protocol**  
      - The client obtains a manifest (playlist) describing available bitrates.  
      - The media player dynamically requests segments from the best matching bitrate based on network conditions.

   3. **CDN Delivery**  
      - Segments are served from an edge node.  
      - If a segment is not in the CDN cache, it’s fetched from the origin storage.

   ### 5. **Search & Discovery**

   1. **Metadata Index**  
      - For each video: title, description, tags, categories, user info.  
      - Possibly store text data in Elasticsearch or Solr for fast full-text search.

   2. **Recommendations**  
      - Use collaborative filtering or content-based approaches.  
      - Incorporate user history, watch times, likes, subscription data.

   3. **Ranking**  
      - Weighted by recency, popularity, watch metrics, etc.

   ### 6. **Stats & Engagement**

   1. **Views, Likes/Dislikes**  
      - Real-time increment counters or event logs.  
      - Possibly store raw events in a queue (Kafka). Then update counters in a DB or cache.  
      - Show an approximate count quickly (e.g., from a cache) while more precise computations happen asynchronously.

   2. **Comments**  
      - **Comment Service** handles new comments.  
      - Some systems use real-time websockets or polling for comment feeds.  
      - Could store comments in a relational DB or NoSQL. If high volume, consider partitioning by video ID.

   3. **Analytics**  
      - Aggregated watch time, user retention, skip rates, watch-later lists.  
      - Data pipelines for large-scale processing (Spark/Flink/Hadoop).  
      - Provide dashboards or recommendation algorithms with the aggregated data.

   ### 7. **Scalability & Performance**

   1. **Distributed File Storage**  
      - Partition videos across multiple servers or use cloud object stores.  
      - Replicate data across regions for fault tolerance.

   2. **Load Balancing & Microservices**  
      - Horizontal scaling of each service (upload, streaming, search, etc.).  
      - Use container orchestration (Kubernetes) or cloud-based scaling solutions.

   3. **CDN**  
      - Offload 80–90% of traffic from the origin.  
      - Replicate popular videos to edge servers.

   4. **Caching**  
      - For metadata lookups, usage stats, partial queries.  
      - Use Redis or Memcached to reduce DB load.

   5. **Global Deployment**  
      - Host microservices in multiple regions, route users to the closest region.  
      - Handle potential data consistency issues with multi-region replication.

   ### 8. **Fault Tolerance & High Availability**

   1. **Redundancy**  
      - Multiple instances of each microservice behind a load balancer.  
      - Use circuit breakers, timeouts, fallback logic.

   2. **Auto-Scaling**  
      - Spike in live streaming events can trigger many concurrent watchers.  
      - Auto-scale up streaming servers/CDN edges as needed.

   3. **Monitoring & Alerts**  
      - Track latencies, error rates, throughput.  
      - Set up alerting for abnormal spikes or service downtime.

   4. **Backups & Disaster Recovery**  
      - Frequent backups of metadata DB.  
      - Stored video content is typically multi-region replicated.

   ### 9. **Example Data Flow**

   1. **Upload**: User -> (Gateway) -> Upload Service -> Object Store + Notification -> Transcoding Service.  
   2. **Transcode**: Raw -> multiple resolutions -> Store in Object Store -> Update DB.  
   3. **Play**: Client -> (Gateway) -> Stream Service -> Retrieve metadata + manifest -> Segments delivered via CDN.  
   4. **Record View**: On playback start or a threshold, an event logs to Kafka -> Stats aggregator increments view count.  
   5. **Comment**: Client -> Comment Service -> Store in DB -> Possibly push to watchers if real-time.

   ### 10. **Conclusion**

   Designing a large-scale video streaming platform involves orchestrating:

   - **Reliable video ingestion & transcoding** pipelines.
   - **High-throughput storage and global distribution** (CDN) for low-latency playback.
   - **Scalable microservices** for search, metadata, analytics, and user engagement.
   - **Real-time or near real-time stats** capturing views, likes, and comments.

   ---

   ## 32. Design Facebook Messenger or WhatsApp (A Global Chat Service)
   To design a global chat service like Facebook Messenger or WhatsApp, you need to focus on real-time communication, scalability, and reliability. Start with user management for registration and login, then implement messaging using technologies like WebSockets for real-time data transfer. For message storage, use distributed databases to handle large amounts of data across different regions. Ensure reliability with features like delivery receipts and read acknowledgments.

   Things to discuss and analyze: 
   Approach for one-on-one text messaging between users.
   Approach for extending the design to support group chats.
   Delivered and read status
   What action needs to be taken if the user is not connected to the internet?
   Push notifications
   Sending media like images or other documents
   Approach for providing end-to-end message encryption.

   ### 1. **Core Requirements**

   1. **User Management & Authentication**  
      - Users register via phone number or email, set up a profile.  
      - Support secure login tokens (JWT, OAuth, or session keys).  

   2. **Real-Time Messaging**  
      - **One-on-one** chats: Immediately transmit messages from sender to recipient.  
      - **Group chats**: Same logic, but messages are replicated to all group members.  
      - Acknowledge message delivery and read status.

   3. **Offline Handling**  
      - If the recipient is offline, the message is queued and delivered once they reconnect.  
      - Push notifications when the user is not in the app.

   4. **Media Sharing**  
      - Send images, videos, documents. Possibly large file size constraints.  
      - Handle upload and retrieval (often separate from the main chat messages).

   5. **End-to-End Encryption**  
      - Messages are encrypted so that only sender and recipient(s) can read them.  
      - Even the server cannot decrypt message content.

   6. **Global Scalability & High Availability**  
      - Infrastructure that handles millions or billions of messages daily.  
      - Multiple regions, low-latency, and resilient to node failures.

   ### 2. **High-Level Architecture**

   ```
                   ┌───────────────────────────┐
                   │        User Client       │
                   │ (Mobile, Web, Desktop)   │
                   └───────────────────────────┘
                         | (HTTPS, WebSockets, etc.)
                         v
            ┌──────────────────────────────────────────┐
            │   API Gateway / Load Balancer           │
            └──────────────────────────────────────────┘
                         |
                         v
            ┌──────────────────────────────────────────┐
            │ Messaging Service (Microservices)        │
            │  - Connection Manager (WebSockets)       │
            │  - Message Routing & Queueing            │
            │  - Group Chat Logic                      │
            │  - Push Notification Handler             │
            └──────────────────────────────────────────┘
               |              |               |
               v              v               v
      ┌────────────────┐  ┌────────────────┐  ┌────────────────────────────────┐
      │   Data Store   │  │  Key Store     │  │    Media Storage (CDN/S3)     │
      │  (NoSQL/SQL)   │  │ (Encryption)   │  └────────────────────────────────┘
      └────────────────┘  └────────────────┘
   ```

   1. **User Client**  
      - Mobile/desktop apps or web clients maintain persistent connections (e.g., WebSockets) to the server for real-time messaging.

   2. **API Gateway / Load Balancer**  
      - Distributes incoming traffic across multiple **messaging server** instances.  
      - Handles authentication tokens and rate limiting.

   3. **Messaging Service**  
      - **Connection Manager**: Maintains open WebSocket/HTTP2 streams with connected clients.  
      - **Message Router**: Determines which client(s) receive a message.  
      - **Queue/Storage**: Stores undelivered messages if a user is offline, then pushes them once they come online.  
      - **Push Notification Handler**: For offline devices, triggers FCM/APNs notifications.  
      - **Group Chat Logic**: Maintains group membership, sends messages to all participants.

   4. **Data Store**  
      - Could be a **NoSQL** DB (e.g., Cassandra, DynamoDB, MongoDB) for high write throughput, or a sharded **SQL** DB.  
      - Store chat records (conversation ID, message ID, sender, timestamp, etc.).  
      - Possibly separate read/write patterns or store ephemeral messages if end-to-end encrypted.

   5. **Key Store** (for end-to-end encryption)  
      - Manages user public keys, ephemeral keys, or group keys if you need to handle advanced encryption.  
      - Each user has a public/private key pair. Private keys remain on the user’s device.

   6. **Media Storage**  
      - Large or binary files (images, videos) are often stored in a CDN or object store like S3.  
      - Chat messages contain references (URLs) to these media objects.  
      - Possibly end-to-end encrypt the media as well, storing a ciphertext in S3, with the encryption key shared among participants.

   ### 3. **One-on-One Messaging**

   1. **Client A** sends message to **Client B**:  
      - **Messaging Service** receives the message, validates session/user.  
      - If end-to-end encryption is enabled, the message is already encrypted with **B**’s public key.  
      - The service stores the message in DB or a queue.  
      - If **Client B** is online, push the message immediately via WebSocket.  
      - If offline, store the message, set a push notification token.

   2. **Delivery/Read Status**  
      - **Delivery**: Mark message as delivered once it’s handed off to the B’s device.  
      - **Read**: B’s client notifies server when the message is viewed. The server notifies A’s client that the message is “read.”

   3. **Offline Handling**  
      - If B is offline, the message remains in the queue or DB with a “pending” status.  
      - On reconnection, B fetches the pending messages.

   ### 4. **Group Chats**

   1. **Group Membership**  
      - Each group has an ID, list of participants, optional admin roles.  
      - For end-to-end encryption, a **group key** is shared or each participant has an ephemeral key for each session.

   2. **Send Message**  
      - Same flow, but the **Messaging Service** replicates the message to **all participants** in the group.  
      - Possibly store an offset or “last read” marker per user to handle new messages while offline.

   3. **Scalability**  
      - Large group chats with hundreds or thousands of participants can create a fan-out problem.  
      - Solutions:  
        - Shard group participants, or  
        - Use specialized group messaging protocols.  
      - For high-scale group features, many systems put limits on group size.

   ### 5. **Real-Time Delivery & Offline Scenarios**

   1. **Real-Time**  
      - Use persistent connections (e.g., WebSocket, MQTT, or XMPP) to push messages instantly.  
      - Low-latency, typically sub-second.

   2. **Offline**  
      - If a device can’t maintain a persistent connection: store messages in a queue.  
      - **Push notifications** via APNs (iOS), FCM (Android), or a web push for browsers.  
      - Device wakes up, connects to the server, fetches new messages.

   ### 6. **Push Notifications**

   1. **Trigger**  
      - The messaging service triggers a push notification when a user is offline (or backgrounded).  
      - The push payload may contain minimal info for privacy (e.g., “New message from X”).

   2. **Delivery**  
      - Integrate with Apple Push Notification service (APNs), Firebase Cloud Messaging (FCM), or other push providers.  
      - On device receiving the push, user opens the app, which fetches full message data from the server.

   ### 7. **Media Sending (Images/Docs)**

   1. **Upload**  
      - The client can upload the media to a dedicated **Media Service** or directly to a cloud store with a pre-signed URL.  
      - The server returns a reference/URL.

   2. **Message**  
      - The chat message includes the reference to the stored media.  
      - For end-to-end encryption, the media is encrypted with a random key, and that key is shared with recipients. The file stored on the server is ciphertext.

   3. **Download**  
      - Recipients request or stream the media from the storage (CDN).  
      - They decrypt locally using the shared key if E2E is used.

   ### 8. **End-to-End Encryption (E2EE)**

   1. **Key Management**  
      - Each user has a public/private key pair. Private key never leaves the user’s device.  
      - For ephemeral messages or advanced group E2EE, might use protocols like the **Signal** protocol, distributing ephemeral session keys.

   2. **Message Flow**  
      - **Sender** encrypts message with the recipient’s (or group’s) public key.  
      - The **server** sees only ciphertext.  
      - **Recipients** decrypt with their private key.

   3. **Challenges**  
      - Group membership changes require re-keying or forward secrecy.  
      - Key backups/restoration if the user loses their device can be tricky.

   ### 9. **Data Storage & Scalability**

   1. **Message Storage**  
      - Billions of messages daily implies a **distributed NoSQL** approach (Cassandra, DynamoDB, HBase) or a well-sharded SQL.  
      - Typically store essential metadata (sender, timestamp, recipient ID, etc.) plus the ciphertext or a reference if large data.

   2. **User State**  
      - Track user presence (online/offline), last-seen timestamp.  
      - Possibly store ephemeral presence info in an in-memory store like Redis.

   3. **Sharding**  
      - Partition data by user_id or conversation_id for horizontal scaling.  
      - Maintain cross-region replication for global coverage and failover.

   4. **Analytics** (Optional)  
      - For usage patterns, system health. Possibly process logs in real-time using Kafka + Spark/Flink.

   ### 10. **Reliability & High Availability**

   1. **Multiple Instances**  
      - Deploy many messaging servers behind a load balancer.  
      - Each server can handle tens of thousands of open connections.

   2. **Failover**  
      - If one node or region fails, connections are re-routed to a healthy node.  
      - Data replication ensures messages aren’t lost.

   3. **Monitoring & Alerting**  
      - Track latencies, error rates, concurrency.  
      - Automated alerts if message throughput or queue sizes go abnormal.

   ### 11. **Example End-to-End Flow (One-on-One)**

   1. **User A** logs in, obtains auth token, opens a WebSocket to the **Messaging Service**.  
   2. **User B** is also online or offline.  
   3. **A sends** “Hello B” (encrypted if E2EE).  
   4. **Messaging Service** receives the message:  
      - Writes it to the DB as “undelivered” if B is offline, or tries direct push if B is online.  
      - If B is offline, triggers a push notification.  
      - Once B’s client acknowledges, mark “delivered.”  
      - When B reads it, mark “read.”  
   5. **A** sees “message delivered” and eventually “message read.”

   ### 12. **Conclusion**

   A global chat system like Facebook Messenger or WhatsApp requires:

   1. **Robust Real-Time Layer**: WebSockets or a persistent protocol (like XMPP, MQTT) to deliver messages instantly.  
   2. **Distributed Storage**: Large-scale, partitioned DB for billions of messages.  
   3. **Offline & Push**: Queue undelivered messages, trigger push notifications for offline users.  
   4. **Group & E2EE**: Extended design to handle group membership, ephemeral keys, and advanced encryption protocols for privacy.  
   5. **High Availability**: Multiple server instances across regions, load balancing, data replication, real-time monitoring.  

   ---

   ## 33. Design Quora/Reddit/HackerNews (A Social Network + Message Board Service)
   These services allow users to post questions, share links and answer the questions of other users. Users can also comment on questions or shared links. 

   Things to discuss and analyze:
   Approach to record stats of each answer such as the number of views, up-votes/down-votes, etc.
   Follow options should be there for users to follow other users or topics.
   News feed generation which means users can see the list of top questions from all the users and topics they follow on their timeline.
   ### 1. **Core Requirements**

   1. **User Profiles & Authentication**
      - Register and sign in users (email, username/password, or OAuth).
      - Maintain profile info (bio, interests, followed topics, etc.).

   2. **Content Creation**
      - **Questions/Posts**: Users can ask questions, share links, or post topics.
      - **Answers/Comments**: Others can respond, discuss, or comment.
      - **Upvotes/Downvotes**: Let users indicate the quality or popularity of content.

   3. **Topic/Tag System**
      - Associate questions/posts with tags or topics (e.g., “JavaScript,” “Politics,” “Science”).
      - Users can follow topics to see relevant content in their feed.

   4. **Feed / Timeline Generation**
      - Show top or relevant questions/posts to each user (based on followed topics, user preferences, or global popularity).
      - Sort by popularity (upvotes, recency, or a combination).

   5. **Engagement & Stats**
      - Record views, upvotes/downvotes, comment counts.
      - Possibly store more advanced analytics: dwell time, user location, etc.

   6. **Scalability & Performance**
      - Handle potentially millions of posts/questions and concurrent users.
      - Efficiently retrieve the best or most relevant content for each user.

   ### 2. **High-Level Architecture**

   ```
                ┌─────────────────────────┐
                │     User Client        │
                │ (Browser, Mobile App)  │
                └─────────────────────────┘
                        |
                 (HTTP / HTTPS)
                        v
         ┌──────────────────────────────────────┐
         │  API Gateway / Load Balancer        │
         └──────────────────────────────────────┘
                        |
                        v
         ┌──────────────────────────────────────┐
         │   Microservices (Content, Feed,     │
         │   User, Topic, Voting, etc.)        │
         └──────────────────────────────────────┘
               |            |              |
               v            v              v
   ┌─────────────────┐  ┌───────────────┐  ┌───────────────────┐
   │Data Storage (DBs)│  │ Cache/Redis   │  │ Search/Index      │
   │(SQL/NoSQL)       │  └───────────────┘  │(Elasticsearch/Solr)│
   └─────────────────┘                       └───────────────────┘
   ```

   1. **User Client**
      - Web or mobile front-end that displays feeds, topics, user profiles.
      - Sends requests to the backend to fetch posts, create answers, upvote, etc.

   2. **API Gateway / Load Balancer**
      - Routes requests to multiple stateless service instances.
      - Handles authentication tokens, rate limiting, etc.

   3. **Microservices** (common decomposition)
      - **User Service**: Manages user profiles, authentication, follow relationships.
      - **Content Service**: Handles creation of questions/posts, answers, comments.
      - **Feed Service**: Aggregates or ranks content for each user’s feed.
      - **Voting/Analytics Service**: Records votes, up/down counters, or advanced analytics.
      - **Topic/Tag Service**: Maintains categories, topics, or tags.

   4. **Data Storage**
      - **Relational DB** (e.g., Postgres/MySQL) or **NoSQL** (e.g., MongoDB, Cassandra) for main content (posts, comments).
      - **Cache** (Redis, Memcached) to quickly retrieve hot or frequently accessed data (popular posts, feed results).
      - **Search Index** (Elasticsearch/Solr) for fast text queries, advanced filtering/sorting by relevance.

   ### 3. **Data Model**

   Possible database tables/collections (simplified):

   1. **Users**
      - `user_id`, `username`, `hashed_password`, `bio`, `joined_date`, `followers[]`, `following_topics[]`
   2. **Posts/Questions**
      - `post_id`, `author_id`, `title`, `body/content`, `created_at`, `updated_at`, `topics[]`
   3. **Answers/Comments** (could be a separate entity or nested under posts if NoSQL)
      - `answer_id`, `post_id`, `author_id`, `content`, `created_at`, `upvotes_count`, etc.
   4. **Votes**
      - `vote_id`, `user_id`, `post_id_or_answer_id`, `vote_type` (up or down), `timestamp`
   5. **Topic/Tag**
      - `topic_id`, `name`, `description`

   ### 4. **Content Creation & Interaction**

   1. **Creating Posts/Questions**
      - User composes a question/post with a title, description/body, optional link or reference, and selects relevant topics/tags.
      - Server stores the content in the DB, indexes text in the search engine.

   2. **Answering / Commenting**
      - Another user can post an answer or comment. They reference the `post_id` in the DB.
      - Possibly store comments in a separate table or nested under the answer (in NoSQL).

   3. **Voting**
      - Upvote or downvote a post/answer.  
      - Tally votes in real-time or near real-time.  
      - Might keep a “score” column updated for quick sorting.

   ### 5. **Follow & Notification System**

   1. **Following Users or Topics**
      - A user can follow another user, so that user’s posts/answers appear in the follower’s feed.
      - Similarly, following a topic means the user’s feed shows new questions tagged with that topic.

   2. **Notifications** (Optional)
      - If user A follows user B, user A can get a notification when B posts something new.
      - Alternatively, a “Notifications Service” can handle mention notifications, direct replies, etc.

   ### 6. **Feed Generation**

   1. **Personalized Feed**
      - The feed could show top or recent posts from:
        - The user’s followed topics
        - The user’s followed users
        - Or a global popularity feed if the user has no follows.
      - Sort by recency, vote score, or a custom ranking formula (like Reddit’s “hot” ranking or Hacker News’ “gravity” formula).

   2. **Feed Service Logic**
      - Could store pre-computed feed data in a cache for each user, updated as new posts arrive.
      - Alternatively, dynamically query the DB or search engine with filters (“topic in user.followed_topics”).

   3. **Ranking / Sorting**
      - Weighted approach: recency + upvotes - downvotes + user engagement.  
      - Some sites apply a time-decay factor, so older posts gradually move down.

   ### 7. **Stats & Analytics**

   1. **View Counting**
      - Each time a post or answer is viewed, increment a “view_count” in a DB or push an event to an analytics pipeline.
   2. **Upvotes/Downvotes**
      - Tally in a column or maintain a separate table to track each user’s vote. Summaries can be updated in real time or asynchronously.
   3. **Trending / Popularity**
      - Use aggregated stats (views, votes, comments) to rank top content in the last 24 hours, 1 week, etc.

   ### 8. **Search & Discovery**

   1. **Keyword Search**
      - Index post titles, bodies, and tags in Elasticsearch or Solr.
      - Provide full-text search to find relevant posts/questions quickly.
   2. **Filtering by Topic or Tag**
      - “Show me posts about [Machine Learning].”
   3. **Ranking**
      - Return results by text relevance, upvotes, recency, or a combination.

   ### 9. **Scalability & Performance**

   1. **Horizontal Scaling**
      - Multiple application servers behind a load balancer.  
      - Database sharding or partitioning by `post_id` or `topic_id`.
   2. **Caching**
      - Cache frequently accessed posts, top feed results, or user data in Redis.
      - Results in lower latency for common queries.
   3. **Eventual Consistency**
      - Some counters (like upvote totals) might be updated asynchronously.  
      - Perfect real-time accuracy is less critical, so it can be eventually consistent.

   ### 10. **Example Workflow: User Browses Feed & Votes**

   1. **User** logs in (API Gateway handles auth).  
   2. **Client** requests `/feed` from the **Feed Service**.  
      - The service queries the DB or a precomputed feed list for that user.  
      - Returns top posts sorted by relevance.  
   3. **User** upvotes a post.  
      - The client sends a `POST /vote` request with `vote_type=up`.  
      - **Voting Service** writes or updates a record in the `Votes` table (or increments a counter).  
      - Possibly updates the `post.score` in the DB or triggers an event for re-ranking.  
   4. **Feed** might be re-fetched or updated in real time if needed.

   ### 11. **Implementation Tips**

   1. **UI**: Provide a user-friendly web or mobile interface with infinite scrolling for new posts.  
   2. **Real-Time Updates** (Optional):
      - Could integrate WebSockets or SSE for real-time notifications of new comments, votes.  
   3. **Spam & Moderation**:
      - Important for large communities. Possibly have user reputation systems, auto-moderation tools, or human moderators.  
   4. **Microservices**:
      - Each area (users, posts, feed, search, votes) can scale independently.  
      - Or a monolith with well-structured modules is feasible for smaller scale.

   ### 12. **Conclusion**

   Designing a platform like Quora, Reddit, or Hacker News involves:

   1. **User & Topic Management**: Follow relationships, user profiles, topic tagging.  
   2. **Content Creation & Interaction**: Posts, answers, comments, up/down votes.  
   3. **Feed Generation**: Personalized or topic-based streams.  
   4. **Ranking & Search**: Combine user signals (votes, views) with text relevance.  
   5. **Scalability**: Handle millions of users, billions of posts, and real-time engagement data.

   ---

   ## 34. Design Search Typeahead (Autocomplete)
   Typeahead service allows users to type some query and based on that it suggests top searched items starting with whatever the user has typed.

   Things to discuss and analyze:
   Approach to storing previous search queries
   Real-time requirement of the system
   Approach to keep the data fresh.
   Approach to find the best matches to the already typed string
   Queries per second are to be handled by the system.
   Criteria for choosing the suggestions.
   A total number of data to be stored.
   Approach to find the best matches to the already typed string

   ### 1. **Core Requirements**

   1. **Real-Time Suggestions**  
      - As the user types each character, display top matches within milliseconds.

   2. **Data Freshness**  
      - Frequently updated index so new trends/queries appear quickly in suggestions.

   3. **Ranking & Relevance**  
      - Offer the most relevant or popular suggestions first based on partial input.

   4. **High Throughput**  
      - Handle potentially thousands of queries per second, especially for large-scale search engines.

   ### 2. **High-Level Architecture**

   ```
           ┌───────────────┐
           │   User Client │
           └───────────────┘
                |   (HTTP requests as user types)
                v
     ┌──────────────────────────────────┐
     │  API Gateway / Load Balancer    │
     └──────────────────────────────────┘
                |
                v
     ┌──────────────────────────────────┐
     │ Typeahead Service (Backend)     │
     │ - Query index / Trie / Suggester│
     │ - Ranking & popularity data     │
     └──────────────────────────────────┘
                |
                v
     ┌──────────────────────────────────┐
     │  Data Store(s) / Index          │
     │  - Possibly a trie or a search  │
     │    engine's completion index    │
     │  - Real-time updates (popularity|
     │    counts, trending queries)    │
     └──────────────────────────────────┘
   ```

   1. **User Client**  
      - Could be a web or mobile app that sends a request on each keystroke (with a small debounce).

   2. **API Gateway / Load Balancer**  
      - Distributes requests to multiple **Typeahead Service** instances.

   3. **Typeahead Service (Backend)**  
      - Receives the partially typed query, looks up suggestions from a data structure (e.g., a prefix tree or specialized search engine autocomplete index).  
      - Applies ranking logic (popularity, recency, textual relevance).  
      - Returns top N suggestions.

   4. **Data Store / Index**  
      - Could be:
        - A **Trie/Prefix Tree** with popularity counters.  
        - A completion index in something like **Elasticsearch** or Solr.  
        - A specialized in-memory data structure with partial string matching.

   ### 3. **Data Storage Approaches**

   1. **Prefix Tree (Trie)**
      - Stores each possible prefix in a tree-like structure.
      - Each node can maintain a list of top suggestions (based on popularity).
      - **Pros**: Fast prefix lookups, memory-efficient for shorter strings.
      - **Cons**: Might become very large if you store millions of queries.

   2. **Search Engine Autocomplete (Elasticsearch, Solr)**
      - Many engines provide a “completion suggester” index.
      - **Pros**: Built-in text scoring, partial matching, fuzzy matching.  
      - **Cons**: Might be more heavyweight, requires cluster management.

   3. **In-Memory Key-Value Store + Ranking**
      - A **Map** from prefix → suggestions sorted by popularity.
      - Possibly a “prefix” to “list of top items” approach (like storing each prefix in Redis, up to a certain length).
      - **Pros**: Very fast lookups if memory is sufficient.
      - **Cons**: Large memory footprint, frequent updates can be expensive.

   ### 4. **Keeping Data Fresh**

   1. **Batch Updates**  
      - Periodically rebuild the suggestion index (e.g., every hour or day) from logs of user queries or popular items.
      - Simpler to implement but might not reflect recent trends quickly.

   2. **Real-Time / Near Real-Time**  
      - Stream incoming queries or trending data into an event pipeline (e.g., Kafka).  
      - Update the index or trie counters for popularity in near real-time.
      - More complex but ensures the system quickly adapts to emerging queries.

   3. **Hybrid**  
      - A combination: batch-based large rebuild + near real-time incremental updates.

   ### 5. **Ranking & Suggestion Logic**

   1. **Popularity-Based**
      - Each string/prefix node stores a popularity score (e.g., query frequency over last 7 days).
      - Return top matches sorted by descending popularity.

   2. **Textual Relevance**
      - For ambiguous or multi-term queries, might factor in string matching relevance (e.g., prefix match, phrase match).
      - Possibly incorporate synonyms, fuzzy matching for typos.

   3. **Personalization** (Optional)
      - If the user is logged in, factor their past searches or user profile into suggestions.
      - Combine global popularity with user’s personal usage patterns.

   4. **Time Decay** (Optional)
      - Queries that spike recently get a boost in ranking over older but historically popular ones.

   ### 6. **Handling High QPS**

   1. **Caching**
      - Many repeated partial queries (e.g., “new y” if lots of people search “new york”).
      - Maintain an LRU cache of recent queries → top suggestions, to avoid repeated index lookups.

   2. **Horizontal Scaling**
      - Multiple Typeahead Service nodes behind a load balancer.
      - Each node has a local copy of the prefix structure or a shared distributed index.

   3. **Efficient Data Structures**
      - Minimizing memory overhead with tries or specialized suffix arrays.
      - Use concurrency-friendly index updates (or single-writer, multiple-reader patterns).

   4. **Sharding** (if the dataset is huge)
      - If storing billions of possible queries, partition them by first letter or hash to scale horizontally.

   ### 7. **Real-Time Query Flow**

   1. **User** types: “new y.”
   2. **Client** sends an HTTP request to `/autocomplete?q=new%20y`.
   3. **Typeahead Service**:
      - Looks up prefix “new y” in an index (e.g., trie node or search engine completion index).
      - Retrieves top N completions (like “new york”, “new year”, etc.), sorted by popularity.
   4. **Service** returns suggestions in ~10-50ms.
   5. **Client** displays them below the search box.

   ### 8. **Example Implementation Steps**

   1. **Initialize Index**
      - Load an existing dictionary of queries or items (e.g., all city names, product names) with popularity scores.
   2. **Trie/Completion Index**  
      - Insert each item with its popularity metric.
   3. **Serve Requests**
      - On user’s partial input, traverse or query the index to get matching suggestions.
      - Sort by popularity or relevance, return top K.
   4. **Data Refresh**
      - Periodically (hourly, daily) rebuild or partially update the popularity counters from logs.
      - If near real-time, apply increments to the relevant entries as new queries arrive.
   5. **Caching Layer**  
      - If “new y” is typed frequently, store the suggestions in Redis or memory for quick retrieval.

   ### 9. **Additional Features**

   1. **Fuzzy Matching**  
      - If a user types “nw yrok,” system suggests “new york.”  
      - Common in advanced systems, but requires more complex indexing (e.g., Levenshtein automaton or search engine fuzzy queries).
   2. **Contextual Suggestions**  
      - If the system knows the user’s location or prior searches, you can rank local or related queries higher.
   3. **Autosuggest**  
      - Provide trending or popular queries even before a user types anything, or after 1-2 characters.

   ### 10. **Conclusion**

   A **Search Typeahead** service must combine a **high-performance prefix-based data structure** with **real-time or near real-time updates** to reflect the latest popular queries. By storing partial prefixes in a **Trie** or leveraging a **search engine completion index**, you can deliver top suggestions within milliseconds. **Caching** repeated queries, **ranking by popularity**, and **scaling horizontally** ensures the service can handle high QPS while maintaining low latency.

   ---

   ## 35 Design Dropbox/Google Drive/Google Photos (A Global File Storage and Sharing Service)
   Design a file or image hosting service that allows users to upload, store, share, delete, and download files or images on their servers and provides synchronization across various devices.

   Things to discuss and analyze:
   Approach to upload/view/search/share/download files or photos from any device.
   Service should support automatic synchronization between devices, i.e., after updating a file on one device, it should get synchronized on all devices.
   ACID (Atomicity, Consistency, Isolation, and Durability) properties should be present in the system.
   Approach to track permission for file sharing.
   Allowing multiple users to edit the same document.
   The system should support storing large files up to a GB.
   ### 1. **Core Requirements**

   1. **File Storage and Retrieval**  
      - Users can upload large files (potentially up to gigabytes).  
      - Download or view files from any device.  
      - Preserve file metadata (name, size, type, timestamps).

   2. **Synchronization**  
      - Automatic updates across devices if a file changes.  
      - Possibly keep version history (for rollback or conflict resolution).

   3. **Sharing & Permissions**  
      - Users can share files/folders with other users (read-only, edit access).  
      - Public links if desired (e.g., read-only download links).

   4. **Collaboration**  
      - Support multiple users editing the same document.  
      - Mechanism to handle or merge concurrent edits (depending on file type).

   5. **Reliability & Availability**  
      - Data must be durable (no data loss) with replication across data centers.  
      - The service must handle partial failures gracefully.

   6. **ACID Properties**  
      - For key metadata transactions and file version updates, ensure correctness.  
      - Full ACID on large file writes can be relaxed, but we maintain at least atomic file updates.

   7. **Scalability**  
      - Potentially millions or billions of files.  
      - Handle large volumes of read/write requests concurrently.

   ### 2. **High-Level Architecture**

   ```
                      ┌─────────────────────────┐
                      │     User Devices        │
                      └─────────────────────────┘
                               |
                        (HTTPS / APIs)
                               v
             ┌─────────────────────────────────────────┐
             │   API Gateway / Load Balancer          │
             └─────────────────────────────────────────┘
                               |
                               v
     ┌───────────────────────────────────────────────────────┐
     │    Microservices (Metadata, File, Auth, Sync, etc.)   │
     └───────────────────────────────────────────────────────┘
               |                  |                   |
               v                  v                   v
   ┌──────────────────┐   ┌────────────────┐  ┌───────────────────────────────┐
   │Metadata Database  │   │ Sync/ChangeLog │  │Object Storage / File Store   │
   │(SQL/NoSQL)        │   │(DB or queue)   │  │ (S3, GCS, HDFS, etc.)        │
   └──────────────────┘   └────────────────┘  └───────────────────────────────┘
   ```

   1. **User Devices**  
      - Desktop, mobile, or web clients that upload, browse, edit, or sync files.

   2. **API Gateway / Load Balancer**  
      - Routes incoming requests to various services (metadata, file service, etc.).  
      - Manages authentication tokens, rate limits, etc.

   3. **Microservices**  
      - **Metadata Service**: Tracks file metadata (name, owner, location, version, permissions).  
      - **File/Storage Service**: Manages actual file storage (upload, download) – typically using a large-scale object storage system.  
      - **Auth/Access Control Service**: Validates user credentials, issues tokens, checks permissions.  
      - **Sync Service**: Keeps track of changed files and notifies devices to pull updates or merges changes.

   4. **Data Stores**  
      - **Metadata Database**: Stores file/folder tree structure, version info, permissions (SQL or NoSQL, depending on scale).  
      - **Sync/Change Log**: Possibly a queue or DB table that logs updates for each user or folder.  
      - **Object Store**: A distributed storage system (AWS S3, GCP Storage, HDFS, or on-prem solutions) for large file data.  
      - Replicates data across regions for durability.

   ### 3. **File Upload & Download Workflows**

   ### 3.1 **Upload**

   1. **Client** requests an upload token or location from the **File Service**.  
   2. **File Service** or a pre-signed URL approach is returned to the client (so the client can upload directly to the object store).  
   3. **Client** uploads the file data (possibly chunked for large files).  
   4. **File Service** on success:
      - Creates or updates an entry in **Metadata DB** (filename, size, hash, version, etc.).  
      - Possibly sends a notification to the **Sync Service** for other devices.

   ### 3.2 **Download**

   1. **Client** fetches file metadata (verifies user permission).  
   2. **Client** either downloads directly from the object store or via the **File Service** (depending on architecture).  
   3. **Metadata** can confirm the file’s current version, last-modified time, etc.

   ### 4. **Synchronization**

   1. **Change Logs**  
      - Each time a file is updated, the system writes an event (“file X updated to version Y, by user Z”).  
      - A **Sync Service** or daemon listens for these events and updates relevant user/device sessions.

   2. **Device Clients**  
      - Periodically poll or maintain a push connection for changes.  
      - On receiving update info, either automatically downloads the new version or merges if collaborative editing is supported.

   3. **Conflict Handling**  
      - If two devices update the same file offline and re-sync later, the system can create separate versions (e.g., “conflicted copy”) or attempt merging (like Google Docs real-time merges).

   ### 5. **Sharing & Permissions**

   1. **Access Control**  
      - **Metadata DB** stores who can read/write each file or folder.  
      - For public sharing, generate a unique link with a token that grants read (or write) access.

   2. **Permission Check**  
      - On each file operation, the service checks if the user or token has the right to read or modify.  
      - Possibly implemented with role-based (owner, editor, reader) or link-based ACL.

   3. **Groups or Collaboration**  
      - For multiple users editing, store a shared folder or a doc with multiple collaborators.  
      - The system merges changes or locks sections if strongly consistent edits are needed.

   ### 6. **Collaboration & Multi-User Editing**

   1. **Document Locking** (Basic approach)  
      - If a user opens a file for editing, it’s locked. Others see it as read-only until unlocked.  
      - Simpler to implement, but not real-time multi-edit.

   2. **Real-Time Collaboration** (Advanced)  
      - Protocols like **Operational Transforms** or **CRDTs** (Conflict-free Replicated Data Types) can merge concurrent edits.  
      - Typically used for text-based documents (Google Docs, for instance).

   ### 7. **Versioning & ACID Properties**

   1. **Version Control**  
      - Each upload creates a new version. The previous versions remain accessible or can be garbage-collected after a certain time.  
      - **ACID** typically applies to metadata transactions:  
        - **Atomic**: Updating a file’s version pointer either succeeds fully or not at all.  
        - **Consistency**: The metadata stays consistent with the object store references.  
        - **Isolation**: Parallel operations on different files or file versions shouldn’t conflict.  
        - **Durability**: Once a file or version is committed, it’s stored durably in the DB and object store.

   2. **Two-Phase Commit** (Optional)  
      - If using a separate DB for metadata and an object store for file data, a **two-phase commit** or a saga-like approach can keep them in sync.  
      - Usually a simpler approach is used: write the file to object storage first, then update the DB in a single transaction.

   ### 8. **Large File Handling**

   1. **Chunked Uploads**  
      - For multi-GB files, break them into smaller chunks (e.g., 4–8 MB).  
      - Upload each chunk, then finalize a “complete upload” call.

   2. **Resume Support**  
      - If an upload fails midway, the client can resume from the last chunk.  
      - The server or object store tracks which chunks are present.

   3. **Parallel Upload**  
      - For fast uploading, the client can upload multiple chunks in parallel.

   ### 9. **Scalability & Reliability**

   1. **Load Balancing & Horizontal Scaling**  
      - Multiple front-end servers handle metadata queries.  
      - Object store is typically distributed or cloud-based (e.g., Amazon S3) for near-infinite scale.

   2. **Caching**  
      - Cache frequently accessed metadata or file pointers (e.g., in Redis).

   3. **Replication**  
      - The object store replicates data across availability zones or data centers for fault tolerance.  
      - The metadata DB may replicate or use multi-region setups for high availability.

   4. **Monitoring & Alerts**  
      - Track latencies, error rates, storage usage.  
      - Trigger alerts if sync backlog or queue grows excessively.

   ### 10. **Security & Encryption**

   1. **Encryption at Rest**  
      - Object store can encrypt files with KMS keys.  
      - The metadata DB can also encrypt sensitive fields.

   2. **Encryption in Transit**  
      - Use HTTPS/TLS for all client-server and inter-service communications.

   3. **Access Control**  
      - Strict enforcement of user authentication tokens, permission checks on each file/folder.

   ### 11. **Example Workflow: File Update**

   1. **User** modifies a file locally.  
   2. **Client** detects the file changed.  
   3. **Client** splits the file into chunks (if large) and uploads them to the object store, referencing the new version.  
   4. **Metadata Service** is updated with `(file_id, new_version, user_id, timestamp)`.  
   5. **Sync Service** logs an event `(file X updated by user Y)`.  
   6. Other devices or collaborators see the update event and download the new version if needed.

   ### 12. **Conclusion**

   To design a **global file storage and sharing service** like Dropbox/Google Drive:

   - **File & Metadata Separation**: Store actual files in a large-scale object store; keep metadata in a DB.  
   - **Sync & Versioning**: Log changes, notify connected clients, handle offline merges or real-time edits.  
   - **Sharing & Permissions**: An ACL system that supports user-based or link-based sharing.  
   - **Scalability & Resilience**: Horizontal scaling of microservices, replication in DB/object store, caching for hot data.  
   - **ACID**: Enforce strong transactional guarantees on metadata updates while storing large file data in a robust object store.  

   ---

   ## 36. Design a Web Crawler
   Design a Web Crawler scalable service that collects information (crawls) from the entire web and fetches hundreds of millions of web documents.

   Things to discuss and analyze:
   Approach to finding new web pages.
   Approach to prioritize web pages that change dynamically.
   Ensure that the crawler is not unbounded on the same domain.
   ### 1. **Core Requirements**

   1. **Massive Scale**  
      - Must crawl and index billions of URLs over time.  
      - Horizontal scaling across many crawler instances or data centers.

   2. **Discovery of New Pages**  
      - Continuously find new pages via links from already discovered pages.  
      - Possibly incorporate external data sources (sitemaps, user-submitted links).

   3. **Prioritization & Scheduling**  
      - Some pages need more frequent refresh (e.g., news sites, dynamic content).  
      - Must not overwhelm any single website.

   4. **Politeness & Domain Constraints**  
      - Respect `robots.txt` directives, domain-specific crawl-delay.  
      - Avoid infinite loops (e.g., repeated or cyclical URLs).

   5. **Data Storage & Indexing**  
      - Store fetched pages or relevant metadata for indexing or further analysis.  
      - Possibly coordinate with a separate search index pipeline.

   6. **Scalability & High Availability**  
      - Add more crawler nodes as needed.  
      - Redundant or replicated infrastructure for fault tolerance.

   ### 2. **High-Level Architecture**

   ```
                  ┌─────────────────────┐
                  │   Crawler Workers   │
                  └─────────────────────┘
                    |           ^
       [Web/HTTP]    |           |
                    v           |
            ┌─────────────────────────┐
            │     URL Frontier        │
            │ (Queue / Priority Q)    │
            └─────────────────────────┘
                    |
                    v
          ┌─────────────────────────────┐
          │   Page Fetching & Parsing  │
          └─────────────────────────────┘
                    |
                    v
      ┌────────────────────────────────────────┐
      │  Data Storage / Index (Crawled Pages) │
      └────────────────────────────────────────┘
                    |
                    v
          ┌───────────────────────────┐
          │  Scheduler & Analytics    │
          │   (re-crawl strategy)     │
          └───────────────────────────┘
   ```

   1. **URL Frontier** (Queue)
      - Central or distributed structure containing URLs to be visited.
      - Maintains priority (score) for each URL (e.g., domain authority, last-crawl time).

   2. **Crawler Workers**
      - Multiple processes/servers that pop URLs from the Frontier.
      - Fetch the page, parse links, handle HTTP responses, respect robots.txt.

   3. **Page Fetching & Parsing**
      - **HTTP fetch** with robust error handling, timeouts.
      - **HTML Parsing** to extract links, metadata.
      - Possibly handle JavaScript if crawling dynamic sites (headless browser approach).

   4. **Data Storage / Index**
      - Fetched pages stored in a distributed store (e.g., HDFS, S3, or a large-scale NoSQL).
      - Optionally store partial or full text, or just metadata and link graph.
      - A separate **search index** pipeline can consume this data for full-text indexing.

   5. **Scheduler & Analytics**
      - Decides re-crawl intervals for updated pages.
      - Analyzes link popularity or external signals to set priority.

   ### 3. **Finding New Pages**

   1. **Extract Links** from each fetched page.
      - Filter out duplicates and disallowed URLs.
      - Insert newly discovered links into the URL Frontier with default or minimal priority.

   2. **Sitemaps / External Feeds**  
      - Some sites provide `sitemap.xml` with structured lists of URLs and change frequency.
      - The crawler can inject these URLs into the frontier to ensure coverage.

   3. **User Submissions** (optional)
      - Users or site owners can submit URLs to be crawled.

   ### 4. **URL Frontier & Scheduling**

   1. **Priority Queue**  
      - Each URL has a **score** (based on link popularity, domain authority, or recency).
      - Higher score → crawled sooner.  
      - Could be implemented with a min-heap or a distributed queue system.

   2. **Politeness**  
      - Group URLs by domain or host.  
      - Enforce per-domain concurrency limits or a minimum delay between requests to the same domain.

   3. **Re-crawl Strategy**  
      - Keep track of last crawl time.  
      - If the site changes frequently, schedule it more often.
      - Use heuristics or known update cycles (e.g., news sites might be re-crawled hourly; static sites less frequently).

   ### 5. **Prioritizing Dynamic Pages**

   1. **Historical Update Patterns**  
      - If a page often changes, assign it a higher re-crawl frequency.

   2. **External Signals**  
      - Social media mention spikes, or site-provided `last-modified` headers.
      - Real-time signals if available, e.g., ping services that indicate updates.

   3. **Domain Authority**  
      - Heuristic that popular/trusted domains are more likely to have important changes.

   ### 6. **Avoiding Unbounded Crawls**

   1. **robots.txt**  
      - Read each site’s `robots.txt` to skip disallowed paths.
      - Adhere to crawl-delay or concurrency guidelines if specified.

   2. **Duplicate / Canonical URLs**  
      - Use canonical URLs if provided (`<link rel="canonical" />`) to avoid infinite loops.
      - Maintain a visited set or a signature-based approach to avoid re-crawling identical pages with different query parameters.

   3. **Limiter**  
      - Possibly keep a maximum depth or maximum pages per domain to avoid recursing endlessly into certain sites.

   ### 7. **Handling JavaScript & Dynamic Content**

   1. **Basic Approach**:  
      - Crawl the static HTML and gather links from it.
      - Many modern sites load content via JavaScript, so the crawler might miss it.

   2. **Headless Browser** (Advanced)  
      - For pages that rely on JS, the crawler can run a headless browser (e.g., Puppeteer) to render the page and extract dynamic links.  
      - Very resource-intensive, so typically done selectively for high-value or known dynamic domains.

   ### 8. **Scalability & Fault Tolerance**

   1. **Distributed Architecture**  
      - Multiple crawler workers, each handling a subset of the Frontier.  
      - Possibly partition by domain or via hashing.

   2. **Data Storage**  
      - Use a distributed file system or NoSQL store for storing fetched pages or link graphs.  
      - Mirror/replicate data across data centers for reliability.

   3. **Queue Persistence**  
      - The frontier must survive worker crashes.  
      - A distributed message queue (Kafka, RabbitMQ) or a specialized DB could store the URL tasks.

   4. **Monitoring & Rate Control**  
      - Track throughput, error rates (DNS failures, HTTP 404, etc.).  
      - Apply backpressure or throttling if certain domains are overloaded.

   ### 9. **Example Crawling Flow**

   1. **Start with Seed URLs** (major websites, domain lists).
   2. **Workers** pop a URL from the frontier:
      1. Check **robots.txt** and domain concurrency constraints.
      2. Fetch the page (HTTP GET).
      3. If status 200, parse HTML for new links, store the content in the data store.
      4. Insert extracted links into the frontier (if not visited or if re-crawl needed).
   3. **Scheduler** updates the priority or sets next re-crawl time for each URL.

   ### 10. **Conclusion**

   A well-designed web crawler must:

   - **Manage a URL Frontier** effectively, controlling fetch order by domain constraints and priority.  
   - **Respect robots.txt** and domain limits to avoid overloading sites.  
   - **Continuously discover** new links while re-crawling high-change pages more frequently.  
   - **Store and index** fetched pages in a scalable data pipeline.  
   - **Distribute** the workload among many crawler workers with robust fault tolerance and monitoring.

   ---

   ## 37. Design a Notification System (Push Notifications)
   Design a Notification System that monitors the number of requests a service receives and blocking excess calls when the limit is exceeded.

   Things to analyze and discuss:
   Facilitate real-time user engagement through timely notifications.
   Implement user registration with customizable preferences.
   Define events triggering notifications from external systems.
   Generate dynamic and personalized notification content.
   Integrate with push notification services for cross-platform delivery.
   Optimize delivery through batch processing and user feedback.
   Prioritize secure communication and token management for data protection.
   Implement throttling mechanisms to control notification volume.
   Ensure scalability with horizontal scaling and redundancy.
   Utilize logging and auditing for monitoring, troubleshooting, and performance optimization.

   ### 1. **Core Requirements**

   1. **User Registration & Preferences**  
      - Users can sign up and select the types of notifications they wish to receive (email, push, SMS, in-app).  
      - Each user has tokens/identifiers for multiple platforms (Android, iOS, web, etc.).  
      - Provide a way to opt-in or opt-out of specific categories (promotions, alerts, reminders).

   2. **Events & Triggers**  
      - External or internal events cause notifications to be generated (e.g., user X likes your post, a system limit is reached, daily summary, etc.).  
      - The system receives these events, transforms them into user-targeted notifications.

   3. **Dynamic & Personalized Content**  
      - Notifications can contain custom messages, user names, or relevant context.  
      - Possibly involve templates or real-time data from external systems.

   4. **Delivery & Throttling**  
      - Integrate with third-party push services (APNs for iOS, FCM for Android, web push APIs) or SMS/email gateways.  
      - Enforce rate limits, blocking or delaying excessive notifications.  
      - Batch or queue messages for efficient delivery when load is high.

   5. **Security & Token Management**  
      - Securely store and manage push tokens or device IDs.  
      - Use HTTPS/TLS, token-based authentication for communication with push services.

   6. **Scalability & Redundancy**  
      - Handle spikes of notifications (e.g., an event triggers notifications to millions of users).  
      - Horizontal scaling of microservices, load balancing, and replication.

   7. **Logging & Auditing**  
      - Keep records of notifications sent, user preferences, successes/failures.  
      - Provide metrics and monitoring for performance optimization and debugging.

   ### 2. **High-Level Architecture**

   ```
            ┌───────────────────────────┐
            │ External / Internal       │
            │ Event Sources             │
            └───────────────────────────┘
                    |  (events)
                    v
      ┌───────────────────────────────────────────┐
      │  Notification Gateway / Ingestion Layer  │
      └───────────────────────────────────────────┘
                    |
                    v
      ┌───────────────────────────────────────────┐
      │ Notification Service (Event Processor)   │
      │  - User Pref Lookup                      │
      │  - Content Personalization / Templating  │
      │  - Throttling / Rate-Limit Checks        │
      │  - Dispatch to Delivery channels         │
      └───────────────────────────────────────────┘
           |               |               |
           v               v               v
   ┌────────────────┐  ┌────────────────┐   ┌─────────────────────────┐
   │ DB for User     │  │Cache/Queue     │   │ Delivery Services (Push,│
   │ Prefs & Tokens  │  │(Redis, Kafka)  │   │ Email, SMS, etc.)       │
   └────────────────┘  └────────────────┘   └─────────────────────────┘
   ```

   1. **External/Internal Event Sources**  
      - Could be microservices or external systems that generate events.  
      - E.g., “User commented on your post,” “System threshold exceeded,” “Daily summary time.”

   2. **Notification Gateway / Ingestion Layer**  
      - A lightweight endpoint receiving event payloads.  
      - Validates and forwards them to the **Notification Service**.

   3. **Notification Service** (Core)  
      - **Event Processor**: Interprets events, determines recipients, builds message content.  
      - **User Preferences & Rate-Limiting**: Checks if the user wants this notification type, and if not over their personal or global limit.  
      - **Content Personalization**: Possibly merges template with user data.  
      - **Dispatch**: Sends the final message to the appropriate delivery channel (push, email, SMS).

   4. **Data Stores**  
      - **User Database**: Stores user info, notification preferences, device tokens.  
      - **Cache/Queue**: For high-volume or short-lived data. Also used to queue notifications if the system experiences surges.  
      - **Logging/Audit DB**: Optionally store notification history.

   5. **Delivery Services**  
      - Could be integrated or separate microservices for push (FCM, APNs), email (via SMTP or API), SMS (Twilio, etc.).  
      - Each channel has distinct APIs, rate limits, and reliability considerations.

   ### 3. **Real-Time User Engagement**

   1. **Immediate Delivery**  
      - For critical or time-sensitive notifications, the system attempts near real-time push (e.g., push notification on a mobile device).
   2. **In-App Messaging**  
      - If the user is currently using the app, optionally skip the external push and show a local notification or pop-up.

   ### 4. **User Registration & Customizable Preferences**

   1. **User Profile & Settings**  
      - Each user can configure which categories or event types they want to receive (e.g., “marketing,” “alerts,” “social”).  
      - Store device tokens for iOS, Android, or web push.

   2. **Token Lifecycle**  
      - When the user logs in or out, or the token changes, update it in the DB.  
      - Expired tokens must be pruned or updated if push providers return invalid token errors.

   ### 5. **Event Triggers & Notification Generation**

   1. **Event Definition**  
      - Each event has a type (e.g., “NEW_COMMENT”, “SYSTEM_ALERT”), a payload (details about the event), and possibly a priority (low, medium, high).
   2. **Mapping to Recipients**  
      - The service determines which users or segments of users are affected.  
      - Could be a single user (like a direct mention) or a user segment (like all watchers of a certain resource).

   3. **Content Personalization**  
      - Templating system merges placeholders (e.g., “{{username}},” “{{postTitle}}”) with real data from the event.  
      - Possibly localized for different languages.

   ### 6. **Integrations with Push Notification Services**

   1. **iOS (APNs)**  
      - Maintain an encrypted connection to Apple servers.  
      - Must handle token-based authentication and error responses.

   2. **Android (FCM)**  
      - HTTP or XMPP-based connection to Google servers.  
      - Provide custom data payloads, handle topic-based or direct device-based messaging.

   3. **Web Push**  
      - Uses the Web Push Protocol and service worker approach.  
      - The server sends requests to the push endpoint (usually hosted by the browser vendor).

   4. **Failure Handling**  
      - If a token is invalid or unsubscribed, remove or update it.  
      - If the push fails temporarily, retry within a certain window.

   ### 7. **Optimization & Throttling**

   1. **Rate Limiting**  
      - **Global**: A system-wide limit on the number of notifications per second.  
      - **User-Level**: Each user can only receive X notifications in a certain time.  
      - If the limit is exceeded, either drop low-priority notifications or queue them for later.

   2. **Batching**  
      - Combine multiple notifications into a single push if they occur within a short interval.  
      - E.g., a daily digest email or a single push with multiple updates.

   3. **User Feedback**  
      - Track open rates, click-through rates, unsubscribes.  
      - Adjust frequency or content if user engagement is low or negative.

   ### 8. **Security & Token Management**

   1. **TLS/HTTPS**  
      - All communication from the server to the push gateway or user device is encrypted.

   2. **Auth Tokens**  
      - Store push credentials (APNs/FCM keys) securely in an encrypted config or secrets manager.  
      - For user-level tokens, carefully handle them in DB, never log them in plain text.

   3. **Permission Model**  
      - Only generate notifications for which the user has given permission.  
      - Provide a user interface for unsubscribing or adjusting settings.

   ### 9. **Scalability & Redundancy**

   1. **Horizontal Scaling**  
      - Multiple instances of the Notification Service behind a load balancer.  
      - Use a distributed queue (e.g., Kafka, RabbitMQ) to handle bursts and ensure reliability.

   2. **Failover**  
      - If one region or node fails, the system reroutes to another.  
      - Keep track of which notifications are pending or in progress in a persistent queue.

   3. **Observability**  
      - Logging of each request, success/failure to the push provider.  
      - Metrics: throughput, latency, error rates, user unsubscribes.

   ### 10. **Logging, Auditing & Performance Optimization**

   1. **Logging**  
      - Store notification events (time, user, channel, status).  
      - Useful for audits (e.g., “Did user X get this critical alert?”).

   2. **Monitoring**  
      - Real-time dashboards for deliveries per second, error rates.  
      - Alert on high failure rates from any push provider.

   3. **Performance Tuning**  
      - Caching user preferences in memory or Redis to reduce DB lookups.  
      - Minimizing overhead in templating or personalization logic.

   ### 11. **Example Workflow**

   1. **Event Received**: External system calls `/notify` with `{"type": "NEW_COMMENT", "userId": 123, "comment": "..."}`.  
   2. **Notification Service** looks up user 123’s preferences.  
   3. The content is rendered into a push message if the user has push enabled.  
   4. **Rate Limit Check**: Ensure user 123 not over their daily limit or global system not overloaded.  
      - If within limit, proceed; otherwise queue or discard.  
   5. **Send** via FCM/APNs with user 123’s device token.  
   6. **Response** from push provider is parsed. If success, log it; if token is invalid, remove or update the token.  
   7. Optionally, user’s device shows the push notification.

   ### 12. **Conclusion**

   A robust **Notification System** must:

   - Provide flexible **user preferences** for different notification types.  
   - Integrate easily with **event sources** (e.g., microservices or external triggers) to create timely notifications.  
   - Use **throttling and rate limiting** to manage volumes while preserving user experience.  
   - Support **multiple channels** (mobile push, email, SMS, web push) with secure token management.  
   - Scale horizontally, using **queues** or **pub/sub** for reliable, asynchronous delivery.  
   - Offer comprehensive **logging** and **monitoring** to track performance and user feedback.  

   By combining these design elements—**event ingestion**, **personalized content assembly**, **rate limiting**, and **push gateway integrations**—the system can reliably deliver notifications at scale, ensuring user engagement without overwhelming them.
