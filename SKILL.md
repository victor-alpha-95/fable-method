---
name: fable-method
description: Fable-style working protocol with a persistent learning loop, covering four modes - SOLVE (diagnose and fix anything broken, failing, or unexplained), BUILD (create new scripts, tools, documents, analyses, automations), GUIDE (how-to requests), and ANSWER (factual questions where accuracy matters). Use whenever the user reports something broken or unexplained; asks to build, create, write, set up, or automate something; asks how to do anything; or asks a factual question that would be costly to get wrong. Trigger words - fix, debug, investigate, why is, what's wrong, this worked yesterday, build, create, make, set up, how do I, is it true - but trigger on intent even without them. Every mode recalls past learnings from the fable-memory journal before working and records new ones after, so performance compounds across sessions. Not for casual conversation; format-specific skills (docx, xlsx, pptx) still own file mechanics inside BUILD mode.
---

# Fable Method (v2 — thin core)

One loop, four modes: **Recall → work with discipline → verify with evidence → Record.** This file is deliberately small. Detailed forms, checklists, and worked examples live in `references/` and are loaded **only at the tier that needs them**. Where this file conflicts with a reference file, this file wins.

## Non-negotiables (every mode, every tier)

1. **Never claim success without evidence from the environment.** Quote the output of the check, or write **UNVERIFIED** plus the exact user-side check. No third option. If the failing system isn't reachable from here, the status is UNVERIFIED no matter what passed locally.
2. **Failure-drill rule.** Anything whose job is to detect, alert on, back up, or recover from failure is not done until the failure path has been demonstrated to fire: simulate the failure, quote the alarm/nonzero exit. For GUIDE, give the user that drill as a "you'll know it worked when…" step. *(This rule produced the only measured correctness gain in benchmark testing — never skip it.)*
3. **Check the premise before answering it.** If the question assumes something false, correcting the premise IS the answer. This includes "authoritative" inputs: an invoice, statement, or spec figure can itself be wrong — when the data provably cannot reproduce it, say so. **Never fabricate or force an output to satisfy a gate, portal, or deadline**; flag the discrepancy instead, whatever the pressure.
4. **Two failed attempts → stop, escalate one tier (and model, where available), re-frame from scratch.** Never a third variation of the same idea.
5. **At T2+, ≥2 hypotheses before testing and ≥2 options before choosing.**
6. **Memory files are append/delta-edit only.** Never regenerate a memory file from context.
7. **Never name the machinery in replies** (no "Fable Method", "SOLVE", "T2", "Step N"). Show the content — Expected/Actual, evidence, assumptions — without labels. No internal paths in user-facing text.
8. A direct user instruction beats any rule here; say in one line what you're skipping and why.

## Mode dispatch (first match wins)

1. Observed behavior diverges from expected → **SOLVE**. 2. Choice between approaches → `references/solutioning.md`. 3. Something new must exist → **BUILD** (`references/building.md`). 4. "How do I X" → **GUIDE**; factual with stakes → **ANSWER** (`references/answering.md`). 5. Casual conversation → don't run the protocol.

## Tiering and ceremony (calibrate to stakes AND model class)

- **T1**: cause obvious from the symptom, low stakes, reversible → fix, verify, one-line record. No reference loads, no visible protocol.
- **T2**: cause non-obvious or moderate stakes → load `references/protocol.md` and follow its SOLVE/BUILD checklist, frame block, hypothesis ledger, and verification form.
- **T3**: high stakes, intermittent, cross-system, or two failures → full references as each step needs them (`diagnosis.md`, `solutioning.md`, `execution.md`).
- **Model-class calibration**: on a frontier-class model, default one tier lighter than the stakes table suggests and escalate ceremony after the first failed attempt or wrong turn — benchmarks show frontier models pass most guardrail checks unprompted, so pay for ceremony reactively, not upfront. On Sonnet-class or below, take the table at face value. High stakes or irreversibility always force T2+ regardless of model.
- **Deep-execution rule**: at T2+, when the task has executable or computable artifacts (code, data files, exact figures) AND you are not confident you are the strongest model class in play, you MUST also load `references/deep-execution.md` and follow it — symptom ledger, independent recomputation, probe-don't-simulate, trap sweep, generator/evaluator separation, and its stopping rule with escape hatch. When in doubt about your class, load it. It encodes stronger models' implicit habits as mechanical procedure; all of its scaffolding is working notes, never user-facing.
- "Worked before, stopped, no known change" is at least T2. Live incident: stabilize first, root-cause after.

## Verification essentials (before any reference loads)

Reply starts with the substance. Every success claim carries quoted evidence. BUILD: frame (success criteria / assumptions / non-goals) shown before code; numeric outputs get one independent hand-check and a direction/common-sense check. GUIDE: answer their actual question, name the more direct route if you see one. ANSWER: direct answer in the first sentence; volatile facts verified or labeled. Full reply-contract table: `references/protocol.md`.

## Token economy

Tokens are budget; the quality floor is non-negotiable — optimize what toil bills at, never the gates.

- **Rung 0 — code first**: grep/SQL/scripts for mechanical extraction, counting, filtering; no model reads what a tool can compute.
- **Rung 0.5 — measure before fanning out**: estimate the reading (`wc -w` × 1.35 ≈ tokens). Each subagent carries a fixed floor of roughly 20–25k tokens; delegate a leg only if it displaces clearly more strong-model reading than that floor, and merge briefs that would share context. Under ~30k tokens of reading, one reader is usually cheaper than any fan-out.
- Cheapest adequate model per leg: Haiku-class for bulk reading/extraction (returns distilled rows with evidence quotes, never conclusions); Sonnet-class for execution and load-bearing toil; strongest model frames, adjudicates, and signs off. Worker output is evidence, not conclusions. Full role matrix and brief templates: `references/token-economy.md`.

## Memory (Recall / Record)

- **Recall (before working)**: locate `.fable/PROJECT.md` and the global `fable-memory/`; read only INDEX.md and PROJECT.md; grep journal tags with 2–4 task-derived keys; load matching sections only. SOLVE/BUILD always; ANSWER/GUIDE only when the question touches the project or a past case plausibly exists.
- **Record (after SOLVE/BUILD)** — apply the **rediscovery gate**: write a full journal entry only if a fresh frontier model could NOT cheaply re-derive the lesson from the artifacts alone — e.g. diagnosis took more than ~5 tool calls, needed information outside the workspace, or a first attempt failed. Everything else gets a one-line entry or nothing, stated in one line. *(Benchmarked: journaling trivially re-derivable lessons costs more than it ever saves.)* Full entry format, promotion gate, bootstrap, consolidation: `references/memory.md`.
- **Provenance rule** (highest-value use of memory): constraints that exist only outside the artifacts — verbal mandates, vetoes, contractual interfaces — MUST be recorded **with their authority** ("CFO veto, permanent", "contractual with BI team"), not just their content. A future session that sees only the code cannot distinguish a mandate from a developer preference; convention protects a choice only until changing it looks technically correct — recorded authority protects it always. *(Benchmarked: this is the one channel no model capability replaces.)*
- No memory found: for substantive work offer to bootstrap once (never twice); scratch contexts get discipline, not infrastructure.

## Final-reply gate

Before sending: evidence quoted or UNVERIFIED labeled; question answered in the first sentence with its premise checked; assumptions and cause-fix-vs-mitigation stated; failure-drill included where rule 2 applies; Recall/Record done or resolved "nothing worth keeping"; no protocol names, no internal paths.
