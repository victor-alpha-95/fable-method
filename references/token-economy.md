# Token economy — model roles, delegation mechanics, cost accounting

Read this file when: orchestrating subagents, deciding what to delegate, writing worker briefs, choosing which model class handles a leg of work, or asked to reduce token spend.

The governing rule, restated: **quality floor first, rate second.** Every gate in SKILL.md (Reply contract, verification form, checklists) binds regardless of which model does the work. Token optimization means moving *toil* to cheaper rates — never moving *judgment* below its minimum adequate tier. A wrong cheap answer costs more than the delta to the right model.

## Contents
- Model ladder and role assignment
- Worker brief template
- Report-back contract
- Advisor consultation protocol
- When NOT to delegate
- Cost accounting
- Platform mapping
- Failure modes this layer guards against

## Model ladder and role assignment

Four classes, cheapest-adequate wins. Concrete names as of this writing (verify current lineup/pricing on the platform pricing page): Haiku 4.5 → Sonnet 5 → Opus 4.8 → Fable 5.

| Role | Minimum class | Owns | Never does |
|---|---|---|---|
| Reader / searcher | Haiku | Web/doc/log reading, coverage sweeps, extraction, format conversion | Judgment calls; user-facing claims; numeric logic that changes outcomes |
| Executor | Sonnet | Running commands/tests, coding, mechanical edits, standard analysis, numerically load-bearing toil, memory greps | Deciding what "done" means; contested adjudication |
| Judgment-heavy executor | Opus | BUILD work where assumptions/sign conventions/split logic change the output; subtle-material reading (contract clauses, nuanced docs) where a cheap reader would summarize away what matters | — |
| Fresh-context reviewer | Opus | Independent review of high-stakes changes (two questions: criterion met? adjacent breakage?) — see `execution.md` | Fixing what it reviews |
| Advisor | Opus first, frontier if stakes are T3 or Opus-level advice failed | One-shot steering at the frame or the contested decision | Execution; per-step consultation |
| Orchestrator | Strongest in session | Decomposition, adjudication, synthesis, verification sign-off, memory Record | Bulk reading it could have delegated |

Assignment heuristics:

- **Rung zero is code, not a model.** Mechanical extraction, counting, filtering, and reconciliation go to deterministic tools (grep, SQL, scripts) — zero token cost, exact results, auditable. A model (of any size) enters only where language understanding is genuinely required. Observed in this skill's own evals: a shell-tool sweep of 16,000 log lines beat any delegation plan on cost *and* accuracy.
- **Coverage-shaped work** (verify N facts, sweep M files, check every X) is the strongest delegation case — the reading is mandatory and parallelizable; only the rate is negotiable. Reference pattern measured ~2.5× cheaper, ~3× faster, 84–98% of input tokens at worker rate.
- **Discovery-shaped work** (find one needle in a big space) rewards frontier search intuition; the gap narrows — delegate less, or run one strong searcher instead of many cheap ones.
- **Subtle-judgment work** stays at Opus-or-above even when token-heavy: a cheap reader returns a summary that is confidently missing the point, and no downstream gate can recover information that was never extracted.
- **Numeric or semantic direction matters** (who owes whom, sign conventions, inclusion rules) → Sonnet minimum for execution, and the orchestrator hand-checks one case independently (SKILL.md BUILD rule) regardless of who executed.
- When genuinely unsure between two tiers, take the higher one. The failed-verification cost of guessing low (re-run + escalation + polluted context) exceeds the tier delta almost always.

## Worker brief template

One brief per independent, self-contained sub-question. In order:

1. Role + scope: "You handle exactly one sub-task for a coordinator."
2. The sub-task, fully self-contained — the worker sees nothing else, including other workers' outputs.
3. Source/tool constraints ("official pages only", "read-only", "this directory only") — minimum toolset is both the cost control and the security blast-radius boundary for untrusted content.
4. Report format: findings first; evidence pointers (URLs, quotes, file:line), not payloads; explicit uncertainty — "if not definitive, state what you found and what remains unknown."
5. Failure behavior: report infrastructure errors as errors, never as findings.

Anti-patterns: briefs sharing most of their context (merge them — each worker thread has a fixed setup floor; over-sharding measurably *raises* the bill); briefs that need another worker's output (sequence through the orchestrator); briefs that delegate the decision itself.

## Report-back contract

Workers return: direct answer → evidence pointers → confidence and gaps. Nothing else; no raw material. The orchestrator treats reports as evidence to weigh — workers can be confidently wrong, and Step 4 sign-off is not delegable. On infrastructure failure: re-assign to a fresh worker. On substantive failure (wrong, incomplete, failed verification): escalate the model class, don't re-brief the same tier.

## Advisor consultation protocol

When you are the executor with stronger models available:

- Default budget: one consultation per task — at the frame (T2+/BUILD) or the contested Step 3 decision, whichever is riskier.
- Bring a decision package: the frame, the ledger or options with your lean, the single question. Never the transcript — the advisor bills on everything you send it.
- Cascade the advisor itself: Opus-class first; frontier only for T3 stakes or when Opus-level advice failed the escalation rule.
- Apply the advice yourself. A second consultation is justified only by two failed attempts or a failed verification (Non-negotiable #4).

## When NOT to delegate

- **Narrow tasks**: little to read, one context — the delegation floor cost exceeds the saving.
- **Frontier-judgment material**: subtle analysis rather than fact-finding.
- **Sequential chains**: if step N+1 needs step N's full context, splitting pays for the context twice.
- **Anything requiring a judgment call to complete** — the worker will make it anyway, silently.
- Post-hoc check: if an orchestrated run ends with no delegations, the frontier round-trip bought nothing — next time route the whole task to an executor-class session.

## Cost accounting

- Attribute spend per delegation (per-thread usage where the platform exposes it), not per session.
- Cache economics: reads bill ≈0.1× the input rate; 5-minute cache writes 1.25×; 1-hour writes 2×. Levers: stable context (instructions, project files) at the front of prompts; reuse existing agent threads for follow-ups instead of re-briefing fresh ones — a persistent thread retains its context and its cache.
- Post-run sanity check for orchestrated work: what share of input tokens billed at worker rates? The measured reference pattern ran 84–98%. A low share means raw material leaked into the orchestrator's context — restructure next time.
- Journal the surprises: "N-brief split cost more than the saving," "cheap reader missed the load-bearing clause" — both are promotable lessons with scope tags.

## Platform mapping

- **Claude Code / Cowork**: Agent (Task) tool with an explicit `model` override per delegation; read-only Explore-type agents for searches; continue a previously spawned agent by ID for follow-ups (cache-friendly). The orchestrator is the main thread.
- **Claude API (Managed Agents)**: `multiagent` coordinator with a worker roster — depth 1, ≤20 roster agents, ≤25 concurrent threads; the roster is snapshotted and the coordinator cannot see worker prompts, so describe worker behavior accurately in the coordinator's own prompt.
- **claude.ai chat**: no subagents — apply the advisor pattern manually (draft at low effort, review hard against the gates) and the memory grep discipline; the delegation machinery is inert here.

## Failure modes this layer guards against

| Failure | Guard |
|---|---|
| Frontier model doing bulk reading | Orchestrator role rule; raw-material boundary |
| Quality traded for rate | Quality-floor rule: gates bind regardless of executor; minimum class per role |
| Over-sharding (floor cost × N briefs) | Merge-briefs rule; delegation check |
| Audited facts on an unverified decomposition | Decomposition verification before fan-out |
| Worker's confident error becomes the answer | Report-back contract; non-delegable Step 4 sign-off |
| Advisor used as a chat partner | One-consultation budget; decision-package format; advisor cascade |
| Retry-thrash on a cheap model | Substantive failure escalates the model class |
| Cache-hostile prompting | Stable-prefix rule; thread reuse over fresh spawns |

Provenance: the measured figures (advisor ~92% of frontier score at ~63% of price; orchestrator 96% at 46%; 2.5×/3×/84–98%) come from Anthropic's published Fable 5 patterns and the `plan_big_execute_small` cookbook; the ratios are single-benchmark samples — the *structures* are the stable part. Full citations in `references/sources.md`.
