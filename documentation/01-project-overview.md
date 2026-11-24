
# BoutiqueHub – Project Overview

## 1. Project Introduction

BoutiqueHub is a full-stack web application designed to connect customers with women's tailoring boutiques.

The platform enables customers to discover approved boutiques, explore tailoring services, submit stitching requests with measurements, receive quotations, and track their orders.

Tailors can register their boutiques, manage services, receive customer requests, provide quotations, and manage stitching orders.

Administrators are responsible for reviewing and approving boutique registrations.

## 2. Business Objectives

- Connect customers with local women's tailoring boutiques.
- Simplify the stitching request and quotation process.
- Provide transparent service pricing.
- Help tailors manage customer requests and orders.
- Maintain platform quality through boutique approval.
- Provide a centralized platform for customers and tailoring businesses.

## 3. User Roles

### 3.1 Customer

- Register and log in.
- Browse approved boutiques.
- Explore available tailoring services.
- View service prices and estimated completion times.
- Submit stitching requests with measurements.
- Upload design reference images.
- Receive and respond to quotations.
- Track stitching order progress.
- View previous orders.

### 3.2 Tailor

- Register as a tailor.
- Create a boutique profile.
- Submit boutique details for admin approval.
- Manage boutique information.
- Add and manage tailoring services.
- Define service prices and estimated completion times.
- View incoming customer requests.
- Submit quotations.
- Update stitching order status.

### 3.3 Administrator

- Log in securely.
- View pending boutique registrations.
- Approve or reject boutique applications.
- Review registered boutiques.
- Manage platform users and boutiques.

## 4. Business Domain

BoutiqueHub focuses exclusively on women's tailoring services.

Initial service categories include:

- Blouse stitching
- Kurti stitching
- Chudidar stitching
- Lehenga stitching
- Saree blouse alterations
- Dress alterations

## 5. Technology Stack

### Backend
- Java 17
- Spring Boot
- Spring Security
- Spring Data JPA
- Spring Cloud
- REST APIs
- Maven

### Frontend
- React.js
- JavaScript
- Vite
- HTML5
- CSS3

### Database
- PostgreSQL
- Hibernate ORM

### Development and Engineering
- Git
- GitHub
- JUnit 5
- Mockito
- Swagger / OpenAPI
- Docker
- GitHub Actions
- Spring Boot Actuator
- Prometheus
- Grafana

## 6. High-Level Architecture

The application follows a microservices architecture.

Initial services:

1. Auth Service
2. Tailor Service
3. Order Service
4. API Gateway
5. Discovery Server
6. Config Server

The frontend communicates with backend services through the API Gateway.

Each business microservice owns its respective database.

## 7. Project Scope

### Included in Version 1

- Customer registration and authentication.
- Tailor registration and boutique approval.
- Boutique and service management.
- Stitching requests with measurements.
- Quotations and quotation decisions.
- Order creation and order tracking.
- Admin approval functionality.

### Planned for Future Versions

- Online payment integration.
- Delivery integration.
- Customer and tailor messaging.
- Mobile application.
- Advanced analytics and recommendations.

## 8. Project Goal

The goal is to develop a secure, maintainable, scalable, and production-oriented full-stack application using modern Java microservices and React technologies.
