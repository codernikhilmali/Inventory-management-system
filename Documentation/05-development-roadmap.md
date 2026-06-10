# 05-development-roadmap.md

## Purpose

This document defines the implementation roadmap for the Enterprise Procurement, Inventory & Dispatch Management Platform.

The objective is to build the project incrementally while understanding every concept, component, and line of code.

The project will follow a backend-first approach.

Frontend development is intentionally excluded from Version 1.

All features will be tested using APIs, database verification, and Kafka event verification.

---

# Development Principles

## Principle 1

Understand every line of code before writing it.

No copy-pasting from tutorials.

---

## Principle 2

Complete one service before moving to the next.

Avoid building multiple incomplete services simultaneously.

---

## Principle 3

Test through APIs.

Frontend development will begin only after backend implementation is complete.

---

## Principle 4

Every feature must be verified through:

```text
API Request
      ↓
Database Update
      ↓
Kafka Event
      ↓
Consumer Processing
```

---

# Development Phases

## Phase 1 - Auth Service

### Goal

Build authentication and user management.

### Concepts Covered

* Spring Boot Fundamentals
* REST APIs
* Spring Data JPA
* Validation
* Exception Handling
* BCrypt Password Encoding
* JWT Authentication

### Database

```text
auth_db

users
```

### Deliverables

```text
User Registration

User Login

JWT Token Generation

JWT Validation
```

### APIs

```text
POST /auth/register

POST /auth/login

GET /auth/profile
```

### Testing

```text
Register User

Verify User Saved In Database

Login User

Verify JWT Token Generated

Access Protected Endpoint
```

### Completion Criteria

User can register and login successfully.

---

# Phase 2 - Inventory Service

### Goal

Build product and inventory management.

### Concepts Covered

* Entity Relationships
* Service Layer Design
* DTOs
* CRUD Operations

### Database

```text
inventory_db

products

inventory

inventory_transactions
```

### Deliverables

```text
Create Product

Update Product

View Product

View Inventory
```

### APIs

```text
POST /products

PUT /products/{id}

GET /products

GET /products/{id}

GET /inventory
```

### Testing

```text
Create Product

Verify Product Saved

Verify Inventory Record Created
```

### Completion Criteria

Products and inventory can be managed successfully.

---

# Phase 3 - Customer Service

### Goal

Manage customers and customer purchase orders.

### Concepts Covered

* One-To-Many Relationships
* Customer PO Management
* Business Validation

### Database

```text
customer_db

customers

customer_po

customer_po_items
```

### Deliverables

```text
Create Customer

View Customer

Create Customer PO

View Customer PO
```

### APIs

```text
POST /customers

GET /customers

POST /customer-po

GET /customer-po/{id}
```

### Kafka Event

```text
CUSTOMER_PO_CREATED
```

### Testing

```text
Create Customer

Create Customer PO

Verify Database Records

Verify Kafka Event Published
```

### Completion Criteria

Customer requirements can be stored successfully.

---

# Phase 4 - Vendor Service

### Goal

Manage procurement and material receiving.

### Concepts Covered

* Procurement Workflow
* Vendor PO Management
* Event Publishing

### Database

```text
vendor_db

vendors

vendor_po

vendor_po_items
```

### Deliverables

```text
Create Vendor

View Vendor

Create Vendor PO

Receive Material
```

### APIs

```text
POST /vendors

GET /vendors

POST /vendor-po

PUT /vendor-po/{id}/receive
```

### Kafka Events

```text
VENDOR_PO_CREATED

STOCK_RECEIVED
```

### Testing

```text
Create Vendor

Create Vendor PO

Receive Material

Verify Kafka Events Published
```

### Completion Criteria

Material receipt process works successfully.

---

# Phase 5 - Kafka Integration

### Goal

Integrate Kafka across services.

### Concepts Covered

* Kafka Topics
* Producers
* Consumers
* Serialization
* Event-Driven Architecture

### Topics

```text
customer-po-created

vendor-po-created

stock-received

dispatch-completed
```

### Testing

```text
Publish Event

Verify Topic Message

Verify Consumer Processing
```

### Completion Criteria

Services communicate asynchronously through Kafka.

---

# Phase 6 - Dispatch Service

### Goal

Manage customer order fulfillment.

### Concepts Covered

* Dispatch Workflow
* Inventory Consumption
* Cross-Service Communication

### Database

```text
dispatch_db

dispatches

dispatch_items
```

### Deliverables

```text
Create Dispatch

Complete Dispatch
```

### APIs

```text
POST /dispatch

GET /dispatch/{id}
```

### Kafka Event

```text
DISPATCH_COMPLETED
```

### Testing

```text
Create Dispatch

Verify Dispatch Saved

Verify Kafka Event Published

Verify Customer PO Updated

Verify Inventory Updated
```

### Completion Criteria

Customer orders can be fulfilled successfully.

---

# Phase 7 - Notification Service

### Goal

Consume events and generate notifications.

### Concepts Covered

* Kafka Consumers
* Event Processing

### Version 1 Scope

No email integration.

Notifications will be generated through:

```text
Console Logs

JSON Responses
```

### Consumed Events

```text
CUSTOMER_PO_CREATED

VENDOR_PO_CREATED

STOCK_RECEIVED

DISPATCH_COMPLETED
```

### Testing

```text
Publish Event

Verify Notification Generated
```

### Completion Criteria

Notification service consumes all business events successfully.

---

# Phase 8 - Spring Cloud

### Goal

Introduce distributed system infrastructure.

### Components

```text
Eureka Server

API Gateway

Config Server
```

### Concepts Covered

* Service Discovery
* Centralized Configuration
* Request Routing

### Completion Criteria

All services communicate through Gateway and Eureka.

---

# Phase 9 - Docker

### Goal

Containerize the complete platform.

### Components

```text
Auth Service

Inventory Service

Customer Service

Vendor Service

Dispatch Service

Notification Service

MySQL

Kafka
```

### Deliverables

```text
Dockerfiles

Docker Compose
```

### Command

```bash
docker compose up
```

### Completion Criteria

Entire application runs through Docker Compose.

---

# Phase 10 - Kubernetes

### Goal

Deploy the platform using Kubernetes.

### Concepts Covered

* Pods
* Deployments
* Services
* ConfigMaps
* Secrets

### Deliverables

```text
Kubernetes Deployment Files

Kubernetes Service Files
```

### Completion Criteria

Application runs successfully inside Kubernetes.

---

# Version 1 Success Criteria

The project is considered complete when:

```text
User Authentication Works

Customer PO Creation Works

Vendor PO Creation Works

Stock Receipt Updates Inventory

Dispatch Completes Customer Orders

Kafka Events Flow Successfully

Notifications Are Generated

Services Are Containerized

Services Run Through Kubernetes
```

---

# Future Enhancements

Possible future improvements:

```text
React Frontend

Email Notifications

Audit Service

Role-Based Access Control

Serial Number Tracking

Multi-Warehouse Support

Reporting Dashboard

Analytics Service
```

These features are intentionally excluded from Version 1.
