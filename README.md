# OpenTicket
OpenTicket is an initiative to conform to a ticket structure for communicating network operational usecases between vendors and customers.

Ticket types include:
1. Return Merchandise Authorization (RMA) between network device hardware vendor and customer.
2. Fiber Maintenance Notification between fiber provider and customer.

## Motivation
Network operational communication between customers (Eg. Google, Microsoft) and device/fiber vendors (Eg. Cisco, Ciena, CenturyLink) happens over email or through independent APIs on each end. Conforming to a standard structure for this communication enables:
1. (If ingesting point is email) Automation hooks can be reliably placed on receipt of these notifications
2. (If ingesting point is an API) Single API for multiple endpoints.
3. Scaling automation solutions on each endpoint independently with adaptors.

## Learning based development
We would evolve the structure with time based on operational use cases for each type of ticket communication.

## Current contirbutors
GOOGL, MSFT
