---
name: deliverable-requirement-check
description: "Test a ready-made list of deliverables, documents or artifacts before anyone commits to tracking it. Use when someone is handed a list from a template, vendor, consultant, PMO framework or AI (\"standard project documents\", \"required artifacts\", \"deliverables checklist\") and asks whether to adopt it, what is really required, what to drop, or what is missing. Tags each item by who requires it (funder, law or regulation with citation, a named person, or standard practice that nobody requires), reads the funder's or agency's own template section by section to find required items the list leaves out, and returns a table plus a drop / keep / add list. Not for building a list from scratch with no list in hand, and not for checking whether finished documents are good."
---

# Deliverable Requirement Check

**License:** Released under CC BY 4.0 — free to use, adapt, and share with attribution.

A supplied list of deliverables is a claim about obligation, not proof of it.
A list that hangs together, uses the right terms and names only real
artifacts can still miss every item the funder actually mandates, and pad the
rest with work nobody requires. Its plausibility is what makes it risky,
because the person receiving it is about to commit to producing every row.

The question this skill asks of each item is not "is this a real thing?" but
**"who requires this, and can I cite them?"** Where the answer is "nobody",
saying so is a finding, not a gap to fill.

## When to use

- Someone has a list in hand (from a template, vendor, consultant, framework
  or AI) and is deciding whether to adopt or track it.
- The work is funded, regulated or overseen by a body that publishes its own
  requirements, templates or checklists.

Not for: drafting a list from nothing (start from the funder's template
instead), or reviewing the quality of finished documents.

## Method

### 1. Catalogue the supplied list as given

Number every item exactly as supplied. Don't merge, rename or add yet. Record
where the list came from and its date, if known.

### 2. Get the governing sources, and read them

Identify who funds, regulates or approves the work. **Obtain their actual
template, checklist or guidance and read its section list.** Don't reason
from what such a template "probably" contains. Requirements change on
effective dates that downstream summaries don't carry, so the source document
is the only reliable check. Note each source's title, version and date.

If a governing source can't be reached, say so and mark every conclusion that
depends on it as provisional at the point it's made.

### 3. Tag every item with one authority value

| Tag | Meaning | Evidence needed |
|---|---|---|
| **Funder** | The funding or oversight body requires it | Template section, guidance clause or award condition |
| **Law/Reg** | A statute or regulation requires it | Citation to the section |
| **Named person** | A specific person asked for it, on a date | Who and when |
| **Standard practice** | Common and maybe useful, but no requiring authority was found | What was searched |

"Standard practice" is a legitimate result, not a failure. Also flag any item
that depends on a body or role the project doesn't have (for example a release-signing
committee on a project that has none).

### 4. Find gaps in a separate second pass

Only after step 3, walk the governing template **section by section** and
list each required item the supplied list leaves out. Keep added rows
visibly separate from supplied rows, so anyone can tell which came from the
source and which the analysis introduced. Cap the additions to what the
sources require, and record anything considered and rejected, with a reason.
Every added row is work handed to a person.

### 5. Output

1. **Table:** item · source (supplied / added) · authority tag · citation or
   evidence · notes (depends on a missing body, overlaps another item, etc.).
2. **Drop / Keep / Add:**
   - **Drop:** standard-practice items with no requiring authority that the
     team doesn't need. Say why each one goes.
   - **Keep:** items with a cited authority, plus any standard-practice
     items the team chooses to keep, labelled as a choice.
   - **Add:** required items missing from the list, each with its citation.
3. **Sources read:** title, version and date of each governing document, and
   any that couldn't be reached.

## Example (generic)

An open-source library receiving a software grant is handed a 17-item
"standard release documents" list from a consultant, grouped into design,
build, launch and maintenance. Reading the grant's own milestone template
shows 5 required deliverables. Two of them are on the list under different
names; three aren't on it at all. Of the 17 supplied items, 2 match the
grant, 1 is required by the license-compliance terms of a dependency, 2 were
asked for by the lead maintainer, and 12 are standard practice with no
requiring authority. Three of those 12 assume a security review team the
project doesn't have. Result: keep 5, add 3, drop or make optional 12.

Without the check, the team would have committed to 17 items and missed 3
that the grant reviewers actually check.

## Before reporting done

- [ ] Every supplied item has exactly one authority tag.
- [ ] Every Funder or Law/Reg tag has a citation from a source actually read.
- [ ] The governing template was read section by section, or its absence is stated and conclusions are marked provisional.
- [ ] Added rows are visibly separate from supplied rows.
- [ ] Drop / Keep / Add is present and each line has a reason.
