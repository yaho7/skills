---
name: atomic-commit-guard
description: Enforce Git-protected development by grouping work into feature-sized atomic units, verifying each unit, and committing after each completed unit so the user can safely roll back through Git history. Use when the user asks for atomic commits, source protection, Git safety, automatic commits, safe AI iteration, feature-by-feature implementation, or wants Codex to initialize Git when missing and preserve rollback points without waiting for confirmation between units.
---

# Atomic Commit Guard

## Core Mission

Help the user iterate quickly while preserving rollback safety through Git history. Treat each feature or coherent functional unit as an independently understandable checkpoint: implement it, verify it when feasible, commit it, then continue to the next unit.

## Operating Rules

1. Before editing, check whether the workspace is inside a Git repository.
2. If no Git repository exists, initialize one with `git init` before making changes.
3. Split work by feature or functional unit, not by tiny mechanical edits.
4. Keep each unit coherent enough to review and revert as one commit.
5. Complete one unit, run focused verification when feasible, then create a Git commit for that unit. For small projects, a scoped compile or build may be the appropriate verification; follow the active verification policy for larger projects.
6. Continue to the next unit after the commit succeeds; do not wait for user confirmation between units.
7. Do not bundle unrelated features, opportunistic refactors, or broad cleanup into the same commit.
8. Never stage unrelated pre-existing user changes unless the user explicitly asks.

## Branch Policy

- Default to the existing checkout and current branch. Working on a new task or making atomic commits does not by itself require a new branch or worktree.
- Work on `main` when the user authorizes it and repository rules permit it. Do not create a feature branch merely as a routine safety step.
- Create a branch or worktree only when the user requests one, repository rules require a pull request, or concrete isolation is needed for conflicting or concurrent work. Reuse a suitable existing branch before creating another.
- Follow the user's chosen integration target. Merge and push when authorized; preserve existing work and never force-push without explicit authorization.

## Workflow

### 1. Establish Git Safety

Run a lightweight Git status check before editing:

- If `git rev-parse --is-inside-work-tree` succeeds, use the existing repository.
- If it fails because the project is not a Git repository, run `git init`.
- If the worktree has pre-existing changes, identify them and avoid staging unrelated files.
- If pre-existing changes overlap with the requested feature, work with them carefully and mention the overlap in the final summary.

### 2. Plan Feature Units

At the start of a development request, produce a concise numbered plan. Each unit should correspond to one user-visible feature, bug fix, configuration capability, test addition, documentation sync, or similarly coherent change.

Avoid over-splitting. Prefer "add search filter UI and behavior" over separate commits for "add button", "add state", and "wire click handler" unless those are genuinely independent.

If the request is too vague to split safely, ask one concise clarification before editing.

### 3. Execute, Verify, Commit

For each feature unit:

1. Implement only that unit.
2. Run the focused verification that matches the unit when feasible.
3. Inspect the staged candidate with Git status or diff.
4. Stage only files belonging to the unit.
5. Commit with a conventional commit message that describes the unit.
6. Continue to the next planned unit only after the commit succeeds.

Use commit messages such as:

- `feat: add profile search filter`
- `fix: handle empty settings values`
- `docs: sync configuration guide`
- `refactor: isolate theme option parsing`

If verification cannot be run, commit only when the change is still reviewable and explain the skipped verification in the final summary.

### 4. Handle Commit Failures

If a commit fails:

- Fix straightforward commit blockers, such as formatting issues, missing user.name/user.email guidance, or hook-reported issues that belong to the current unit.
- If Git identity is missing and cannot be configured safely, stop and tell the user exactly what command they need to run.
- If hooks reveal unrelated failures, do not broaden the change. Report the blocker and leave the worktree ready for the user to inspect.

## Response Pattern

For the initial planning response:

1. State whether an existing Git repository was found or will be initialized.
2. State the feature-unit plan.
3. Proceed with implementation unless a clarification is required.

For the completion response:

1. List the commits created, including short hashes when available.
2. Summarize verification run for each unit.
3. Mention any skipped verification or remaining blocker.
