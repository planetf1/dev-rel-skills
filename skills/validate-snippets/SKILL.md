---
name: validate-snippets
description: >-
  Extract fenced code blocks from a piece of writing (blog post, README, docs),
  execute each snippet, and report which ones pass, fail, or were skipped.
  Supports Python, Go, JavaScript, TypeScript, and shell.
---

# Validate Code Snippets in Writing

Use this skill to catch broken code examples before publishing. It extracts
every fenced code block from a markdown file, runs each one, and tells you
what failed.

## Usage

```
/validate-snippets <path/to/file.md>
```

The input must be a file path to a markdown document (or any file containing
fenced code blocks).

---

## Step 1: Read and Inventory

Read the input file. Scan for all fenced code blocks (lines between
` ```lang ` and ` ``` `). For each block, record:

- **Snippet number** (1-indexed, in document order)
- **Language tag** (the annotation after the opening triple backticks)
- **Source line** (the line number in the file where the opening fence appears)
- **Content** (the code inside the fences)

Report the inventory to the user before running anything:

```
Found N code snippets: X python, Y shell, Z go, ...
```

---

## Step 2: Classify Each Snippet

Classify every snippet into one of three categories:

### Runnable

The language tag maps to a supported runner:

| Tag(s) | Runner command | Temp file extension |
|---------|---------------|---------------------|
| `python`, `py` | `python3 <tmp>.py` | `.py` |
| `go`, `golang` | `go run <tmp>.go` | `.go` |
| `javascript`, `js` | `node <tmp>.js` | `.js` |
| `typescript`, `ts` | `npx tsx <tmp>.ts` | `.ts` |
| `bash`, `shell`, `sh`, `zsh` | `bash <tmp>.sh` | `.sh` |

### Skip — non-runnable language

These tags represent output, data, or markup, not executable code. Skip them
silently:

`console`, `output`, `text`, `txt`, `json`, `yaml`, `yml`, `toml`, `xml`,
`html`, `css`, `sql`, `diff`, `log`, `csv`, `ini`, `conf`, `proto`,
`graphql`, `gql`, `markdown`, `md`

### Skip — no tag or unsupported

If a block has no language tag or a tag not listed in either table above,
skip it with a note: "no language tag" or "unsupported language: <tag>".

### Skip — partial snippet

If the block content contains placeholder markers that indicate it's an
incomplete fragment, skip it with reason "partial snippet". Markers to detect:

- Lines consisting of only `...` or `# ...` or `// ...`
- The literal string `<your-` or `YOUR_` (template placeholders)
- The literal string `[...]`

---

## Step 3: Check Runner Availability

Before executing anything, check which runner binaries are available on PATH:

```bash
which python3 && which go && which node && which npx && which bash
```

If a runner is missing, mark all snippets of that language as SKIP with
reason "runner not found: <binary>". Do not fail the whole skill.

---

## Step 4: Execute Each Runnable Snippet

For each runnable snippet:

1. Write the snippet content to a temporary file with the correct extension.
   For Go snippets, if the snippet does not contain `package main`, wrap it:
   ```go
   package main

   import "fmt"

   func main() {
       // snippet content here
   }
   ```
   Use your judgment about what imports to add based on the snippet content.

2. Run the appropriate command. Use a **30-second timeout** per snippet.
   Capture:
   - Exit code
   - stdout (first 20 lines)
   - stderr (first 20 lines)

3. Record the result:
   - Exit code 0 → **PASS**
   - Non-zero exit code → **FAIL**
   - Timeout → **FAIL** (reason: "timed out after 30s")

4. Clean up the temporary file.

**Important:** Run snippets one at a time, sequentially. Do not run them in
parallel — a snippet may depend on side effects or assume exclusive access to
a port or file.

---

## Step 5: Compile the Report

Build a markdown report with two sections:

### Summary

```
## Snippet Validation Report: <filename>

**Date:** YYYY-MM-DD
**File:** <path>

| Total | Pass | Fail | Skip |
|-------|------|------|------|
| N     | N    | N    | N    |
```

### Detail Table

```
| # | Lang | Line | Status | Detail |
|---|------|------|--------|--------|
| 1 | python | 24 | PASS | — |
| 2 | shell | 41 | FAIL | `ModuleNotFoundError: No module named 'foo'` |
| 3 | json | 55 | SKIP | non-runnable language |
| 4 | python | 72 | SKIP | partial snippet |
```

For FAIL entries, include the first line of stderr (or stdout if stderr is
empty) as the detail. Truncate to 80 characters.

### Failed Snippet Details

For each failing snippet, include a collapsed section with the full snippet
and the full error output:

```markdown
<details>
<summary>Snippet 2 (shell, line 41): FAIL</summary>

**Code:**
\```shell
pip install foo
foo --version
\```

**Error:**
\```
ModuleNotFoundError: No module named 'foo'
\```

</details>
```

---

## Step 6: Write Report and Offer Fixes

1. Write the report to `snippet-report-<input-slug>.md` where `<input-slug>`
   is the input filename without extension (e.g., `blog-foo.md` →
   `snippet-report-blog-foo.md`).

2. Print the summary table to the user.

3. If there are any FAIL results, ask:

   > **N snippet(s) failed.** Would you like me to attempt to fix them in the
   > original file?

4. If the user accepts, iterate over each failing snippet:
   - Analyze the error message
   - Propose a corrected version of the snippet
   - Show the diff to the user and apply only with approval
   - Re-run the corrected snippet to verify the fix works

If the user declines, stop after the report.

---

## Step 7: Self-Review Checklist

Before finalizing the report, verify each item:

| # | Check |
|---|-------|
| 1 | Were all fenced code blocks in the file detected? |
| 2 | Were language tags correctly parsed (including common aliases)? |
| 3 | Were non-runnable blocks (json, yaml, output, etc.) correctly skipped? |
| 4 | Were partial/placeholder snippets detected and skipped? |
| 5 | Was runner availability checked before execution? |
| 6 | Was each snippet executed with the correct runner and file extension? |
| 7 | Were Go snippets without `package main` wrapped correctly? |
| 8 | Did all executions respect the 30-second timeout? |
| 9 | Were all temporary files cleaned up? |
| 10 | Does the report accurately reflect what happened for every snippet? |

---

## Common Mistakes to Avoid

- **Running non-code blocks**: JSON, YAML, HTML, and shell output are not
  programs. Always check the language tag before executing.
- **Missing imports in Go**: Go snippets in blog posts usually omit the
  `package main` and `import` boilerplate. You need to wrap them.
- **Treating SKIP as FAIL**: Skipped snippets are expected and fine. Only
  highlight failures.
- **Running snippets that modify the filesystem**: Be cautious with shell
  snippets that write files or install packages. Run them but note in the
  report if a snippet has side effects (writes files, installs packages,
  modifies system state).
- **Ignoring snippet dependencies**: Some blog posts have snippets that build
  on each other (e.g., snippet 2 imports a file created by snippet 1). If a
  snippet fails and references something from a prior snippet, note this in
  the report as a possible cause.
- **Exit-0 is not output-correct**: A PASS only means the snippet ran without
  error. It does not verify that `# Example output:` comment values (scores,
  labels, model responses) match what the code actually produces. For docs
  containing concrete output values, those must be checked by running the
  example against the real target model and comparing. Flag any snippet that
  contains `# Example output:` or similar inline expected-output comments so
  the author knows to verify them manually.
- **Heavy inference examples**: Snippets that load large models or adapters
  (HuggingFace, LoRA, etc.) will time out or fail without the required
  hardware and dependencies. Skip-classify these rather than failing the whole
  run — look for imports of `transformers`, `peft`, or adapter-loading calls
  as signals.
- **Over-wrapping Go snippets**: If a snippet already has `package main`,
  don't double-wrap it.

---

## Related Skills

- `/write-technical-blog` — run `/validate-snippets` on the output to verify
  all code examples work before publishing
- `/release-blog` — release posts often include before/after code snippets
  that should be validated
- `/de-llmify` — run after validating snippets, since fixing code may
  introduce new LLM-ish phrasing around the corrected examples
