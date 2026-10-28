---
name: "resumable-web-index-crawl"
description: "Build a resumable link index of a large multi-page site (docs portals, API references, standards libraries, help centers) without bulk-downloading its documents. Use for \"crawl this site\", \"index these manuals\", \"map out this portal\", \"list every document on this page\", \"catalog all the API endpoints\", or any crawl that may outlast one session. Prefer over ad-hoc fetching when the goal is a durable catalog. Also turns a borrowed or licensed ebook into paraphrased, section-numbered reference notes before access ends."
---

# Resumable Web Index Crawl

**License:** Released under CC BY 4.0 — free to use, adapt, and share with
attribution.

Distilled from multi-session work cataloguing large documentation sites — the
kind that run to hundreds of versioned guides, references, and release notes —
into an index you can query selectively instead of re-crawling every time.

---

## What this skill is for

The goal is a **durable, navigable catalog** of what a large site contains —
one link per document, with names, URLs, formats, dates, and topic tags — so a
human or a later task can retrieve *specific* documents on demand, or find
every item touching a topic without re-reading the whole catalog. The catalog
is the product. The crawl session that builds it is disposable.

The failure mode this skill exists to prevent: a well-intentioned crawl that
tries to download everything, blows past a session/context limit halfway
through, and leaves nothing reusable behind — forcing a full restart. Left
unchecked, an open-ended crawl can also grow large enough to crash the fetch
tool, the browser, or the host session itself — that has happened in
practice, which is why a **hard per-session fetch cap** (see "Session limits
and multi-session planning" below) is a mandatory, enforced part of this
skill, not optional advice. The antidote is three commitments held together:
**index, don't download**; **breadth-limited depth**; and **append-only
progress tracking** so any interruption resumes from exactly where it
stopped — plus the hard session cap that makes sure a crawl always stops on
its own terms instead of the tool's or the machine's.

Use it when the target is many pages / hundreds of links, when the site may be
rate-limited or flaky, or when the work plausibly won't finish in one sitting.
For reading a single known page, this is overkill — just fetch that page.

## The three core commitments

### 1. Index, don't download

Capture *metadata about* each document, never the document itself. For every
item, record: name, URL, format (HTM/PDF/xls/…), any date/version shown on
the listing page (issued, revised, effective, replaced), — when the source
provides one — its own **native reference identifier** as printed on the
document or the listing page (e.g. "Chapter 7," "Section 4.3," "Release
4.2"), and **2-4 short topic tags** capturing what the item is actually
about (e.g. `authentication, rate-limits, errors` or `migration, deprecation, breaking-changes`).
Use the native identifier, not crawl-discovery order, as the item's primary
key and sort order; fall back to crawl-sequence position only when the source
has no native identifier of its own. Downloading the actual files is a
separate, later, *targeted* step the human authorizes once they see the index
— it is not part of building the catalog.

Why: the whole point is to avoid pulling gigabytes of documents the user may
never need. The index tells them what's worth retrieving; retrieval is cheap
and precise once you know the target. A crawl's own discovery order is an
artifact of how the crawler happened to traverse the site, not a fact about
the source — treating it as the primary key silently substitutes the tool's
bookkeeping for the source's actual structure, and the substitution is
invisible until someone cross-checks a printed document against the index by
hand.

Tags exist for a related reason: a title plus a prose relevance note is
readable, but it only scales by *reading* — opening each index and scanning
it. A short, consistent tags field turns "which indexes touch topic X" into a
single grep across the whole reference folder. Draw tags from the item's own
content (what it's actually about), not from the crawl's discovery path or
the downstream project's priority labels — those already live in the priority
flag and relevance note. Keep tags short (single words or short phrases,
comma-separated) and consistent in style within one crawl so they're
grep-friendly.

**Two tag tiers.** The tags described above — written at crawl time from a
title/TOC-level read — are **topic tags**: cheap, inferred, a best guess at
what an item is about. They are not a substitute for having actually read the
item. A second, separate tier — **verified tags** — exists for after someone
has actually done a full-text read of the source, for any reason (a
downstream question, a deeper follow-up dig, unrelated research that happened
to fetch the same URL). Verified tags carry more weight precisely because
they're expensive to earn; keep them visually distinct from topic tags (a
separate column) so nobody mistakes an inferred guess for a confirmed
finding. See "Verified tags and the update-on-read rule" below.

**Priority flags are a reading-order call, not a relevance verdict.** A
section flagged low priority or "link only" was not read, so the index
cannot say it holds nothing on a topic. State this in the index header. When
someone later uses the index to map a citation or requirement to the source
("which items cover X?"), search every section, including low-priority and
link-only ones; otherwise a skipped section shows up as a false "not
covered" gap.

### 2. Breadth-limited depth (one level down, by default)

Fetch the start page and enumerate every link. Where a link is a **category /
landing page** (a list of more links, not a document), follow it **one level
down** to capture the items it lists — then stop. Do not recurse arbitrarily.

- A landing page that lists documents → go in, list them, come back.
- A landing page that lists *more landing pages* → capture the sub-landing
  names + URLs, but treat going deeper as a **separate, explicitly-flagged
  follow-up pass**, not something you do inline.
- A huge flat list of dated files (e.g. 800 dated release notes) → capture the
  category, a **count**, and the **date range**, plus a few representative
  entries — not every line. Itemizing a thousand near-identical rows burns the
  session for near-zero added value; the count + range is what a human needs to
  decide whether to dig in.

Why one level: depth is where crawls explode. A fixed, shallow default keeps
the pass finite and predictable. Deeper digs are valuable but should be *chosen*
by the human against the first-level index, not stumbled into.

### 3. Append-only progress tracking (the resumability contract)

Maintain **two artifacts that only ever grow**:

- **A crawl log / tracker** — one line per unit of work, appended the moment
  that unit finishes: `- [item name] | [URL or "main page only"] | [N links] |
  done` (or `| FAILED: reason` / `| PARTIAL: where it stopped`). For a planned
  set of deeper crawls, use a **checklist table with a Status column** you flip
  from `PENDING` to `DONE` per row.
- **The index deliverable itself**, written **section by section** — header and
  skeleton first, then one section appended per completed unit.

The rule that makes interruption a non-event: **the log/tracker is the single
source of truth for what's done.** At session start, read it and skip anything
already marked done. If the session dies mid-crawl, the next session resumes
from the first non-`done` row with zero re-derivation.

Why append-only: a deliverable rewritten wholesale at the end is lost entirely
if the run is cut short. A file that grows per-item preserves every completed
unit. Write progress *as you go*, never batched at the end — a mental note of
"I finished that one" does not survive a crash.

## Session limits and multi-session planning

A crawl session has a **hard, enforced ceiling: 50 page fetches per session**
(counting every page load — the start page, every landing page, every
sub-page). This is not a soft guideline; treat it the same way you'd treat a
rate limit. The moment the 50th fetch completes (or a session has run past
~20 minutes of active fetching, whichever comes first — slow/flaky sites can
hit a time problem well before a count problem), stop fetching immediately,
even mid-unit:

1. Finish logging whatever unit is in progress — mark it `DONE`, `PARTIAL`
   (with where it stopped), or `FAILED`. Never leave an in-flight fetch
   unlogged.
2. Append a `SESSION-END` line to the tracker with a timestamp and a one-line
   note of exactly where the next session should resume.
3. End the session there. Do not rationalize "just a few more pages" — the
   uncapped "just a bit more" instinct is precisely what produces sessions
   long enough to crash the fetch tool, the browser tab, or the host process.
   The cap is what makes the resumability contract in commitment 3 actually
   load-bearing instead of theoretical.

**Plan large crawls as multiple sessions from the start, not as one long
push.** Before starting, estimate scope from the start page (link count,
number of landing pages implied). If the estimate is north of ~100 pages,
say so up front and lay out a multi-session plan rather than attempting it in
one sitting — e.g. "this looks like ~300 pages across the site, so I'll plan
on roughly 6 passes of 50 pages each." Concretely:

- For crawls expected to span more than one session, register a follow-up task
  (daily or every-other-day is typical) that reads the tracker and resumes from
  the first non-`done` row, running to the next 50-page cap each time. Use
  whatever scheduling the tool you are running in offers (a scheduled task, a
  cron-style routine). If there is no scheduling tool, write the task prompt
  to a file and hand off registration to the user.
- Every pass — scheduled or manual — starts with the Resume Check (Workflow
  step 1) before touching anything, so a follow-up session never re-does
  completed work.
- If delegating to subagents (see "Delegation for lean, long crawls" below),
  the 50-page cap applies **per subagent invocation**. Don't work around the
  cap by chaining subagent after subagent within one continuous orchestrator
  session to finish a whole large site in one sitting — that reproduces the
  exact runaway-session problem the cap exists to prevent, just relocated
  into the orchestrator. Large sites get multiple sessions/scheduled passes,
  whether the fetching itself is done directly or delegated.

**In-page loops that return a few hundred characters per file are a
different case** (an explicit exception). The cap
protects context and session stability when every fetch brings page text
into context. A loop that runs inside one loaded page and returns only short
snippets per file does not have that cost (for example, a few hundred such requests
pulling dates from around a hundred PDFs). Such a loop may go past 50 requests in one
session; the run's report says so, with the count.

**A backfill pass over already-built indexes (e.g. adding tags to indexes
built before this field existed) is pure local file editing — no web fetches
occur, so the 50-page/rate-limit cap does not apply.** Pace a backfill for
review-batch-size reasons if the human wants smaller diffs to check, not out
of a rate-limit concern; say so explicitly if the human asks for slow pacing
on a backfill, so the reasoning for going slow is accurate rather than assumed.

## Workflow

1. **Resume check.** Read the tracker/log if it exists. Note what's already
   `done` and skip it. If it doesn't exist, create it with a short header (date,
   start URL, purpose, ground rules, and the 50-page session cap).

2. **Load context for prioritization (if any).** If the catalog feeds a
   downstream purpose (a reference corpus, a migration, a research question), read
   whatever defines that purpose so you can tag each item's relevance and
   priority (e.g. HIGH/MEDIUM/LOW). Also read any sibling index already built
   for the same site so this pass **reconciles** (notes overlaps/complements)
   rather than duplicates.

3. **Enumerate the start page.** List every link: name, URL, format, any date.
   Use this to sanity-check your scope estimate against the session cap — if
   it's clearly a multi-session job, say so now (see "Session limits and
   multi-session planning").

4. **One level down where needed.** For each landing page, capture its listed
   items, including each item's native identifier where the source provides
   one (commitment 1). Apply the dense-list rule (count + range) for huge flat
   lists. If the source's own identifiers imply a detectable sequence (numbered
   chapters, sectioned bulletins), note any gap or out-of-order jump between
   what the crawl found and what the source's own numbering implies (e.g.
   "chapters 1–11 and 13 found, 12 missing") as part of that unit's log line —
   so the mismatch surfaces during the crawl, not in a later manual
   reconciliation pass. Log each completed unit immediately (commitment 3).
   Track your running fetch count against the 50-page session cap as you go.

5. **Write the deliverable incrementally.** Header + reconciliation note +
   per-item sections with a table (native ID, if any | item | URL | format |
   date/version | topic tags | verified tags | last verified | reads) + a 1–3
   sentence relevance note + a priority flag + 2-4 short topic tags per item
   (commitment 1) — cheap to add while already writing that row's relevance
   note, and what makes the index grep-searchable by topic later. Leave the
   Verified Tags and Last Verified columns blank and Reads at 0 at crawl
   time — they only fill in once someone actually reads the item in full (see
   "Verified tags and the update-on-read rule").

   **Start the file with a short summary header** so a later session can tell
   what an index covers without opening its tables: site and start URL, scope
   (what was and wasn't crawled), date built, status (`complete`, `partial`
   or `capped`, with the resume point), item count, and the combined list of
   topic tags used. Use YAML frontmatter where the folder's other files do,
   otherwise a short block at the top. Update status, count and tag list
   whenever a session appends to the file. One grep across the reference
   folder's headers then answers "which index covers X?"

   **Flag thin items instead of padding them.** If a listed item turns out to
   be a stub, a redirect, an empty landing page or a near-duplicate of another
   row, set its priority flag to `THIN` with a few words on why (e.g.
   `THIN: placeholder page, no content`). Thin rows stay in the index so
   nobody re-discovers them, but they are left off the top-priority summary
   and the deeper-dig checklist.

6. **Summarize.** End the deliverable with: total unit count, total link count,
   the top ~10 priority items for the downstream purpose (with URLs), anything
   that failed to fetch, any gaps or out-of-order jumps detected between the
   source's own native identifiers and what the crawl actually found, and — if
   the session ended because it hit the fetch cap rather than because the
   crawl finished — a clear note of that plus the resume point for next time.

7. **Flag deeper digs, don't do them.** List any second-level follow-ups worth
   doing (deeper categories, per-document date pages) as a *separate checklist*
   for the human to authorize — see the tracker pattern in commitment 3.
   Order the checklist by use: follow-ups under rows with the highest Reads
   count come first, because those are the areas later work actually draws
   on. On a first crawl, when every count is 0, order by priority flag.

## Delegation for lean, long crawls

A large crawl generates a lot of intermediate page text that does not belong in
the orchestrating context. If subagents are available, **delegate each crawl
pass to a subagent** with a tight brief: the start URL(s), the ground rules
below (including the 50-page session cap), the two files it owns (log +
index), and an instruction to report back a concise summary only. The
orchestrator stays lean and just tracks task status. Run a mid-priced/default
model for the fetch-and-transcribe work — it does not need a premium
reasoning model.

Give each subagent the **paths of the files it owns** and tell it to touch
*only* those, so parallel or sequential passes never clobber each other's work
or unrelated project files.

**When a subagent's job is to answer "does this document contain X" (as
opposed to plain cataloging),** its brief must also include the TOC-completeness
check described in "Verified tags and the update-on-read rule" below — a
subagent that faithfully reads every line it was handed can still miss content
that never made it into the fetch in the first place, and it has no way to know
that on its own unless explicitly told to check the source's own TOC first. If
the standard fetch tool has previously truncated on a large PDF, tell the
subagent about the "large PDF conversion timeout" fetch quirk (below) so it
knows to fall back to a local download + extraction rather than retrying the
same fetch and accepting a second incomplete result.

## Handling site fetch quirks

**Note: this section is the single source of truth for fetch quirks.** The
file `references/fetch-quirks.md` is a short summary of quirks 1-4 only; if
the two ever differ, follow this section.

Real sites break fetchers in boring, recurring ways. When a fetch returns
**empty content or an obvious page shell**, don't retry the identical request in
a loop and don't reach for shell/`curl`/`wget`/Python HTTP libraries as an
improvised, page-specific workaround for quirks 1-4 below (that bypasses the
sanctioned fetch path and is typically disallowed anyway). Diagnose the quirk
**once**, apply the fix crawl-wide.

**Quirks 1-4 cover a fetch that comes back empty or as an obvious shell** —
something you can see and diagnose. **Quirk 5 is different**: wrong or
missing content on an ostensibly *successful* fetch, or an outright
timeout/failure with no page-shell symptom at all. **Quirk 6** is the
large-PDF case of that: text that just stops partway. Routing source-of-truth
pulls through the `reliable-fetch` skill's protocol (a real browser by
default; with no browser, only its narrow official public API
exception) is a scoped, deliberate exception to the no-shell-workarounds
rule above — not the kind of improvised, page-specific curl workaround that
rule is about.

### 1. URL-casing sensitivity

**Symptom:** A mixed-case URL (e.g. `/DocsArchive/ReleaseNotes.htm`) returns
empty or a 404-like shell, but the site clearly exists and links to that path.

**Cause:** Some web servers (and some CDN/proxy configs) treat paths
case-sensitively, or the fetch tool normalizes differently than the browser.

**Fix:** Lowercase the entire path and refetch (e.g.
`/docsarchive/releasenotes.htm`). If the lowercase variant returns real
content, **lowercase every URL for the whole crawl** — including sub-page
paths you build from the listing.

### 2. Session-level fetch dedup cache

**Symptom:** A URL you know is live returns a response like "already fetched,
reuse content" or an empty body — often when the same URL was touched
earlier in the session, or by a sibling/parallel session sharing the cache.

**Cause:** The fetch tool caches by URL for some window (e.g. ~15 minutes)
and short-circuits repeat requests, sometimes returning no body.

**Fix:** Cache-bust — append a harmless query string that changes the URL
without changing what the server returns: a bare `?`, or `?v=1` /
`?nocache=<n>`. This forces a fresh fetch. Vary the param if you need to
fetch the same page more than once.

### 3. Client-rendered (JavaScript) pages

**Symptom:** The fetch succeeds but the body is a near-empty scaffold — a
root `<div>`, a loading spinner, an "enable JavaScript" notice, or
navigation chrome with no real content.

**Cause:** The page builds its content in the browser via JavaScript; a raw
HTTP fetch only sees the pre-render shell.

**Fix:** This is *not* a quirk you fetch your way around. Log the page in
the crawl log as `client-rendered — needs browser tool`, capture whatever
links are present in the shell if any, and move on. If rendering the page is
essential to the catalog, escalate to a browser-based tool (one that
executes JavaScript) rather than retrying the fetch.

### 4. Transient "not approved" / rate hiccups

**Symptom:** A one-off failure (a "not approved" tool response, a timeout, a
5xx) on a URL that is correctly cased and not cached.

**Cause:** Transient — rate limiting, a momentary server blip, or a
tool-side throttle.

**Fix:** Wait a beat and retry **once**. If it succeeds, continue. If it
fails a second time, log it `FAILED: <reason>` and move on — do not let one
page stall the catalog. Distinguish this from the casing/cache quirks above:
if casing is already correct and the URL wasn't touched earlier, it's likely
transient, not structural.

### 5. Unreliable or silently-wrong fetches (see the `reliable-fetch` skill)

**Symptom:** Two failure shapes, neither of which looks like quirks 1-4
above: (a) the fetch tool times out or fails outright, with no page-shell,
no pattern-matchable error, and no body to inspect at all; or (b) the fetch
"succeeds" — a normal-looking response comes back — but the content is
wrong, incomplete, or missing detail, with nothing in the response flagging
that anything is off.

**Cause:** Built-in fetch tools can time out on every URL in a session,
including a trivial page with nothing to summarize — this isn't a
one-off, diagnose-and-fix-once pattern the way quirks 1-4 are, just no
response. Separately, some fetch tools route content through a smaller
summarization model that can silently drop or invent details on an
ostensibly successful fetch, with nothing in the response signaling it
happened.

**Fix:** Don't treat this as a quirk to diagnose once and patch around here
— use the dedicated `reliable-fetch` skill's protocol (a real browser as
the source-of-truth text pull; with no browser, only its narrow
official public API exception; a blocked domain is a stop) for
source-of-truth pulls in this skill, rather than
trusting a single raw fetch call by default.

### 6. Large-PDF conversion timeout (silent truncation)

**Symptom:** A fetch of a large PDF (tens of pages, a megabyte or more)
returns real text with no error, but the text stops partway: mid-sentence,
mid-word, or before sections the document's own table of contents lists.
Retrying the same fetch returns the same partial text, or a different one.

**Cause:** The fetch tool converts the PDF to text under a time or size
limit. When it hits the limit, it returns what it has so far and does not say
the text is incomplete.

**Fix:** Don't retry the same fetch. For a catalog row, the partial text is
usually enough: the title, the TOC and the first sections give you the
metadata and topic tags. For a full-text read (verified tags, and above all
any `no-<topic>-confirmed` tag), download the PDF locally and extract the text
with a local tool, then check the result against the TOC (see "Verified tags
and the update-on-read rule"). The download is for this one read, which is a
targeted retrieval, not cataloging, so commitment 1 still holds. Log the
truncation on the row as `partial-read: <section> uncaptured (YYYY-MM-DD)`
until the full text has been checked.

### Diagnostic order

When a fetch returns empty or you can't be sure a "successful" fetch is
trustworthy, check in this order (cheapest first):

1. **Is the URL mixed-case?** → lowercase it, refetch.
2. **Did you (or a sibling session) touch this URL already?** → cache-bust
   with `?`, refetch.
3. **Is the body a JS shell?** → log as client-rendered, escalate to a
   browser tool only if essential.
4. **None of the above, but the fetch still failed transiently (timeout,
   5xx, "not approved")?** → retry once, then `FAILED` if it fails again.
5. **Fetch timed out outright with nothing to diagnose, or you need to
   trust the content as accurate source-of-truth text?** → this isn't a
   diagnose-once quirk like 1-4 — use the `reliable-fetch` skill's protocol
   instead of trusting a single raw fetch call.
6. **Large PDF whose text stops mid-sentence or before TOC-listed
   sections?** → quirk 6: no retry; download locally and extract if you need
   the full text.

## Pacing and politeness

Space requests out (a short pause between fetches); cap retries at ~2 per page;
never hammer a site. You are cataloging someone else's server — behave like a
considerate guest. If a page fails twice, log it as `FAILED` with the reason and
continue; a single failed page must never stall the whole catalog. This is
about *how* you fetch within a session; the 50-page hard cap above is about
*how much* you fetch in one sitting — both apply together. This pacing guidance
is about live fetches only — it does not apply to local file edits, such as a
tag-backfill pass over indexes already on disk (see "Session limits and
multi-session planning").

## Backfilling tags onto existing indexes

If tags are added to this skill after some indexes were already built, backfill
them as a **separate, local-only pass**, distinct from a live crawl:

1. For each existing index file, read it in full (it's already on disk — no
   fetching involved).
2. For each item row, derive 2-4 tags from the item's own name and its existing
   relevance note — don't re-fetch the source page to generate tags unless the
   existing title/note genuinely doesn't contain enough signal to tag
   accurately.
3. Add a `Tags` column to each item table (or a `Tags:` line under the item if
   the table format doesn't have room) and fill it in per row.
4. Treat this like any other append/edit to a durable project file — go file by
   file, and if the human asks for slower pacing on a backfill, that's for
   easier review of the diffs, not because of any rate limit (none applies to
   local edits).
5. Note in each backfilled file's header (or a shared backfill log) which
   indexes have been tagged and which are still pending, so a backfill that
   spans sessions is itself resumable the same way a live crawl is.

**Note:** a backfill pass (this section) produces **topic tags** only, derived
from existing titles/notes — not verified tags, since no full-text read
happens during a backfill. If a backfilled index item later gets a full-text
read for any reason, follow the update-on-read rule below to add verified
tags at that point.

## Verified tags and the update-on-read rule

Topic tags (commitment 1) are inferred from a title or TOC and cost nothing —
that's what makes the initial crawl cheap. But they're a guess, not a
finding. The gap between "probably about X" and "actually confirmed to
contain X" is exactly what causes rework in practice: a chapter gets tagged
`configuration, defaults` at crawl time, then months later gets fully read
for an unrelated question and turns out to be the one page that actually
covers webhook retries — a fact the topic tag never captured, so the next
person to wonder "does anything cover retries" has to re-read it to find
out again.

**The fix is a second field, Verified Tags, plus a standing rule that isn't
scoped to crawl sessions:**

- **Verified tags** are written only after an actual full-text read of the
  source — never inferred, never written speculatively. Format:
  `tag (YYYY-MM-DD, why it was read)` — e.g.
  `rate-limiting (read while scoping the v3 client migration)`. The date and reason
  matter: they tell a future reader how fresh the finding is and what
  question it answered, not just that it exists.
- **Negative findings get tagged too**, using a `no-<topic>-confirmed`
  convention — e.g. `no-webhook-content-confirmed (read while scoping the v3 client
migration)`. A confirmed absence is exactly as valuable as a confirmed presence:
  it's what stops the next person from re-opening a dead end. There is no
  separate "ruled out" column — negative verified tags live in the same
  Verified Tags field as positive ones, just named to say what they mean.
- **Negative verified tags require a completeness check — "I read every line
  I was given" is not sufficient proof of absence.** A full-text fetch,
  especially of a large PDF, can silently truncate: the extraction stops
  partway through with no error and no page-shell symptom, and a reader (human
  or subagent) who dutifully reads every line of what was saved will still
  never see the missing part, because it was never captured in the first
  place (see the "large-PDF conversion timeout" fetch quirk above — this is
  the concrete failure mode that makes this check necessary, not a
  hypothetical). Before writing a `no-<topic>-confirmed` tag, first locate
  the source's own table of contents (most long-form PDFs on a docs site carry
  one on page 1-2) and confirm every TOC-listed section actually appears
  somewhere in the body that was read. If any TOC-listed section never shows
  up, that's a truncation signal, not a negative finding — the correct tag is
  `partial-read: <section> uncaptured (YYYY-MM-DD)`, and no negative claim
  should be written until the missing section is actually retrieved (falling
  back to local download + extraction if the standard fetch keeps
  truncating) and checked. **Positive findings don't need this check** —
  finding something proves it's there regardless of what else might be
  missing further along; only claims of absence require proving the read was
  complete.
- **Add a Last Verified date column** alongside Verified Tags so staleness is
  visible at a glance — a verified tag from a year ago on a reference guide
  deserves a second look before being trusted blindly.
- **Update-on-read is mandatory, and it applies outside this skill.** Any
  task — a crawl session, a one-off research question, an unrelated deep
  dig — that does a full-text fetch of a URL already present in one of these
  indexes must write the verified tags (positive or negative) and a
  one-line finding back to that row, with a Last Verified date, and add 1 to
  the row's Reads count, **before the session ends.** The count is what
  orders the deeper-dig checklist (Workflow step 7), so later crawls go
  where real work keeps going. Don't defer this to a later backfill pass. The instinct is
  the same as capturing a decision into memory the moment it's made: write
  down what you just learned while the context is live and cheap to record —
  not as a cleanup project someone has to remember to schedule later.
- If the task doing the reading isn't this skill (e.g., a Q&A research
  thread that happens to fetch an already-indexed chapter for its own
  purposes), it should still check whether the URL has an index row — per
  the "check the index first" habit this skill already assumes — and write
  back to it, even though building or maintaining the index isn't that
  task's primary goal.
- **When delegating this kind of read to a subagent**, its brief must
  explicitly require the TOC-completeness check as a first step: quote (or
  paraphrase) the document's own table of contents near the top of its
  report, then confirm section-by-section that each TOC entry actually
  appears in the body it read, and report any that don't as gaps — not as
  confirmed absence. A subagent instructed only to "read everything and
  report findings" will faithfully do exactly that and still produce a false
  negative if the fetch itself was incomplete, because it has no way to know
  what it wasn't given.

## Indexing a licensed book (paraphrase-only reference notes)

Sometimes the "site" is a single long book you can read only for a limited time:
a library ebook loan, a subscription reader, a trial account. The goal is the
same as a site crawl, a durable reference that outlives access, but the
product changes. **You index what the book says, in your own words, keyed by
the book's own section numbers, and you keep none of its text.** Everything
else in this skill still applies: the session cap, the append-only tracker,
topic and verified tags, and update-on-read.

**Rules specific to a licensed book:**

1. **No book text kept.** Write each entry as a paraphrase. At most one short
   phrase per entry may be quoted, and only when the exact wording matters.
   Never paste passages, tables or figure contents into the index, the
   tracker, a scratch file or a chat reply. The test: could someone rebuild
   the book's text from your notes? If yes, you copied too much.
2. **The book's section number is the primary key** (for example `9.3.2`,
   `Table 4-2`), as with any native identifier (commitment 1). Record the
   edition, because section numbers move between editions.
3. **Every entry carries a "use" line.** Say how the section applies to the
   work that prompted the reading (for example "<Project> use: …"). A bare
   summary of a borrowed book loses most of its value once access ends. The
   use line is what makes the notes worth keeping. If the notes will serve
   several projects, store them in a shared, project-neutral location and let
   each project add its own use lines instead of copying the file.
4. **Write findings back into the project during the session.** When a section
   bears on a decision that is live in the same session, update the project
   artifact or decision record while the reading is fresh, then cite the
   section number there. A finding left only in the index is rarely applied
   later.
5. **Plan around the access end date.** Record when access expires in the
   tracker header at the start. Prioritize sections by the phase the work is
   entering next, not by chapter order. End with a closing pass that lists
   what was indexed **and what was deliberately skipped, with why**, so the
   gap is a decision rather than an accident.
6. **Mark anything inferred as provisional.** If a cross-reference to another
   edition, or any mapping, came from a secondary source rather than the book,
   label it provisional where it appears, not only in the summary.

**Reader mechanics (licensed web readers):**

- **Prefer a reader whose page text you can read.** Some app-style readers
  render the book in a cross-origin frame whose text cannot be extracted, so
  the only option is screenshots. That costs one screen per small page and
  burns the session cap fast. A web reader from the same service often
  renders chapters in same-origin frames whose text is readable. If the first
  reader allows screenshots only, stop and ask the human to open the book in
  the alternative reader before continuing. Don't grind through screenshots.
- **Navigate with the reader's own controls** (its table-of-contents or chapter
  buttons), then read the text the reader has rendered. Raw chapter files
  fetched directly are often obfuscated until the reader renders them. Treat
  that as a signal to stop, not something to decode.
- **Never copy a reader URL that carries session or access tokens** into
  another tab, file or message, even to work around an extraction limit. It
  is a credential, not a link.
- **Count chapter jumps, not screenshots, against the cap** when you read
  rendered text. Log the count in the tracker.
- **Timestamps come from the clock.** Read the time from the system
  (for example `date`) at the moment you write each tracker line. Never
  estimate it. If you find an estimated stamp, append a correction line; don't
  edit the old one.

## Anti-patterns

- **Saving book text "for later" when indexing a licensed book.** The index holds paraphrase and section numbers only. Copied passages are exactly what the access terms prohibit, and they are not needed once each entry has a use line.
- **Working around a locked reader with a token-bearing URL.** Ask for a readable reader instead.
- **Downloading documents "to be safe."** No. The index says what exists;
  retrieval is a later, targeted, human-authorized step.
- **Recursing to arbitrary depth.** One level down by default; deeper is a
  flagged follow-up, not an inline decision.
- **Writing the deliverable only at the end.** If the run is cut short you lose
  everything. Write header-first, append per item.
- **Batching log writes.** "I'll record progress once I'm done" defeats
  resumability — the crash happens *before* "done." Append the instant each unit
  finishes.
- **Retry-looping an empty fetch.** Empty ≠ down. Check for a casing/cache/
  large-PDF-timeout quirk (quirks 1, 2 and 6 under "Handling site fetch
  quirks") before assuming the page is dead —
  and note that retrying the identical fetch call on a large-PDF timeout tends
  to just reproduce the same partial result, not fix it; the fix is local
  download + extraction, not another retry.
- **Trusting a single fetch call as verbatim source-of-truth text without
  using the `reliable-fetch` protocol.** A "successful," non-empty fetch is
  not proof of accuracy — see "Handling site fetch quirks" above.
- **Itemizing giant uniform lists line by line.** Count + date range + a sample
  is the useful artifact; a thousand near-identical rows is noise.
- **Numbering items by crawl-discovery order when the source has its own
  numbering.** A chapter, section, or bulletin number printed on the source is
  the primary key; the order you happened to find things in is not. Silently
  substituting one for the other produces an index that looks complete but
  disagrees with the document the moment someone checks it by hand.
- **Running a crawl session with no fetch cap, or rationalizing past the cap
  ("just a few more pages").** The 50-page/~20-minute cap is mandatory, not
  advisory — an open-ended session is what crashes the fetch tool, the
  browser, or the host process. Stop, log, and hand off instead.
- **Treating a large site (100+ pages) as a one-sitting job.** Plan multiple
  sessions or scheduled passes up front — don't discover the need for a
  second session only after the first one runs long.
- **Applying the live-crawl fetch cap or pacing rules to a local tag-backfill
  pass.** Backfilling tags onto already-built indexes touches no external
  server — treat it as a normal file-edit task, and if slow pacing is wanted,
  it's for reviewability, not rate-limit avoidance.
- **Folding tags into the free-text relevance note instead of a dedicated
  field.** Tags need to be short and consistently formatted to be
  grep-friendly; burying them in prose defeats the purpose.
- **Reading a source in full for some other task and not writing the finding
  back to its existing index row.** The update-on-read rule applies any time
  a full-text read happens, not just during a crawl session — deferring the
  write-back is exactly what turns into a backfill project later.
- **Writing a negative verified tag from "I read every line I was given"
  without checking that against the source's own table of contents.** A
  truncated fetch and a genuinely empty section look identical from inside
  the read alone — only a TOC cross-check tells them apart. This is the
  single most likely way a verified tag ends up wrong.

## Pre-flight check (before declaring the crawl done)

Re-read these and verify against your output:

1. Did you **download any documents**? You shouldn't have — index only.
2. Is the **tracker/log complete and accurate** — every attempted unit marked
   `done`/`FAILED`/`PARTIAL`, matching what's actually in the deliverable?
3. Is the **deliverable append-structured** (header + per-item sections), so a
   re-run could resume cleanly?
4. Does the summary report **counts, top priorities, and failures**?
5. Did you **reconcile** with any sibling index for the same site instead of
   duplicating it?
6. Are deeper digs **flagged as an authorizable checklist**, not silently
   performed?
7. Where the source provides its own native identifiers (chapter/section/
   bulletin numbers), did you use them — not crawl-discovery order — as the
   primary key, and flag any gap or out-of-order jump against that numbering?
8. Did every item get **2-4 short topic tags** in a dedicated, consistently
   formatted field — not folded into the relevance-note prose — so a later
   session can grep across the reference folder by topic instead of
   re-reading each index?
9. Did you **stop at or before the 50-page/~20-minute session cap** and hand
   off via the tracker, rather than pushing through it?
10. If the site's total scope was large (100+ pages), was a **multi-session or
    scheduled plan** communicated up front rather than improvised mid-crawl?
11. If this session did a **full-text read** of any URL that already has an
    index row elsewhere (in this crawl or a sibling one), did you write
    verified tags (or a confirmed-absence tag) and a Last Verified date back
    to that row before finishing — instead of leaving it for a future
    backfill?
12. If this session wrote any **negative verified tags** (`no-<topic>-confirmed`),
    was each one checked against the source's own table of contents first, to
    confirm no TOC-listed section was missing from what was actually read —
    rather than relying on "the subagent said it read everything it got"?
13. For any **large PDF** (tens of pages, megabyte-plus) that was fetched, did
    the extracted text end at a natural section/document boundary, or did it
    stop mid-sentence/mid-word? The latter is the large-PDF-conversion-timeout
    quirk, not a complete read — fall back to local download + extraction
    before trusting anything drawn from it, especially a negative finding.
14. Did you use the **`reliable-fetch` protocol** (a real browser, or with
    no browser its official public API exception) for source-of-truth
    text pulls, rather than
    trusting a single raw fetch call as verbatim page content by default?

15. **Licensed book only:** does the index, tracker or any scratch file hold book text beyond one short phrase per entry? Does every entry have a section number and a use line? Are the access end date and a "deliberately skipped" list recorded? Is every inferred mapping marked provisional where it appears?
16. Was every tracker timestamp **read from the clock** at write time, with correction lines appended for any that were estimated?
17. Does the index file open with a **summary header** (site, scope, date built, status, item count, combined tag list) that matches the file's current state?
18. Are stubs and near-duplicates flagged **`THIN`** with a reason, kept out of the top-priority summary, and is the deeper-dig checklist **ordered by Reads count** (or by priority flag when all counts are 0)?

A 30-second pass over this list prevents a restart.

---

## Related skills

This skill is self-contained — nothing else in this pack is required.

**Pairs well, ships in this pack:**
- The `reliable-fetch` skill. This skill's fetch protocol assumes retries, extraction fallbacks, and a way to tell a real 404 from a transient failure; `reliable-fetch` provides that.
- A scheduling capability, for crawls that span more than one session.
