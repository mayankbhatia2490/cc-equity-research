# Trade Journal

description: A simple local ledger that follows each idea from first note to closed position: status, planned vs. actual entry and size, kill criteria, every trim and exit with realized P&L, and one lesson at the end. It is the data source for the circuit breaker, the pre-trade gate and the post-mortem. Use to record a thesis, log an entry or exit, or review open positions. Triggers on "log this trade", "journal my entry", "record my exit", "open positions in my journal", "close out [ticker] in the journal".

*Ported from the `trader-memory-core` skill in tradermonty/claude-trading-skills (MIT). See `community-skills/THIRD_PARTY_NOTICES.md`. The source's Python store, schema validator and FMP price adapter are not used; the lifecycle and ledger design are. Verification: ported 2026-11-14; not yet run on live trades.*

A thesis you cannot find later is a thesis you will rewrite after the fact. The journal is the one place the original reason, the original risk and the original exit rule are written down before the outcome is known. The thesis itself (what you believe and why) can use the `thesis-tracker` skill; this file adds the status, risk and P&L ledger around it.

## Where it lives

One markdown file per position under `local/journal/` (for example `local/journal/EHC-20261114.md`). `local/` is git-ignored in this repo. **Never commit journal files to a public repo**: they hold your trades and account size.

## Lifecycle

`IDEA → ENTRY_READY → ACTIVE → PARTIALLY_CLOSED → CLOSED`, and any non-final state can go to `INVALIDATED` when a kill criterion fires. Moves are forward only: a closed or invalidated entry is never reopened; a re-entry is a new file with a new thesis.

| Status | Meaning |
|---|---|
| IDEA | Screened, not validated |
| ENTRY_READY | Plan written: entry condition, stop, size, kill criteria |
| ACTIVE | Filled; actual price, date and shares recorded |
| PARTIALLY_CLOSED | Some shares sold; shares remaining recorded |
| CLOSED | Fully exited; outcome and lesson recorded |
| INVALIDATED | Thesis killed before or during the hold |

## Entry template

```
# <TICKER> · opened <date> · status <STATUS>
Thesis: <falsifiable one-liner>        Horizon: <...>
Plan: entry <price/condition> · stop <price> · size <shares> · planned risk $<..> · ceiling <shares>
Kill criteria: 1) ... 2) ... 3) ...
Pre-mortem: <how this fails>
Data & sources: <as-of date, where the numbers came from>

## Ledger (one row per action)
| Date | Action | Shares | Price | Reason given then | Realized P&L | Skill that informed it |
```

## Rules for entries

- **Realized P&L** per sale = `(sell price − average entry price) × shares sold`, less fees. Cumulative P&L is the sum of the ledger; `pnl % = cumulative P&L ÷ (entry price × original shares)`.
- **Write reasons at the time**, not afterwards. A reason written later is marked `(later)`.
- **Review dates:** set the next check at entry and record when it was actually done.
- **Idempotent:** one position per file; do not duplicate the same idea.
- **Close with one lesson** in the first person (behavior, evidence, rule), then run `trade-postmortem` for the full grade.

## Important Notes

- **Statuses are facts, not feelings.** `INVALIDATED` is not a failure; ignoring a fired kill criterion is.
- **The journal feeds** `circuit-breaker` (realized losses and exit times) and `pre-trade-gate` (revenge window, plan adherence).
- **No automation required.** Claude reads and updates the files on request; nothing here connects to a broker or price feed.

**What you can ask:**
- "Open a journal entry for EHC with these kill criteria"
- "Log a partial sale of 40 shares at 128.50"
- "Show open positions and their next review dates"
