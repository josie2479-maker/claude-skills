---
name: "grilling"
description: "Interview the user about something they are ABOUT TO BUILD, before any of it is built — a feature, a system, an automation, a workflow, a document structure. Use when a plan exists only in their head or as a rough idea and the design decisions are still open: \"grill me\", \"interview me about this\", \"help me think this through before I build it\", \"what am I not deciding\", \"ask me what you need to know\". Works the design as a dependency tree, asking one answerable question per round (the one that unblocks the most decisions first) with a recommended answer attached, and never asking anything it could look up itself. BOUNDARY — this is front-loaded, for decisions not yet made. Reviewing a plan that already exists and stress-testing it for holes is the plc skill. Interrogating information someone else handed you is critical-thinking-walkthrough. If the thing is already written down and the question is \"is this any good\", it is not this skill."
---

# Grilling — the pre-build interview

**Adapted from `grilling` by Matt Pocock** — [github.com/mattpocock/skills](https://github.com/mattpocock/skills). Original © 2026 Matt Pocock, MIT License (see LICENSE in this folder). Changes in this copy are released under the same MIT License.

The description is narrowed so this skill owns the pre-build interview and does not compete with `plc` (review of an existing plan) or `critical-thinking-walkthrough` (interrogating someone else's material).

Interview the user relentlessly until you reach a shared understanding of
what they are about to build. Map it as a **design tree**: every decision
branches into the decisions that hang off it.

## Rounds and the frontier

Work the tree in **rounds**. The **frontier** is every decision whose
prerequisites are already settled — the questions you can ask *now* without
guessing at answers you haven't heard yet.

Ask **one question per round** by default: the frontier question whose answer
unblocks the most downstream decisions, with your own recommended answer.
Then stop and wait for the user's answer before the next round. If the user
says "batch them", ask the whole frontier in one round instead: number each
question and give your recommended answer for each.

Format a question like this (in batch mode, repeat the block for each
numbered question):

```
❓ **Q1** — **<question title>**: <question body, may be several paragraphs,
may offer multiple choices>

➡️ <your recommended answer>

---

❓ **Q2** — **<question title>**: <question body>

➡️ <your recommended answer>
```

Each round of answers reshapes the tree: settled decisions push the frontier
outward and unblock questions that depended on them. Recompute the frontier
and ask the next round.

A question whose answer depends on another question still open in this round
belongs to a **later** round, not this one. Asking everything at once loses
the dependency structure that makes the interview converge.

## Facts are your job, decisions are theirs

**Never ask the user something you could find out yourself.** If a frontier
question needs a fact from the environment — what a file contains, what
already exists, what a tool supports, what a service currently costs —
find it. Dispatch a sub-agent, read the file, run the search.

Don't block on it. A running exploration is an unsettled prerequisite, so
only the questions downstream of it wait; keep asking from the rest of the frontier now.

The **decisions** are the user's. Put each one to them and wait. Do not
decide on their behalf because waiting is slower.

## Ending

The session is done when the frontier is empty — every branch visited,
nothing left silently assumed.

**Do not start building until the user confirms you have reached a shared
understanding.** Reaching the end of the questions is not the same as being
told to proceed.

## Scope

This is for a design that does not exist yet. If the user brings a written
plan and asks whether it holds up, that is a review, and `plc` is the skill
for it. If they bring something someone else wrote and want to interrogate
it, that is `critical-thinking-walkthrough`. Hand off rather than
half-running the wrong interview.

