# System Architecture Definition

## Task 1.1: System Components

**Major "Things" (Entities):**

- Users (authentication/authorization)
- Categories/Envelopes (spending categories with budget limits)
- Transactions (spending events assigned to categories)

**Major "Actions" (Operations):**

- Create/edit/delete categories with budget limits
- Record and categorize transactions
- Calculate and display remaining balances
- Transfer money between categories
- Display visual envelope fill levels

**System Components Identified:**

1. User Management - handles authentication, registration, user settings
2. Category Management - manages spending categories and budget limits
3. Transaction Processing - records and categorizes spending events
4. Balance Calculation - computes balances, fill levels, handles transfers

## Task 1.2: Component Dependencies

**Dependency Chain:**

```
User → Category → Transaction → Balance
```

**Validation:**

- Linear dependency chain (no circular dependencies)
- Clean build order identified
- Balance Calculation depends on BOTH Category AND Transaction data

## Task 1.3: Technology Stack

**Frontend:** React + TypeScript

- **Why:** Component-based architecture fits envelope UI concept
- **Why TypeScript:** Type safety for financial calculations

**Backend:** Node.js + Express

- **Why:** JavaScript everywhere, lightweight REST API framework
- **Why fits:** Straightforward CRUD operations with business logic

**Database:** PostgreSQL

- **Why:** ACID compliance for financial data integrity
- **Why:** Strong relational features for user→category→transaction relationships

## Task 1.4: System Architecture Diagram

**Architecture Pattern:** 3-Tier Architecture

- **Presentation Tier:** React + TypeScript (User Interface)
- **Business Logic Tier:** Node.js + Express (All 4 components)
- **Data Tier:** PostgreSQL (Users, Categories, Transactions tables)
