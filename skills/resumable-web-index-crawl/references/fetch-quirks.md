# Fetch Quirks Reference

Recurring reasons a fetch returns empty/broken content on real sites, and the
fix to apply for the rest of the crawl. Read this the first time a page comes
back empty or looks like a shell. Diagnose the quirk **once**, apply the fix
crawl-wide — do not retry the identical request in a loop, and do not fall back
to shell/`curl`/`wget`/Python HTTP libraries (those bypass the sanctioned
fetch path and are typically disallowed anyway).

## 1. URL-casing sensitivity

**Symptom:** A mixed-case URL (e.g. `/DocsArchive/ReleaseNotes.htm`) returns
empty or a 404-like shell, but the site clearly exists and links to that path.

**Cause:** Some web servers (and some CDN/proxy configs) treat paths
case-sensitively, or the fetch tool normalizes differently than the browser.

**Fix:** Lowercase the entire path and refetch (e.g.
`/docsarchive/releasenotes.htm`). If the lowercase variant returns real
content, **lowercase every URL for the whole crawl** — including sub-page paths
you build from the listing.

## 2. Session-level fetch dedup cache

**Symptom:** A URL you know is live returns a response like "already fetched,
reuse content" or an empty body — often when the same URL was touched earlier in
the session, or by a sibling/parallel session sharing the cache.

**Cause:** The fetch tool caches by URL for some window (e.g. ~15 minutes) and
short-circuits repeat requests, sometimes returning no body.

**Fix:** Cache-bust — append a harmless query string that changes the URL
without changing what the server returns: a bare `?`, or `?v=1` / `?nocache=<n>`.
This forces a fresh fetch. Vary the param if you need to fetch the same page
more than once.

## 3. Client-rendered (JavaScript) pages

**Symptom:** The fetch succeeds but the body is a near-empty scaffold — a root
`<div>`, a loading spinner, an "enable JavaScript" notice, or navigation
chrome with no real content.

**Cause:** The page builds its content in the browser via JavaScript; a raw
HTTP fetch only sees the pre-render shell.

**Fix:** This is *not* a quirk you fetch your way around. Log the page in the
crawl log as `client-rendered — needs browser tool`, capture whatever links are
present in the shell if any, and move on. If rendering the page is essential to
the catalog, escalate to a browser-based tool (one that executes JavaScript)
rather than retrying the fetch.

## 4. Transient "not approved" / rate hiccups

**Symptom:** A one-off failure (a "not approved" tool response, a timeout, a
5xx) on a URL that is correctly cased and not cached.

**Cause:** Transient — rate limiting, a momentary server blip, or a tool-side
throttle.

**Fix:** Wait a beat and retry **once**. If it succeeds, continue. If it fails a
second time, log it `FAILED: <reason>` and move on — do not let one page stall
the catalog. Distinguish this from the casing/cache quirks above: if casing is
already correct and the URL wasn't touched earlier, it's likely transient, not
structural.

## Diagnostic order

When a fetch returns empty, check in this order (cheapest first):

1. **Is the URL mixed-case?** → lowercase it, refetch.
2. **Did you (or a sibling session) touch this URL already?** → cache-bust with
   `?`, refetch.
3. **Is the body a JS shell?** → log as client-rendered, escalate to a browser
   tool only if essential.
4. **None of the above?** → treat as transient, retry once, then `FAILED`.
