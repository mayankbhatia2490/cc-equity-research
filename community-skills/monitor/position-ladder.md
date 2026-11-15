# Position Ladder

description: Execution plan for a single holding — a share-count floor and hard ceiling, pre-committed entry rungs sized from volatility, a trim and re-add cycle, honest total-return comparison against simply holding, and a gate that stops adding when the thesis breaks. Scenario modeling, not a recommendation. Use when you own or plan to own a stock and want to stage entries without averaging down blindly. Triggers on "ladder into [ticker]", "how should I scale into [ticker]", "plan my entries", "should I average down on [ticker]", "position sizing for [ticker]".

*Adapted from the `position-ladder` prompt in yennanliu/InvestSkill (MIT). See `community-skills/THIRD_PARTY_NOTICES.md`.*

The other lenses decide what to own and what it is worth. This one answers "I own this, now what is my plan?" Its main value is the ceiling and the gate: disciplined averaging down is a machine for concentrating capital into a deteriorating business unless something stops it.

## Workflow

### Step 1: Inputs

Ticker, shares held and average cost (or zero), live price (pulled, not assumed), and a **ceiling** (max shares, max % of portfolio, or max dollars). Ask for the ceiling; never invent a cap. Defaults if unstated: floor 60% of ceiling, 5 rungs spaced 1.0x ATR(14), 2-quarter time budget, single stock.

### Step 2: Set the bounds first

Ceiling is the most restrictive of: a concentration cap (about 10% of portfolio for a large-cap, 5% for a high-beta or single-product name); a capital cap (the dollars committed if every rung fills — compute it); and a loss-tolerance cap (ceiling shares times the gap from the bottom rung to the bear-case price must be survivable). The floor is the share count you would still want if the stock did nothing for two years. State this verbatim:

> **At the ceiling, adding stops.** Further weakness is a reason to re-run the thesis gate, not to add. Raising the ceiling mid-drawdown is the most common way this plan fails.

### Step 3: Build the entry ladder

Rung spacing: volatility-scaled (1.0–1.5x ATR, default), fixed percentage (3–4% mega-cap, 5–7% mid-cap, 8–12% high-beta), or support levels. Under 0.5x ATR all rungs fill on noise; over 2x they never fill. Sizing: equal dollars (default, lowers average cost versus equal shares); a pyramid concentrates the most capital into the deepest drawdown and needs the strongest conviction. Always show:

- blended average cost at full fill and total capital at full fill,
- the drawdown the plan expects to sit through,
- the unrealized loss at full fill if price stops at the bottom rung (the number you must be able to hold),
- dry powder left uncommitted.

**Underfill:** in a durable uptrend a ladder never fills. Choose in advance between a starter tranche (30–40% at market) and a time-based backstop (re-anchor upward or accept the smaller position).

### Step 4: Trim and re-add cycle

Above blended cost, sell the highest-cost lots first, never below the floor. Below the new blended cost, re-add toward the ceiling using fresh rungs. Show a table with one full cycle (build, trim to floor, re-add to ceiling): trigger price, action, shares, position, blended cost, cash, realized P&L. Selling the highest-cost lot needs specific-lot identification; a default FIFO sale changes the numbers.

### Step 5: Tax and accounting note

Tax treatment depends on the user's jurisdiction and account type and is not modeled. If the user is a US taxpayer, flag the 30-day wash-sale window on any trim that realizes a loss, since the re-add leg can trigger it.

### Step 6: Reality check

Compare total return versus buy-and-hold with the same capital for three paths: price recovers to entry (cycler wins), price rallies well above the top rung (cycler loses, since trims sold shares that kept rising, so quantify the dollar gap), and price stays below the bottom rung (both lose; the ceiling bounds the damage). Include the opportunity cost of capital parked waiting.

### Step 7: Thesis-break gate

Stop laddering and review an exit if: the thesis is falsified (not merely delayed); accounting or governance red flags appear (run `bear-case` and `financial-forensics`); leverage deteriorates or emergency dilutive financing appears; consensus estimates fall as fast as the price (the stock is not getting cheaper); or the position already breaches the concentration cap.

Do not ladder: leveraged or inverse ETFs, binary-event names (single-drug biotech, pending cash acquisitions), or anything where the bear case is solvency.

Regime check: ADX under 25 and ATR 1.5–5% of price favor a ladder; ADX over 30 or ATR under 1% do not.

**Ladder Suitability Score (0–10):** thesis integrity 0–3 (would you buy this fresh today?), regime fit 0–2, volatility adequacy 0–2, position headroom 0–2, tax or account fit 0–1. 8+ execute as modeled; 6–7.9 reduce the ceiling and widen rungs; 4–5.9 prefer a single sized entry; under 4 do not ladder.

## Important Notes

- **This measures execution suitability, not price direction.** Read direction from `thesis-check` or `bear-case`.
- **Pairs with** `risk-stress-test` (the concentration ceiling) and `trade-postmortem` (grading the plan afterward).

**What you can ask:**
- "Plan a ladder into EHC with a 5% ceiling"
- "Should I average down on CECO?"
- "How much would a trim and re-add cycle have changed my cost basis?"
