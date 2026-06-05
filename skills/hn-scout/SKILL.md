---
name: hn-scout
description: >-
  Scan Hacker News front page for AI-related posts, then evaluate each for
  mellea integration or demo potential. Outputs a ranked list of bandwagon
  opportunities with concrete demo ideas.
---

# HN Scout: Find Mellea Bandwagon Opportunities

Mellea-specific wrapper over `/hn-scout-generic`. Runs that skill targeting
`generative-computing/mellea` with the mellea capability profile and fit
rubric defined below.

## How to run

Execute the full `/hn-scout-generic` skill with the following overrides:

1. **Skip README inference in Step 1.** Use the profile and rubric below directly
   as the capabilities profile and fit rubric. Do not fetch the README.
2. **Set `--repo generative-computing/mellea`** as the target repo.
3. Follow all remaining `/hn-scout-generic` steps unchanged.

---

## Mellea capabilities profile

```
Project: mellea
One-liner: Python library for structured generative programs — typed, validated
  LLM output with batteries included.
Primary language: Python
Key capabilities:
- Structured output via Pydantic: typed, validated LLM responses
- @generative decorator: turns typed Python functions into LLM calls — no prompt templates
- Requirements + repair: attach natural-language constraints and auto-retry on failure
- Sampling strategies: rejection sampling, majority voting, best-of-n, SOFAI
- MCP tool exposure: any generative program can be served as an MCP tool
- mify utility: drop mellea into existing codebases incrementally
- ReACT agents: built-in agent loop with structured output and tool calling
- @tool decorator: typed function tools with automatic schema generation; LangChain/smolagents interop
- Built-in code interpreter: sandboxed execution as a tool
- Vision: pass images to vision-capable backends via ImageBlock
- Process Reward Models (PRMs): reward-model-guided scoring for inference-time scaling
- Uncertainty Quantification (UQ): calibrate confidence in LLM outputs
- RichDocument / Docling: load PDFs, extract tables as MObjects, query with LLM assistance
- Query clarification for RAG: built-in intrinsic to refine ambiguous queries
- Multi-backend: Ollama (default), OpenAI, HuggingFace, LiteLLM (100+ providers), WatsonX
- Constrained decoding: grammar-enforced generation for Ollama and HuggingFace
- Cache backend: cache inference results for cost and latency
- Granite 4 hybrid models: optimized support for IBM's current model family
- Hooks / plugin system: callbacks at generation, tool invocation, sampling loop, session lifecycle
- OpenTelemetry integration: OTLP + Prometheus exporters
- GuardianLib intrinsics: safety checks for harmful/off-topic/hallucinated outputs
- m serve: OpenAI API-compatible HTTP endpoint
- m decompose: automatic task decomposition pipeline
- m alora: fine-tune and upload LoRA adapters
Integrations: Ollama, OpenAI, HuggingFace, LiteLLM, WatsonX, LangChain, smolagents, Docling, Pydantic, OpenTelemetry
Primary use cases:
- Building typed, validated LLM pipelines in Python
- Structured output extraction from documents, APIs, or conversational turns
- LLM observability and safety in production
- Inference-time compute scaling with reward-guided sampling
```

## Mellea fit rubric

Use this rubric verbatim in Step 5 in place of the generic fit table.

| Signal | Points |
|--------|--------|
| Post about structured/typed LLM output, schema enforcement, or output parsing | **+50** |
| Post involves Python + LLM integration patterns | **+40** |
| Post discusses prompt engineering pain points mellea solves (retries, validation, structured responses) | **+40** |
| Post about LLM observability, tracing, or metrics | **+40** |
| Post about inference-time compute scaling, test-time compute, or reward models | **+35** |
| Post about multi-model/multi-provider patterns | **+35** |
| Post about MCP, tool use, or function calling | **+35** |
| Post about LLM safety, guardrails, or content filtering | **+35** |
| Post about OpenAI API compatibility or serving LLMs behind an API | **+35** |
| Post about document processing, PDF extraction, or table manipulation | **+30** |
| Post about RAG, agents, or LLM pipelines needing structured intermediate steps | **+30** |
| Post about a new model or provider mellea already supports | **+25** |
| Post about LLM reliability, testing, or output quality | **+25** |
| Post about middleware, plugins, or cross-cutting concerns for LLM apps | **+25** |
| Post about multimodal/vision LLMs | **+25** |
| Post about LLM cost optimization or caching | **+25** |
| Post about code execution sandboxing or code interpreters | **+25** |
| Post about a new model or provider mellea could add support for | **+15** |
| Post about AI but in a domain far from mellea's sweet spot | **+5** |

Take the highest applicable signal (don't stack).
