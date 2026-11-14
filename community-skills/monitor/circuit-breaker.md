# Circuit Breaker

description: Decides whether new trade risk is allowed today from realized losses alone. Four rules: a daily loss limit, a losing-streak cooldown, a weekly drawdown halt and a monthly drawdown halt. Output is TRADING_ALLOWED, COOLDOWN or HALTED. Advisory, never forces a sale. Use before screening or sizing a new entry, and after any loss. Triggers on "can I trade today", "circuit breaker", "am I in cooldown", "did I hit my loss limit", "should I stop trading this week".

*Ported from the `drawdown-circuit-breaker` skill in tradermonty/claude-trading-skills (MIT). See `community-skills/THIRD_PARTY_NOTICES.md`. The source's Python checker and exchange-calendar dependency are not used: Claude applies the rules by hand from the journal. Verification: ported 2026-11-14; not yet run on live trades. The default thresholds are the source's, not tuned or validated for you.*

A breaker exists to stop escalation after realized damage: the urge to size up and win it back is strongest exactly when judgment is worst. It counts **realized** P&L only, not paper losses. Existing positions remain your call; the breaker only blocks **new** risk.

## Workflow

### Step 1: Read the journal

From the trade journal (`trade-journal`): every closed or partly closed position with its exit time and realized P&L. Use account equity as the base for percentages. Day, week and month boundaries use the New York market calendar (Monday to Friday sessions, US market holidays excluded); for a deterministic check, state the "as of" time used.

### Step 2: Apply the rules (defaults, adjustable)

| Rule | Default | State | Releases |
|---|---|---|---|
| Daily loss | realized loss of 2.0% of equity today | HALTED | next trading day |
| Losing streak | 2 consecutive losing closes | COOLDOWN | 24 hours after the last loss exit |
| Weekly drawdown | realized loss of 5.0% week to date | HALTED | next Monday |
| Monthly drawdown | realized loss of 8.0% month to date | HALTED | first trading day of next month |

Thresholds are the source's defaults. State the values used, and offer to tighten them (for example 1.5% daily, 3-loss streak, 48-hour cooldown).

### Step 3: Handle bad data safely

- **Empty journal:** `TRADING_ALLOWED`, with the data-quality note "no history", so a new user is not blocked.
- **Journal exists but a trade is unreadable, duplicated or missing a P&L:** fail closed. Say `HALTED` with data-quality `PARTIAL` and list what to fix before re-running. Do not guess a number.

### Step 4: Present

One line: the recommendation, the rule(s) triggered, observed vs. threshold, and when each releases. Then realized P&L today, week to date and month to date, consecutive losses, and the last loss exit time. The result is also an input to `pre-trade-gate`.

## Important Notes

- **Advisory.** It informs your decision; it does not stop a broker order.
- **Realized only.** Mixing in unrealized P&L makes the breaker trip on volatility, not on behavior.
- **A cooldown is not a punishment.** Use it to run `trade-postmortem` on the last loss before the next entry.

**What you can ask:**
- "Am I allowed to open a new position today?"
- "Check the breaker with a 1.5% daily limit"
- "What releases my halt?"
