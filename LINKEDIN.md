# LinkedIn Positioning

## Headline (120 chars max)

Pick one based on target role:

**AI Engineer slant:**
```
AI Platform Engineer · LLM systems, agentic pipelines, self-hosted inference · building production AI infra
```

**MLOps slant:**
```
MLOps / AI Infra Engineer · multi-provider LLM cascades, MCP tooling, vLLM deployment · shipping reliable AI systems
```

**Generic (safest):**
```
Software Engineer → AI Platform · LLM routing, agentic systems, MCP, production inference infra
```

---

## About section

```
I build the infrastructure layer that makes LLM applications work in
production.

Over the last year I've shipped:
• A multi-provider LLM cascade (5 providers, automatic failover, cost-aware
  routing) serving all organizational AI traffic.
• An autonomous agentic intelligence pipeline (scraping + tiered LLM dispatch
  + vision fallback) delivering a daily briefing with no human in the loop.
• A 100% local-inference ops bot on self-hosted vLLM (GLM-4.7-AWQ), with 15
  MCP tools and read-only-by-default safety.

I come from software engineering and care about the parts most ML deploys
skip: SSO-gated access, observable failure modes, cost-aware routing, and
making the system survive provider outages without the application layer
knowing.

Currently exploring roles in AI Platform / LLM Systems / MLOps at companies
building agentic products.

Open to: Applied AI Engineer, AI Platform Engineer, MLOps, LLM Systems roles.
Preferred setup: hybrid or remote, EU time zones.
```

---

## Featured section

Pin in this order:
1. Portfolio README (link to repo or hosted page)
2. LiteLLM Cascade project
3. Agentic Intelligence Pipeline project
4. OpenClaw N0-Lab project

---

## Experience — how to frame current role

Don't just list duties. Frame as **outcomes shipped on production AI infra**:

```
[Title] — [Company]
[Dates]

Owned the AI platform layer for internal teams: multi-provider LLM routing,
agentic pipelines, self-hosted inference, MCP-based agent tooling. Shipped
15+ integrated services on a single VPS with SSO-gated access, Cloudflare
tunneling, systemd-level ops, and push-based alerting.

• Designed and deployed a 5-provider LLM cascade (LiteLLM + Ollama + vLLM +
  NVIDIA NIM + OpenRouter + Anthropic) cutting ~70% of traffic off frontier
  APIs while maintaining sub-second failover.

• Built an autonomous daily intelligence pipeline (CDP browser automation +
  tiered LLM classifier/synthesizer + vision fallback) with explicit
  failure-propagation — cut per-run cost ~8x via small-model dispatch.

• Deployed a 100% local-inference Telegram ops bot on Vast.ai GPU with
  GLM-4.7-Flash-AWQ (tensor_parallel=2) exposing 15 read-only MCP tools for
  infrastructure queries, email parsing, and autonomous search.

• Operated and maintained the full stack: Docker Compose, systemd,
  Cloudflare Tunnels, OAuth SSO proxy, ntfy alerting, health checks.
```

---

## Skills section (add all)

Python · PyTorch · LiteLLM · vLLM · Ollama · LangChain · MCP Protocol ·
Anthropic API · OpenAI API · Docker · Docker Compose · Kubernetes basics ·
FastAPI · PostgreSQL · ChromaDB · RAG · Agentic Systems · Fine-tuning
(LoRA/QLoRA) · Chrome DevTools Protocol · Playwright · Cloudflare Tunnels ·
Linux sysadmin · systemd · Prometheus · Grafana · Dataiku · SQL · AWS
basics · Self-hosted inference · AWQ quantization · Tensor parallelism

---

## Activity strategy (before outreach)

4 weeks of warm-up before sending cold DMs:

1. **Week 1:** Comment substantively on 3 ML engineers' posts per day. No self-promo.
2. **Week 2:** Publish 1 technical post about your LiteLLM cascade (architecture sketch + lessons).
3. **Week 3:** Publish 1 post about the agentic pipeline (vision fallback lesson).
4. **Week 4:** Start reaching out to MLE/AI engineers at target companies.

This builds "seen your name before" familiarity. Cold DMs from unfamiliar profiles hit ~2% response. DMs after 2-3 weeks of visible activity hit 10–15%.
