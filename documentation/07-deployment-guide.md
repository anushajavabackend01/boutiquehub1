
# 7. Deployment Guide

## 7.1 Overview

This document describes the development environment,
application startup sequence, deployment approach and
infrastructure requirements for BoutiqueHub.

The application consists of multiple Spring Boot
microservices and a React frontend.

PostgreSQL is used for persistent data storage.

---

## 7.2 Technology Prerequisites

The following software is required for development:

| Technology | Purpose |
|---|---|
| Java 17 | Backend development |
| Spring Boot | Backend framework |
| Maven | Build and dependency management |
| PostgreSQL | Relational database |
| IntelliJ IDEA | Backend development IDE |
| Node.js | Frontend development |
| npm | Frontend package management |
| Git | Version control |
| GitHub | Source code repository |
| Docker | Containerization |
| Postman | API testing |

---

## 7.3 Local Environment Configuration

The application will run locally during development.

### Backend Services

| Service | Port |
|---|---|
| Eureka Discovery Server | 8761 |
| Config Server | 8888 |
| API Gateway | 8080 |
| Auth Service | 8081 |
| Tailor Service | 8082 |
| Order Service | 8083 |

### Frontend

React development server:

http://localhost:5173

### Databases

The following PostgreSQL databases will be created:

- boutiquehub_auth_db
- boutiquehub_tailor_db
- boutiquehub_order_db

Each microservice will connect only to its own database.

---

## 7.4 Application Startup Sequence

The recommended startup sequence is:

1. Start PostgreSQL.
2. Start Eureka Discovery Server.
3. Start Config Server.
4. Start API Gateway.
5. Start Auth Service.
6. Start Tailor Service.
7. Start Order Service.
8. Start React frontend.

Each Spring Boot service must successfully initialize
its configuration, database connection and required
dependencies before being considered ready.

Service health will be verified using Spring Boot
Actuator health endpoints.

---

## 7.5 Backend Build and Execution

Backend services will use Maven.

To build a service:

mvn clean package

To execute a Spring Boot application:

mvn spring-boot:run

For packaged applications:

java -jar application.jar

Each service will be built and executed independently
during the initial development phase.

---

## 7.6 Frontend Execution

The frontend will use React and Vite.

Install frontend dependencies:

npm install

Start the development server:

npm run dev

The frontend will communicate with backend APIs through
the API Gateway.

---

## 7.7 Environment Configuration

Environment-specific configuration must not be hardcoded
into application source code.

Configuration values include:

- Database URLs
- Database usernames
- Database passwords
- JWT signing secrets or keys
- External service URLs
- Application ports

Sensitive values must be supplied through environment
variables or a secure configuration mechanism.

Secrets must never be committed to GitHub.

---

## 7.8 Database Migration

Flyway will manage database schema changes.

Each microservice will maintain its own migration files.

Migration responsibilities:

- Create required database tables.
- Define primary and foreign keys.
- Add indexes and constraints.
- Apply schema changes in a versioned sequence.

Database changes must be reviewed and tested before
being deployed to shared environments.

---

## 7.9 Containerization

Docker will be used to package applications and
support consistent execution across environments.

The planned containerized components are:

- Eureka Discovery Server
- Config Server
- API Gateway
- Auth Service
- Tailor Service
- Order Service
- PostgreSQL databases
- React frontend

Docker Compose will be introduced to coordinate
local application containers.

Containerization will be implemented after the core
services work correctly in the local development
environment.

---

## 7.10 CI/CD Pipeline

GitHub Actions will be used for continuous integration
and deployment automation.

Planned pipeline stages:

1. Checkout source code.
2. Set up Java and Maven.
3. Build backend services.
4. Execute automated tests.
5. Build frontend.
6. Perform required quality checks.
7. Build Docker images.
8. Deploy to the configured environment.

The initial pipeline will focus on build and test
automation. Deployment automation will be introduced
later.

---

## 7.11 Monitoring and Logging

Spring Boot Actuator will provide application health
and operational information.

The planned monitoring stack includes:

- Spring Boot Actuator
- Prometheus
- Grafana

Application logs should include useful information
for troubleshooting without exposing passwords,
access tokens or sensitive customer measurements.

---

## 7.12 Production Deployment

Production deployment will be considered after
successful local development and testing.

The production environment should provide:

- Secure application configuration.
- HTTPS communication.
- Restricted database access.
- Database backup and recovery.
- Centralized application logging.
- Application health monitoring.
- Automated build and deployment pipelines.
- Appropriate access controls.

The cloud hosting platform will be selected during
the deployment phase.

---

## 7.13 Deployment Strategy

The project will follow these implementation stages:

Phase 1:
Local development and individual service execution.

Phase 2:
Integration testing across microservices.

Phase 3:
Docker containerization and Docker Compose.

Phase 4:
CI pipeline using GitHub Actions.

Phase 5:
Cloud deployment and production monitoring.

---

## 7.14 Maintenance

- Keep dependencies updated.
- Review application logs regularly.
- Monitor service health.
- Apply database migrations carefully.
- Maintain automated test coverage.
- Back up important databases.
- Protect application secrets.
