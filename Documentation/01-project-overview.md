Project Name

Enterprise Procurement, Inventory & Dispatch Management Platform

Overview

The Enterprise Procurement, Inventory & Dispatch Management Platform is a distributed microservices-based business application used by trading, distribution, logistics, and system integration companies to manage the complete lifecycle of customer requirements, procurement, inventory management, and dispatch operations.

The platform enables organizations to receive customer purchase orders, analyze material requirements, procure materials from vendors, track inventory, manage partial and complete receipts, reserve stock, create dispatches, and fulfill customer orders efficiently.

The system follows a real-world procurement-to-dispatch workflow commonly used in enterprise organizations and leverages Spring Boot, Spring Cloud, Kafka, Docker, Kubernetes, and MySQL to demonstrate modern backend architecture patterns.

Business Problem

Organizations frequently receive customer purchase orders containing multiple products and quantities.

Available inventory may not always be sufficient to fulfill customer requirements immediately.

The organization must:

Track customer requirements.
Check available inventory.
Procure missing materials from vendors.
Receive goods partially or fully.
Maintain accurate stock levels.
Reserve inventory against customer requirements.
Dispatch materials to customers.
Maintain complete audit and notification records.

Managing this process manually through spreadsheets often results in delays, stock inconsistencies, and poor traceability.

The platform solves these challenges through automation and event-driven communication.

Core Business Flow
Customer Purchase Order Received
            |
            v
     Requirement Analysis
            |
            v
      Inventory Check
            |
    +-------+-------+
    |               |
    | Stock Exists  |
    |               |
    v               |
Reserve Material    |
    |               |
    v               |
Dispatch            |
                    |
                    |
            Stock Shortage
                    |
                    v
          Create Vendor PO
                    |
                    v
             Send to Vendor
                    |
                    v
            Material Received
                    |
                    v
            Inventory Updated
                    |
                    v
          Material Reserved
                    |
                    v
                Dispatch
                    |
                    v
           Customer Order Closed
Main Business Entities
Customer

Represents organizations purchasing products.

Customer Purchase Order (Customer PO)

Represents customer requirements.

Example:

Customer PO

Router A      50 Qty
Switch B      20 Qty
Server C       5 Qty
Vendor

Represents suppliers providing materials.

Vendor Purchase Order (Vendor PO)

Created when stock is insufficient.

Example:

Vendor PO

Router A      20 Qty
Server C       5 Qty
Product

Represents inventory items managed by the company.

Inventory

Maintains:

Total Stock
Reserved Stock
Available Stock

Formula:

Available Stock =
Total Stock - Reserved Stock
Goods Receipt

Represents materials received from vendors.

Supports:

Partial Receipt
Full Receipt
Dispatch

Represents shipment of products to customers.

Key Business Features
Customer Management
Create Customers
Update Customers
Customer Contact Information
Customer PO Management
Create Customer PO
Add Multiple Line Items
Track Fulfillment Status
Vendor Management
Vendor Registration
Vendor Details
Vendor Contact Management
Procurement Management
Create Vendor PO
Generate PO Document
Email PO to Vendor
Track Procurement Status
Inventory Management
Product Management
Stock Tracking
Reservation Management
Inventory Visibility
Goods Receipt Management
Partial Receipt
Full Receipt
Automatic Stock Updates
Dispatch Management
Create Dispatch
Material Allocation
Shipment Tracking
Notification Management
Email Notifications
Business Alerts
Audit & Analytics
Activity Tracking
Business Reporting
Operational Dashboard
System Architecture Goals

The platform is intentionally designed to learn and demonstrate:

Spring Boot
REST APIs
Spring Data JPA
Validation
Exception Handling
Transactions
Spring Security
JWT Authentication
Authorization
Role-Based Access Control
Spring Cloud
Eureka Server
API Gateway
Config Server
Apache Kafka
Producers
Consumers
Event-Driven Architecture
Consumer Groups
Docker
Dockerfiles
Docker Compose
Container Networking
Kubernetes
Deployments
Services
ConfigMaps
Secrets
Auto Scaling
Learning Objective

The primary objective of this project is not only to build a business application but also to gain a deep understanding of enterprise backend development concepts, microservices architecture, distributed systems, event-driven communication, containerization, and cloud-native deployment while preparing for Spring Boot and Java backend interviews.