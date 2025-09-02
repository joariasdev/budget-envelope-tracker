# Budget Envelope Tracker API Documentation

## Base URL

```
/api/v1
```

## Authentication

All endpoints (except auth) require a Bearer token in the Authorization header:

```
Authorization: Bearer <token>
```

---

## Authentication Endpoints

### Register User

```http
POST /api/v1/auth/register
```

**Request Body:**

```json
{
  "email": "user@example.com",
  "password": "securepassword",
  "first_name": "John",
  "last_name": "Doe"
}
```

### Login User

```http
POST /api/v1/auth/login
```

**Request Body:**

```json
{
  "email": "user@example.com",
  "password": "securepassword"
}
```

**Response:**

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": 1,
    "email": "user@example.com",
    "first_name": "John",
    "last_name": "Doe"
  }
}
```

---

## Categories

### List Categories

```http
GET /api/v1/categories
```

**Response:**

```json
[
  {
    "id": 1,
    "title": "Groceries",
    "budget": 500.0,
    "balance": 275.5,
    "created_at": "2025-08-27T10:30:00Z",
    "updated_at": "2025-08-27T10:30:00Z"
  }
]
```

### Create Category

```http
POST /api/v1/categories
```

**Request Body:**

```json
{
  "title": "Entertainment",
  "budget": 200.0
}
```

### Update Category

```http
PUT /api/v1/categories/:id
```

**Request Body:**

```json
{
  "title": "Updated Title",
  "budget": 300.0
}
```

### Delete Category

```http
DELETE /api/v1/categories/:id
```

---

## Transactions

### List Transactions

```http
GET /api/v1/transactions
```

**Response:**

```json
[
  {
    "id": 156,
    "description": "Starbucks Coffee",
    "amount": 25.0,
    "transaction_type": "expense",
    "transaction_datetime": "2025-08-27T14:30:00Z",
    "category_to_id": 2,
    "category_from_id": null,
    "created_at": "2025-08-27T14:30:15Z",
    "updated_at": "2025-08-27T14:30:15Z"
  }
]
```

### Create Transaction

```http
POST /api/v1/transactions
```

**Expense Example:**

```json
{
  "description": "Starbucks Coffee",
  "amount": 25.0,
  "transaction_type": "expense",
  "transaction_datetime": "2025-08-27T14:30:00Z",
  "category_to_id": 2
}
```

**Income Example:**

```json
{
  "description": "Monthly Salary",
  "amount": 500.0,
  "transaction_type": "income",
  "transaction_datetime": "2025-08-27T09:00:00Z",
  "category_to_id": 1
}
```

**Transfer Example:**

```json
{
  "description": "Moving budget to entertainment",
  "amount": 100.0,
  "transaction_type": "transfer",
  "transaction_datetime": "2025-08-27T15:00:00Z",
  "category_from_id": 1,
  "category_to_id": 2
}
```

### Update Transaction

```http
PUT /api/v1/transactions/:id
```

### Delete Transaction

```http
DELETE /api/v1/transactions/:id
```

---

## Error Responses

All errors follow this format:

```json
{
  "error": {
    "code": "ERROR_CODE",
    "message": "Human readable error message"
  }
}
```

### Common Error Codes

| Code                   | Status | Description                                    |
| ---------------------- | ------ | ---------------------------------------------- |
| `INSUFFICIENT_BALANCE` | 400    | Transaction exceeds available category balance |
| `CATEGORY_NOT_FOUND`   | 404    | Specified category does not exist              |
| `VALIDATION_ERROR`     | 400    | Invalid request data                           |
| `UNAUTHORIZED`         | 401    | Missing or invalid authentication token        |

**Insufficient Balance Example:**

```json
{
  "error": {
    "code": "INSUFFICIENT_BALANCE",
    "message": "Cannot spend $500.00. Groceries category has $100.00 available.",
    "details": {
      "available_balance": 100.0,
      "requested_amount": 500.0
    }
  }
}
```

---

## Business Rules

### Envelope Budgeting Rules

- **Hard Balance Limits**: Cannot spend more than available category balance
- **No Negative Balances**: Transactions that would result in negative balances are rejected
- **Transfer Validation**: Source category must have sufficient balance for transfers

### Transaction Types

- **Expense**: Money leaving a category (reduces balance)
- **Income**: Money entering a category (increases balance)
- **Transfer**: Money moving between categories (reduces source, increases destination)

---

## Usage Examples

### Typical Workflow

1. **Register/Login** to get authentication token
2. **Create categories** for budget envelopes (Groceries, Entertainment, etc.)
3. **Add income** transactions to fund categories
4. **Record expenses** as they occur
5. **Transfer money** between categories as needed
6. **Monitor balances** to stay within budget limits

### Frontend Integration Notes

- Store auth token securely (not in localStorage)
- Refresh category list after transaction creation to show updated balances
- Validate transaction amounts on frontend before API calls
- Handle insufficient balance errors gracefully in UI
