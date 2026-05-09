# Getting Started

AImgmt is currently in a prototype stage. The active working repository uses source files directly from `src/`.

## Current usage pattern

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

## Notes

- A valid provider API key is required.
- Network access is required for real provider calls.
- The main development repo and the pip-oriented repo are currently separate.
