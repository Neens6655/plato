# Audit Frameworks

Evaluation checklists for each advisor. Assess each item and produce findings
only for items that reveal problems or blind spots.

## Strategist Checklist

### Business Model
- [ ] Revenue model defined and viable?
- [ ] Unit economics make sense? (CAC vs LTV)
- [ ] Pricing strategy justified by value delivered?
- [ ] Multiple revenue streams, or single point of failure?

### Market (USE MCP TOOLS TO VERIFY)
- [ ] Target market identified and sized? Search for market data.
- [ ] Customer pain point validated? Check social sentiment.
- [ ] Competitive landscape mapped? Search for competitors.
- [ ] Differentiation is real and defensible?
- [ ] Market timing — why now? Check Google Trends.

### Moat
- [ ] What prevents copying? (network effects, data, switching costs, brand)
- [ ] Moat grows stronger over time, or erodes?

### Go-to-Market
- [ ] Acquisition channel identified?
- [ ] First 100 users — where do they come from?

### Risks
- [ ] Single customer or platform dependency?
- [ ] Regulatory exposure?
- [ ] Key person risk?

---

## Architect Checklist

### Structure
- [ ] Clear separation of concerns?
- [ ] Module boundaries well-defined?
- [ ] No circular dependencies?
- [ ] Entry points obvious?
- [ ] Configuration externalized?

### Quality
- [ ] Error handling present and consistent?
- [ ] Edge cases addressed in critical paths?
- [ ] No hardcoded secrets or credentials?
- [ ] Logging/observability present?

### Security
- [ ] Auth/authz correct?
- [ ] Input validation on user-facing data?
- [ ] No SQL injection, XSS, CSRF vectors?
- [ ] Dependencies up to date (no known CVEs)?

### Scalability
- [ ] Database queries efficient (no N+1)?
- [ ] Caching strategy where beneficial?
- [ ] Rate limiting on public endpoints?

### Debt
- [ ] Technical debt intentional or accidental?
- [ ] Debt manageable or compounding?
- [ ] Critical path free of workarounds?

---

## Designer Checklist

### UX Flow
- [ ] Primary user flow completable in minimal steps?
- [ ] Error states handled gracefully?
- [ ] Loading states present?
- [ ] Empty states designed for first-time users?
- [ ] Navigation intuitive?

### Accessibility — Automated (USE PLAYWRIGHT MCP)
- [ ] Run axe-core via browser_evaluate — zero critical/serious violations?
- [ ] Accessibility tree (browser_snapshot) has correct landmarks (main, nav, banner)?
- [ ] Heading hierarchy valid (h1 → h2 → h3, no skips)?
- [ ] All interactive elements have accessible names?
- [ ] Skip-to-content link present?

### Accessibility — Manual Review
- [ ] Semantic HTML used (buttons not divs, nav not div)?
- [ ] Alt text on images?
- [ ] Keyboard navigable (tab through all interactive elements)?
- [ ] Color contrast meets WCAG AA (4.5:1 text, 3:1 UI components)?
- [ ] Focus indicators visible on all interactive elements?
- [ ] aria-labels on icon-only buttons?

### Responsive — Multi-Viewport (USE PLAYWRIGHT MCP)
- [ ] Screenshot at 375px (mobile) — no overflow, no broken layouts?
- [ ] Screenshot at 768px (tablet) — navigation works, content readable?
- [ ] Screenshot at 1440px (desktop) — full layout correct?
- [ ] Touch targets at least 44x44px on mobile?
- [ ] No horizontal scroll at any breakpoint?

### Visual Design
- [ ] Visual hierarchy clear?
- [ ] Consistent spacing and alignment?
- [ ] Typography consistent (no rogue font-sizes or font-families)?
- [ ] Color palette consistent across all pages?

### Design Token Enforcement
- [ ] Read project's token file (tokens.js, CSS variables, tailwind theme)?
- [ ] Grep for hardcoded hex colors that should use tokens?
- [ ] Grep for hardcoded pixel values that should use spacing tokens?
- [ ] No inline styles with values the token system covers?
- [ ] Component patterns consistent (same button style everywhere)?

### User Psychology
- [ ] Trust signals present for transactional flows?
- [ ] Cognitive load manageable?
- [ ] Feedback loops tight (user knows action registered)?
- [ ] Progressive disclosure (not overwhelming)?

### User Feedback (USE MCP TOOLS)
- [ ] Search Reddit/X for complaints about similar products
- [ ] What do users of competitors wish was different?
- [ ] What causes users to abandon similar tools?

---

## Quant Checklist

### Edge (USE MCP TOOLS TO VERIFY)
- [ ] Statistical edge explicitly defined?
- [ ] Edge source identified (information, speed, behavioral)?
- [ ] Edge magnitude estimated? Pull actual Sharpe if available.
- [ ] Edge decay considered? Check recent vs historical performance.

### Backtesting
- [ ] Look-ahead bias eliminated?
- [ ] Survivorship bias addressed?
- [ ] Transaction costs included?
- [ ] Slippage modeled realistically?
- [ ] Out-of-sample period tested (at least 20% of data)?
- [ ] Walk-forward analysis performed? Run backtest tool if available.

### Risk Management
- [ ] Maximum drawdown bounded? Check actual max DD.
- [ ] Position sizing rules defined?
- [ ] Tail risk considered?
- [ ] Risk per trade limited?

### Regime Sensitivity (USE MCP TOOLS)
- [ ] Performance across different market regimes tested?
- [ ] Regime detection or filter built in?
- [ ] Graceful degradation in unfavorable regimes?
- [ ] What is the CURRENT regime? Pull live data.

### Live Performance (IF PORTFOLIO EXISTS)
- [ ] Actual Sharpe ratio above 1.0?
- [ ] Max drawdown within acceptable bounds?
- [ ] Win rate aligned with backtest expectations?
- [ ] Live results tracking backtest projections?

---

## Customer Checklist

### First Impression (10-Second Test)
- [ ] Can I understand what this does in 10 seconds?
- [ ] Is the value proposition clear and compelling?
- [ ] Does this look professional and trustworthy?
- [ ] Is there a clear call to action?

### Value Assessment
- [ ] What problem does this solve for ME specifically?
- [ ] How is this better than what I currently use?
- [ ] What is the switching cost (time, money, learning)?
- [ ] Is the pricing fair relative to the value?

### Getting Started
- [ ] Can I get value in under 5 minutes?
- [ ] Do I need to create an account before seeing anything?
- [ ] Is there a demo, free trial, or sandbox?
- [ ] Is documentation clear enough to self-serve?

### Trust & Longevity
- [ ] Does this feel maintained and active?
- [ ] Is there a community or support channel?
- [ ] Will this still exist in 2 years?
- [ ] Are there testimonials, case studies, or social proof?

### Competitive Context (USE MCP TOOLS)
- [ ] Search for what users say about competing products
- [ ] What do users love about alternatives?
- [ ] What do users hate about alternatives?
- [ ] Where does THIS product fit in the landscape?

---

## Contrarian Checklist

### Core Assumptions
- [ ] Top 3 assumptions identified?
- [ ] Each validated with evidence (not belief)?
- [ ] What happens if assumption #1 is wrong?

### Opportunity Cost
- [ ] Is this the highest-leverage use of resources?
- [ ] Could 20% of the effort get 80% of the value?
- [ ] Could buying/subscribing replace building?

### Logical Fallacies
- [ ] Sunk cost driving continued investment?
- [ ] Confirmation bias in research?
- [ ] Survivorship bias in inspiration?
- [ ] Premature optimization before proving value?

### External Evidence (USE MCP TOOLS)
- [ ] Search for similar projects that FAILED — why did they fail?
- [ ] Check Google Trends — is demand growing or shrinking?
- [ ] How many competitors exist? Is the market saturated?
- [ ] What happened to the last 3 products in this space?

### Second-Order Effects
- [ ] If this succeeds, what new problems emerge?
- [ ] Does this create limiting dependencies?

### The Hard Questions
- [ ] Would you start this today, knowing what you know now?
- [ ] Can you explain why this matters in 30 seconds?
- [ ] What is the kill criteria?
- [ ] Pre-mortem: 1 year from now this failed — what went wrong?

---

## QA Engineer Checklist (Specialist)

### Automated Accessibility (USE PLAYWRIGHT MCP)
- [ ] Inject axe-core on every page — zero critical violations?
- [ ] Inject axe-core on every page — zero serious violations?
- [ ] Accessibility tree has correct landmark structure?
- [ ] All form inputs have associated labels?
- [ ] All images have alt attributes?
- [ ] Heading hierarchy is valid (no skipped levels)?

### Performance Metrics (USE PLAYWRIGHT MCP)
- [ ] First Contentful Paint < 1.5s?
- [ ] DOM Content Loaded < 3s?
- [ ] DOM node count < 1500?
- [ ] Total transfer size < 500KB?
- [ ] No render-blocking resources in critical path?

### Responsive Integrity (USE PLAYWRIGHT MCP)
- [ ] No horizontal overflow at 375px?
- [ ] No horizontal overflow at 768px?
- [ ] No horizontal overflow at 1440px?
- [ ] All text readable at each breakpoint?
- [ ] Touch targets >= 44x44px on mobile?

### Quality Infrastructure
- [ ] ESLint configured with jsx-a11y plugin?
- [ ] Prettier configured with tailwind plugin?
- [ ] Pre-commit hooks (Husky + lint-staged) configured?
- [ ] Commit message linting (commitlint) configured?
- [ ] Playwright tests exist for a11y and visual regression?
- [ ] CI/CD pipeline exists (.github/workflows/)?
- [ ] Bundle size monitoring configured (size-limit)?
- [ ] Design tokens defined and enforced?

### Production Readiness
- [ ] Build succeeds without warnings?
- [ ] No console errors in production build?
- [ ] Source maps configured correctly?
- [ ] Environment variables not hardcoded?
- [ ] .gitignore covers all generated files?
- [ ] No dev dependencies in production bundle?
