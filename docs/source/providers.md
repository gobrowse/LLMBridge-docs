# Supported Providers

The current prototype contains built-in metadata for these providers:

- `openai` (HTTP)
- `anthropic` (SDK/HTTP)
- `google` (SDK - Gemini)
- `groq` (SDK/HTTP)
- `mistral` (HTTP - legacy/direct)
- `openrouter` (HTTP)

## Hybrid Support

LLMBridge supports both direct HTTP requests (via `APIKeyManager`) and official SDK clients (via `LLMBridgeFactory`). SDK-based providers often provide more robust features like streaming and better error handling.

## Environment variable examples

- `OPENAI_API_KEY`
- `ANTHROPIC_API_KEY`
- `GEMINI_API_KEY` (for Google)
- `GROQ_API_KEY`
- `MISTRAL_API_KEY`
- `OPENROUTER_API_KEY`
