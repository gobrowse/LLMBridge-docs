# API Overview

## `Model_Init`

`Model_Init` is a minimal setup helper.

Current responsibility:
- add provider API keys through `add_api_key(provider, key)`

## `Model`

`Model` stores:
- selected model name
- selected provider name
- optional tool flags
- system prompt text
- in-memory conversation state

Current methods:
- `message(message)` sends a provider request and stores the reply in local conversation state
- `model_info(provider=None)` returns a small status dictionary
- `compress_conversation()` returns the stored conversation as joined text

## `LLMBridgeFactory`

The `LLMBridgeFactory` in `providers.py` is the new primary interface for diverse provider integrations.

Current responsibilities:
- `get_google_client()`: Returns an official Google GenAI client.
- `get_anthropic_client()`: Returns an official Anthropic client.
- `get_groq_client()`: Returns an official Groq client.
- `call_http_provider()`: A helper method that bridges to the legacy `APIKeyManager` for standard HTTP-based provider calls.

## `APIKeyManager`

`APIKeyManager` (in `API_manager.py`) remains the engine for HTTP-based provider interactions.

Current responsibilities:
- Manage API keys and provider metadata.
- Build standard OpenAI-compatible request payloads and headers.
- Handle responses for legacy HTTP-based providers like OpenAI and OpenRouter.
