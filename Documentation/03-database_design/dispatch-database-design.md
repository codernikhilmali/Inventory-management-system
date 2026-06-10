# dispatch-database-design.md

## Database Name

dispatch_db

---

# Purpose

The Dispatch Service is responsible for:

* Creating Dispatch Documents
* Managing Dispatch Items
* Tracking Material Dispatch
* Completing Customer Order Fulfillment
* Publishing Dispatch Events

The service does not manage:

* Inventory
* Customer Information
* Vendor Information
* Authentication

Its responsibility begins when material is ready to be shipped and ends when the dispatch is completed.

---

# Tables

## dispatches

Stores dispatch header information.

A dispatch represents the shipment created against a Customer Purchase Order.

### Structure

```sql
id (PK)

dispatch_number (UNIQUE)

customer_po_id

customer_id

dispatch_date

status

remarks

created_at
updated_at
created_by
updated_by
```

---

# Field Description

| Field           | Description              |
| --------------- | ------------------------ |
| id              | Primary Key              |
| dispatch_number | Unique dispatch number   |
| customer_po_id  | Customer PO reference    |
| customer_id     | Customer reference       |
| dispatch_date   | Dispatch date            |
| status          | Current dispatch status  |
| remarks         | Additional notes         |
| created_at      | Creation timestamp       |
| updated_at      | Modification timestamp   |
| created_by      | User who created record  |
| updated_by      | User who modified record |

---

# Constraints

### Dispatch Number

Must be unique.

```sql
UNIQUE(dispatch_number)
```

---

# Dispatch Status

```text
CREATED

COMPLETED

CANCELLED
```

---

# Status Meaning

### CREATED

Dispatch document has been created.

Material is ready for shipment.

---

### COMPLETED

Material has been dispatched successfully.

Customer PO fulfillment is completed.

---

### CANCELLED

Dispatch has been cancelled.

No inventory deduction should occur.

---

# dispatch_items

Stores products included in a dispatch.

### Structure

```sql
id (PK)

dispatch_id

product_id

quantity

remarks
```

---

# Field Description

| Field       | Description               |
| ----------- | ------------------------- |
| id          | Primary Key               |
| dispatch_id | Dispatch reference        |
| product_id  | Product reference         |
| quantity    | Quantity being dispatched |
| remarks     | Additional notes          |

---

# Business Rules

### Rule 1

A Dispatch must contain at least one item.

---

### Rule 2

```text
quantity > 0
```

---

### Rule 3

One Customer PO can have only one Dispatch.

```text
Customer PO
      |
      | 1
      |
      | 1
      |
Dispatch
```

This rule is intentionally enforced in Version 1 to keep the business flow simple.

---

### Rule 4

A Dispatch can only be created when sufficient stock is available.

---

### Rule 5

When Dispatch status changes to:

```text
COMPLETED
```

the system publishes:

```text
DISPATCH_COMPLETED
```

Kafka event.

---

# Relationships

## dispatches → dispatch_items

```text
dispatches
     |
     | 1
     |
     | N
dispatch_items
```

One dispatch can contain multiple products.

---

# Example

## Customer PO

```text
CPO-1001

Router A      10
Switch B       5
```

---

## Dispatch

```text
DSP-1001
```

---

## Dispatch Items

```text
Router A

Quantity : 10
```

```text
Switch B

Quantity : 5
```

---

# Dispatch Completion Flow

```text
Dispatch Created
        |
        v
Dispatch Completed
        |
        v
Kafka Event Published
        |
        +------------------+
        |                  |
        v                  v

Inventory Service    Customer Service
```

---

# Kafka Events Produced

## DISPATCH_COMPLETED

Published when a dispatch is completed successfully.

### Topic

```text
dispatch-completed
```

### Payload

```json
{
  "eventId": "uuid",
  "eventType": "DISPATCH_COMPLETED",
  "dispatchId": 1,
  "dispatchNumber": "DSP-1001",
  "customerPoId": 10,
  "eventTime": "2026-06-11T10:30:00"
}
```

---

# Consumer Actions

## Inventory Service

Updates inventory.

```text
total_stock -= dispatched_quantity

available_stock -= dispatched_quantity
```

Creates inventory transaction:

```text
transaction_type = DISPATCH
```

---

## Customer Service

Updates Customer PO status.

```text
customer_po.status = COMPLETED
```

---

## Notification Service

Generates notification.

Example:

```text
Dispatch Completed

Dispatch Number : DSP-1001

Customer PO : CPO-1001
```

---

# Future Enhancements

Possible future additions:

* Partial Dispatch
* Multiple Dispatches per Customer PO
* Shipment Tracking
* Delivery Tracking
* Transport Information
* E-Way Bill Integration

These features are intentionally excluded from Version 1 to keep the Dispatch Service focused on simple order fulfillment and inventory deduction.
