# Pre-Trade Gate

description: A short checklist run immediately before placing a manual entry order. It blocks planless, oversized, revenge-risk, regime-blocked and circuit-breaker-blocked entries and records the decision. Decisions are GO, NO_GO, REVIEW_REQUIRED or NO_ACTIONABLE_ORDERS. It never places or cancels an order. Use right before you click buy. Triggers on "pre-trade check", "can I take this trade", "run the gate on [ticker]", "am I allowed to enter", "check my discipline before I buy".

*Ported from the `pre-trade-discipline-gate` skill in tradermonty/claude-trading-skills (MIT). See `community-skills/THIRD_PARTY_NOTICES.md`. The source's Python rule engine is not used: Claude applies the rules below by hand. Verification: ported 2026-11-14; not yet run on live trades; the source's own security and financial-logic reviews are marked not verified.*

Most blown accounts come from entries that broke the trader's own rules: no plan, no stop, a bigger size than planned, a trade taken to win back a loss. This gate makes those four failures visible at the one moment they can still be stopped. It is manual and offline: it reads your answers and your journal, nothing else.

## Workflow

### Step 1: Collect the answers (ask, never assume)

For each intended entry: ticker; order intent (a real intent to place an order today, or just a watchlist idea); is the entry in the written plan; is the stop predefined; is the size within the plan; **planned risk in dollars** and **actual risk in dollars** (zero is valid; a missing or non-numeric value means REVIEW_REQUIRED). Watchlist or ignored ideas are recorded as `NO_ACTIONABLE_ORDERS` and nothing is placed.

### Step 2: Check upstream state

- **Revenge window:** any losing exit or partial loss in the journal within the last 24 hours (adjustable). See `trade-journal`.
- **Circuit breaker:** run `circuit-breaker`; `COOLDOWN` or `HALTED` blocks entry.
- **Market regime:** if you keep an exposure or regime call (for example from `themes` or a market-posture note) and it says reduce-only or cash priority, that blocks entry. If you keep no such call, say so rather than assuming it passes.

### Step 3: Apply the rules

The gate returns **NO_GO** if any of these is true for an actionable entry:

- the entry is not in the written plan, the stop is not predefined, or the size is not confirmed within the plan;
- actual risk dollars exceed planned risk dollars;
- a loss occurred inside the revenge window;
- the regime call is reduce-only or cash priority;
- the circuit breaker is `COOLDOWN` or `HALTED`.

It returns **REVIEW_REQUIRED** when an input is missing or unreadable (risk dollars, the circuit-breaker result, or the journal when a regime or breaker input was promised). Otherwise **GO**.

### Step 4: Present and record

State the decision in one line, list each failed or unknown rule, and quote the checklist answers used so the record can be audited later. Append the decision to the journal entry for the trade (`trade-journal`). Do not advance any review schedule.

## Important Notes

- **Manual execution only.** The output is a checklist result, not an order. Nothing here places, cancels or routes anything, and no broker connection should be used by this skill.
- **Unknown is not pass.** A missing answer blocks the entry until it is supplied.
- **A written plan comes first.** If the plan was written after the price moved, that is a failed answer.
- **Pairs with** `position-sizer` (planned risk), `circuit-breaker`, `trade-journal` and `trade-postmortem` (grading adherence later).

**What you can ask:**
- "Run the gate for a CECO entry, risk $150, stop at 44"
- "Am I inside my revenge window after Tuesday's loss?"
- "Why did the gate say REVIEW_REQUIRED?"
