
# BoutiqueHub – Functional Requirements Specification

## 1. Introduction

This document describes the functional requirements of the BoutiqueHub application.

It defines the features, business rules, and expected behaviour of the system for customers, tailors, and administrators.

## 2. User Authentication and Management

### FR-001: Customer Registration
- The system shall allow customers to register using their name, email, phone number, and password.
- Email addresses must be unique.
- Passwords must be securely hashed before storage.
- Newly registered customers shall receive the CUSTOMER role.

### FR-002: Tailor Registration
- The system shall allow tailors to register using their personal details.
- Email addresses must be unique across all users.
- Newly registered tailors shall receive the TAILOR role.
- Tailors must create a boutique profile after registration.

### FR-003: User Login
- Customers, tailors, and administrators shall be able to log in securely.
- The system shall authenticate users using their registered credentials.
- Successful authentication shall return an access token.
- The system shall enforce role-based access control.

### FR-004: User Profile
- Authenticated users shall be able to view their profile.
- Users shall only access and update their own permitted profile information.

## 3. Boutique Management

### FR-005: Boutique Profile Creation
- Registered tailors shall be able to create a boutique profile.
- A boutique profile shall contain boutique name, description, address, city, state, pincode, and contact number.
- Each tailor can own one boutique in Version 1.
- Newly created boutiques shall have PENDING approval status.

### FR-006: Boutique Approval
- Administrators shall be able to view pending boutique applications.
- Administrators shall be able to approve or reject applications.
- Approved boutiques shall become visible to customers.
- Rejected boutiques shall not be publicly visible.
- Tailors shall be able to update rejected applications and resubmit them.

### FR-007: Boutique Discovery
- Customers shall be able to browse approved boutiques.
- Customers shall be able to search boutiques by location.
- Customers shall be able to view boutique details.

## 4. Tailoring Service Management

### FR-008: Service Creation
- Approved tailors shall be able to add tailoring services.
- Each service shall contain a service name, description, category, price, and estimated completion days.

### FR-009: Service Management
- Tailors shall be able to update service information.
- Tailors shall be able to activate or deactivate their services.
- Only active services belonging to approved boutiques shall be visible to customers.

### FR-010: Service Discovery
- Customers shall be able to view available services offered by a boutique.
- Customers shall be able to view service pricing and estimated completion time.

## 5. Stitching Request Management

### FR-011: Create Stitching Request
- Customers shall be able to submit stitching requests to approved boutiques.
- Customers shall select a boutique and an available tailoring service.
- Customers shall provide a request description.
- Customers shall provide the required measurements.
- Customers may provide a design reference image URL.
- Customers may specify a preferred completion date.
- Each new request shall have PENDING_QUOTE status.

### FR-012: View Stitching Requests
- Customers shall be able to view their own requests.
- Tailors shall be able to view requests assigned to their boutiques.
- Users shall not be able to access requests belonging to unauthorized users.

## 6. Quotation Management

### FR-013: Submit Quotation
- Tailors shall be able to submit quotations for eligible stitching requests.
- A quotation shall contain price, estimated completion days, comments, and validity period.
- Customers shall be notified when a quotation is available.

### FR-014: Quotation Decision
- Customers shall be able to accept or reject quotations.
- Accepting a valid quotation shall create an order.
- Rejecting a quotation shall update the request accordingly.
- The system shall prevent duplicate orders for the same stitching request.

## 7. Order Management

### FR-015: Order Creation
- The system shall create an order after successful quotation acceptance.
- The order shall contain customer, boutique, tailor, request, quotation, and final price information.

### FR-016: Order Tracking
- Tailors shall be able to update order progress.
- Customers shall be able to track their order status.

Initial order statuses:

1. CONFIRMED
2. IN_PROGRESS
3. READY_FOR_PICKUP
4. COMPLETED

### FR-017: Order History
- Customers shall be able to view their previous orders.
- Tailors shall be able to view orders assigned to their boutiques.
- The system shall maintain order status history.

## 8. Authorization and Business Rules

- Only authenticated users can access protected operations.
- Customers cannot create requests on behalf of other customers.
- Tailors cannot manage another tailor's boutique or services.
- Only approved boutiques can receive new stitching requests.
- Only the assigned tailor can submit quotations and update order progress.
- Only the customer who created a request can accept or reject its quotation.
- Administrators alone can approve or reject boutiques.
- Public registration must never allow users to assign themselves the ADMIN role.
- Measurements and customer information must only be accessible to authorized users.
- Invalid order status transitions must be rejected.

## 9. Version 1 Exclusions

The following features are outside the initial release:

- Online payment processing.
- Delivery partner integration.
- Real-time chat.
- Mobile applications.
- AI-based design recommendations.
- Advanced analytics.
