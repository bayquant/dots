# dots
Configuration files

## .vscode/settings.json

VS Code workspace settings for Python development. Targets a virtual environment named `.venv` located in the parent directory of the workspace (`../.venv`).

| Setting | Value | Description |
|---|---|---|
| `python.defaultInterpreterPath` | `../.venv/bin/python` | Python interpreter inside the virtual environment (Windows: `Scripts/python.exe`) |
| `python.terminal.activateEnvironment` | `true` | Auto-activates the virtual environment in the integrated terminal |
| `VIRTUAL_ENV` | `../.venv` | Marks the active virtual environment for tools like pip |
| `PATH` | `../.venv/Scripts;$PATH` | Prepends venv executables so they take precedence |
| `PYTHONPATH` | `${workspaceFolder}` | Allows imports from the project root without installing the package |
