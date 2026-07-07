# BUILD — greenfield discipline

Read this file when: building anything non-trivial from scratch — a script, tool, document system, analysis, automation, app. The through-line: a build is a chain of claims ("this will do X"), and the discipline is converting each claim into evidence as early as possible instead of all at once at the end.

## 1. Frame before building

For small builds, one line: "Done means: <observable outcome>." For anything bigger, a four-line brief (show it to the user):

```
Success criteria: <what must be demonstrably true when done — testable, not vibes>
Constraints:      <platform, deps, deadline, budget, compatibility>
Non-goals:        <what this deliberately does NOT do>
Assumptions:      <the defaults chosen where the user didn't specify>
```

- **Success criteria** are the contract Step "Verify" is graded against. "A script that splits expenses" is not a criterion; "given expenses.csv, prints each person's balance and the balances sum to zero" is.
- **Non-goals are scope armor.** Most build failures are drift: each "while I'm at it" addition is individually reasonable and collectively fatal. When mid-build you want something outside the non-goals, note it in the handoff instead of building it.
- **Question budget: the one or two questions whose answers change the architecture.** Everything else gets a sensible default, stated explicitly in Assumptions so the user can veto cheaply. Twenty questions before writing anything is as much a failure as zero.

## 2. Design

- **Reuse check first**: does a library, an existing script in this project, a standard tool, or a past build (grep the journal and `.fable/PROJECT.md`) already do this? The best code is the code not written; the second-best is boring.
- **Two options for consequential builds** — same Einstellung logic as SOLVE Step 3: the first design that comes to mind actively blocks better ones. A two-line sketch of each and a reason for the choice is enough; a full tradeoff matrix only for genuinely contested calls (`references/solutioning.md`).
- **Prefer reversible foundations.** File formats, schemas, and public interfaces are expensive to change later; internal implementation is cheap. Spend design attention proportionally.
- **Architecture decisions get a decision record** (template in `references/solutioning.md`) in `.fable/PROJECT.md` — future sessions inherit the *why*, not just the code.

## 3. Build in checkpoints — walking skeleton first

Build the thinnest end-to-end path before fleshing anything out: input → core transformation → output, minimal but real. Then iterate in increments that each leave the build runnable.

Why this order is non-negotiable: the walking skeleton surfaces the integration surprises (encoding, formats, permissions, API shapes) in minute ten instead of hour three, while the code is still small enough to change cheaply. A big-bang build tested only at the end is BUILD mode's version of the confident false success — everything "should work," and the first run is an archaeology dig.

Per increment: one concern, verify it does what the increment claims (run it, render it), then next. Keep the running change log — it becomes the journal entry.

## 4. Verify against the frame — with predicted edge cases

1. Re-read the success criteria from the frame. Check each one with evidence: run the command, render the document, execute the query. Quote outputs.
2. **Predict edge cases before testing, then test the predictions**: empty input, missing file, malformed row, boundary values (zero items, one item, exactly-at-limit), duplicate keys, unicode, the participant with no data. Predicting first matters — testing only what you happen to try tests your habits, not the build.
3. Numeric outputs get an independent check: recompute a case by hand or by a second method, compare exactly. Rounding, off-by-one, and accumulation errors survive casual inspection.
4. If a criterion can't be verified in this environment, mark it explicitly: "unverified — run <check> to confirm." Never present an unverified build as done.
5. If verification reveals the design was wrong: that's SOLVE mode now — frame the divergence, don't patch blindly.

## 5. Handoff

Every non-trivial build ships with (in the final message or a README, proportional to size):

- **Run it**: the exact command / open-this-file, with expected output.
- **Not included**: the non-goals and anything discovered-but-deferred, so absence reads as a decision rather than an oversight.
- **Sharp edges**: the two or three ways this will bite (input assumptions, scale limits, environment deps), each with its symptom.

## 6. Record

- Decision records for architecture choices → `.fable/PROJECT.md`.
- New project facts (layout, conventions, environment quirks discovered) → `.fable/PROJECT.md`.
- Journal entry for the build (BUILD mapping: Problem = what was built and why; Contributing causes = key design decisions; Ruled out = rejected options; Verified by = the evidence from step 4). Build gotchas — the library that lied, the format that wasn't what docs said, the platform quirk — are among the highest-value playbook candidates the journal ever collects, because they repeat across projects.

## Interplay with other modes and skills

- User asks to build but the real content is choosing an approach → start in `references/solutioning.md`, build after the decision.
- Building surfaces a defect in something that already exists → SOLVE protocol for that defect, then resume.
- A dedicated format skill exists for the artifact (docx, xlsx, pptx, dashboards) → that skill owns the file mechanics; this mode still owns the frame (criteria, non-goals), the verification, and the record. The two compose — frame first, delegate mechanics, verify the product against the frame.
