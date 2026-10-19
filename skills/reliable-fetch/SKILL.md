---
name: "reliable-fetch"
description: "Use whenever a task needs the actual, trustworthy text of a web page — research, citations, documentation lookups, or feeding a crawl/index — not just a quick fact check. Covers the preferred way to pull reliable source-of-truth page content when the built-in web-fetch tool is unavailable, times out, or may be summarizing/compressing content through an intermediate model rather than returning the real text. Applies to ad-hoc research as well as to the resumable-web-index-crawl skill."
---

# reliable-fetch

**License:** Released under CC BY 4.0 — free to use, adapt, and share with attribution.

## The problem this solves

Built-in "fetch a URL" tools (WebFetch-style tools generally, including any
`web_fetch` tool in your environment) are not guaranteed to hand back the
page's actual text. Two distinct failure modes have been observed, and both
matter:

1. **Outright failure.** In testing, a `web_fetch` tool timed out on every test
   URL in a single session, including a trivial page (example.com) with
   nothing to summarize and no plausible size excuse. This was not a
   large-page problem. The tool simply did not return.
2. **Silent inaccuracy.** Users have reported that some WebFetch-style tools
   route fetched content through a smaller, cheaper model that
   compresses/summarizes before handing it back. That smaller model can drop
   sections, average numbers that were never in the same table, or invent
   citations or names that do not exist in the source. The output *looks* like
   a normal successful fetch (no error, no empty-page shell), which is what
   makes it dangerous: nothing about the response signals "this may not be
   verbatim."

Either failure mode produces the same practical risk: research, citations, or
a crawl index built on top of it can be wrong in ways that are invisible until
someone checks the primary source by hand.

**Note on scope:** whether the summarization-model behavior described in (2)
is actually happening in any given environment's fetch tool is not something
this skill independently confirms. The tool description available to the agent
may not disclose that detail either way. Treat (2) as a live possibility to
guard against by design, not a confirmed fact about every environment. (1) is
easy to spot: if the fetch tool times out or errors, that is self-evident and
this skill's alternate path applies immediately.

## The protocol

1. **Use the built-in fetch tool and/or web search only for discovery**:
   finding the right URL, confirming a page exists, getting a rough sense of
   what is on it. Do not treat its returned text as the authoritative source
   for exact facts, figures, quotes, or citations.

2. **Pull source-of-truth text with a real browser, if one is available.** This
   is the primary path, not a fallback. Open the URL in a browser tool
   (navigate, then a "get page text" action for body text or a "read page"
   action for structure). A browser renders JavaScript, so it also handles
   client-rendered pages that a raw HTML fetch cannot see into. If you have
   more than one browser tool, use whichever the user has asked for.

   Prefer text/structure extraction over screenshots; it returns the page's
   real text rather than pixels of one viewport. Before trusting the pull,
   confirm three things: the loaded URL is the one you asked for (no redirect
   to a login, consent wall, or error page); the body is actually present
   rather than a spinner, an "enable JavaScript" notice, or a paywall stub; and
   nothing referenced by the page's own headings or table of contents is
   missing. If content loads lazily, scroll and re-read before quoting.

   The browser is the preferred path. Outside the one exception below, do not
   use shell commands, scripts, or any programming library to fetch page
   content, and do not route pulls through third-party extraction, readability,
   or proxy services.

   **Exception: official public APIs.** When no browser is available, a shell
   pull from the publisher's own official, documented public API is allowed.
   Note in the output which method was used. Never use a shell pull to get
   around a fetch tool that refused or blocked a site; in that case, stop and
   tell the user (step 3 below still governs).

3. **A blocked or restricted domain is terminal.** If a fetch tool reports that
   a domain cannot be fetched or is restricted, stop. Do not use shell tools
   (curl, wget, lynx), scripts (requests, urllib, httpx, aiohttp) or any other
   language to retrieve it; do not reach for archive sites, caches, or mirrors
   of the blocked content; and do not switch browsers, tabs, or tool tiers to
   get at the same blocked domain. Escalating past a block is not a technical
   problem to solve. It is out of bounds regardless of how legitimate the
   research need is.

   **A site-issued 403 and a tool-policy blocklist look alike but are not the
   same thing, and neither one justifies routing around the block.** A clean,
   non-JS "403 Forbidden / you cannot view this page" rendered by the site
   itself is a page-level access restriction: a real browser already hit that
   wall, so no further tier gets past it. A tool- or platform-level block is a
   policy decision about that domain. In both cases the correct next move is
   the same: stop fetching that URL.

   What you *may* do after either kind of block:
   - Note whether the failure is **per-URL or per-domain**. Other pages on the
     same site loading normally in the same session means one URL is
     restricted, not the whole domain, so do not over-infer.
   - Look for a sibling or companion page on the same site covering the same
     material, or a genuinely different primary source published elsewhere.
     A different source is fine; a cached or mirrored copy of the blocked page
     is not.
   - **Say plainly in the deliverable which specific URL was unreachable and
     what it would have contributed**: the claim it would have supported, the
     figure it would have supplied, the section of the index it leaves empty.
     Never quietly substitute a weaker source and present the result as if the
     gap were not there.
   - **Ask the user for a copy early** (a PDF or printout of the page they can
     open themselves) before building a durable artifact from secondary
     summaries. If they supply one, rebuild from it and mark in the artifact
     which items came from the primary source and which from summaries. Before
     re-proposing a file built from the first pass, re-read the live copy,
     because the user may have saved the earlier version.

4. **Never send private, authenticated, or session-specific URLs to any
   third-party fetcher, extraction service, or public proxy.** Those services
   offer no guarantee of privacy for the URL or its contents; treat one like
   any other stranger you would not hand a signed-in session link to. For
   anything behind a login, use the browser with the user's own authenticated
   session, and do not paste the signed-in URL anywhere else.

## Saving a binary file (PDF) without a shell fetch

The fetch tool returns text only. To save a public PDF into a project folder
(behavior varies by browser tool; re-check before relying on it):

1. In the browser, `fetch()` the URL from the page and record the byte count
   and the first bytes (`%PDF`).
2. Trigger the download. Some browser tools save the file to Downloads under a
   generated temporary name and ignore the requested filename. A download from
   a `blob:` URL (an `<a download>` built in the page) may never reach
   Downloads at all. If nothing with the matching size appears, give the user
   the direct link to download themselves; do not spoof headers or retry
   around bot protection.
3. Request access to Downloads, pick the newest temporary file whose size
   matches exactly, copy it into place under the right name, and check that it
   opens (for example with `pdfinfo`). Never pick by filename or by "newest"
   alone.
4. Tell the user about the leftover temporary file so they can delete it.

This is for a site that loads normally. A blocked or refused site is still
terminal under step 3 of the protocol.

## Pages that need the browser

- **JS-rendered pages load their content late.** A plain fetch can return only
  the site shell, and the first browser read right after load can come back
  empty. Wait several seconds and read again, and confirm the page is showing
  the state you asked for (the selected date, tab or filter) before reporting
  something as absent. An empty first read is not evidence.
- **Many small reads from one site.** To pull a few fields from many
  same-origin files (dates from a hundred PDFs), load one page in the browser
  and loop `fetch()` inside it, paced about a second apart. Return raw
  snippets, not parsed values, and parse outside the browser, because real
  headers carry misspellings and odd spacing. A long-running script can time
  out on the tool side and keep running in the page; launch batches without
  waiting and poll for results.
- **Large tool results.** A result too big for context may be saved to a file
  that the shell can read. Move data through that file rather than printing it
  back into context.
- **Price or market history.** Go straight to the browser. A public chart API
  that returns JSON opens in the browser as plain text; parse it with one
  in-page script into date/value rows. Do not spend fetch-tool timeouts on it
  first. Name the instrument in the deliverable (futures, spot or benchmark),
  because the easy-to-reach series is often not the one asked for.

## When this level of care isn't needed

For a quick fact-check where being roughly right is fine and nothing turns on
exact wording, a casual web search or fetch answer is fine. Reach for this
protocol when something will be *cited*, *quoted exactly*, *fed into an index
or downstream document*, or *relied on for a specific number/date/name*: the
cases where a silently-wrong answer actually costs something.

## Verifying in a new environment

Since failure mode (1) may be environment- or session-specific (a live outage,
a broken connection) rather than a permanent property of a given fetch tool,
it is reasonable to spot-check before assuming this protocol is necessary in a
fresh environment. But the spot-check has to use a **genuinely independent**
second source, not just a second call into the thing already suspected of
being broken.

**Standing rule: do not verify a browser pull by comparing it against the
built-in fetch or web-search tool.** That comparison is not independence. Both
readings would be trusting the same tool this skill exists to route around, so
a match does not rule out failure mode (2) (silent, plausible-looking
inaccuracy), and a mismatch just confirms what is already assumed. It only
*feels* like verification.

**Be honest about what is left.** With the browser as the only preferred
source-of-truth path, there is no longer a second, fully independent fetch
pipeline to diff against. The checks below are weaker than a true two-pipeline
comparison. Use them, and say so when reporting confidence; do not describe
them as independent verification when they are not.

What is actually available, strongest first:

- **Check the claim, not the pipeline.** Confirm the specific fact, figure, or
  date against a second primary source at a *different origin*: the
  publisher's own page, the standards body, the company's own filing. This
  verifies the thing that matters even though it says nothing about the fetch
  mechanism.
- **Two browser stacks, when both are available.** Two separate browsers (or
  browser tools with separate profiles) are separate code paths. Rendering the
  same page in both and comparing headings plus a handful of specific facts is
  modest, but real. Only for domains that load normally; never as a way to
  reach a blocked one.
- **Two extraction paths inside one browser.** "Get page text" and "read page"
  style actions read the same rendered DOM differently. Agreement between them
  is weak evidence (a shared upstream problem shows up in both), but
  disagreement is a strong signal that something was truncated or missed.
  Treat a mismatch as a reason to look harder.
- **Render-integrity checks.** Confirm the URL, look for consent walls,
  paywall stubs, "enable JavaScript" notices, spinners, or a body that stops
  mid-section, and cross-check the page's own table of contents against what
  you actually captured. These catch the common silent-partial-page failures
  without needing a second source at all.

If those checks come back clean, the overhead of routing every pull through
this protocol may not be worth paying every time in that environment for
low-stakes fetches, but keep defaulting to it for anything where correctness
actually matters (cited, quoted, fed into an index). The failure this protocol
guards against is silent by nature, the spot-check only tells you about the one
page you happened to check, and the remaining backstop for high-stakes work is
a human reading the primary source. Keep quotes short and attributed to the
exact URL so that check stays cheap for whoever does it.
