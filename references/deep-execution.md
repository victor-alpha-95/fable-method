# Deep execution (load at T2+ when the task has executable or computable artifacts — code, data, exact figures — and you are not confident you are the strongest model class in play; when in doubt, load it)

This file encodes, as mechanical procedure, the habits a frontier model applies implicitly on hard technical tasks. Each rule exists because its absence is how strong-but-not-frontier models fail: not by lacking knowledge, but by stopping early, trusting artifacts, and reasoning in their heads where they should be probing.

**Scope and proportionality.** These rules apply to the executable/numeric surface of the task — apply each rule only where its object exists (no symptom ledger without symptoms, no probes without runnable code, no recomputation without figures). Prose-only ANSWER/GUIDE work does not load this file. **Everything here — ledgers, probe outputs, sweeps — is working notes, never user-facing** (core rule 7); the user sees conclusions and evidence, not the scaffolding.

## 1. Symptom accounting — the single most important rule

Never accept a fix while any part of the symptom is unexplained. In working notes, map every observed anomaly to a confirmed cause:

```
S1: total is 695937.42, statement says 716354.19 (gap: 20416.77)
    → explained by: [cause?]  residual after fix: [exact number]
S2: two analysts got different matched pairs → explained by: [cause?]
```

After each fix, recompute the residual. **In exact-arithmetic domains (money, counts, reconciliation), the required residual is exactly 0** — a residual of 0.02 is the signature of a second, subtler cause (rounding level, boundary row, one mis-flagged record), not noise. In inherently approximate domains (floats, statistics, measurements), state the tolerance explicitly up front and close symptoms against it instead.

Corollary: **one found bug is evidence of nothing.** Bugs cluster and mask each other. After finding cause N, derive what the output should be with only N fixed and compare with what you actually get; divergence means more causes.

**Escape hatch (bounded effort).** If a residual persists after the trap sweep (rule 5) and one fresh reframe, or no independent verification route is constructible: stop, report the work as UNVERIFIED with the exact residual, the causes confirmed so far, and your ranked remaining hypotheses (including "source data is wrong" — check it: the authoritative figure can be the buggy one). An honestly-scoped handoff beats an infinite probe loop.

## 2. Independent recomputation — never let the artifact grade itself

For every **load-bearing** numeric claim (one that decides the conclusion or ships to the user — not incidental small counts), recompute the figure by a separate route sharing no code with the artifact: a one-liner over the raw data, a hand-sum of a slice. The routes must agree to the last digit (or stated tolerance). No independent route constructible → say so, mark UNVERIFIED.

Same for your own reading: a conclusion reached by reading code ("this filter drops those rows") is confirmed by execution (`print(len(...))` before/after), not by re-reading.

## 3. Probe, don't simulate

Where a frontier model can reliably simulate code or data in its head, you must not. Any "this line probably produces X" → stop, run a probe (REPL one-liner, 5-row slice, print at the point of doubt). Probes cost tens of tokens; a wrong mental simulation costs the task. Probe, never assume: what a parser returns for one concrete row (types included); how many rows survive each filter stage; what a comparison does at the exact boundary value; what order an iteration visits when order matters; what a library call does with an edge argument.

## 4. Read the data before theorizing about the code

Before forming hypotheses, spend one probe on the raw input: row count, 5-row sample, distinct values of every flag-like column, min/max of dates and amounts, duplicate-key check. Half of "code bugs" are data properties the code silently mishandles. Distrust column names and comments — they describe intent, not contents.

## 5. The classic-trap sweep

On any task touching money, dates, matching, or aggregation, check each mechanically before declaring done:

1. Float arithmetic near currency (grep `float(`, bare arithmetic on parsed amounts).
2. Inclusive vs exclusive at every range endpoint (run the endpoint value through the predicate).
3. Rounding at the wrong level (per-row vs per-group vs total — compute both ways; if they differ and the spec doesn't say which, **ask the user one targeted question rather than silently guessing**; if unreachable, choose the one matching the authoritative figure and flag the assumption).
4. Mutation of a collection being iterated; missing `break` in first-match loops.
5. Duplicate keys where uniqueness was assumed (count distinct vs count).
6. Timezone/date-parsing drift at period boundaries.
7. Sort instability or iteration-order dependence anywhere two runs could differ.

## 6. Adversarial self-review before any success claim

Draft the conclusion, then attack it in working notes: the **falsifier** ("what single observation would prove this wrong?" — go check it; unfalsifiable = unverified); the **residual audit** (rule 1 closed?); the **direction check** (state one plain-language implication — "the biggest payer should *receive* money" — and test it; sign errors pass every arithmetic check). Only a failed **executable** verification counts as a failed attempt for the two-failures escalation rule — self-review nitpicks trigger fixes, not escalation.

## 7. Red-herring discipline

Suspicious-looking code is not guilty; only a probe showing a wrong output for a concrete input convicts. Fixes to innocent code are how new bugs enter during debugging. Symmetrically, clean-looking code is not innocent; only probes acquit.

## 8. Separate the generator from the evaluator

A model grading its own output over-praises it; the verifier, not the generator, is the bottleneck. In order of strength: (1) **executable verifiers** — tests, diffs, exact tie-outs; for a recurring or high-stakes bug class, optionally leave a minimal workspace-local repro-check (never unrequested deliverable files, and defer to the memory/rediscovery gates on what to persist); (2) **fresh-context review** — where subagents exist, high-stakes sign-off goes to a reviewer that didn't produce the work and sees only frame + artifact + checks; (3) **adversarial self-review** (rule 6) as the floor. After a failed attempt with no stronger model available: one fresh re-derivation from scratch (no reference to the failed reasoning), compare conclusions; still failing → the rule-1 escape hatch, not a third variation.

## 9. Stopping rule

Stop when all of: every symptom-ledger row shows a named cause with residual 0 (exact domains) or within stated tolerance (approximate domains); the original failing check passes with quoted output; independent recomputation agrees on the load-bearing figures; the trap sweep is clean; the falsifier came up empty. Or: the rule-1 escape hatch fired and the handoff is written. "The main numbers now match" is the canonical premature stop — when totals first match is exactly when to check the non-total outputs (pair-level, ordering, row-level) hardest, because that is when you are most tempted not to.
