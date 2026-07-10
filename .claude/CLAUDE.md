## Principles

1. Don’t assume. Don’t hide confusion. Surface tradeoffs.
2. Minimum code that solves the problem. Nothing speculative.
3. Touch only what you must. Clean up only your own mess.
4. Define success criteria. Loop until verified.

## Python Environment

Always use `uv` with the pip interface for Python operations. Run all `pip` commands as `uv pip` (e.g., `uv pip install`).

Before running any Python or pip command, check if a `.venv` exists in the current repository. If it does not exist, create one with `uv venv` and activate it with `source .venv/bin/activate`. Always ensure the virtual environment is activated before running any commands.

Never use plain `pip` or `python -m pip` directly.

## Code Style

Follow [PEP 8](https://peps.python.org/pep-0008/) throughout. Key rules:

- Do not use spaces to vertically align tokens on consecutive lines — prohibited by [PEP 8 § Whitespace in Expressions and Statements](https://peps.python.org/pep-0008/#whitespace-in-expressions-and-statements).
- Use `#` for all comments, including multi-line blocks. Reserve `"""` for docstrings on modules, classes, and functions only ([PEP 257](https://peps.python.org/pep-0257/)).
- Use `X | None` instead of `Optional[X]` for nullable types ([PEP 604](https://peps.python.org/pep-0604/)). `Optional` from `typing` is not needed in Python 3.10+.

All `.py` files (even if sections are empty) should contain the following blocks:

# ----------------------------------------------------------------------------
# Imports
# ----------------------------------------------------------------------------
Separate imports with comments # Standard library imports and # Other imports. Should import one item per line. Sort by absolute and relative style and then alphabetically.

# ----------------------------------------------------------------------------
# Globals and constants
# ----------------------------------------------------------------------------
Module-level __all__ and other constants. Only add constants that are used substantially across the code. Constants with a preceding _ should be in this section.

# ----------------------------------------------------------------------------
# General API
# ----------------------------------------------------------------------------
The public surface — classes and functions documented, and exported via __all__.              

# ----------------------------------------------------------------------------
# Private API
# ----------------------------------------------------------------------------
Implementation details prefixed with _. Not intended for external use.

`if __name__ == "__main__":` goes after the Private API section, at the very end of the file.

### Naming conventions

- Use `args` and `kwargs` for positional and keyword argument collections. Use `arg` and `kwarg` when iterating over them.

## Git Commits

When committing (requested by user), commit messages must use no capital letters whatsoever — the only exception is variable names or identifiers that are inherently capitalized (e.g., `MyClass`, `MY_CONSTANT`).