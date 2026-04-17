---
name: hn-scout
description: >-
  Scan Hacker News front page for AI-related posts, then evaluate each for
  mellea integration or demo potential. Outputs a ranked list of bandwagon
  opportunities with concrete demo ideas.
---

# HN Scout: Find Mellea Bandwagon Opportunities

Scan the Hacker News front page for AI-related posts, then evaluate whether
mellea could be used with or alongside whatever is being discussed. The goal is
to find trending topics where we can credibly insert mellea into the
conversation — via a demo, blog post, integration, or Show HN.

## Usage

```
/hn-scout [--top N]
```

Defaults: `--top 30` (number of HN front page items to scan).

---

## Steps

### 1. Fetch the Hacker News front page

Use the Hacker News API to get the current top story IDs, then fetch details
for each:

```bash
# Get top story IDs (returns up to 500, we take the first N)
curl -s "https://hacker-news.firebaseio.com/v0/topstories.json" | python3 -c "
import json, sys
ids = json.load(sys.stdin)[:TOP_N]
print(json.dumps(ids))
"
```

Then for each story ID, fetch the item:

```bash
curl -s "https://hacker-news.firebaseio.com/v0/item/{id}.json"
```

Collect: `id`, `title`, `url`, `score`, `descendants` (comment count), `by`, `time`.

To avoid excessive API calls, batch the fetches and run them in parallel where
possible.

### 2. Filter for AI relevance

Score each post for AI relevance using the title, URL domain, and (when
available) a quick fetch of the linked content.

**AI-relevance signals** (any match = relevant):

| Signal | Weight |
|--------|--------|
| Title contains: LLM, GPT, Claude, AI, ML, model, inference, transformer, embedding, RAG, agent, fine-tun, prompt, diffusion, neural, deep learning, GenAI, generative, multimodal, vision model, language model, copilot, assistant, chatbot, reasoning, chain-of-thought, MCP, tool use, function calling | High |
| Title contains: Python, API, structured output, schema, validation, type-safe, Pydantic, OpenTelemetry, observability, guardrails, safety, constrained decoding, grammar | Medium (mellea-adjacent tech) |
| URL domain is: openai.com, anthropic.com, huggingface.co, arxiv.org (cs.AI/cs.CL/cs.LG), ollama.com, together.ai, replicate.com, deepmind.google, ai.meta.com | High |
| HN score >= 100 AND title mentions any programming/tech concept alongside AI | Medium |

Discard posts with no AI relevance signals. Keep the rest for scoring.

### 3. Read linked content for relevant posts

For each AI-relevant post, use WebFetch to read the linked URL. Extract:

- **What it is**: the project, paper, tool, or announcement in one sentence
- **Core technology**: what stack/language/framework it uses
- **Key capability**: what it does that people care about
- **Why it's trending**: what makes this interesting right now (new release, benchmark result, controversy, etc.)

If the URL is inaccessible (paywall, PDF-only, etc.), work from the title and
HN comments thread at `https://news.ycombinator.com/item?id={id}` instead.

### 4. Score each post for mellea opportunity

For each AI-relevant post, evaluate the mellea integration potential. Mellea is
a Python library for structured generative programs — its strengths are:

**Core capabilities:**
- **Structured output via Pydantic**: any scenario needing typed, validated LLM output
- **Requirements + repair**: attach natural-language constraints and auto-retry on failure
- **Sampling strategies**: rejection sampling, majority voting, best-of-n, SOFAI for higher-quality results
- **@generative decorator**: turns typed Python functions into LLM calls — no prompt templates
- **MCP tool exposure**: any generative program can be served as an MCP tool
- **mify utility**: drop mellea into existing codebases incrementally
- **ReACT agents**: built-in agent loop with structured output and tool calling
- **`@tool` decorator**: define tools from typed functions with automatic schema generation; import tools from LangChain and smolagents
- **Built-in code interpreter**: sandboxed code execution as a tool
- **Images and vision**: pass images to any vision-capable backend via ImageBlock

**Inference-time scaling:**
- **Process Reward Models (PRMs)**: reward-model-guided scoring for inference-time compute scaling
- **Uncertainty Quantification (UQ)**: intrinsic for calibrating confidence in LLM outputs

**Document processing:**
- **RichDocument / Docling integration**: load PDFs, extract tables as MObjects, query and transform documents with LLM assistance
- **Query clarification for RAG**: built-in intrinsic that refines ambiguous queries before retrieval

**Backend support:**
- **Multi-backend**: Ollama (default), OpenAI, HuggingFace, LiteLLM (100+ providers incl. Anthropic, AWS Bedrock, Azure, Vertex AI), WatsonX, LangChain/smolagents tool interop
- **Constrained decoding**: grammar-enforced generation for Ollama and HuggingFace — valid output at the token level, not retried into existence
- **Cache backend**: cache inference results for cost optimization and latency reduction
- **Granite 4 hybrid models**: optimized support for IBM's current model family

**Observability & safety (v0.4.x):**
- **Hooks / plugin system**: callback functions at LLM generation, tool invocation, sampling loop, and session lifecycle points — for logging, caching, PII redaction, retries, circuit breakers
- **OpenTelemetry integration**: OTLP + Prometheus exporters for metrics and tracing
- **GuardianLib intrinsics**: safety checks for harmful, off-topic, or hallucinated outputs
- **Context attribution**: trace which context sources contributed to a generation

**CLI & serving:**
- **`m serve`**: OpenAI API-compatible HTTP endpoint (structured output + tool calling)
- **`m decompose`**: automatic task decomposition pipeline
- **`m alora`**: fine-tune and upload LoRA adapters

Score each post on two axes:

#### Fit score (0-50): How naturally does mellea apply?

| Signal | Points |
|--------|--------|
| Post is about structured/typed LLM output, schema enforcement, or output parsing | **+50** |
| Post involves Python + LLM integration patterns | **+40** |
| Post discusses prompt engineering pain points mellea solves (retries, validation, structured responses) | **+40** |
| Post is about LLM observability, tracing, or metrics (mellea has OTel integration + hooks) | **+40** |
| Post is about multi-model/multi-provider patterns (mellea supports many backends via LiteLLM) | **+35** |
| Post is about MCP, tool use, or function calling (mellea has MCP support + m serve) | **+35** |
| Post is about LLM safety, guardrails, or content filtering (mellea has GuardianLib) | **+35** |
| Post is about OpenAI API compatibility or serving LLMs behind an API (mellea has m serve) | **+35** |
| Post is about RAG, agents, or LLM pipelines that need structured intermediate steps | **+30** |
| Post is about a new model or provider mellea already supports | **+25** |
| Post is about LLM reliability, testing, or output quality (mellea's requirements/repair pattern) | **+25** |
| Post is about middleware, plugins, or cross-cutting concerns for LLM apps (mellea has hooks) | **+25** |
| Post is about document processing, PDF extraction, or table manipulation (mellea has RichDocument/Docling) | **+30** |
| Post is about inference-time compute scaling, test-time compute, or reward models (mellea has PRMs + sampling strategies) | **+35** |
| Post is about multimodal/vision LLMs (mellea supports images via ImageBlock on vision-capable backends) | **+25** |
| Post is about LLM cost optimization or caching (mellea has cache backend) | **+25** |
| Post is about code execution sandboxing or code interpreters (mellea has built-in code interpreter tool) | **+25** |
| Post is about a new model or provider mellea could add support for | **+15** |
| Post is about AI but in a domain far from mellea's sweet spot (robotics, hardware, policy) | **+5** |

Take the highest applicable signal (don't stack).

#### Buzz score (0-50): How much visibility could we get?

| Signal | Points |
|--------|--------|
| HN score >= 300 | **+30** |
| HN score >= 150 | **+20** |
| HN score >= 50 | **+10** |
| Comment count >= 200 | **+20** |
| Comment count >= 100 | **+15** |
| Comment count >= 50 | **+10** |

**Total opportunity score** = Fit score + Buzz score (max 100).

### 5. Generate demo ideas

For each post scoring >= 40 total, generate a concrete demo concept:

- **Demo title**: a short, punchy name for the demo (e.g., "mellea x Qwen2.5: Structured extraction in 10 lines")
- **What to build**: 2-3 sentences describing a small, buildable demo
- **mellea features used**: which specific mellea capabilities this showcases
- **Effort estimate**: S (afternoon hack), M (1-2 days), L (3-5 days)
- **Content angle**: how to frame this for maximum developer interest (blog post title, tweet hook, Show HN title)
- **Timeliness**: how quickly we need to ship to ride the wave (hours, days, this week)

### 6. Output ranked results

Write a markdown file `hn-scout-YYYY-MM-DD.md` in the current working directory.

#### Output format

```markdown
# HN Scout Report — YYYY-MM-DD

> Scanned top {N} Hacker News stories. Found {X} AI-relevant posts.
> Generated {Y} demo opportunities.

---

## Top Opportunities

### 1. [Post Title](HN link) — Score: XX/100

> {one-line summary of the post}

| | |
|---|---|
| **HN Score** | {score} ({comments} comments) |
| **Fit** | {fit_score}/50 — {reason} |
| **Buzz** | {buzz_score}/50 |
| **Source** | [{domain}]({url}) |

**Demo idea: {demo title}**

{What to build — 2-3 sentences}

- **mellea features**: {list}
- **Effort**: {S/M/L}
- **Content angle**: "{blog/tweet/ShowHN title}"
- **Ship by**: {timeliness}

---

### 2. ...

(repeat for all posts scoring >= 40)

---

## Also Relevant (score 20-39)

| # | Title | Score | Fit | Buzz | Quick take |
|---|-------|-------|-----|------|------------|
| ... | ... | ... | ... | ... | one-line note |

---

## Skipped (AI-relevant but low opportunity)

{Bulleted list of titles that were AI-relevant but scored < 20, with a
one-phrase reason for skipping each.}
```

---

## Guidance

- **Credibility is paramount.** Only suggest demos where mellea genuinely adds
  value. A forced integration will backfire — developers smell marketing from a
  mile away. If a post is trending but mellea has no natural angle, say so and
  move on.
- **Speed matters.** HN cycles are 24-48 hours. Flag anything where the window
  is closing. A mediocre demo shipped today beats a polished one shipped next
  week.
- **Concrete beats vague.** "Build a demo" is not an action item. "Use
  `@generative` to extract structured paper metadata from the arXiv API and
  compare output quality across Ollama and OpenAI" is.
- **Think like a developer, not a marketer.** The content angle should be
  genuinely useful or interesting, not "look at our product." The best
  bandwagon content teaches something while naturally showcasing the tool.
