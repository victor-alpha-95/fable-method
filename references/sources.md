# Sources

Provenance for the empirical claims made in this skill. Read only if verifying or updating a claim.

## Diagnosis & incident discipline
- Google SRE Book, Ch.12 "Effective Troubleshooting" — hypothetico-deductive loop, stabilize-first ("ignore that instinct" to root-cause during outages), bisection, "what touched it last", negative results, pitfalls (latching onto past causes, correlation≠causation). https://sre.google/sre-book/effective-troubleshooting/
- Google SRE Book, Ch.14 "Managing Incidents" — freelancing, role separation, incident state. https://sre.google/sre-book/managing-incidents/
- Google SRE Book, Ch.15 "Postmortem Culture" — blameless postmortems, action items; cites Dekker against single-root-cause. https://sre.google/sre-book/postmortem-culture/
- Zeller, *Why Programs Fail* — scientific debugging, TRAFFIC (reproduce/minimize) . Summary: https://queue.acm.org/detail.cfm?id=1217270
- Agans, *Debugging: 9 Indispensable Rules* — "quit thinking and look", guess only to focus the search, one change at a time.
- Kepner-Tregoe Problem Analysis — IS/IS-NOT differential specification.
- Allspaw, "The Infinite Hows" — criticisms of 5-Whys, how-vs-why questioning. https://www.kitchensoap.com/2014/11/14/the-infinite-hows-or-the-dangers-of-the-five-whys/
- InfoQ (2015) on 5-Whys non-repeatability (Card). https://www.infoq.com/news/2015/02/five-why/

## Solutioning
- Nutt, *Why Decisions Fail* / AoM studies of 400+ strategic decisions — ~half fail; imposed first ideas and limited search as the driver.
- Luchins (1942) water-jar experiments; Bilalić, McLeod & Gobet (Cognition, 2008) chess eye-tracking — Einstellung: first solution suppresses better ones. https://www.sciencedirect.com/science/article/abs/pii/S0010027708001133
- Klein, "Performing a Project Premortem" (HBR 2007); prospective-hindsight effect traced to Mitchell, Russo & Pennington (1989).
- Nygard, "Documenting Architecture Decisions" (2011) — ADR structure. https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions
- McKinsey-style issue trees / MECE — Conn & McLean, *Bulletproof Problem Solving*.

## Agent failure modes
- "False success" study (arXiv 2606.09863, 2026) — 44–52% of failures on tau2-bench single-control domains are confident false-success claims (75.8% AppWorld); reasoning traces rationalize rather than verify; external state verification reduces to ~3%. https://arxiv.org/abs/2606.09863
- MAST taxonomy (arXiv 2503.13657) — no/incorrect verification, premature termination rates in multi-agent systems.
- Anthropic, "Claude Code: Best practices for agentic coding" — explore→plan→implement→verify; evidence over assertion; context pollution after failed corrections. https://www.anthropic.com/engineering/claude-code-best-practices
- Anthropic SWE-bench writeup — wrong-level-of-abstraction fixes; repro-script-first scaffold. https://www.anthropic.com/engineering/swe-bench-sonnet

## Memory & learning loop
- Zhang et al., "Agentic Context Engineering" (ACE, arXiv 2510.04618, ICLR 2026) — itemized playbook bullets with helpful/harmful counters, delta updates, grow-and-refine; documented failure modes: context collapse (18,282→122 tokens in one rewrite, below no-memory baseline) and brevity bias. https://arxiv.org/abs/2510.04618
- Xiong et al. (arXiv 2505.16067) — experience-following; error propagation; selective storage beats add-all; future outcomes as free quality labels. https://arxiv.org/abs/2505.16067
- Shinn et al., "Reflexion" (arXiv 2303.11366) — verbal self-reflection stored and replayed. https://arxiv.org/abs/2303.11366
- Wang et al., "Voyager" (arXiv 2305.16291) — validated, compositional skill library. https://arxiv.org/abs/2305.16291
- Park et al., "Generative Agents" (arXiv 2304.03442) — retrieval by recency×importance×relevance; periodic reflection. https://arxiv.org/abs/2304.03442
- Anthropic, "Effective context engineering for AI agents" — attention budget, context rot, just-in-time retrieval, compaction recall-first. https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- Claude Code memory docs — index+topic-file architecture, ~200-line guidance. https://code.claude.com/docs/en/memory
- Vincent, "Superpowers" (2025) — subagent memory search to avoid context pollution. https://blog.fsck.com/2025/10/09/superpowers/

Caveats: the premortem effect size (~30%) is widely repeated but traces to one 1989 study; treated qualitatively here. Xiong's headline accuracy numbers come via secondary analysis; treated directionally. ACE/Reflexion gains were measured on repeated same-distribution benchmarks; transfer magnitude to heterogeneous personal workflows is unmeasured.
