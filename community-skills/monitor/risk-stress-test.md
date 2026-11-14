# Risk Stress Test

description: Converts a list of holdings into the numbers that decide whether you are still around to be right — dollar loss in a bad month, losses replayed through real market crashes, how much diversification disappears in a crisis, factor exposures, and how many days it takes to exit. Scores the book against a stated drawdown budget. Use when sizing a portfolio, before adding to a concentrated book, or after a loss. Triggers on "stress test my portfolio", "how much could I lose", "portfolio risk", "is my book too concentrated", "what happens in a crash".

*Adapted from the `risk-stress-test` prompt in yennanliu/InvestSkill (MIT). See `community-skills/THIRD_PARTY_NOTICES.md`.*

Every other lens asks whether a holding is good. This one asks how much the whole book can lose, how fast, and whether it can be sold when it has to be. A language model cannot recall a covariance matrix, so every number here comes from data pulled or pasted, and every formula is shown so the arithmetic can be checked.

## Workflow

### Step 1: Inputs

Holdings with weights (or shares and prices), per-holding beta against SPY, annualized volatility and 30-day average volume, and total portfolio value. Pull holdings from the broker connector (read-only) and price history from drillr. Ask for the **drawdown budget**; if unstated propose one (15% conservative, 25% balanced, 40% aggressive) and show how the score changes at the others. If a beta or volatility is missing, use the sector ETF and mark every derived number `(proxy)`. Treat cash, bonds and hedges as zero unless stated.

### Step 2: Exposure map

Net beta = sum of weight times beta. Sector weights (flag any above 30%) and top-3 weight. Say plainly what the book is: "a 1.3-beta, 62%-technology portfolio" is more useful than any statistic that follows.

### Step 3: Historical scenario replay

Measure every holding over each scenario's **common date window** (index peak to trough), not each stock's own peak-to-trough:

| Scenario | Window |
|---|---|
| 2008 financial crisis | Oct 2007 to Mar 2009 |
| COVID crash | 19 Feb 2020 to 23 Mar 2020 |
| 2022 rate shock | Jan 2022 to Oct 2022 |
| 2025 tariff shock | 19 Feb 2025 to 8 Apr 2025 |

Compute the index move from price history rather than quoting remembered figures. Portfolio move = sum of weight times each holding's replay return; use actual return if it traded through the episode, otherwise beta times the index move (`proxy`). Report the worst scenario in dollars and whether it exceeds the budget. That comparison is the headline.

### Step 4: Parametric VaR and CVaR

`VaR = z × σp × √t × value` with z = 1.645 (95%) or 2.326 (99%); `CVaR ≈ 2.063 σ√t × value` (95%) or `2.665 σ√t × value` (99%); t = 1/252 for a day, 21/252 for a month. Normal returns understate fat tails, so present VaR as the **floor** of a bad day and the scenario replay as the honest tail.

### Step 5: Correlation spike

Compute portfolio σ twice using `σp² = ΣΣ wi wj σi σj ρij`: with observed correlations (or assumed 0.6 within sector, 0.35 across), and with crisis correlations (0.85 for every equity pair, 0.95 within sector). A crisis σ above 1.4x normal means the book is diversified in name only.

### Step 6: Factor sensitivities

Rates up (10-year +100 bp), dollar up (+5%), oil up (+25%): who gets hurt, who benefits, what share of the book carries each. Score Low, Medium or High and give the direction. Use segment and geographic revenue disclosure, with sector as a stated proxy.

### Step 7: Liquidity

Days to exit = shares held divided by 20% of average daily volume. Flag above 5 days; above 20 days is a private holding with a ticker, so widen its replay loss by a 10–25% haircut.

### Step 8: Risk Budget Score (0–10)

| Component | Weight | Full marks |
|---|---|---|
| Worst scenario vs. budget | 35% | at most 60% of budget |
| 1-month CVaR 99% vs. budget | 20% | at most 40% of budget |
| Correlation-spike resilience | 15% | crisis σ at most 1.2x normal |
| Concentration | 15% | top-3 under 25%, no sector over 30% |
| Liquidity | 10% | every position exits within 2 days |
| Factor tilt | 5% | no High exposure |

Score each 0–10 linearly between the anchors. 6.0 or more is within budget, 4.0–5.9 at the edge, under 4.0 over budget.

## Important Notes

- **This measures risk, not attractiveness.** A low score means reduce risk, not that any holding is a short.
- **Show assumptions.** Currency mix, margin and hedges not captured are listed as limits.
- **Pairs with** `position-ladder` (the ceiling this report may lower) and `thesis-check` (is a drawdown a thesis event or just volatility).

**What you can ask:**
- "Stress test my IBKR book"
- "How much do I lose in a 2022-style rate shock?"
- "Which position is my biggest liquidity risk?"
