# Overview

## What AImgmt is right now

AImgmt currently exists in three parts:

- an active prototype repository with source files under `src/`
- a separate pip-oriented packaging repository
- this separate documentation repository

The active prototype code focuses on two main files:

- `API_manager.py` for provider metadata, API key storage, request building, and response parsing
- `main.py` for `Model_Init`, `Model`, and in-memory conversation tracking

## What it can do

- store provider API keys in memory
- load provider API keys from environment variables
- build and send simple chat-style requests to supported providers
- keep basic conversation history and resend it on later calls

## What it is not yet

- not yet a polished pip package in the active prototype repo
- not yet a stable public API
- not yet fully tested across real providers in public documentation
