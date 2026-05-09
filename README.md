# AImgmt Documentation

This repository contains the standalone documentation site for AImgmt.

## Purpose

This docs repo is separate from the active prototype repo and the pip-oriented packaging repo. Its job is to describe the project clearly as it exists today, without pretending the library is more stable or more packaged than it is.

## Local build

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
sphinx-build -b html docs/source docs/build/html
```

## Read the Docs

This repository is configured for Read the Docs using `.readthedocs.yaml`.
