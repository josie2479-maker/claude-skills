---
name: "fact-verification"
description: "Verify factual claims before delivering them. Use before any answer or deliverable the user will act on, forward, or share with others — rules, policy, legal, financial, date, or product facts — and whenever they say 'is that right', 'double-check that', 'verify this', 'fact-check', 'are you sure', or 'source?'. Exception: vetting a claim someone else handed the user is critical-thinking-walkthrough; fetching page text is reliable-fetch (this skill calls it)."
---

# Fact Verification

**License:** Released under CC BY 4.0 — free to use, adapt, and share with attribution.

## 1. Pull the claims
List every factual claim in the draft answer. Skip opinion and reasoning.

## 2. Triage
Mark each claim:
- **Structural**: the user would decide, act, or forward based on it → must verify
- **Background**: context only → label, don't chase

## 3. Verify load-bearing claims
- Check against a primary source (the publisher's own site, the statute or standard itself, official documentation), not a summary
- Use reliable-fetch for page text, if you have it; otherwise pull the page with a real browser tool rather than a summarizing fetch. If a source can't be reached, say so; don't fill in from memory
- Dates, deadlines, amounts: confirm against the source's own wording, and note its as-of date
- Forecasts and warnings: a forecast whose time has passed is not a result. Label every number as observed or forecast. For a past forecast, peak or alert level, look up the observed record (the measurement log or the system's recorded data), not the forecast or alert text, and report the actual. A number from a withdrawn forecast never happened.
- Find the exact figure or wording in the source itself. A working link, a real journal and a matching topic don't prove the source says it. Several sources agreeing counts only if they didn't copy each other.
- Citations carried in templates or older documents can be out of date. Check each cited rule, clause or section against the current official text, not the template.
- A negative ("no requirement", "not found") covers only the sources and words actually searched. Before stating one:
  - search every layer that can create the duty (law, regulation, official guidance, local rules) and the project's own records;
  - search the concept with synonyms, not one keyword;
  - scope the claim to what was searched ("no regulation found in the sources checked", not "no requirement").

  Keep that scope in the headline itself. Caveats placed below a headline don't travel with it when it's copied.

- Separate what a rule permits from what happened in the user's own case. Requests, letters, approvals and decisions come from the user's records (their notes, decision and event logs, earlier research in the project), not from the rule itself. Search those first, and ask where the record lives if the obvious place is empty.
- Product plans ("is X planned?"): check the vendor's help-center "current limitations" sections first, then release notes, then blogs and press.
- An arithmetic discrepancy handed to you: re-add every level (line items, subtotals, totals) from the source. The given explanation may cover only one of several gaps.

## 4. Label in the answer
- ✅ Verified: [source, date checked]
- ⚠️ Unverified: from training data, may be stale
- ❓ Guess/inference: say what it rests on

## 5. Before delivering
Re-read steps 2–4. Any load-bearing claim without ✅ or an explicit ⚠️ → fix it or flag it.
