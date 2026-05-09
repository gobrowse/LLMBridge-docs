# Getting Started

## Current prototype usage

The active prototype repo is still used directly from `src/`.

```python
import sys

sys.path.append("src")

from main import Model_Init, Model

setup = Model_Init()
setup.add_api_key("openai", "sk-example")

model = Model("gpt-4.1-mini", "openai")
reply = model.message("hello")

print(reply)
print(model.model_info())
```

## Requirements

- a valid provider API key
- network access for real provider requests
- `requests` installed in the Python environment

## Notes

- imports are still source-file based in the prototype repo
- provider/network failures currently surface as runtime exceptions
- conversation history is stored in memory on the model instance
