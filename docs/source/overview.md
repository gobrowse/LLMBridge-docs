# Overview

## What LLMBridge is right now

LLMBridge currently exists in three parts:

- an active prototype repository with source files under `src/`
- a separate pip-oriented packaging repository
- this separate documentation repository

The active prototype code focuses on three main files:

- `API_manager.py` for legacy provider metadata and basic API key management.
- `providers.py` which introduces the `LLMBridgeFactory` to handle diverse provider integrations (SDK-based and HTTP-based).
- `main.py` for `Model_Init`, `Model`, and in-memory conversation tracking.

## What it can do

- Store provider API keys in memory or load them from environment variables.
- Support hybrid integrations: direct SDK usage for Google and Anthropic, and standard HTTP requests for OpenAI, Groq, and OpenRouter.
- Build and send chat-style requests with built-in conversation history tracking.

## What it is not yet

- not yet a polished pip package in the active prototype repo
- not yet a stable public API
- not yet fully tested across real providers in public documentation
