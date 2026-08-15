## Principles

1. Don’t assume. Don’t hide confusion. Surface tradeoffs.
2. Minimum code that solves the problem. Nothing speculative.
3. Touch only what you must. Clean up only your own mess.
4. Define success criteria. Loop until verified.

## Writing Style

@.claude/writing-style.md

## Python Environment

Always use `uv` with the pip interface for Python operations. Run all `pip` commands as `uv pip` (e.g., `uv pip install`).

Before running any Python or pip command, check if a `.venv` exists in the current repository. If it does not exist, create one with `uv venv` and activate it with `source .venv/bin/activate`. Always ensure the virtual environment is activated before running any commands.

Never use plain `pip` or `python -m pip` directly.

## Code Standards

### PEP 8

Follow [PEP 8](https://peps.python.org/pep-0008/) throughout. Key rules:

- Do not use spaces to vertically align tokens on consecutive lines — prohibited by [PEP 8 § Whitespace in Expressions and Statements](https://peps.python.org/pep-0008/#whitespace-in-expressions-and-statements).
- Use `#` for all comments, including multi-line blocks. Reserve `"""` for docstrings on modules, classes, and functions only ([PEP 257](https://peps.python.org/pep-0257/)).
- Use `X | None` instead of `Optional[X]` for nullable types ([PEP 604](https://peps.python.org/pep-0604/)). `Optional` from `typing` is not needed in Python 3.10+.

### File structure

All Python files (`.py` files and `.ipynb` notebooks) should organize imports as follows:

```python
# ----------------------------------------------------------------------------
# Imports
# ----------------------------------------------------------------------------
```
Separate imports with comments marking each section, in this order:

1. `# Future imports`
2. `# Standard library imports`
3. `# Third-party imports`
4. `# First-party imports`
5. `# Local imports`

Add the section comments; maintain blank lines between sections, and within each section:

- put all import x lines before any from x import y lines
- alphabetize each of those two groups by module name
- alphabetize the names listed after import in each from line
- one import per line — never combine multiple modules or names on a single `import` or `from` statement

Import style by section:

| Section | Style | Example |
|---|---|---|
| First-party imports | Absolute imports for other packages in the project | `from spread_sniper import ...`, `from mail.service import ...` |
| Local imports | Relative imports for modules within the same package | `from .module import ...`, `from ..sibling import ...` |

For `.py` files only, use the section blocks below.

```python
# ----------------------------------------------------------------------------
# Globals and constants (for .py files)
# ----------------------------------------------------------------------------
```
Module-level `__all__` and other constants. Only pull a value out into a module-level constant if it's reused substantially across the code — a value used once should stay inline, not be promoted here. Constants with a preceding `_` should be in this section.

```python
# ----------------------------------------------------------------------------
# General API (for .py files)
# ----------------------------------------------------------------------------
```
The public surface — classes and functions documented, and exported via __all__.              

```python
# ----------------------------------------------------------------------------
# Private API (for .py files)
# ----------------------------------------------------------------------------
```
Private members (leading `_`), excluded from `__all__`.

`if __name__ == "__main__":` goes after the Private API section, at the very end of the file.

### Naming conventions

- Use `args`/`kwargs` for collections, `arg`/`kwarg` when iterating.
- Use `start`/`end` for range bounds (dates, indices, versions, …).
- Suffix a variable with `_dir` if it holds a directory (e.g. `dest_dir = "/data/output"`), or `_path` if it holds a specific file (e.g. `config_path = "/data/output/config.yaml"`). Don't use bare `dir` — it shadows the builtin.

### Dates and times

`datetime.datetime` is the common denominator across stdlib, pandas, polars, and API/JSON boundaries. Use it for scalars, function signatures, config, and dataclasses. Always timezone-aware, in UTC; convert to local/exchange time only at display time.

Inside a DataFrame, let the library store its native vectorized type (`pd.Timestamp`/`datetime64[ns]` for pandas, `pl.Datetime` for polars) — that's unavoidable and fine for bulk/vectorized data. Don't type function signatures as `pd.Timestamp` unless the value is guaranteed to live inside a DataFrame; that couples interfaces to pandas unnecessarily and breaks when data crosses into polars or plain Python.

## Notebooks

Every notebook must include a title in a top-level markdown cell. The title should be in ALL-CAPS.

The first code cell (under the title) must always include:

```python
%load_ext autoreload
%autoreload 2
```

After these directives, add imports and configuration in the same cell, following the [import ordering rules](#file-structure).

## Git Commits

When committing (requested by user), commit messages must use no capital letters whatsoever — the only exception is variable names or identifiers that are inherently capitalized (e.g., `MyClass`, `MY_CONSTANT`).