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

3. **Ask the user to select.** `AskUserQuestion` allows at most 4 options
   per question and at most 4 questions per call — plan around that
   instead of passing every folder as one question's options.

   - **4 or fewer folders:** call `AskUserQuestion` once with
     `multiSelect: true` and one option per folder (label = folder name).
     Question: "Which folders should this instruction apply to?" Show the
     instruction text in the question so the user has context.

   - **More than 4 folders:** first ask a single question with options
     `"All folders"` / `"Let me pick a subset"` (not multiSelect). If the
     user picks "All folders", treat every folder from step 2 as selected
     and skip the rest of this step.

     Otherwise, split the folder list into chunks of up to 4 and ask one
     `multiSelect: true` question per chunk (label = folder name), e.g.
     "Which of these should this instruction apply to? (batch 1 of 3)".
     Up to 4 such questions fit in a single `AskUserQuestion` call — batch
     that many chunks together per call. If there are more chunks than
     that (more than 16 folders), issue additional `AskUserQuestion` calls
     for the remaining chunks. Union the selections across every batch
     into the final set of scoped folders.

4. **Execute the instruction**, treating only the selected subfolders as
   in scope: read and edit files inside them freely, but don't read or
   edit files outside them (including other subfolders and files sitting
   directly in the current folder) unless completing the instruction is
   impossible without doing so — in that case, stop and tell the user
   what's blocking it rather than silently expanding scope.

5. This is a one-shot scoping for the current instruction only — nothing
   is written to disk about the selection, and it does not affect scope
   for any other prompt in the conversation.
