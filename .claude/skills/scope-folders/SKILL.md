---
name: scope-folders
description: Run a given instruction scoped to a user-picked subset of subfolders in the current folder — lists the subfolders, lets the user multi-select which ones the instruction applies to, then executes it against only those.
---

# Scope Folders

Usage: `/scope-folders <instruction>`

Lets the user restrict a single instruction to a subset of subfolders in
the current folder, chosen interactively, instead of Claude guessing
scope from the prompt alone.

## Steps

1. **Get the instruction.** Use the text passed as the skill's argument.
   If none was given, ask the user what instruction they want to run
   before doing anything else.

2. **List candidate folders.** From the current folder (the directory the
   session was opened in — use `pwd`, don't assume the cwd of this skill
   file), run:

   ```
   find . -maxdepth 1 -mindepth 1 -type d -not -path './.git' -not -name '.*' | sed 's|^\./||' | sort
   ```

   If this returns no folders, tell the user there's nothing to scope to
   and ask whether to just run the instruction unscoped.

3. **Ask the user to select.** Call `AskUserQuestion` with
   `multiSelect: true`, one option per folder found in step 2 (label =
   folder name). Question: "Which folders should this instruction apply
   to?" Show the instruction text in the question so the user has context
   for what they're scoping.

4. **Execute the instruction**, treating only the selected subfolders as
   in scope: read and edit files inside them freely, but don't read or
   edit files outside them (including other subfolders and files sitting
   directly in the current folder) unless completing the instruction is
   impossible without doing so — in that case, stop and tell the user
   what's blocking it rather than silently expanding scope.

5. This is a one-shot scoping for the current instruction only — nothing
   is written to disk about the selection, and it does not affect scope
   for any other prompt in the conversation.
