# Distributed-Enterprise-System-Development

Use Case 1: Producer Product Listing and Inventory Management

A registered producer wants to list a new product on the marketplace. The producer
submits the required product information, including product name, description, category,
price, available quantity, availability period, farm origin and any relevant information such
as harvest date, allergen details, organic certification and best-before date.

The system validates the submitted information. If mandatory information is missing or
invalid, the producer is asked to correct the details. If organic certification is claimed, the
certification information should be checked using an external or simulate
d verification
service.

Once all required information has been successfully validated, the product is published
and becomes available to customers. The producer can later update the product quantity
and seasonal availability.

When stock reaches zero, the product should automatically become unavailable for new
orders. If stock is replenished and the product is still within its seasonal availability period,
it can become available again.

The flow should also consider what happens if the external verification service is
unavailable or validation fails.
