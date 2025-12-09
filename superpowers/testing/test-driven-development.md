# Test-Driven Development (TDD) Skill

> **Source**: [obra/superpowers - test-driven-development](https://github.com/obra/superpowers)
> **Purpose**: Enforce RED-GREEN-REFACTOR cycles for reliable code development

## Overview

Test-Driven Development is a **core discipline** in the Superpowers framework. Every feature, bug fix, or refactoring MUST follow the TDD cycle.

## The RED-GREEN-REFACTOR Cycle

```
┌─────────────────────────────────────────────────────────────────┐
│                         RED PHASE                               │
│  1. Write a failing test                                        │
│  2. Run the test - watch it FAIL                               │
│  3. Verify failure is for the expected reason                  │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                        GREEN PHASE                              │
│  1. Write MINIMAL code to pass the test                        │
│  2. No extra features, no "while I'm here" changes             │
│  3. Run tests - verify they PASS                               │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                      REFACTOR PHASE                             │
│  1. Clean up code while tests pass                             │
│  2. Remove duplication                                          │
│  3. Improve naming and structure                               │
│  4. Tests must STAY GREEN                                       │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
                    [COMMIT & REPEAT]
```

---

## Core Rules

### 1. Never Write Code Without a Failing Test

```
❌ WRONG:
   - Write feature code
   - Write tests after
   - "It works, let me add tests"

✓ RIGHT:
   - Write test that describes expected behavior
   - Run test, see it fail
   - Write code to make test pass
```

### 2. Watch the Test Fail

This is **non-negotiable**. You MUST see the test fail before writing implementation code.

**Why?**
- Confirms test is actually testing something
- Verifies test setup is correct
- Ensures test can detect regressions
- Proves you understand the requirement

### 3. Write Minimal Code

```python
# Test: test_add_returns_sum
def test_add_returns_sum():
    assert add(2, 3) == 5

# ❌ WRONG - Over-engineering
def add(a, b):
    if not isinstance(a, (int, float)):
        raise TypeError("a must be numeric")
    if not isinstance(b, (int, float)):
        raise TypeError("b must be numeric")
    logger.info(f"Adding {a} + {b}")
    result = a + b
    metrics.record("add_operation")
    return result

# ✓ RIGHT - Minimal implementation
def add(a, b):
    return a + b
```

### 4. Commit After Each Cycle

Each RED-GREEN-REFACTOR cycle should result in a commit:

```bash
# After GREEN phase
git add -A
git commit -m "Add feature: [description]"

# After REFACTOR phase (if changes made)
git add -A
git commit -m "Refactor: [description]"
```

---

## TDD Workflow Template

### Step 1: Understand the Requirement

```markdown
Feature: User can reset password

Given: User has a registered email
When: User requests password reset
Then: Reset email is sent
And: Reset token is valid for 24 hours
```

### Step 2: Write the First Test

```python
# test_password_reset.py

def test_password_reset_sends_email():
    """User receives reset email when requesting password reset."""
    user = create_user(email="test@example.com")

    result = request_password_reset("test@example.com")

    assert result.success is True
    assert_email_sent_to("test@example.com")
    assert_email_contains("reset your password")
```

### Step 3: Run Test and Watch It Fail

```bash
$ pytest test_password_reset.py -v

FAILED test_password_reset.py::test_password_reset_sends_email
E       NameError: name 'request_password_reset' is not defined
```

**Verify**: The failure is because the function doesn't exist (expected).

### Step 4: Write Minimal Implementation

```python
# password_reset.py

def request_password_reset(email: str):
    user = find_user_by_email(email)
    if not user:
        return Result(success=False)

    token = generate_reset_token(user)
    send_reset_email(user.email, token)

    return Result(success=True)
```

### Step 5: Run Test and Watch It Pass

```bash
$ pytest test_password_reset.py -v

PASSED test_password_reset.py::test_password_reset_sends_email
```

### Step 6: Refactor (if needed)

```python
# After refactoring - tests must still pass
def request_password_reset(email: str) -> Result:
    """Request password reset for user with given email."""
    user = find_user_by_email(email)
    if not user:
        return Result.failure("User not found")

    token = generate_reset_token(user)
    send_reset_email(user.email, token)

    return Result.success()
```

### Step 7: Commit

```bash
git add -A
git commit -m "Add password reset request functionality"
```

---

## Test Types and When to Use Them

### Unit Tests

```python
# Test individual functions in isolation
def test_calculate_discount():
    assert calculate_discount(100, 0.1) == 90
```

**Use for**: Pure functions, business logic, utilities

### Integration Tests

```python
# Test components working together
def test_checkout_creates_order_and_sends_email():
    cart = create_cart_with_items([item1, item2])
    user = create_user()

    order = checkout(cart, user)

    assert order.status == "confirmed"
    assert_email_sent_to(user.email)
```

**Use for**: Service interactions, database operations, API calls

### End-to-End Tests

```python
# Test complete user workflows
def test_user_can_purchase_product():
    # Login
    login_as(test_user)

    # Add to cart
    navigate_to("/products/widget")
    click("Add to Cart")

    # Checkout
    navigate_to("/checkout")
    fill_payment_details(test_card)
    click("Place Order")

    # Verify
    assert_page_contains("Order Confirmed")
```

**Use for**: Critical user journeys, smoke tests

---

## TDD Anti-Patterns to Avoid

### 1. Test After Development (TAD)

```
❌ "I'll write tests after the code works"

Problem: Tests become documentation, not drivers
Result: Incomplete coverage, false confidence
```

### 2. Testing Implementation Details

```python
# ❌ WRONG - Tests internal implementation
def test_uses_redis_cache():
    result = get_user(1)
    assert redis_client.get.called_once()

# ✓ RIGHT - Tests behavior
def test_get_user_returns_cached_result():
    user1 = get_user(1)
    user2 = get_user(1)  # Should use cache
    assert user1 == user2
    assert database_query_count() == 1
```

### 3. Large Test Suites with Slow Feedback

```
❌ "Running tests takes 20 minutes"

Solution:
- Isolate slow tests
- Use test parallelization
- Mock external services
- Run fast tests frequently, slow tests on CI
```

### 4. Ignoring Failing Tests

```
❌ @skip("TODO: fix later")
❌ @pytest.mark.xfail

Rule: Fix or delete. Never ignore.
```

---

## Verification Checklist

Before considering a TDD cycle complete:

- [ ] Test was written BEFORE implementation
- [ ] Test was seen to FAIL
- [ ] Failure was for the EXPECTED reason
- [ ] Implementation is MINIMAL
- [ ] All tests PASS
- [ ] Code is COMMITTED
- [ ] No tests were skipped or ignored

---

## Integration with Superpowers Workflow

TDD integrates with other Superpowers skills:

```
writing-plans
    └── Includes test requirements for each task

subagent-driven-development
    └── Each subagent follows TDD for their task

verification-before-completion
    └── Confirms TDD cycle was followed

code-review
    └── Verifies tests exist and are meaningful
```

---

## Quick Reference

| Phase | Action | Verification |
|-------|--------|--------------|
| RED | Write failing test | Test runs and fails |
| RED | Verify failure reason | Error matches expectation |
| GREEN | Write minimal code | Test passes |
| GREEN | No extra features | Only test requirement met |
| REFACTOR | Clean up code | Tests still pass |
| REFACTOR | Commit changes | Clean git history |
