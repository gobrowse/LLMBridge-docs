# Getting Started

## Current prototype usage (Legacy)

The original prototype interface is still supported for HTTP-based providers.

```python
import sys
sys.path.append("src")
from main import Model_Init, Model

setup = Model_Init()
setup.add_api_key("openai", "sk-example")

model = Model("gpt-4.1-mini", "openai")
reply = model.message("hello")
print(reply)
```

## Advanced Usage (Hybrid Factory)

For providers that support official SDKs (Google, Anthropic, Groq), use the `LLMBridgeFactory`.

### Google Gemini (SDK)
```python
import sys
sys.path.append("src")
from providers import LLMBridgeFactory

# Automatically uses GEMINI_API_KEY from environment
client = LLMBridgeFactory.get_google_client()
response = client.models.generate_content(
    model="gemini-2.0-flash",
    contents="Explain AI in a few words"
)
print(response.text)
```

### Groq (SDK)
```python
from providers import LLMBridgeFactory
client = LLMBridgeFactory.get_groq_client()
# Uses GROQ_API_KEY from environment
chat = client.chat.completions.create(
    messages=[{"role": "user", "content": "Hello!"}],
    model="llama-3.3-70b-versatile",
)
print(chat.choices[0].message.content)
```

## Requirements

- A valid provider API key.
- Network access for real provider requests.
- `requests` installed in the Python environment.
- Official SDKs (`google-genai`, `anthropic`, `groq`) for SDK-based providers.

## Notes

- imports are still source-file based in the prototype repo
- provider/network failures currently surface as runtime exceptions
- conversation history is stored in memory on the model instance
