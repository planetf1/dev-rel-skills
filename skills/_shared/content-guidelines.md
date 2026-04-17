# Content Guidelines

Shared voice, values, and messaging rules for all content-creation skills
in this repo. Every skill that produces written output (`write-tweet`,
`write-linkedin-post`, `write-technical-blog`, `write-youtube-script`,
`release-blog`) should apply these guidelines.

These guidelines are derived from the mellea 2026 DevRel strategy and
workshop synthesis. They are non-negotiable.

---

## Voice

Write like a practitioner talking to peers. The reader is a developer
who builds things — they respect specificity, honesty, and evidence.
They distrust marketing, hype, and corporate comms.

- **First-person, author-voiced.** "I built this because..." or "We
  hit this problem when..." — not anonymous corporate "we are pleased
  to announce."
- **Technical but approachable.** Assume competence; don't assume
  context. Spell out acronyms on first use, but don't over-explain
  concepts a working developer would know.
- **Direct.** State the point, then support it. Don't build to a
  reveal. Developers skim — front-load value.
- **Confident, not arrogant.** "This solves X" is good. "This is the
  best solution to X" needs a benchmark to back it up.

---

## Values

### Integrity

Every claim must be verifiable. No exceptions.

- **Ground claims in documentation, benchmarks, or working code.**
  "3x faster" needs a methodology note or link. "Dramatically faster"
  is not acceptable — use the number.
- **No unverifiable claims.** If you can't point to evidence, don't
  say it.
- **Acknowledge limitations.** One honest caveat builds more trust
  than the rest of the piece combined. Say what the feature does NOT
  solve, who should NOT use it yet, or what's still experimental.
- **No hype words.** "Game-changing", "revolutionary", "best-in-class",
  "enterprise-grade" (without specifics), "blazing fast" (without
  numbers) — these activate developer skepticism. Replace with
  specifics or delete.

### No Competitor Blame

We differentiate on merits. We do not win by making others look bad.

- **Never name a competitor negatively.** Don't say "unlike [tool],
  mellea actually works" or "while [tool] struggles with..."
- **Respectful differentiation is fine.** "Mellea uses token-level
  constrained decoding rather than retry-based validation" describes
  a technical difference without attacking anyone.
- **Acknowledge the ecosystem.** Other tools exist for good reasons.
  Developers who use Instructor, PydanticAI, LangChain, or any other
  framework are potential mellea users, not opponents.
- **If a comparison is necessary**, frame it as a technical trade-off:
  "Approach A works well for X. Mellea takes a different approach
  that optimises for Y." Let the reader decide.

### Positive Framing

Lead with what's possible, not what's wrong.

- **Frame around what the reader can now do**, not what was broken
  before. "You can now validate LLM output against requirements in
  one line" beats "LLM output used to be unreliable and untestable."
- **Celebrate the community.** Credit contributors, acknowledge users
  who reported issues, highlight community-built extensions.
- **Be constructive about problems.** When describing pain points
  (necessary for motivation), frame them as shared challenges the
  community faces — not as failures of other tools.
- **Enthusiasm is fine; cheerleading is not.** Genuine excitement
  about a feature ("this one's been requested since v0.2") reads
  differently from manufactured excitement ("we're SO excited to
  announce!!!").

---

## Code-First Rule

Every content asset must include or link to working code.

- **Blog posts**: at minimum one runnable code example; ideally
  before/after comparison.
- **Social posts**: include a code snippet (screenshot for LinkedIn,
  inline for X) or link to one.
- **Videos**: show real code running with real output. No slides
  substituting for live demos.
- **All code must be tested** before publishing. Use `/validate-snippets`
  on blog posts and scripts.

---

## Mellea + Granite Positioning

Mellea is model-agnostic. It also works particularly well with Granite.
Both are true. The messaging depends on context.

### When to lead with model-agnostic

- General awareness content (first-touch blog posts, social posts to
  broad developer audience)
- Posts targeting non-IBM developers (Ollama users, vLLM users,
  OpenAI users)
- Feature announcements that apply to all backends
- Any content where the reader might think "this isn't for me because
  I don't use IBM"

**Say:** "Mellea works with Ollama, OpenAI, HuggingFace, and 100+
providers via LiteLLM."

**Don't say:** "Mellea is IBM's framework for Granite models."

### When to lead with Granite

- Posts specifically about Granite model releases or capabilities
- Content targeting IBM ecosystem developers
- Demos showing Granite-specific features (aLoRAs, Granite 4 hybrid
  models, GuardianLib with Granite)
- Content on IBM-owned channels (IBM blog, ibm-granite HF org)

**Say:** "Mellea pairs with Granite to deliver structured, reliable
outputs from small models" or "Mellea + Granite 4 micro: structured
extraction in 10 lines."

**Don't say:** "Mellea only works with IBM Granite" or "You need IBM
infrastructure to run Mellea."

### The balance

Most content should mention both: model-agnostic capability upfront,
Granite as a highlighted example. Show mellea with Ollama/OpenAI in
the getting-started section, then show the Granite-optimised path for
users who want it.

---

## Channel-Specific Adaptation

The same insight must be expressed differently on each channel. Never
copy-paste content across platforms.

| Channel | Tone | Length | Differentiator |
|---------|------|--------|----------------|
| **mellea.ai/blogs** | Practitioner deep-dive | 800-2000 words | The canonical source; technical depth, working code |
| **HuggingFace blog** | Practitioner, model/tool-focused | 800-1500 words | Ecosystem integration, model cards, Hub artifacts |
| **IBM blog** | Authoritative, enterprise-grade | 1000-2000 words | Research backing, enterprise value prop |
| **X (Twitter)** | Direct, community-native, short | 200-280 chars / thread | Speed, hot takes, what's new right now |
| **LinkedIn** | Professional but human, narrative | 800-1200 chars | Why it matters, lessons learned, career relevance |
| **YouTube** | Instructional, real code | 2-20 min | Show don't tell, durable discovery |

---

## Self-Review: Apply to All Content

Before publishing any content through any skill, verify:

| # | Check |
|---|-------|
| 1 | Are all claims **backed by evidence** (benchmarks, code, docs)? |
| 2 | Is there **zero competitor blame** — no negative mentions of other tools? |
| 3 | Is the framing **positive** — leading with capability, not with problems? |
| 4 | Does the piece include or link to **working code**? |
| 5 | Is the **Granite/model-agnostic balance** appropriate for this channel and audience? |
| 6 | Does it read like a **practitioner wrote it**, not a press release? |
| 7 | Is there at least one **honest limitation or caveat**? |
| 8 | Would you be **proud** to have this represent the project? |
