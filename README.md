# AImgmt Documentation

This repository contains the standalone documentation site for the AImgmt project.

## Local build

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
sphinx-build -b html docs/source docs/build/html
```
