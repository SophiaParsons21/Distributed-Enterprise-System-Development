# Identification and Scoping of Two Candidate Major Functionalities

## 1. Purpose and case-study basis

The Bristol Regional Food Network is a proposed digital marketplace for 40 small-to-medium food producers serving customers within approximately 20 miles of Bristol city centre. The case study highlights producers' differing technical capabilities and the need for straightforward product listing and frequent seasonal stock updates. It requires product descriptions, prices, availability information, food safety details and accurate customer-facing availability.

The assigned **Use Case 1** starts when a registered producer wants to list a new product. The producer supplies product information, the system validates it, and claimed organic certification is checked using an external or simulated verification service. Successfully validated products become available to customers. Producers can subsequently update quantities and availability periods; zero stock makes a product unavailable, while replenishment may make it available again if it is still in season.

Two **candidate major functionalities** are identified below as a proposed division of this one use case. The division is an analysis decision, not a statement that the module team has already agreed it.

### Alignment with the group's business process analysis

The group's `business_process_analysis.md` identifies six processes within Use Case 1. These are grouped into two proposed end-to-end functionalities, without replacing the separate process analysis:

| Candidate functionality | Business processes in the group analysis |
|---|---|
| **A — Product Registration, Validation and Publication** | P1 Product Submission; P2 Product Validation; P3 Organic Certification Verification; P4 Product Publication |
| **B — Stock and Seasonal Availability Management** | P5 Inventory Management; P6 Seasonal Availability Management |

**Shared stakeholder terminology:** **Registered Producer (Farmer / Supplier)** supplies and updates product information; **Marketplace Platform (Core Services)** validates, stores and manages listings; **Organic Certification Authority (Service / API)** checks claimed organic credentials; and a proposed **Marketplace Quality & Compliance Officer** handles unresolved verification exceptions. The last role and manual review policy come from the group's analysis and must be confirmed, rather than treated as explicit use-case requirements.

## 2. Candidate Functionality A — Product Registration, Validation and Publication

**Business goal:** Enable a registered producer to create a complete, validated product record and make it available to customers when eligibility conditions are met.

| Scope item | Proposal |
|---|---|
| Primary actor | Registered producer |
| Supporting actors/systems | Marketplace Platform (Core Services); Organic Certification Authority (Service / API) when certification is claimed; proposed Marketplace Quality & Compliance Officer for escalated exceptions |
| Trigger | Producer chooses to list a new product |
| Inputs | Name, description, category, price, available quantity, availability period, farm origin; relevant harvest date, allergen information, organic certification and best-before date |
| Core activities | Capture information → validate mandatory fields and business rules → verify claimed organic certification → save product → evaluate current availability → publish/list product |
| Key decisions | Are required fields valid? Has certification been claimed? Is certification verified? Can product be listed as currently available? |
| Outputs | Saved product record and published listing, or an explicit correction/pending-verification outcome |
| Exceptions | Missing or invalid fields; rejected verification; verification-service timeout/unavailability |
| Process end | Product record is published, or producer receives an actionable validation/verification outcome |

### Proposed scope boundaries

**In scope:** Structured product-entry Form; validation of required data, dates, positive price and non-negative stock; conditional certification check; product save and publish; user feedback and safe handling of verification failure.

**Out of scope for this candidate:** Customer checkout, payment, delivery, producer settlement, recipes and food-miles reporting. Editing an existing product's stock and season is primarily Functionality B.

**Design assumptions to confirm:** Which context-dependent fields are mandatory; how certification evidence is represented; and how a failed verification is resolved. The group's analysis proposes placing an unavailable-service check in `PENDING_VERIFICATION`, retrying up to three times and then escalating to a proposed compliance officer. These details need group/module-team agreement; the use-case document does not mandate three retries or manual review.

**Initial zero-stock decision:** The group's business analysis proposes a minimum initial stock of one, while the assessment use case requires products to become unavailable at zero stock without explicitly forbidding zero-stock registration. **Proposed resolution:** permit a valid, non-negative initial quantity, but mark zero-stock products unavailable for orders. Obtain agreement before fixing validation rules.

### Candidate acceptance criteria

- **A1:** Given valid required product details, the system stores the product and returns a successful listing outcome; orderability separately depends on quantity and seasonal eligibility.
- **A2:** Given missing or invalid required data, the producer can correct and resubmit without publishing an invalid listing.
- **A3:** Given an organic-certification claim, the process requests verification from an external or simulated service and uses its result in the decision.
- **A4:** Given a rejected verification or an unavailable verification service, the process follows a defined correction, retry or pending-review route; it does not silently mark an unverified claim as certified organic.
- **A5:** A listing with zero quantity or outside its defined seasonal availability is not shown as available for new orders.

### Candidate Camunda implementation opportunities

Producer data-entry and correction **user tasks/Forms**; data-validation, certification-check and product-save **service tasks**; exclusive **gateways** for valid/invalid information and verification outcomes; **process variables** for product fields, validation results and verification status; an error/timeout path for the verification service. The appropriate retry or manual-review policy must be agreed by the group.

## 3. Candidate Functionality B — Stock and Seasonal Availability Management

**Business goal:** Allow producers to maintain stock and season information for existing products so that customers see accurate availability.

| Scope item | Proposal |
|---|---|
| Primary actor | Registered producer |
| Supporting systems | Marketplace Platform (Core Services), including inventory/product and customer-facing catalogue services |
| Trigger | Producer requests an inventory or availability update; an availability reassessment may also be triggered by a relevant change in date or stock |
| Inputs | Existing product ID, updated quantity, revised availability start/end dates |
| Core activities | Retrieve product → check producer's authority to update it → validate change → save new stock/season → determine effective availability → update catalogue status |
| Key decisions | Does the product exist and belong to the producer? Is quantity valid? Is stock greater than zero? Is the product within the specified season? |
| Outputs | Persisted stock/season and updated available/unavailable status, or correction feedback |
| Exceptions | Invalid quantity or date range; missing/unauthorised product; persistence/service failure; stock at zero; outside-season product |
| Process end | Inventory and customer-facing availability agree, or the update is rejected with an explanation |

### Proposed scope boundaries

**In scope:** Existing-product lookup; producer-initiated quantity and season updates; non-negative stock and logical-date checks; automatic unavailability at zero stock; reactivation after replenishment when still within the seasonal period; clear result feedback.

**Out of scope for this candidate:** Initial registration and organic certification verification (Functionality A), basket checkout, payments and multi-vendor order orchestration (separate Use Case 2). A daily/timer-driven seasonal status review may be added if needed to make availability change automatically when dates pass; this is a design proposal to agree, not an explicit mandated mechanism.

### Candidate acceptance criteria

- **B1:** A registered producer can update the stock quantity and season of their existing product.
- **B2:** Invalid or negative quantities and illogical date ranges are rejected without corrupting existing records.
- **B3:** When stock becomes **zero**, the catalogue marks that product **unavailable for new orders**.
- **B4:** When stock is replenished to a positive quantity **and the product is in season**, the catalogue can mark it available again.
- **B5:** Positive stock alone does not make an out-of-season product available; publication and certification conditions must also remain satisfied.
- **B6:** The recorded update and customer-facing status remain consistent if an update fails.

### Candidate Camunda implementation opportunities

An inventory update **Form/user task**; retrieve-product, validation, save-inventory and update-listing **service tasks**; **gateways** for stock and seasonal checks; **process variables** for product ID, quantity, dates and publication status; exception handling for failed updates. Idempotent updates or safeguards against concurrent changes would improve reliability if implemented.

## 4. Comparison and rationale

| Criterion | A — Product registration | B — Inventory and availability |
|---|---|---|
| Distinct business outcome | A new product becomes a validated listing | An existing product's availability stays accurate |
| Main initiating event | New product submission | Existing product update or reassessment |
| User interaction | Product-entry/correction Form | Stock and seasonal-update Form |
| Automated decisions | Field validation and certification result | Quantity and in-season checks |
| Integration opportunities | Certification verification and product persistence | Inventory persistence and catalogue updates |
| Alternative/error paths | Data errors, invalid certification, service outage | Invalid updates, zero stock, expired season, save failure |
| Relation to other candidate | Produces product records | Operates on records from A |

These candidates are complementary but have different triggers, process boundaries and outputs. Both have sufficient proposed decision points and automation steps for operational BPMN and executable Camunda work **provided** the group demonstrates real persistence, Forms, validation and exception behaviour, rather than simply displaying diagrams. They also align with the case-study need for a producer-friendly workflow and accurate product availability.

**Approval question for module team:** Is dividing Use Case 1 into (A) initial product registration/verification/publication and (B) ongoing inventory/seasonal availability management sufficiently substantial as **two major end-to-end functionalities**, or should their boundaries be adjusted? Detailed implementation should follow the team's agreement.

## 5. Traceability to the existing group product backlog

The group's `Product Backlog.txt` already lists **18 initial requirements**. This scope analysis groups them rather than replacing the teammate's backlog or asserting new team priorities. The item numbers below count the bullets in that file from top to bottom (1–18).

| Scope group | Existing backlog items | How they support the functionality |
|---|---|---|
| **A — Producer product registration** | 1–4 | Login, new listing and product fields |
| **A — Validation and correction** | 5–6 | Required-field validation and error messages |
| **A — Organic verification and exception paths** | 7–10 | Organic declaration, external verification, rejection notice and service failure |
| **A — Publication** | 11–12 | Automatic publication and visibility only after validation |
| **B — Inventory and seasonal updates** | 13–14 | Update quantity and seasonal dates |
| **B — Availability rules** | 15–16 | Unavailability at zero stock and reactivation after replenishment (only if in season) |
| **Shared supporting behaviour** | 17–18 | Status visibility and validation/verification logs |

### Candidate acceptance checks for team review

| Check | Functionality | Illustrative demonstration/test |
|---|---|---|
| A-T1 | A | Submit valid non-organic product; confirm validated product record and permitted publication |
| A-T2 | A | Submit missing/invalid data; verify correction feedback and no invalid publication |
| A-T3 | A | Submit organic claim; show verified, rejected and temporarily unavailable service outcomes |
| B-T1 | B | Change quantity to zero; verify item is unavailable for new orders |
| B-T2 | B | Restock item while in season; verify item becomes available if all other conditions permit |
| B-T3 | B | Change season to exclude current date; verify item remains unavailable despite positive stock |

**Open data-definition issue:** `Product Backlog.txt` describes harvest date, allergens and best-before date as optional, while the group's business analysis treats some as mandatory. The assigned use case says these details are relevant where applicable. Before designing validation, the team should label fields **mandatory**, **conditional** or **optional** by product type, and agree on consistent rules.

## 5A. Proposed shared product status terminology

To avoid the different labels in the business analysis (such as `PENDING_AUDIT`/`AWAITING_VERIFICATION` and `ACTIVE_LISTED`/`ACTIVE & PUBLISHED`), agree on one vocabulary across the scoping document, BPMN models and implementation.

| Proposed status | Meaning |
|---|---|
| `DRAFT` | Producer is preparing product details |
| `SUBMITTED` | Product is awaiting validation |
| `VALIDATED` | Required product checks have passed |
| `PENDING_VERIFICATION` | Claimed organic certification has not yet been resolved |
| `PUBLISHED` | Listing has passed applicable publication checks |
| `OUT_OF_STOCK` | Available quantity is zero; new orders are blocked |
| `OUT_OF_SEASON` | Current date is outside the product's declared availability period |
| `UNLISTED` | Listing has been intentionally withdrawn |

**Important distinction:** Publication status and *orderable availability* are not necessarily the same thing. A product might remain published for information but be unavailable for new orders. The team should decide whether these are represented as one status field or separate `publicationStatus` and `availabilityStatus` fields. This table is a **proposal**, not a requirement explicitly specified by the source documents.

## 6. Dependencies, risks and next actions

- **Agreement:** Obtain module-team approval for the two candidates before detailed implementation, as required by the assessment brief.
- **Strategic alignment:** Coordinate with the teammate producing the strategic BPMN so the two functionalities appear within the enterprise-level product-management process.
- **Shared data:** Agree mandatory/conditional/optional fields, the initial zero-stock rule, and the status vocabulary used by both workflows.
- **Certification failure policy:** Align the proposed retry/manual-review path with the group analysis, but obtain agreement on the number of retries and whether publication is blocked while verification is pending.
- **Availability timing:** Agree how a product becomes unavailable when its season ends without a producer manually editing it.
- **Backlog alignment:** Link work to the existing `Product Backlog.txt` instead of introducing a second competing backlog.
- **Repository evidence:** Commit this analysis using the contributor's own account; collect group feedback and update the document with agreed decisions.

## 7. Conclusion

The proposed functions divide the selected use case into (A) getting a new product accurately validated and published and (B) keeping an existing product's stock and seasonal availability accurate. This offers clear responsibilities, data flows, business decisions and error scenarios for later operational BPMN and Camunda implementation. The split is **provisional** until the group and module team agree its boundaries and adequacy.

## 8. Source basis

1. *Bristol Regional Food Network – Digital Marketplace*, **Case Study – v2**, especially producer requirements and core product-management requirements.
2. *Task 1 – Use Cases*, **Use Case 1: Producer Product Listing and Inventory Management**, pp. 1–2.
3. *UFCFTR-30-3 Distributed and Enterprise Software Development – Task 1 Assessment Brief* (2026–27), especially candidate functionality selection, Sprint 1 expectations and repository requirements.
4. Group draft: `business_process_analysis.md` — six process breakdowns, actors, proposed exception handling and status stages.
5. Group draft: `Product Backlog.txt` — 18 initial backlog items.
