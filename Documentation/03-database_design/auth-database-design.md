# auth-database-design.md

## Database Name

auth_db

---

# Purpose

The Auth Service is responsible for:

* User Registration
* User Login
* JWT Authentication
* User Account Management

The service does not contain business logic related to customers, vendors, inventory, procurement, or dispatch.

Its only responsibility is authentication and user management.

---

# Tables

## users

Stores application user information.

### Structure

```sql
id (PK)

first_name
last_name

email (UNIQUE)

phone

password

is_active

last_login_at

created_at
updated_at
created_by
updated_by
```

---

# Field Description

| Field         | Description                     |
| ------------- | ------------------------------- |
| id            | Primary Key                     |
| first_name    | User first name                 |
| last_name     | User last name                  |
| email         | Login email address             |
| phone         | Contact number                  |
| password      | Encrypted password using BCrypt |
| is_active     | User account status             |
| last_login_at | Last successful login timestamp |
| created_at    | Record creation timestamp       |
| updated_at    | Record modification timestamp   |
| created_by    | User who created record         |
| updated_by    | User who modified record        |

---

# Constraints

### Email

Must be unique.

```sql
UNIQUE(email)
```

---

# Authentication Flow

### Registration

User submits:

```json
{
  "firstName": "Nikhil",
  "lastName": "Mali",
  "email": "nikhil@gmail.com",
  "password": "password"
}
```

System:

* Validates request
* Encrypts password using BCrypt
* Stores user record

---

### Login

User submits:

```json
{
  "email": "nikhil@gmail.com",
  "password": "password"
}
```

System:

* Validates credentials
* Generates JWT Token
* Returns token to client

---

# Relationships

No foreign key relationships.

Auth Service owns its data independently.

---

# Future Enhancements

Possible future additions:

* Roles
* Permissions
* Password Reset
* Refresh Tokens
* Account Locking
* Multi-Factor Authentication (MFA)

These features are intentionally excluded from Version 1 to keep the authentication module simple and focused.
