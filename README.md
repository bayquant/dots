# dots
Configuration files

## .claude/

Claude Code configuration for this repo. Place this directory at the root of any project to give Claude project-specific instructions.

### `.claude/CLAUDE.md`

Project-level instructions that Claude reads automatically when working inside this repo. Currently configures the Python environment:

- All `pip` commands must be run as `uv pip`
- A `.venv` is created with `uv venv` if one doesn't exist, then activated before any Python/pip command
- Plain `pip` or `python -m pip` are never used directly

**Where to put `CLAUDE.md`:**

| Location | Scope |
|---|---|
| `~/.claude/CLAUDE.md` | Global — applies to every project |
| `<repo>/.claude/CLAUDE.md` | Project — applies only inside that repo |
| `<subdir>/CLAUDE.md` | Subtree — applies when working inside that subdirectory |

Project-level files take precedence over global ones. Both can coexist.

## .vscode/settings.json

VS Code workspace settings for Python development. Targets a virtual environment named `.venv` located in the parent directory of the workspace (`../.venv`).

| Setting | Value | Description |
|---|---|---|
| `python.defaultInterpreterPath` | `../.venv/bin/python` | Python interpreter inside the virtual environment (Windows: `Scripts/python.exe`) |
| `python.terminal.activateEnvironment` | `true` | Auto-activates the virtual environment in the integrated terminal |
| `VIRTUAL_ENV` | `../.venv` | Marks the active virtual environment for tools like pip |
| `PATH` | `../.venv/Scripts;$PATH` | Prepends venv executables so they take precedence |
| `PYTHONPATH` | `${workspaceFolder}` | Allows imports from the project root without installing the package |
