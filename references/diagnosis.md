# Diagnosis toolkit

Read this file when: T3 problems, stuck at T2, intermittent failures, data discrepancies, or process/human-system breakdowns.

Order of operations: **stabilize → observe → reproduce → hypothesize → test → conclude.** Skipping ahead in this order is the most common diagnostic error; each stage exists because the next one is unreliable without it.

## Stabilize first (live incidents)

When the problem is actively causing damage, resist the instinct to root-cause immediately. Make the system as functional as possible — roll back the last change, disable the failing subsystem, divert traffic, switch to the manual process — then diagnose at leisure. Preserve evidence before it disappears: copy logs before rotation, snapshot state, save the failing input. The rollback that ends the damage in minutes beats the diagnosis that explains it in hours.

## Observe: look before theorizing

- Read the error literally and completely, including the parts after the first line. Error text is also a search signature: grep memory for it (Step 0) and search the web for it verbatim.
- Get real telemetry before forming theories: logs, traces, metrics, the actual rows, the actual file. If observability doesn't exist, adding minimal instrumentation (print the intermediate value, log the branch taken, count the rows at each stage) is the first move, not a last resort.
- Guessing is permitted for exactly one purpose: choosing *where to look next*. Guess-then-fix without observation is how systems accumulate cargo-cult patches.
- Read the code that actually emitted the error — follow the message to its source — not the code you assume is responsible.

## Reproduce and minimize

A reliable reproduction converts debugging from archaeology into experimentation, and it becomes the Step 4 verification for free.

1. Capture the failing case as something rerunnable: a command, a script, a saved input file.
2. Confirm it fails. (Surprisingly often it doesn't — which is itself a major clue: environment, timing, or state.)
3. Minimize: remove components, shrink the data, delete steps — while the failure persists. Every removal that keeps the failure eliminates a suspect. The minimal failing case frequently names the cause outright.

## Hypothesis ledger

Maintain it visibly. Format:

```
H1: <candidate cause> | prior: high|med|low | test: <observation that would distinguish it>
    → result: RULED OUT — <evidence>   (or: SUPPORTED — <evidence>)
H2: ...
```

- Minimum two candidates at T2+; if only one comes to mind, the second is "something in my framing is wrong."
- A valid cause must explain the *entire* observed pattern — both what happens and what doesn't (see IS/IS-NOT below). A hypothesis that explains the failure but not the working cases is incomplete.
- Design tests to *disconfirm*, not to confirm. A test that can only agree with you is not a test.
- Ruled-out hypotheses stay in the ledger and go into the journal's `Ruled out:` field.

## IS / IS-NOT specification (for ambiguous or multi-candidate problems)

|  | IS | IS NOT (but could be) | What distinguishes? |
|---|---|---|---|
| **What** | failing object/symptom | similar things not failing | |
| **Where** | locations observed | plausible locations not observed | |
| **When** | first seen, pattern, frequency | times it doesn't occur | |
| **Extent** | how many, how big, trend | how big it could be but isn't | |

The IS-NOT column is where weak hypotheses die: any candidate cause that would also produce the IS-NOT cases is wrong or incomplete. This is differential diagnosis for systems, and it is the fastest tool for intermittent and "sometimes it works" problems.

## What changed last

Systems have inertia: something that worked and stopped working was almost always touched — a deploy, config change, dependency bump, data shape drift, upstream change, expired credential, filled disk. Check change logs first; timing correlation is a strong prior. It is a prior, not proof — confirm the mechanism before declaring victory, and stay alert to the trap of latching onto whatever caused the *last* incident.

## Bisection

For large search spaces, halve instead of scan: commit history (git bisect), pipeline stages (check intermediate outputs at the midpoint), data ranges (which half of the file triggers it), config diffs (apply half the differences). Each test eliminates half the space; ten tests search a thousand suspects.

## Base rates, parsimony, and the multi-cause caveat

Hoofbeats are horses before zebras: typos before compiler bugs, your code before the library, the library before the platform. But note Hickam's dictum — real systems can have several mundane contributing causes at once, and a single elegant root cause is sometimes a fiction imposed on a messy reality. When evidence resists one explanation, test whether two partial ones fit better. Write "contributing causes", plural, in the journal.

## Intermittent problems

- Increase observation rate: add capture-on-failure harnesses (loop the repro, dump state when it fails).
- Hunt environmental correlates: load, concurrency, time of day, specific inputs, cache state, first-run-after-restart.
- The IS/IS-NOT When-row is your main instrument.
- Never claim an intermittent problem fixed on one clean run. Required: enough clean runs that the historical failure rate would have produced several failures. State the arithmetic ("failed ~1 in 5; 25 clean runs since fix").

## Articulation (the rubber-duck effect)

When stuck, explain the system to the user from first principles: what each part does, what you believe happens at each step, where belief stops being grounded in observation. The point where the explanation goes vague or contradicts an observation is usually the bug's address. This costs five minutes and routinely beats another hour of trial-and-error.

## Domain playbook: data discrepancies

Two numbers disagree ("dashboard says X, finance says Y", "revenue dropped 40%"):

1. Reconcile at the source: recompute both numbers independently from the rawest shared data. One of them usually stops being reproducible immediately.
2. Check definitions before arithmetic: time zones and window boundaries (inclusive/exclusive, calendar vs rolling), dedupe keys, filters (test/internal rows), currency/units, NULL handling in aggregates.
3. Row-count the pipeline stage by stage; the stage where counts jump or drop is the crime scene.
4. Check join cardinality: unexpected duplicates after a join (fan-out) and silent row loss (inner join on dirty keys) explain a large share of business-data mysteries.
5. Ask what changed: schema, upstream export, tracking code, a "harmless" filter someone added.
6. Verify the discrepancy is real before diagnosing it: an expected seasonal effect, a partial period compared against a full one, or a genuinely-correct number that merely surprised someone accounts for many reported "drops".

## Domain playbook: process and human-system failures

1. Walk the path an actual failing instance took — timestamps, handoffs, artifacts — not the documented process. The divergence point between the diagram and reality is the finding.
2. Interview the artifact trail before interviewing people; artifacts don't reconstruct memories.
3. Frame in contributing factors, never a single root cause, and never "human error" as a terminus. If a person made an error, the diagnostic question is "how did that action make sense to them at the time" — what information, incentives, and defaults they faced. Blame-seeking shuts down the information flow diagnosis depends on, and "root cause: carelessness" has zero predictive power for prevention.
4. Prefer "how did this happen" narratives over "why" chains: five-whys-style linear chains are non-repeatable (different analysts reliably reach different "root causes"), force single-cause stories onto multi-cause events, and stop wherever is politically convenient. If a why-chain is used at all, treat it as brainstorming input to the ledger, not as a method.
