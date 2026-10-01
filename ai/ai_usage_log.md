# AI Usage Log — {{PROJECT_NAME}}

This log records all substantive AI-assisted sessions for the project
"{{PROJECT_TITLE}}".

Required per University of Scranton AI Policy, HamSCI Generative AI Use Agreement, NASA AI guidance, NSF AI guidance, and {{FUNDER}} expectations.

---

<!-- Append new entries below this line, newest at the bottom. Use the format produced by the /commit command. -->

## [2026-04-25 13:26 EDT]
- **Tool**: Claude (Anthropic), claude-opus-4-7
- **Session Purpose**: Fix invalid `$schema` URL in `.claude/settings.json` so Claude Code stops rejecting the file (caught while using a downstream project scaffolded from this template).
- **Sections/Files Affected**: `.claude/settings.json`
- **Nature of Contribution**: Bug fix
- **Human Review Status**: Reviewed and verified
- **Git Hash**: 839ee05

## [2026-04-25 13:31 EDT]
- **Tool**: Claude (Anthropic), claude-opus-4-7
- **Session Purpose**: Align template with the `.claude/` folder anatomy described in https://blog.dailydoseofds.com/p/anatomy-of-the-claude-folder — gitignore the personal-override file and scope language-specific rules to relevant file types so they don't load when not applicable.
- **Sections/Files Affected**: `.gitignore` (added `CLAUDE.local.md`), `.claude/rules/latex-writing.md` (added `paths:` frontmatter scoping to `.tex`/`.bib`/`.cls`/`.sty`), `.claude/rules/python-code.md` (added `paths:` frontmatter scoping to `.py`/`pyproject.toml`/`requirements*.txt`)
- **Nature of Contribution**: Configuration / scaffolding refinement
- **Human Review Status**: Reviewed and verified
- **Git Hash**: e229ba5

## [2026-10-01 16:26 UTC]
- **Tool**: Claude (Anthropic), claude-opus-5-5
- **Session Purpose**: Update the template so that every commit goes on a feature branch and
  through a pull request that a human reviews and merges, replacing the earlier
  "commit to main, ask before pushing" workflow.
- **Sections/Files Affected**: `.claude/commands/commit.md` (steps 6 to 9 rewritten for branch, PR, pointer bump, push order; see the follow-up entry below), `CLAUDE.md` (new Git Workflow section; Submodules steps), `.claude/rules/python-code.md`, `.claude/rules/ai-governance.md`, `README.md`
- **Nature of Contribution**: Edit
- **Human Review Status**: Pending review
- **Git Hash**: c525fe3 (branch pr-workflow, PR w2naf-academia/ai_project_template#1), merged as 28aaa71

## [2026-10-01 16:50 UTC]
- **Tool**: Claude (Anthropic), claude-opus-5-5
- **Session Purpose**: Follow-up on the same PR, at N. A. Frissell's direction: keep this
  template self-contained. The first commit had replaced `/commit` with a copy of a private
  workflow file that cited rule IDs and a repository readers of this template cannot see; this
  commit replaces it with a self-contained branch/PR workflow and removes those references.
- **Sections/Files Affected**: `.claude/commands/commit.md`, `README.md`, `ai/ai_usage_log.md`
  (Sections field of the 16:26 entry corrected to describe the final change)
- **Nature of Contribution**: Edit
- **Human Review Status**: Pending review
- **Git Hash**: d9af543 (branch pr-workflow, PR w2naf-academia/ai_project_template#1), merged as 28aaa71
