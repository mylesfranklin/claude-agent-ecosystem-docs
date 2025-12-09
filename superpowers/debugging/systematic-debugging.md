# Systematic Debugging Skill

> **Source**: [obra/superpowers - systematic-debugging](https://github.com/obra/superpowers)
> **Purpose**: Four-phase root cause analysis for effective bug resolution

## Overview

Systematic debugging replaces ad-hoc guessing with a **structured, evidence-based approach** to finding and fixing bugs. This skill ensures problems are properly understood before solutions are attempted.

## The Four-Phase Process

```
┌─────────────────────────────────────────────────────────────────┐
│              PHASE 1: REPRODUCE                                 │
│  • Create reliable reproduction steps                          │
│  • Confirm the bug exists                                       │
│  • Document exact failure behavior                             │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│              PHASE 2: ISOLATE                                   │
│  • Narrow down the problem area                                │
│  • Use bisection to find the failing component                 │
│  • Create minimal reproduction case                            │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│              PHASE 3: DIAGNOSE                                  │
│  • Understand WHY the bug occurs                               │
│  • Identify root cause (not just symptoms)                     │
│  • Document the chain of causation                             │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│              PHASE 4: FIX & VERIFY                              │
│  • Implement minimal fix                                        │
│  • Write regression test                                        │
│  • Verify fix doesn't break other things                       │
└─────────────────────────────────────────────────────────────────┘
```

---

## Phase 1: Reproduce

### Goal
Create a **reliable, repeatable** way to trigger the bug.

### Steps

1. **Gather information**
   ```markdown
   - What is the expected behavior?
   - What is the actual behavior?
   - What are the exact steps to reproduce?
   - What environment (OS, version, configuration)?
   ```

2. **Create reproduction script**
   ```python
   # reproduction.py
   """
   Bug: User profile doesn't save when email contains '+'
   Expected: Profile saves successfully
   Actual: Silent failure, profile unchanged
   """

   def reproduce_bug():
       user = create_user("test+tag@example.com")
       user.name = "Updated Name"
       user.save()

       # Reload and verify
       reloaded = User.get(user.id)
       assert reloaded.name == "Updated Name", f"Bug confirmed: name is {reloaded.name}"

   if __name__ == "__main__":
       reproduce_bug()
   ```

3. **Confirm reproducibility**
   ```bash
   # Run multiple times to ensure consistency
   $ python reproduction.py  # Fails
   $ python reproduction.py  # Fails
   $ python reproduction.py  # Fails - consistent!
   ```

### Anti-Patterns

| Wrong | Right |
|-------|-------|
| "It just doesn't work" | Specific error message documented |
| "Sometimes it fails" | Identify conditions that cause failure |
| "I think the bug is in X" | Reproduce first, then diagnose |

---

## Phase 2: Isolate

### Goal
Narrow down the problem to the **smallest possible scope**.

### Bisection Strategy

```
Full System
    │
    ├── Frontend ────── Works? ─── No ──► Focus here
    │
    └── Backend
         │
         ├── API Layer ────── Works? ─── Yes
         │
         └── Database Layer ── Works? ─── No ──► Focus here
              │
              ├── Connection ── Works? ─── Yes
              │
              └── Query Builder ── Works? ─── No ──► ROOT CAUSE
```

### Techniques

1. **Binary Search**
   ```python
   # If you have 100 lines of code, test at line 50
   # If it works, bug is in lines 51-100
   # If it fails, bug is in lines 1-50
   # Repeat until found
   ```

2. **Component Isolation**
   ```python
   # Test each component independently
   def test_database_layer():
       """Does the database work correctly?"""
       db.execute("INSERT INTO users (email) VALUES ('test+tag@example.com')")
       result = db.execute("SELECT email FROM users WHERE email = 'test+tag@example.com'")
       assert result is not None  # If this fails, bug is in DB layer

   def test_orm_layer():
       """Does the ORM correctly handle special characters?"""
       user = User(email="test+tag@example.com")
       user.save()
       # If database test passes but this fails, bug is in ORM
   ```

3. **Minimal Reproduction**
   ```python
   # Reduce to smallest failing case
   # Original: 500 lines of setup
   # Minimal: 5 lines that trigger the bug

   def minimal_reproduction():
       email = "test+tag@example.com"
       sanitized = sanitize_email(email)  # BUG: '+' gets stripped
       assert sanitized == email
   ```

---

## Phase 3: Diagnose

### Goal
Understand **WHY** the bug occurs, not just where.

### Root Cause Analysis

1. **Ask "Why?" five times**
   ```
   1. Why does the profile not save?
      → Because the email validation fails

   2. Why does email validation fail?
      → Because the '+' character is rejected

   3. Why is '+' rejected?
      → Because the regex doesn't allow it

   4. Why doesn't the regex allow it?
      → Because it was copied from an old validation library

   5. Why was this not caught earlier?
      → Because no tests exist for emails with special characters
   ```

2. **Trace the execution**
   ```python
   # Add logging at key points
   def save_profile(user):
       logger.debug(f"1. Received user: {user.email}")
       validated_email = validate_email(user.email)
       logger.debug(f"2. After validation: {validated_email}")  # Check here
       sanitized = sanitize_input(validated_email)
       logger.debug(f"3. After sanitization: {sanitized}")  # Or here
       db.save(sanitized)
       logger.debug(f"4. Saved to database")
   ```

3. **Document the causal chain**
   ```markdown
   ## Root Cause Analysis

   **Symptom**: User profile doesn't save for emails with '+'

   **Chain of Causation**:
   1. User enters email "test+tag@example.com"
   2. `sanitize_input()` calls `remove_special_chars()`
   3. `remove_special_chars()` uses regex `[^a-zA-Z0-9@.]`
   4. This regex removes '+' from the email
   5. Email becomes "testtag@example.com" (invalid)
   6. Database unique constraint fails silently
   7. Profile appears unchanged to user

   **Root Cause**: `remove_special_chars()` is too aggressive
   '+' is valid in email local-part per RFC 5321
   ```

---

## Phase 4: Fix & Verify

### Goal
Implement a **minimal fix** and ensure it works.

### Steps

1. **Write failing test FIRST**
   ```python
   def test_email_with_plus_saves_correctly():
       """Regression test for bug #1234"""
       user = create_user()
       user.email = "test+tag@example.com"
       user.save()

       reloaded = User.get(user.id)
       assert reloaded.email == "test+tag@example.com"
   ```

2. **Implement minimal fix**
   ```python
   # Before (broken)
   def remove_special_chars(text):
       return re.sub(r'[^a-zA-Z0-9@.]', '', text)

   # After (fixed)
   def remove_special_chars(text):
       # Allow '+' which is valid in email addresses
       return re.sub(r'[^a-zA-Z0-9@.+]', '', text)
   ```

3. **Verify fix doesn't break other things**
   ```bash
   # Run full test suite
   $ pytest

   # Run specific regression tests
   $ pytest tests/test_email_validation.py -v

   # Manual verification
   $ python reproduction.py  # Should pass now
   ```

4. **Document the fix**
   ```markdown
   ## Fix Summary

   **Bug**: #1234 - Emails with '+' don't save
   **Root Cause**: Overly aggressive character sanitization
   **Fix**: Allow '+' in sanitize_input regex

   **Files Changed**:
   - `utils/sanitize.py` - Updated regex pattern
   - `tests/test_sanitize.py` - Added regression test

   **Verification**:
   - All existing tests pass
   - New regression test passes
   - Manual reproduction test passes
   ```

---

## Debugging Toolbox

### Logging Strategy

```python
import logging

# Configure levels appropriately
logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger(__name__)

def problematic_function(data):
    logger.debug(f"Input: {data}")

    step1_result = step1(data)
    logger.debug(f"After step1: {step1_result}")

    step2_result = step2(step1_result)
    logger.debug(f"After step2: {step2_result}")

    return step2_result
```

### Debugger Usage

```python
# Python debugger
import pdb; pdb.set_trace()

# Or use breakpoint() in Python 3.7+
breakpoint()

# Conditional breakpoint
if suspicious_condition:
    breakpoint()
```

### Print Debugging (When Appropriate)

```python
# Quick checks (remove before committing!)
print(f"DEBUG: variable = {variable}")
print(f"DEBUG: type = {type(variable)}")
print(f"DEBUG: len = {len(variable)}")
```

---

## Common Bug Patterns

| Pattern | Symptoms | Typical Cause |
|---------|----------|---------------|
| Off-by-one | Works for some inputs, fails at boundaries | Loop conditions, array indexing |
| Race condition | Intermittent failures | Concurrent access, timing |
| Null pointer | Crashes on missing data | Unhandled None/null cases |
| Encoding issues | Special characters break things | UTF-8/ASCII mismatches |
| State leakage | Tests pass alone, fail together | Shared mutable state |

---

## Verification Checklist

Before closing a bug:

- [ ] Reproduction script exists and originally failed
- [ ] Root cause is documented (not just the fix)
- [ ] Regression test prevents recurrence
- [ ] Fix is minimal (no unrelated changes)
- [ ] Full test suite passes
- [ ] Reproduction script now passes
- [ ] Code reviewed by another developer
