# Functional and Non-Functional Requirements for Use Case 1 (Sprint 1)

## 1. Purpose

This document lists the functional and non-functional requirements for Use Case 1, Producer Product Listing and Inventory Management, in the Bristol Regional Food Network Digital Marketplace. We based it on the case study, the use case and our group documents (`business_process_analysis.md`, `functional_scope.md` and the `Product Backlog`). We will use these requirements for the BPMN models in Sprint 2 and the Camunda implementation in Sprint 3.

**Notes on the tables**

Requirement IDs starting with FR-A belong to Functionality A (product registration, validation and publishing), FR-B to Functionality B (stock and seasonal availability), and FR-S to both. Non-functional requirements start with NFR.

Priorities use MoSCoW, where M is Must, S is Should, C is Could and W is Won't.

---

## 2. Actors

| Actor | Description |
|---|---|
| Registered Producer | A farmer or supplier who is logged in and lists products and updates stock. |
| Customer | Looks at the catalogue and can only see products that are published. |
| Marketplace Platform | The system itself, which does the validation, saving and status changes. |
| Organic Certification Service | An external or simulated service that checks if an organic certificate is valid. |
| Compliance Officer (proposed) | A member of marketplace staff who would check verification cases that can't be resolved automatically. We still need to confirm this role with the module team. |

---

## 3. Functional Requirements

Functional requirements describe what the system needs to do.

### 3.1 Functionality A: Product Registration, Validation and Publication

| ID | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| FR-A01 | Only a logged-in, registered producer should be able to create a product listing. | M | A user who isn't logged in can't access the form. |
| FR-A02 | The system shall have a product form with name, description, category, price, quantity, availability start and end dates, and farm origin. | M | All fields are on the form. |
| FR-A03 | The producer should also be able to enter harvest date, allergens, best-before date and whether the product is organic, with a certificate number if it is. | M | The fields are on the form and get saved. |
| FR-A04 | Category should be chosen from a set list of Vegetables, Dairy, Bakery, Preserves and Seasonal Specialities. | M | Category is a drop-down. |
| FR-A05 | The system shall not accept a product if a mandatory field is empty. | M | The product isn't saved if a mandatory field is blank. |
| FR-A06 | The system shall check basic rules. For example, price must be more than £0, quantity can't be negative, the start date must be before the end date and the best-before date can't be in the past. | M | Invalid values are rejected. |
| FR-A07 | If validation fails, the producer should see an error message and be able to fix it without re-entering everything. | M | The producer can correct the form and resubmit. |
| FR-A08 | If the product is marked as organic, the system shall send the certificate details to the certification service. | M | The check only happens for organic products. |
| FR-A09 | If the certificate is valid, the product is marked as organic verified. | M | The product shows as organic verified. |
| FR-A10 | If the certificate is rejected, the producer is told and can either fix the details or list the product as non-organic. | M | A rejected product is not marked organic. |
| FR-A11 | If the certification service is down, the product should be saved as `PENDING_VERIFICATION` and the request retried later so nothing is lost. | M | The product is saved as pending when the service is down. |
| FR-A12 | If it still fails after a few retries (we suggest 3), a manual review task should go to the Compliance Officer. | S | A user task is created in Camunda. |
| FR-A13 | A product should only be published once validation, and verification if needed, are done. | M | Customers can't see products that aren't published. |
| FR-A14 | The producer should get a confirmation when their product is published. | S | The producer gets a notification. |
| FR-A15 | Customers should be able to see the harvest date, farm origin, allergens, best-before date and organic status on a product. | S | These details show on the product page. |

### 3.2 Functionality B: Stock and Seasonal Availability Management

| ID | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| FR-B01 | A producer should be able to update the stock quantity of their products. | M | The new quantity is saved. |
| FR-B02 | A producer should be able to change the season start and end dates of their products. | M | The new dates are saved. |
| FR-B03 | Producers can only edit their own products. | M | Editing another producer's product is not allowed. |
| FR-B04 | Invalid updates, such as a negative quantity or an end date before the start date, should be rejected and the old values kept. | M | The old values stay the same. |
| FR-B05 | When stock gets to 0, the product should become `OUT_OF_STOCK` and can't be ordered. | M | The product can't be added to the basket. |
| FR-B06 | When stock goes back above 0 and the product is still in season, it should go back to `PUBLISHED`. | M | The product can be ordered again. |
| FR-B07 | If stock is added but the product is out of season, it should stay unavailable as `OUT_OF_SEASON`. | M | The product still can't be ordered. |
| FR-B08 | The system should check season dates once a day and update product statuses automatically. | S | A timer event in Camunda updates the statuses. |
| FR-B09 | A producer could withdraw a product (`UNLISTED`), for example if the crop fails. | C | The product is removed from the catalogue. |
| FR-B10 | Stock should go down when a customer order is confirmed. This links to Use Case 2. | C | Not part of Task 1, so only the interface is defined. |

### 3.3 Shared requirements

| ID | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| FR-S01 | The producer should be able to see the status of each of their products, for example `DRAFT`, `PENDING_VERIFICATION`, `PUBLISHED`, `OUT_OF_STOCK`, `OUT_OF_SEASON` or `UNLISTED`. | S | A status is shown for each product. |
| FR-S02 | The system should keep a log of validation errors, verification results and status changes. | S | The events appear in the log. |

### 3.4 Out of scope

This use case does not cover checkout, the basket or payment, which are part of Use Case 2, or quality incidents, which are Use Case 3. Producer payments, the 5% commission, food miles, and recipes and farm stories are also left out.

---

## 4. Non-Functional Requirements

Non-functional requirements describe how well the system should work. We tried to give each one a target so it can be tested.

| ID | Category | Requirement | Priority | How it will be checked |
|---|---|---|---|---|
| NFR-01 | Usability | A producer without technical skills should be able to add a product in under 5 minutes. | M | One of us times themselves acting as a producer. |
| NFR-02 | Usability | Error messages should be clear and say what needs fixing, for example "Price must be more than £0.00". | M | Check the error messages. |
| NFR-03 | Accessibility | Forms should follow the basic WCAG 2.2 AA guidelines, such as labelled fields, keyboard use and colour contrast. | S | Manual check. |
| NFR-04 | Performance | Validation results should come back within about 2 seconds. | S | Browser or Camunda timing. |
| NFR-05 | Performance | Stock or season changes should show in the catalogue within 5 seconds. | S | Update stock and check the catalogue. |
| NFR-06 | Reliability | If the certification service fails, the process shouldn't crash or lose the product. The call should time out after 10 seconds. | M | Simulate the service being down. |
| NFR-07 | Data integrity | Stock should never go below 0. | M | Test with updates. |
| NFR-08 | Data integrity | A failed update shouldn't partly save the product. | M | Compare the record before and after an error. |
| NFR-09 | Security | Producers have to log in and can only access their own products. | M | Try to access a product as a different producer. |
| NFR-10 | Security | HTTPS should be used and passwords should be hashed. | M | Check the configuration. |
| NFR-11 | Legal | Producer personal data should be handled in line with UK GDPR and the Data Protection Act 2018. | M | Review what data we store. |
| NFR-12 | Legal | Allergen information for the 14 main allergens should be shown on products. | M | Check the product page. |
| NFR-13 | Auditability | Logs should be kept and staff should be able to read them. | S | Look at the Camunda history. |
| NFR-14 | Maintainability | Values like the retry count and timeout should be easy to change and not hard-coded. | S | Change a value in the config. |
| NFR-15 | Interoperability | The certification and product services should be called through REST so they can be reused later in Task 2 and Use Case 2. | M | Documented in the README. |
| NFR-16 | Testing | Only a simulated certification service and test data should be used. | M | Check the repository. |
| NFR-17 | Portability | It should be possible to run the project locally by following the README. | S | Someone else in the group follows the README. |

---

## 5. Open questions

1. Should some optional fields be mandatory for certain categories, such as best-before dates for dairy?
2. Can a product be added with a quantity of 0?
3. How many retries should there be, and is the Compliance Officer role in scope?
4. Do we need the daily season check in FR-B08, or should availability only be checked when a producer edits a product?
