---
name: release-launch-plan
description: >-
  Orchestrate the full release content workflow: assess readiness, generate
  a checklist, and walk through each phase (draft, validate, promote) by
  invoking the appropriate content skills. Works with the release content
  playbook in mellea-dev-rel.
---

# Release Launch Plan

> **Shared guidelines apply.** Before writing, read
> `skills/_shared/content-guidelines.md` for voice, values (no hype, no
> competitor blame, code-first), and Granite positioning rules. Those
> rules override any conflicting guidance below.

Use this skill to plan and execute the content side of a release. It
assesses what exists, generates a checklist of what's needed, and walks
through each step — calling other skills as appropriate.

This is an orchestration skill. It produces a plan, not content. The
content skills (`/write-technical-blog`, `/write-tweet`,
`/write-linkedin-post`, `/write-youtube-script`, `/release-blog`) do
the actual writing.

## Usage

```
/release-launch-plan [--tag vX.Y.Z] [--repo owner/repo]
```

Defaults:
- `--tag` = latest release (or upcoming if milestone exists)
- `--repo` = auto-detected from the current working directory

---

## Step 1: Gather Context

### 1.1 Identify the release

```bash
gh repo view --json nameWithOwner -q .nameWithOwner
```

If `--tag` was provided, fetch that release. Otherwise check for:

1. A GitHub milestone with "release" or a version number in the title:
   ```bash
   gh api repos/OWNER/REPO/milestones --jq '.[] | select(.state=="open") | {title, due_on, open_issues, closed_issues}'
   ```

2. The latest release:
   ```bash
   gh release view --repo OWNER/REPO --json tagName,name,publishedAt,body,url
   ```

Record: version, release date (actual or target), release URL.

### 1.2 Determine the hero narrative

If working from a milestone (pre-release), examine the issues:

```bash
gh issue list --repo OWNER/REPO --milestone "MILESTONE" --state all --json number,title,labels,state --limit 50
```

Group issues by theme. Identify the largest cluster of features or the
single highest-impact item. Ask:

- What is the one thing a developer would care about most?
- What's the before/after story?
- Does this serve the primary audience (local-LLM builders, framework users)?

If working from a shipped release, use the release body to identify
the hero — the same analysis as `/release-blog` Step 7.

Present the hero narrative candidates to the user for confirmation
before proceeding.

### 1.3 Determine release size

Based on the version number and scope of changes:

| Size | Version pattern | Content scope |
|------|----------------|---------------|
| **Patch** | 0.X.Y where Y increments | X thread only. Blog optional. |
| **Minor** | 0.X.0 | Blog + X + LinkedIn. YouTube optional. |
| **Major** | X.0.0 or significant milestone | All channels. |

Present the recommended content scope to the user. Adjust based on
their input.

---

## Step 2: Assess Current State

Check what content already exists for this release:

### In the current working directory

```bash
ls -la blog-*vX.Y.Z* tweet-*vX.Y.Z* linkedin-*vX.Y.Z* video-*vX.Y.Z* 2>/dev/null
```

### In the mellea-dev-rel repo (if accessible)

Check the monthly content files for any drafts in progress.

### On the live site

Use WebFetch to check if a blog post already exists at
`https://mellea.ai/blogs/` that covers this release.

### GitHub issues

Check for an existing tracking issue:

```bash
gh issue list --repo OWNER/DEVREL_REPO --search "vX.Y.Z" --limit 5
```

Report what exists and what's missing.

---

## Step 3: Generate the Checklist

Based on the release size and current state, produce a checklist.
Mark items that already exist as done.

```markdown
## Release Content Plan: <project> vX.Y.Z

**Release date:** <date>
**Hero narrative:** <one sentence>
**Release size:** <patch / minor / major>
**Content lead:** <TBD — ask user>

### Phase 1: Draft (due T-7)

- [x/blank] Blog post — `/release-blog --tag vX.Y.Z` or `/write-technical-blog`
- [x/blank] Blog validated — `/validate-snippets`
- [x/blank] Blog de-LLMified — `/de-llmify`
- [x/blank] Blog PR submitted to mellea-website

### Phase 2: Social (due T-3)

- [x/blank] X thread — `/write-tweet blog-post.md`
- [x/blank] LinkedIn post — `/write-linkedin-post blog-post.md`
- [x/blank] Link preview — `/link-preview blog-post.md`
- [x/blank] YouTube script — `/write-youtube-script blog-post.md`
- [x/blank] IBM approval submitted (if needed)
- [x/blank] HF blog coordinated with ibm-granite team

### Phase 3: Launch (T-0)

- [ ] Release live on PyPI + GitHub
- [ ] Blog merged and live on mellea.ai/blogs/
- [ ] X thread posted
- [ ] LinkedIn posted + first comment with link
- [ ] Reddit r/LocalLLaMA (if minor/major)
- [ ] Show HN (if major + compelling hero)
- [ ] HF blog live
- [ ] Bluesky (adapt X content)

### Phase 4: Follow-up (T+1 to T+7)

- [ ] YouTube video published
- [ ] Cross-links updated (README, release notes, docs)
- [ ] Engagement monitored
- [ ] Retrospective notes
```

---

## Step 4: Offer to Execute

After presenting the checklist, offer to start executing unchecked
items. For each, invoke the appropriate skill:

| Item | Skill to invoke |
|------|----------------|
| Blog post | `/release-blog --tag vX.Y.Z` or `/write-technical-blog` |
| Validate snippets | `/validate-snippets blog-file.md` |
| De-LLMify | `/de-llmify blog-file.md` |
| X thread | `/write-tweet blog-file.md` |
| LinkedIn post | `/write-linkedin-post blog-file.md` |
| Link preview | `/link-preview blog-file.md` |
| YouTube script | `/write-youtube-script blog-file.md` |

Do NOT auto-invoke all skills without confirmation. Present the
checklist, let the user pick what to work on next. Execute one skill
at a time so the user can review output before proceeding.

---

## Step 5: Write the Plan File

Write the checklist to a markdown file in the current working directory:

- **Filename:** `release-plan-vX.Y.Z.md`
- Use the Write tool. Do not ask — just write it.
- Tell the user the filename after writing.

If a GitHub issue should be created for tracking (minor or major
release), offer to create one using the template from the release
content playbook.

---

## Step 6: Self-Review

| # | Check |
|---|-------|
| 1 | Is the **hero narrative** confirmed with the user? |
| 2 | Is the **release size** appropriate for the content scope? |
| 3 | Does the checklist include **all relevant channels** for this release size? |
| 4 | Are **existing assets** correctly marked as done? |
| 5 | Are **approval workflows** included for IBM channels if needed? |
| 6 | Is there a **timeline** with dates for each phase? |
| 7 | Is a **content lead** assigned or flagged as TBD? |

---

## Related Skills

- `/release-blog` — draft the blog post from a GitHub release
- `/write-technical-blog` — draft a deep-dive blog post
- `/write-tweet` — draft the X/Twitter promotion
- `/write-linkedin-post` — draft the LinkedIn promotion
- `/write-youtube-script` — draft the video script
- `/validate-snippets` — test code examples in the blog
- `/de-llmify` — remove LLM writing patterns
- `/link-preview` — generate social sharing metadata
- `/get-blog-candidates` — rank PRs if unsure what to highlight
