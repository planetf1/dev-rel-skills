---
name: write-technical-blog
description: >-
  Guide for writing a good technical blog post about a new code feature,
  capability, or release. Synthesizes best practices from Stripe, GitHub,
  Cloudflare, HashiCorp, and Google engineering blogs.
---

# Write a Technical Blog Post

> **Shared guidelines apply.** Before writing, read
> `skills/_shared/content-guidelines.md` for voice, values (no hype, no
> competitor blame, code-first), and Granite positioning rules. Those
> rules override any conflicting guidance below.

Use this skill when drafting or reviewing a technical blog post about a new
code feature, capability, or release. Apply the checklist and structure below
to produce a post that is credible, scannable, and genuinely useful to
developers.

## Usage

```
/write-technical-blog [topic or PR number or description]
```

If given a PR number, fetch its title, description, and changed files first.
Determine the target repo in this order:

1. Use `--repo owner/repo` if the user supplied one.
2. Otherwise auto-detect from the current working directory:
   `gh repo view --json nameWithOwner -q .nameWithOwner`.
3. If auto-detection fails, ask the user.

Then run:

```bash
gh pr view <number> --repo OWNER/REPO
```

and apply the framework below.

---

## Working from Source Material

If given a file path as input, extract text from it before proceeding:

- **PDF**: use the Read tool directly — Claude can read PDFs natively.
- **PowerPoint (.pptx)**: convert to markdown with pandoc, then read the result:
  ```bash
  pandoc path/to/file.pptx -o /tmp/slides.md
  ```
  Then use the Read tool on `/tmp/slides.md`. To work with a specific slide
  range, grep for the slide headings or read the relevant line range.
  If pandoc is not available, fall back to `python-pptx`:
  ```bash
  python3 - <<'EOF'
  from pptx import Presentation
  prs = Presentation("path/to/file.pptx")
  for i, slide in enumerate(prs.slides, start=1):
      texts = []
      for shape in slide.shapes:
          if shape.has_text_frame:
              for para in shape.text_frame.paragraphs:
                  line = " ".join(run.text for run in para.runs).strip()
                  if line:
                      texts.append(line)
      print(f"\n--- Slide {i} ---")
      print("\n".join(texts) if texts else "(no text)")
  EOF
  ```
- **Other formats**: ask the user to export to PDF or paste the text.

Once text is extracted, proceed with the steps below.

---

## Step 1: Research Before Writing

Do this before drafting a single sentence. Writing from a PR title or a user
prompt alone produces inaccurate technical claims.

### 1a. Read the implementation

In the main project repo (e.g. `generative-computing/mellea`), read:

- The source file(s) that implement the feature
- Any example scripts or notebooks that demonstrate it
- Relevant tests — they show expected behaviour, edge cases, and error conditions
- The relevant section of the docs (if it exists already)

Use `gh` to browse the repo if you don't have it checked out locally:

```bash
gh api repos/OWNER/REPO/contents/path/to/file --jq '.content' | base64 -d
```

**Goal:** verify every API signature, parameter name, and behavioural claim
before writing it down. A wrong method name in a code block destroys reader
trust instantly.

### 1b. Check existing posts in this repo

Skim recent posts in `blogs/` for the current year to:

- Avoid repeating topics or examples already covered
- Match the tone and depth level established by adjacent posts
- Identify cross-link opportunities ("If you haven't read our post on X…")

```bash
ls blogs/$(date +%Y)/
```

### 1c. Cross-source audit (vendor / external capability posts)

When the post is about a vendor capability that is also documented elsewhere
(an upstream model card, a vendor repo README, partner docs), audit *all*
public surfaces for setup and invocation guidance **before** drafting setup
steps. Drafting setup from one source and only checking others at review
time is the most common cause of mid-review rewrites.

For each external source, capture: the install command, the serve/launch
command, the import path, and the expected output. Compare. Any divergence
(different extras, different flags, different module names) becomes either:

- a deliberate choice you make in the draft (with a sentence explaining why
  you diverge from upstream), or
- an open question for the reviewer (see "Tracking unknowns" below).

Typical sources to check for an IBM/Granite-adjacent post:

- mellea docs (`docs/docs/integrations/*.md`, `docs/docs/advanced/*.md`)
- the vendor repo README (e.g. `granite-switch`, `granitelib-*`)
- the Hugging Face model card
- PyPI package metadata (extras, version constraints)

---

## Step 2: Identify the Post Type

Before writing anything, determine which type of post this is:

| Type | Use when | Key difference |
|------|----------|----------------|
| **Narrative / deep-dive** | New capability, architecture change, design decision | Story arc: problem → journey → solution |
| **Feature announcement** | New API, new option, meaningful improvement | Lead with outcome; include before/after |
| **Tutorial / how-to** | Walk a reader through using a feature end-to-end | Goal-oriented; every step is runnable |
| **Retrospective / postmortem** | Design history, trade-offs, lessons learned | Honest; show failures alongside wins |

> A changelog entry and a blog post are different documents. A changelog is a
> factual, scannable record of what changed (Added / Changed / Fixed). A blog
> post explains *why* it matters and *how* to use it. Never substitute one
> for the other.

---

## Step 3: Answer These Before Writing

These questions define the post. If any are unclear, ask the user.

1. **Who is the reader?** (existing users, practitioners new to the project,
   broad developer audience)
2. **What problem does the reader already have** that this feature solves?
3. **What should the reader be able to do after reading** that they couldn't
   before?
4. **What is the single most important insight or result?** (the thesis)
5. **Are there known limitations or trade-offs** to acknowledge?
6. **Does this pattern only apply to a subset of use cases?** If so, plan a
   best-fit callout near the top so readers can self-select early.

---

## Step 4: Post Structure

Use this skeleton, in order:

### 1. Title

- Lead with the **problem solved** or **outcome achieved**, not the feature name.
- Include the technology/feature name for SEO.
- Target 50–60 characters.
- Proven patterns:
  - `How we [solved X] in [product]`
  - `[Feature]: [one-line benefit]`
  - `[Problem] at scale: how we solved it`
- **Avoid**: "We are excited to announce…", vague superlatives, clickbait.

### 2. Opening hook (1–3 paragraphs)

- State the **problem the reader already has** in concrete terms.
- Quantify the pain if possible: "40 lines of boilerplate," "re-implemented
  in every project," "blocked the event loop."
- Include one compelling metric if available ("100× faster", "saved 600
  hours/year").
- The reader should know within 2–3 sentences: what this is, who it helps,
  why it matters now.
- **Avoid**: "We are excited to announce…", starting with the solution.
- **If the feature only applies to a subset of readers**, add a blockquote
  callout — but place it **near the Trade-offs section, not right after the
  intro**. Putting it immediately after a punchy hook kills momentum before
  the reader is invested. At the Trade-offs section it acts as positive
  framing ("here's where it shines") before the caveats.

### 3. Problem / motivation (1–2 sections)

- Show the world *before* the feature. Use real code that illustrates the
  friction.
- Explain why the old approach was hard, not just that it was hard.
- A before-snippet here sets up the before/after comparison later.

### 4. Solution walkthrough (2–4 H2 sections)

- **For tutorial-style posts: run first, explain after.** Show the complete
  working example and expected output *before* the deep walkthrough — not at
  the end. Readers want to get running, then learn why it works:
  1. Prerequisites (brief — link to installers, don't reproduce docs)
  2. The problem the example solves (2–3 sentences + diagram if spatial)
  3. Complete runnable script
  4. `run` command + expected output immediately after
  5. "Read on for a breakdown" → walkthrough sections explain each piece
  This structure means the walkthrough can use prose + micro-snippets rather
  than re-showing the full code.

- **Show the complete script once, then reference it.** In the walkthrough,
  quote only the 2–5 lines that illustrate the concept being explained — not
  the whole function again. Use `# ...` for omitted code. This prevents the
  same 40-line block appearing three times.

- **Annotate the separation between domain logic and library code** — readers
  are intimidated by long scripts. Use section comments to show what's theirs
  vs. what's boilerplate:
  ```python
  # ── Your problem-specific logic ──────────────────────
  def check_result(ctx) -> ValidationResult:
      ...

  # ── Library: this part is always the same ────────────
  sofai = SomeStrategy(s1=..., s2=...)
  result = session.instruct(..., strategy=sofai)
  ```

- **Progressive disclosure**: start with the minimal working example, then
  add complexity in labeled steps.
- Each step introduces exactly one new concept.
- Every major concept gets a code block with inline comments on non-obvious
  lines.
- Include at least one diagram for structural/relational concepts (a
  paragraph-sized prose explanation of relationships between components
  signals a diagram is needed).
- **Before/after as a diff, not two full blocks.** Often the actual change is
  one parameter. Show just that:
  ```python
  # Before
  result = session.instruct(prompt, requirements=requirements)
  # After
  result = session.instruct(prompt, requirements=requirements, strategy=sofai)
  ```
  Two full code blocks repeating 15 lines each to show one changed argument
  wastes the reader's attention.
- **Secondary or optional config**: if a parameter or mode is worth mentioning
  but not core to understanding the feature, write it as prose and link to
  the docs — do not give it a full table and section header. A full
  `s2_solver_mode` comparison table is documentation, not a blog post.

### 5. Results / demo

- Show that it works: terminal output, benchmark numbers, screenshot of
  working state.
- Real numbers beat adjectives. "p99 latency: 120ms → 18ms on a 10GB
  dataset" beats "dramatically faster."
- If a full demo exists (notebook, repo, live link), link it here.
- **Illustrative calculations** (e.g. "if S1 handles 70% of requests at 10×
  lower cost, blended cost drops ~7×") are useful but must be labeled as
  illustrative. Never present back-of-envelope math as a measured benchmark.

### 6. Honest trade-offs (brief)

- Acknowledge what the feature does *not* solve, known limitations, or who
  should not use it yet.
- One short paragraph or bullet list is enough.
- This is counterintuitive but dramatically increases reader trust.

### 7. Conclusion + CTA

- Summarize in **one sentence** (restate the core insight).
- Return to the reader's perspective: "If you're hitting [problem from
  intro], [feature] is now available…"
- Exactly **one primary CTA**: link to docs, quickstart, GitHub repo, or
  release notes. Not the marketing homepage.
- Optional secondary CTA: feedback thread, Discord/Slack, hiring link.
- **Avoid**: "We hope you find this useful", vague summaries, multiple
  competing CTAs.
- Optional: one sentence on future directions if there's a natural next
  chapter.

---

## Step 5: Code Example Rules

- **Never use screenshots for code.** Use fenced code blocks with the
  language specified (` ```python `, ` ```bash `, etc.).
- **Introduce every code block** with a sentence ending in a colon.
- **Show output** alongside non-trivial code blocks. Don't make readers
  mentally simulate execution.
- **Full working examples** for tutorials; **focused snippets** for
  illustrating a single concept.
- For omitted boilerplate, say so explicitly: `# ... existing setup omitted`.
- **Link to a complete, runnable repo** after inline snippets when possible.
- Mark placeholders with angle brackets: `<your-api-key>` (not curly braces,
  which conflict with many languages).
- Wrap lines at ~85 characters to avoid horizontal scroll.
- **Test every example.** Broken code is the fastest way to lose reader
  trust permanently.
- **For AI/ML posts: verify the demo exercises the right failure mode.**
  Don't just test that it runs — test that the small model fails for a
  *genuine reasoning gap*, not a trivial instruction-following failure
  (e.g. using a wrong color name). Run the example several times to confirm
  the failure is consistent and meaningful. If the small model solves the
  problem reliably, the demo doesn't prove the point — find a harder problem.
- **Verify model/API names against the actual source.** Use `ollama show
  <model>` to confirm parameter counts and quantisation; run the code before
  publishing. Two models with different tags but identical specs (same weights)
  provide no capability split and undermine the demo.
- **Show expected output immediately after the run command** — not in a
  separate section further down. The reader just ran something; they want to
  know if their output matches before reading on.
- **When an example uses a specific model, service, or provider**, add a
  comment or sentence noting that alternatives work:
  ```python
  # Any Ollama-compatible model pair works (e.g. llama3.1:8b + llama3.1:70b)
  s1 = OllamaModelBackend("granite4:micro")
  ```
  This prevents examples from reading as vendor prescriptions.

---

## Step 6: Tone and Prose

- **Active voice, present tense**: "The SDK sends a request" not "A request
  is sent."
- **One post, one idea.** If you find yourself writing "and also," consider
  a separate post.
- **Avoid the curse of knowledge**: every acronym gets spelled out on first
  use; every internal term gets a one-sentence introduction.
- **Bold and inline code sparingly** — no more than 10% of prose text.
  Overuse makes nothing feel important. Use bold for the first mention of a
  product/library name, key terms, and standalone emphasis lines. Avoid
  bolding whole sentences.
- **Standalone emphasis lines work.** A single short bold line between
  paragraphs ("**There's a better way: use both.**") creates impact that
  multi-sentence bold phrases don't.
- **Section titles as questions** ("Is This Right for Your Workload?")
  improve scannability and signal to readers whether a section applies to them.
- **Use US spelling** in posts targeting a broad developer audience,
  regardless of the author's locale — consistency across the blog matters
  more than author voice.
- **Let engineers own the voice.** First-person bylines ("I built this
  because…") build more trust than anonymous corporate "we."
- **Show failures.** Posts that include dead ends before the solution are
  more credible and more shareable than posts that show only the finished
  answer. For AI/ML posts this means showing actual model failure output, not
  just "and then it fails." The specific failure message IS the story.

---

## Step 7: SEO and Discoverability

- **URL slug**: `/blog/feature-name-benefit` not `/blog/post-12345`.
- **Title**: primary keyword in the first 3–4 words.
- **H2s**: each is an opportunity for a secondary keyword — write them as
  questions or statements a developer would search ("How to handle rate
  limiting").
- **Meta description** (150–160 chars): what the reader will be able to *do*
  after reading + one mild CTA ("See the full example").
- **Internal links**: link to related posts and docs; go back and add links
  from older posts to new ones.
- **External links**: official docs, RFCs, GitHub issues that explain design
  decisions — signals rigor.
- **Publish release-announcement posts on the same day as the release.**
- **Cross-post to dev.to or Hashnode** with a canonical URL pointing back to
  the original.
- **Promote in context**: GitHub README, changelog, release notes, Hacker
  News "Show HN" (plain informative title, no marketing language), relevant
  subreddits/Discord channels with 1–2 sentences of context, not just a link.

---

## Step 8: Write the Draft to a File

After drafting the post, detect the target location from the current working directory:

**If the CWD is a Next.js blog repo with `content/blogs/`** (e.g. mellea-website):
- Write to `content/blogs/<slug>.md` — never prefix with `blog-`, `post-`, or `article-`.
  The `/blogs/` URL path already provides that context.
- Check the repo's `AGENTS.md` for slug naming rules before choosing the filename —
  the slug becomes a permanent public URL with no redirect support.
- Check `content/blogs/` for existing posts to match frontmatter schema and spot
  cross-link opportunities before writing.

**Otherwise** (generic working directory or no `content/blogs/`):
- Write to `blog-<slug>.md` in the CWD, where `<slug>` is a short kebab-case
  summary of the topic (e.g. `blog-rate-limiting.md`).
- If the argument was a PR number, use `blog-pr-<number>-<short-title>.md`.

In both cases: use the Write tool to create the file without asking. Tell the user
the filename and location after writing.

---

## Step 8b: Tracking Unknowns During Review

A draft will often have setup details, version pins, or terminology choices
that need verification but don't block the rest of review. The pattern
that works: tag each unknown in the post body **and** mirror to a
consolidated list in the PR description.

**In the post body** — at the relevant point, add a blockquote prefixed
`**Reviewer note —**`:

```markdown
> **Reviewer note — `[vllm20]` vs `[vllm]`:** the upstream README leads
> with `[vllm]`; we've validated `[vllm20]` only. Decide which to lead
> with before merge.
```

This means a reviewer landing cold sees the uncertainty exactly where it
matters, not in a separate document.

**In the PR body** — a numbered "Open questions for the reviewer" section
that lists the same items consolidated, plus a one-line pointer back at
the in-place callouts:

```markdown
The N reviewer callouts in the post (one per open question above) are
tagged `**Reviewer note —**` so they're easy to grep before publish.
```

**Pre-merge cleanup is mandatory.** All `Reviewer note —` blockquotes are
draft-only; remove every one before publish. A self-review checklist item
covers this (see #24 below).

---

## Step 9: Self-Review Checklist

Before finalizing, verify each item:

| # | Check |
|---|-------|
| 1 | Does the first paragraph state the **problem the reader already has**? |
| 2 | Is the motivation explained **before** the API/solution? |
| 3 | Does the title name a **problem or outcome**, not just the feature? |
| 4 | Is there a **before/after** comparison with labeled snippets? |
| 5 | Does every code block have **tested, runnable examples** with output shown? |
| 6 | Are **limitations or trade-offs** acknowledged? |
| 7 | Is there exactly **one primary CTA** at the end? |
| 8 | Can a skimmer understand the post from **headings alone**? |
| 9 | Is advanced content in a **late section** so beginners can stop early? |
| 10 | Are all code blocks using **fenced syntax with language specified**? |
| 11 | Does the post have a **single clear thesis** (one idea, not a roundup)? |
| 12 | Does it read like an **engineer wrote it**, not a press release? |
| 13 | If the pattern only fits a subset of use cases, is the **best-fit callout near Trade-offs**, not immediately after the hook? |
| 14 | Are any illustrative calculations **explicitly labeled as illustrative**, not presented as benchmarks? |
| 15 | If examples use a specific model or service, is there a note that **alternatives work**? |
| 16 | Are secondary/optional config options in **prose + doc link** — no full table or code block for something not in the example? |
| 17 | Is the **complete runnable script shown once** (Quick Start / run section), with micro-snippets in the walkthrough? |
| 18 | Is **expected output shown immediately after the run command**, not separated by paragraphs? |
| 19 | Has the demo been tested to confirm S1/small model **fails for the right reason** (reasoning gap, not trivial failure)? |
| 20 | Are **ASCII diagrams verified for alignment** — corner characters (`┐`, `│`, `┘`) at the same column? |
| 21 | Are **doc links grouped at the end** (source + API ref + guide) rather than scattered mid-post? |
| 22 | (Mellea IVR posts) Does every repair loop use **`RepairTemplateStrategy`**, not `RejectionSamplingStrategy`? |
| 23 | (Mellea IVR posts) Does the post distinguish **detection guarantees from repair success**? |
| 24 | After any hook rewrite, has the **title and excerpt been re-read** to check they still match the lede? Title/excerpt drift is the most common stale-after-rewrite failure mode. |
| 25 | Have all **`Reviewer note —` blockquotes been removed** before publish? They are draft-only annotations and must not ship. |

---

## Common Mistakes to Avoid

- **Writing from description alone**: a PR title or user prompt is not
  sufficient source material. Read the implementation, examples, and tests
  before making technical claims.
- **Changelog-as-blog-post**: a bulleted list of what changed with no
  narrative earns neither trust nor attention.
- **Burying the lede**: the most important result goes in the first 3
  paragraphs, not the conclusion.
- **Overlong intro**: "Software is complex and APIs are hard" before getting
  to the point loses most readers.
- **Illustrative math presented as fact**: "cost drops 6–7×" is back-of-envelope
  math, not a benchmark. Label it explicitly or don't include it.
- **Vague superlatives**: "dramatically faster" is marketing; a benchmark
  with methodology is engineering.
- **Missing visuals for structural concepts**: if you're explaining
  relationships between components in 2+ paragraphs, use a diagram.
- **Treating all readers as the same audience**: a post for existing advanced
  users looks different from one for new practitioners.
- **Multiple competing CTAs**: pick one.
- **Quick Start at the end**: for tutorial-style posts, setup + run should
  appear early (before the deep explanation), not as a footnote after 2000
  words of theory. Readers want to run the code, then learn why it works.
- **Config sections that become documentation**: a `s2_solver_mode` table
  with three rows is API docs. If the parameter isn't used in the example,
  one sentence and a link to the docs is enough.
- **Code block creep**: the same 40-line example appearing in Quick Start,
  the walkthrough, the before/after, and the Going Further section makes posts
  feel padded and intimidates readers. Show it once completely; use
  micro-snippets and prose for everything after.
- **Untested demo failure mode**: writing "the small model fails" without
  checking *how* it fails. Trivial failures (wrong color name) are less
  credible than genuine reasoning failures (wrong constraints violated). Test
  that the failure is meaningful before publishing it.
- **Misaligned ASCII diagrams**: box-drawing characters (`┐`, `│`, `┘`, `└`)
  must be in the same column or the diagram looks broken in monospace. Verify
  with `python3 -c "print(len('<line>'))"` for each line.

---

## Mellea-specific pitfalls

These apply to any blog post using Mellea's IVR / requirements pattern.

**Strategy class: always use `RepairTemplateStrategy` for repair loops.**
`RejectionSamplingStrategy.repair()` returns the unchanged action and context —
same prompt, no failure feedback injected. `RepairTemplateStrategy` builds a
repair prompt that includes the failed requirement description or
`ValidationResult.reason`. If your blog describes repair ("Mellea feeds the
failure reason back into the next attempt"), the code must use
`RepairTemplateStrategy`, not `RejectionSamplingStrategy`.

```python
from mellea.stdlib.sampling import RepairTemplateStrategy  # correct
# NOT: from mellea.stdlib.sampling import RejectionSamplingStrategy
```

**Local inference platform matrix for editorial notes.**
When the model is not yet on Ollama (e.g. safetensors-only on HuggingFace),
the editorial note should cover both reviewer platforms:
- **Apple Silicon (macOS)** → `mlx-vlm`: `uv run python -m mlx_vlm.server --model <id>`
- **Linux + GPU** → `vLLM`: `vllm serve <id>`

vLLM does not run natively on macOS (Linux/CUDA only). `vllm-metal` is a
community plugin that targets Apple Silicon but supports text-only models.
Do not recommend vLLM as the macOS path.

**Detection and repair are separate guarantees.** When using `validation_fn`
or `requirements=`, the validation layer reliably detects failures. Whether
the model self-corrects depends on its scale — a 4b vision model anchors to
what it reads in the image and will often ignore repair instructions. Be
explicit in the blog: the value of programmatic checks is guaranteed detection,
not guaranteed repair. Repair success scales with model size.
