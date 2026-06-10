# inventory-database-design.md

## Database Name

inventory_db

---

# Purpose

The Inventory Service is responsible for:

* Product Management
* Inventory Management
* Stock Tracking
* Inventory Transaction History
* Stock Updates from Vendor Receipts
* Stock Deductions from Dispatches

The service does not manage:

* Customer Purchase Orders
* Vendor Purchase Orders
* Dispatch Documents
* Authentication

Its responsibility is to maintain accurate product and inventory information across the system.

---

# Tables

## products

Stores product master information.

### Structure

```sql
id (PK)

product_code (UNIQUE)

product_name

description

unit_of_measure

hsn_code

is_active

created_at
updated_at
created_by
updated_by
```

---

# Field Description

| Field           | Description               |
| --------------- | ------------------------- |
| id              | Primary Key               |
| product_code    | Unique product identifier |
| product_name    | Product name              |
| description     | Product description       |
| unit_of_measure | Unit such as NOS, PCS, KG |
| hsn_code        | GST HSN code              |
| is_active       | Product status            |
| created_at      | Creation timestamp        |
| updated_at      | Modification timestamp    |
| created_by      | User who created record   |
| updated_by      | User who modified record  |

---

# Constraints

### Product Code

Must be unique.

```sql
UNIQUE(product_code)
```

---

# Example

```text
Product Code : PROD-001

Product Name : Router A

Unit : NOS
```

---

# inventory

Stores the current stock position of every product.

### Structure

```sql
id (PK)

product_id (UNIQUE)

total_stock

available_stock

last_stock_update_at

created_at
updated_at
created_by
updated_by
```

---

# Field Description

| Field                | Description                     |
| -------------------- | ------------------------------- |
| id                   | Primary Key                     |
| product_id           | Product reference               |
| total_stock          | Current stock quantity          |
| available_stock      | Available quantity              |
| last_stock_update_at | Last inventory update timestamp |
| created_at           | Creation timestamp              |
| updated_at           | Modification timestamp          |
| created_by           | User who created record         |
| updated_by           | User who modified record        |

---

# Business Rules

### Rule 1

Every product must have exactly one inventory record.

```text
One Product
       |
       | 1
       |
       | 1
       |
Inventory Record
```

---

### Rule 2

```text
total_stock >= 0
```

---

### Rule 3

```text
available_stock >= 0
```

---

### Rule 4

Since Version 1 does not support stock reservation:

```text
available_stock = total_stock
```

---

# inventory_transactions

Stores complete stock movement history.

Every inventory increase or decrease must be recorded in this table.

### Structure

```sql
id (PK)

transaction_number

product_id

transaction_type

quantity

reference_type

reference_id

remarks

transaction_date

created_at
created_by
```

---

# Field Description

| Field              | Description                |
| ------------------ | -------------------------- |
| id                 | Primary Key                |
| transaction_number | Unique transaction number  |
| product_id         | Product reference          |
| transaction_type   | Type of inventory movement |
| quantity           | Transaction quantity       |
| reference_type     | Source of transaction      |
| reference_id       | Source document ID         |
| remarks            | Additional notes           |
| transaction_date   | Transaction timestamp      |
| created_at         | Creation timestamp         |
| created_by         | User who created record    |

---

# Transaction Types

```text
STOCK_RECEIVED

DISPATCH

MANUAL_ADJUSTMENT
```

---

# Reference Types

```text
VENDOR_PO

DISPATCH

MANUAL
```

---

# Example

### Stock Received

```text
Transaction No : INV-TXN-001

Product : Router A

Transaction Type : STOCK_RECEIVED

Quantity : 100

Reference Type : VENDOR_PO

Reference Id : 10
```

---

### Dispatch

```text
Transaction No : INV-TXN-002

Product : Router A

Transaction Type : DISPATCH

Quantity : 50

Reference Type : DISPATCH

Reference Id : 20
```

---

# Inventory Update Rules

## When Material Is Received

Vendor Service publishes:

```text
STOCK_RECEIVED
```

Inventory Service updates:

```text
total_stock += received_quantity

available_stock += received_quantity
```

Creates:

```text
inventory_transactions
```

record.

---

## When Dispatch Is Completed

Dispatch Service publishes:

```text
DISPATCH_COMPLETED
```

Inventory Service updates:

```text
total_stock -= dispatched_quantity

available_stock -= dispatched_quantity
```

Creates:

```text
inventory_transactions
```

record.

---

# Relationships

## products → inventory

```text
products
    |
    | 1
    |
    | 1
inventory
```

One product has one inventory record.

---

## products → inventory_transactions

```text
products
    |
    | 1
    |
    | N
inventory_transactions
```

One product can have many inventory transactions.

---

# Example Inventory State

### Product

```text
Router A
```

---

### Inventory

```text
Total Stock : 150

Available Stock : 150
```

---

### Transactions

```text
+100 STOCK_RECEIVED

+100 STOCK_RECEIVED

-50 DISPATCH
```

Result:

```text
150
```

Current stock.

---

# Kafka Events Consumed

## STOCK_RECEIVED

Producer:

```text
Vendor Service
```

Topic:

```text
stock-received
```

Action:

```text
Increase inventory
Create inventory transaction
```

---

## DISPATCH_COMPLETED

Producer:

```text
Dispatch Service
```

Topic:

```text
dispatch-completed
```

Action:

```text
Decrease inventory
Create inventory transaction
```

---

# Future Enhancements

Possible future additions:

* Stock Reservation
* Warehouse Management
* Multiple Warehouses
* Bin Locations
* Batch Tracking
* Serial Number Tracking
* Inventory Reconciliation

These features are intentionally excluded from Version 1 to keep the Inventory Service focused on stock management and transaction tracking.
