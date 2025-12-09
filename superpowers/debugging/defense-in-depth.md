# Defense in Depth Skill

> **Source**: [obra/superpowers - defense-in-depth](https://github.com/obra/superpowers)
> **Purpose**: Multiple validation layers to catch errors before they propagate

## Overview

Defense in Depth is a strategy that uses **multiple layers of validation** to catch errors as early as possible. No single layer is trusted completely; instead, each layer verifies the work of previous layers.

## Core Principle

```
┌─────────────────────────────────────────────────────────────────┐
│                     Layer 1: INPUT VALIDATION                   │
│         "Reject invalid input at the boundary"                  │
└─────────────────────────┬───────────────────────────────────────┘
                          │ Only valid input passes
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                     Layer 2: TYPE CHECKING                      │
│         "Ensure data types are correct"                         │
└─────────────────────────┬───────────────────────────────────────┘
                          │ Only correctly typed data passes
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                     Layer 3: BUSINESS RULES                     │
│         "Verify business logic constraints"                     │
└─────────────────────────┬───────────────────────────────────────┘
                          │ Only logically valid data passes
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                     Layer 4: DATABASE CONSTRAINTS               │
│         "Final backstop at persistence layer"                   │
└─────────────────────────┬───────────────────────────────────────┘
                          │ Only persistable data passes
                          ▼
                    [DATA STORED SAFELY]
```

---

## Layer 1: Input Validation

### Purpose
Reject invalid input at the system boundary before it enters your codebase.

### Implementation

```python
from pydantic import BaseModel, validator, EmailStr
from typing import Optional

class UserInput(BaseModel):
    email: EmailStr
    name: str
    age: Optional[int] = None

    @validator('name')
    def name_must_not_be_empty(cls, v):
        if not v or not v.strip():
            raise ValueError('Name cannot be empty')
        if len(v) > 100:
            raise ValueError('Name too long (max 100 characters)')
        return v.strip()

    @validator('age')
    def age_must_be_reasonable(cls, v):
        if v is not None and (v < 0 or v > 150):
            raise ValueError('Age must be between 0 and 150')
        return v


# Usage at API boundary
def create_user_endpoint(request_data: dict):
    try:
        validated = UserInput(**request_data)  # Layer 1 validation
    except ValidationError as e:
        return {"error": str(e)}, 400

    return create_user(validated)
```

### Key Rules

- Validate ALL external input (user input, API responses, file contents)
- Fail fast with clear error messages
- Never trust input from untrusted sources
- Whitelist valid input rather than blacklist invalid

---

## Layer 2: Type Checking

### Purpose
Ensure data flowing through your system has the correct types.

### Static Type Checking

```python
from typing import List, Optional, Dict
from dataclasses import dataclass

@dataclass
class User:
    id: int
    email: str
    name: str
    age: Optional[int] = None


def get_users_by_age(users: List[User], min_age: int) -> List[User]:
    """
    Type hints enable static analysis to catch errors.
    Run: mypy your_code.py
    """
    return [u for u in users if u.age is not None and u.age >= min_age]


# This would be caught by mypy:
# get_users_by_age(users, "18")  # Error: str instead of int
```

### Runtime Type Checking

```python
from typeguard import typechecked

@typechecked
def process_payment(amount: float, currency: str) -> bool:
    """Runtime validation of types."""
    # If called with wrong types, raises TypeError
    return amount > 0


# Runtime error if called incorrectly:
# process_payment("100", "USD")  # TypeError at runtime
```

---

## Layer 3: Business Rules

### Purpose
Ensure data meets business logic requirements beyond basic type/format validation.

### Implementation

```python
class Order:
    def __init__(self, items: list, customer: Customer):
        self.items = items
        self.customer = customer
        self._validate_business_rules()

    def _validate_business_rules(self):
        """Layer 3: Business rule validation."""

        # Rule 1: Orders must have at least one item
        if not self.items:
            raise BusinessRuleViolation("Order must contain at least one item")

        # Rule 2: Customer must have verified email
        if not self.customer.email_verified:
            raise BusinessRuleViolation("Customer email must be verified")

        # Rule 3: Order total must not exceed credit limit
        total = sum(item.price for item in self.items)
        if total > self.customer.credit_limit:
            raise BusinessRuleViolation(
                f"Order total ${total} exceeds credit limit ${self.customer.credit_limit}"
            )

        # Rule 4: Restricted items require age verification
        restricted_items = [i for i in self.items if i.age_restricted]
        if restricted_items and not self.customer.age_verified:
            raise BusinessRuleViolation(
                "Age verification required for restricted items"
            )
```

### Invariant Checking

```python
class BankAccount:
    def __init__(self, balance: float):
        self.balance = balance
        self._check_invariants()

    def withdraw(self, amount: float):
        self.balance -= amount
        self._check_invariants()  # Verify after every mutation

    def deposit(self, amount: float):
        self.balance += amount
        self._check_invariants()  # Verify after every mutation

    def _check_invariants(self):
        """Invariants that must ALWAYS be true."""
        assert self.balance >= 0, "Balance cannot be negative"
        assert isinstance(self.balance, (int, float)), "Balance must be numeric"
```

---

## Layer 4: Database Constraints

### Purpose
Final backstop to prevent invalid data from being persisted.

### SQL Constraints

```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) NOT NULL UNIQUE,
    name VARCHAR(100) NOT NULL,
    age INT CHECK (age >= 0 AND age <= 150),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    -- Constraints as final defense
    CONSTRAINT email_format CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z]{2,}$'),
    CONSTRAINT name_not_empty CHECK (LENGTH(TRIM(name)) > 0)
);

CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    user_id INT NOT NULL REFERENCES users(id),
    total DECIMAL(10,2) NOT NULL CHECK (total > 0),
    status VARCHAR(20) NOT NULL DEFAULT 'pending',

    CONSTRAINT valid_status CHECK (status IN ('pending', 'paid', 'shipped', 'completed', 'cancelled'))
);
```

### ORM-Level Constraints

```python
from sqlalchemy import Column, Integer, String, CheckConstraint
from sqlalchemy.orm import validates

class User(Base):
    __tablename__ = 'users'

    id = Column(Integer, primary_key=True)
    email = Column(String(255), nullable=False, unique=True)
    name = Column(String(100), nullable=False)
    age = Column(Integer)

    __table_args__ = (
        CheckConstraint('age >= 0 AND age <= 150', name='valid_age'),
        CheckConstraint("LENGTH(TRIM(name)) > 0", name='name_not_empty'),
    )

    @validates('email')
    def validate_email(self, key, email):
        """ORM-level validation before database."""
        if '@' not in email:
            raise ValueError("Invalid email format")
        return email.lower()
```

---

## Layered Validation in Practice

### Complete Example

```python
from pydantic import BaseModel, validator
from typing import Optional

# Layer 1: Input Schema
class CreateUserRequest(BaseModel):
    email: str
    name: str
    age: Optional[int] = None

    @validator('email')
    def validate_email_format(cls, v):
        if '@' not in v or '.' not in v:
            raise ValueError('Invalid email format')
        return v.lower()

    @validator('name')
    def validate_name(cls, v):
        v = v.strip()
        if not v:
            raise ValueError('Name required')
        return v


# Layer 2 & 3: Domain Model
class User:
    def __init__(self, email: str, name: str, age: Optional[int] = None):
        self.email = email
        self.name = name
        self.age = age
        self._validate()

    def _validate(self):
        """Business rules validation."""
        if self.age is not None and self.age < 13:
            raise BusinessRuleViolation("Users must be at least 13 years old")


# Layer 4: Repository with DB constraints
class UserRepository:
    def save(self, user: User) -> User:
        try:
            db_user = UserModel(
                email=user.email,
                name=user.name,
                age=user.age
            )
            session.add(db_user)
            session.commit()
            return user
        except IntegrityError as e:
            if 'unique constraint' in str(e):
                raise DuplicateEmailError(user.email)
            raise


# Controller orchestrating all layers
def create_user_endpoint(request_data: dict):
    # Layer 1: Input validation
    try:
        validated_input = CreateUserRequest(**request_data)
    except ValidationError as e:
        return {"error": "Invalid input", "details": e.errors()}, 400

    # Layer 2 & 3: Domain validation
    try:
        user = User(
            email=validated_input.email,
            name=validated_input.name,
            age=validated_input.age
        )
    except BusinessRuleViolation as e:
        return {"error": str(e)}, 422

    # Layer 4: Persistence
    try:
        saved_user = repository.save(user)
    except DuplicateEmailError:
        return {"error": "Email already registered"}, 409

    return {"user": saved_user.to_dict()}, 201
```

---

## Defense in Depth Checklist

For every piece of external input:

- [ ] **Layer 1**: Is input validated at the boundary?
- [ ] **Layer 2**: Are types enforced (static + runtime)?
- [ ] **Layer 3**: Are business rules checked?
- [ ] **Layer 4**: Are database constraints in place?

For every mutation:

- [ ] Are invariants checked after the change?
- [ ] Is the operation atomic (all or nothing)?
- [ ] Are errors handled and propagated appropriately?

---

## Key Benefits

| Benefit | Description |
|---------|-------------|
| **Early Detection** | Errors caught at boundary, not deep in code |
| **Clear Errors** | Each layer provides specific error messages |
| **Redundancy** | Multiple chances to catch issues |
| **Isolation** | Bugs in one layer don't corrupt data |
| **Auditability** | Clear points to add logging/monitoring |
