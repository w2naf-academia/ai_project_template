# /grant-setup: Create or Adopt the Grant Repository Layout

Brings this repository to the grant management standard in `.claude/rules/grant-management.md`:
the layout, `grant.yaml`, folder READMEs and `.gitignore` safeguards. Read that rule first; it
is the specification, and this command only carries it out.

Works on a new repository (creates the layout) and on an existing one (moves what is there into
the layout, with the user's approval). It never deletes a file and never renames the GitHub
repository.

## Steps

### 1. Sync and look
```bash
git fetch && git status -sb
git ls-files | head -200
```
Start from a clean tree on a feature branch from the remote tip
(`git switch -c grant-setup origin/main`). If the tree is dirty, stop and ask.

Check the repository name against the standard (`<sponsor>_<program>_<account>`). If it does
not match, propose the new name to the user and leave the rename to them.

### 2. Gather the award's facts
Read the award documents in the repository (notice of award, subaward agreement,
modifications, the submitted budget). Ask the user only for what the documents do not state.
Draft `grant.yaml` to the schema in the rule:
- every filled value gets a `sources:` entry naming the document and, where useful, the page
  or article;
- every value no document states is `null` with a `# verify:` comment;
- `people` carries roles and committed effort only.

Show the draft to the user before writing it.

### 3. Plan the layout
- **New repository:** list the folders to create (the standard ones; the optional ones only if
  the award needs them).
- **Existing repository:** build a move map, one line per path, `old/path -> new/path`, for
  every tracked file that has a home in the standard. Rename point-in-time files to
  `YYYYMMDD_description.ext` where the date is evident from the name or the document; leave a
  received document's own file name alone inside a dated folder. List anything with no
  obvious home separately and ask.

Show the plan and **wait for approval**. A large move is hard to review after the fact.

### 4. Carry it out
- Move with `git mv`, so history follows the files.
- Create each standard folder with a short `README.md` saying what belongs there (use the
  rule's layout comments). `finance/README.md` and `personnel/README.md` also state the
  sensitive-material rules.
- Write `grant.yaml` and, if missing, `award/README.md` (the fact sheet: terms, obligations,
  deadlines, each citing its document).
- Append to `.gitignore`:
  ```
  # Personal data about other people never enters this repository
  personnel/**/applicants/
  personnel/**/confidential/
  ```
- Update the repository-structure section of `CLAUDE.md` to the new layout, and fix any paths
  `CLAUDE.md` or `README.md` cite that moved.
- Search the tree for references to moved paths (`git grep -n '<old path>'`) and update living
  documents. Historical records (`ai/ai_usage_log.md`, dated notes) keep the paths of their
  time.

### 5. Verify
```bash
git status --short          # only moves, new READMEs, grant.yaml, .gitignore, CLAUDE.md
python3 -c "import yaml; yaml.safe_load(open('grant.yaml'))" && echo grant.yaml ok
```
Every `sources:` path must exist: check each with `test -e`.

### 6. Commit through `/commit`
One PR for the restructure. The PR body summarizes the move map, lists every `# verify:` left in
`grant.yaml`, and names any repository rename proposed in step 1.
