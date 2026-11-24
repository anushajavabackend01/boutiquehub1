
# BoutiqueHub - Database Design

## 1. Introduction

This document describes the database structure for the BoutiqueHub application.

BoutiqueHub follows a database-per-service approach. Each microservice owns and manages its database. Other services cannot directly access or modify that database.

### Database technology

- Database: PostgreSQL
- Primary key strategy: UUID
- Monetary values: NUMERIC / DECIMAL
- Date and time: TIMESTAMP WITH TIME ZONE
- Schema management: Flyway

## 2. Database Architecture

BoutiqueHub uses three separate databases.

| Microservice | Database | Responsibility |
|---|---|---|
| Auth Service | boutiquehub_auth_db | Users, authentication and roles |
| Tailor Service | boutiquehub_tailor_db | Boutiques, categories and tailoring services |
| Order Service | boutiquehub_order_db | Stitching requests, measurements, quotations and orders |

Each service is responsible for its own database schema and transactions.

## 3. Auth Service Database

Database name: `boutiquehub_auth_db`

The Auth Service manages user registration, authentication, password security and role-based access.

### 3.1 users

Stores customer, tailor and administrator account information.

| Column | Data type | Constraints | Description |
|---|---|---|---|
| id | UUID | Primary Key | Unique user identifier |
| full_name | VARCHAR(100) | NOT NULL | User's full name |
| email | VARCHAR(150) | UNIQUE, NOT NULL | Login email |
| password_hash | VARCHAR(255) | NOT NULL | Encrypted password hash |
| phone_number | VARCHAR(20) | | Contact number |
| role | VARCHAR(20) | NOT NULL | CUSTOMER, TAILOR or ADMIN |
| account_status | VARCHAR(20) | NOT NULL | ACTIVE or LOCKED |
| created_at | TIMESTAMPTZ | NOT NULL | Account creation time |
| updated_at | TIMESTAMPTZ | NOT NULL | Last update time |

Business rules:

- Email addresses must be unique.
- Email addresses should be normalized before storing.
- Passwords must be stored as secure hashes, never as plain text.
- Public registration supports CUSTOMER and TAILOR roles.
- ADMIN accounts must be provisioned through a secure administrative process.
- Role and account status values must be validated.

## 4. Tailor Service Database

Database name: `boutiquehub_tailor_db`

The Tailor Service manages boutique profiles, approval and tailoring services.

### 4.1 boutiques

Stores boutique information registered by tailors.

| Column | Data type | Constraints | Description |
|---|---|---|---|
| id | UUID | Primary Key | Boutique identifier |
| tailor_user_id | UUID | UNIQUE, NOT NULL | User ID from Auth Service |
| boutique_name | VARCHAR(150) | NOT NULL | Boutique name |
| description | TEXT | | Boutique description |
| address | VARCHAR(255) | NOT NULL | Street address |
| city | VARCHAR(100) | NOT NULL | City |
| state | VARCHAR(100) | NOT NULL | State |
| pincode | VARCHAR(10) | NOT NULL | Postal code |
| phone_number | VARCHAR(20) | | Contact number |
| approval_status | VARCHAR(20) | NOT NULL | PENDING, APPROVED or REJECTED |
| approved_by | UUID | | Admin user ID from Auth Service |
| approved_at | TIMESTAMPTZ | | Approval time |
| created_at | TIMESTAMPTZ | NOT NULL | Creation time |
| updated_at | TIMESTAMPTZ | NOT NULL | Last update time |

Business rules:

- A tailor can register only one boutique in Version 1.
- New boutiques start with PENDING approval status.
- Only approved boutiques are visible in public searches.
- Only administrators can approve or reject boutiques.
- Only the boutique owner can update their boutique profile.
- The tailor must be authenticated before creating a boutique.

Note: `tailor_user_id` and `approved_by` refer to users in Auth Service. They are external service identifiers, not database foreign keys.

### 4.2 service_categories

Stores the categories of tailoring services.

| Column | Data type | Constraints | Description |
|---|---|---|---|
| id | UUID | Primary Key | Category identifier |
| category_name | VARCHAR(100) | UNIQUE, NOT NULL | Category name |
| description | TEXT | | Category description |
| active | BOOLEAN | NOT NULL | Whether category is active |
| created_at | TIMESTAMPTZ | NOT NULL | Creation time |

Example categories:

- Blouse Stitching
- Kurti Stitching
- Chudidar Stitching
- Lehenga Stitching
- Saree Blouse Alterations
- Dress Alterations

### 4.3 tailor_services

Stores services and pricing offered by each boutique.

| Column | Data type | Constraints | Description |
|---|---|---|---|
| id | UUID | Primary Key | Service identifier |
| boutique_id | UUID | Foreign Key, NOT NULL | Related boutique |
| category_id | UUID | Foreign Key, NOT NULL | Related category |
| service_name | VARCHAR(150) | NOT NULL | Service name |
| description | TEXT | | Service details |
| price | NUMERIC(10,2) | NOT NULL | Service price |
| estimated_days | INTEGER | NOT NULL | Estimated completion days |
| active | BOOLEAN | NOT NULL | Service availability |
| created_at | TIMESTAMPTZ | NOT NULL | Creation time |
| updated_at | TIMESTAMPTZ | NOT NULL | Last update time |

Business rules:

- Each service belongs to one boutique.
- Each service belongs to one category.
- Price cannot be negative.
- Estimated completion days must be greater than zero.
- Only approved boutique owners can publish or manage services.

## 5. Order Service Database

Database name: `boutiquehub_order_db`

The Order Service manages stitching requests, measurements, quotations, orders and order status history.

### 5.1 stitching_requests

Stores stitching requests submitted by customers.

| Column | Data type | Constraints | Description |
|---|---|---|---|
| id | UUID | Primary Key | Request identifier |
| customer_user_id | UUID | NOT NULL | Customer ID from Auth Service |
| boutique_id | UUID | NOT NULL | Boutique ID from Tailor Service |
| tailor_user_id | UUID | NOT NULL | Tailor ID from Auth Service |
| tailor_service_id | UUID | NOT NULL | Service ID from Tailor Service |
| request_description | TEXT | NOT NULL | Customer's requirements |
| design_reference_url | VARCHAR(500) | | Optional design reference |
| preferred_completion_date | DATE | NOT NULL | Requested completion date |
| request_status | VARCHAR(30) | NOT NULL | Current request status |
| created_at | TIMESTAMPTZ | NOT NULL | Request creation time |
| updated_at | TIMESTAMPTZ | NOT NULL | Last update time |

Initial request status examples:

- PENDING_QUOTE
- QUOTATION_SENT
- QUOTATION_REJECTED
- ORDER_CREATED
- CANCELLED

Business rules:

- Customer identity must come from the authenticated user's JWT.
- The selected boutique and service must be valid and available.
- The service must belong to the selected boutique.
- The preferred completion date must be validated.
- Only the assigned tailor can respond to the request.

### 5.2 request_measurements

Stores measurements associated with a stitching request.

| Column | Data type | Constraints | Description |
|---|---|---|---|
| id | UUID | Primary Key | Measurement identifier |
| request_id | UUID | Foreign Key, NOT NULL | Related stitching request |
| measurement_name | VARCHAR(100) | NOT NULL | Measurement name |
| measurement_value | NUMERIC(8,2) | NOT NULL | Measurement value |
| measurement_unit | VARCHAR(10) | NOT NULL | INCH or CM |

Example measurements:

- Chest
- Waist
- Shoulder
- Sleeve Length
- Dress Length

Business rules:

- Each measurement belongs to one stitching request.
- Measurement values must be positive.
- Measurements must be accessible only to authorized users involved in the request.

### 5.3 quotations

Stores quotations submitted by tailors.

| Column | Data type | Constraints | Description |
|---|---|---|---|
| id | UUID | Primary Key | Quotation identifier |
| request_id | UUID | Foreign Key, NOT NULL | Related stitching request |
| quoted_price | NUMERIC(10,2) | NOT NULL | Quoted amount |
| estimated_days | INTEGER | NOT NULL | Estimated completion days |
| tailor_comments | TEXT | | Additional information |
| quotation_status | VARCHAR(20) | NOT NULL | SENT, ACCEPTED, REJECTED or EXPIRED |
| valid_until | TIMESTAMPTZ | | Quotation expiry time |
| created_at | TIMESTAMPTZ | NOT NULL | Quotation creation time |

Business rules:

- Only the assigned tailor can submit a quotation.
- Quoted price cannot be negative.
- Estimated days must be greater than zero.
- A request can have quotation history, but only one active SENT quotation at a time.
- A customer can accept or reject the active quotation.

### 5.4 orders

Stores confirmed stitching orders created after quotation acceptance.

| Column | Data type | Constraints | Description |
|---|---|---|---|
| id | UUID | Primary Key | Order identifier |
| request_id | UUID | UNIQUE, Foreign Key | Related stitching request |
| quotation_id | UUID | UNIQUE, Foreign Key | Accepted quotation |
| customer_user_id | UUID | NOT NULL | Customer ID from Auth Service |
| boutique_id | UUID | NOT NULL | Boutique ID from Tailor Service |
| tailor_user_id | UUID | NOT NULL | Tailor ID from Auth Service |
| final_price | NUMERIC(10,2) | NOT NULL | Confirmed order price |
| order_status | VARCHAR(30) | NOT NULL | Current order status |
| confirmed_at | TIMESTAMPTZ | NOT NULL | Order confirmation time |
| completed_at | TIMESTAMPTZ | | Completion time |
| created_at | TIMESTAMPTZ | NOT NULL | Order creation time |

Initial order status examples:

- CONFIRMED
- IN_PROGRESS
- READY_FOR_DELIVERY
- COMPLETED
- CANCELLED

Business rules:

- An order is created only after the customer accepts a valid quotation.
- One stitching request can create only one order.
- One quotation can be used to create only one order.
- The final price is captured at order creation.
- Order creation and quotation acceptance must be handled in a single database transaction.

### 5.5 order_status_history

Maintains a history of order status changes.

| Column | Data type | Constraints | Description |
|---|---|---|---|
| id | UUID | Primary Key | History identifier |
| order_id | UUID | Foreign Key, NOT NULL | Related order |
| previous_status | VARCHAR(30) | | Previous order status |
| new_status | VARCHAR(30) | NOT NULL | Updated order status |
| updated_by | UUID | NOT NULL | User ID from Auth Service |
| comments | TEXT | | Optional update comments |
| changed_at | TIMESTAMPTZ | NOT NULL | Status change time |

Business rules:

- Every order status change must be recorded.
- The user making the change must be authorized.
- Valid status transitions must be checked by the Order Service.
- History records should not be modified during normal application operations.

## 6. Database Relationships

### Auth Service

- One user can own one boutique in Version 1.
- User information is maintained only in Auth Service.

### Tailor Service

- One boutique can offer multiple tailoring services.
- One category can be associated with multiple tailoring services.
- Each tailoring service belongs to one boutique and one category.

### Order Service

- One stitching request can contain multiple measurements.
- One stitching request can have quotation history.
- One accepted quotation can create one order.
- One order can have multiple status history records.

## 7. Cross-Service Relationships

BoutiqueHub uses UUID references to connect records across services.

Examples:

- `tailor_user_id` in Tailor Service refers to a user in Auth Service.
- `customer_user_id` in Order Service refers to a user in Auth Service.
- `boutique_id` in Order Service refers to a boutique in Tailor Service.
- `tailor_service_id` in Order Service refers to a service in Tailor Service.

These cross-service references do not use database foreign keys.

The owning service validates external references through REST API communication when required.

## 8. Indexing Strategy

The following indexes should be considered for frequently accessed records:

### Auth Service

- Unique index on normalized email.

### Tailor Service

- Unique index on tailor_user_id.
- Index on boutique approval_status.
- Index on boutique city.
- Index on tailor_services boutique_id.
- Index on tailor_services category_id.

### Order Service

- Index on stitching_requests customer_user_id.
- Index on stitching_requests tailor_user_id.
- Index on stitching_requests request_status.
- Index on stitching_requests boutique_id.
- Index on quotations request_id.
- Index on orders customer_user_id.
- Index on orders tailor_user_id.
- Index on order_status_history order_id.

Indexes will be finalized based on actual query patterns and performance testing.

## 9. Data Integrity and Security

- Use UUID values as primary keys.
- Use appropriate foreign keys for relationships within the same database.
- Do not create cross-service database foreign keys.
- Use NOT NULL, UNIQUE and CHECK constraints wherever appropriate.
- Store passwords as secure hashes.
- Never expose password hashes through APIs.
- Protect customer measurements and personal information.
- Validate all user input in the application layer.
- Store monetary amounts using NUMERIC rather than floating-point types.
- Store timestamps consistently in UTC.
- Apply role-based access control to sensitive data.

## 10. Database Migration Strategy

Flyway will manage database schema changes.

- Each microservice maintains its own migration scripts.
- Migration scripts are version controlled.
- Existing migration scripts should not be edited after they have been applied to a shared environment.
- New schema changes should be introduced through new migration versions.
- Database migrations will be executed as part of service deployment.

## 11. Future Enhancements

The following improvements may be considered after the core application is completed:

- Refresh token and session management tables.
- Audit logging for sensitive account and boutique changes.
- Soft deletion or archival strategies.
- Additional quotation revision and expiry workflows.
- Database backup and recovery automation.
- Advanced search and filtering indexes.

These enhancements are not required for the initial database implementation.

## 12. Conclusion

The BoutiqueHub database design separates authentication, boutique management and order processing into independently owned databases.

This approach supports service independence, data ownership, maintainability and future scalability.
