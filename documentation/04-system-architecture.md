
# BoutiqueHub – System Architecture

## 1. Architecture Overview

BoutiqueHub follows a microservices-based architecture with a React frontend and Spring Boot backend services.

The system is designed to support independent development, deployment, maintenance, and scaling of individual services.

## 2. Frontend Architecture

Technology:
- React.js
- JavaScript
- Vite
- HTML5
- CSS3

The frontend provides separate interfaces for:
- Customers
- Tailors
- Administrators

The frontend communicates with backend services through the API Gateway using REST APIs.

## 3. Backend Microservices

### 3.1 Auth Service

Port: 8081

Responsibilities:
- Customer registration.
- Tailor registration.
- User authentication.
- Password hashing.
- JWT generation and validation support.
- Role management.
- User profile management.

Database: boutiquehub_auth_db

### 3.2 Tailor Service

Port: 8082

Responsibilities:
- Boutique profile management.
- Boutique approval workflow.
- Tailoring category management.
- Tailoring service management.
- Service pricing.
- Approved boutique discovery.

Database: boutiquehub_tailor_db

### 3.3 Order Service

Port: 8083

Responsibilities:
- Stitching request creation.
- Measurement management.
- Quotation management.
- Quotation acceptance and rejection.
- Order creation.
- Order status management.
- Order history.

Database: boutiquehub_order_db

## 4. Infrastructure Services

### 4.1 API Gateway

Port: 8080

Responsibilities:
- Single entry point for frontend requests.
- Request routing.
- Authentication enforcement.
- Centralized cross-cutting concerns.

### 4.2 Discovery Server

Port: 8761

Technology: Netflix Eureka.

Responsibilities:
- Service registration.
- Service discovery.
- Dynamic service lookup.

### 4.3 Config Server

Port: 8888

Responsibilities:
- Centralized external configuration.
- Environment-specific configuration.
- Shared configuration management.

## 5. Database Architecture

The application uses PostgreSQL with database-per-service architecture.

Each microservice owns its database and controls its schema.

Direct cross-service database access is prohibited.

Cross-service communication is performed through REST APIs.

## 6. Service Communication

Initial communication mechanism: Synchronous REST APIs over HTTP.

The frontend communicates through the API Gateway.

Backend services use service discovery and REST communication where required.

Kafka and other asynchronous messaging platforms are excluded from Version 1.

## 7. Security Architecture

- Spring Security for authentication and authorization.
- JWT-based access tokens.
- BCrypt password hashing.
- Role-based access control.
- Backend validation of user identity and permissions.
- Secure configuration of credentials and secrets.

## 8. Technology Stack

- Java 17
- Spring Boot
- Spring Cloud
- Spring Security
- Spring Data JPA
- PostgreSQL
- React.js
- Vite
- Maven
- Git and GitHub
- Docker
- GitHub Actions
- JUnit 5 and Mockito
- Swagger / OpenAPI
- Spring Boot Actuator
- Prometheus and Grafana

## 9. Initial Service Ports

| Component | Port |
|---|---:|
| Discovery Server | 8761 |
| Config Server | 8888 |
| API Gateway | 8080 |
| Auth Service | 8081 |
| Tailor Service | 8082 |
| Order Service | 8083 |

## 10. Architectural Principles

- Single responsibility for each microservice.
- Independent service ownership of data.
- No direct cross-service database access.
- Secure API communication.
- Consistent API response formats.
- Centralized configuration.
- Independent testing and deployment.
- Maintainable and extensible code structure.
