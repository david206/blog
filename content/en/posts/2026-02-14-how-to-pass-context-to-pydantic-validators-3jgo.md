---
title: "How to Pass Context to Pydantic Validators"
draft: false
devto_id: 3241970
slug: "how-to-pass-context-to-pydantic-validators-3jgo"
cover_image: "/devto-assets/how-to-pass-context-to-pydantic-validators-3jgo/cover-1.png"
devto_url: "https://dev.to/david_shimon/how-to-pass-context-to-pydantic-validators-3jgo"
devto_published: false
date: "2026-02-14T20:33:46Z"
description: "I had a function that needed to behave differently depending on who was calling it. Not what it does,..."
tags:
  - "python"
  - "fastapi"
  - "pydantic"
  - "backend"
---

I had a function that needed to behave differently depending on who was calling it. Not _what_ it does, just whether it should log errors or stay quiet.

Sounds simple. It wasn't.

## The Setup

We have a phone number normalization function. It takes messy phone numbers from various data sources and formats them consistently. Sometimes the numbers are garbage (missing digits, wrong format, random text). When that happens, the function returns the original value and moves on.

The function is wired into our Pydantic models as a `BeforeValidator`:

```python
from typing import Annotated
from pydantic import BeforeValidator

PhoneNumber = Annotated[str, BeforeValidator(normalize_phone_number)]
```

This means Pydantic calls it automatically whenever it constructs a model that has a phone number field. We don't control when or how it's called. Pydantic just passes the value and expects a value back.

## The Problem

This function runs in two very different contexts:

1. **API requests** - serving data to users. Bad phone numbers are expected (the source data might be messy). Logging every invalid number here would flood our logs with noise on every request.

2. **Data validation pipeline** - checking new data files before deploying them. Here we _want_ to see every invalid number so we can catch problems early.

Same function. Same Pydantic model. Two different needs.

## The Obvious Ideas (and Why They Don't Work)

My first thought was the simplest one: just add a parameter.

```python
def normalize_phone_number(value, log_errors=False):
    ...
```

But this function is a Pydantic `BeforeValidator`. Pydantic calls it with `(value)`. You can't pass extra arguments. The signature is fixed.

OK, what about a global flag?

```python
_log_errors = False

def enable_logging():
    global _log_errors
    _log_errors = True
```

This works if you only handle one request at a time. But in FastAPI, multiple requests run concurrently. If Request A sets the flag to `True`, Request B (running at the same time) will see it too - even though Request B never asked for logging.

Global variables are shared across all concurrent requests. That's not what we want.

What about two separate functions? One that logs, one that doesn't?

```python
def normalize_phone_number(value):
    ...  # no logging

def normalize_phone_number_with_logging(value):
    ...  # with logging
```

Now I'm duplicating the normalization logic. And since the function is bound to the Pydantic model through `BeforeValidator`, I'd need a separate model just for the validation pipeline. That's a lot of ceremony for a logging flag.

## Enter ContextVar

Python has a module called `contextvars` in the standard library (since 3.7). It gives you variables that are isolated per execution context. When you set a `ContextVar` value in one async task, other tasks running concurrently don't see it. Each gets its own copy.

Here's what I ended up with:

```python
from contextlib import contextmanager
from contextvars import ContextVar
from typing import Any
import logging

logger = logging.getLogger(__name__)

_validation_logging_enabled: ContextVar[bool] = ContextVar(
    "validation_logging_enabled", default=False
)

@contextmanager
def enable_validation_logging():
    token = _validation_logging_enabled.set(True)
    try:
        yield
    finally:
        _validation_logging_enabled.reset(token)

def log_validation_error_if_enabled(message: str, **kwargs: Any):
    if _validation_logging_enabled.get():
        logger.error(message, **kwargs)
```

That's the whole module. No dependencies beyond the standard library.

## How It Fits Together

The normalization function calls the conditional logger:

```python
def normalize_phone_number(value):
    ...
    try:
        parsed = phonenumbers.parse(value)
    except NumberParseException as e:
        log_validation_error_if_enabled(
            "Invalid phone number format", value=value, error=str(e)
        )
        return value
    ...
```

The data validation pipeline wraps its work in the context manager:

```python
def validate_incoming_data(data, schema):
    with enable_validation_logging():
        result = validate_records(data, model=schema)
    return result

def validate_records(data, model):
    """Check a each data record through a Pydantic model
    ...

```

That's it. Inside the `with` block, errors get logged. Outside of it (like during API requests), they don't. No parameters to thread through the call chain, no global state to worry about.

## The Token Pattern

One detail worth understanding: the `set()` / `reset(token)` pattern.

```python
token = _validation_logging_enabled.set(True)
# ... do work ...
_validation_logging_enabled.reset(token)
```

When you call `set()`, you get back a token. When you call `reset(token)`, it restores the _previous_ value. Not `False`. The previous value. This means if someone nests two `enable_validation_logging()` calls, it still works correctly. The inner one resets to `True` (what the outer one set), not to `False`.

Wrapping this in a `try/finally` means cleanup happens even if an exception is thrown.

Starting from Python 3.14, tokens can be used directly as context managers, so you can skip the wrapper entirely:

```python
with _validation_logging_enabled.set(True):
    result = validate_records(data, model=schema)
```

The `with` block calls `reset(token)` for you on exit. If you're on 3.14+, this removes the need for a custom context manager.

## Testing It

The tests verify both sides of the behavior. We use pytest's `caplog` fixture, which captures log records so we can assert whether a message was emitted:

```python
class TestLogPhoneNumberErrors:
    def test_no_logging_outside_context(self, caplog):
        normalize_phone_number("not a phone number")
        assert caplog.records == []

    def test_logs_error_inside_context(self, caplog):
        with enable_validation_logging():
            normalize_phone_number("not a phone number")
        assert "Invalid phone number format" in caplog.text

    def test_context_manager_resets_after_exit(self, caplog):
        with enable_validation_logging():
            pass
        normalize_phone_number("another invalid number")
        assert caplog.records == []
```

The default is `False` (silent). You opt in to logging. Existing code doesn't change behavior.

## The Trade-off

I'll be honest about the downside: this is "action at a distance." The normalization function reads a flag it didn't receive as a parameter. If you're reading the module for the first time, you might not realize that logging is controlled by something outside the function.

A good docstring helps. And for our use case (a simple boolean flag, two clear contexts), the trade-off is worth it. The alternative would be restructuring how Pydantic validation works just to pass a logging flag through.

## When to Reach for ContextVar

ContextVar is a good fit when:

- You need to pass context through layers you don't control (like Pydantic validators, middleware, or third-party callbacks)
- You're in an async environment where global state isn't safe
- The context is simple (a flag, a request ID, a correlation token)
- The context is for cross-cutting concerns (logging, tracing, monitoring), not business logic.

It's not the right tool for complex state management. If you need to collect errors into a list or build up rich context, there are better patterns. But for "should this code path behave slightly differently right now?" it's exactly right.

## Wrapping Up

The whole change was a small new module and a few test cases. No new dependencies. The data validation pipeline now catches bad phone numbers early, and API request logs stay clean.

Sometimes the right solution isn't a new library or a clever architecture. Sometimes it's a stdlib module you haven't used before.

Have you used ContextVar in your projects? I'd like to hear about your use cases in the comments.
