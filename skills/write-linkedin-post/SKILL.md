---
name: write-linkedin-post
description: >-
  Write high-engagement LinkedIn posts about technical content, especially
  open-source project releases and developer tools. Applies research-backed
  patterns from 2025-2026 LinkedIn algorithm analysis and developer
  practitioner data (LangChain, Hugging Face, Pydantic, FastAPI).
---

# Write a LinkedIn Post

> **Shared guidelines apply.** Before writing, read
> `skills/_shared/content-guidelines.md` for voice, values (no hype, no
> competitor blame, code-first), and Granite positioning rules. Those
> rules override any conflicting guidance below.

Use this skill to write a LinkedIn post about a technical feature, release,
milestone, or concept — optimized for developer audience engagement on
LinkedIn.

LinkedIn is fundamentally different from X/Twitter. LinkedIn rewards depth,
narrative, and expertise over speed. Posts live 24-48 hours in the feed
with a long search tail. The algorithm prioritizes "knowledge and advice"
content — educational posts get 3-5x more reach than announcements.

## Usage

```
/write-linkedin-post [topic, PR number, path/to/post.md, or description]
```

### Input types

**PR number** — fetch context first. Auto-detect the repo from the working
directory (`gh repo view --json nameWithOwner -q .nameWithOwner`), or use
`--repo owner/repo` if provided:

```bash
gh pr view <number> --repo OWNER/REPO
```

**Markdown file (blog post)** — read the file, then extract:
1. The thesis / core insight (usually in the opening or conclusion)
2. The single most compelling metric or before/after
3. The primary CTA link (docs, repo, blog URL)
4. Any code snippet that demonstrates the key point

A LinkedIn post derived from a blog is NOT a summary. It's a standalone
piece of content that delivers value on its own and makes the reader want
the full story. The blog link is supplementary, not the point.

---

## Step 1: Classify the Content Type

Determine which type of content this is. The type dictates the structure
and tone:

| Type | LinkedIn angle | Optimal length |
|------|---------------|----------------|
| **Release announcement** | Problem this solves + what changed + what it unlocks | 800-1,200 chars |
| **New feature** | Before/after narrative — how the world changed | 600-1,000 chars |
| **Performance improvement** | Numbers + methodology + what you learned | 800-1,200 chars |
| **Architecture / design decision** | "Here's why we chose X over Y" | 1,200-2,000 chars |
| **Milestone** | Journey narrative, not the number | 1,000-1,500 chars |
| **Tutorial / how-to** | "I figured out how to do X. Here's the approach:" | 1,000-1,500 chars |
| **Concept / opinion** | Contrarian claim or non-obvious insight | 600-1,200 chars |

---

## Step 2: Write the Post

### Structure

LinkedIn posts have a hard 3,000 character limit. The "see more" fold
appears at ~210 characters on mobile (first 2-3 lines). Everything above
the fold must create a reason to expand.

```
Lines 1-2:  HOOK — bold claim, surprising stat, or problem statement
            (must work in ~210 characters)
[blank line]
Lines 3-8:  CONTEXT — what changed, why it matters, who benefits
[blank line]
Lines 9-12: KEY DETAILS — 1-3 specific points with concrete evidence
[blank line]
Lines 13-14: CTA — genuine question to drive comments
[blank line]
Line 15:    LINK NOTE — "Full details on our blog — link in the comments."
[blank line]
Lines 16-17: HASHTAGS — 3-5 specific hashtags
```

### The hook (first 2 lines)

The first 210 characters decide whether anyone reads the rest. These
rules are non-negotiable:

**What works:**
- Lead with the **problem**, not the product or version
- Open with a **number or specific claim**: "We cut inference latency from 3.2s to 800ms"
- Use the **discovery frame**: "We just learned something surprising about how small models fail in production"
- State a **contrarian view**: "Most LLM reliability problems aren't model problems. They're engineering problems."

**What kills the hook — never do these:**
- "Excited to announce..." (press release voice)
- "Today we're shipping..." (corporate announcement)
- "I'm thrilled to share..." (sycophantic opener)
- Leading with a version number ("v0.5.0 is out!")
- Starting with the project name before establishing why it matters
- Any sentence that could describe any product ("We've been working hard on improvements")

### Body content

**One sentence per line.** White space is critical on LinkedIn. Dense
paragraphs kill dwell time (the algorithm's highest-weight signal).

No more than 3 sentences in a block before a line break.

**Bullet points and numbered lists** increase dwell time and "see more"
click-through, but don't overdo it. A post that's 100% bullets reads
like a slide deck. Mix prose and lists.

**Include specific evidence:**
- Real numbers ("reduced from 40 lines to 8", not "dramatically simpler")
- Named technologies ("works with Ollama, vLLM, and OpenAI", not "supports many backends")
- Concrete use cases ("extracting structured data from customer support tickets", not "various applications")

**One honest caveat builds more trust than the rest of the post combined.**
Acknowledge what this doesn't solve or who shouldn't use it yet.

### Formatting rules

- **No more than 1-2 emojis per post.** Use functionally (as bullet
  markers or section breaks), not decoratively. Heavy emoji use reads
  as marketing fluff to engineers.
- **Bold sparingly.** One or two key phrases, not every other line.
- **No inline code on LinkedIn.** LinkedIn has no code formatting. For
  short code references, use backtick-style emphasis or just name the
  function. For code demos, use a carousel image or screenshot (see
  Step 4).

---

## Step 3: Handle the Link

**Critical rule: external links in the LinkedIn post body reduce reach
by approximately 60%.** This is the single most impactful formatting
decision.

### Recommended approaches (in order of preference)

1. **Link in first comment.** Write the post with no link in the body.
   End with "Link to the full post in the comments." Draft the comment
   text as part of the output.

2. **Link in profile bio.** If the poster regularly shares content from
   one source, update their bio with the link. End the post with
   "Link on my profile."

3. **Carousel post (document/PDF).** Create a multi-slide summary of
   the blog's key points. Carousels get 6.6% engagement rate — 278%
   more than video, 596% more than text-only. Include the blog URL on
   the final slide. No link penalty because the content is native.

4. **Link in the post body.** Last resort. Accept the ~60% reach cut.
   Only use when the post is supplementary to existing distribution
   (e.g., the blog already has its own traffic).

### The first comment

Always draft the first comment alongside the post. It should include:
- The blog URL (if linking to a post)
- A one-sentence teaser that adds something not in the main post
- Optionally, a second link (GitHub repo, docs, demo)

The author MUST post this comment immediately after publishing — within
30 seconds if possible. Early comments from the author trigger
engagement signals.

---

## Step 4: Media Recommendation

LinkedIn posts with media get significantly higher engagement. Recommend
one of these for every post:

| Format | Engagement | Best for |
|--------|-----------|----------|
| **Carousel (PDF)** | 6.6% avg | Release announcements, before/after, architecture comparisons |
| **Native video** | 5.6% avg | Quick demos, "here's what changed" (30-90s sweet spot) |
| **Image** | 4.85% avg | Architecture diagrams, benchmark charts, code screenshots |
| **Text-only** | 2-4% avg | Strong opinion pieces, short observations |

For **code demonstrations**, recommend a screenshot with syntax
highlighting (Carbon or ray.so, dark theme, include language label)
rather than inline text. LinkedIn renders code poorly.

For **release announcements**, a carousel with 4-6 slides is ideal:
1. Problem statement (title slide)
2. Before code / old approach
3. After code / new approach
4. Key numbers / benchmarks
5. Getting started command
6. Blog link + CTA

---

## Step 5: Posting Account Strategy

**Personal profiles outperform company pages on every organic metric:**
- 5-8x higher engagement rate
- 10-72x more impressions per follower
- 65% of LinkedIn feed is personal content; 5% is company content

### Recommendation

- The **maintainer, DevRel lead, or project founder** posts from their
  personal account. This is the primary distribution channel.
- The **company/project page** reposts or gets tagged. It exists for
  credibility, job postings, and paid ads — not organic reach.
- If multiple team members can post, coordinate: one posts, others
  engage in comments within the first 60 minutes.

Note the recommended posting account in the output. If the user hasn't
specified who will post, flag this as a decision to make.

---

## Step 6: Hashtags

**3-5 specific, relevant hashtags.** Place at the end of the post.

### Rules

- **Specific beats generic.** `#LLMOps` `#StructuredOutput` `#Ollama`
  over `#AI` `#MachineLearning` `#Innovation`
- **Name ecosystems and tools.** `#Pydantic` `#Python` `#OpenSource`
  signal topic to the algorithm's semantic retrieval system.
- **Never use:** `#Innovation` `#Leadership` `#Digital` `#FutureOfWork`
  — these are noise.
- LinkedIn's LLM-based retrieval (deployed Aug 2025) understands post
  meaning directly, so hashtags matter less for discovery than they
  once did. They still help with topic classification.

---

## Step 7: Timing and Engagement

### Posting timing

- **Tuesday-Thursday, 8-10 AM** in the target audience's timezone
- **Thursday 10 AM** is the single highest-engagement slot
- **Never post more than once in 24 hours** — reach penalty on all posts
- **Never edit the post after publishing** — can reset distribution

### First 60 minutes (critical)

LinkedIn tests posts with 2-5% of the author's network initially.
Engagement velocity in this window determines expanded distribution.

- **Reply to every comment within 30 minutes.**
- Coordinate with teammates to engage early (authentic comments only —
  the algorithm detects pod behavior at 97% accuracy).
- The author's own replies count as engagement signals.

Include a timing recommendation in the output.

---

## Step 8: Output Format

Write the post to a markdown file in the current working directory:

- **Filename convention**: `linkedin-<slug>.md` where `<slug>` is a
  short kebab-case summary of the topic.
- If the argument was a PR number, use `linkedin-pr-<number>-<short-title>.md`.
- If the argument was a markdown file, derive the slug from the filename.
- Use the Write tool to create the file. Do not ask — just write it.
- Tell the user the filename after writing.

### Output file format

```markdown
# LinkedIn Post: <topic>

> Source: <PR url, file path, or description>
> Date: <today's date>

---

## Post (~NNN chars)

[the full post text, formatted exactly as it should appear on LinkedIn]

---

## First comment

[the comment to post immediately after publishing, including the link]

---

## Alternative hook

[an alternative opening 2 lines using a different hook formula]

---

## Media recommendation

**Format:** [carousel / image / video / text-only]
**What to create:** [specific description of the visual]
[If carousel: outline of each slide]

## Posting notes

- **Account:** [personal / company page / recommendation to decide]
- **Timing:** [recommended day and time]
- **Hashtags:** [the hashtags, already included in post but listed here for reference]
- **Engagement plan:** Reply to all comments within 30 min. [Any specific coordination notes.]
```

---

## Step 9: Self-Review Checklist

| # | Check |
|---|-------|
| 1 | Does the hook work **standalone in ~210 characters** (above the fold)? |
| 2 | Does it lead with the **problem or insight**, not the product name? |
| 3 | Is the post **800-1,200 characters** (or justified if longer)? |
| 4 | Is there **one sentence per line** with adequate white space? |
| 5 | Is there **no external link in the post body** (link in comment instead)? |
| 6 | Does the post **deliver standalone value** without clicking any link? |
| 7 | Is there at least one **specific number or concrete claim**? |
| 8 | Is there at least one **honest caveat or limitation**? |
| 9 | Does it end with a **genuine question** (not engagement bait)? |
| 10 | Are there **3-5 specific hashtags**, no generic ones? |
| 11 | Is a **first comment** drafted with the link and a teaser? |
| 12 | Is there a **media recommendation** (carousel, image, or video)? |
| 13 | Does it read like an **engineer wrote it**, not a marketing team? |
| 14 | Is this **fundamentally different content** from the X/tweet version? |
| 15 | Has a **posting account** been recommended (personal, not company page)? |

---

## Common Mistakes to Avoid

- **Cross-posting the tweet.** LinkedIn and X are different platforms
  with different audiences, formats, and algorithms. A tweet thread
  copy-pasted to LinkedIn will underperform. Write LinkedIn-native
  content that emphasizes the "why" narrative.
- **Putting the link in the post body.** ~60% reach reduction. Always
  use the first comment. This is the single biggest tactical mistake.
- **"Excited to announce" opener.** The algorithm and the audience both
  penalize this. It signals corporate comms, not practitioner content.
- **Company page as primary distribution.** Personal profiles get 5-8x
  the engagement. The company page reposts.
- **Generic hashtags.** `#AI` `#Innovation` `#Technology` are noise.
  Use ecosystem-specific tags.
- **No engagement plan.** The first 60 minutes determine the post's
  lifetime reach. If the author can't be available to reply to
  comments, delay the post to a time they can.
- **Text-only for a release announcement.** Release posts with a
  carousel or code screenshot dramatically outperform plain text.
- **Over-length posts for minor updates.** A 2,000-character post for
  a bug fix signals the author doesn't know what's important. Match
  length to significance.
- **Multiple competing links.** One primary destination per post.
  Additional links go in the first comment.
- **Engagement bait.** "Comment YES if you agree" / "Like = X, Repost
  = Y" — LinkedIn's algorithm detects and suppresses these patterns.
  Ask a genuine question that invites thoughtful replies.

---

## LinkedIn vs X: Quick Reference

| Dimension | LinkedIn | X/Twitter |
|-----------|----------|-----------|
| Audience | Eng managers, CTOs, enterprise devs | Individual contributors, OSS maintainers |
| Content lifespan | 24-48h feed + long search tail | 2-4h peak then gone |
| Optimal length | 800-1,200 chars | 200-280 chars |
| Link handling | In first comment (body = -60% reach) | In tweet (accepted) |
| Tone | Professional but human. "I built/learned" | Casual, terse, hot takes |
| Best format | Carousel > video > image > text | Text + media > text-only |
| Discovery | Semantic/LLM matching to interests | Follower timeline + For You |
| Virality | Deliberately suppressed. Depth over breadth. | Designed for virality |

---

## Related Skills

- `/write-tweet` — write the X/Twitter version of the same content
  (different content, not a cross-post)
- `/write-technical-blog` — write the blog post that the LinkedIn post
  will link to
- `/release-blog` — draft a release blog post, then use this skill to
  write the LinkedIn promotion
- `/de-llmify` — run over the LinkedIn post before publishing to
  remove LLM writing patterns
- `/link-preview` — generate Open Graph tags for the blog post so the
  link renders well when shared in the first comment
