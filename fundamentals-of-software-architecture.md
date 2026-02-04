# Summary: Fundamentals of Software Architecture
*By Mark Richards & Neal Ford*

## The 4 Dimensions of Software Architecture
What do architects actually do? The book defines the role through 4 major dimensions:

1.  **Architecture Characteristics**
2.  **Architecture Decisions**
3.  **Structure**
4.  **Design Principles**

---

## 1. Architecture Characteristics
Architecture Characteristics (often called "non-functional requirements" or "quality attributes") define the success criteria of the system. They are generally orthogonal to the domain functionality but are critical for the system to work properly.

### Definitions
* **Implicit vs. Explicit Characteristics**
    * *Implicit:* Required for the system to function but rarely stated in requirements docs (e.g., Availability, Reliability, Security).
    * *Explicit:* Stated directly in requirements documents (e.g., "System must handle 10,000 concurrent users").

### The Lists of Characteristics
*(Note: These are the standard definitions used in the book)*

#### Operational Characteristics
* **Availability:** System uptime; how long the system is available for use.
* **Continuity:** Disaster recovery capability; ability to recover functionality after a catastrophic failure.
* **Performance:** Stress testing, response times, and capacity analysis.
* **Recoverability:** How fast the system can come back online after a failure (often measured in RTO/RPO).
* **Reliability/Safety:** Ability to fail safely without causing harm or data loss.
* **Robustness:** Ability to handle error and boundary conditions (e.g., internet outage, bad data) gracefully.
* **Scalability:** Ability of the system to operate as user load or requests increase.
* **Elasticity:** (Distinct from Scalability) The ability to auto-scale resources *down* as well as up to optimize cost during variable bursts.

#### Structural Characteristics
* **Configurability:** Ability to change software behavior via configuration rather than code changes.
* **Extensibility:** Ease of plugging in new functionality.
* **Installability:** Ease of installation on target platforms.
* **Leveragability/Reuse:** Ability to reuse components in other products.
* **Localization:** Support for multiple languages, currencies, and units.
* **Maintainability:** Ease of applying changes and enhancements (linked closely to modularity).
* **Portability:** Ability to run on diverse platforms (OS, hardware).
* **Supportability:** Level of diagnostics (logging, tracing) available to debug errors.
* **Upgradability:** Ease of updating from a previous version to a newer one.

#### Cross-Cutting Characteristics
* **Accessibility:** Usability for users with disabilities (e.g., color blindness, hearing loss).
* **Archivability:** Strategy for moving old data to long-term storage or deletion.
* **Authentication:** Verifying users are who they say they are.
* **Authorization:** Verifying users have access to specific functions.
* **Legal:** Compliance with laws (e.g., GDPR, data sovereignty).
* **Privacy:** Hiding transactions/data from unauthorized internal/external employees.
* **Security:** Encryption, data protection, and network security.
* **Usability:** Ease of learning and using the application.

### Identifying & Measuring Characteristics
* **"Least-Worst Architecture":** Don't try to support *all* characteristics. Every supported characteristic adds cost and complexity. Identify the top 3-5 "must-haves."
* **Architecture Katas:** A method to practice deriving characteristics from requirements.
    * *Inputs:* Description, Users, Requirements, Context.
* **Governing via Fitness Functions:**
    * Automated tests (metrics, unit tests, chaos engineering) that break the build if an architecture characteristic degrades (e.g., a test that fails if page load > 500ms).

---

## 2. Architecture Decisions
Architecture decisions form the **hard rules** (constraints) of the system.

* **Architecture Decision vs. Design Principle:**
    * *Decision:* A rule that *must* be followed. Strict. Checked by compliance (e.g., "The presentation layer must not access the database directly").
    * *Principle:* A guideline or preference (e.g., "Prefer asynchronous communication where possible").
* **Architecture Decision Records (ADR):** The standard artifact for documenting decisions. Should include: Title, Status, Context, Decision, Consequences (Pros/Cons), and Compliance.

### The 8 Core Expectations of an Architect
1.  **Make architecture decisions:** Define the technical constraints.
2.  **Continually analyze the architecture:** Assess vitality of current systems.
3.  **Keep current with latest trends:** Maintain "Technical Breadth".
4.  **Ensure compliance with decisions:** Governance.
5.  **Diverse exposure and experience:** Don't specialize too early.
6.  **Have business domain knowledge:** Understand *what* the system solves.
7.  **Possess interpersonal skills:** Leadership and negotiation.
8.  **Understand and navigate politics:** Every decision will be challenged.

---

## 3. Structure (Modularity & Styles)
This section covers how code is organized (Modularity) and the patterns used to deploy it (Styles).

### Modularity
* **Cohesion:** How related the parts of a module are to one another. (High cohesion is good).
* **Coupling:** How dependent modules are on each other.
    * *Afferent Coupling:* Incoming connections (who depends on me?).
    * *Efferent Coupling:* Outgoing connections (who do I depend on?).
    * *Abstractness & Instability:* Metrics used to calculate the "Distance from the Main Sequence" (balance between rigidity and uselessness).
* **Connascence:** (Important Concept) Two components are connascent if a change in one requires a change in the other.
    * *Static Connascence (weaker/better):* Name, Type, Meaning, Position, Algorithm.
    * *Dynamic Connascence (stronger/worse):* Execution (order matters), Timing (race conditions), Values, Identity.
    * *Rule of Degree:* Convert strong forms (Dynamic) to weaker forms (Static).
    * *Rule of Locality:* Strong connascence is acceptable if the code is very close together (same file).

### Architecture Styles

#### Monolithic Styles
1.  **Layered Architecture:** Organized by technical role (Presentation $\to$ Business $\to$ Persistence $\to$ DB). Good for cost/simplicity; bad for deployability/modularity ("Architecture Sinkhole" anti-pattern).
2.  **Pipeline:** Logic flows through a sequence of filters/pipes. High modularity; low complexity.
3.  **Microkernel (Plug-in):** Core system (minimal logic) + isolated plug-ins. Great for extensibility and distinct customer deployments.

#### Distributed Styles
**The 8 Fallacies of Distributed Computing:**
1. The network is reliable.
2. Latency is zero.
3. Bandwidth is infinite.
4. The network is secure.
5. The network topology never changes.
6. There is only one administrator.
7. Transport cost is zero.
8. The network is homogenous.

**Distributed Patterns:**
1.  **Service-Based Architecture:** Coarse-grained services (e.g., 5-7 services) sharing a single database. Good compromise between Monolith and Microservices.
2.  **Event-Driven Architecture (EDA):**
    * *Broker Topology:* Lightweight message bus; no central coordinator. High scale, hard error handling.
    * *Mediator Topology:* Central coordinator (orchestrator) handles workflow. Better error handling, lower scale.
3.  **Space-Based Architecture:**
    * Designed for high scalability/elasticity.
    * Replaces the central DB bottleneck with in-memory data grids (Processing Units) replicated across the network.
4.  **Microservices:**
    * *Bounded Context:* Each service owns its data and logic (Share-nothing).
    * *Orchestration vs. Choreography:* Ways to coordinate workflows.
    * *Sidecars/Service Mesh:* Offloading operational concerns (logging, mTLS) to a separate process.
    * *Distributed Transactions:* Avoid them (ACID is impossible). Use Sagas (Base State Consistency) instead.

---

## 4. Design Principles
Guidelines for developers that align with the architecture (e.g., "Use loose coupling," "Strategy pattern over If/Else"). These differ from *Decisions* because they allow developer discretion.

---

## 5. Soft Skills (The "People" Side)
* **Leadership (The 4 C's):** Communication, Collaboration, Clarity, Conciseness.
* **Negotiation:**
    * Avoid "Ivory Tower" dictation.
    * Translate technical debt into business terms (Cost & Time).
    * *Divide and Conquer:* Challenge stakeholders—do you need 99.999% availability for *everything*, or just the checkout button?
* **Architecture vs. Coding:**
    * Architects must stay technical ("Breadth" over "Depth").
    * *Trap:* Don't code the "Critical Path" (you become a bottleneck).
    * *Do:* Code POCs (Proof of Concepts), internal tools, and perform code reviews to maintain fitness functions.

### Working with Teams
* **Control Levels:**
    * *Junior/New Team:* Needs tighter constraints/checklists.
    * *Senior/Experienced Team:* Needs looser boundaries; focus on outcomes.
* **Team Dysfunctions to Watch:**
    * *Pluralistic Ignorance:* Everyone agrees publicly but disagrees privately.
    * *Process Loss:* Productivity drops as team size grows (communication overhead).