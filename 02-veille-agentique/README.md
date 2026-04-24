# Autonomous Intelligence Pipeline

> A fully autonomous daily scraping + classification + synthesis pipeline covering YouTube, Instagram, Facebook, and web sources. Uses tiered LLM dispatch and vision fallback to deliver a curated morning briefing — no human in the loop.

## The Problem

Staying on top of a fast-moving technical field (AI research, trading signals, industry announcements) used to mean either:

- **Manual reading** — 1–2 hours/day across 50+ sources, inconsistent coverage.
- **Generic news aggregators** — broad but shallow, no classification against *your* areas of interest.
- **Naive scraping + summarization** — brittle when platforms change DOM, expensive when you route everything through a frontier LLM.

The real-world challenges I hit:

- **DOM-fragile platforms** (Instagram, Facebook) constantly break selectors — a scrape pipeline that worked yesterday returns empty HTML today.
- **Cost explosion** if every scraped item is scored by GPT-4-class models.
- **Relevance drift** — "is this interesting?" depends on a personal profile, not a generic keyword list.
- **Silent failures** — the worst outcome is the pipeline running "successfully" with empty results and you not noticing.

## The Solution

A **daily scheduled agentic pipeline** that:

1. **Scrapes** a curated list of sources via a **real Chrome instance** (not headless-detectable) driven by Chrome DevTools Protocol.
2. **Falls back to vision** when DOM scraping fails — a VLM describes the rendered page, we extract items from the description. This makes the pipeline resilient to DOM changes without us noticing for weeks.
3. **Classifies** each item using a **small/fast LLM** (Haiku-tier) scoring relevance against a personal-interest profile. Cheap per-item, runs at scale.
4. **Synthesizes** the top-N items via a **mid-tier LLM** (Sonnet-tier) into a structured digest.
5. **Delivers** the briefing via push notification + written artifact.
6. **Surfaces failures explicitly** — if a source returns zero items after both DOM + vision fallback, an error propagates to the user, not a silent green checkmark.

## Architecture

```
┌────────────────────────────────────────────────────┐
│  Scheduler (systemd timer, 08:00 daily)            │
└───────────────────┬────────────────────────────────┘
                    ↓
┌────────────────────────────────────────────────────┐
│  Source queue (YouTube, IG, FB, tech blogs, …)     │
└───────────────────┬────────────────────────────────┘
                    ↓ sequential (rate-limit friendly)
┌────────────────────────────────────────────────────┐
│  Scraper                                           │
│   ├─ CDP via real Chrome on headful desktop        │
│   ├─ DOM extractor (primary)                       │
│   └─ Vision fallback on failure → VLM describes    │
└───────────────────┬────────────────────────────────┘
                    ↓ raw items
┌────────────────────────────────────────────────────┐
│  Relevance classifier (Haiku-tier)                 │
│   - Personal interest profile + examples           │
│   - 0–10 score + rationale                         │
└───────────────────┬────────────────────────────────┘
                    ↓ top-N
┌────────────────────────────────────────────────────┐
│  Synthesizer (Sonnet-tier)                         │
│   - Structured digest: takeaway + source + quote   │
└───────────────────┬────────────────────────────────┘
                    ↓
┌────────────────────────────────────────────────────┐
│  Delivery (push notification + markdown artifact)  │
└────────────────────────────────────────────────────┘
```

Key design decisions:

- **Real Chrome, not headless.** Headless browsers are trivially detected by Instagram/FB and return logged-out HTML. A headful Chrome on a dedicated desktop VM with proper session cookies scrapes reliably.
- **Sequential queue.** Parallel scraping triggers rate limits on every platform. Sequential is slower but reliable.
- **Explicit failure propagation.** Empty results raise a distinguishable error vs. "nothing relevant found." The single most important anti-silent-failure pattern.

## Measurable Impact

- **~50 sources** covered daily, across 4 platform types + open web.
- **Tiered dispatch** cuts per-run LLM cost by **~8x** vs. routing everything through a frontier model.
- **Vision fallback** auto-recovered from 3 distinct DOM changes over a 6-month period without code changes.
- **Run time:** ~15 minutes end-to-end for a daily briefing covering ~200 raw items filtered to ~15 for synthesis.

## Stack

- **Browser automation:** Chrome DevTools Protocol (CDP), real Chrome on a headful desktop (x11vnc + Xtigervnc)
- **LLM dispatch:** multi-tier — Haiku for classification, Sonnet for synthesis (via LiteLLM cascade)
- **Vision fallback:** VLM over rendered screenshots
- **Orchestration:** Python, systemd timer (not cron — better observability via journalctl)
- **Delivery:** push notifications (ntfy), markdown artifacts written to inbox folder

## What I'd Do Differently Next Time

- **Start with vision-first, DOM-fallback** instead of the reverse. After 2 iterations of DOM-first I realized vision extraction is now cheap enough that the DOM is the optimization, not the primary path.
- **Build a source-health scorecard** — per-source success rate over 30 days. Currently I inspect failures reactively; a rolling scorecard would surface slowly-decaying sources before they fully break.
- **Semantic dedup across days** — right now an article covered by 3 sources produces 3 entries. A lightweight embedding-based dedup would tighten the briefing.

## Key Technical Lessons

- **Silent success is the enemy.** The hardest bug in a scraping pipeline is "it ran, it produced output, the output is empty." Every layer now raises distinguishable errors.
- **The "small model for the bulk + big model for the hard part" pattern is the single biggest cost/quality lever in agentic pipelines.** Generalizes far beyond this use case.
- **Headless browser automation is a dead end for modern consumer platforms.** Invest in real-browser automation early.
