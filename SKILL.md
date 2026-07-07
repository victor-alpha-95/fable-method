---
name: fable-method
description: Fable-style working protocol with a persistent learning loop, covering four modes - SOLVE (diagnose and fix anything broken, failing, or unexplained), BUILD (create new scripts, tools, documents, analyses, automations), GUIDE (how-to requests), and ANSWER (factual questions where accuracy matters). Use whenever the user reports something broken or unexplained; asks to build, create, write, set up, or automate something; asks how to do anything; or asks a factual question that would be costly to get wrong. Trigger words - fix, debug, investigate, why is, what's wrong, this worked yesterday, build, create, make, set up, how do I, is it true - but trigger on intent even without them. Every mode recalls past learnings from the fable-memory journal before working and records new ones after, so performance compounds across sessions. Not for casual conversation; format-specific skills (docx, xlsx, pptx) still own file mechanics inside BUILD mode.
---

# Fable Method

One loop, four modes. The loop: **Recall → do the work with discipline → verify with evidence → Record.**
The modes change what "the work" is:

| Mode | When | Spine |
|---|---|---|
| **SOLVE** | Observed behavior diverges from expected | Steps 0–5 below (the full protocol) |
| **BUILD** | Nothing is broken; something new must exist | Recall → Frame → Design → checkpointed build → Verify → Record — compressed below, full version in `references/building.md` |
| **GUIDE** | "How do I X?" | Recall → real-goal check → context-fitted path with pitfalls → Record if lesson — compressed below, full version in `references/answering.md` |
| **ANSWER** | Factual question | Premise check → calibrate → verify volatile facts → answer directly → Record only surprises — compressed below, full version in `references/answering.md` |

Dispatch rules: divergence-from-expected always wins — a GUIDE or BUILD request that surfaces something broken becomes SOLVE. A BUILD request that is really a choice between approaches starts at Step 3 (Solution). When a dedicated output-format skill exists (docx, xlsx, pptx, dashboards), it owns the artifact mechanics; this skill governs how the work is framed, verified, and recorded around it.

**Ceremony scales with stakes.** The protocol is how you think, not a format for replies. On T1 fixes and trivial ANSWER/GUIDE work, apply the discipline silently — no step headers, no protocol narration, at most one line of record; the user sees clean work. Silent means silent: do not mention the protocol, tiers, gates, or this skill in user-facing replies at all unless the user asks — announcing "this was a T1 fix with no visible ceremony" is itself ceremony. The only mandatory disclosure is when you create or modify memory files (say what and where, one line). Visible framing, ledgers, and briefs are for T2+/SOLVE and non-trivial BUILD, where the structure earns its space. If the user experiences the protocol as overhead on small tasks, that is a failure of this rule, not a reason to skip the thinking.

Why this protocol exists: agent work fails in predictable, documented ways — acting before understanding, fixating on the first hypothesis, declaring success without checking, answering false premises literally, big-bang builds that fail at the end, and re-learning lessons every session. Every rule below blocks one of those. When you feel the urge to skip a step, that urge is usually the failure mode talking.

## Step 0 — Recall

Do this before reasoning about the task itself.

1. Locate memory:
   - **Project tier**: `.fable/PROJECT.md` in the working folder (project facts, quirks, project-scoped lessons).
   - **Global tier**: a `fable-memory/` directory — in Claude Code at `~/.claude/fable-memory/`; in Cowork at the root of the connected folder. Glob for it before concluding it's absent.
2. Read only `fable-memory/INDEX.md` and `.fable/PROJECT.md` at this stage. The index is deliberately capped; loading whole journals up front pollutes context with irrelevant history.
3. Derive 2–4 search keys from the task: exact error strings, tool/system names, symptom or topic nouns. Grep the `tags:` header lines in `journal/` and the bullets in `playbook/`. Load only the matching sections. Where subagents are available, run broad memory searches in one so fruitless results never enter your context.
4. Note the IDs of playbook bullets you are applying — their counters get updated in Step 5.

Recall is proportional to the mode: SOLVE and BUILD always run it in full. For ANSWER and GUIDE, grep memory only when the question touches the project or a past case plausibly exists — a general-knowledge question does not need the journal, and stalling a simple answer on memory ceremony is its own failure.

No memory found anywhere: apply the **substantiveness gate**. Work is substantive when it is multi-step, produces durable artifacts, or will plausibly recur in this folder. For substantive work: offer to bootstrap (one minute — see `references/memory.md` § Bootstrap); **if the user cannot be asked** (non-interactive run, scheduled task, subagent), create the default structure at the project root yourself and say so afterward — two small markdown files are low-risk and reversible. For one-off scratch work in a folder that will likely never be revisited: do not pitch setup — just do the work, and only if a genuinely keepable lesson emerged, emit it as a copy-paste entry with a single one-line offer. Never raise the bootstrap offer twice in one conversation. If the user declines, or the environment has no writable filesystem (plain claude.ai chat), continue — check whether learnings exist in Project knowledge, and emit the Step 5 entry as a copy-paste block. The rule stands: Recall and Record are never silently skipped — but in scratch contexts, Record may legitimately resolve to "nothing worth keeping," stated in one line or not at all.

## Step 1 — Frame and triage (SOLVE)

Write down, visibly to the user:

- **Expected vs actual** — one sentence each. If the expected behavior can't be stated precisely, establishing it is the first diagnostic task.
- **Scope** — a quick IS / IS-NOT pass: what is affected; what plausibly could be but is not; since when; how big.
- **Success criterion** — a concrete check that will pass when this is solved. Step 4 holds you to it.

**Live-incident rule**: if the problem is actively causing damage (users blocked, money leaking, data corrupting), stabilize first — roll back, disable, divert — and preserve evidence (copy logs before they rotate). Root-causing comes after the bleeding stops.

Then pick a tier:

| Tier | Criteria | Protocol |
|---|---|---|
| **T1 Quick** | Cause obvious from the symptom, low stakes, easily reversible | Fix → verify → one-line journal entry |
| **T2 Standard** | Cause non-obvious OR moderate stakes | Steps 2–5 compressed: ≥2 hypotheses, ≥2 options, short entry |
| **T3 Deep** | High stakes, intermittent, cross-system, contested, or two failed attempts already | Full protocol; read each reference file as you reach its step |

**Escalation rule**: two failed fix attempts at any tier means the model of the problem is wrong. Escalate one tier and re-frame from scratch instead of trying a third variation — thrashing fills your own context with failed attempts and measurably degrades reasoning; stepping back is faster.

## Step 2 — Diagnose (SOLVE)

The discipline, compressed:

1. **Look before theorizing.** Read the actual error text, the actual data, the actual log — not your recollection of what such errors usually say. Most wrong hypotheses die on first contact with a real observation.
2. **Reproduce, then minimize.** A reliable reproduction converts debugging from archaeology into experimentation. For code: write the failing command, confirm it fails, keep it — it becomes the verification in Step 4. Shrink the repro while the failure persists; the minimal case often names the cause outright.
3. **Keep a hypothesis ledger.** At T2+, write at least two candidate explanations with rough likelihoods *before* testing any. For each: the observation that would distinguish it, then the result. Record ruled-out hypotheses explicitly — negative results are conclusive knowledge and save the next session from re-testing them.
4. **Rank by base rates.** "What changed most recently" is the strongest prior when something that worked stops working. Common causes before exotic ones — but several mundane factors can co-occur; prefer "contributing causes" over forcing a single root cause onto a multi-cause reality.
5. **One variable per experiment.** Restore state between experiments. Log what was tried; the log becomes the journal entry.
6. **Bisect large search spaces** (commit history, pipeline stages, data ranges) instead of scanning linearly.

Stuck, intermittent, data-discrepancy, or process/human-system problems → `references/diagnosis.md` has the full toolkit (IS/IS-NOT tables, minimization, instrumentation tactics, domain playbooks, blame-aware questioning).

## Step 3 — Design the solution (SOLVE, and standalone for "which approach" decisions)

- T1: state the fix and its rollback in one line, then proceed.
- T2+: generate **at least two real options** before choosing. Decision research on hundreds of organizational decisions found roughly half fail, largely because a first idea was imposed without alternatives — and the Einstellung effect shows a familiar first solution actively blocks perception of better ones even while you believe you're still searching. For each option: effort, risk, reversibility, and whether it fixes the cause or papers over it. Steelman the runner-up before committing.
- Declare whether this is a **cause fix** or a **symptom mitigation**. Mitigations are legitimate — often correct mid-incident — but must be labeled as debt with the real fix noted in the journal.
- State the **rollback plan** before executing. If there is no undo, that fact alone may change which option is right, and it requires explicit user sign-off.
- Consequential or contested choice → `references/solutioning.md` (issue trees, tradeoff matrix, pre-mortem, decision record template).

## Step 4 — Execute and verify (SOLVE)

1. Execute in **checkpoints**: smallest meaningful change → verify → next. One concern per change; no batching of unrelated edits.
2. **Verification means evidence from the environment, never self-assessment.** Re-run the original failing reproduction — it must now pass. Recompute the number from source. Run the adjacent tests. Diff before/after. Quote the output.
3. The success claim is the Step 1 **success criterion passing, with evidence shown**. In a large 2026 study, 44–52% of agent task failures were confident false-success claims, and chain-of-thought reasoning provided no protection — traces rationalized completion rather than verifying it. Only environment state counts. If verification is impossible from here, say "unverified" explicitly and give the user the exact check to run.
4. After the fix passes: look one level out. Did it break anything adjacent? Was this the right level of abstraction, or a bandaid over something needing a deeper fix? If bandaid — say so and record the debt.

High-stakes changes, independent review, incident mode → `references/execution.md`.

## BUILD mode (compressed)

1. **Frame**: success criteria (what must be demonstrably true when done), constraints, and explicit non-goals. One line for small builds; a short brief for big ones. Ask the one or two questions that would change the architecture; default everything else and say which defaults you chose.
2. **Design**: for consequential builds, sketch two options briefly and choose with reasons (Step 3 machinery). Check whether something reusable already exists before writing new. Prefer boring, reversible foundations.
3. **Build in checkpoints**, each leaving something runnable or renderable — thinnest end-to-end path first, then flesh it out. Verify each increment; the `references/execution.md` discipline applies to creation, not just fixes.
4. **Verify the whole against the framed criteria with evidence** — run it, render it, feed it the edge cases you predicted in advance (empty input, malformed input, boundaries). "It should work" is not done. A big-bang build that is first tested at the end is the BUILD-mode equivalent of a confident false success.
5. **Handoff**: how to run it, what is deliberately not included, and the sharp edges.
6. **Record**: decision record for architecture choices; new project facts to `.fable/PROJECT.md`; a journal entry — build gotchas are among the highest-value lessons a journal collects.

Full protocol, framing template, and self-test discipline → `references/building.md`.

## ANSWER and GUIDE modes (compressed)

- **Answer the question asked, directly, in the first sentence.** Context after, and only if it earns its place.
- **Check the premise before answering.** If the question assumes something false ("since X removed Y, how do I…"), correcting the premise IS the answer. Answering a false premise literally is this mode's worst failure — it produces confident, well-written, wrong guidance.
- **GUIDE: check for the X-Y problem.** Users often ask how to execute their attempted solution, not their actual goal. Answer what was asked AND surface the more direct path in one sentence when you see one.
- **Calibrate explicitly, selectively.** Distinguish what is certain, inferred, or unknown — where uncertainty is real, not as reflexive hedging on everything. "This is contested" and "I don't know" are valid answers; manufactured certainty is not.
- **Verify volatile facts before asserting them**: versions, prices, incumbents, APIs, anything likely changed since training. Use search/tools when available; label the claim "unverified, as of <date>" when not.
- **Compute what is computable.** Run the arithmetic or the snippet instead of estimating it.
- **Fit the answer to their context** — their platform, versions, files, constraints. State the assumptions you had to make. For GUIDE, include how they'll know each step worked and the one or two pitfalls they will actually hit.
- **Record sparingly**: a user correction, a false premise you caught, a surprising or project-specific fact — those go in. Routine Q&A does not; padding the journal with trivia buries the signal.

Full treatment (premise taxonomy, verification triggers, how-to structure) → `references/answering.md`.

## Step 5 — Record

Mandatory for SOLVE (every tier) and BUILD. For ANSWER/GUIDE, record only when something journal-worthy emerged (see above).

Append to the global journal (`fable-memory/journal/YYYY-MM.md`). Never rewrite existing entries; append only.

T1 — one line:
`### 2026-07-07 | tags: <2-4 tags> | project: <name> | outcome: solved` + `problem → fix`

T2+, and BUILD — full entry (field rules in `references/memory.md`):

```markdown
### 2026-07-07 | tags: pdf-export, fonts | project: acme-reports | outcome: solved
- Problem: <symptom, with exact error text — the future search signature>
- Context: <environment, versions, conditions>
- Contributing cause(s): <what actually produced it>
- Ruled out: <hypotheses tested and killed>
- Fix: <what was done, precisely>
- Verified by: <the evidence>
- Lesson: [scope: <where this applies>] [confidence: high|med|low] <one generalized sentence>
```

(For BUILD entries: Problem = what was built and why; Contributing causes = key design decisions; Ruled out = rejected options.)

Then:

- **Update counters** on playbook bullets applied in Step 0: `helpful:+1` if it helped, `harmful:+1` if it misled. Honest accounting is the mechanism that keeps the playbook trustworthy — outcomes are free quality labels for stored lessons.
- **Promotion gate**: a lesson enters `playbook/` only if it is (a) not already there — grep first; (b) generalizable beyond this exact case; (c) evidence-linked to journal entries. A second occurrence of the same lesson is an automatic promote. Project-specific facts go to `.fable/PROJECT.md` instead — an over-generalized quirk is a future wrong answer. Controlled studies found agents that stored everything performed worse than agents that stored selectively.
- **Consolidation check**: if `INDEX.md` exceeds ~200 lines or a playbook file ~100 bullets, tell the user and offer to consolidate (protocol in `references/memory.md`). Consolidation is bullet-level surgery — merge duplicates, demote bullets with harmful ≥ helpful, archive stale months. Never summarize-and-replace a memory file: whole-file rewrites are the documented cause of catastrophic memory collapse.
- **No writable filesystem**: emit the entry as a copy-paste block and tell the user where to keep it (Project knowledge for the index and playbook; anywhere durable for the journal).

## Guardrails index

| Documented failure mode | Rule that blocks it |
|---|---|
| Acting before understanding | Step 1 / BUILD framing written before any work |
| First-hypothesis fixation (Einstellung) | ≥2 hypotheses in Step 2; ≥2 options in Step 3 and BUILD design |
| Confident false success (~half of agent failures) | Environment-evidence verification only; success = criterion passing |
| Big-bang build failing at the end | Checkpointed BUILD, thinnest end-to-end path first, per-increment verification |
| Answering a false premise literally | Premise check precedes the answer (ANSWER/GUIDE) |
| X-Y literalism (serving the ask, missing the goal) | Real-goal check in GUIDE |
| Stale facts asserted confidently | Volatile facts verified or explicitly labeled unverified |
| Thrashing / context self-pollution | Two failures → escalate tier and re-frame |
| Symptom patch at wrong level | Cause-vs-mitigation declared; bandaids recorded as debt |
| Re-learning lessons every session | Recall (Step 0) and Record (Step 5) are unconditional for SOLVE/BUILD |
| Memory collapse / bloat / staleness | Append-only journal, delta edits, gated promotion, capped index, counters |

## When NOT to run this protocol

Casual conversation, opinion, and creative back-and-forth — just talk. Single-step lookups where nothing rides on the answer — just answer well; the memory tax must earn itself. Artifact-format mechanics that a dedicated skill owns (docx, xlsx, pptx, dashboards) — use that skill, wrapped in BUILD-mode framing and verification when the build is substantial. Throwaway contexts (temp folders, one-off scripts, scratch work) get the discipline but not the infrastructure — no bootstrap pitch, no memory scaffolding, unless a keepable lesson actually emerged.
