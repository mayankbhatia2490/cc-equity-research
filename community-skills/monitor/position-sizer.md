# Position Sizer

description: Turns a trade idea into a share count that respects a fixed risk budget. Three methods (fixed fractional, ATR-based, Kelly), portfolio caps on position size and sector exposure, and a plain statement of which constraint is binding. Use before placing any entry, when a stop is set, or when asking "how many shares". Triggers on "position size for [ticker]", "how many shares should I buy", "risk per trade", "size this trade", "Kelly sizing", "ATR sizing".

*Ported from the `position-sizer` skill in tradermonty/claude-trading-skills (MIT). See `community-skills/THIRD_PARTY_NOTICES.md`. The source's Python script is not used: Claude does the arithmetic and shows it. Verification: ported 2026-11-14; not yet run on live trades. The source repo lists its own financial-logic and security reviews as not verified.*

Sizing is about surviving a losing streak, not maximizing winners. A good idea with bad size can wreck an account. The method is deliberately mechanical, so the number comes from the stop and the risk budget, not from conviction.

## Workflow

### Step 1: Gather inputs

Account equity; entry price; either a stop price or ATR(14) with a multiplier (default 2.0x); risk per trade (default 1% of equity, never above 2% without a stated reason). Optional: max position % of account, max sector % and current sector exposure, whether the broker supports fractional shares. Pull live price and ATR from drillr or the broker connector (read-only); never assume them. The stop must be below the entry for a long.

### Step 2: Size by one method (or compare)

| Method | Formula |
|---|---|
| Fixed fractional | `shares = floor(equity × risk% ÷ (entry − stop))` |
| ATR-based | `stop = entry − ATR × multiplier`, then the same formula with `entry − stop = ATR × multiplier` |
| Kelly (budget) | `R = avg win ÷ avg loss`, `kelly = W − (1 − W) ÷ R`, **use half Kelly**; a negative result is floored at 0, meaning "do not trade this system" |

Floor shares to an integer (or to the broker's fractional precision). Never round up. If no method was named, show a comparison: fixed fractional at 0.5%, 1.0% and 1.5% risk, and ATR at 1.5x, 2.0x and 3.0x.

Worked example: equity $100,000, entry $155.00, stop $148.50, risk 1%. Risk per share $6.50, dollar risk $1,000, shares = floor(1,000 ÷ 6.50) = 153, position $23,715 (23.7% of equity).

### Step 3: Apply portfolio constraints

- `max shares by position cap = floor(equity × max position % ÷ entry)`
- Sector headroom = max sector % − current sector exposure %, converted to dollars and shares.
- The **strictest** limit wins. Say which one is binding and why.
- Portfolio heat: total open risk across positions should stay within about 6–8% of equity.

### Step 4: Present

Method and why, exact shares and position value, dollar risk and % of equity, stop price, binding constraint, and reminders: fractional shares do not remove broker minimums, spread, fees or margin rules, and intraday-trading rules are broker-specific. A 50% loss needs a 100% gain to recover.

## Important Notes

- **Half Kelly at most**, and only with a real record (100+ trades); Kelly on a guess is a false precision.
- **The stop comes first.** A size computed from a stop you won't honor is not a size.
- **Risk levels:** 0.25–0.5% conservative, 0.5–1% experienced swing trader, 1–1.5% proven system, above 2% dangerous.
- **Pairs with** `pre-trade-gate` (blocks entries larger than the plan), `circuit-breaker` (cuts size or halts after losses) and `position-ladder` (staged entries inside a ceiling).

**What you can ask:**
- "Size a trade in EHC with a stop at 118"
- "Compare 0.5%, 1% and 1.5% risk for this entry"
- "Does this position fit a 10% cap and a 30% sector cap?"
