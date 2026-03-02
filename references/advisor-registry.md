# Advisor Registry

Core advisors + specialist spawning rules. The council grows over time.

## Core Council (Always Available)

| ID | Advisor | Domain | Auto-Seat When |
|----|---------|--------|----------------|
| strategist | The Strategist | Business model, market, moat | Always (except focused modes) |
| architect | The Architect | Code, scalability, security | Code exists |
| designer | The Designer | UX, accessibility, friction | Frontend exists |
| quant | The Quant | Edge, risk, regimes | Trading signals detected |
| customer | The Customer | Target user perspective | User-facing project |
| contrarian | The Contrarian | Assumptions, fallacies | Always |

Personas: [advisor-personas.md](advisor-personas.md)

## Specialist Detection

During Phase 2, scan the project for signals no core advisor covers. If detected, spawn a temporary specialist.

| Signal | Specialist | Lens |
|--------|-----------|------|
| Auth, payments, GDPR, HIPAA, terms-of-service, cookie consent | **Legal/Compliance** | "What could get you sued or fined?" |
| Dockerfile, CI/CD, k8s, terraform, nginx, deploy scripts | **DevOps** | "Will this deploy reliably at scale?" |
| User data collection, analytics, PII, encryption-at-rest | **Data Privacy** | "Is user data safe and compliant?" |
| REST endpoints, GraphQL schema, OpenAPI spec, rate limiting | **API Designer** | "Would a developer enjoy integrating this?" |
| Blog, CMS, SEO meta tags, content strategy, marketing copy | **Content Strategist** | "Does the content convert and retain?" |
| ML models, training data, inference pipeline, embeddings | **ML Engineer** | "Is the model reliable in production?" |
| i18n, l10n, multi-currency, timezone handling | **Globalization** | "Does this work outside your country?" |
| Frontend with UI, React/Next.js, Tailwind, deployed URL, Playwright configured | **QA Engineer** | "Does this pass automated quality gates?" |
| p5.js, canvas, WebGL, shaders, generative, procedural, flow field, fractal | **Creative Technologist** | "Is the generative system artistically coherent and performant?" |
| Multi-agent, swarm, orchestration, subagent, parallel agents, crew, LangGraph | **Agent Architect** | "Will this agent system scale, coordinate, and self-correct?" |
| Backtest, walk-forward, Monte Carlo, Sharpe, drawdown, regime, TSMOM, Kelly | **Quant Auditor** | "Does the methodology survive statistical scrutiny?" |
| Video, audio, podcast, TTS, STT, voice agent, media pipeline, Remotion | **Media Engineer** | "Is the media pipeline reliable, latency-budgeted, and production-ready?" |
| SaaS, subscription, pricing, MRR, churn, launch, indie, micro-SaaS | **Growth Strategist** | "Will this acquire, convert, and retain users profitably?" |
| D3, chart, visualization, dashboard, infographic, data story | **Data Storyteller** | "Does the visualization reveal truth or obscure it?" |

## Specialist Persona Template

When spawning a specialist, generate their persona using this template:

```
IDENTITY: [Role title]. [Experience summary — 1 sentence].
You think in terms of [3-4 domain concepts].

EXPERTISE:
- [5-8 domain-specific evaluation areas]

TONE: [2 sentences — how they communicate]

EVALUATION LENS: "[One question that captures their perspective]"

TOOLS AVAILABLE:
- WebSearch — search for [domain-specific research]
- [Any relevant MCP tools from the project]

ANTI-PATTERNS YOU WATCH FOR:
- [4-6 domain-specific red flags]
```

Rules:
- Maximum 2 specialists per audit (focus, not sprawl)
- Specialists get the same finding format as core advisors
- Specialists contribute to health score under a "Specialist" dimension
- Confidence threshold: 70+ (same as core advisors)

## Promoted Specialists

Specialists that proved valuable get saved here for future audits. When the user says "keep this advisor" after an audit, add them below.

_(empty — specialists will be added as they prove value)_

## Promotion Criteria

A specialist should be promoted when:
1. It produced 2+ RED or YELLOW findings with 80+ confidence
2. The user acted on its recommendations
3. It covers a gap no core advisor addresses
4. It was spawned in 2+ separate audits

Promoted specialists auto-seat like core advisors when their detection signals match.
