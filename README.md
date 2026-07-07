# lintro-pre-commit

[![PyPI](https://img.shields.io/pypi/v/lintro.svg)](https://pypi.org/project/lintro/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](./LICENSE)

A [pre-commit](https://pre-commit.com/) hook mirror for
[lintro](https://github.com/lgtm-hq/py-lintro).

Distributed as a standalone repository so the hook installs the published
`lintro` wheel from [PyPI](https://pypi.org/project/lintro/) instead of building
py-lintro from source. Hook environments provision in seconds, and the hook
`rev` is decoupled from py-lintro's library release tags — the same model
[astral-sh/ruff-pre-commit](https://github.com/astral-sh/ruff-pre-commit) uses.

## Usage

Add the following to your project's `.pre-commit-config.yaml`:

```yaml
repos:
  - repo: https://github.com/lgtm-hq/lintro-pre-commit
    rev: v0.69.0 # pin to a released tag
    hooks:
      - id: lintro-check
      # - id: lintro-format  # opt in for auto-formatting
```

Then install and run:

```bash
pip install pre-commit  # or: uv tool install pre-commit
pre-commit install
pre-commit run --all-files
```

## Available hooks

| Hook ID         | Runs             | Use it for                                    |
| --------------- | ---------------- | --------------------------------------------- |
| `lintro-check`  | `lintro check`   | Fail the commit when quality issues are found |
| `lintro-format` | `lintro format`  | Auto-fix formatting; re-stage and re-commit   |

Both hooks pass the staged filenames to lintro, so only the files you are
committing are inspected. lintro applies each underlying tool only to the file
types it supports, so a mixed set of staged files is fine.

## Hermetic vs. full-toolchain

These hooks use `language: python`: pre-commit builds an **isolated virtual
environment** and pip-installs the pinned `lintro` wheel. That environment
contains lintro and its Python-based tools, but **not** native, non-Python
binaries (for example `hadolint` or `shellcheck`). lintro gracefully skips any
tool that is not available, so those checks simply do not run under this hook.

If you rely on native tools, use py-lintro's in-repo hooks with
`language: system`, which reuse a full lintro installation (native tools
included) from your machine:

```yaml
repos:
  - repo: https://github.com/lgtm-hq/py-lintro
    rev: v0.69.0
    hooks:
      - id: lintro-check
```

See the
[py-lintro pre-commit guide](https://github.com/lgtm-hq/py-lintro/blob/main/docs/pre-commit.md)
for the full comparison.

## Versioning

The `rev:` tag matches the `lintro` version this mirror pins (see
[`pyproject.toml`](./pyproject.toml)). New tags are published automatically on
each py-lintro release; `pre-commit autoupdate` can bump `rev:` for you.

## License

[MIT](./LICENSE) — matching py-lintro.
