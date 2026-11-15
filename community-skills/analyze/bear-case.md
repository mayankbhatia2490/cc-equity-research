# Bear Case

description: Builds the strongest honest short thesis against a stock or against a bullish analysis — valuation stretch, deteriorating fundamentals, earnings-quality red flags, competitive threats, management, catalysts, downside math — then lists what would prove the bear wrong. Deliberately one-sided red-team tool. Use before buying, before adding, or when you only hear the bull side. Triggers on "bear case on [ticker]", "what could go wrong with [ticker]", "red-team my thesis", "why might [ticker] fall", "devil's advocate on [ticker]".

*Adapted from the `bear-case` prompt in yennanliu/InvestSkill (MIT). See `community-skills/THIRD_PARTY_NOTICES.md`.*

Most research is written by people who like the stock. This skill forces the counterevidence to the surface. Its value comes from being one-sided: argue the bear with conviction, never fabricate, and then say what would break it.

Open every output with: *"This is a deliberately one-sided bear case. Pair it with a balanced view (`thesis-check`, `financial-forensics`) before acting."*

## Workflow

### Step 1: Pull inputs

For the named ticker, from drillr: 8+ quarters of fundamentals, valuation and growth snapshot, insider flow, recent news and filings. If the user pasted a prior analysis or thesis to challenge, read it first and attack its specific claims. Never use remembered numbers; if a figure is not in the data, say so.

### Step 2: Build the case across six pillars

| Pillar | Look for |
|---|---|
| **Valuation stretch** | Multiple vs. own 5-year range and peers; what growth and margin today's price requires (reverse DCF) and whether that is plausible |
| **Deteriorating fundamentals** | Revenue deceleration (last two quarters, not TTM), margin compression, FCF diverging from net income, rising leverage, DSO or inventory outrunning revenue, ROIC below cost of capital |
| **Earnings-quality flags** | GAAP-to-adjusted gap, recurring "one-time" charges, buybacks only offsetting SBC; run `financial-forensics` and `reporting-quality` rather than repeating them |
| **Competitive and secular threats** | Moat erosion, disruption, customer or supplier concentration, regulatory or legal overhang |
| **Management and capital allocation** | Buybacks at peak prices, value-destroying M&A, insider selling clusters, repeated guide-downs; see `management` |
| **Catalysts and timing** | A bear case without a catalyst is an opinion. List near-term (0–6 months) and medium-term (6–18 months) triggers, each with the trigger, likely size and timing |

### Step 3: Quantify the downside

- **Bear price target:** a de-rated multiple on conservative estimates, with the math shown.
- **Scenario tree:** base-bear and severe-bear with rough probabilities.
- **Risk/reward from here:** downside to target vs. upside if the bear is wrong.
- **Fundamental floor:** book value, net cash or asset value that limits the fall. Honest bears state the floor.

### Step 4: Thesis-killers (mandatory)

List 3–5 developments that would invalidate the bear case (margin re-acceleration, deleveraging, a successful product, activist or takeover interest), the strongest bull counterarguments, and the biggest risk of being on the bear side (squeeze potential, takeout risk, valuation support).

### Step 5: Present

Banner, then the bear thesis in three sentences, then the strength score, then findings by pillar with numbers and citations, then downside target, then thesis-killers.

| Pillar | Weight |
|---|---|
| Valuation stretch | 0–2 |
| Deteriorating fundamentals | 0–2 |
| Earnings-quality flags | 0–2 |
| Competitive and secular threats | 0–2 |
| Management and capital allocation | 0–1 |
| Catalyst clarity and timing | 0–1 |

Bear Case Strength is the sum, 0–10: 0–2 weak (bull case likely intact); 2.1–4 modest; 4.1–6 moderate (real red flags, worth reviewing position size); 6.1–8 strong (multiple independent negatives); 8.1–10 severe (deep impairment or fraud-risk signals).

## Important Notes

- **A weak bear case is not a buy signal.** It means the bull thesis survived scrutiny, not that it is strong. Run the positive case separately.
- **Never invent.** Every claim is grounded in data, a real risk, or an explicitly labeled assumption.
- **Cross-reference, don't duplicate.** Point to `financial-forensics`, `reporting-quality` and `management` for depth; cite short interest and squeeze risk before leaning on a short-side view.
- **Re-run when:** the next earnings release lands, the price moves ±15%, 60 days pass, or a material event hits.

**What you can ask:**
- "Bear case on CECO"
- "Red-team my thesis on EHC before I buy"
- "What would have to be true for NVTS to keep falling?"
