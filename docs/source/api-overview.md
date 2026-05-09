# API Overview

## `Model_Init`

`Model_Init` is responsible for registering provider API keys.

## `Model`

`Model` stores provider and model selection, tracks conversation history in memory, and sends chat-style requests through the API manager.

## `APIKeyManager`

`APIKeyManager` stores provider metadata, loads keys from environment variables, builds headers, and sends provider requests.
