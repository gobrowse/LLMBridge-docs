# Security

LLMBridge includes a comprehensive security module (`security.py`) that provides input validation, rate limiting, sensitive data masking, and prompt injection protection for all provider interactions.

## Configuration

### `SecurityConfig` Class

Global security configuration with constants used throughout the library:

**Input Validation Patterns:**
- `ALLOWED_PROVIDER_PATTERN`: Regex for valid provider names (`^[a-zA-Z0-9_-]{1,64}$`)
- `ALLOWED_MODEL_PATTERN`: Regex for valid model names (`^[a-zA-Z0-9-_:./]{1,128}$`)
- `ALLOWED_KEY_PATTERN`: Regex for valid API key formats (`^[a-zA-Z0-9_\-\.]{1,4096}$`)

**Size Limits:**
- `MAX_MESSAGE_LENGTH`: 100,000 characters
- `MAX_PROMPT_LENGTH`: 10,000 characters
- `MAX_CONVERSATION_LENGTH`: 100 messages
- `MAX_RESPONSE_LENGTH`: 100,000 characters

**Rate Limiting Defaults:**
- `DEFAULT_RATE_LIMIT`: 100 requests per minute
- `DEFAULT_BURST_LIMIT`: 10 concurrent requests

**Network Settings:**
- `SSL_VERIFY`: Always `True` (cannot be disabled)
- `SSL_TIMEOUT`: 30 seconds
- `DEFAULT_TIMEOUT`: 30 seconds
- `MAX_TIMEOUT`: 300 seconds maximum

**Security Patterns:**
- `INJECTION_PATTERNS`: List of regex patterns for detecting prompt injection attempts
- `SENSITIVE_KEYWORDS`: List of keywords for identifying sensitive data (api_key, secret, password, token, etc.)

**Class Methods:**
- `mask_api_key(key)`: Masks an API key, showing only first 4 and last 4 characters
- `is_sensitive_env_var(name)`: Checks if a string looks like a sensitive environment variable name

## Rate Limiting

### `RateLimiter` Class

Token bucket implementation for API request rate limiting:

```python
from llmbridge import RateLimiter

# Create with custom settings
limiter = RateLimiter(rate=60, burst=5)  # 60 requests/min, max 5 concurrent

# Acquire a token (non-blocking)
if limiter.acquire():
    # Proceed with request
    pass
else:
    # Rate limited
    pass

# Wait for a token (blocking with timeout)
success = limiter.wait_for_token(timeout=30.0)  # Wait up to 30 seconds
```

**Methods:**
- `acquire(tokens=1)`: Attempt to acquire tokens immediately. Returns `True` if successful.
- `wait_for_token(tokens=1, timeout=60.0)`: Block until tokens are available or timeout is reached.

The `APIKeyManager` and `LLMBridgeFactory` both use `RateLimiter` instances to enforce rate limits on provider requests.

## Input Validation

### `InputValidator` Class

Static methods for validating all inputs to the library:

**Provider and Model Validation:**
- `validate_provider_name(provider)`: Validates and normalizes provider names
- `validate_model_name(model)`: Validates model names against size and pattern constraints

**API Key Validation:**
- `validate_api_key(key)`: Validates API key format and length

**Message Validation:**
- `validate_message(message, max_length=MAX_MESSAGE_LENGTH)`: Validates user message content
- `validate_system_prompt(prompt, max_length=MAX_PROMPT_LENGTH)`: Validates system prompt content
- `validate_response(response, max_length=MAX_RESPONSE_LENGTH)`: Validates response size

**Prompt Injection Detection:**
- `detect_prompt_injection(message)`: Returns list of matched injection patterns found in message
- `sanitize_message(message, remove_injections=False)`: Sanitizes message content, optionally removing detected injection patterns

Example usage:
```python
from llmbridge import InputValidator

# Validate inputs before use
provider = InputValidator.validate_provider_name("openai")
model = InputValidator.validate_model_name("gpt-4")

# Check for prompt injection
injections = InputValidator.detect_prompt_injection(user_message)
if injections:
    # Handle or sanitize
    clean_message = InputValidator.sanitize_message(user_message, remove_injections=True)
```

The injection patterns detect common attacks including:
- System/assistant role injection
- Instruction override attempts
- Memory wipe attempts
- Role redefinition
- Conversation manipulation
- System message delimiter injection

## Sensitive Data Masking

### `mask_sensitive_data()` Function

Recursively masks sensitive data in nested structures (dicts, lists, tuples):

```python
from llmbridge import mask_sensitive_data

# Mask API keys and other sensitive values
config = {"api_key": "sk-1234567890abcdef", "model": "gpt-4"}
masked = mask_sensitive_data(config)
# Result: {"api_key": "sk-...cdef", "model": "gpt-4"}

# Works recursively with nested structures
data = {
    "provider": "openai",
    "credentials": {"api_key": "sk-secret", "token": "xyz"},
    "settings": {"timeout": 30}
}
masked = mask_sensitive_data(data)
# Keys containing sensitive keywords are also masked
```

The function:
- Masks long alphanumeric strings that look like API keys
- Masks values containing sensitive keywords
- Masks dictionary keys containing sensitive keywords
- Handles nested structures up to `max_depth=5`
- Masks sensitive environment variable names

### `secure_exception_handler()` Decorator

Decorator to ensure exceptions don't leak sensitive data:

```python
from llmbridge import secure_exception_handler

@secure_exception_handler
def handle_request(api_key, message):
    # If this raises an exception with api_key in the message,
    # the decorator will mask it before re-raising
    pass
```

This decorator intercepts `ValueError`, `KeyError`, and `RuntimeError` exceptions and masks any sensitive data in their arguments before re-raising.

## Usage in the Library

All major components use the security module:

- **`APIKeyManager`**: Uses `InputValidator` for all input validation, `RateLimiter` for rate limiting, `mask_sensitive_data()` for safe logging, and `secure_exception_handler` decorator on `send_message()`
- **`LLMBridgeFactory`**: Uses `InputValidator` for validation and `RateLimiter` for SDK client rate limiting
- **`Model`**: Uses `InputValidator` for validation, `mask_sensitive_data()` for logging, and thread-local storage for conversation isolation
- **`ProviderConfig`**: Validates configuration on creation using `InputValidator`
