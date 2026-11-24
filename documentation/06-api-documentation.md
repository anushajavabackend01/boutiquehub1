
# 6. API Documentation

## 6.1 Overview

BoutiqueHub exposes REST APIs to communicate between
the frontend and backend microservices.

The frontend communicates with backend services through
the API Gateway.

Base URL:
http://localhost:8080

API Version:
/api/v1

Request and response format:
application/json

Authentication:
JWT Bearer Token

Example Authorization Header:

Authorization: Bearer <access_token>

---

## 6.2 Authentication APIs

Service: Auth Service

Base Path: /api/v1/auth

### 1. Customer Registration

Endpoint:
POST /api/v1/auth/register/customer

Access: Public

Request:
{
"fullName": "Anusha",
"email": "customer@example.com",
"password": "StrongPassword123",
"phoneNumber": "9876543210"
}

Response: 201 Created

{
"success": true,
"message": "Customer registered successfully",
"data": {
"userId": "UUID",
"fullName": "Anusha",
"email": "customer@example.com",
"role": "CUSTOMER",
"accountStatus": "ACTIVE"
}
}

### 2. Tailor Registration

Endpoint:
POST /api/v1/auth/register/tailor

Access: Public

Request:
{
"fullName": "Tailor Name",
"email": "tailor@example.com",
"password": "StrongPassword123",
"phoneNumber": "9876543210"
}

Response: 201 Created

The response contains the registered user details
and the next step: CREATE_BOUTIQUE_PROFILE.

### 3. Login

Endpoint:
POST /api/v1/auth/login

Access: Public

Request:
{
"email": "customer@example.com",
"password": "StrongPassword123"
}

Response: 200 OK

{
"success": true,
"message": "Login successful",
"data": {
"accessToken": "JWT_TOKEN",
"tokenType": "Bearer",
"expiresIn": 3600,
"user": {
"userId": "UUID",
"fullName": "Anusha",
"email": "customer@example.com",
"role": "CUSTOMER"
}
}
}

### 4. Get Current User

Endpoint:
GET /api/v1/auth/me

Access: Authenticated users

Returns the profile of the currently authenticated user.

### 5. Logout

Endpoint:
POST /api/v1/auth/logout

Access: Authenticated users

Invalidates the current session or refresh token
according to the token management implementation.

---

## 6.3 Tailor Service APIs

Service: Tailor Service

Base Path: /api/v1/tailors

### 1. Create Boutique Profile

Endpoint:
POST /api/v1/tailors/boutiques

Access: TAILOR

Request:
{
"boutiqueName": "Elegant Stitches",
"description": "Women's tailoring services",
"address": "Main Road",
"city": "Hyderabad",
"state": "Telangana",
"pincode": "500001",
"phoneNumber": "9876543210"
}

Response: 201 Created

The boutique is created with PENDING approval status.

The tailor_user_id is obtained from the authenticated
JWT identity, not from the request body.

### 2. Get My Boutique

Endpoint:
GET /api/v1/tailors/boutiques/me

Access: TAILOR

Returns the logged-in tailor's boutique profile.

### 3. Update My Boutique

Endpoint:
PUT /api/v1/tailors/boutiques/me

Access: TAILOR

Updates the logged-in tailor's boutique information.

### 4. Browse Approved Boutiques

Endpoint:
GET /api/v1/tailors/boutiques

Access: Public / CUSTOMER

Returns only approved and active boutiques.

Optional query parameters:
- city
- state
- page
- size

### 5. Get Boutique Details

Endpoint:
GET /api/v1/tailors/boutiques/{boutiqueId}

Access: Public / CUSTOMER

Returns boutique details.

### 6. Add Tailoring Service

Endpoint:
POST /api/v1/tailors/boutiques/{boutiqueId}/services

Access: Approved TAILOR owner

Request:
{
"categoryId": "UUID",
"serviceName": "Blouse Stitching",
"description": "Custom blouse stitching",
"price": 750.00,
"estimatedDays": 5
}

Response: 201 Created

### 7. Update Tailoring Service

Endpoint:
PUT /api/v1/tailors/services/{serviceId}

Access: TAILOR owner

Updates service details and pricing.

### 8. Browse Boutique Services

Endpoint:
GET /api/v1/tailors/boutiques/{boutiqueId}/services

Access: Public / CUSTOMER

Returns active services offered by the boutique.

### 9. Update Service Status

Endpoint:
PATCH /api/v1/tailors/services/{serviceId}/status

Access: TAILOR owner

Allows the tailor to activate or deactivate a service.

### 10. Get Pending Boutique Approvals

Endpoint:
GET /api/v1/tailors/admin/boutiques/pending

Access: ADMIN

Returns boutiques awaiting approval.

### 11. Approve or Reject Boutique

Endpoint:
PATCH /api/v1/tailors/admin/boutiques/{boutiqueId}/approval

Access: ADMIN

Request:
{
"approvalStatus": "APPROVED"
}

Allowed values:
- APPROVED
- REJECTED

---

## 6.4 Order Service APIs

Service: Order Service

Base Path: /api/v1/orders

### 1. Create Stitching Request

Endpoint:
POST /api/v1/orders/requests

Access: CUSTOMER

Request:
{
"boutiqueId": "UUID",
"tailorServiceId": "UUID",
"requestDescription": "Need a custom blouse",
"preferredCompletionDate": "2026-12-10",
"measurements": [
{
"measurementName": "Chest",
"measurementValue": 36.00,
"measurementUnit": "INCH"
},
{
"measurementName": "Waist",
"measurementValue": 30.00,
"measurementUnit": "INCH"
}
]
}

Response: 201 Created

{
"success": true,
"message": "Stitching request created successfully",
"data": {
"requestId": "UUID",
"requestStatus": "PENDING_QUOTE"
}
}

The customer_user_id is obtained from the JWT identity.

The Order Service validates boutique and service details
through the Tailor Service API.

### 2. Get My Stitching Requests

Endpoint:
GET /api/v1/orders/requests/my

Access: CUSTOMER

Returns requests created by the authenticated customer.

### 3. Get Incoming Requests

Endpoint:
GET /api/v1/orders/requests/incoming

Access: TAILOR

Returns requests assigned to the authenticated tailor.

### 4. Get Stitching Request Details

Endpoint:
GET /api/v1/orders/requests/{requestId}

Access: Request owner or assigned TAILOR

Returns request details and authorized measurements.

### 5. Submit Quotation

Endpoint:
POST /api/v1/orders/requests/{requestId}/quotation

Access: Assigned TAILOR

Request:
{
"quotedPrice": 850.00,
"estimatedDays": 7,
"tailorComments": "Custom design and stitching included"
}

Response: 201 Created

### 6. Accept or Reject Quotation

Endpoint:
PATCH /api/v1/orders/requests/{requestId}/quotation/decision

Access: Request-owning CUSTOMER

Request:
{
"decision": "ACCEPTED"
}

Allowed values:
- ACCEPTED
- REJECTED

Accepting a quotation creates an order.
The operation must prevent duplicate order creation.

### 7. Cancel Stitching Request

Endpoint:
PATCH /api/v1/orders/requests/{requestId}/cancel

Access: Request-owning CUSTOMER

Cancellation is allowed only when the request is
in an eligible state.

### 8. Get My Orders

Endpoint:
GET /api/v1/orders/my

Access: CUSTOMER

Returns orders belonging to the authenticated customer.

### 9. Get Assigned Orders

Endpoint:
GET /api/v1/orders/assigned

Access: TAILOR

Returns orders assigned to the authenticated tailor.

### 10. Get Order Details

Endpoint:
GET /api/v1/orders/{orderId}

Access: Customer owner or assigned TAILOR

Returns order details and status history.

### 11. Update Order Status

Endpoint:
PATCH /api/v1/orders/{orderId}/status

Access: Assigned TAILOR

Request:
{
"status": "IN_PROGRESS",
"comments": "Stitching work started"
}

Possible statuses will be validated against the
defined order lifecycle.

---

## 6.5 Standard Success Response

{
"success": true,
"message": "Operation completed successfully",
"data": {},
"timestamp": "2026-11-24T12:00:00Z"
}

## 6.6 Standard Error Response

{
"success": false,
"message": "Resource not found",
"errorCode": "RESOURCE_NOT_FOUND",
"timestamp": "2026-11-24T12:00:00Z"
}

## 6.7 HTTP Status Codes

| Status | Meaning |
|---|---|
| 200 | OK |
| 201 | Created |
| 204 | No Content |
| 400 | Bad Request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Not Found |
| 409 | Conflict |
| 422 | Validation Error |
| 500 | Internal Server Error |
| 503 | Service Unavailable |

## 6.8 API Security Rules

- Protected APIs require a valid JWT access token.
- Role-based authorization is enforced.
- Customers can access only their own requests and orders.
- Tailors can access only their assigned requests and orders.
- Tailors can modify only their own boutique and services.
- Only administrators can approve boutiques.
- User IDs and roles are derived from verified JWT claims.
- Request bodies must not be trusted for authenticated
  user identity.
- Sensitive measurements are returned only to authorized
  users.

## 6.9 API Versioning

All Version 1 APIs use the prefix:

/api/v1

Future breaking API changes can be introduced through
a new version such as /api/v2.
