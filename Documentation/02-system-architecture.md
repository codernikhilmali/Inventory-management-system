Architecture Style

The platform follows a Microservices Architecture combined with an Event-Driven Architecture.

Why Microservices?

Different business domains have different responsibilities:

Customer Management
Procurement
Inventory
Dispatch
Notifications

Separating them allows:

Independent deployment
Better scalability
Clear ownership
Easier maintenance
High-Level Architecture
                    Frontend
                        |
                        v
                 API Gateway
                        |
 ----------------------------------------------------
 |            |            |           |            |
 v            v            v           v            v

Auth      Customer     Product    Procurement   Dispatch
Service    Service     Service      Service      Service

                        |
                        v

                     Kafka

                        |
 ---------------------------------------------
 |                    |                      |
 v                    v                      v

Notification      Audit Service      Analytics Service
Service
Service Breakdown

We will start with 5 core services and add others later.

1. Auth Service
Responsibility

Authentication and authorization.

Owns
users
roles
user_roles
Features
Register User
Login User
JWT Generation
Role Management
APIs
POST /auth/register
POST /auth/login
GET  /auth/profile
2. Customer Service
Responsibility

Manage customers and customer purchase orders.

Owns
customers
customer_po
customer_po_items
Features
Customer Creation
Customer PO Creation
Customer PO Tracking
Customer PO Status
DRAFT
CREATED
PARTIALLY_ALLOCATED
ALLOCATED
PARTIALLY_DISPATCHED
DISPATCHED
COMPLETED
APIs
POST /customers
GET  /customers

POST /customer-po
GET  /customer-po/{id}
Kafka Events Produced
CUSTOMER_PO_CREATED
CUSTOMER_PO_UPDATED
CUSTOMER_PO_COMPLETED
3. Product & Inventory Service
Responsibility

Manage products and inventory.

Owns
products
inventory
inventory_transactions
stock_reservations
Features
Product Management
Inventory Tracking
Stock Reservation
Inventory Formula
Available Stock =
Total Stock - Reserved Stock
APIs
POST /products
GET  /products

GET  /inventory
POST /inventory/reserve
Kafka Events
PRODUCT_CREATED
STOCK_RECEIVED
LOW_STOCK
STOCK_RESERVED
4. Procurement Service
Responsibility

Vendor procurement and goods receipt.

Owns
vendors
vendor_po
vendor_po_items
goods_receipts
goods_receipt_items
Features
Vendor Management
Vendor PO Creation
PO PDF Generation
Email PO to Vendor
Goods Receipt
Vendor PO Status
DRAFT
CREATED
SENT
PARTIALLY_RECEIVED
RECEIVED
CLOSED
APIs
POST /vendors

POST /vendor-po

POST /goods-receipt
Kafka Events
VENDOR_PO_CREATED
GOODS_RECEIVED
VENDOR_PO_COMPLETED
5. Dispatch Service
Responsibility

Dispatch and shipment management.

Owns
dispatches
dispatch_items
Features
Create Dispatch
Allocate Material
Complete Shipment
APIs
POST /dispatch
PUT  /dispatch/complete
Kafka Events
DISPATCH_CREATED
DISPATCH_COMPLETED
Supporting Services

These services consume events.

6. Notification Service
Responsibility

Send notifications.

Examples
Vendor PO Email
Low Stock Alert
Dispatch Notification
Consumes
CUSTOMER_PO_CREATED
VENDOR_PO_CREATED
LOW_STOCK
DISPATCH_CREATED
7. Audit Service
Responsibility

Track every business action.

Stores
audit_logs
Consumes

All business events.

8. Analytics Service
Responsibility

Business reporting.

Example Metrics
Total Customer POs
Total Vendor POs
Inventory Value
Dispatch Count
Database Ownership Rule

Very important.

Each service owns its own database.

auth_db

customer_db

inventory_db

procurement_db

dispatch_db

notification_db

No service directly accesses another service's tables.

This is a core microservices principle.

Communication Pattern

We will use two patterns.

Pattern 1: Synchronous Communication

Used when an immediate response is needed.

Example:

Dispatch Service
        |
        v
Inventory Service

Question:

Can I allocate stock?

Answer required immediately.

Later we'll use OpenFeign.

Pattern 2: Asynchronous Communication

Used for business events.

Example:

Vendor PO Created
        |
        v
Kafka
        |
        +--> Notification
        |
        +--> Audit
        |
        +--> Analytics

No direct dependency.

Kafka Event Flow

This is the heart of the system.

CUSTOMER_PO_CREATED
           |
           v
     Procurement

VENDOR_PO_CREATED
           |
           v
     Notification

GOODS_RECEIVED
           |
           v
     Inventory Updated

STOCK_RECEIVED
           |
           v
     Customer PO Allocation

DISPATCH_CREATED
           |
           v
     Notification

DISPATCH_COMPLETED
           |
           v
     Customer PO Completed
Infrastructure Services
Eureka Server

Purpose:

Service Discovery

Services register themselves.

Example:

AUTH-SERVICE

CUSTOMER-SERVICE

INVENTORY-SERVICE

PROCUREMENT-SERVICE

DISPATCH-SERVICE
API Gateway

Single entry point.

/api/auth/**

/api/customers/**

/api/inventory/**

/api/procurement/**

/api/dispatch/**
Config Server

Centralized configuration.

Stores:

Database Config
Kafka Config
JWT Secret
Email Config
Deployment Strategy
Local
Spring Boot
MySQL
Kafka
Docker Compose
Production Simulation
Kubernetes
One Improvement Before We Continue

I think we should rename Product & Inventory Service to simply:

Inventory Service

Because inventory is the real business capability. Products are part of inventory management.

So our core services become:

Auth Service
Customer Service
Inventory Service
Procurement Service
Dispatch Service

This is cleaner and closer to real enterprise systems.