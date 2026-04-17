---
name: write-youtube-script
description: >-
  Write YouTube video scripts for technical content — both short demos
  (2-5 min) and longer walkthroughs (10-20 min). Covers structure, pacing,
  screen recording notes, and thumbnail/title optimization for developer
  audience discovery.
---

# Write a YouTube Video Script

> **Shared guidelines apply.** Before writing, read
> `skills/_shared/content-guidelines.md` for voice, values (no hype, no
> competitor blame, code-first), and Granite positioning rules. Those
> rules override any conflicting guidance below.

Use this skill to write a script or outline for a technical YouTube video
about a feature, release, tutorial, or concept. Supports two formats:
short demos and longer walkthroughs.

YouTube is the durable channel — videos remain discoverable for months or
years. A well-titled tutorial video compounds in value long after a tweet
or LinkedIn post has faded.

## Usage

```
/write-youtube-script [topic, PR number, path/to/post.md, or description]
```

Optional flags:
- `--short` — 2-5 minute demo (default)
- `--long` — 10-20 minute walkthrough

### Input types

**PR number** — fetch context first:

```bash
gh pr view <number> --repo OWNER/REPO
```

**Markdown file (blog post)** — read the file, extract the core demo
path. A video is NOT a narrated blog post. It should show the thing
working, not recite the text.

---

## Step 1: Choose the Format

| Format | Duration | Use when |
|--------|----------|----------|
| **Short demo** | 2-5 min | Single feature, release highlight, "here's what changed" |
| **Long walkthrough** | 10-20 min | End-to-end tutorial, architecture deep-dive, multi-step workflow |

Short demos are higher ROI for release promotion. Long walkthroughs are
higher ROI for search/discovery.

---

## Step 2: Write the Script

### Short Demo (2-5 min)

```
[0:00-0:15] HOOK
- Show the end result first. "Here's what we're building / what changed."
- One sentence: what problem this solves.
- Show the terminal/output/demo — visual proof before explanation.

[0:15-0:45] CONTEXT
- What was the problem before? (Brief — 2-3 sentences max.)
- Who hits this problem?

[0:45-3:30] THE DEMO
- Live coding or terminal walkthrough. Real code, real output.
- Narrate what you're doing and why, not just what you're typing.
- One concept at a time. Pause briefly between steps.
- Show errors and how to fix them — this builds trust.

[3:30-4:30] KEY TAKEAWAY
- What did we just see? One sentence summary.
- Where to learn more (docs link, blog post).
- "Try it: pip install mellea" or equivalent one-liner.

[4:30-5:00] CLOSE
- Subscribe/like CTA (brief, not begging).
- "Link to the docs and blog post in the description."
```

### Long Walkthrough (10-20 min)

```
[0:00-0:30] HOOK
- Show the finished working system. "By the end, you'll have X."
- State what the viewer will be able to do that they can't now.

[0:30-2:00] OVERVIEW
- What we're building, why, and what you need installed.
- Show the architecture or flow if it helps (diagram or whiteboard).

[2:00-15:00] BUILD (3-5 sections)
- Each section: explain what we're about to do → do it → show it works.
- Section breaks: "Now that X works, let's add Y."
- Keep terminal/editor visible. No slides in the build section.
- Show real output after each step — never skip verification.
- If something breaks, show the fix. Don't edit out mistakes.

[15:00-17:00] FULL DEMO
- Run the complete thing end-to-end without stopping.
- This is the proof that everything works together.

[17:00-19:00] RECAP + NEXT STEPS
- What we built, what concepts were covered.
- What to explore next (link to docs, related videos).
- "All the code is in the repo — link in the description."

[19:00-20:00] CLOSE
- Brief CTA. "If this helped, subscribe for more."
```

---

## Step 3: Screen Recording Notes

For each section, note what should be **visible on screen**:

| Notation | Meaning |
|----------|---------|
| `[TERMINAL]` | Terminal/shell in focus |
| `[EDITOR]` | Code editor in focus |
| `[BROWSER]` | Browser showing docs/output/UI |
| `[DIAGRAM]` | Architecture diagram or whiteboard |
| `[SPLIT]` | Side-by-side (e.g., editor + terminal) |
| `[OUTPUT]` | Just the output/result (fullscreen) |

Include these markers in the script so the recorder knows what to show.

### Recording guidelines

- **Font size 18-20pt minimum** in terminal and editor. Viewers watch
  on phones.
- **Dark theme** for terminal and editor. Light themes wash out on
  compressed video.
- **Clean desktop.** Hide bookmarks bar, notifications, unrelated tabs.
- **1920x1080 recording resolution.** YouTube compresses hard below this.
- **Type at readable speed.** If the command is long, have it ready to
  paste but explain what each part does.
- **Pause 1-2 seconds** after showing output before moving on. Let the
  viewer read.

---

## Step 4: Title and Thumbnail

### Title rules

YouTube search is the primary discovery mechanism. Titles must be
search-optimized.

- **Lead with what the viewer will learn or build**, not the product name.
- Include the **technology name** for search (e.g., "Structured LLM
  Output with Python" not just "New Feature Demo").
- **Under 60 characters** — longer titles get truncated on mobile.
- Proven patterns:
  - `How to [do X] with [tool] in [N] minutes`
  - `[Tool] [version]: [what's new / what it enables]`
  - `Build [thing] with [tool] — step by step`
  - `[Problem]? Here's how to fix it with [tool]`

**Never:** "AMAZING NEW FEATURE!!!", clickbait, all-caps.

### Thumbnail guidance

- **Text overlay:** 3-5 words max, readable at phone size.
- **Show code or terminal output** — developers click on code, not faces.
- **High contrast.** Dark background, bright text.
- **1280x720 minimum.**

---

## Step 5: Description Template

```
[One-sentence summary of what the video covers]

[Timestamp chapters — YouTube auto-creates chapters from these]
0:00 — Intro
0:15 — The problem
0:45 — Demo
3:30 — Key takeaway

Links:
- Docs: [url]
- Blog post: [url]
- GitHub: [url]
- Install: pip install mellea

#hashtag1 #hashtag2 #hashtag3
```

Always include timestamps — they create clickable chapters and improve
watch time metrics.

---

## Step 6: Output Format

Write the script to a markdown file in the current working directory:

- **Filename convention**: `video-<slug>.md`
- If `--short`: `video-demo-<slug>.md`
- If `--long`: `video-walkthrough-<slug>.md`
- Use the Write tool. Do not ask — just write it.
- Tell the user the filename after writing.

### Output file format

```markdown
# Video Script: <topic>

> Source: <PR url, file path, or description>
> Date: <today's date>
> Format: <short demo / long walkthrough>
> Estimated duration: <N minutes>

---

## Title options

1. <primary title> (<N chars>)
2. <alternative title> (<N chars>)

## Thumbnail text

<3-5 word overlay suggestion>

---

## Script

[the full script with timing markers and screen recording notes]

---

## Description

[ready-to-paste YouTube description with timestamps and links]

---

## Recording checklist

- [ ] Font size 18pt+ in terminal and editor
- [ ] Dark theme enabled
- [ ] Clean desktop, no notifications
- [ ] 1920x1080 recording resolution
- [ ] Test all commands before recording
- [ ] Have paste-ready versions of long commands
```

---

## Step 7: Self-Review Checklist

| # | Check |
|---|-------|
| 1 | Does the video **show the result in the first 15 seconds**? |
| 2 | Is every code section **real and tested**, not pseudocode? |
| 3 | Is the title **under 60 characters** and search-optimized? |
| 4 | Are **screen recording notes** included for every section? |
| 5 | Does the script **narrate the why**, not just the what? |
| 6 | Is the pacing right — **one concept per section**, pauses after output? |
| 7 | Is there a **YouTube description** with timestamps and links? |
| 8 | Does the video stand alone — viewer doesn't need to read the blog first? |
| 9 | For short demos: **under 5 minutes**? For long: **under 20**? |
| 10 | Is there exactly **one CTA** (docs link, install command, or subscribe)? |

---

## Common Mistakes to Avoid

- **Narrating a blog post.** Video should show, not tell. If you're
  reading paragraphs aloud, write a blog post instead.
- **Skipping the hook.** Viewers decide in 10 seconds. Show the
  result first, then explain how to get there.
- **Tiny font.** If a phone viewer can't read the code, they leave.
  18pt minimum.
- **No timestamps.** Without chapters, viewers can't skip to what they
  need. Watch time drops.
- **Over-editing.** Small mistakes and recovery are more authentic than
  a perfectly smooth take. Edit for length, not for perfection.
- **Slides in a coding video.** Developers want to see code, not
  bullet points. Use diagrams sparingly and only for architecture.
- **"Don't forget to like and subscribe" at the start.** Earn the CTA
  by delivering value first. Put it at the end, briefly.

---

## Related Skills

- `/write-technical-blog` — write the companion blog post
- `/write-tweet` — promote the video on X
- `/write-linkedin-post` — promote the video on LinkedIn (embed natively
  or link in first comment)
- `/validate-snippets` — test all code from the script before recording
- `/de-llmify` — run over the script narration to remove robotic phrasing
