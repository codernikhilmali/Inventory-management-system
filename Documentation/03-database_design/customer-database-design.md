# customer-database-design.md

## Database Name

customer_db

---

# Purpose

The Customer Service is responsible for:

* Customer Management
* Customer Purchase Order (Customer PO) Management
* Customer Requirement Tracking
* Customer PO Status Tracking

The service does not manage:

* Inventory
* Vendor Purchase Orders
* Dispatch Operations
* Authentication

Its responsibility ends at managing customer information and customer requirements.

---

# Tables

## customers

Stores customer master information.

### Structure

```sql
id (PK)

customer_code (UNIQUE)

customer_name

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

| Field         | Description                |
| ------------- | -------------------------- |
| id            | Primary Key                |
| customer_code | Unique customer identifier |
| customer_name | Customer company name      |
| email         | Customer email             |
| phone         | Customer contact number    |
| gst_number    | Customer GST number        |
| address_line1 | Primary address            |
| address_line2 | Secondary address          |
| city          | Customer city              |
| state         | Customer state             |
| country       | Customer country           |
| pincode       | Postal code                |
| is_active     | Customer status            |
| created_at    | Creation timestamp         |
| updated_at    | Modification timestamp     |
| created_by    | User who created record    |
| updated_by    | User who modified record   |

---

# Constraints

### Customer Code

Must be unique.

```sql
UNIQUE(customer_code)
```

---

# customer_po

Stores Customer Purchase Order header information.

A Customer PO represents the requirement received from a customer.

### Structure

```sql
id (PK)

po_number (UNIQUE)

customer_id

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
| customer_id  | Customer reference       |
| po_date      | Customer PO date         |
| status       | Current PO status        |
| total_amount | Total PO value           |
| remarks      | Additional notes         |
| created_at   | Creation timestamp       |
| updated_at   | Modification timestamp   |
| created_by   | User who created record  |
| updated_by   | User who modified record |

---

# Customer PO Status

```text
DRAFT

CREATED

COMPLETED

CANCELLED
```

### Status Meaning

#### DRAFT

Customer PO is being prepared.

#### CREATED

Customer PO has been finalized and saved.

#### COMPLETED

Customer PO has been fully dispatched.

#### CANCELLED

Customer PO has been cancelled.

---

# customer_po_items

Stores line items belonging to a Customer PO.

### Structure

```sql
id (PK)

customer_po_id

product_id

quantity

unit_price

line_total

remarks
```

---

# Field Description

| Field          | Description                   |
| -------------- | ----------------------------- |
| id             | Primary Key                   |
| customer_po_id | Customer PO reference         |
| product_id     | Product reference             |
| quantity       | Requested quantity            |
| unit_price     | Selling price entered by user |
| line_total     | Quantity × Unit Price         |
| remarks        | Additional notes              |

---

# Business Rules

### Rule 1

```text
line_total =
quantity × unit_price
```

---

### Rule 2

```text
customer_po.total_amount =
sum(all customer_po_items.line_total)
```

---

### Rule 3

A Customer PO must contain at least one item.

---

### Rule 4

Quantity must be greater than zero.

```text
quantity > 0
```

---

### Rule 5

Unit Price must be greater than zero.

```text
unit_price > 0
```

---

# Relationships

## customers → customer_po

```text
customers
    |
    | 1
    |
    | N
customer_po
```

One customer can have multiple purchase orders.

---

## customer_po → customer_po_items

```text
customer_po
    |
    | 1
    |
    | N
customer_po_items
```

One purchase order can contain multiple products.

---

# Example

## Customer

```text
Customer Code : CUST-001

Customer Name : ABC Technologies
```

---

## Customer PO

```text
PO Number : CPO-1001
```

---

## Customer PO Items

```text
Router A

Quantity : 10

Unit Price : 5000

Line Total : 50000
```

```text
Switch B

Quantity : 5

Unit Price : 3000

Line Total : 15000
```

---

## Total Amount

```text
50000 + 15000

=

65000
```

Stored in:

```text
customer_po.total_amount
```

---

# Kafka Events Produced

## CUSTOMER_PO_CREATED

Published when a Customer PO is created successfully.

### Topic

```text
customer-po-created
```

### Consumers

```text
Notification Service
```

---

# Future Enhancements

Possible future additions:

* Customer Contacts
* Customer Credit Limits
* Customer Payment Terms
* Customer Invoices
* Customer Attachments

These features are intentionally excluded from Version 1 to keep the Customer Service focused on customer requirement management.
