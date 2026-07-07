# Execution discipline

Read this file when: T3 execution, high-stakes or hard-to-reverse changes, multi-step rollouts, or live incidents.

## Before touching anything

- **Rollback is concrete**: the actual command, the actual backup path, the actual previous version — not "we can revert if needed". If writing the rollback takes effort, that effort was mandatory anyway; if it can't be written, the change is irreversible and needs explicit user sign-off before proceeding.
- **Evidence preserved**: the failing state is about to be destroyed by the fix. Save what proves the diagnosis (logs, failing outputs, before-values) — both for the journal and in case the fix doesn't hold.
- **Backup before destructive edits**: copy the file/table/config first. Boring, thirty seconds, non-negotiable for anything that can't be regenerated.

## Checkpointed execution

- Smallest meaningful change → verify → next. Each checkpoint leaves the system in a state that is either working or cleanly revertable.
- One concern per change. Bundling the fix with a drive-by refactor means neither can be verified independently, and the rollback undoes both.
- Keep a running change log as you execute (what/when/result, one line each). It costs nothing in the moment, becomes the journal entry's Fix field, and is the difference between a recoverable and unrecoverable situation when step 4 of 6 fails.

## Verification patterns by domain

The universal rule: verification is evidence from the environment, not your assessment of your own work. Concretely:

- **Code**: rerun the original failing reproduction — it must pass now. Run adjacent tests, the build, the linter. Quote actual output; "the tests should pass" is not a result.
- **Data**: recompute the corrected figure from source by an independent path (different query shape, different tool). Reconcile totals two ways. Spot-check individual rows against ground truth.
- **Documents/config**: diff before/after and read the diff as the consumer of the document would; render/parse it with the real consumer where possible.
- **Process/automation**: one supervised end-to-end live run, then monitor the first several unsupervised executions. First-run success is necessary, not sufficient.
- **Intermittent problems**: enough clean runs to be statistically meaningful against the historical failure rate — state the arithmetic explicitly.

If none of these are executable from the current environment, the honest output is: "Fix applied; unverified. Run <exact check> to confirm." An unverified-but-labeled fix maintains trust; a confident false success destroys it and, per the benchmark data, is the single most common way agents fail.

## Independent review (high stakes)

For consequential changes, have a fresh-context reviewer check the work — a subagent where available, the user otherwise. Fresh context matters because the doer's context is contaminated by their own intentions: they read what they meant, not what is there. Scope the reviewer to two questions: does the change satisfy the success criterion, and what adjacent thing might it break. Cap the nitpicking — the reviewer exists to catch failures, not to gold-plate.

## After the change

- Look one level out: what shares state, imports, or depends on what was touched? Run its checks too.
- If the failure mode is delayed (caches, cron, month-end), schedule the follow-up check or tell the user exactly when and what to look for.
- Update the journal entry (Step 5) with `Verified by:` filled in from real output.

## Thrash guard

Two failed correction attempts is a stop signal, not an invitation for a third variation. Failed attempts accumulate in context and actively degrade subsequent reasoning — the model starts pattern-matching against its own failures. On the second failure: stop, restate the problem from scratch as if new, re-read the original evidence, and re-enter at Step 1 with the tier escalated. A restatement from a clean frame routinely finds in minutes what variation-thrashing misses in an hour. Where a fresh subagent is available, handing the restated problem to one is the strongest version of this move.

## When to stop and ask the user

- The next step is destructive or irreversible and wasn't explicitly pre-approved.
- Scope has grown past the original mandate (the fix now requires touching systems the user never mentioned).
- Evidence contradicts the user's stated assumption — say so plainly before proceeding on either version.
- Cost of continuing exceeds the value of the fix (report the situation and the options instead of grinding).

## Incident mode (live, user-impacting problems)

- Severity first: who is affected, how badly, is it worsening. Response effort scales with severity, not with how interesting the bug is.
- Mitigate → communicate → root-cause, in that order. Rollback is the default mitigation; a rollback that ends impact in four minutes beats a diagnosis that takes an hour.
- One change source: during an incident, changes go through one coordinated channel. Parallel uncoordinated fixes ("freelancing") reliably make outages worse.
- Communicate state transitions to the user as they happen: mitigated / cause identified / fixed / verified. Uncertainty is fine to report; silence is not.
- Afterwards, the journal entry doubles as a lightweight blameless postmortem: impact, timeline, contributing causes, what would have caught it earlier — and one prevention action, owned and dated, or the honest note that prevention isn't worth its cost.
