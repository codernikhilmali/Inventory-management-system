# vendor-database-design.md

## Database Name

vendor_db

---

# Purpose

The Vendor Service is responsible for:

* Vendor Management
* Vendor Purchase Order (Vendor PO) Management
* Procurement Tracking
* Material Receipt Tracking
* Publishing Stock Received Events

The service does not manage:

* Inventory
* Customer Purchase Orders
* Dispatch Operations
* Authentication

Its responsibility ends at managing vendors and procurement activities.

---

# Tables

## vendors

Stores vendor master information.

### Structure

```sql
id (PK)

vendor_code (UNIQUE)

vendor_name

email
phone

gst_number

address_line1
address_line2

city
state
country
pincode

is_active

created_at
updated_at
created_by
updated_by
```

---

# Field Description

| Field         | Description              |
| ------------- | ------------------------ |
| id            | Primary Key              |
| vendor_code   | Unique vendor identifier |
| vendor_name   | Vendor company name      |
| email         | Vendor email             |
| phone         | Vendor contact number    |
| gst_number    | Vendor GST number        |
| address_line1 | Primary address          |
| address_line2 | Secondary address        |
| city          | Vendor city              |
| state         | Vendor state             |
| country       | Vendor country           |
| pincode       | Postal code              |
| is_active     | Vendor status            |
| created_at    | Creation timestamp       |
| updated_at    | Modification timestamp   |
| created_by    | User who created record  |
| updated_by    | User who modified record |

---

# Constraints

### Vendor Code

Must be unique.

```sql
UNIQUE(vendor_code)
```

---

# vendor_po

Stores Vendor Purchase Order header information.

A Vendor PO represents a purchase order sent to a vendor for procuring material.

### Structure

```sql
id (PK)

po_number (UNIQUE)

vendor_id

po_date

status

total_amount

remarks

created_at
updated_at
created_by
updated_by
```

---

# Field Description

| Field        | Description              |
| ------------ | ------------------------ |
| id           | Primary Key              |
| po_number    | Unique PO number         |
| vendor_id    | Vendor reference         |
| po_date      | Vendor PO date           |
| status       | Current PO status        |
| total_amount | Total PO value           |
| remarks      | Additional notes         |
| created_at   | Creation timestamp       |
| updated_at   | Modification timestamp   |
| created_by   | User who created record  |
| updated_by   | User who modified record |

---

# Vendor PO Status

```text
DRAFT

CREATED

SENT

RECEIVED

CLOSED

CANCELLED
```

---

# Status Meaning

### DRAFT

Vendor PO is being prepared.

### CREATED

Vendor PO has been saved.

### SENT

Vendor PO has been shared with vendor.

### RECEIVED

All ordered quantities have been received.

### CLOSED

Procurement cycle completed.

### CANCELLED

Vendor PO cancelled.

---

# vendor_po_items

Stores line items belonging to a Vendor PO.

### Structure

```sql
id (PK)

vendor_po_id

product_id

ordered_quantity

received_quantity

unit_price

line_total

remarks
```

---

# Field Description

| Field             | Description                   |
| ----------------- | ----------------------------- |
| id                | Primary Key                   |
| vendor_po_id      | Vendor PO reference           |
| product_id        | Product reference             |
| ordered_quantity  | Quantity ordered from vendor  |
| received_quantity | Quantity received from vendor |
| unit_price        | Purchase price per unit       |
| line_total        | Ordered Quantity × Unit Price |
| remarks           | Additional notes              |

---

# Business Rules

### Rule 1

```text
line_total =
ordered_quantity × unit_price
```

---

### Rule 2

```text
vendor_po.total_amount =
sum(all vendor_po_items.line_total)
```

---

### Rule 3

A Vendor PO must contain at least one item.

---

### Rule 4

```text
ordered_quantity > 0
```

---

### Rule 5

```text
unit_price > 0
```

---

### Rule 6

```text
received_quantity <= ordered_quantity
```

Received quantity can never exceed ordered quantity.

---

### Rule 7

Vendor PO status becomes:

```text
RECEIVED
```

when:

```text
For every item:

ordered_quantity
=
received_quantity
```

---

# Material Receiving Flow

### Vendor PO

```text
Router A      Ordered : 100
Switch B      Ordered : 50
```

---

### First Receipt

```text
Router A      Received : 100
Switch B      Received : 0
```

Vendor PO remains:

```text
SENT
```

because all items have not yet been received.

---

### Final Receipt

```text
Router A      Received : 100
Switch B      Received : 50
```

Vendor PO automatically becomes:

```text
RECEIVED
```

---

# Relationships

## vendors → vendor_po

```text
vendors
    |
    | 1
    |
    | N
vendor_po
```

One vendor can have multiple purchase orders.

---

## vendor_po → vendor_po_items

```text
vendor_po
    |
    | 1
    |
    | N
vendor_po_items
```

One purchase order can contain multiple products.

---

# Example

## Vendor

```text
Vendor Code : VEN-001

Vendor Name : XYZ Suppliers
```

---

## Vendor PO

```text
PO Number : VPO-1001
```

---

## Vendor PO Items

```text
Router A

Ordered Quantity : 100

Received Quantity : 50

Unit Price : 4500

Line Total : 450000
```

---

# Kafka Events Produced

## VENDOR_PO_CREATED

Published when a Vendor PO is created successfully.

### Topic

```text
vendor-po-created
```

### Consumers

```text
Notification Service
```

---

## STOCK_RECEIVED

Published whenever material is received against a Vendor PO.

### Topic

```text
stock-received
```

### Consumers

```text
Inventory Service

Notification Service
```

---

# STOCK_RECEIVED Example Payload

```json
{
  "eventId": "uuid",
  "eventType": "STOCK_RECEIVED",
  "vendorPoId": 100,
  "productId": 1,
  "receivedQuantity": 50
}
```

---

# Future Enhancements

Possible future additions:

* Vendor Contacts
* Vendor Bank Details
* Vendor Performance Rating
* Vendor Documents
* Purchase Cost Analytics

These features are intentionally excluded from Version 1 to keep the Vendor Service focused on procurement and material receipt management.
