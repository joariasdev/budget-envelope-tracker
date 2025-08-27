# Step 2: Data Structure Definition

## Task 2.1: Core Entities

**User Entity:**

- id, email, password_hash, first_name, last_name, created_at, updated_at
- **Key decisions:** Email for authentication, separate name fields for personalization

**Category Entity:**

- id, user_id, title, budget, created_at, updated_at
- **Key decisions:** Budget stored as limit, balance calculated from transactions

**Transaction Entity:**

- id, description, amount, transaction_type, transaction_datetime, created_at, updated_at, category_to_id, category_from_id (nullable)
- **Key decisions:** Single table for all transaction types, elegant transfer handling

## Task 2.2: Entity Relationships

**Relationship Structure:**

```
User (1) ←→ (Many) Categories (1) ←→ (Many) Transactions
                 ↑___________________________|
                    (transfers only)
```

**Key Relationships:**

- **User → Categories:** One-to-Many (user_id foreign key)
- **Categories → Transactions:** One-to-Many with dual relationship for transfers
- **Transfer Logic:** Single transaction with category_from_id and category_to_id

**Design Decision:** Transfers handled in single transaction record rather than separate entity

## Task 2.3: Database Schema Design

**Users Table:**

```
id: serial primary key
email: varchar(255) unique not null
password_hash: varchar(255) not null
first_name: varchar(100) not null
last_name: varchar(100) not null
created_at: timestamp not null default current_timestamp
updated_at: timestamp not null default current_timestamp
```

**Categories Table:**

```
id: serial primary key
user_id: integer references users(id) not null
title: varchar(255) not null
budget: decimal(10,2) not null check (budget >= 0)
created_at: timestamp not null default current_timestamp
updated_at: timestamp not null default current_timestamp
unique(user_id, title)
```

**Transactions Table:**

```
id: serial primary key
description: varchar(255) not null
amount: decimal(10,2) not null check (amount > 0)
transaction_type: enum('income', 'expense', 'transfer') not null
transaction_datetime: timestamp not null
category_to_id: integer references categories(id) not null
category_from_id: integer references categories(id) null
created_at: timestamp not null default current_timestamp
updated_at: timestamp not null default current_timestamp

-- Business rule constraints:
CHECK (
(transaction_type = 'transfer' AND category_from_id IS NOT NULL) OR
(transaction_type != 'transfer' AND category_from_id IS NULL)
)

CHECK (
transaction_type != 'transfer' OR category_from_id != category_to_id
)
```
