# Testing Anti-Patterns

> **Source**: [obra/superpowers - testing-anti-patterns](https://github.com/obra/superpowers)
> **Purpose**: Document common testing mistakes to avoid

## Overview

This guide documents **testing anti-patterns** that undermine test effectiveness. Recognizing these patterns helps write better, more maintainable tests.

---

## Anti-Pattern 1: The Giant Test

### Problem

```python
def test_everything_about_user_registration():
    # Setup
    user_data = {"email": "test@example.com", "password": "secret123"}

    # Test registration
    response = register_user(user_data)
    assert response.status == 200
    assert response.data["id"] is not None

    # Test email verification
    token = get_verification_token(response.data["email"])
    verify_response = verify_email(token)
    assert verify_response.status == 200

    # Test login
    login_response = login(user_data)
    assert login_response.status == 200
    assert login_response.data["token"] is not None

    # Test profile update
    profile_response = update_profile(login_response.data["token"], {"name": "Test"})
    assert profile_response.status == 200

    # ... 200 more lines
```

### Why It's Bad

- Hard to identify which assertion failed
- Slow to run
- Difficult to maintain
- Can't run specific scenarios in isolation

### Solution

```python
def test_user_registration_creates_account():
    response = register_user({"email": "test@example.com", "password": "secret"})
    assert response.status == 200
    assert response.data["id"] is not None


def test_email_verification_activates_account():
    user = create_unverified_user()
    token = get_verification_token(user.email)

    response = verify_email(token)

    assert response.status == 200
    assert User.get(user.id).verified is True


def test_login_returns_auth_token():
    user = create_verified_user()

    response = login({"email": user.email, "password": "secret"})

    assert response.status == 200
    assert response.data["token"] is not None
```

---

## Anti-Pattern 2: Testing Implementation Details

### Problem

```python
def test_user_service_uses_cache():
    user_service = UserService()

    user_service.get_user(1)

    # Testing HOW it works, not WHAT it does
    assert user_service._cache.get.called_once_with(1)
    assert user_service._database.query.not_called()
```

### Why It's Bad

- Breaks when refactoring internals
- Doesn't test actual behavior
- Creates fragile tests
- Couples tests to implementation

### Solution

```python
def test_get_user_returns_same_result_on_repeated_calls():
    user_service = UserService()

    first_call = user_service.get_user(1)
    second_call = user_service.get_user(1)

    assert first_call == second_call


def test_get_user_is_fast_on_repeated_calls():
    user_service = UserService()

    # First call - may be slow (database)
    user_service.get_user(1)

    # Second call - should be fast (cached)
    start = time.time()
    user_service.get_user(1)
    duration = time.time() - start

    assert duration < 0.01  # Less than 10ms
```

---

## Anti-Pattern 3: The Liar Test

### Problem

```python
def test_data_processing():
    # This test always passes but tests nothing meaningful
    result = process_data(sample_data)
    assert result is not None  # Always true if function returns anything
    assert isinstance(result, dict)  # Type check, not behavior
```

### Why It's Bad

- Creates false confidence
- Doesn't catch actual bugs
- Wastes test execution time
- Misleads code coverage metrics

### Solution

```python
def test_process_data_extracts_correct_fields():
    sample_data = {"raw_name": "John Doe", "raw_email": "john@example.com"}

    result = process_data(sample_data)

    assert result["name"] == "John Doe"
    assert result["email"] == "john@example.com"
    assert "raw_name" not in result
    assert "raw_email" not in result
```

---

## Anti-Pattern 4: The Mockery

### Problem

```python
def test_send_notification():
    # Everything is mocked - testing nothing real
    mock_user = Mock()
    mock_user.email = "test@example.com"

    mock_email_service = Mock()
    mock_email_service.send.return_value = True

    mock_template_engine = Mock()
    mock_template_engine.render.return_value = "Hello"

    mock_logger = Mock()

    result = send_notification(
        mock_user, mock_email_service,
        mock_template_engine, mock_logger
    )

    assert result is True  # Of course it is - we mocked it to be True!
```

### Why It's Bad

- Tests mock behavior, not real behavior
- Provides zero confidence
- Complex setup for no value
- Mocks can diverge from real implementations

### Solution

```python
# Use real objects where possible, mock only external boundaries

def test_send_notification_sends_email(mock_smtp_server):
    """Use a mock SMTP server, but real everything else."""
    user = create_user(email="test@example.com")
    email_service = EmailService(smtp=mock_smtp_server)
    template_engine = TemplateEngine()

    result = send_notification(user, email_service, template_engine)

    assert result is True
    assert mock_smtp_server.received_email_to("test@example.com")
```

---

## Anti-Pattern 5: The Secret Catcher

### Problem

```python
def test_api_endpoint():
    try:
        response = api.call_endpoint()
        assert response.status == 200
    except Exception:
        pass  # Silently catches all errors - test always "passes"
```

### Why It's Bad

- Hides failures
- Test can never fail
- Provides false confidence
- Bugs go undetected

### Solution

```python
def test_api_endpoint_returns_success():
    response = api.call_endpoint()
    assert response.status == 200


def test_api_endpoint_handles_invalid_input():
    with pytest.raises(ValidationError):
        api.call_endpoint(invalid_data)
```

---

## Anti-Pattern 6: The Slow Poke

### Problem

```python
def test_with_real_external_services():
    # Makes actual HTTP calls - slow and flaky
    response = requests.get("https://external-api.com/data")
    result = process_external_data(response.json())
    assert result["processed"] is True


def test_with_sleep():
    start_async_job()
    time.sleep(30)  # Wait for job to complete
    assert job_completed()
```

### Why It's Bad

- Slow feedback loop
- Flaky due to network issues
- Expensive (API calls may cost money)
- Not deterministic

### Solution

```python
# Use fixtures and mocks for external services
def test_process_external_data(mock_external_api):
    mock_external_api.return_value = {"data": "sample"}

    result = process_external_data()

    assert result["processed"] is True


# Use polling instead of sleep
def test_async_job_completes():
    start_async_job()

    # Poll with timeout instead of fixed sleep
    result = wait_for(job_completed, timeout=5, interval=0.1)

    assert result is True
```

---

## Anti-Pattern 7: The Flickering Test

### Problem

```python
def test_random_behavior():
    result = get_random_recommendation()
    assert result in ["A", "B", "C"]  # Passes sometimes, fails others


def test_time_dependent():
    if datetime.now().hour < 12:
        expected = "Good morning"
    else:
        expected = "Good afternoon"
    assert get_greeting() == expected  # Fails at noon
```

### Why It's Bad

- Non-deterministic results
- Erodes trust in test suite
- Wastes debugging time
- Leads to "retry until pass" mentality

### Solution

```python
# Control randomness with seeds
def test_random_recommendation():
    random.seed(42)  # Deterministic seed

    result = get_random_recommendation()

    assert result == "B"  # Consistent result


# Inject time dependencies
def test_morning_greeting(freeze_time):
    freeze_time("2024-01-15 09:00:00")

    assert get_greeting() == "Good morning"


def test_afternoon_greeting(freeze_time):
    freeze_time("2024-01-15 14:00:00")

    assert get_greeting() == "Good afternoon"
```

---

## Anti-Pattern 8: The Commented-Out Test

### Problem

```python
# def test_important_feature():
#     """TODO: Fix this test later"""
#     result = important_feature()
#     assert result.works

@pytest.mark.skip(reason="Temporarily disabled")
def test_critical_path():
    ...

@pytest.mark.xfail
def test_known_broken():
    ...
```

### Why It's Bad

- "Temporary" becomes permanent
- Technical debt accumulates
- Coverage metrics lie
- Bugs hide in disabled tests

### Solution

**Rule**: Fix or delete. Never disable.

```python
# If test is broken, fix the code or fix the test
# If test is obsolete, delete it
# If test is flaky, make it deterministic

# Track as technical debt with deadline
@pytest.mark.skip(reason="JIRA-1234: Fix by 2024-02-01")
def test_needs_fix():
    ...
```

---

## Quick Reference: Anti-Pattern Detection

| Anti-Pattern | Warning Sign | Fix |
|--------------|--------------|-----|
| Giant Test | 100+ lines, many assertions | Split into focused tests |
| Implementation Details | Mocking private methods | Test behavior, not structure |
| Liar Test | `assert x is not None` | Assert specific values |
| Mockery | More mocks than real code | Use real objects at boundaries |
| Secret Catcher | `except: pass` | Let exceptions fail tests |
| Slow Poke | `time.sleep()`, real HTTP | Mock externals, use polling |
| Flickering | Random/time-dependent | Control inputs, freeze time |
| Commented-Out | `@skip`, `# test_...` | Fix or delete |

---

## Prevention Checklist

Before committing a test, verify:

- [ ] Test has a clear, specific purpose
- [ ] Test name describes expected behavior
- [ ] Assertions test actual values, not just existence
- [ ] External dependencies are mocked at boundaries
- [ ] Test runs in < 1 second (unit) or < 10 seconds (integration)
- [ ] Test produces same result every run
- [ ] No commented-out or skipped tests without JIRA ticket
