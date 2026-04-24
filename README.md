# Adrien Cantin — AI Platform / LLM Systems Engineer

> I design, deploy, and operate production AI systems — multi-provider LLM routing, agentic pipelines, local inference stacks, and MCP-based tooling for autonomous operations.

---

## What I Do

I build the infrastructure layer that makes LLM applications actually work in production:

- **Multi-provider LLM routing** with intelligent failover and cost control
- **Agentic pipelines** that run autonomously (scrape → classify → synthesize → dispatch)
- **Local inference stacks** (vLLM, Ollama) paired with cloud providers for hybrid cost/latency profiles
- **MCP server ecosystems** for tool-calling agents with fine-grained permissioning
- **End-to-end ops**: reverse proxies, SSO, tunnels, monitoring, alerting, systemd — no "it works on my laptop"

I come from a software engineering background and have shipped ~15 integrated AI services on a single production server, all SSO-gated, observable, and resilient to provider outages.

---

## Featured Projects

### 1. [LiteLLM Multi-Provider Cascade](./01-litellm-cascade/)
Single OpenAI-compatible endpoint routing across 5 LLM providers with cost-optimized failover. Handles 100% of organizational LLM traffic with automatic degradation under provider outages.

**Stack:** LiteLLM · Ollama · vLLM · NVIDIA NIM · OpenRouter · Anthropic · Docker · Cloudflare Tunnels

### 2. [Autonomous Intelligence Pipeline](./02-veille-agentique/)
Daily agentic scraping + classification + synthesis pipeline covering YouTube, Instagram, Facebook, and web sources. Uses tiered LLM dispatch (small model for classification, large for synthesis) and vision fallback when DOM scraping fails.

**Stack:** Chrome DevTools Protocol · Python · Multi-tier LLM dispatch · CDP headful browser automation

### 3. [OpenClaw N0-Lab — 100% Local LLM Bot](./03-openclaw-n0lab/)
Telegram ops bot running entirely on local inference (vLLM + GLM-4.7-AWQ on rented GPU) with 15 MCP tools for read-only infrastructure queries, email parsing, and autonomous search. Zero cloud LLM dependency.

**Stack:** vLLM · GLM-4.7-Flash-AWQ · FastAPI · Telegram Bot API · MCP Protocol · Vast.ai

---

## Core Skills

| Category | Stack |
|---|---|
| LLM Infrastructure | LiteLLM, vLLM (tensor_parallel), Ollama, Anthropic/OpenAI/Gemini SDKs, Responses API |
| Agentic Systems | MCP protocol (client + server), LangChain patterns, tool-use, multi-agent dispatch |
| RAG | ChromaDB, SQLite FTS5, embedding pipelines, knowledge graphs |
| MLOps / Deployment | Docker, Docker Compose, systemd, Cloudflare Tunnels, reverse proxies, SSO (OAuth/auth-proxy) |
| Observability | Prometheus-style metrics, structured logging, ntfy/webhook alerting, health checks |
| Data Engineering | Dataiku, PostgreSQL, SQL on AS/400 + Athena, ETL pipelines |
| Languages | Python (primary), Bash, JavaScript/TypeScript |
| Browser automation | Chrome DevTools Protocol, Playwright, CDP session management |

---

## What I'm Looking For

Applied AI / AI Platform / LLM Systems Engineer roles where production reliability matters as much as model choice. Comfortable leading architecture for agentic systems and owning the full deploy-to-monitor lifecycle.

**Contact:** [to add]
