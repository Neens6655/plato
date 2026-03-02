# Plato Brain: Quantitative Trading Frameworks

> Knowledge context for the **Quant** advisor. These frameworks provide the analytical lens through which the Quant evaluates trading systems, backtests, risk management, and quantitative methodology. Each framework maps to a specific failure mode the Quant is trained to detect.

---

## I. Trend Following Methodology

### 1. Hurst Exponent & Long Memory
**Source:** Harold Edwin Hurst (1951); Mandelbrot; refined by Andrew Lo (1991)
**Advisors:** quant, strategist
**One-liner:** The Hurst exponent measures whether a time series trends, mean-reverts, or follows a random walk.
**Core concept:** H > 0.5 indicates persistent (trending) behavior, H < 0.5 indicates anti-persistent (mean-reverting) behavior, and H = 0.5 indicates a random walk. The exponent is estimated via rescaled range (R/S) analysis, detrended fluctuation analysis (DFA), or variance-ratio methods. A system claiming trend-following alpha should operate on instruments and timeframes where H is demonstrably above 0.5, otherwise the strategy is harvesting noise.
**Audit application:** The Quant checks whether the project has measured the Hurst exponent of its target instruments and timeframes before committing to a trend-following approach. A mismatch between strategy type and regime persistence is a foundational design error.
**Red flags it detects:**
- Trend-following system applied to instruments with H near or below 0.5
- No empirical measurement of serial dependence before strategy selection
- Conflating short-term momentum with long-memory trending behavior

---

### 2. Time-Series Momentum (TSMOM)
**Source:** Moskowitz, Ooi, Pedersen (2012) "Time Series Momentum"; AQR Capital Management
**Advisors:** quant, strategist
**One-liner:** TSMOM is the empirical finding that an asset's own past returns predict its future returns across virtually all asset classes and timeframes.
**Core concept:** Unlike cross-sectional momentum (buying winners, selling losers relative to peers), TSMOM compares each asset only to itself. A positive 12-month return predicts continued positive returns. The effect is strongest at 1-12 month horizons, decays beyond 12 months, and is linked to behavioral underreaction and slow information diffusion. Managed futures and CTA strategies are essentially systematic harvesting of TSMOM premia.
**Audit application:** The Quant evaluates whether a trend system's lookback windows, holding periods, and signal construction align with empirically validated TSMOM horizons, or whether the system is using arbitrary parameters that happen to backtest well.
**Red flags it detects:**
- Signal lookback periods with no empirical or theoretical justification
- Ignoring the well-documented TSMOM reversal beyond 12 months
- No decomposition of returns into TSMOM beta vs. genuine alpha

---

### 3. Variance Ratio Test
**Source:** Lo and MacKinlay (1988) "Stock Market Prices Do Not Follow Random Walks"
**Advisors:** quant
**One-liner:** The variance ratio test checks whether a price series follows a random walk by comparing variances at different sampling intervals.
**Core concept:** For a random walk, the variance of k-period returns should be exactly k times the variance of 1-period returns. A variance ratio VR(k) significantly above 1 indicates positive serial correlation (momentum); below 1 indicates mean reversion. The test provides a rigorous statistical foundation for whether trend-following or mean-reversion strategies have a theoretical basis on a given instrument.
**Audit application:** The Quant uses the variance ratio as a first-pass diagnostic: before any strategy is built, does the target data exhibit the statistical properties the strategy assumes? A trend system on a VR(k) < 1 series is structurally flawed.
**Red flags it detects:**
- No statistical pre-screening of instruments for serial correlation structure
- Strategy logic contradicts the variance ratio properties of the underlying data
- Cherry-picking the sampling interval k to manufacture a favorable ratio

---

## II. Risk Management Frameworks

### 4. Kelly Criterion & Fractional Kelly
**Source:** John L. Kelly Jr. (1956); Edward Thorp's practical adaptation
**Advisors:** quant, strategist, architect
**One-liner:** The Kelly criterion calculates the mathematically optimal fraction of capital to risk on each bet to maximize long-run geometric growth.
**Core concept:** Full Kelly sizing is f* = (bp - q) / b, where b is the odds, p is the win probability, and q = 1-p. In practice, parameter estimation error means full Kelly is dangerously aggressive --- it assumes perfect knowledge of edge and variance. Thorp and most practitioners use half-Kelly or quarter-Kelly to trade off growth rate for dramatically reduced drawdown and ruin probability. The gap between full Kelly and fractional Kelly is the "cost of uncertainty."
**Audit application:** The Quant checks whether position sizing has any theoretical basis, whether the project accounts for estimation uncertainty in its edge parameters, and whether sizing is consistent with the strategy's actual win rate and payoff ratio --- not hypothetical ones.
**Red flags it detects:**
- Fixed position sizes with no relationship to edge magnitude or confidence
- Full Kelly sizing without acknowledging parameter estimation error
- Position sizing derived from backtest statistics without out-of-sample validation

---

### 5. Maximum Drawdown & Conditional Drawdown-at-Risk
**Source:** Standard risk management; Chekhlov, Uryasev, Zabarankin (2005)
**Advisors:** quant, contrarian, architect
**One-liner:** Maximum drawdown measures the worst peak-to-trough decline, while CDaR quantifies the expected drawdown in the worst tail scenarios.
**Core concept:** Max drawdown is the single worst historical decline from a peak to a subsequent trough. It is path-dependent and always increases with time --- given enough time, any strategy will experience a drawdown larger than any historical one. CDaR (Conditional Drawdown-at-Risk) extends this by averaging drawdowns beyond a given percentile, analogous to CVaR for losses. A 95% CDaR of 15% means that in the worst 5% of drawdown scenarios, the average drawdown is 15%.
**Audit application:** The Quant evaluates whether the project has realistic drawdown expectations, whether it has stress-tested beyond historical worst cases, and whether its stated risk tolerance is consistent with the strategy's actual drawdown profile.
**Red flags it detects:**
- Quoting only average or median drawdowns while ignoring tail drawdown risk
- Max drawdown calculated from a short or favorable backtest period only
- No plan for drawdowns exceeding the historical maximum (which will happen)

---

### 6. Value-at-Risk and Expected Shortfall (CVaR)
**Source:** J.P. Morgan RiskMetrics (1994); Basel III regulatory framework; Artzner et al. (1999)
**Advisors:** quant, architect
**One-liner:** VaR estimates the maximum loss at a given confidence level, while CVaR (Expected Shortfall) measures the average loss beyond that threshold.
**Core concept:** 95% daily VaR of $10,000 means there is a 5% chance of losing more than $10,000 in a day. VaR's fatal flaw is that it says nothing about how bad the losses are in that 5% tail --- this is where CVaR steps in, averaging the losses beyond the VaR threshold. CVaR is a coherent risk measure (satisfies subadditivity), while VaR is not. Modern risk management requires both: VaR for day-to-day monitoring, CVaR for understanding tail exposure.
**Audit application:** The Quant checks whether the project uses coherent risk measures, whether tail risk is explicitly modeled rather than assumed Gaussian, and whether risk limits are actually enforced in the system architecture.
**Red flags it detects:**
- Using only VaR without CVaR, ignoring tail severity entirely
- Assuming normally distributed returns when the strategy trades fat-tailed instruments
- Risk metrics calculated but not integrated into automated position limits or kill switches

---

### 7. Risk Parity & Inverse Volatility Weighting
**Source:** Bridgewater Associates (Ray Dalio); Qian (2005) "Risk Parity Portfolios"
**Advisors:** quant, strategist
**One-liner:** Risk parity allocates capital so that each position contributes equally to total portfolio risk, rather than equal dollar amounts.
**Core concept:** Equal-dollar allocation creates massively unequal risk allocation: $10K in bonds and $10K in crypto is 90%+ of the risk in crypto. Risk parity sizes positions inversely proportional to their volatility (or more precisely, their marginal contribution to portfolio variance). This ensures no single position dominates the portfolio's risk profile. The simplest implementation uses inverse-volatility weighting; more sophisticated versions account for correlations via risk budgeting.
**Audit application:** The Quant evaluates whether portfolio construction accounts for heterogeneous volatility across instruments, or whether the system naively allocates equal capital to positions with vastly different risk profiles.
**Red flags it detects:**
- Equal dollar allocation across instruments with 10x+ volatility differences
- No dynamic volatility-adjusted sizing as regime conditions change
- Portfolio "diversification" that is actually concentrated risk in one asset class

---

## III. Backtesting Pitfalls & Statistical Rigor

### 8. Multiple Comparisons & Backtest Overfitting
**Source:** Marcos Lopez de Prado, "Advances in Financial Machine Learning" (2018); Harvey, Liu, Zhu (2016) "...and the Cross-Section of Expected Returns"
**Advisors:** quant, contrarian
**One-liner:** When you test many strategy variations, some will appear profitable purely by chance; the probability of false discovery rises exponentially with the number of trials.
**Core concept:** If you test 100 parameter combinations, the best one will look impressive even on random data --- this is the multiple comparisons problem. The Probability of Backtest Overfitting (PBO) framework by Lopez de Prado uses combinatorial symmetric cross-validation (CSCV) to estimate the probability that a backtest-selected strategy is actually overfit. Harvey et al. argue that a t-statistic of 2.0 is no longer sufficient; with the thousands of strategies tested industry-wide, t > 3.0 should be the new minimum.
**Audit application:** The Quant asks: how many variations were tested to arrive at this "best" strategy? What is the ratio of parameters to independent observations? Has the Deflated Sharpe Ratio or PBO been calculated?
**Red flags it detects:**
- Reporting the best of many backtests without adjusting for selection bias
- High parameter count relative to independent data points (overfitting by construction)
- No out-of-sample holdout, walk-forward validation, or CSCV analysis

---

### 9. Look-Ahead Bias & Survivorship Bias
**Source:** Standard quantitative finance methodology; Aronson, "Evidence-Based Technical Analysis" (2006)
**Advisors:** quant, contrarian, architect
**One-liner:** Look-ahead bias uses future information unavailable at the time of the trading decision; survivorship bias tests only on assets that survived to the present.
**Core concept:** Look-ahead bias is insidious: it includes using adjusted close prices that incorporate future splits, calculating indicators on entire datasets before simulating trades, or using point-in-time data that was actually revised later. Survivorship bias tests strategies only on stocks that still exist today, missing the hundreds that were delisted, bankrupted, or merged --- these would have been tradeable at the time and often represent the losing trades the backtest never sees.
**Audit application:** The Quant scrutinizes data pipelines for temporal leakage: are features calculated with only past data? Is the universe of instruments the one available at each historical point, or today's universe projected backward? Are corporate actions handled correctly?
**Red flags it detects:**
- Using point-in-time adjusted data without understanding what adjustments were applied
- Testing on a current index membership rather than historical constituent lists
- Feature engineering that inadvertently incorporates future information (e.g., z-scoring with full-sample statistics)

---

### 10. Deflated Sharpe Ratio
**Source:** Bailey and Lopez de Prado (2014) "The Deflated Sharpe Ratio"
**Advisors:** quant
**One-liner:** The Deflated Sharpe Ratio adjusts a strategy's Sharpe ratio for the number of trials, skewness, kurtosis, and track record length to estimate the probability it is genuinely positive.
**Core concept:** A raw Sharpe ratio of 1.5 is meaningless without context: how many strategies were tested to find it? How long is the track record? Are returns normally distributed? The DSR incorporates: (1) the number of independent trials conducted, (2) the skewness and kurtosis of the return distribution, and (3) the length of the track record. It outputs the probability that the true Sharpe ratio is positive after all adjustments. A DSR below 0.95 suggests the observed Sharpe is likely a statistical artifact.
**Audit application:** The Quant recalculates reported Sharpe ratios using the DSR framework. A project claiming Sharpe 2.0 after testing 500 parameter sets on 2 years of data will see its DSR collapse, revealing the true confidence level.
**Red flags it detects:**
- High Sharpe ratios from short track records with many strategy variations tested
- Ignoring non-normality (negative skew, excess kurtosis) in Sharpe calculation
- No disclosure of the number of trials, configurations, or strategy variants tested

---

## IV. Regime Analysis

### 11. Markov Regime-Switching Models
**Source:** Hamilton (1989) "A New Approach to the Economic Analysis of Nonstationary Time Series"
**Advisors:** quant, strategist
**One-liner:** Markov regime-switching models identify discrete market states (e.g., bull/bear, high/low volatility) with probabilistic transitions between them.
**Core concept:** Financial markets do not operate in a single regime: volatility clusters, correlations shift, and the return-generating process itself changes. Hamilton's model posits that returns are generated by a mixture of distributions, each corresponding to a hidden "regime," with transition probabilities governing switches. A two-state model might distinguish a low-volatility trending regime from a high-volatility mean-reverting regime. The model estimates regime probabilities in real-time, enabling adaptive strategy selection.
**Audit application:** The Quant evaluates whether the project acknowledges that strategy performance is regime-dependent, whether it has any mechanism for detecting regime shifts, and whether its backtest performance is dominated by a single favorable regime that may not persist.
**Red flags it detects:**
- Strategy performance that is entirely concentrated in one market regime
- No mechanism for adapting to or even detecting regime changes
- Assuming stationarity in a fundamentally non-stationary market environment

---

### 12. Volatility Regime Classification
**Source:** Practitioner consensus; CBOE VIX methodology; Mandelbrot's volatility clustering
**Advisors:** quant, strategist, architect
**One-liner:** Volatility regimes classify the market into distinct volatility states that demand fundamentally different trading approaches.
**Core concept:** Volatility is the most persistent and predictable property of financial markets (GARCH effects). A simple framework classifies regimes as: low volatility (compression, breakout setups), normal volatility (trend following works best), high volatility (mean reversion dominates, wider stops required), and crisis volatility (correlations spike to 1, only risk-off works). The realized/implied volatility ratio and the VIX term structure provide real-time regime signals. A strategy optimized for one regime will blow up in another.
**Audit application:** The Quant checks whether the system classifies the current volatility environment, whether strategy parameters adapt to regime (stop widths, position sizes, holding periods), and whether backtest results are broken down by regime.
**Red flags it detects:**
- Fixed stop-loss and take-profit levels regardless of current volatility
- No segmentation of backtest performance by volatility regime
- Crisis-period performance not stress-tested or simply excluded from the backtest

---

## V. Performance Metrics & Evaluation

### 13. Sharpe, Sortino, and Calmar Ratios
**Source:** William Sharpe (1966); Frank Sortino (1994); Terry Young (Calmar, 1991)
**Advisors:** quant, strategist
**One-liner:** These three ratios measure risk-adjusted returns but differ critically in how they define "risk," and using only one gives an incomplete picture.
**Core concept:** Sharpe = (return - risk-free) / total standard deviation --- penalizes upside and downside volatility equally. Sortino = (return - MAR) / downside deviation --- penalizes only negative volatility, appropriate for asymmetric return distributions. Calmar = annualized return / max drawdown --- directly measures the return earned per unit of worst-case pain. A strategy with Sharpe 2.0 but Calmar 0.5 has great daily consistency but terrible drawdown characteristics. Together they reveal the full risk-return picture; individually, each can mislead.
**Audit application:** The Quant requires all three ratios reported together. A project touting only its best metric is likely hiding unflattering risk characteristics in the others.
**Red flags it detects:**
- Reporting only the Sharpe ratio while the Calmar or Sortino tell a different story
- Sharpe calculated on daily returns then annualized (hides autocorrelation and non-stationarity)
- Calmar ratio calculated over a period that does not include a significant drawdown event

---

### 14. Profit Factor & Expectancy
**Source:** Standard trading system evaluation methodology; Van Tharp
**Advisors:** quant, contrarian
**One-liner:** Profit factor is gross profits divided by gross losses; expectancy is the average dollar amount won or lost per trade.
**Core concept:** Profit factor = sum(winning trades) / abs(sum(losing trades)). A PF of 1.0 is breakeven; above 1.5 is solid; above 2.0 is excellent but should trigger suspicion of overfitting. Expectancy = (win rate x average win) - (loss rate x average loss), representing the expected dollar gain per trade. These metrics decompose profitability into its components: you can profit from a high win rate with small average wins (scalping), or a low win rate with large average wins (trend following). The decomposition reveals the strategy's fundamental character.
**Audit application:** The Quant decomposes reported returns into win rate, average win, average loss, and trade frequency. This reveals whether profitability depends on a few outsized winners (fragile) or consistent small edges (robust), and whether the numbers are realistic given the strategy type.
**Red flags it detects:**
- Profit factor above 3.0 in a backtest (almost certainly overfit unless ultra-high-frequency)
- Profitability driven by 2-3 outlier trades that may not repeat
- Win rate and average win/loss inconsistent with the strategy type (e.g., trend system claiming 80% win rate)

---

### 15. Tail Ratio & Return Distribution Shape
**Source:** Nassim Taleb, "Fooled by Randomness" (2001), "Antifragile" (2012); fat-tail literature
**Advisors:** quant, contrarian
**One-liner:** The tail ratio compares the magnitude of positive tail outcomes to negative tail outcomes, revealing whether a strategy is convex (antifragile) or concave (fragile).
**Core concept:** Tail ratio = abs(95th percentile return) / abs(5th percentile return). A ratio above 1.0 means positive tails are larger than negative tails (convex payoff --- you make more in good scenarios than you lose in bad ones). Below 1.0 means the strategy has negative convexity: it earns small consistent returns but is exposed to catastrophic losses (the Taleb "picking up pennies in front of a steamroller" profile). Beyond the ratio, the full return distribution shape --- skewness, kurtosis, and the specific tail behavior --- determines whether a strategy survives Black Swan events.
**Audit application:** The Quant plots the full return distribution histogram and calculates the tail ratio. A strategy that looks great on Sharpe but has a tail ratio of 0.6 is a blowup waiting to happen --- it is short volatility in disguise.
**Red flags it detects:**
- High Sharpe ratio combined with negative skewness and tail ratio below 1.0
- No analysis of return distribution beyond mean and standard deviation
- Strategy that is implicitly or explicitly short gamma / short tail risk without acknowledging it

---

## VI. Market Microstructure & Execution

### 16. Slippage & Transaction Cost Modeling
**Source:** Practitioner consensus; Kissell & Glantz, "Optimal Trading Strategies" (2003)
**Advisors:** quant, architect
**One-liner:** Slippage is the difference between the theoretical execution price and the actual fill, and it can silently destroy a strategy's edge.
**Core concept:** Slippage has three components: the bid-ask spread (guaranteed cost), market impact (your order moving the price against you), and timing delay (price moving between signal and execution). For high-frequency strategies, slippage can consume 50-100% of gross alpha. A realistic slippage model must account for order size relative to average volume, time of day, and volatility regime. The rule of thumb: if your average profit per trade is less than 3x your estimated slippage, the strategy is not viable in live trading.
**Audit application:** The Quant verifies that backtests include realistic transaction costs: not just commissions, but spread, slippage, and market impact proportional to position size and instrument liquidity. Zero-slippage backtests are fantasy.
**Red flags it detects:**
- Backtests assuming zero slippage or using only commission costs
- High-frequency strategies with average profit per trade close to the bid-ask spread
- No sensitivity analysis showing how performance degrades with increasing slippage assumptions

---

### 17. Liquidity Risk & Capacity Constraints
**Source:** Amihud (2002) "Illiquidity and Stock Returns"; Kyle (1985) lambda model
**Advisors:** quant, architect, strategist
**One-liner:** Every strategy has a capacity limit beyond which its own trades destroy its edge through market impact.
**Core concept:** Strategy capacity is the maximum capital that can be deployed before market impact erodes returns to zero. It is determined by the instruments' average daily volume, the strategy's turnover, and the price impact function. Amihud's illiquidity ratio (|return| / dollar volume) provides a cross-sectional measure. Kyle's lambda estimates the permanent price impact per unit of order flow. A strategy backtested on $100K that "scales" to $10M without adjusting for impact is a fiction. Capacity constraints are especially binding for small-cap, crypto, and emerging market strategies.
**Audit application:** The Quant estimates strategy capacity by modeling market impact as a function of participation rate (order size / daily volume). If the strategy requires more than 1-2% of average daily volume, the backtest results are unreliable at the stated capital level.
**Red flags it detects:**
- No discussion of strategy capacity or maximum deployable capital
- Backtesting with position sizes that exceed realistic participation rate limits
- Projecting small-account backtest results to institutional-scale capital without impact adjustment

---

### 18. Walk-Forward Optimization & Out-of-Sample Testing
**Source:** Robert Pardo, "The Evaluation and Optimization of Trading Strategies" (2008); Lopez de Prado (2018)
**Advisors:** quant, architect
**One-liner:** Walk-forward optimization trains on rolling in-sample windows and tests on sequential out-of-sample windows, simulating how a strategy would actually be developed and deployed in real time.
**Core concept:** Traditional backtesting optimizes on historical data and reports in-sample results --- this is circular and virtually guarantees overfitting. Walk-forward analysis (WFA) divides history into sequential segments: optimize on segment 1, test on segment 2; optimize on segments 1-2, test on segment 3; and so on. Only the out-of-sample equity curve matters. The walk-forward efficiency ratio (out-of-sample performance / in-sample performance) should exceed 0.5; below 0.3 indicates severe overfitting. Combinatorial purged cross-validation (CPCV) from Lopez de Prado extends this further.
**Audit application:** The Quant demands to see the out-of-sample equity curve, not the in-sample one. A strategy that was "optimized" on all available data and reports that same period's results has provided zero evidence of predictive power.
**Red flags it detects:**
- Reporting in-sample backtest results as evidence of strategy viability
- No walk-forward, holdout, or cross-validation methodology applied
- Walk-forward efficiency ratio below 0.3, indicating the optimization does not generalize

---

## VII. Foundational Quantitative Principles

### 19. Ergodicity & Ensemble vs. Time Averages
**Source:** Ole Peters (2019) "The Ergodicity Problem in Economics"; Nassim Taleb; Kelly (1956)
**Advisors:** quant, contrarian, strategist
**One-liner:** Most financial models assume ergodicity (that the time-average equals the ensemble average), but this assumption fails catastrophically when ruin is possible.
**Core concept:** In an ergodic system, the average outcome across many parallel trials equals the average outcome of one trial over time. Financial returns are non-ergodic: a sequence of +50%, -50%, +50%, -50% has an ensemble average of 0% but a time-average of -13.4% per period (geometric decay). This means that strategies evaluated by expected (arithmetic mean) returns can be wealth-destroying in practice. Ruin is an absorbing state --- once you lose 100%, no subsequent gain recovers you. This is why geometric return maximization (Kelly) and drawdown constraints are not optional luxuries but mathematical necessities.
**Audit application:** The Quant checks whether the project evaluates compounded (geometric) returns rather than arithmetic averages, and whether ruin scenarios are explicitly modeled. A strategy with positive expected value but a non-trivial probability of ruin will eventually blow up given enough time.
**Red flags it detects:**
- Evaluating strategy quality by arithmetic mean return rather than geometric/compounded return
- No explicit ruin probability calculation or maximum loss constraint
- Monte Carlo simulations that report average outcomes but ignore the distribution of terminal wealth across paths

---

### 20. Signal-to-Noise Ratio & The Fundamental Law of Active Management
**Source:** Grinold (1989) "The Fundamental Law of Active Management"; Grinold & Kahn (2000)
**Advisors:** quant, strategist
**One-liner:** The information ratio of a strategy equals the skill per trade (information coefficient) multiplied by the square root of the number of independent trades --- breadth and accuracy are substitutes.
**Core concept:** IR = IC x sqrt(BR), where IC is the information coefficient (correlation between forecast and outcome, typically 0.02-0.10 for real strategies), and BR is the breadth (number of independent bets per year). This means a slightly-better-than-random signal (IC = 0.05) applied across 1,000 independent trades per year yields IR = 0.05 x sqrt(1000) = 1.58 --- an excellent information ratio. The same IC with only 10 trades/year yields IR = 0.16 --- noise dominates. The law explains why diversification across independent signals and instruments is the most reliable path to performance.
**Audit application:** The Quant estimates the strategy's IC and breadth independently to determine whether the claimed performance is achievable. A strategy making 50 trades per year needs IC > 0.14 to achieve IR = 1.0, which is at the extreme end of realistic skill.
**Red flags it detects:**
- Low-frequency strategy claiming high Sharpe without extraordinary per-trade accuracy
- No decomposition of performance into skill (IC) and opportunity (breadth)
- Treating correlated trades as independent bets, inflating effective breadth

---

*End of Quant advisor knowledge context. Total frameworks: 20.*