# Cold Outreach — Templates

> Use referrals when possible (5–10x conversion vs cold). These are for cold fallback.

---

## LinkedIn DM — to an MLE/AI Engineer at target company

> Target: an engineer one level above you at the company. Not a recruiter.

```
Hi [First name],

I'm [name] — I've been building production LLM infrastructure on a single
self-hosted stack: multi-provider routing with automatic failover, agentic
pipelines, and a local-inference ops bot with MCP tool-calling.

I noticed [company] is doing [specific thing from their recent post/blog/JD].
I'd love to learn how you're approaching [specific technical question].

If you have 15 minutes, I'd be glad to hear how the team thinks about it.
Happy to share what I've shipped in return.

— [name]
```

**Why this works:**
- Specific hook (their recent content) → not copy-paste
- Asks for information, not a job → lower-pressure
- Offers value reciprocally (your portfolio)
- Short enough to read on mobile

---

## LinkedIn DM — warm ice-breaker before the ask

> First touch: comment substantively on their post. Wait 48h. Then DM:

```
Hey [First name] — enjoyed your post on [topic]. The angle on [specific point]
matched something I ran into while building [brief parallel from your work].
Would you be open to a quick chat?
```

---

## Email to hiring manager (when you have their address)

Subject: `Production LLM infra engineer — [specific role] at [company]`

```
Hi [First name],

I'm writing about your [role] opening. Quick summary of the fit:

- Built and operate a multi-provider LLM cascade (5 providers, auto-failover)
  serving all AI workload at my current org — similar shape to what
  [company/team] would need at your stage.
- Shipped an agentic scraping + classification pipeline with tiered LLM
  dispatch (small/big models) cutting per-run cost ~8x.
- Deployed a 100% local LLM ops bot (vLLM + AWQ quant, MCP tools) with
  read-only-by-default safety model.

Short portfolio: [link]

I'd appreciate 20 minutes to talk about how you're currently handling
[specific technical challenge visible from JD]. Happy to answer technical
questions directly — I'm more comfortable in a systems conversation than
a generic screen.

Best,
[name]
```

**Variations by company type:**
- **Series B–D startup:** emphasize shipping speed, single-owner ops of 15 services
- **Mid-size enterprise:** emphasize multi-provider governance, cost control, SSO
- **AI-first company:** go deeper technical (quantization, tensor parallelism, MCP)

---

## Referral ask (stronger than cold)

```
Hi [First name] — we worked/crossed paths on [context]. I'm looking into
[company] for an AI platform role. I've been building [1-sentence summary].

Would you be open to making a quick intro to [specific person or team]? I've
got a short portfolio here: [link]

Totally understand if now isn't the right time — no pressure.

Thanks either way.
```

---

## Follow-up (after 7–10 days silence)

```
Hi [First name] — just bumping this up. I know timing is rough; if it's not
the right moment or right fit, a quick "not now" is perfectly fine and I'll
stop bugging you.

If it IS the right moment, happy to share more specifics on [relevant detail].
```

---

## Key rules

- **One follow-up max.** Silence after that is an answer.
- **Lead with specifics.** "I build AI systems" is noise. "I run a 5-provider LLM cascade with sub-second failover" is a signal.
- **Never attach resume upfront.** Link to the portfolio README instead.
- **Don't apologize for cold-reaching.** State the ask directly.
- **Send Tuesday/Wednesday morning** — lowest inbox competition.
