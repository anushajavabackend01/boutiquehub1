
# BoutiqueHub – Non-Functional Requirements Specification

## 1. Introduction

This document defines the non-functional requirements of the BoutiqueHub application.

The objective is to ensure that the application is secure, reliable, maintainable, scalable, and suitable for production deployment.

## 2. Security Requirements

### NFR-001: Authentication
- The application shall use Spring Security for authentication.
- JWT shall be used for API access authentication.
- Passwords shall be hashed using BCrypt.
- Plain-text passwords must never be stored.

### NFR-002: Authorization
- The application shall implement role-based access control.
- Supported roles are CUSTOMER, TAILOR, and ADMIN.
- Users shall only access resources they are authorized to access.
- Backend authorization shall be enforced independently of frontend controls.

### NFR-003: Data Protection
- Sensitive customer information and measurements shall only be accessible to authorized users.
- Secrets and database credentials shall not be committed to GitHub.
- Production communication shall use HTTPS.
- Inputs shall be validated to prevent invalid or malicious data.

### NFR-004: Authentication Token Security
- Access tokens shall have a limited lifetime.
- Refresh token handling shall follow secure storage and rotation practices.
- Invalid and expired tokens shall be rejected.

## 3. Performance Requirements

### NFR-005: API Response Time
- Common API operations should target a response time below two seconds under normal expected load.
- Performance shall be measured through testing rather than assumed.
- Database queries shall be optimized where necessary.

### NFR-006: Frontend Performance
- The frontend shall load efficiently.
- API calls shall avoid unnecessary repeated requests.
- Large images shall be optimized before display or upload.

## 4. Scalability Requirements

### NFR-007: Independent Scaling
- Microservices shall be independently deployable.
- Services shall be designed to support independent scaling.
- Stateless API services shall be preferred where practical.

### NFR-008: Database Scalability
- Each microservice shall own its database.
- Database indexing shall be applied to frequently queried columns.
- Database connection pooling shall be configured appropriately.

## 5. Availability and Reliability

### NFR-009: Service Health
- Each backend service shall expose health information through Spring Boot Actuator.
- Service failures shall be identifiable through health monitoring.

### NFR-010: Data Consistency
- Database transactions shall protect related operations.
- Quotation acceptance shall not create duplicate orders.
- Invalid order status transitions shall be rejected.
- Important order status changes shall be recorded in history.

### NFR-011: Error Handling
- The application shall return consistent error responses.
- Appropriate HTTP status codes shall be used.
- Internal exceptions shall not expose passwords, tokens, or sensitive system details.

## 6. Maintainability

### NFR-012: Code Quality
- The application shall follow clean coding practices.
- Each service shall maintain clear separation of responsibilities.
- Business logic shall be separated from controllers and persistence logic.
- Common API response and exception-handling conventions shall be followed.

### NFR-013: Configuration Management
- Environment-specific configuration shall be externalized.
- Development, testing, and production configurations shall be separated.
- Sensitive configuration values shall be stored securely.

## 7. Testing Requirements

### NFR-014: Unit Testing
- JUnit 5 shall be used for unit testing.
- Mockito shall be used for mocking dependencies.
- Important business rules shall have automated tests.

### NFR-015: Integration Testing
- Database integration shall be tested.
- REST API integration shall be verified.
- Service-to-service communication shall be tested.

### NFR-016: API Documentation
- Swagger / OpenAPI shall document REST endpoints.
- Request and response formats shall be documented.

## 8. Monitoring and Logging

### NFR-017: Application Logging
- The application shall maintain structured logs.
- Important business events and application errors shall be logged.
- Passwords, access tokens, and sensitive measurements shall not appear in logs.

### NFR-018: Monitoring
- Spring Boot Actuator shall expose application health and metrics.
- Prometheus shall collect metrics.
- Grafana shall visualize application metrics and dashboards.

## 9. Deployment Requirements

### NFR-019: Containerization
- Backend services shall support Docker containerization.
- Docker Compose shall be used for local multi-service development.

### NFR-020: CI/CD
- GitHub Actions shall automate build and test execution.
- Failed tests shall prevent an unsuccessful build from being treated as successful.
- Deployment workflows shall be introduced after the application is ready.

### NFR-021: Environment Management
- Development, testing, and production environments shall be separated.
- Production secrets shall never be hardcoded.

## 10. Backup and Recovery

### NFR-022: Database Backup
- Database backup procedures shall be documented.
- Recovery procedures shall be tested before production release.

## 11. Browser Compatibility

### NFR-023: Frontend Compatibility
- The application shall support modern versions of Chrome, Edge, and Firefox.
- The user interface shall be responsive across desktop, tablet, and mobile screen sizes.

## 12. Quality Objectives

BoutiqueHub shall aim to provide:

- Secure authentication and authorization.
- Reliable business transactions.
- Consistent API behaviour.
- Maintainable microservices.
- Automated testing.
- Observable application behaviour.
- Repeatable deployment processes.
