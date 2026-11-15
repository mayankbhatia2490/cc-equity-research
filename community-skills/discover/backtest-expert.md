# Backtest Expert

description: A method for deciding whether a rule-based trading idea is robust enough to risk money on, built to find strategies that "break the least" rather than ones that profit most on paper. Covers rule codification, stress tests, out-of-sample checks, and a five-part scorecard with hard blocking gates. Use before trusting any screen or entry rule, or to judge a backtest someone shows you. Triggers on "is this backtest any good", "validate my screen", "stress test this strategy", "will this rule hold up", "is this overfit".

*Ported from the `backtest-expert` skill in tradermonty/claude-trading-skills (MIT). See `community-skills/THIRD_PARTY_NOTICES.md`. The source's scoring script is not used: Claude applies the rubric below to figures you supply. Verification: ported 2026-11-14; not yet exercised on a real backtest. The source marks its own financial-logic review as not verified.*

A backtest that looks great mostly measures how hard it was fitted. The discipline is to add friction until the idea breaks, then see what survives. This pairs with the screener plan: the fundamentals lenses cannot be back-tested point-in-time, so price-based entry and exit rules are what this skill is for.

## Workflow

### Step 1: One-sentence hypothesis

State the edge and why it should exist. If it cannot be said in one sentence, stop.

### Step 2: Codify with zero discretion

Exact entry, exit (stop, target, time), sizing, filters and universe. Every decision rule-based.

### Step 3: Baseline test

At least 5 years (10 preferred), across bull, bear and high- and low-volatility regimes, with realistic commissions and conservative slippage. Test **every** name that met the criteria, including failures; hand-picked winners are survivorship bias.

### Step 4: Stress test (most of the effort)

- **Parameters:** stop at 50–150% of baseline, target at 80–120%; look for a **plateau** of stable results, not a narrow peak.
- **Friction:** slippage at 1.5–2x typical, worst-case fills, order rejections, higher commissions.
- **Time:** year-by-year results; positive expectancy in most years; no reliance on one or two exceptional years.
- **Sample:** 30 trades is the floor, 100+ preferred, 200+ for high confidence.

### Step 5: Out-of-sample

Walk-forward: optimize on one period, test on the next, roll forward. Warning signs: out-of-sample under half of in-sample, frequent re-optimization, parameters that swing between periods.

### Step 6: Score (supply the figures; show the arithmetic)

Total trades, win rate, average win %, average loss %, max drawdown %, years tested, number of tunable parameters, and whether slippage was tested.

| Dimension (0–20 each) | Rough shape |
|---|---|
| Sample size | 0 below 30 trades; about 8–15 for 30–100; 15–20 for 100–200; 20 at 200+ |
| Expectancy | 0 if negative; rises to 20 at about 1.5% per trade |
| Risk management | 0 at 50%+ drawdown; full marks under 20% with a healthy profit factor |
| Robustness | 0 under 5 years; best at 10+ years with few parameters |
| Execution realism | 20 only if slippage was tested; otherwise 0 |

70 or more: eligible to deploy; 40–69: refine; under 40: abandon. The score is diagnostic. **The gates below decide.**

### Step 7: Blocking gates (any one stops it)

Fewer than 30 trades; negative or zero expectancy; max drawdown of 50% or more, or above your stated limit; no slippage test; under 5 years tested; seven or more tunable parameters (curve-fit risk); a "too good" profile such as a win rate above 90% with drawdown under 5% (suspect look-ahead bias). Report every gate that fired, not just the first.

## Important Notes

- **Separate generating from validating.** Intuition can propose an idea; only data can approve it.
- **Keep a graveyard.** Record each failed idea, why you thought it would work, and where it broke.
- **Too good to be true usually is.** Hunt for look-ahead and survivorship bias first.

**What you can ask:**
- "I tested a 20-day breakout: 140 trades, 58% wins, 7 years; is it robust?"
- "What stress tests should I run before trusting this screen?"
- "Which blocking gates does this result trip?"
