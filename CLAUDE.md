# {{PROJECT_NAME}}

## Project Overview
{{ONE-PARAGRAPH PROJECT DESCRIPTION — what is being written or built, its purpose, and audience.}}

**PI**: {{PI_NAME_AND_AFFILIATION}}
**Collaborators**: {{COLLABORATORS}}
**Funder**: {{FUNDER}}{{FUNDING_AMOUNT_OPTIONAL}}
**Project period**: {{PROJECT_PERIOD}}

## Project Goal
{{PROJECT_GOAL — 1-3 sentences.}}

## Repository Structure
This project starts from the `ai_project_template` scaffold. Add or remove top-level directories to match your project type. The scaffold expects:

```
{{REPO_NAME}}/
├── CLAUDE.md
├── README.md
├── .gitignore
├── .gitmodules                   ← present only if you add submodules
├── .claude/
│   ├── settings.json
│   ├── commands/commit.md        ← /commit workflow
│   └── rules/
│       ├── ai-governance.md
│       ├── latex-writing.md      ← delete if no LaTeX
│       └── python-code.md        ← delete if no Python
├── ai/
│   └── ai_usage_log.md           ← mandatory AI session log
└── {{PROJECT-SPECIFIC FOLDERS}}  ← e.g., manuscript/, src/, posters/, proposal/
```

## Rendered Output

**Commit the human-readable record.** Rendered memos, reports, posters and final figures (PDF,
PNG) are committed next to the source that produces them. They show what the work found without
anyone having to rebuild it, and the commit history becomes a readable record of what was done.
When a source changes, rebuild its output and commit both in the same commit, so they never
disagree. What stays out of git: LaTeX intermediates (`.aux`, `.log`, `.bbl` and the like),
caches, scratch output, and bulk or raw data that a committed script regenerates. A single file
over 50 MB needs a decision first (GitHub warns at 50 MB and rejects files over 100 MB).

## Git Workflow
Every change goes through a pull request, in this repo and in every submodule. Nothing is committed directly to `main`.
1. Branch from the remote tip: `git fetch && git switch -c <topic> origin/main`
2. Commit on the branch, with the `[AI-assisted]` prefix where applicable
3. Push the branch and open a PR: `git push -u origin <topic>`, then `gh pr create`
4. A human reviews and merges. Claude never pushes `main` and never merges.

Once the user has approved a commit, pushing its feature branch and opening or updating its PR is standing permission. Any other push needs explicit instruction. Never force-push or hard-reset.

## Submodules (optional)
If your project includes submodules (e.g., an Overleaf manuscript or a separate code repo):
1. Make changes and commit **inside** the submodule first, on its own feature branch and PR
2. Bump the submodule pointer in this repo only after the submodule PR has merged; never pin this repo to an unmerged branch commit
3. Push the submodule before this repo, and open one PR per repo, cross-linked in each body
4. A repo whose remote cannot host a PR (e.g., Overleaf) is outside the PR rule; ask before changing one

The `/commit` workflow auto-detects submodules via `git submodule status`.

## AI Governance
All AI-assisted work must comply with the policies in `.claude/rules/ai-governance.md`.
Every substantive AI session must be logged in `ai/ai_usage_log.md` before committing.
Use the `/commit` command to handle logging and committing in the correct order.
