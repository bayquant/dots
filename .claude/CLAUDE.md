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

All Python files (`.py` files and `.ipynb` notebooks) should organize imports as follows:

# ----------------------------------------------------------------------------
# Imports
# ----------------------------------------------------------------------------
Separate imports with comments marking each section: `# Standard library imports`, `# Third-party imports`, `# First-party imports`, `# Local imports`. Ruff's isort integration automatically organizes and sorts alphabetically within each section. Add the section comments; isort will preserve them and maintain blank lines between sections.

**Import style by section:**
- **First-party imports**: Use absolute imports for other packages in the project (e.g., `from spread_sniper import ...`, `from mail.service import ...`).
- **Local imports**: Use relative imports for modules within the same package (e.g., `from .module import ...`, `from ..sibling import ...`).

For `.py` files, use the section blocks below. For notebooks, include imports in the first code cell after the autoreload directives.

# ----------------------------------------------------------------------------
# Globals and constants (for .py files)
# ----------------------------------------------------------------------------
Module-level __all__ and other constants. Only add constants that are used substantially across the code. Constants with a preceding _ should be in this section.

# ----------------------------------------------------------------------------
# General API (for .py files)
# ----------------------------------------------------------------------------
The public surface — classes and functions documented, and exported via __all__.              

# ----------------------------------------------------------------------------
# Private API (for .py files)
# ----------------------------------------------------------------------------
Implementation details prefixed with _. Not intended for external use.

`if __name__ == "__main__":` goes after the Private API section, at the very end of the file.

### Naming conventions

- Use `args` and `kwargs` for positional and keyword argument collections. Use `arg` and `kwarg` when iterating over them.

## Notebooks

Every notebook must include a title in a top-level markdown cell. The title should be in ALL-CAPS.

The first code cell (under the title) must always include:

```python
%load_ext autoreload
%autoreload 2
```

After these directives, add imports and configuration in the same cell, following the import ordering rules: separate with comments `# Standard library imports` and `# Other imports`, one import per line, sorted by absolute and relative style then alphabetically.

## Git Commits

When committing (requested by user), commit messages must use no capital letters whatsoever — the only exception is variable names or identifiers that are inherently capitalized (e.g., `MyClass`, `MY_CONSTANT`).