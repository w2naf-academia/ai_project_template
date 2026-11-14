# Grant Management Repository Standard

> **Optional rule.** Keep this file (and `.claude/commands/grant-setup.md`) in a repository that
> manages one sponsored award. Delete both in any other project, and list them under
> `not_adopted` in `.claude/template.json`.

A grant management repository holds everything about **one award**: its terms, budget,
spending records, people, reporting and correspondence. Science code and data live in their own
repositories and are linked from here. Run `/grant-setup` to create this layout in a new
repository or to bring an existing one to it.

## Repository name

`<sponsor>_<program>_<account>`, lowercase with underscores, where `<account>` is the
university account (fund) number: `nsf_career_123456`, `nasa_b21_eclipse_123457`. A subaward is
named for the prime sponsor. Before an account number exists, use the sponsor's award or
proposal identifier and rename when the account is assigned (GitHub redirects the old name).
Renaming is the PI's decision; propose it, never do it unasked.

## Layout

```
grant.yaml        ← the award's facts, machine-readable (schema below)
award/            ← what the sponsor (or the prime institution) issued
├── README.md     ← fact sheet: terms, obligations, deadlines, each citing its document
├── notice_of_award/   award letter or notice of award, terms and conditions; an incoming
│                      subaward or subcontract agreement goes here too
├── modifications/     YYYYMMDD_mod##_description.pdf: incremental funding, rebudget
│                      approvals, no-cost extensions, PI or scope changes
└── correspondence/    YYYYMMDD_description.ext: award-level email with the sponsor,
                       the prime institution and the sponsored-programs office
proposal/         ← as submitted: narrative, budget, justification, attachments
finance/          ← the money
├── budget/            current working budget and its justification
├── rebudgets/         YYYYMMDD_description/: each rebudget request, with its approval
├── orsp/              YYYYMMDD_<account>_orsp.xlsx: every sponsored-programs tracker
│                      received, dated by when it was received; never overwrite one
├── ledger/            YYYYMMDD_<account>_recap.xlsx: business-office fund reports from the
│                      university ledger, dated by their as-of date; where they disagree
│                      with a tracker, the ledger wins on amounts and dates
├── orders/            YYYYMMDD_<item_or_vendor>/: one folder per purchase, holding the
│                      quote, purchase request, invoice, receipt and related email
├── invoices/          invoices received: outgoing subawards, consultants
└── closeout/          final expenditure report, closeout correspondence
personnel/        ← position descriptions, postings, effort records
reporting/        ← <yyyy>_year<N>/ per annual report (drafts, submitted version,
                    receipt); final/ for closeout reports
reference/        ← sponsor guides and policies (PAPPG, GCAM, award terms references)
notes/            ← YYYY-MM-DD_<topic>.md, one per working session
ai/               ← ai_usage_log.md
```

Add these only when an award needs them: `subawards/<partner>/` (outgoing subawards),
`management/` (plans, meetings, team), `activities/` (one folder per proposal activity),
`evaluation/`, `communications/` (announcements, presentations, publications list).

Every top-level folder carries a short `README.md` saying what belongs in it.

## File naming

- Point-in-time files: `YYYYMMDD_description.ext`, lowercase, underscores, no spaces.
  Living documents (`grant.yaml`, READMEs, the working budget) are undated.
- A document received from someone else may keep its original file name inside a dated
  folder (`finance/orders/20260505_rx888_receivers/Invoice 4471.pdf`); the folder carries
  the date and the description.
- Never overwrite a dated file. A newer version is a new dated file.

## `grant.yaml`

The award's facts in one machine-readable file. It is **authoritative for this award**: a
portfolio registry or summary that lists the same facts reads them from here and flags any
disagreement. Each fact cites the document that states it, under `sources`. Unknown values are
`null` with a `# verify:` comment; never guess.

```yaml
schema: 1
account: "123456"            # university account (fund) number, quoted; null until assigned
short: NSF Example           # short label for tables
title: "Full award title"
status: active               # proposal | pending-setup | active | no-cost-extension | ended | closed
sponsor: NSF                 # who funds the prime award
program: AGS DASI            # sponsor program or solicitation
award_no: AGS-0000000        # prime award number
instrument: grant            # grant | cooperative-agreement | contract | gift
via: null                    # prime institution, when this is a subaward
subaward_no: null            # the prime institution's subaward or subcontract number
role: PI                     # our role: PI | Co-PI | Co-I | subaward-PI
pi: A. Person                # PI of the prime award
period:
  start: 2025-01-01
  end: 2027-12-31
  original_end: 2027-12-31   # differs from end after a no-cost extension
budget:
  total: 0                   # USD to this institution, all years, direct plus F&A
  fa_rate: 0.47
  fa_base: MTDC              # MTDC | TDC | salaries
  cost_share: 0
  years:                     # obligated tracks incremental funding
    - {year: 1, start: 2025-01-01, end: 2025-12-31, budget: 0, obligated: 0}
people:                      # roles and committed effort only; no salaries, no IDs
  - {name: A. Person, role: PI, effort: [{year: 1, months: 0.5, period: summer}]}
contacts:                    # work contact details only
  program_officer: null
  grant_officer: null
  sponsored_programs: null
reports:                     # status: due | submitted | waived
  - {name: Annual project report, covers: Year 1, due: 2026-03-31, status: due}
acknowledgment: "This material is based upon work supported by ..."
repos:                       # related repositories
  science: null
  proposal: null
sources:                     # fact -> document (paths relative to the repo root)
  total: award/notice_of_award/20250101_award_letter.pdf
  period: award/modifications/20260601_mod01_nce.pdf
```

`award/README.md` is the human-readable companion. It covers what does not fit in YAML: the
statement of work, payment and invoicing terms, prior-approval items, required
acknowledgments, flow-down obligations and the reporting calendar, each citing its document.
Where it repeats a fact from `grant.yaml`, the two must agree; when a new document changes a
fact, update both in the commit that adds the document.

## Sensitive material

This repository holds salaries, fringe and indirect rates, budgets, receipts and vendor
correspondence. It stays **private**.

- **Inside a session, use it.** Reading and analyzing these records in an AI session on this
  private repository, under the institution's governed AI plan, is the intended workflow.
- **Never send it outward.** No salary, rate or receipt detail goes into issues, pull-request
  descriptions, commit messages, public repositories, sponsor-facing narrative, or any other
  tool or service.
- **Never commit personal data about other people:** applicant materials (CVs, cover and
  reference letters, evaluations), student grades or records, university ID numbers, Social
  Security numbers, bank or full card numbers. `/grant-setup` adds `.gitignore` safeguards for
  `personnel/**/applicants/` and `personnel/**/confidential/`.
- Sharing the repository with collaborators exposes all of the above; it needs the PI's
  explicit decision first.
