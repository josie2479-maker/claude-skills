---
name: "plain-register-pass"
description: "Strip AI-essay voice from a document that will be shared outside this conversation — a memo, spec, brief, proposal, SOW, report, or any deliverable going to a supervisor, agency, client, or contractor. Use when asked to \"clean this up\", \"make this professional\", \"take the Claude out of it\", \"de-AI this\", \"make it ready to send\", or when a draft is about to be handed off and has not had a register pass. Also run it unprompted as the last step when drafting any external-facing document. Not for chat replies, personal notes, or internal scratch files — conversational voice is fine there and flattening it is a loss. Not for fiction or other creative writing, where the voice is the point; see the fiction note in the body."
---

# Plain register pass

**License:** Released under CC BY 4.0 — free to use, adapt, and share with attribution.

Rewrite a document into the register of its own document type. Same content, same structure, same findings. Only the prose changes.

The problem this solves: AI drafts arrive full of essay-voice emphasis. A reader who knows the genre spots it immediately, and the person sending it has to strip it by hand every time. A draft that needs a de-AI pass before it can be sent is not finished.

## 1. Establish the target register first

Ask, or infer from the document: **who receives this, and what kind of document is it?** A procurement document, a legal memo, a clinical protocol and a board update each have a native voice. Write in that voice, not in a generic "professional" one.

Default target for most business and government documents: flat, declarative, boring on purpose. **Claim, basis, consequence.** State a point once and do not restate it with more force. Prefer the shorter, duller word.

Working test: if a line would sound odd read aloud in a status meeting, rewrite it.

**Check for a reader-specific tells list.** If the recipient has said what reads as AI to them (for example, a reviewer who wanted the recommendation in the first sentence, or fewer tables), that list outranks the general lists below. Look for it in the project folder (a README, tracker, or notes file) before starting. If the user reports a new reaction from that reader, add it to the list.

**Fiction and creative writing: don't run the flattening pass.** "Boring on purpose" is the wrong target for a story. Use the tells lists in sections 2 and 2a only to spot AI habits, and keep the piece's own voice rules (a story bible or style notes) as the target register. If no voice rules exist yet, write them down with the user first.

## 2. Cut these

**Intensifiers used as emphasis** — genuinely, actually, honestly, truly, simply, "the real question", "the whole point".

**Verdict-adjectives smuggled in as description** — sharp, uncomfortable, honest, brutal, clean, elegant, stark, blunt, striking. If the adjective is the writer's opinion of the fact rather than a property of it, cut it.

**Metaphor doing the analysis** — load-bearing, the quiet part, says the quiet part out loud, wearing X's clothes, in disguise, blast radius, the tell, peeled off, the join, points back at. Replace with the literal relationship: "determines", "limits", "refers to", "indicates".

**Contrast couplets** — "It isn't X, it's Y." / "That's not X. That's Y." / "not whether A but whether B." / "Not just X, but also Y." / "No X, no Y, just Z." / the reversed form "Y rather than X" and "Y instead of X". Collapse to a single positive statement of what is the case.

**Diagnosis nouns** — the trap is, the failure mode is, the tell is, the main risk here is, the thing to notice. State the point directly instead of announcing that you are about to.

**Reader-nudging** — worth noting, worth your eye, here's the thing, notably, crucially, importantly, it's important to note/remember/consider, "and only then", "without ceremony", "if it is the finding". Say the thing; do not stage it.

**Self-certifying rigor** — words that assure the reader the writer was careful: honest, deliberately, not from memory, rather than silently omitted, carried rather than dropped. State the fact alone. A method note keeps the procedure ("each citation was checked against the current regulation on [date]") and drops the virtue. Also cut instructions addressed to a future drafter rather than the reader ("do not re-derive", "do not fill by inference").

**Em-dash asides carrying emphasis.** Convert to a clause, a separate sentence, or delete. An em-dash used for parenthetical information is fine; one used to land a beat is not.

**Rhetorical shape** — three-beat lists, escalating repetition, one-line paragraphs for effect, and any sentence whose only job is to make the previous sentence land harder. Delete that sentence.

**Decoration** — emoji flags, arrows, bolded exclamations. Keep the flag, drop the ornament: replace a ⚠️ with a plain bold label such as **Caveat.** or **Assumption.**

**Workspace artifacts** — references that only make sense inside the drafting setup:
- instruction or config file names (CLAUDE.md and similar)
- AI or assistant role labels ("Assistant (project lead)", "the AI")
- internal ticket or tracker tags (TASK#N style, decision-log IDs such as D7 or O12)
- local file paths
- provenance notes such as "per [name]" or "[name] [date]"
- chatbot leftovers: "I hope this helps", "Certainly!", "Of course!", "Would you like…", "let me know", "here is a…"

Remove them, or replace them with the reader-facing equivalent: the approving role, a decision-log reference, the document title. These hide in tables, footers, comments and source cells, so search the whole file, including inside Office files, not just the body text. If a tag is the only record of where a fact came from, keep the fact and flag the missing source instead of deleting the tag silently.

## 2a. Known AI tells (external checklist)

Primary source: Wikipedia, "Signs of AI writing" (WikiProject AI Cleanup advice page). Save a dated copy of the page (for example as a PDF) alongside your project notes so the list can be checked against what it said at the time; items below marked (W) come from it. Unmarked items come from general press coverage of overused chatbot words and are less well sourced.

Two cautions from the source apply to every pass:
- **One or two hits prove nothing.** These words and patterns co-occur; a cluster is the signal. Do not strip every instance of an ordinary word like "key" or "additionally" mechanically.
- **The signs point to deeper problems.** Puffed-up importance, vague attribution and trailing analysis usually mark a claim with no basis. Fix the claim (add the basis, narrow it, or cut it), not only the wording.

**Puffed-up importance (W)** — stands/serves as, is a testament/reminder, a crucial/pivotal/vital/significant/key role or moment, underscores/highlights its importance, reflects broader, symbolizing its ongoing/enduring/lasting, contributing to the, setting the stage for, marking/shaping the, represents/marks a shift. Also: plays a vital role, rich history, enduring legacy, paving the way for. State what happened and to whom.

**Trailing "-ing" analysis (W)** — a present-participle phrase attached to the end of a factual sentence: ", highlighting/underscoring/emphasizing…", ", ensuring…", ", reflecting/symbolizing…", ", contributing to…", ", cultivating/fostering…", ", encompassing…", ", enhancing…". Also "valuable insights", "align/resonate with". Cut the tail, or make it its own sentence with a basis.

**Promotional language (W)** — boasts, vibrant, rich, profound, enhancing, showcasing, exemplifies, commitment to, groundbreaking, renowned, featuring, diverse array, nestled, in the heart of. Also: seamless, cutting-edge, innovative, dynamic, transformative, game-changing, state-of-the-art, streamlined, empower. Replace with the measurable property or delete.

**AI vocabulary (W)** — Additionally (especially opening a sentence), align with, bolstered, crucial, deep dive, delve, emphasizing, enduring, enhance, fostering, garner, highlight (verb), interplay, intricate/intricacies, key (adjective), landscape (abstract noun), meticulous, pivotal, robust, showcase, tapestry (abstract noun), testament, underscore (verb), valuable, vibrant. Also: realm, navigate (figurative), leverage, holistic, multifaceted, nuanced, embark, elevate, harness, unlock.

**Avoiding plain "is" and "has" (W)** — "serves as a", "stands as", "functions as", "operates as", "marks", "represents", and "boasts/features/offers/maintains" where "is" or "has" would do. Also elaborate versions such as "began her career as" for "was". Use the plain verb.

**Vague connection (W)** — "connected with", "in connection with", "in association with", "associated with" in place of the actual relationship ("was CEO of", "taught at"). State the relationship.

**Vague attribution (W)** — industry reports, observers have cited, experts argue, some critics argue, several sources (when one is cited); also "studies show", "many believe". Name the source or cut the claim.

**Formula openers and closers (W, partly)** — "Despite its…, faces several challenges…", "Despite these challenges…", "Future outlook" endings, "In summary", "In conclusion", "Overall", a closing paragraph that restates the piece. Also: "In today's…", "When it comes to…", "Ultimately,", "from X to Y" ranges used for sweep, reflexive "While X, Y" openings, and paired "challenges and opportunities".

**Hedges about sources (W)** — "While specific details are limited…", "not widely documented/available", "based on available information", "in the provided sources". Either find the fact or mark the gap as a gap.

**Formatting habits (W, partly)** — boldface on every instance of a key phrase; inline-header lists (bullet, bold label, colon, text); title-case headings; emoji used as formatting; tables where prose would do; spaced em dashes used for punch; colon headers; a heading over every two paragraphs; uniform sentence length. Match the genre: a letter gets paragraphs; a project proposal may keep headings and a few labels.

**Do not use AI-detector sites** (GPTZero, Pangram and similar). The Wikipedia page warns against relying on them, they produce false positives, and pasting a draft into a public tool may break the user's workplace or client confidentiality or AI-use policy. Pattern review and the read-aloud test are the check.

## 3. Do not change

- Substantive claims, figures, citations, statutory or regulatory references, dates, names.
- Document structure, section order, headings.
- Placeholders, `[CONFIRM]` markers, gap lists, assumption flags, anything labelled speculative or constructed. Make these **more** clinical, not less visible.
- Ownership and authorship framing, including deliberately blank fields.

**The critical guardrail: strip the voice without softening the conclusions.** An unwelcome finding stated pointedly becomes the same finding stated plainly — it does not become an optimistic finding. "For most users the upgrade is no worse than before" becomes "For most users the expected outcome is no change", not "users will benefit". Blandness is the target for the prose, never for the substance. If a rewrite makes a document more comfortable to send, check whether a conclusion moved.

## 4. Standard terms are not metaphors

Some domain phrases look figurative but are the accepted term of art — "critical path", "carve-out", "runway", "single point of contact". Keep them and note the call. Rewriting a term of art into a description reads as unfamiliarity with the field.

## 5. Method

1. Back up the file before editing (a copy, or version control if the file is in a repository).
2. Rewrite. A full-file rewrite is appropriate here because the whole prose surface is the subject, but confirm this is wanted if a standing rule requires surgical edits.
3. Run a mechanical sweep: search the result for the words and patterns in sections 2 and 2a (including "rather than", "instead of", "serves as", em-dashes, and sentences ending in ", [verb]ing …") and fix every hit or note why it stays.
4. Read it as the named reader would. Any line that would sound odd in a meeting, or that matches the reader-specific tells list, gets rewritten.
5. Verify by reading the result back through a different path than the one that wrote it. Confirm section structure, every citation and every placeholder survived.
6. **Report the specific phrases cut, original → replacement.** This is the most useful output of the pass. It lets the reader judge whether the rule is catching the right things, and it teaches the pattern faster than the rewritten document does.
7. Flag any judgment call that could reasonably go the other way, so it can be reversed without re-reading the whole document.
