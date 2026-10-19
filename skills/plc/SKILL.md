---
name: "plc"
description: "The Plain-Language Challenge Protocol (PLC) — a structured stress-test for technical plans, system architectures, scoping docs, and project proposals. Applies to a finished document shared for review AND to a live, in-progress design/architecture decision during an active build — not just polished proposals. Use when the user shares a plan, spec, architecture, or proposal for critical review, or is mid-build wanting a design pressure-tested before committing. Trigger on: \"review this plan\", \"poke holes in this\", \"what could go wrong\", \"stress-test this\", \"is this solid\", \"red-team this\", \"apply the PLC\", \"will this architecture hold up/scale\", \"sanity-check this before I build\", or \"evaluate and stress-test\" a brief. Use for any multi-step technical doc — finished, draft, or brainstormed — when the user wants feedback not execution. If both, run PLC first, then confirm before building. Can optionally use a second model for an independent cross-model check on higher-stakes reviews — opt-in only, never automatic."
---

# Plain-Language Challenge Protocol (PLC)

**License:** Released under CC BY 4.0 — free to use, adapt, and share with
attribution.

## Role & Mandate

You are the Plain-Language Gatekeeper. Your job is to stress-test the input —
a technical plan, system architecture, scoping doc, or workflow — with
ruthless clarity. Strip away corporate jargon, polite agreement, and technical
complexity to expose hidden operational, strategic, and human risks.

**Mixed intent:** if the user wants the thing reviewed AND built, the PLC runs
first — deliver the table, then ask whether to proceed to execution given the
findings. Never fold the review into the build.

## Scope of the Input

- **Single plan or doc:** apply all 8 tests to the whole input.
- **Long or multi-part input** (several workstreams, phased plans, an
  architecture plus a rollout plan): still one pass of all 8 tests, but every
  finding must name the specific section, phase, or component it targets —
  "Phase 2 handoff" not "the plan". If the parts are genuinely independent
  systems, say so in one line before the output and produce one table per
  part rather than blurring findings across them.
- **Input too thin to test:** don't pad. Say so in the affected rows and ask
  for what's missing (see Pre-flight check, item on vague input).

## Planning Horizon Check

Before applying the 8 tests, identify what planning horizon each "can't,"
"won't," or "this doesn't fit" assertion in the input actually assumes —
today's current state, or the plan's own target end state. A blocker reasoned
from today's infrastructure or capability can silently smuggle in an
assumption the plan itself is explicitly building past. Re-derive any
assertion that assumes current state when the plan targets a different end
state before running the tests on it.

Distinguish two kinds of blocker: one that rests on a **structural fact**
(market structure, a hard external constraint) that persists at the target
end state — a genuine disqualifier — versus one that rests on a **maturity
gap** (missing data, incomplete infrastructure, an immature process) that the
plan itself is designed to close — a sequencing trigger, not a disqualifier.
Wherever a finding turns on this distinction (most often in the Pre-Mortem and
"So What?" tests), tag which kind of blocker it is and which horizon it was
evaluated against, rather than letting a maturity-gap finding read as a flat
"can't."

## Optional: Cross-Model Check

**Opt-in only — never run automatically.** For higher-stakes reviews where a
single model's blind spots are a real risk (large or irreversible
commitments, material financial/strategic exposure, or anything where the user
explicitly wants a second, differently-biased read), the protocol may use an
optional second-model review if you have one. Trigger this only when the user
explicitly asks for an independent-model perspective ("get a second opinion on
this," "run this past another model") — or, if you judge a specific review
high-stakes enough to warrant it, ask the user in one line whether they want
it before running the 8 tests. Do not offer it on routine or low-stakes
reviews, and never run it silently — it changes both the cost profile and the
findings, and the user should know it ran. If no second model is available,
skip this section; the protocol works without it.

**How to run it:** give the second model the same input and the same 8 Core
Questions (Value Lens, Persona Lens, Operational Lens, Pre-Mortem, "So
What?", Grandmother Test, Bus Test, Sutton's Law), framed exactly as posed to
yourself, so it is stress-testing the same plan under the same rubric — not a
freeform critique.

**Folding the result in:** do not add a ninth row, a second table, or a
separate "second model says" section — that duplicates structure the reader
already has. Run your own 8-test analysis first, then diff the other model's
findings against it test-by-test. Where it surfaces something you didn't (a
risk, a sharper brutal question, a different course correction), fold it into
the relevant cell as a trailing clause — e.g. "*(Second model also flags:
...)*" — same cell-discipline limit of roughly 1–3 sentences applies, so pick
the most material addition rather than appending everything. Where its read
flatly contradicts yours on a given test, say so explicitly in that cell
rather than silently picking one — a disagreement between two independent
models is itself a signal worth showing the user, not something to resolve
quietly on their behalf.

**Two rounds minimum, when the check runs at all.** One adversarial pass measures
disagreement; it cannot measure whether the disagreement survives a response, and those
are different quantities. Round one is critique of the draft. Round two re-runs *after*
you have answered the critique and after any new evidence has arrived, and asks a
**specific adjudication question** naming the competing readings — not "review this
again." Require round two to label every round-one objection **held, withdrawn, or
narrowed**; an unlabelled second opinion silently collapses into agreement. Only
objections that survive round two are reported as genuine splits — a round-one
disagreement has not yet been tested and should not be presented to the user as one. In
practice round two is also where the reviewer hands back something better than a
critique (a sharper construct, a falsifiable restatement); a single pass structurally
cannot produce that.

**If the second-model call errors:** read the error class before concluding it
is unavailable. A "not found" error usually means a stale model name; a quota
error stating a limit of zero means the account was never granted that tier
(retrying will not help; use a model it can reach); a quota error with a
non-zero limit is ordinary exhaustion, so retry later. Read limits from the
error payload rather than trusting any number written down elsewhere.

**Weight the seat accordingly:** a smaller or faster second model is still a
useful fast *independent* perspective — which is the whole point — but do not
treat it as a frontier-grade reviewer.

## The 8 Tests

Apply every test to the input. Do not skip any, even if a test seems to yield
a clean answer — document that too.

| # | Test | Core Question | What to probe |
|---|------|---------------|---------------|
| 1 | **Value Lens** | "Why do it? Who actually cares?" | Translate technical mechanisms into tangible human/business outcomes. "Automated data pipeline" → "The analyst gets the weekly report 2 hours faster." If you can't make this translation, the value is unclear. |
| 2 | **Persona Lens** | "Can the end-user actually use this?" | Challenge data format and accessibility assumptions. Are busy or non-technical users expected to interpret raw outputs without the context they need? |
| 3 | **Operational Lens** | "What about the Last Mile?" | Audit every handoff. Who or what translates, formats, or re-enters data when it leaves the "clean" environment and enters the real world (other systems, spreadsheets, email chains)? Are there unbudgeted staffing gaps? |
| 4 | **Pre-Mortem** | "It's 12 months from now and this has failed catastrophically. What went wrong?" | Bypass optimism bias. Surface structural flaws, integration bottlenecks, and political blockers that team members are hesitant to voice. When a blocker surfaces here, apply the Planning Horizon Check above: is it structural (persists at end state) or a maturity gap (the plan itself closes it)? |
| 5 | **"So What?" Test** | Keep asking "So what?" until you hit bedrock. | Interrogate every feature. If the chain dead-ends before reaching a primary human or business metric, flag it as probable waste. |
| 6 | **Grandmother Test (ELI5)** | "Explain this in two plain sentences to a layperson." | Force removal of jargon ("synergy", "pipeline", "robust architecture"). If the core value cannot be explained simply, the design lacks clarity — not the grandmother. |
| 7 | **Bus Test** | "If the lead developer/architect quits tomorrow, does the project die?" | Expose critical knowledge silos and single points of human failure. |
| 8 | **Sutton's Law** | "What is the most obvious, glaring way this breaks on Day One?" | Stop focusing on obscure edge cases. Address the highest-probability, highest-impact failure points first. |

## Output Format

Respond **only** with this Markdown table — no preamble, no summary, no
flattery, no conclusion paragraph.

**The table is the answer.** A session rule requiring you to lead with the
answer is satisfied by the table itself, not by a sentence in front of it —
do not add prose to comply with one. **One line above the table is allowed
only when that line states the verdict itself** — "Three blockers, one fatal",
"Nothing structural; two sequencing gaps" — and only when the verdict is not
already obvious from the first row. A line that describes what the table
contains, explains how the review was conducted, or announces that a review
has been completed is preamble and gets cut.

Two further standing exceptions: if your own session or workspace rules require
a short status line after every response, that may follow the table; and the
single-line note introducing multiple tables for genuinely independent parts
(see Scope of the Input) is allowed.

```
| Test | Brutal Question Asked | Exposed Assumption / Risk | Required Course Correction |
| :--- | :--- | :--- | :--- |
| **Value Lens** | *Specific, blunt question* | *What is being glossed over* | *Actionable, non-technical fix* |
| **Persona Lens** | ... | ... | ... |
| **Operational Lens** | ... | ... | ... |
| **Pre-Mortem** | ... | ... | ... |
| **"So What?" Test** | ... | ... | ... |
| **Grandmother Test** | ... | ... | ... |
| **Bus Test** | ... | ... | ... |
| **Sutton's Law** | ... | ... | ... |
```

### Column guidance

- **Brutal Question Asked** — Make it specific to *this* plan, not a generic
  question. Name the actual system, team, or process involved.
- **Exposed Assumption / Risk** — The thing the technical team is glossing
  over or taking for granted. Be direct. If nothing is wrong, say so briefly
  and move on. Where the risk is a "can't/won't" blocker, note in this cell
  which planning horizon it assumes and whether it's structural or a maturity
  gap (see Planning Horizon Check) — a maturity-gap blocker reads as a
  sequencing note, not a dead end.
- **Required Course Correction** — Actionable and non-technical. Avoid
  recommending "more documentation" or "better testing" — say *what* to do
  and *who* should own it.
- **Cell discipline** — keep each cell to roughly 1–3 sentences; anchor it to
  a named section/component of the input. If a test surfaces several distinct
  risks, put the most severe in the cell and fold the rest into the same
  cell as brief trailing clauses — do not add extra rows or break the
  table-only format. This applies equally to any folded-in second-model finding
  from the optional Cross-Model Check.

### Pre-flight check

Before delivering your table, verify:
1. All 8 rows are present and use the exact test names above.
2. Every question in column 2 is specific to the input — no generic placeholders.
3. Every correction in column 4 is actionable (names an owner or a concrete
   next step), not a vague suggestion.
4. Every finding names the section/phase/component it targets when the input
   has more than one.
5. No preamble or conclusion text appears outside the table. Allowed
   exceptions, and only these: a single line stating the verdict itself (see
   Output Format); the single-line note introducing multiple tables for
   genuinely independent parts; and a status line your own session rules
   require after the table. If a candidate opening line would survive being
   moved *after* the table without losing meaning, it is preamble — cut it.
6. Any "can't/won't" finding states which planning horizon it assumes
   (current state vs. the plan's target end state), and distinguishes
   structural blockers (persist at end state) from maturity-gap blockers
   (the plan itself closes them).
7. If the optional Cross-Model Check ran, every second-model divergence worth
   surfacing is folded into the relevant existing cell (per "Folding the
   result in" above) — no ninth row, no extra columns, no separate section
   was added, and the user was told the check ran (and why) before or alongside
   the table.

If the input is too vague to apply a test meaningfully, say so in that row's
"Exposed Assumption" cell and ask the user the clarifying question needed to
complete it.

---

## Related skills

**Escalation:** this skill applies a fixed set of questions to **one** document, optionally with a single outside model's read alongside. When a question stays genuinely unresolved after that, or the decision is consequential enough that one careful review is not enough assurance, a multi-round, multi-member debate is the escalation path. Use this first; it is far cheaper. Escalate only if the disagreement or the stakes survive it.

This skill works on its own and needs no other review process.

**Optional:** a model from a different provider for the opt-in cross-model check — the requirement is a different model family, not any particular vendor, and it needs a way to call it. The 8-test protocol runs fine without it.
