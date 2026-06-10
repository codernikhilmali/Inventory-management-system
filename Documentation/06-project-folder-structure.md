# 06-project-folder-structure.md

## Purpose

This document defines the standard project structure that will be followed across all microservices.

The structure is designed to:

* Improve maintainability
* Improve scalability
* Support large codebases
* Encourage domain-driven design
* Follow industry best practices
* Make interview discussions easier

---

# Repository Structure

```text
enterprise-procurement-platform
│
├── docs
│
├── auth-service
│
├── customer-service
│
├── inventory-service
│
├── vendor-service
│
├── dispatch-service
│
├── notification-service
│
├── api-gateway
│
├── config-server
│
├── discovery-server
│
├── docker
│
└── kubernetes
```

---

# Standard Microservice Structure

Every service follows the same structure.

Example:

```text
inventory-service
│
├── src
│   ├── main
│   │   ├── java
│   │   │
│   │   └── resources
│   │
│   └── test
│
├── Dockerfile
│
├── pom.xml
│
└── README.md
```

---

# Package Structure

Base Package:

```text
com.nikhil.procurement.inventory
```

Example:

```text
src/main/java
│
└── com
    └── nikhil
        └── procurement
            └── inventory
```

---

# Root Packages

Every service contains:

```text
inventory
│
├── config
│
├── exception
│
├── common
│
├── product
│
└── InventoryServiceApplication
```

---

# config Package

Contains application configuration.

```text
config
│
├── OpenApiConfig
│
├── KafkaConfig
│
├── SecurityConfig
│
└── ModelMapperConfig
```

Examples:

```java
KafkaConfig.java

SecurityConfig.java
```

No business logic here.

---

# exception Package

Contains exception handling.

```text
exception
│
├── GlobalExceptionHandler
│
│
├── ResourceNotFoundException
│
├── BadRequestException
│
└── BusinessException
```

Responsibilities:

* Centralized exception handling
* Custom exceptions

---

# common Package

Contains reusable code.

```text
common
│
├── constants
│
├── enums
│
├── util
│
├── response
│
└── mapper
```

Examples:

```java
ApiResponse.java

DateUtil.java
```

---

# Feature-Based Structure

Instead of:

```text
controller
service
repository
entity
```

at the root level,

we group code by business feature.

---

# Example: Product Module

```text
product
│
├── controller
│
├── service
│
│   ├── ProductService
│   └── ProductServiceImpl
│
├── repository
│
├── entity
│
├── dto
│
│   ├── request
│   │
│   └── response
│
├── mapper
│
├── validator
│
└── event
```

Everything related to Product stays together.

---

# DTO Structure

```text
dto
│
├── request
│   │
│   ├── CreateProductRequest
│   └── UpdateProductRequest
│
└── response
    │
    ├── ProductResponse
    └── ProductSummaryResponse
```

Request and response objects are separated.

---

# Service Layer Structure

```text
service
│
├── ProductService
│
└── ProductServiceImpl
```

Example:

```java
public interface ProductService
```

```java
public class ProductServiceImpl
```

Interview Benefit:

Can discuss Dependency Inversion Principle.

---

# Repository Layer

```text
repository
│
└── ProductRepository
```

Only database operations.

---

# Entity Layer

```text
entity
│
└── Product
```

Contains JPA entities.

No business logic.

---

# Mapper Layer

```text
mapper
│
└── ProductMapper
```

Responsibilities:

```text
Entity → DTO

DTO → Entity
```

---

# Validator Layer

```text
validator
│
└── ProductValidator
```

Contains business validations.

Example:

```text
Product Code Already Exists

Invalid Product Name
```

---

# Event Package

Used when Kafka is introduced.

```text
event
│
├── producer
│
├── consumer
│
└── payload
```

Example:

```text
event
│
├── producer
│   └── StockReceivedProducer
│
├── consumer
│   └── DispatchCompletedConsumer
│
└── payload
    └── StockReceivedEvent
```

---

# Resources Structure

```text
resources
│
├── application.yml
│
├── db
│   └── migration
│
└── logback-spring.xml
```

---

# Database Migrations

We will use:

```text
Flyway
```

Structure:

```text
db
│
└── migration
    │
    ├── V1__create_users_table.sql
    │
    ├── V2__create_products_table.sql
    │
    └── V3__create_inventory_table.sql
```

No manual database creation.

---

# Testing Structure

```text
test
│
├── controller
│
├── service
│
└── repository
```

Future:

```text
Unit Tests

Integration Tests
```

---

# Coding Standards

## Constructor Injection Only

Use:

```java
private final ProductRepository repository;

public ProductServiceImpl(ProductRepository repository)
```

Avoid:

```java
@Autowired
private ProductRepository repository;
```

---

## No Business Logic In Controller

Controller:

```text
Receive Request

Call Service

Return Response
```

Only.

---

## Service Contains Business Logic

All rules belong here.

---

## Repository Contains Database Logic

Only data access.

---

# Final Architecture Principle

```text
Feature First

Not Layer First
```

Meaning:

```text
product
│
├── controller
├── service
├── repository
├── entity
├── dto
```

is preferred over:

```text
controller
service
repository
entity
```

at the application root.

This structure scales much better as the project grows and is commonly used in modern Spring Boot microservice applications.
