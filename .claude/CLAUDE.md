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

- All .py files (even if sections are empty) should contain the following blocks:

#-----------------------------------------------------------------------------
# Imports
#-----------------------------------------------------------------------------
Separate imports with comments # Standard library imports and # Other imports. Should import one item per line. Sort by absolute and relative style and then alphabetically.

#-----------------------------------------------------------------------------
# Globals and constants
#-----------------------------------------------------------------------------
Module-level __all__ and other constants. Only add constants that are used substantially across the code. Constants with a preceding _ should be in this section.

#-----------------------------------------------------------------------------
# General API
#-----------------------------------------------------------------------------
The public surface — classes and functions documented, and exported via __all__.              

#-----------------------------------------------------------------------------
# Private API
#-----------------------------------------------------------------------------
Implementation details prefixed with _. Not intended for external use.

- Use `#` for all comments, including multi-line blocks (PEP 8). Reserve `"""` for docstrings on modules, classes, and functions (PEP 257).

### Naming conventions

- Use `args` and `kwargs` for positional and keyword argument collections. Use `arg` and `kwarg` when iterating over them.