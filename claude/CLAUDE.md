# Python Environment

Always use `uv` with the pip interface for Python operations. Run all `pip` commands as `uv pip` (e.g., `uv pip install`).

Before running any Python or pip command, check if a `.venv` exists in the current repository. If it does not exist, create one with `uv venv` and activate it with `source .venv/bin/activate`. Always ensure the virtual environment is activated before running any commands.

Never use plain `pip` or `python -m pip` directly.
