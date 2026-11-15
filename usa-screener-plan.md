# USA Stock Screener: Plan

Built from what is already connected (drillr, IBKR, Notion/n8n if enabled). No installs required to start. It narrows about 5,000 US names to a short list worth reading. It does not predict winners, and no "sure shot" exists.

## Principles (learned from this session's work)

1. **Funnel, not a ranker.** Universe, then quality gate, then signal lenses, then cross-check, then human review. The gate removes names whose earnings are cash-weak or dilutive before any signal is applied.
2. **Recent run-rate beats TTM.** Option Care Health showed 6% TTM growth but only +1% to +2% in its last two quarters. Always compute the last two quarters YoY.
3. **Verify before trusting.** drillr rows have errors (annual flows in Q4 rows, stale share counts, zero SBC). Use FY rows for cash flow, and check the top names against filings.
4. **Prove it forward.** drillr has no point-in-time fundamentals snapshots, so a fundamentals screen cannot be cleanly back-tested. Log every list and measure it forward instead.
5. **Sizing matters more than picking.** The current book's losses (CECO, NVTS, IREN) are sizing and exit problems first.

## Step 1: Universe (all lenses)

| Rule | Setting (default, adjustable) |
|---|---|
| Listing | US common stock; exclude ETFs, funds, SPACs |
| Market cap | at least $1B |
| Price | above $5 |
| History | listed 12+ months; 8+ quarters of data |
| Excluded industries | biotech, pharma, banks, REITs, BDCs, mortgage |

## Step 2: Quality gate (pass at least 4 of 5)

| Test | Pass |
|---|---|
| FCF / net income, 3-year cumulative (FY rows) | at least 0.85 |
| SBC as % of revenue | at most 8% (5% preferred) |
| Diluted share count change | at most +3% a year |
| DSO YoY change; inventory growth vs. revenue growth | DSO at most +10%; inventory at most revenue growth +10pp |
| Net debt / EBITDA | at most 3.0x |

**Automatic out:** FCF/NI below 0.5 with positive net income; SBC above 15% of revenue; share count +10% YoY; A/R growth above 2x revenue growth with revenue growth under 10%.

## Step 3: Lenses (a name can pass more than one)

| Lens | Rules | Why it exists |
|---|---|---|
| **A. Quality at a fair price** | ROIC at least 15%; FCF margin at least 8%; revenue growth at least 8% TTM and 6% forward; fwd P/E at most 25; price not more than 25% below the 200-day average | Compounders you can hold through noise |
| **B. Acceleration** | Last-two-quarter YoY revenue growth at least 12% and at least 5pp above TTM; margin up YoY; average EPS surprise above 5%; 8 or fewer analysts; 3-month return under +60% | Earlier, before consensus catches up |
| **C. Insider cluster (overlay)** | 3+ insiders buying at least $100K each in 90 days, including a CEO, CFO or chair; drop if sales exceed 2x purchases in the same window | A confirming signal only, never a standalone reason |

**Ranking:** number of lenses passed, with quality-gate score as tie-break. No weighted model; it overfits.

## Step 4: Cross-check (automatic, per shortlist name)

Next earnings date (skip names reporting within 5 days unless you accept the event risk), Street rating and target vs. price, insider net flow, executive changes, one news check, and the six-pattern forensics from `financial-forensics`.

## Step 5: Weekly output (maximum 15 names)

Ticker, lenses passed, key numbers, red flags, next earnings date, insider flag, Street upside. Top 3 to 5 get a full `financial-forensics` plus `management` pass.

## Step 6: Position rules (suggestions; your decision)

- Maximum 12 names; starter position at most 3% of net liquidation value; add up to 6% only after the next print confirms the thesis.
- Write 3 kill criteria before buying, stored in Notion or this repo.
- No averaging down. No more than 25% in one theme.

## Phases

| Phase | When | Work | Install? |
|---|---|---|---|
| 0 | Now | Run the Step 1-3 filters in drillr; produce the first list | No |
| 1 | Week 1 | Add the Step 4 cross-check; save the list and prices to a log | No |
| 2 | Weeks 2 to 12 | Run weekly; compare each pick to IWM and SPY at 1, 3 and 6 months | No |
| 3 | Month 3 | Keep only lenses with a positive median excess return after at least 30 picks | No |
| 4 | Optional | edgartools to verify filings; vectorbt to test price-based entry and exit rules; n8n or a scheduled task to run weekly | Yes |

## Known limits

- drillr is a current snapshot, so there is no historical back-test of the fundamentals lenses.
- A free-tier query returns at most 100 rows, so filters must be tight or paged.
- Thresholds are my starting defaults, not optimised; tune them from the forward log.
- Insider purchases are a moderate signal; sizes are often small relative to company value.
- A short list narrows the search; it does not remove the risk of a bad pick.

---

## Sources & References

**Data:** drillr standardized financial data; drillr market and ownership data; drillr analyst consensus data. No SEC filings were read for this plan.
