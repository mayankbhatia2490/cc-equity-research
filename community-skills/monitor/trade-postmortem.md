# Trade Post-Mortem

description: Grades a closed trade, or a year of trades, on the decision separately from the result. Reconstructs what was known at each decision, measures the outcome honestly against a benchmark, attributes return to market, sector and stock, checks plan adherence, finds evidence of behavioral bias, and ends with one journal lesson. Use after a position is closed, at year end, or when a loss makes you want to re-enter. Triggers on "post-mortem on [ticker]", "review my trades", "why did I lose on [ticker]", "grade my decisions", "year review of my trading".

*Adapted from the `trade-postmortem` prompt in yennanliu/InvestSkill (MIT). See `community-skills/THIRD_PARTY_NOTICES.md`.*

Outcomes are noisy and decisions are what you control. A good decision can lose money and a bad one can win. This skill separates the two, so you learn whether the *process* works, not only whether you were lucky this time. It is not a re-analysis of the stock with hindsight.

## Workflow

### Step 1: Inputs

A closed trade (ticker) or a year. Source of the trades, in order of preference: your written thesis or notes, a broker export (read-only pull from the broker connector if connected, or a pasted CSV), or the user's own account of what they planned. Needed per fill: date, side, quantity, price, fees. Match sells to buys by lot ID if given, else FIFO, and say which. With no written plan, reconstruct the implied plan by asking "what were you expecting, and what would have made you sell?" and label it **reconstructed**.

### Step 2: Reconstruct the timeline (the hindsight firewall)

One row per decision: date, action, shares and price, position after, thesis status then, reason given then. Grade each decision only on what was knowable that day. Mark facts learned later as `(later)` and keep them out of the process grade.

### Step 3: Measure the outcome

Gross P&L, dividends, costs (commissions, fees, spread), total return after costs, money-weighted return when there were several entries, holding period, and the same-window return of a benchmark (SPY; the sector ETF when the thesis was about the company within its industry; the local index for non-US trades). Outcome is **good** if excess return after costs is at least zero, **bad** if below. Report absolute return beside it. Currency and tax treatment depend on the user's jurisdiction and are not modeled.

### Step 4: Attribute the return

Market (benchmark return), sector (sector minus market), stock-specific (stock minus sector). They sum to the stock's return. If beta was far from 1, show alpha as stock return minus beta times market return. Then **timing effect** = actual return minus the return had every fill followed the written plan.

### Step 5: Plan adherence

Grade entry, sizing, adds, share-count ceiling, exit trigger (date it fired vs. date acted on), profit exit and reviews: 10 followed, 5 partial or late, 0 ignored. An exit lag over 10 trading days is partial, over 30 ignored. A deviation that made money is still a deviation.

### Step 6: Biases, with evidence

| Bias | Evidence pattern in the log |
|---|---|
| Anchoring | Reasons referencing cost basis ("sell when back to even") |
| Disposition effect | Winner sold with thesis intact; loser held past a fired exit trigger |
| FOMO | Entry after a >20% run in the prior 20 sessions; thesis written on or after the entry date |
| Confirmation | No `bear-case` before entry; thresholds loosened after a breach |
| Sunk cost | Adds while the thesis was weakening, "to lower my average" |
| Overconfidence | Size above the stated cap; cap raised mid-trade |
| Loss aversion | Stop moved down; held "to avoid locking in the loss" |

A bias is **confirmed** only with a quoted log line, **suspected** if partly there, otherwise not seen. Never diagnose from the outcome alone.

### Step 7: Process Score and quadrant

`Process Score = 0.25 × plan quality + 0.35 × adherence + 0.25 × bias (10, minus 2 per confirmed, minus 1 per suspected) + 0.15 × review discipline`

Score of 6.0 or more is a good decision. Combine with the outcome:

| | Good outcome | Bad outcome |
|---|---|---|
| **Good decision** | Deserved success: repeat it, don't raise size because it worked | Bad luck: confirm the thesis actually broke before "fixing" anything |
| **Bad decision** | Dumb luck: the dangerous cell; write down exactly what was skipped | Deserved loss: fix the process, not the stock |

### Step 8: One journal lesson

One first-person sentence naming the behavior, the evidence and the rule. Not about the market, not requiring hindsight. In the good-decision cells it is what to keep doing.

### Year mode

Run the steps per trade in one line each, then compute hit rate, average win and loss, payoff ratio, expectancy per trade, profit factor, largest loss vs. plan, holding period of winners vs. losers (losers held 1.5x longer with average win below average loss flags the disposition effect), excess return, quadrant counts and the most frequent bias. Under 20 closed trades is an anecdote; say so and lean on the per-trade process grades.

## Important Notes

- **Re-entry is a new decision.** Ask "would I buy this today knowing what I found?" with fresh work and a new written thesis. The old cost basis is irrelevant.
- **Don't confuse luck with edge.** One override that worked proves little; ten in a row is evidence.
- **Pairs with** `thesis-check` (the contract written before the trade) and `position-ladder` (the plan the sizing is graded against).

**What you can ask:**
- "Post-mortem on my IREN trade"
- "Grade my 2026 trades"
- "Why do my losers last longer than my winners?"
