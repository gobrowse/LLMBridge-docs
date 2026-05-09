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

## `APIKeyManager`

`APIKeyManager` currently handles:
- provider registration
- API key storage
- environment-variable loading
- auth header generation
- provider request payload building
- provider response parsing
