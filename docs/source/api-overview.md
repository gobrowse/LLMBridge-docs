# API Overview

## `Model_Init`

`Model_Init` is a minimal setup helper.

Current responsibility:
- add provider API keys through `add_api_key(provider, key)` with validation

## `Model`

`Model` stores:
- selected model name
- selected provider name
- optional tool flags
- system prompt text
- in-memory conversation state using **thread-local storage** for isolation

Current methods:
- `message(message)` sends a provider request and stores the reply in local conversation state
- `model_info(provider=None)` returns a small status dictionary (API keys are never exposed)
- `compress_conversation()` returns the stored conversation as joined text with sensitive data masked

**Thread Safety:** Uses thread-local storage via `threading.local` to prevent cross-contamination between threads. Each thread maintains its own isolated conversation state.

## `LLMBridgeFactory`

The `LLMBridgeFactory` in `providers.py` is the primary interface for diverse provider integrations.

Current responsibilities:
- `get_google_client()`: Returns an official Google GenAI client with secure key management.
- `get_anthropic_client()`: Returns an official Anthropic client with secure key management.
- `get_groq_client()`: Returns an official Groq client with secure key management.
- `get_mistral_client()`: Returns an official Mistral client using the Mistral SDK with secure key management.
- `call_mistral(model, messages, api_key=None)`: Call Mistral using the official SDK with full security checks.
- `call_http_provider()`: A helper method that bridges to the legacy `APIKeyManager` for standard HTTP-based provider calls.

## `APIKeyManager`

`APIKeyManager` (in `API_manager.py`) is the engine for HTTP-based provider interactions.

Current responsibilities:
- Manage API keys and provider metadata.
- Register new providers via `register_provider()` with full validation.
- Build standard OpenAI-compatible request payloads and headers.
- Handle responses for legacy HTTP-based providers like OpenAI and OpenRouter.
- Send messages via `send_message()` with security checks.
- Load API keys from environment variables via `load_from_environment()`.
- Rate limiting enforcement using `RateLimiter`.
- Authorization hooks for custom validation via `set_auth_hook()`.

## `ProviderConfig`

`ProviderConfig` is a dataclass for provider configuration with validation on creation.

## Security Classes

### `SecurityConfig`

Global security configuration class with constants for validation patterns, size limits, rate limiting defaults, and sensitive data detection.

**Key constants:**
- `ALLOWED_PROVIDER_PATTERN`, `ALLOWED_MODEL_PATTERN`, `ALLOWED_KEY_PATTERN`
- `MAX_MESSAGE_LENGTH`, `MAX_PROMPT_LENGTH`, `MAX_RESPONSE_LENGTH`
- `DEFAULT_RATE_LIMIT`, `DEFAULT_BURST_LIMIT`
- `INJECTION_PATTERNS`, `SENSITIVE_KEYWORDS`

**Methods:**
- `mask_api_key(key)`: Masks API keys for logging
- `is_sensitive_env_var(name)`: Checks if an environment variable name is sensitive

### `RateLimiter`

Token bucket implementation for rate limiting API requests.

**Methods:**
- `acquire(tokens=1)`: Non-blocking token acquisition
- `wait_for_token(tokens=1, timeout=60.0)`: Blocking token acquisition with timeout

### `InputValidator`

Static methods for input validation:

- `validate_provider_name(provider)`
- `validate_model_name(model)`
- `validate_api_key(key)`
- `validate_message(message, max_length)`
- `validate_system_prompt(prompt, max_length)`
- `validate_response(response, max_length)`
- `detect_prompt_injection(message)`: Returns list of matched injection patterns
- `sanitize_message(message, remove_injections=False)`: Sanitizes message content

## Security Functions

### `mask_sensitive_data(data, depth=0, max_depth=5)`

Recursively masks sensitive data in nested structures. Masks API keys, tokens, passwords, and other sensitive values. Also masks dictionary keys and environment variable names that match sensitive keywords.

### `secure_exception_handler(func)`

Decorator to ensure exceptions don't leak sensitive data. Intercepts `ValueError`, `KeyError`, and `RuntimeError` exceptions and masks sensitive data in their arguments before re-raising.
