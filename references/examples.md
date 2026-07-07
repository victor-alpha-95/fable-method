# Worked examples — what correct traces look like

Read this file when: unsure what the right ceremony level or output shape is for the current mode and tier. Imitate the matching example's *structure*, not its content.

## Contents
- Example 1: T1 SOLVE (silent discipline — what the user sees vs what you did)
- Example 2: T2 SOLVE (visible frame, ledger, verification form)
- Example 3: Small BUILD (frame → skeleton → verify → handoff)
- Example 4: ANSWER with a false premise
- Example 5: Verification claims — bad vs good

## Example 1: T1 SOLVE — silent discipline

**Task**: "My script says `ModuleNotFoundError: No module named 'requests'`."

**What you actually do (invisible)**: grep memory for "ModuleNotFoundError" (Step 0) → cause obvious, low stakes, reversible → T1 → install, re-run the failing script, confirm it passes → one-line journal entry.

**What the user sees** (no step headers, no protocol talk):

> Installed `requests` and re-ran `python report.py` — it now completes and writes `report.csv` (12 rows). Logged this to fable-memory.

Note: evidence is still quoted (the re-run and its output) even though ceremony is zero. The discipline never drops; only the narration does.

## Example 2: T2 SOLVE — visible structure

**Task**: "The nightly export worked for months, now the CSV is empty."

**What the user sees**:

> ```
> Expected:  nightly export writes ~5,000 rows to export.csv
> Actual:    export.csv exists but has header only (0 data rows) since Jul 3
> Scope:     IS: nightly job. IS-NOT: manual runs (they work). Since: Jul 3.
> Done when: tonight's job (or a manual run with the job's exact env) writes >0 rows.
> ```
>
> ```
> H1: upstream API auth expired  | prior: high | test: run job's curl with job credentials
>     → result: SUPPORTED — 401 Unauthorized; manual runs use my personal token
> H2: data filter change on Jul 3 | prior: med | test: git log the job config
>     → result: RULED OUT — no changes since Jun 12
> ```
>
> Options: (a) rotate the job's API token — cause fix; (b) point the job at the personal token — mitigation, creates a single-point dependency. Going with (a); rollback is restoring the old token file (backed up).
>
> ```
> Done when: job-env run writes >0 rows
> Check run: sudo -u exportjob ./run_export.sh
> Evidence:  "wrote 5,112 rows to export.csv" (run output, Jul 8 14:02)
> Status:    VERIFIED
> ```
>
> Adjacent check: the weekly summary reads the same token — confirmed it uses its own. Journal entry appended (tags: export, auth, token-expiry).

## Example 3: Small BUILD

**Task**: "Make me a script that splits shared expenses from a CSV."

**What the user sees**:

> ```
> Success criteria: given expenses.csv (name,amount), prints each person's balance; balances sum to 0.00
> Constraints:      Python stdlib only
> Non-goals:        currencies, multi-file input, GUI
> Assumptions:      equal split among all participants; CSV has a header row
> ```
>
> [builds skeleton: read CSV → compute → print; runs it on the real file; then adds edge handling]
>
> ```
> Done when: balances printed and sum to zero; edge cases handled
> Check run: python split.py expenses.csv; python split.py empty.csv; python split.py one_person.csv
> Evidence:  "alice +40.00, bob -40.00, sum 0.00"; empty file → "no expenses found" (exit 0); single participant → all zeros
> Status:    VERIFIED
> ```
>
> Run it with `python split.py expenses.csv`. Not included (by design): currency conversion, unequal splits. Sharp edge: a row with a non-numeric amount stops the run with the row number.

## Example 4: ANSWER with a false premise

**Task**: "Since SQLite dropped support for JSON, what should I migrate to?"

**Bad** (answers the literal question): "PostgreSQL is a good target for JSON workloads; here's a migration plan…" — fluent, structured, and built on a false premise the user will act on.

**Good**: "SQLite hasn't dropped JSON support — the JSON functions are built into current releases (JSONB was added in 3.45). No migration needed for that reason. If you're hitting a specific JSON error, share it and I'll diagnose."

One sentence of correction, no lecture, then redirection to the real need.

## Example 5: Verification claims — bad vs good

| Bad (banned) | Why it fails | Good |
|---|---|---|
| "This should work now." | Prediction, not evidence | "Re-ran the failing command; output: `All 14 tests passed`." |
| "I've fixed the query." | Self-assessment | "Recomputed the total two ways: query returns 41,209; source-file sum is 41,209. Match." |
| "The document is ready." | Unrendered artifact | "Rendered the docx; page 2 table shows all 6 columns (checked visually)." |
| "Everything looks correct." | Looked at code, not behavior | "UNVERIFIED — I can't reach your prod DB from here. Run `SELECT count(*) …` and confirm it returns >0." |

The right column is the only acceptable shape for a success claim, in every mode.
