# Getting Started

## Package Imports

LLMBridge is designed to be imported as a proper Python package. The recommended import style is:

```python
# Proper package import (recommended)
from llmbridge import Model_Init, Model
from llmbridge import LLMBridgeFactory
from llmbridge import APIKeyManager, default_key_manager
from llmbridge import InputValidator, mask_sensitive_data
```

The legacy style (for backwards compatibility with the prototype) also works:

```python
# Legacy prototype import style
import sys
sys.path.append("src")
from main import Model_Init, Model
from providers import LLMBridgeFactory
```

## Basic Usage (HTTP-based providers)

```python
from llmbridge import Model_Init, Model

# Setup API keys
setup = Model_Init()
setup.add_api_key("openai", "sk-your-api-key")

# Create a model instance
model = Model("gpt-4.1-mini", "openai")

# Send a message
reply = model.message("Hello, how are you?")
print(reply)
```

## Advanced Usage (Hybrid Factory)

For providers that support official SDKs (Google, Anthropic, Groq, Mistral), use the `LLMBridgeFactory`.

### Google Gemini (SDK)
```python
from llmbridge import LLMBridgeFactory

# Automatically uses GEMINI_API_KEY from environment
client = LLMBridgeFactory.get_google_client()
response = client.models.generate_content(
    model="gemini-2.0-flash",
    contents="Explain AI in a few words"
)
print(response.text)
```

### Anthropic (SDK)
```python
from llmbridge import LLMBridgeFactory

client = LLMBridgeFactory.get_anthropic_client()
# Uses ANTHROPIC_API_KEY from environment
message = client.messages.create(
    model="claude-3-haiku",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Hello!"}]
)
print(message.content)
```

### Groq (SDK)
```python
from llmbridge import LLMBridgeFactory

client = LLMBridgeFactory.get_groq_client()
# Uses GROQ_API_KEY from environment
chat = client.chat.completions.create(
    messages=[{"role": "user", "content": "Hello!"}],
    model="llama-3.3-70b-versatile",
)
print(chat.choices[0].message.content)
```

### Mistral (SDK)
```python
from llmbridge import LLMBridgeFactory

# Get the Mistral client
client = LLMBridgeFactory.get_mistral_client()
# Uses MISTRAL_API_KEY from environment

# Or use the convenience method
response = LLMBridgeFactory.call_mistral(
    model="mistral-large",
    messages=[{"role": "user", "content": "Hello!"}]
)
print(response)
```

## Security Example

Always use the security utilities when handling sensitive data for logging:

```python
from llmbridge import mask_sensitive_data, default_key_manager

# Add API key securely
api_key = "sk-secret-api-key-1234567890"
default_key_manager.set_key("openai", api_key)

# Safe logging - masks sensitive data
config = {"api_key": api_key, "model": "gpt-4"}
print(mask_sensitive_data(config))
# Output: {"api_key": "sk-...890", "model": "gpt-4"}

# The Model class also uses mask_sensitive_data automatically in its logging
```

## Using APIKeyManager Directly

```python
from llmbridge import APIKeyManager, default_key_manager, InputValidator

# Register a custom provider
default_key_manager.register_provider(
    provider="custom",
    env_var="CUSTOM_API_KEY",
    base_url="https://api.custom.com/v1/chat/completions",
    auth_scheme="bearer"
)

# Set API key
default_key_manager.set_key("custom", "your-api-key")

# Send a message directly
response = default_key_manager.send_message(
    provider="custom",
    model="custom-model",
    message="Hello!"
)
print(response)

# Load from environment
default_key_manager.load_from_environment("custom")
```

## Requirements

- A valid provider API key.
- Network access for real provider requests.
- `requests` installed in the Python environment.
- Official SDKs (`google-genai`, `anthropic`, `groq`, `mistralai`) for SDK-based providers.

## Notes

- Provider/network failures surface as runtime exceptions with sensitive data masked
- Conversation history is stored in memory on the model instance using thread-local storage
- All inputs are validated using `InputValidator`
- Rate limiting is enforced by default using `RateLimiter`
