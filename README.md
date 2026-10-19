# Claude skills

Skills for Claude (Claude Code, the Claude apps, or anything that loads `SKILL.md` skills). Each lives in its own folder under `skills/`.

| Skill | What it does |
|---|---|
| [reliable-fetch](skills/reliable-fetch/SKILL.md) | Gets the real text of a web page instead of a summarised or failed fetch, for research and citations. |
| [resumable-web-index-crawl](skills/resumable-web-index-crawl/SKILL.md) | Builds a link index of a large documentation site that survives interruption, without bulk-downloading it. Pairs with reliable-fetch. |
| [fact-verification](skills/fact-verification/SKILL.md) | Checks factual claims against sources before you act on them or pass them on. |
| [plain-register-pass](skills/plain-register-pass/SKILL.md) | Strips AI-essay voice from a document before it goes to someone else. |
| [deliverable-requirement-check](skills/deliverable-requirement-check/SKILL.md) | Tests a ready-made list of deliverables before you commit to it: who actually requires each item, what's padding, and what required items are missing. |
| [project-foreman](skills/project-foreman/SKILL.md) | Runs large multi-batch projects (migrations, cleanups, big builds) in small checked batches, stopping to flag anything ambiguous instead of guessing. |

## Install

- **Claude Code:** copy a skill's folder into `~/.claude/skills/` (all users) or `.claude/skills/` in a project.
- **Claude apps:** zip the skill's folder and upload it under Settings → Capabilities → Skills.

Read a skill before installing it. Each one says when it should trigger and what it will and won't do.

## Licence

[CC BY 4.0](LICENSE). Free to use, adapt and share with attribution.
