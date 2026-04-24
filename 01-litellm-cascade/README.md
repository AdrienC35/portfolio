# LiteLLM Multi-Provider Cascade

> A single OpenAI-compatible endpoint that routes LLM traffic across 5 providers with automatic failover, cost-aware ordering, and real-time degradation alerts.

## The Problem

Running LLM-powered tooling at any real volume creates three simultaneous pressures:

1. **Cost drift** — Anthropic/OpenAI pricing is predictable, but every agentic loop you add multiplies token usage. Without cost-aware routing, monthly spend scales with the number of agents you deploy, not with the value delivered.
2. **Provider incidents** — every major LLM provider has had rate-limit surges, regional outages, and quality regressions in 2025. A single-provider setup means your automation stops when they page.
3. **Capability heterogeneity** — local models are excellent for classification and cheap bulk work. Frontier models are needed for hard reasoning. Picking the right model per task manually doesn't scale across dozens of internal services.

## The Solution

A **single OpenAI-compatible endpoint** (LiteLLM proxy) fronting a cascade of 5 providers, ordered by cost + latency + capability:

```
Local Ollama (free, slow)
    ↓ fallback on unavailable / timeout
Self-hosted vLLM on rented GPU (cheap, fast)
    ↓
NVIDIA NIM (cheap managed)
    ↓
OpenRouter (broad model catalog)
    ↓
Anthropic / OpenAI (final fallback — known-good)
```

Every internal service (agents, coding assistants, RAG, scrapers) points to one URL. The cascade is invisible from the client perspective but the system stays available even if 3 providers are down simultaneously.

## Architecture

- **LiteLLM Proxy** as the routing layer. Model groups defined per use case (coding, classification, synthesis, vision).
- **Ordered fallback lists** per model group. Cheapest provider first; escalate only on 5xx / timeout / rate-limit.
- **Alerting on switch**: every time the cascade falls through to a downstream provider, a structured event goes to a push channel. The team sees degradation in real time, not at billing time.
- **Anthropic-compatible translation layer** so clients using the Anthropic SDK (i.e. native `claude-code` style tooling) transparently use the cascade.
- **Responses API bridge** (FastAPI) translating OpenAI Responses → Chat Completions for providers that don't support `/v1/responses` yet.
- **Containerized**, deployed via Docker Compose on a single VPS, fronted by Cloudflare Tunnel + SSO (no public attack surface).

## Measurable Impact

- **5 providers integrated**, cascade survives any 3-provider simultaneous outage observed in production.
- **~70% of organizational LLM traffic served by non-frontier providers** (local + NIM + OR) at a fraction of the cost of Anthropic/OpenAI equivalents.
- **Zero manual routing decisions** at the application layer — services just ask for `openai/chat-completions` and get the right model.
- **Sub-second switch time** on provider failure (no retry loops visible to the end user).

## Stack

- **LLM infra:** LiteLLM proxy, Ollama, vLLM (tensor_parallel=2 on rented 2x GPU), NVIDIA NIM, OpenRouter
- **Translation:** FastAPI bridge for Responses API → Chat Completions
- **Ops:** Docker Compose, systemd, Cloudflare Tunnel, auth-proxy (OAuth SSO)
- **Observability:** structured JSON logs, ntfy alerts on cascade events, rate_limits tracking pushed to status line

## What I'd Do Differently Next Time

- **Introduce a cost ledger** from day 1. I added provider switch logging before I added per-provider token accounting. Flipping that order would give cleaner cost-attribution dashboards sooner.
- **Embed the cascade config as code with CI validation** — early on, a malformed YAML silently disabled a fallback provider and I only caught it on the next outage.
- **Add semantic caching** at the proxy layer (e.g. GPTCache / Redis-backed). A double-digit % of our traffic is deterministic re-queries from agents that could hit the cache instead of a provider.

## Key Technical Lessons

- **Designing for failure is cheaper than designing for peak performance.** The team notices provider outages less than they notice the faster local model being picked first for small tasks.
- **A single endpoint is a force multiplier.** Internal teams ship AI features faster because the "which provider?" conversation is now the platform's problem, not theirs.
- **Local inference is not "toy" anymore.** Properly-quantized 70B models on 2x consumer-ish GPUs (via Vast.ai) deliver good-enough quality for 60–70% of daily workload. The cost delta vs API is real.
