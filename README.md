# dots
Configuration files

## .claude/

Claude Code configuration. Place this directory at the root of any project
to give Claude project-specific instructions.

- **`CLAUDE.md`** — project-level instructions Claude reads automatically
  (currently: Python environment conventions). Can also live at
  `~/.claude/CLAUDE.md` (global) or in a subdirectory (scoped to that
  subtree); project-level takes precedence.
- **`agents/`** — custom subagent definitions (`software-engineer`,
  `quantitative-researcher`, `data-engineer`), invocable via the `Agent`
  tool.
- **`skills/`** — custom skills invocable as slash commands
  (`scope-folders`, for running an instruction scoped to a user-picked
  subset of subfolders).
- **`writing-style.md`** — shared writing-style guidance, referenced from
  `CLAUDE.md`.

## .vscode/settings.json

VS Code workspace settings for Python development, pointing at a `.venv`
in the parent directory of the workspace.
