# 04-kafka-events.md

## Purpose

Kafka is used for asynchronous communication between microservices.

Instead of one service directly calling multiple services, it publishes an event to Kafka and interested services consume that event.

Example:

Customer Service
→ Kafka
→ Notification Service

Vendor Service
→ Kafka
→ Inventory Service
→ Notification Service

Dispatch Service
→ Kafka
→ Customer Service
→ Inventory Service
→ Notification Service

This keeps services loosely coupled and scalable.

---

# Event 1: CUSTOMER_PO_CREATED

## Producer

Customer Service

## Published When

A new Customer Purchase Order is created successfully.

## Topic

customer-po-created

## Payload

```json
{
  "eventId": "uuid",
  "eventType": "CUSTOMER_PO_CREATED",
  "customerPoId": 1,
  "customerPoNumber": "CPO-1001",
  "customerId": 10,
  "eventTime": "2026-06-11T10:30:00"
}
```

## Consumers

Notification Service

## Notification Action

```text
Customer PO Created Successfully

PO Number: CPO-1001
Customer ID: 10
```

---

# Event 2: VENDOR_PO_CREATED

## Producer

Vendor Service

## Published When

A Vendor Purchase Order is created successfully.

## Topic

vendor-po-created

## Payload

```json
{
  "eventId": "uuid",
  "eventType": "VENDOR_PO_CREATED",
  "vendorPoId": 100,
  "vendorPoNumber": "VPO-1001",
  "vendorId": 20,
  "eventTime": "2026-06-11T10:30:00"
}
```

## Consumers

Notification Service

## Notification Action

```text
Vendor PO Created Successfully

PO Number: VPO-1001
Vendor ID: 20
```

---

# Event 3: STOCK_RECEIVED

## Producer

Vendor Service

## Published When

User updates received quantity against a Vendor PO.

Example:

Ordered Quantity = 100

Received Quantity = 50

The event is published immediately after receiving material.

## Topic

stock-received

## Payload

```json
{
  "eventId": "uuid",
  "eventType": "STOCK_RECEIVED",
  "vendorPoId": 100,
  "productId": 1,
  "receivedQuantity": 50,
  "eventTime": "2026-06-11T10:30:00"
}
```

## Consumers

Inventory Service

Notification Service

---

## Inventory Service Action

Update inventory.

```text
total_stock += receivedQuantity

available_stock += receivedQuantity
```

Create inventory transaction:

```text
transaction_type = STOCK_RECEIVED
reference_type = VENDOR_PO
reference_id = vendorPoId
```

---

## Notification Action

```text
Stock Received

Product ID: 1
Quantity: 50
Vendor PO: VPO-1001
```

---

# Event 4: DISPATCH_COMPLETED

## Producer

Dispatch Service

## Published When

A dispatch document is created and material is dispatched.

For this project:

Dispatch Created = Dispatch Completed

There is no separate shipment tracking stage.

## Topic

dispatch-completed

## Payload

```json
{
  "eventId": "uuid",
  "eventType": "DISPATCH_COMPLETED",
  "dispatchId": 50,
  "dispatchNumber": "DSP-1001",
  "customerPoId": 1,
  "eventTime": "2026-06-11T10:30:00"
}
```

## Consumers

Customer Service

Inventory Service

Notification Service

---

## Customer Service Action

Update Customer PO status.

```text
customer_po.status = COMPLETED
```

---

## Inventory Service Action

Deduct inventory for all dispatched items.

```text
total_stock -= dispatched_quantity
```

Create inventory transaction.

```text
transaction_type = DISPATCH
reference_type = DISPATCH
reference_id = dispatchId
```

---

## Notification Action

```text
Dispatch Completed

Dispatch Number: DSP-1001
Customer PO: CPO-1001
```

---

# Kafka Topics

```text
customer-po-created

vendor-po-created

stock-received

dispatch-completed
```

---

# Notification Service Scope (Version 1)

Notification Service will not send emails.

It will only consume Kafka events and generate console logs or JSON notifications.

Example:

```json
{
  "message": "Customer PO Created Successfully",
  "referenceNumber": "CPO-1001",
  "timestamp": "2026-06-11T10:30:00"
}
```

Email integration can be added in a future version.

---

# Kafka Learning Objectives

This project will demonstrate:

* Kafka Producers
* Kafka Consumers
* Topics
* JSON Serialization
* Event-Driven Architecture
* Service Decoupling
* Asynchronous Processing

without introducing unnecessary complexity.
