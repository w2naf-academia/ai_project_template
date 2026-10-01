---
description: Append an AI usage log entry, then commit every changed repo (submodules first, then the main repo) on a feature branch and open a PR for each.
---

# /commit: AI-Assisted Commit Workflow

Use this command any time you finish a substantive AI-assisted work session. It enforces
conventions **A1** (log before committing), **R2** (every change via branch and PR), **R4** (submodule
commit order), **R5** (never push without instruction), **R7** (commit messages), and **R8** (never commit artifacts or secrets).

Handles flat repos (no submodules), single-submodule repos, and multi-submodule repos.
Submodules are auto-detected from `git submodule status`.

**Vendored copies.** A project may keep a copy of this file in its own `.claude/commands/`, so
that `/commit` still works on a machine where research-conventions is not installed. Such a copy
must stay **byte-identical** to this one; put project specifics in the project's `CLAUDE.md`
instead. Edit this file first, then re-copy it.

## Steps

### 1. Get the current timestamp
```bash
date
```
Use this exact output; never estimate the date/time (A1).

### 2. Identify all changes
Check status across the main repo and every submodule:
```bash
git status
git submodule foreach 'git status'
```

Show diff summaries:
```bash
git diff --stat
git submodule foreach 'git diff --stat'
```

If `git submodule foreach` produces no output, the repo has no submodules; proceed without them.

If the project vendors this command and has a drift check (e.g. `tools/check_vendored_commands.sh`),
run it now. A failure means the two copies of this workflow disagree: stop and reconcile them
before committing anything.

### 3. Locate the AI usage log
`ai/ai_usage_log.md` is the convention. If the repo does not have one, check for a project-
specific location named in its `CLAUDE.md` before creating a new file. Submodules with their own
log get their own entry there, not in the parent's (A1).

### 4. Ask the user for session purpose
Draft a purpose of this session for the AI log. Show it to the user, and ask them to confirm.

### 5. Draft the AI usage log entry
Use this format. Use the **actual running model ID** in the Tool field (e.g. `claude-opus-5`),
never a placeholder.

```
## [YYYY-MM-DD HH:MM TZ]
- **Tool**: Claude (Anthropic), <actual-model-id>
- **Session Purpose**: [user's description]
- **Sections/Files Affected**: [list changed files and sections]
- **Nature of Contribution**: [Draft / Edit / Analysis / Code generation / Research / etc.]
- **Human Review Status**: [Reviewed and verified / Partially reviewed / Pending review]
- **Git Hash**: [fill in after committing]
```

Present the draft to the user. Wait for confirmation or corrections.

### 6. Append the entry to the log

### 7. Every repo with changes: feature branch, commit, push the branch, open a PR (R2)
No repository takes a direct commit to `main`, the main repo included. Work the submodules
first, then the main repo (R4). For each repo with changes, propose a short kebab-case branch
name with the commit message and wait for the user's approval. Then:
```bash
git -C <repo> fetch
git -C <repo> switch -c <topic> origin/main     # uncommitted changes carry over to the branch
git -C <repo> add <files>
git -C <repo> commit -m "[AI-assisted] <description>"
git -C <repo> push -u origin <topic>
gh pr create --repo <owner>/<repo> --base main --head <topic> --title "..." --body-file <file>
```
If the repo is already on a feature branch whose PR is still open **for this same change**, add
the commit there and push it; do not open a second PR. If it is on some other branch, say so and
ask before branching.

The PR body says what changed and why, references the tracking issue (R7), links the companion
PRs in the other repos of the same change, and ends with the A8 attribution trailer. Pushing the
feature branch, opening the PR and pushing further commits to it are standing permission once
the user has approved the commit. **Never push `main`. Never merge**: merging is the user's
call. A repo whose remote cannot host a PR is outside the rule; ask.

In the main repo, stage the AI usage log with the other changed files. **Do not stage a
submodule pointer that names an unmerged branch commit** (R2).

Write each message for someone reading it in two years with no context: what changed and why,
not a restatement of the diff. Reference the tracking issue (`closes #N` / `refs #N`, or
`org/repo#N` when the issue lives in a different repo than the commit) (R7).

### 8. Fill in the git hash(es)
```bash
git log --oneline -1
```
Update the log entry's **Git Hash** field with the branch commit and PR for each repo (e.g.
`main-repo=abc1234 (branch topic, PR owner/repo#5), sub=def5678 (PR owner/sub#3), pending merge`).
Commit that on the same branch with a non-`[AI-assisted]` message such as
`Update AI usage log with git hashes`, and push it.

### 9. After the user merges a submodule PR: bump the pointer
```bash
git -C <sub> switch main && git -C <sub> pull --ff-only && git -C <sub> branch -d <topic>
git add <sub> ai/ai_usage_log.md && git commit -m "Bump <sub> to merged PR #N"
```
Make that commit on the main repo's feature branch: the still-open PR for the same change if
there is one, otherwise a new branch and PR. Replace "pending merge" in the log with the merged
SHA. If the user merged by squash or rebase, the branch SHAs in the log no longer exist; correct
them to the merged SHAs (R2).

### 10. Pushing
Never push `main` in any repository (R5). The feature-branch pushes in steps 7 to 9 are the one
standing exception. Always fast-forward only, fetch and verify the remote state first, never
force-push, never hard-reset.

## Notes

- The `[AI-assisted]` prefix applies only to commits whose content was produced or substantially
  shaped with AI assistance. Pure human edits (the user manually fixes a typo or rewords a
  sentence) do not carry the prefix.
- Commit submodules with their own `[AI-assisted]` prefix when their content is AI-assisted,
  separately from the main-repo pointer-bump commit.
- Use `git add <specific-files>` rather than `git add -A` or `git add .`, to avoid staging
  untracked artifacts: build outputs, credentials, large binaries (R8).
- On a machine where `gh` is a confined snap, it may not be able to see repos outside `$HOME`.
  Use `/usr/bin/git` for anything touching the work tree, and pass `--repo owner/name` to `gh`.
  Such a `gh` also cannot read `--body-file` paths under `/tmp` or outside `$HOME`; stage PR and
  comment bodies in `~/snap/gh/current/`.
