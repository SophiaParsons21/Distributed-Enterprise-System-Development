# Sprint 2: Socio-Technical Modelling of the Agro-Marketplace (i* SD and SR Models)

| | |
| :--- | :--- |
| **Module** | UFCFTR-30-3 Distributed and Enterprise Software Development |
| **Author** | Tauhid Adhikari |
| **Student ID** | 24054998 |
| **Sprint** | Sprint 2 |
| **Artefacts** | `strategic_dependency_model_refined.drawio`, `strategic_rationale_model_refined.drawio`, `SD_MODEL.svg`, `SR_MODEL.svg` |

---

## 1. Purpose and Scope

While Sprint 1 established the operational sequence of the agro-marketplace through BPMN process workflows (*what* steps take place when a harvest batch is registered or an order is submitted), Sprint 2 investigates the underlying motivational architecture (*why* those specific procedures exist). Using goal-oriented socio-technical modelling, this phase maps intentional relationships, organizational delegations, and the qualitative design drivers that influence technical implementation in Camunda.

Two complementary models have been developed:

* **Strategic Dependency (SD) Model:** Treats every participant as an opaque black box, focusing strictly on external commitments, resource exchanges, goals, and softgoals circulating between stakeholders.
* **Strategic Rationale (SR) Model:** Expands the internal operational reasoning of the primary system actors—specifically the **Producer** and the **Marketplace Platform**. It decomposes internal tasks, isolates means-ends alternatives, and models how technical design paths support or constrain qualitative non-functional requirements.

The project scope encompasses producer onboarding, batch harvest listing, organic certification validation, catalogue indexing, atomic inventory allocation, and customer order processing. Physical fulfilment logistics and reverse-refund workflows remain outside the boundary of this sprint.

---

## 2. Modelling Approach and Notation

The models strictly adhere to the consolidated **iStar 2.0** standard language specification (Dalpiaz, Franch and Horkoff, 2016), extending the foundational intentional modelling principles introduced by Yu (1995; 1997). The models were designed directly in draw.io to facilitate iterative versioning within the team repository.

| Construct | Shape | Interpretation in System Context |
| :--- | :--- | :--- |
| Actor | Circle | An intentional entity capable of autonomous action. |
| Agent | Circle with top horizontal bar | A concrete, physically instantiated system or organization (e.g. software system, third-party authority). |
| Role | Circle with bottom curved arc | An abstract stakeholder capacity or set of responsibilities (e.g. any registered farmer or customer). |
| Goal | Rounded rectangle | A crisp, binary condition an actor aims to achieve (either fulfilled or unfulfilled). |
| Quality (Softgoal) | Cloud | A non-functional, qualitative criterion lacking a sharp algorithmic boundary, evaluated through satisficing. |
| Task | Hexagon | A prescriptive operational procedure outlining a concrete way of accomplishing an objective. |
| Resource | Rectangle | An informational payload or physical asset required to execute work. |

| Relationship Link | Syntax | Semantic Meaning |
| :--- | :--- | :--- |
| Dependency | Depender → *D* → Dependum → *D* → Dependee | The depender delegates responsibility for a dependum to the dependee. |
| AND-refinement | Line with a perpendicular bar | The parent intentional element is satisfied only if every sub-element is fulfilled. |
| OR-refinement | Directed arrow with solid arrowhead | Each child element represents an independent, alternative means to satisfy the parent. |
| Contribution | Dashed arrow with qualitative tag | Documents positive (*make*, *help*) or negative (*hurt*, *break*) impacts on softgoals. |

To guarantee traceability across documentation, diagrams, and operational code, all dependencies share uniform identifiers (D1 through D15) between the SD and SR representations.

---

## 3. Actor Characterisation

| Actor | iStar 2.0 Type | Role & Boundary Definition | Justification for Classification |
| :--- | :--- | :--- | :--- |
| **Producer** | Role | Farming entity or agricultural cooperative listing surplus harvest. | Modelled as a Role because varied independent agricultural suppliers assume this identical operational position. |
| **Buyer** | Role | Consumer or wholesale client procuring verified fresh produce. | Modelled as a Role to reflect generic procurement behavior rather than a single concrete user identity. |
| **Marketplace Platform** | Agent | Core automated mediation software orchestrating validation, inventory, and sales. | Modelled as an Agent because it represents a single, concrete distributed software system being engineered. |
| **Organic Certification Body** | Agent | External regulatory institution maintaining statutory registers of organic licensing. | Modelled as an Agent as it represents a distinct statutory external body operating an authoritative registry outside platform control. |
| **Payment Gateway** | Agent | External PCI-compliant clearing facility executing card payment transactions. | Modelled as an Agent representing an integrated third-party banking service. |
| **Compliance Officer** | Role | Internal platform operations specialist conducting manual review of flagged submissions. | Modelled as a Role reflecting operational governance duties regardless of which human staff member is on duty. |

---

## 4. Strategic Dependency (SD) Model

### 4.1 System Dependency Register

The complete dependency network comprising 15 strategic relationships is catalogued below:

| ID | Depender | Dependum | Element Type | Dependee | Operational Justification |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **D1** | Marketplace Platform | Batch Listing Details | Resource | Producer | Platform validation engines cannot instantiate a catalogue entry without harvest dates, pricing, volume, and licence identifiers. |
| **D2** | Producer | Listing Published | Goal | Marketplace Platform | The supplier seeks commercial exposure on the live marketplace without concerning themselves with back-end indexing algorithms. |
| **D3** | Producer | Fair Catalogue Visibility | Quality | Marketplace Platform | Producers rely on the platform to present listings objectively without algorithmic bias or unannounced suppression. |
| **D4** | Marketplace Platform | Current Stock Levels | Resource | Producer | Digital stock buffers must faithfully reflect actual agricultural warehouse inventory to prevent over-allocation. |
| **D5** | Buyer | Order Placed | Goal | Marketplace Platform | Customers expect confirmed booking of selected items without handling back-end transaction mechanics. |
| **D6** | Buyer | Product Origin Details | Resource | Marketplace Platform | Traceability data, farm location, and picking timestamps directly drive purchasing decisions. |
| **D7** | Buyer | Trustworthy Organic Claims | Quality | Marketplace Platform | Consumers paying premium rates require genuine assurance that green certification badges are backed by verified evidence. |
| **D8** | Marketplace Platform | Delivery & Payment Details | Resource | Buyer | Checkout processing requires a validated delivery destination and funding instrument. |
| **D9** | Marketplace Platform | Payment Authorised | Goal | Payment Gateway | Financial settlement is delegated to specialized infrastructure; the platform relies purely on a valid authorization token. |
| **D10** | Marketplace Platform | Secure Card Handling | Quality | Payment Gateway | Sensitive cardholder information is kept off platform servers to satisfy PCI-DSS governance by relying on tokenised provider infrastructure. |
| **D11** | Marketplace Platform | Licence Status Record | Resource | Organic Certification Body | Verifiable status evidence (validity dates, scope, licensee identity) must be retrieved to evaluate claims. |
| **D12** | Marketplace Platform | Registry Availability | Quality | Organic Certification Body | Automated synchronous verification depends entirely on continuous uptime of third-party public authority endpoints. |
| **D13** | Marketplace Platform | Review Flagged Listing | Task | Compliance Officer | Ambiguous submissions, edge cases, and registry timeout anomalies require a human investigator to execute standard review protocols. |
| **D14** | Compliance Officer | Audit Case File | Resource | Marketplace Platform | Adjudicators require structured case bundles containing producer inputs, automated logs, and external response codes. |
| **D15** | Producer | Valid Organic Certificate | Resource | Organic Certification Body | Agricultural producers must formally obtain accreditation before lawfully claiming organic provenance. |

**Structural Distribution:** 3 Goals, 1 Task, 7 Resources, and 4 Qualities spanning 6 intentional actors.

---

### 4.2 Vulnerability & Exposure Analysis

In iStar theory, every outgoing dependency constitutes an architectural vulnerability (Yu, 1997). When a dependee fails to honour a commitment, the depender's downstream objectives are compromised.

| Actor | Outgoing Dependencies (Depender) | Incoming Dependencies (Dependee) | Structural Position & Risk Assessment |
| :--- | :---: | :---: | :--- |
| **Marketplace Platform** | 8 | 6 | Central mediator. High vulnerability stemming from reliance on external APIs and human operational fallback. |
| **Producer** | 3 | 2 | Balanced position; reliant on the platform for market access and the certification body for credentials. |
| **Buyer** | 3 | 1 | Primarily a depender; customer retention relies fundamentally on platform integrity (D6, D7). |
| **Organic Certification Body** | 0 | 3 | Autonomous external authority; holds critical leverage over platform listing throughput. |
| **Payment Gateway** | 0 | 2 | Autonomous financial service provider; single point of failure for transactional completion. |
| **Compliance Officer** | 1 | 1 | Internal operational safety valve; low link volume but represents an essential contingency path. |

#### Critical Failure Paths & Engineering Mitigations:
1. **Third-Party Registry Vulnerability (D11, D12):** Network partitions or authority downtime threaten the listing pipeline. Rather than dropping transactions or accepting unvetted claims, an asynchronous review path (D13, D14) acts as a fault-tolerant safety net.
2. **Supplier Data Quality Risks (D1, D4):** Erroneous harvest dates or stale stock counters directly contaminate buyer trust (D6). The platform implements strict ingestion boundaries with automated constraint checks rather than blindly trusting submitted payloads.
3. **Transaction Settlement Atomicity (D9):** Stock reservations cannot be committed until payment capture is confirmed, preventing uncollectable reservations and inventory locking conflicts.

---

## 5. Strategic Rationale (SR) Model

### 5.1 Producer Reasoning Boundary

The root goal of the Producer is **Produce Sold Profitably**. The actor evaluates two top-level means-ends options (OR-refinement):

* **Sell via Marketplace:** Expanded via AND-refinement into *Submit Product Listing* and *Manage Stock Levels*. The submission task requires *Set Price & Quantity*, *Declare Licence Reference*, and *Enter Batch & Harvest Dates*.
* **Sell at Local Market:** Traditional physical sales model retained as a strategic baseline.

| Action / Element | Contribution | Softgoal (Quality) | Strategic Rationale |
| :--- | :---: | :--- | :--- |
| *Sell via Marketplace* | **make** | Wide Customer Reach | Digital distribution surmounts regional travel barriers, accessing regional buyer volume. |
| *Sell at Local Market* | **hurt** | Wide Customer Reach | Restricted to local foot traffic and regional weather conditions. |
| *Sell at Local Market* | **help** | Low Admin Effort | Immediate cash transactions with zero metadata entry or licence validation hurdles. |
| *Submit Product Listing* | **hurt** | Low Admin Effort | Obligates farmers to perform structured digital data entry and batch record tracking. |
| *Manage Stock Levels* | **help** | Good Seller Reputation | Accurate digital allocations eliminate out-of-stock cancellations, boosting feedback scores. |
| *Declare Licence Reference* | **help** | Good Seller Reputation | Legitimate accreditation distinguishes premium producers from conventional suppliers. |

The friction imposed by *Submit Product Listing* on *Low Admin Effort* represents the primary barrier to digital onboarding. To prevent supplier drop-off, the front-end interface must minimize unnecessary input fields and support automated defaults.

---

### 5.2 Marketplace Platform Reasoning Boundary

The platform structures its operations around two parent objectives: **Listing Published** and **Order Processed**.

#### Decomposing "Listing Published"
The platform considers two architectural alternatives:
1. **Publish Without Checks:** Bypasses validation to achieve immediate listing creation. Rejected due to severe downstream risks to data integrity.
2. **Publish After Validation (Selected Strategy):** AND-refined into *Validate Listing Data*, *Licence Verified*, and *Index Listing in Catalogue*.

The sub-goal **Licence Verified** is structured through an inclusive OR-refinement:
* **Query Certification Registry (Primary Fast-Path):** Automated API lookup verifying credential status in real time.
* **Refer to Compliance Review (Contingency Path):** Manual escalation to human officers triggered during API outages or data mismatches.

#### Decomposing "Order Processed"
AND-refined into **Reserve Stock Atomically** (guaranteeing thread-safe inventory reservation prior to billing) and **Take Payment** (delegating card settlement to D9). In addition, a periodic background task, **Run Nightly Seasonal Check**, runs autonomously to cull stale or expired batches.

| Internal Node | Contribution | Softgoal | Justification |
| :--- | :---: | :--- | :--- |
| *Publish Without Checks* | **help** | Fast Producer Onboarding | Delivers instant feedback and eliminates onboarding delays. |
| *Publish Without Checks* | **break** | Data Accuracy | Allows fraudulent, erroneous, or expired listings into the active catalogue. |
| *Publish After Validation* | **hurt** | Fast Producer Onboarding | Introduces latency into the publication pipeline while checks execute. |
| *Validate Listing Data* | **make** | Data Accuracy | Rejects corrupted data (e.g. harvest date > today, price ≤ 0, negative inventory). |
| *Run Nightly Seasonal Check* | **help** | Data Accuracy | Automatically purges expired harvest batches without requiring vendor intervention. |
| *Query Certification Registry* | **hurt** | Fault Tolerance | Couples platform processing directly to third-party server health. |
| *Refer to Compliance Review* | **help** | Fault Tolerance | Provides an operational circuit-breaker that maintains pipeline continuity during external outages. |
| *Reserve Stock Atomically* | **make** | No Overselling | Guarantees transactional consistency during concurrent order submissions for limited stock. |
| *Licence Verified* | **make** | Buyer Trust | Confirms every organic badge is legally certified and auditable. |
| *Data Accuracy* | **help** | Buyer Trust | Reliable product specifications eliminate customer disillusionment upon delivery. |
| *No Overselling* | **help** | Buyer Trust | Prevents post-purchase fulfillment cancellations due to oversold inventory. |

---

### 5.3 Internal Mapping of External Dependencies

The SR diagram binds external boundary dependencies to internal operational nodes:

| ID | Internal Origin (Depender Node) | Internal Target (Dependee Node) |
| :--- | :--- | :--- |
| **D1** | Platform: *Validate Listing Data* | Producer: *Enter Batch & Harvest Dates* |
| **D2** | Producer: *Sell via Marketplace* | Platform: *Listing Published* |
| **D3** | Producer: *Produce Sold Profitably* | Platform: *Index Listing in Catalogue* |
| **D4** | Platform: *Index Listing in Catalogue* | Producer: *Manage Stock Levels* |
| **D5** | Buyer (External Actor) | Platform: *Order Processed* |
| **D7** | Buyer (External Actor) | Platform: *Buyer Trust* |
| **D9, D10** | Platform: *Take Payment* | Payment Gateway (External Actor) |
| **D11** | Platform: *Query Certification Registry* | Organic Certification Body (External Actor) |
| **D13** | Platform: *Refer to Compliance Review* | Compliance Officer (External Actor) |
| **D15** | Producer: *Declare Licence Reference* | Organic Certification Body (External Actor) |

*Note:* D6, D8, D12, and D14 connect to the outer perimeter of the Platform boundary because they represent system-wide infrastructural dependencies rather than isolated operational tasks.

---

### 5.4 Qualitative Evaluation of Architectural Alternatives

Following the qualitative evaluation label propagation procedure (Horkoff and Yu, 2016), both publication candidates were evaluated against key softgoals:

| Evaluated Softgoal | Alternative A: Publish Without Checks | Alternative B: Publish After Validation (Selected) |
| :--- | :--- | :--- |
| **Fast Producer Onboarding** | Partially Satisfied (*help*) | Partially Denied (*hurt*) |
| **Data Accuracy** | Fully Denied (*break*) | Fully Satisfied (*make* + *help*) |
| **Fault Tolerance** | Neutral / Inapplicable | Partially Satisfied (negative impact of registry offset by manual fallback) |
| **Buyer Trust** | Fully Denied (no badge backing, inaccurate data) | Fully Satisfied (compound positive propagation from Trust, Accuracy, and Inventory integrity) |
| **Status of D7 (Trustworthy Organic Claims)** | **Breached (Failed)** | **Satisficed (Achieved)** |

**Architectural Decision:** Alternative B is adopted. Although it introduces minor onboarding latency (*hurt* on Fast Onboarding), it rigorously secures **Data Accuracy** and **Buyer Trust**, which are indispensable for commercial viability and legal compliance. By automating the registry lookup as the primary execution path, onboarding friction is kept to a negligible minimum for standard valid submissions.

---

## 6. Socio-Technical Trade-off Analysis

1. **Onboarding Velocity vs Catalogue Trustworthiness:** Rapid vendor self-service often undermines data integrity. By utilizing real-time automated API validation, the system achieves sub-second verification for the vast majority of legitimate users, reserving slower manual reviews strictly for anomalous edge cases.
2. **System Autonomy vs External Dependency Vulnerability:** Delegating credential verification to the Certification Body (D11, D12) introduces external network coupling. The human compliance fallback mechanism decouples uptime dependency, maintaining high availability without compromising verification rigor.
3. **Producer Administrative Friction vs Buyer Information Completeness:** Detailed batch data demands supplier time (*hurt* on Low Admin Effort), yet directly underpins consumer purchasing confidence (D6, D7). The platform resolves this by enforcing a lean data schema restricted strictly to fields that are either legally required or visible to buyers.

---

## 7. Traceability: i* Models to Camunda BPMN Implementation

The table below demonstrates direct alignment between abstract intentional i* nodes and concrete BPMN / Camunda execution constructs:

| i* Element Reference | Intentional Type | BPMN 2.0 Realisation | Camunda Engine Implementation |
| :--- | :--- | :--- | :--- |
| *Submit Product Listing* | Task (Producer) | User Task within Producer Lane | Form-driven user task capturing batch metadata (D1 fields). |
| *Validate Listing Data* | Task | Service Task followed by Exclusive Gateway | Java Delegate executing validation rules; branches to error end event if invalid. |
| *Query Certification Registry* | Task | Service Task with HTTP Connector | External worker task polling registry endpoint with automatic retry policy. |
| *Refer to Compliance Review* | Task | Error Boundary Event routing to User Task | Asynchronous incident handler instantiating a user task for the compliance group (D13). |
| *Licence Verified* | Goal | Converging Exclusive Gateway | Process variable `licenceValid = true` evaluated before continuing. |
| *Index Listing in Catalogue* | Task | Service Task | Persistence delegate updating database listing status to `ACTIVE`. |
| *Run Nightly Seasonal Check* | Task | Dedicated Process with Timer Start Event | Cron-scheduled batch worker running daily at 00:00 UTC. |
| *Reserve Stock Atomically* | Task | Service Task preceding payment execution | Database transaction executing an atomic conditional update (`UPDATE inventory SET stock = stock - qty WHERE stock >= qty`). |
| *Take Payment* | Task | Service Task with Error Boundary Event | REST connector interacting with Payment Gateway; captures declined responses via boundary events. |
| *No Overselling* | Softgoal | Conditional Sequence Flow on Stock Update | Gateway routing insufficient inventory exceptions to a graceful cancellation path. |

---

## 8. Assumptions and System Limitations

* **Registry Integration:** The Organic Certification Body is assumed to expose a reachable REST/SOAP API for programmatic verification. Should the authority only provide flat-file monthly digests, *Query Certification Registry* would transition to querying a locally indexed mirror, mitigating D12 risk.
* **Scope of Internal Decomposition:** Only the Producer and Marketplace Platform are expanded into internal rationale trees. The Buyer, Payment Gateway, Certification Body, and Compliance Officer remain opaque because their internal architectures do not dictate the software mechanics of this sprint.
* **Evaluation Subjectivity:** Contribution weights (*make*, *help*, *hurt*, *break*) reflect qualitative expert engineering judgment formulated during system requirements elicitation.

---

## 9. Deliverable Artefacts

| Artefact Identifier | Description & Format |
| :--- | :--- |
| `strategic_dependency_model_refined.drawio` | Master editable vector source for the refined Strategic Dependency (SD) model (6 actors, 15 dependencies). |
| `strategic_rationale_model_refined.drawio` | Master editable vector source for the refined Strategic Rationale (SR) model (expanded Producer and Platform boundaries). |
| `SD_MODEL.svg` | Scalable vector graphic export of the SD model, styled with refined corporate color palette and updated student credentials. |
| `SR_MODEL.svg` | Scalable vector graphic export of the SR model, styled with refined corporate color palette and updated student credentials. |

---

## 10. Academic References

Dalpiaz, F., Franch, X. and Horkoff, J. (2016) *iStar 2.0 Language Guide*. arXiv:1605.07767. Available at: https://arxiv.org/abs/1605.07767 (Accessed: 15 November 2026).

Horkoff, J. and Yu, E. (2016) 'Interactive goal model analysis for early requirements engineering', *Requirements Engineering*, 21(1), pp. 29–61. doi:10.1007/s00766-014-0209-8.

Yu, E. (1995) *Modelling Strategic Relationships for Process Reengineering*. PhD thesis. University of Toronto.

Yu, E. (1997) 'Towards modelling and reasoning support for early-phase requirements engineering', in *Proceedings of the Third IEEE International Symposium on Requirements Engineering*. Annapolis, MD: IEEE, pp. 226–235. doi:10.1109/ISRE.1997.566873.

Yu, E., Giorgini, P., Maiden, N. and Mylopoulos, J. (eds.) (2011) *Social Modeling for Requirements Engineering*. Cambridge, MA: MIT Press.
