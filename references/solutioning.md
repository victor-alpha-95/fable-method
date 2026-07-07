# Solutioning toolkit

Read this file when: the fix choice is consequential or contested, the problem is fuzzy enough that options aren't obvious, or the user asks "which approach should I take" (this file works standalone for that).

## Why two options minimum is non-negotiable

Studies of 400+ organizational decisions found roughly half failed, and failure concentrated where a decision-maker imposed the first idea and limited the search for alternatives. Separately, the Einstellung effect (demonstrated from 1942 water-jar experiments through modern chess eye-tracking) shows that a familiar first solution doesn't just win by default — it actively suppresses perception of better solutions, while the solver sincerely believes they are still searching. You cannot introspect your way out of this; the only reliable countermeasure is mechanically producing a second real option before evaluating the first.

"Real option" means something you could actually defend choosing. A strawman alternative generated to make the first idea look good does not count and is worse than nothing, because it manufactures false confidence.

## Decomposing fuzzy problems: issue trees

When the problem is a fog ("sales are down", "the team is slow", "this system is unreliable"), decompose before solutioning:

1. State the question precisely enough that an answer would be recognizable.
2. Split into branches that don't overlap and roughly cover the space (MECE as a direction, not a religion — 80% coverage with clean branches beats a perfect taxonomy).
3. Recurse until leaves are individually testable with available data.
4. Kill branches with quick evidence checks before going deep on any.

Leading with a hypothesis ("I bet it's the checkout flow") is fine and fast — but then the tests must be designed to *disconfirm* it, and one sibling branch must be checked as a control. Hypothesis-led without disconfirmation is just confirmation bias with a project plan.

## Generating the option set

For most fixes, the honest option set includes:

1. **Cause fix** — remove the contributing cause.
2. **Mitigation** — reduce the symptom's impact without touching the cause. Legitimate, often right mid-incident or when the cause fix is expensive; must be labeled as debt with the cause fix noted.
3. **Do nothing** — always price it. Sometimes the problem costs less than any fix; saying so is a valid recommendation.
4. **Probe** — when in genuinely unknown territory, a small reversible experiment that generates information beats committing to any full option. Prefer probes whenever uncertainty is high and probes are cheap.

## Choosing: screens, then weights

1. **Musts first**: hard constraints (budget, deadline, compatibility, policy). Options failing a must are out regardless of how well they score otherwise — this prevents a high total score from smuggling in a fatal flaw.
2. **Wants, weighted**: effort, risk, reversibility, blast radius, time-to-relief, maintenance burden. Score the survivors. The matrix's value is exposing *why* an option wins, not the arithmetic; if the numbers contradict your gut, the disagreement is the finding — investigate it.
3. **Reversibility gets extra weight when uncertainty is high.** An undoable choice can be wrong cheaply. If the leading option is irreversible and confidence is moderate, prefer the reversible runner-up or a probe, and get explicit user sign-off before any irreversible step.

## Steelman the runner-up

Before committing, write the strongest honest case for the second-place option — the case its most competent advocate would make. If you can't write a convincing one, you don't yet understand the options well enough to choose. If the steelman turns out more convincing than expected, that is the process working, not a delay.

## Pre-mortem (before executing consequential plans)

Assume the chosen fix has already failed, spectacularly, months from now — and write the story of what went wrong. Prospective hindsight ("it HAS failed, explain why") measurably surfaces more failure modes than asking "what could go wrong", because it converts loyalty-to-the-plan into narrative imagination. Take the top two failure stories and add concrete mitigations or early-warning checks to the execution plan. Five minutes, every T3 plan.

## Decision record

For consequential choices, append to `.fable/PROJECT.md` (or the journal entry for cross-project decisions):

```markdown
## DR-003: <title>  (2026-07-07)
Context: <the neutral facts that force a decision>
Options: A) <one line + key tradeoff>  B) ...  C) do nothing — <its cost>
Decision: <chosen> — because <the two or three reasons that actually decided it>
Accepted consequences: <the negatives being knowingly accepted>
Revisit if: <observable conditions that invalidate this decision>
```

The `Revisit if` line is what makes the record useful later: it converts a static justification into a tripwire. Decisions whose rationale has evaporated shouldn't keep governing behavior silently.

## Right level of abstraction

Before finalizing: is this fix located where the defect lives, or where intercepting it was cheapest? Both can be correct — but say which, out loud. The documented agent failure here is the bandaid applied at the symptom site when the structure needs the repair; the equally real opposite failure is the "proper" refactor nobody asked for when a two-line patch serves. The tiebreaker is recurrence: symptoms that will recur elsewhere justify fixing the level below; genuine one-offs justify the patch.
