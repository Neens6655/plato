---
name: plato
description: Spawn a council of advisor agents to critique, audit, and stress-test your project from multiple angles. MCP-powered — advisors use web search, social sentiment, live trading data, and competitor research to ground their critique in facts, not guesses. Produces a prioritized report with actionable findings across business strategy, technical architecture, design quality, quantitative edge, and customer experience. Use when asked to "review this project", "audit this", "critique this", "plato", "give me honest feedback", "what am I missing", "stress test this", "find the blind spots", "play devil's advocate", "council review", "roast this project", or "what would kill this".
---

# /plato — Council of Advisors (V2)

Spawn parallel sub-agents that critique your project from different angles.
Each advisor is an independent voice with access to real data via MCPs.
They disagree with each other. That is the point.

## Usage

```
/plato                              (audit the entire project)
/plato business                     (strategy & business model only)
/plato technical                    (architecture & code quality only)
/plato design                       (UX/UI & design system only)
/plato quant                        (trading/quantitative edge only)
/plato customer                     (target user perspective only)
/plato idea [description]           (stress-test an idea before building — no code needed)
/plato [specific area or file path] (scope to a specific concern)
```

## The Council

| Advisor | Domain | Lens | MCP Tools |
|---------|--------|------|-----------|
| Strategist | Business model, market, moat, revenue | "Would I invest?" | Exa, Social Sentiment, Trends |
| Architect | Code quality, scalability, security, debt | "Where will it break?" | Context7 |
| Designer | UX, accessibility, friction, psychology, tokens | "Would a user succeed?" | Playwright (axe-core, screenshots, a11y tree), Reddit, X |
| Quant | Edge validity, risk mgmt, regimes | "Would I bet real money?" | Trend Engine, Zignal, TradingView |
| Customer | Target user perspective, value prop, switching cost | "Would I actually use this?" | Reddit, X, YouTube |
| Contrarian | Assumptions, opportunity cost, fallacies | "What if you're wrong?" | Exa, Company Research, Trends |
| QA Engineer* | Automated testing, a11y gates, performance, CI/CD | "Does this pass quality gates?" | Playwright (axe-core, perf, responsive) |

*QA Engineer is a specialist — auto-seats when frontend + Playwright MCP detected.

**Contrarian always runs.** Others auto-selected by project type. Customer runs for any user-facing project.

## Workflow

### Phase 0.5: Recall (Audit Memory)

Before reading the project, check for previous audit history.

**Primary: Local audit files**
1. Check if `[skill_dir]/audits/[project-name]/` directory exists
2. If it exists, read the most recent `*.md` file (sorted by filename date)
3. Extract from the previous report:
   - Previous health score
   - Previous RED/YELLOW finding count
   - Unresolved findings (any that weren't marked as fixed)
   - Date of last audit
4. Store as `AUDIT HISTORY` block for later use

**Fallback: Mem0 MCP** (if available and local files not found)
1. Search Mem0 for `"plato audit [project name]"`
2. Extract same fields as above

**No history found:**
- The report will show "First audit (no history)" instead of delta tracking.

### Phase 1: Reconnaissance

Read the project to build shared context for all advisors.

1. Read CLAUDE.md if it exists
2. Read package.json / requirements.txt / go.mod / Cargo.toml
3. Map file structure using Glob
4. Read docs/PRD.md if it exists
5. Read key entry points (index, main, app, layout — 3-5 files max)
6. If scoped by user argument: read only the targeted files/area
7. **Domain doc detection:** Scan for `*.md` files in project root and `docs/` that contain domain-specific rules (trading rules, design system specs, API contracts, etc.)
8. **Knowledge retrieval:** Read [knowledge/INDEX.md](knowledge/INDEX.md) for the advisor-to-knowledge mapping. Based on project type and seated advisors, read the relevant knowledge files from the [knowledge/](knowledge/) directory. For each advisor, select the **3-5 most relevant frameworks** (not all of them) based on:
   - Project type match (e.g., SaaS → pricing frameworks, trading → risk frameworks)
   - Project maturity match (e.g., idea stage → validation frameworks, shipped → scaling frameworks)
   - Current audit scope (e.g., `/plato business` → business-only frameworks)
   Inject selected frameworks into advisor prompts as `KNOWLEDGE CONTEXT`.

Produce an internal context block:

```
PROJECT CONTEXT:
- Name: [project name]
- Type: [web-app | trading-system | api | cli | design-system | mcp-server | saas]
- Stack: [languages, frameworks, dependencies]
- Maturity: [idea | prototype | shipped | production]
- PRD exists: [yes/no, summary if yes]
- Key files: [list of files read]
- Domain docs found: [list of domain-specific docs]
- User's stated goal: [from /plato args or conversation]
```

**Domain doc injection rules:**

| Doc Type | Keywords to Detect | Inject Into |
|----------|-------------------|-------------|
| Trading rules | hurst, backtest, regime, confluence, TSMOM | Quant |
| PRD / requirements | features, success criteria, user stories | Strategist + Customer |
| Architecture notes | CLAUDE.md with stack/pattern info | Architect |
| Design system docs | tokens, palette, typography, components | Designer |
| Roadmap / tasks | TASKS.md, milestones, phases | Strategist + Contrarian |

### Phase 1.5: Research (MCP-Powered)

Before seating the council, gather external data. This runs automatically based on project type. Skip if no relevant MCPs are available.

**For ALL projects (load tools via ToolSearch first):**
- Use Perplexity `search` or `reason` for competitor analysis and market sizing (ToolSearch: "perplexity search reason")
- Use Exa `web_search_exa` for current news and data (ToolSearch: "exa web search")
- Use Exa `company_research_exa` for specific competitor intel (ToolSearch: "exa company research")
- Use `social_search_trends` for Google Trends demand trajectory (ToolSearch: "social sentiment search")
- Use `social_analyze_sentiment` for NLP sentiment on market conversations

**For trading systems:**
- Call trend-engine `analyze_trend` for full 5-module analysis on key symbols (ToolSearch: "trend engine analyze")
- Call trend-engine `get_regime` for each asset class (ToolSearch: "trend engine regime")
- Call trend-engine `backtest` for walk-forward validation (ToolSearch: "trend engine backtest")
- Call zignal `get_performance_metrics` for live Sharpe, drawdown, win rate (ToolSearch: "zignal portfolio performance")
- Call zignal `get_portfolio` for current positions and allocation
- Call zignal `get_distribution_analysis` for return distribution stats

**For web apps / SaaS:**
- Use `social_search_reddit` for user sentiment about similar products
- Use `social_search_x` for mentions and complaints about competitors
- Use `social_search_trends` for demand trajectory
- If deployed URL exists: use Playwright to navigate and screenshot key pages (ToolSearch: "playwright browser screenshot")

**For idea stage (no code):**
- Use Perplexity `reason` for deep competitive landscape analysis
- Use Exa `deep_researcher_start` for comprehensive market research (ToolSearch: "exa deep researcher")
- Use social sentiment tools to validate demand signals
- Check Google Trends for keyword demand

Produce a `RESEARCH CONTEXT` block appended to PROJECT CONTEXT:

```
RESEARCH CONTEXT:
- Competitors found: [list with brief notes]
- Market signals: [demand trajectory, sentiment summary]
- Live data (trading): [regime states, portfolio metrics]
- User sentiment: [what real users say about similar products]
```

### Phase 2: Council Selection

**Step 1:** Auto-detect which core advisors to seat based on project type:

| Project Signal | Advisors Seated |
|----------------|-----------------|
| Trading system (trend-engine, zignal, backtesting, signals) | Strategist, Architect, Quant, Customer, Contrarian |
| Web app with UI (React, Next.js, HTML/CSS, dashboard) | Strategist, Architect, Designer, Customer, Contrarian |
| API / Backend / CLI (no frontend) | Strategist, Architect, Contrarian |
| Design system (Z-Signal, tokens, Bauhaus) | Strategist, Designer, Customer, Contrarian |
| MCP server / AI tool | Strategist, Architect, Customer, Contrarian |
| SaaS product (auth + payments + UI) | Strategist, Architect, Designer, Customer, Contrarian |

**Step 2: Specialist Detection** — Scan for signals no core advisor covers. Check [references/advisor-registry.md](references/advisor-registry.md) for detection rules.

If specialist signals found:
1. Match signals to specialist type from the registry
2. Generate a temporary persona using the Specialist Persona Template
3. Maximum 2 specialists per audit
4. Include promoted specialists from the registry if their signals match

**Step 3:** Present the lineup to the user:

```
COUNCIL ASSEMBLED:

Core advisors seated:
  1. The Strategist — business model, market, revenue [Exa + Trends]
  2. The Architect  — code quality, scalability, security [Context7]
  3. The Customer   — target user perspective [Reddit + X]
  4. The Contrarian — challenges everything [Exa + Research]

Specialists spawned:
  + The Legal/Compliance — auth + payments detected [WebSearch]

Not seated (say "add [advisor]" to override):
  - The Designer   — no frontend detected
  - The Quant      — not a trading project

Research gathered: [summary of Phase 1.5 findings]
Scope: Full project
Ready to convene?
```

**GATE:** Wait for user confirmation.

Focused modes:
- `/plato business` → Strategist + Contrarian only
- `/plato technical` → Architect + Contrarian only
- `/plato design` → Designer + Customer + Contrarian only
- `/plato quant` → Quant + Contrarian only
- `/plato customer` → Customer + Contrarian only

### Phase 3: Convene the Council

Launch each seated advisor as a **parallel Task sub-agent** (subagent_type: "Explore").

Each sub-agent receives:
1. The PROJECT CONTEXT + RESEARCH CONTEXT from Phase 1/1.5
2. Their advisor persona from [references/advisor-personas.md](references/advisor-personas.md)
3. Their evaluation checklist from [references/audit-frameworks.md](references/audit-frameworks.md)
4. The scoring rules from [references/scoring-matrix.md](references/scoring-matrix.md)
5. **Domain docs** relevant to their specialty (if found in Phase 1)
6. **MCP tool list** they can use for live research during audit

**Sub-agent prompt template:**

```
You are The [Advisor Name]. Your job is to audit this project from your perspective.

[Paste full persona from advisor-personas.md]

PROJECT CONTEXT:
[Paste context block from Phase 1]

RESEARCH CONTEXT:
[Paste research data from Phase 1.5]

DOMAIN DOCUMENTS:
[Paste relevant domain docs — e.g., trading rules for Quant, PRD for Customer]

KNOWLEDGE CONTEXT:
[Paste 3-5 most relevant frameworks for this advisor from the knowledge files.
Select based on project type and the advisor's evaluation lens.
These are proven frameworks from leading books and research. Use them to ground
your evaluation in established thinking, not just personal opinion.
Do NOT dump all frameworks — pick only the ones directly applicable to this project.]

EVALUATION CHECKLIST:
[Paste relevant checklist from audit-frameworks.md]

TOOL LOADING (CRITICAL — read this before using any MCP tool):
MCP tools are DEFERRED — they do not exist until you load them.
Before calling any MCP tool, you MUST first call the ToolSearch tool to load it.
Example: to use social_search_reddit, first call ToolSearch with query "social sentiment search".
Once a tool appears in ToolSearch results, it is loaded and you can call it directly.
Do NOT call an MCP tool without loading it first — it will fail silently.

TOOLS AVAILABLE (load via ToolSearch before use):
[List advisor-specific tools from advisor-personas.md — include ToolSearch queries]

INSTRUCTIONS:
1. Read the key project files to understand what this project does
2. Load your MCP tools via ToolSearch, then use them to gather external evidence
3. Evaluate each item on your checklist, applying your KNOWLEDGE CONTEXT frameworks
4. Return your findings in this EXACT format (one per finding):

---
FINDING: [one-line description]
ADVISOR: [your advisor name]
SEVERITY: RED | YELLOW | GREEN
CONFIDENCE: [0-100]
EFFORT: QUICK_WIN | HALF_DAY | DEEP_WORK
EVIDENCE: [what in the project + external data supports this — cite sources]
RECOMMENDATION: [specific, actionable step to take]
FRAMEWORK: [which knowledge framework informed this finding, if any — or "observation"]
---

Rules:
- Only include findings at 70+ confidence (Contrarian: 50+ allowed)
- Be specific — cite file names, function names, line numbers
- Cite external sources when MCP data informed your finding
- Reference knowledge frameworks when they ground your finding
- One finding per issue — don't bundle multiple problems
- Recommend actions, not vague improvements
- Estimate effort: QUICK_WIN (<1hr), HALF_DAY (2-4hr), DEEP_WORK (1+ days)
- If you find nothing significant, say so explicitly
- Maximum 8 findings per advisor (focus on highest-impact)
```

Launch all advisors in parallel using multiple Task tool calls in a single message.

### Phase 4: Consolidate

After all sub-agents return:

1. **Collect** all findings from all advisors
2. **Filter** — remove any findings below confidence threshold (70 general, 50 Contrarian)
3. **Deduplicate** — if two+ advisors flagged the same underlying issue:
   - Merge into one finding
   - Use the HIGHEST severity from any advisor
   - Use the HIGHEST confidence from any advisor
   - Use the LOWEST effort estimate (most optimistic)
   - Note all contributing perspectives
4. **Identify debates** — if advisors disagree on same topic, create "Council Debate"
5. **Sort** — by priority: RED Quick Wins first, then RED Half Days, then RED Deep Work, then YELLOW Quick Wins, etc.
6. **Score** — Calculate per-dimension health scores (X/10) and overall health score
7. **Count** — Total findings per tier for the summary

### Phase 5: Deliver the Council Report

Output using the template from [assets/council-report-template.md](assets/council-report-template.md).

Key sections:
- **Council Verdict** — ONE sentence, the single most important takeaway
- **Health Score** — X/10 overall + per-dimension breakdown
- **Research Summary** — What external data was gathered and key takeaways
- **RED findings** — Each with: what's wrong, evidence (internal + external), effort, recommendation
- **YELLOW findings** — Same structure
- **GREEN findings** — Same structure
- **Council Debates** — Where advisors disagree, both positions presented
- **Audit Coverage** — What was reviewed, what MCPs were used, what was skipped
- **Remediation Map** — Priority-ordered (severity x effort) with skill recommendations

### Phase 5.5: Remember (Audit Memory)

After delivering the report, save results for longitudinal tracking.

**Always: Save locally**
1. Create directory `[skill_dir]/audits/[project-name]/` if it doesn't exist
2. Write the full report to `[skill_dir]/audits/[project-name]/[YYYY-MM-DD].md`
3. Write a summary JSON sidecar to `[skill_dir]/audits/[project-name]/[YYYY-MM-DD].json`:
   ```json
   {
     "date": "YYYY-MM-DD",
     "project": "[name]",
     "health_score": X,
     "red_count": N,
     "yellow_count": N,
     "green_count": N,
     "advisors_seated": ["list"],
     "open_findings": ["one-line descriptions of RED and YELLOW findings"]
   }
   ```
4. If AUDIT HISTORY exists from Phase 0.5: compare scores, identify resolved findings, add delta to report

**Cross-project patterns:**
5. Read all `*.json` sidecar files across `[skill_dir]/audits/*/` directories
6. Count finding types — if same finding type appears 3+ times across projects → surface as **Systemic Pattern**

**Specialist memory:**
7. If a spawned specialist produced 2+ findings at 80+ confidence → add to [references/advisor-registry.md](references/advisor-registry.md) Promoted Specialists section

**Additionally: Mem0 MCP** (if available)
8. Also save summary to Mem0 for semantic retrieval in future audits

### Phase 5.7: Post-Audit Reflection

Council reflects on its own coverage gaps (brief, 3 questions):

1. **Coverage gaps** — signals no advisor was equipped to evaluate? → suggest new specialist
2. **Checklist gaps** — did a checklist miss something this project needed? → note for future
3. **Tool gaps** — questions that needed an MCP tool the council didn't have? → note the gap

Output in report as `COUNCIL REFLECTION` section. Feeds the registry over time.

### Phase 6: Remediation Map

Present actionable next steps sorted by priority (RED Quick Wins first):

| Finding Type | Recommended Skill |
|---|---|
| Bug or security issue | `/fix` |
| Architecture problem | `/architect` |
| Missing feature / business gap | `/prd` then `/kickoff` |
| UX/design issue | `/design` |
| Accessibility violation | `/audit-design` then `/fix` |
| Design token inconsistency | `/audit-design` then `/fix` |
| Missing quality infrastructure | `/lint-and-validate` then set up Tier 1 guardrails |
| Performance issue | `/iterate performance` |
| Strategy pivot needed | Manual decision |
| Edge validation needed | Run `/plato quant` with backtest |
| AI-generated code fragility | `/vibe-code-auditor` |
| Completion claims without proof | `/verification-before-completion` |
| React/Next.js perf anti-patterns | `/react-best-practices` |
| Tailwind v4 architecture debt | `/tailwind-patterns` |
| Backtesting methodology gaps | `/backtesting-frameworks` |
| Quant risk/metrics issues | `/quant-analyst` |
| Multi-agent orchestration needed | `/dispatching-parallel-agents` or `/subagent-driven-development` |
| Full app scaffolding needed | `/app-builder` |
| MVP / indie SaaS launch | `/micro-saas-launcher` |
| Data storytelling / presentation | `/data-storytelling` |
| D3.js / chart visualization | `/claude-d3js-skill` |
| Generative art / creative coding | `/algorithmic-art` or `/canvas-design` |
| Scroll-driven experience needed | `/scroll-experience` |
| GLSL shader / GPU visuals | `/shader-programming-glsl` |
| Programmatic video generation | `/remotion-best-practices` |
| Video pipeline / search / edit | `/videodb-skills` |
| Viral / shareable tool building | `/viral-generator-builder` |
| Marketing psychology gaps | `/marketing-psychology` |
| Writing quality (anti-AI-slop) | `/beautiful-prose` |
| MCP server development | `/mcp-builder` |
| Agent memory / context bloat | `/hierarchical-agent-memory` |
| Durable workflow / background jobs | `/inngest` or `/temporal-python-pro` |
| Voice agent architecture | `/voice-agents` |
| Financial modeling needed | `/startup-financial-modeling` |
| TypeScript advanced patterns | `/typescript-expert` |
| Continuous improvement process | `/kaizen` |
| Full autonomous agent system | `/loki-mode` |
| React artifact bundling | `/web-artifacts-builder` |
| UI/UX design intelligence | `/ui-ux-pro-max` |

Ask: "Pick a finding to act on, or save the report for later?"

## Edge Cases

**No code yet (idea mode):**
- Triggered when: no package.json/requirements.txt found, OR user says `/plato idea [description]`
- Ask user for a 2-3 sentence description of the idea if not already provided
- Phase 1 context built from user's description instead of file reads:
  ```
  PROJECT CONTEXT:
  - Name: [from user]
  - Type: idea
  - Stack: unknown
  - Maturity: idea
  - PRD exists: no
  - Idea description: [user's 2-3 sentences]
  - User's stated goal: [from /plato args or conversation]
  ```
- Phase 1.5 Research runs HEAVIER than normal:
  - Use Perplexity `reason` for deep competitive landscape analysis
  - Use Exa `deep_researcher_start` for comprehensive market research
  - Use `social_search_trends` for demand trajectory on key terms
  - Use `social_search_reddit` for what users say about existing solutions
  - Use `social_search_x` for market chatter and pain point signals
- Council seats: Strategist + Customer + Contrarian (always). Designer only if user mentions UI.
- Architect and Quant skipped (no code to review)
- Remediation map points to `/prd` as the next step for every finding
- Report includes a **VIABILITY VERDICT**: Go / Pivot / Kill (with reasoning)

**MCPs not available:**
- Fall back to code-only analysis (V1 behavior)
- Note in report: "Limited audit — no external research tools available. Findings based on code review only."

**Trading project without live portfolio:**
- Quant reviews rules and code without live metrics
- Note: "No live portfolio data. Findings based on system design review only."

**Sub-agent returns vague findings:**
- Discard findings below confidence threshold
- If an advisor returns zero qualifying findings: "The [Advisor] found no significant issues"

**Advisors agree on everything:**
- Contrarian exists to prevent this
- If even Contrarian finds nothing: "The council found no significant blind spots."

**Project too large:**
- Cap file reads at 20 files per advisor
- Note: "Partial audit — [N] files reviewed out of [M] total."

## Knowledge Ingestion

To add new knowledge to the Plato Brain:

```
"ingest [book/PDF path] into plato"
```

**Workflow:**
1. Read the PDF using PyMuPDF (extract text from first 25 pages)
2. Extract 3-5 key frameworks per source (name, concept, audit application, red flags)
3. Tag each framework with relevant advisors
4. Append to the appropriate knowledge file in [knowledge/](knowledge/) based on domain
5. Update [knowledge/INDEX.md](knowledge/INDEX.md) with new source and framework count

**Knowledge is stored in the local knowledge directory:**
- [knowledge/strategy-business.md](knowledge/strategy-business.md) — ~28 frameworks from business/strategy books
- [knowledge/design-ux.md](knowledge/design-ux.md) — ~25 frameworks from design/UX books
- [knowledge/market-trends.md](knowledge/market-trends.md) — ~22 frameworks from market research reports
- [knowledge/ai-bigpicture.md](knowledge/ai-bigpicture.md) — ~22 frameworks from AI/technology sources
- [knowledge/quant-trading.md](knowledge/quant-trading.md) — ~20 frameworks from quant finance literature (Lopez de Prado, Taleb, AQR, etc.)
- [knowledge/INDEX.md](knowledge/INDEX.md) — advisor-to-knowledge mapping table

**During audits:** Phase 1 step 8 reads the INDEX, identifies which knowledge files are relevant to each seated advisor, and injects matched frameworks into advisor prompts as `KNOWLEDGE CONTEXT`.

## What /plato Does NOT Do

- Does not write code — critiques only
- Does not fix bugs — identifies them, offers `/fix`
- Does not make decisions for you — presents trade-offs
- Does not replace user judgment — challenges and informs
- Does not auto-deploy — critique before those phases
- Does not run all 6 advisors every time — seats relevant ones

## Integration Points

```
/prd  →  /plato  →  /architect  →  /kickoff  →  /ship
(define)  (stress-test)  (plan)     (build)     (deploy)

Post-ship:
/ship  →  /iterate  →  /plato  →  /iterate
(deploy)  (improve)   (re-audit)  (improve more)
```

Best used:
- **Before building** — stress-test the idea with real market data
- **Before shipping** — final audit with user perspective
- **After iterating** — verify improvements with data
- **On competitor projects** — analyze open-source competitors

## Quality Checklist

- [ ] All seated advisors returned findings (or explicitly found nothing)
- [ ] Specialist detection ran; specialists spawned if signals matched
- [ ] MCP research was attempted and results included
- [ ] Domain docs were injected into relevant advisors
- [ ] Findings are deduplicated
- [ ] Council Debates captured where advisors disagree
- [ ] Every RED finding has specific recommendation + effort estimate
- [ ] Remediation map sorted by priority (severity x effort)
- [ ] Cross-project patterns surfaced (if audit history exists)
- [ ] Post-audit reflection completed
- [ ] Report is scannable by a non-coder (plain English)
