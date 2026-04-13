---
tags: [project, moc, demo]
status: active
created: 2026-04-13
---

# Demo CLI Tool — Project Index

> **Đây là demo project minh họa vault context cho CLI/backend projects.**
>
> Claude đọc file này để hiểu tech stack, architecture, và focus hiện tại.

## Overview

Example CLI tool for processing markdown files with frontmatter parsing.

## Status

- **Phase:** MVP
- **Priority:** Low
- **Last Active:** 2026-04-13

## Tech Stack

- **Language:** Python 3.11+
- **CLI Framework:** Typer
- **Testing:** pytest
- **Package Manager:** uv
- **Distribution:** PyPI

## Key Links

- **Repo:** https://github.com/example/demo-cli-tool
- **Decisions:** [[memory/decisions-log]]
- **Related Wiki:** [[wiki/patterns/testing]]

## Architecture

```
src/
├── cli.py            # Typer entry point
├── parser.py         # Frontmatter parser
├── transformer.py    # Content transformer
└── utils/            # Helpers
tests/
└── test_parser.py
```

## Key Files

- `src/cli.py` — Main CLI
- `pyproject.toml` — Package config
- `.github/workflows/release.yml` — Auto-publish to PyPI

## Active Focus

- Add `--output-format json|yaml` flag
- Improve error messages for malformed frontmatter
- Set up CI/CD pipeline
