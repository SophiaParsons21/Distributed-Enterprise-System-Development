# Sprint 2 — Requirements and Model Traceability

## 1. Purpose and traceability method

This record explains how stakeholder goals and dependencies represented by the group's i* Strategic Dependency (SD) and Strategic Rationale (SR) models relate to the Sprint 1 strategic BPMN and the two detailed Sprint 2 operational BPMN models. It is **evidence of a cross-model review**, not proof that the automated workflows have been deployed or tested.

The i* labels below are taken from `SD_MODEL.svg` and `SR_MODEL.svg`. Strategic and operational activity labels follow the group diagrams shared for review. Where multiple artifacts use different wording, the table specifies a **conceptual relationship** rather than claiming identical model elements or verified execution-level links.

## 2. Scope and actor correspondence

| i* actor or element | Relevant operational role | Notes |
|---|---|---|
| `Producer` | Producer Interaction lane | Submits/corrects listing information and updates stock |
| `MarketplacePlatform` / `Marketplace Platform` | Marketplace System lane | Validates data, coordinates verification and controls publication/availability |
| `OrganicCertificationBody` | Organic Certification Service black-box pool (Model 1) | External or simulated certification check |
| `ComplianceOfficer` | Proposed escalation/review responsibility | The current operational Model 1 ends at Verification Pending; no compliance-officer review task was shown |
| `Buyer` | Downstream beneficiary | Relies on published listings and accurate stock; no buyer workflow is within these two operational diagrams |
| `PaymentGateway` | Outside these workflows | Payment belongs to broader enterprise / another use case |

## 3. Cross-model traceability matrix

| ID | i* SD/SR element (exact label as shown) | Sprint 1 strategic BPMN activity | Sprint 2 operational BPMN activity/route | Review finding |
|---|---|---|---|---|
| TR-01 | `Submit Product Listing`; `Batch Listing Details` | `Submit Product Information`; `Maintain Product Catalogue` | Model 1: `Submit Product Information` | **Mapped** — producer submission is explicitly present |
| TR-02 | `Validate Listing Data`; `Data Accuracy` | `Maintain Product Catalogue` | Model 1: `Validate Product Information` → `Product Details Valid?` → `Correct Product Information` on No | **Mapped** — mandatory validation and correction loop |
| TR-03 | `Declare Licence Reference`; `Query Certification Registry`; `Licence Verified` | `Maintain Product Catalogue` | Model 1: `Organic Certification Claimed?` → `Verify Organic Certification` → `Certification Valid?` | **Mapped** — conditional organic verification |
| TR-04 | `RegistryAvailability`; `Refer to Compliance Review`; `Review Flagged Listing` | `Maintain Product Catalogue` | Model 1: verification error boundary event → `Record Pending Verification` → `Notify Producer of Verification Delay` → `Verification Pending` | **Partially mapped** — pending/notification represented; compliance-officer review not shown |
| TR-05 | `Listing Published`; `Index Listing in Catalogue`; `Publish After Validation` | `Maintain Product Catalogue`; downstream `Display Product Catalogue` | Model 1: `Publish Product` → `Product Published` | **Mapped at process level** — actual catalogue indexing must be confirmed in service implementation |
| TR-06 | `Manage Stock Levels`; `Set Price & Quantity`; `Current Stock Levels` | `Update Seasonal Inventory`; `Maintain Product Catalogue` | Model 2: `Submit Inventory Update` → `Validate Inventory Update` → `Save Inventory Changes` | **Mapped** — stock update and validation |
| TR-07 | `No Overselling`; `Current Stock Levels` | `Update Seasonal Inventory`; downstream `Check Product Availability` | Model 2: `Stock Available?` → `Set Product Out of Stock` when No | **Partially mapped** — zero stock handled; atomic sales reservation not within Model 2 |
| TR-08 | `Run Nightly Seasonal Check`; `Manage Stock Levels` | `Update Seasonal Inventory` | Model 2: `Check Seasonal Availability` → `Within Availability Period?` → `Set Product Out of Season` / `Set Product Available` | **Partially mapped** — seasonal check during producer-initiated update; no scheduled trigger shown |
| TR-09 | `Fair Catalogue Visibility`; `Buyer Trust`; `Good Seller Reputation` | `Maintain Product Catalogue`; `Display Product Catalogue` | Model 1 validation/publication and Model 2 status decisions | **Conceptual alignment** — these are qualities/outcomes, not stand-alone executable tasks |
| TR-10 | `Publish Without Checks` | No corresponding approved strategic activity | No such route shown in Model 1 | **Deliberately not implemented** — direct publication without validation conflicts with Case 1 requirement; clarify its negative contribution meaning in the SR model |

## 4. Detailed operational process boundaries

### Operational BPMN 1 — Product Registration, Validation and Publication

- **Trigger:** Registered producer requests a new product listing.
- **Normal outcome:** Validated product is published. Organic claims are checked when asserted.
- **Alternative routes:** Missing/invalid details are corrected; rejected certification details are corrected; service error leads to pending-verification status and producer notification.
- **External communication:** The black-box `Organic Certification Service` receives a verification request and returns a verification response via BPMN message flows.
- **Boundary:** Stock replenishment and seasonal status changes after publication belong to Operational BPMN 2.

### Operational BPMN 2 — Inventory and Seasonal Availability Management

- **Trigger:** Producer requests an inventory or seasonal-availability update.
- **Normal outcome:** Changes are validated and saved; the system sets an appropriate stock/season availability status.
- **Alternative routes:** Invalid updates return to the producer for correction; zero stock sets out-of-stock; positive stock with out-of-season date sets out-of-season; positive stock during the season can set available **only if other publication/verification prerequisites are satisfied**.
- **Boundary:** Current diagram is producer-triggered. Calendar-triggered re-evaluation is not yet shown.






