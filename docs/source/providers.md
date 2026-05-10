# Supported Providers

The current prototype contains built-in metadata for these providers:

- `openai` (HTTP)
- `anthropic` (SDK/HTTP)
- `google` (SDK - Gemini)
- `groq` (SDK/HTTP)
- `mistral` (SDK/HTTP)
- `openrouter` (HTTP)

## Hybrid Support

LLMBridge supports both direct HTTP requests (via `APIKeyManager`) and official SDK clients (via `LLMBridgeFactory`). 

**SDK-based providers** use official Python libraries and provide access to advanced features like streaming and provider-specific functionality. The `LLMBridgeFactory` includes:
- `get_google_client()` - Google GenAI SDK
- `get_anthropic_client()` - Anthropic SDK
- `get_groq_client()` - Groq SDK
- `get_mistral_client()` - Mistral SDK

**HTTP-based providers** use standard REST API calls through `APIKeyManager` for compatibility.

**Mistral** supports both SDK and HTTP modes:
- Use `get_mistral_client()` for the official Mistral SDK
- Use `call_mistral()` for a convenience method with the SDK
- Use `APIKeyManager` for HTTP-based calls

## Environment variable examples

- `OPENAI_API_KEY`
- `ANTHROPIC_API_KEY`
- `GEMINI_API_KEY` (for Google)
- `GROQ_API_KEY`
- `MISTRAL_API_KEY`
- `OPENROUTER_API_KEY`

All environment variables are automatically loaded by `APIKeyManager.load_from_environment()` and are validated using `InputValidator`. Sensitive variable names are masked in error messages.
