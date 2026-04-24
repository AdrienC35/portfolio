# OpenClaw N0-Lab — 100% Local LLM Ops Bot

> A Telegram ops bot running entirely on self-hosted inference (vLLM + GLM-4.7-AWQ on rented GPU), exposing 15 MCP tools for read-only infrastructure queries, email parsing, and autonomous web search. Zero cloud LLM dependency.

## The Problem

Most "AI assistant" deployments that sound useful on paper break on one of three rocks in real conditions:

1. **Cloud LLM vendor lock-in + privacy** — running an ops bot that has access to infrastructure state via OpenAI or Anthropic means every tool call's arguments and results traverse a third-party. Fine for generic coding, unacceptable for infrastructure introspection.
2. **Tool sprawl without permissioning** — giving an LLM "arbitrary shell access" is how you get production incidents. The right shape is a curated tool surface where every tool is read-only by default and write operations require explicit approval.
3. **Protocol mismatch** — popular agentic clients (like OpenClaw) target the OpenAI *Responses* API, while most self-hosted stacks expose *Chat Completions*. Nobody wants to patch the client to make the two talk.

## The Solution

A fully self-hosted stack where the LLM, the orchestration, and the tool surface all live under my control:

- **Local inference** via vLLM running **GLM-4.7-Flash-AWQ** with `tensor_parallel=2` on a rented 2x-GPU Vast.ai instance. Quantized 4-bit AWQ keeps latency low and VRAM usage reasonable.
- **Bridge layer** (FastAPI) translating **OpenAI Responses API → Chat Completions** so the existing client works unmodified against the self-hosted backend.
- **MCP tool surface** with 15 tools exposing *only* read-only operations: `infra-*` (container status, service list, process check), `fs-*` (file tree, read-only), `mail-*` (IMAP search and read, no send), plus DuckDuckGo search for external information.
- **Explicit mutation gate** — any state-changing action is a separate arbitration flow via push notification with CTA (Approve / Reject), never a direct tool call.
- **Delivered over Telegram** via the bot API, making the UX zero-install for the operator.

## Architecture

```
   Telegram client (operator, mobile-first)
            ↓
   Telegram Bot API
            ↓
   OpenClaw orchestrator (local)
            ↓ /v1/responses
   Responses API bridge (FastAPI)  ──── translates ───→  /v1/chat/completions
            ↓
   vLLM server (Vast.ai GPU)
     - GLM-4.7-Flash-AWQ
     - tensor_parallel=2
            ↓ tool calls (MCP JSON-RPC)
   MCP server — 15 read-only tools
     ├─ infra-status, infra-list, infra-fs-read
     ├─ mail-imap-search, mail-imap-read
     ├─ search-duckduckgo
     └─ …
            ↓ (mutation requests)
   Push notification with CTA → human approves/rejects
```

Key design decisions:

- **AWQ 4-bit quantization** over BF16 — quality loss is negligible on agentic tool-use workloads, and the 3–4x VRAM reduction means a 2x consumer-GPU instance is enough.
- **Read-only tool surface by default.** The agent can always answer "what is the state?" but never directly "change the state." This is the single most important safety property for an ops bot.
- **Bridge, don't patch.** Writing the Responses → Chat Completions translation layer once means every OpenAI-Responses-API client works without modification.
- **Telegram as UI** — mobile-first, no dashboard to maintain, push notifications for arbitration are native.

## Measurable Impact

- **Zero cloud LLM spend** for the ops bot workload. Vast.ai GPU rental is a flat hourly rate, predictable and cheaper than API equivalents at sustained usage.
- **Data stays on infrastructure I control.** No tool-call arguments or infrastructure metadata leaked to third-party LLM providers.
- **15 tools, all read-only by design.** Mutation attempts require explicit human approval — zero autonomous state changes possible.
- **Standard OpenAI-compatible API surface** so any client speaking Responses or Chat Completions works without changes.

## Stack

- **LLM serving:** vLLM 0.6+, GLM-4.7-Flash-AWQ, tensor_parallel=2 on rented GPU (Vast.ai)
- **Compatibility layer:** FastAPI bridge (Responses → Chat Completions)
- **Tool protocol:** MCP (Model Context Protocol) — stdio + SSE
- **Interface:** Telegram Bot API
- **Search:** DuckDuckGo (no API key, no rate-limit contract)
- **Networking:** SSH tunnels for GPU connectivity, Cloudflare Tunnel for public-facing services
- **Safety:** push-notification arbitration for mutations (ntfy with CTA actions)

## What I'd Do Differently Next Time

- **Pin the GPU instance config as IaC.** The first deploy was ad-hoc; after one instance recycled and I had to rebuild by memory, I built provisioning scripts. Start there.
- **Structured logging for tool calls from day 1.** It took a while to realize the only way to debug "why did the agent pick this tool?" is to have full tool-call traces with inputs + model reasoning token — add it at the bridge layer early.
- **Per-tool rate limits.** A runaway agent loop can exhaust DuckDuckGo quota in minutes. Budget each tool.

## Key Technical Lessons

- **Self-hosted LLMs are production-ready in 2026 if you pick the right quant + model.** GLM-4.7-Flash-AWQ on a $0.50/hr GPU instance outperforms many managed offerings for tool-use workloads.
- **Read-only by default + explicit write arbitration is the right safety pattern** for any agent with access to stateful systems. Tools that "just execute" create incidents.
- **Protocol bridges are a high-leverage abstraction.** Writing one small adapter unlocks a much larger ecosystem than forking and patching the client.
