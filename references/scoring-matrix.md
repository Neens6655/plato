# Scoring Matrix

Rules for scoring, weighting, deduplicating, and prioritizing findings.

## Severity Definitions

**RED — Blocks Success**
Project will likely fail, lose money, or be insecure if ignored.

**YELLOW — Degrades Value**
Project works but significant value left on the table.

**GREEN — Sharpens Edge**
Functional and viable — this makes it notably better.

## Effort Definitions

**QUICK_WIN** — Under 1 hour. Single file, obvious fix. Examples: add input validation, fix a typo in pricing, add alt text to images.

**HALF_DAY** — 2-4 hours. Few files, straightforward. Examples: add loading states, implement error handling, set up rate limiting.

**DEEP_WORK** — 1+ days. Multiple files, needs planning. Examples: redesign auth system, define revenue model, add regime detection.

## Priority Formula

```
Priority = Severity Weight x Effort Multiplier

Severity: RED = 3, YELLOW = 2, GREEN = 1
Effort:   QUICK_WIN = 3x, HALF_DAY = 2x, DEEP_WORK = 1x

Result: RED QUICK_WIN = 9 (do first)
        RED HALF_DAY = 6
        YELLOW QUICK_WIN = 6
        RED DEEP_WORK = 3
        YELLOW HALF_DAY = 4
        GREEN QUICK_WIN = 3
        YELLOW DEEP_WORK = 2
        GREEN HALF_DAY = 2
        GREEN DEEP_WORK = 1 (do last)
```

Sort remediation map by priority score descending.

## Confidence Scoring

| Range | Meaning | Who |
|-------|---------|-----|
| 90-100 | Near certain. Clear evidence. | All advisors |
| 70-89 | High confidence. Strong signals. | All advisors |
| 50-69 | Moderate. Reasonable concern. | Contrarian only |
| Below 50 | Do not include. | — |

**MCP-backed findings get a confidence boost:** If a finding is supported by external data (web search results, live metrics, social sentiment), add +10 to confidence (capped at 100). Cite the source.

## Deduplication

When 2+ advisors flag the same issue:
1. Merge into one finding
2. Use highest severity and confidence
3. Use lowest effort estimate
4. Note all perspectives
5. Combine recommendations

When advisors disagree:
1. Create a Council Debate entry
2. Present both positions fairly
3. Do NOT resolve — user decides

## Priority Within Same Score

**RED:** Security > Data integrity > Business fatal flaw > Core broken
**YELLOW:** Missing PRD requirement > Scalability > UX friction > Compounding debt
**GREEN:** Performance > Design polish > Code quality > Documentation

## Health Score

| Findings | Score |
|----------|-------|
| No RED, no YELLOW | 9-10 |
| No RED, 1-2 YELLOW | 7-8 |
| No RED, 3+ YELLOW | 5-6 |
| 1 RED | 3-4 |
| 2+ RED | 1-2 |

Overall = weighted average of seated advisor scores.
Contrarian weighted at 0.5x (advisory, not concrete).
Customer weighted at 1.0x (represents real user impact).
