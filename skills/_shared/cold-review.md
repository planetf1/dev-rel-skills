---
name: cold-review
description: >
  Reusable independent review prompt. Spawn a subagent with no context from
  the current conversation to review a draft (design issue, design proposal,
  or discussion response) before circulating or filing. Not invoked directly —
  referenced by design-issue, design-proposal, and respond-to-design-discussion.
---

## Subagent review prompt (reusable)

When spawning the independent reviewer, give it:

- The design doc path
- A paragraph of project context (what the library does, what the epic
  is about, where the doc sits in the lifecycle)
- Explicit checklist: progressive disclosure, clarity for a new reader,
  internal consistency (section refs point correctly), coverage of the
  epic threads, technical accuracy (spot-check against code), weaknesses
  and gaps, tone/readability, appendix and sequencing-note usefulness
- Output format: top-line verdict, must-fix, should-fix, nice-to-have,
  what's working well. Under 800 words.
- Tell it NOT to rewrite the doc — review only.
- Any user-specific style preferences captured in memory (e.g. specific
  word bans) — tell the reviewer to flag re-emergences.
- Ask the reviewer specifically to check:
  a. banned-word re-emergences;
  b. duplicate questions / compound questions (per the iteration
     hazards list);
  c. whether Part II re-enumerates Part I symptoms rather than
     explaining structural causes;
  d. whether status markers and version numbers are internally
     consistent across sections.
- Reviewer should also confirm **what's working well** — after N
  iterations the author loses confidence in the good bits; name them
  explicitly so they get protected in subsequent edits.
