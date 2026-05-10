# Overview

## What LLMBridge is right now

LLMBridge currently exists in three parts:

- an active prototype repository with source files under `src/`
- a separate pip-oriented packaging repository
- this separate documentation repository

The active prototype code focuses on these main files:

- `API_manager.py` for provider metadata, API key management, and HTTP-based provider requests.
- `providers.py` which introduces the `LLMBridgeFactory` to handle diverse provider integrations (SDK-based and HTTP-based).
- `main.py` for `Model_Init`, `Model`, and in-memory conversation tracking.
- `security.py` for comprehensive security utilities including input validation, rate limiting, and sensitive data masking.
- `tools.py` is a placeholder for future expansion.

## What it can do

- Store provider API keys in memory or load them from environment variables.
- Support hybrid integrations: direct SDK usage for Google, Anthropic, Groq, and Mistral, and standard HTTP requests for OpenAI, Groq, and OpenRouter.
- Build and send chat-style requests with built-in conversation history tracking.
- Validate all inputs against security constraints.
- Enforce rate limiting on API requests.
- Detect and sanitize prompt injection attempts.
- Mask sensitive data in logs and error messages.
- Provide thread-safety via thread-local storage for conversation isolation.

## What it is not yet

- not yet a polished pip package in the active prototype repo
- not yet a stable public API
- not yet fully tested across real providers in public documentation
