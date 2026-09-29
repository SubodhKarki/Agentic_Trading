# PUT Options Flow Go/No-Go Framework — 7–45 DTE

## Purpose
Evaluate bearish PUT option-flow alerts using Unusual Whales (UW) plus live market data. The goal is to find clean, actionable bearish setups without over-engineering the checklist.

## Core assumptions
- Primary strategy: **7–45 DTE**
- Typical position: **1 contract**
- Strong setup: **2 contracts**
- Very strong setup: **up to 3 contracts**
- The trader decides how much money to transfer into the dedicated trading account.
- Do not impose an artificial 1% account-risk rule.
- AI may recommend 1/2/3 contracts, but the human decides the bankroll and gives final GO for a new position.
- Soft cautions should not automatically become NO-GO.

## 1. PUT Flow Baseline

| Condition | Baseline |
|---|---:|
| DTE | 7–45 |
| Total premium | $500K+ |
| Ask-side | 65%+ |
| Volume/OI | 1.5x+ |
| Contracts | 1 normally, 2 strong, 3 max |
| Default stop | -25% |
| Strong setup stop | -20% |

Do not let a UW label override the actual numbers.

## 2. Flow Quality
Large PUT premium does not automatically mean bearish conviction. It can represent an opening bearish position, closing trade, hedge, spread, roll, or other complex positioning.

Prefer:
- meaningful premium
- high ask-side execution
- volume materially above OI
- reasonable OI
- genuinely separate repeated prints
- underlying confirming weakness
- flow appearing before/during the move

Caution when:
- OI is extremely small
- huge Volume/OI is caused by tiny OI
- trade looks like a spread or hedge
- underlying is strongly rising
- flow appears only after a major selloff

## 3. Anti-Chasing / Flow Timing
Ask:

> **Has the underlying already fallen substantially before I am entering?**

Guide:
- 0–2% decline: fresh
- 2–5%: caution
- 5%+: likely chasing; require stronger evidence

This is a **soft caution**, not an automatic NO-GO.

Also check today's move, alert price, recent 30–60 minute low, VWAP, and major support.

## 4. Five Core Checks

| Check | GREEN | CAUTION | RED |
|---|---|---|---|
| Fresh vs stale | Fresh bearish momentum | Already down significantly | Move looks exhausted/stale |
| News/events | No major conflict | Manageable event risk | Unsuitable binary event |
| Sector | Peers also weak | Mixed | Stock falling alone while sector is strongly bullish |
| Support/downside room | Plenty of downside room | Approaching support | Major support directly below / little room |
| Catalyst/thesis | Credible bearish reason | Mostly technical | Rumor/unconfirmed/no credible thesis |

Minor caution does not automatically kill a trade. Hard red flags normally do.

## 5. Bearish Market Confirmation
Prefer:
- stock below VWAP
- lower highs/lower lows
- failed bounce
- breakdown/rejection
- weak relative strength
- sector weakness

SPY/QQQ do not have to be red. A stock can be a good PUT if it materially underperforms the market.

Example:
- SPY -0.2%
- Stock -3.0% → meaningful relative weakness

## 6. Support / Downside Room
Identify:
1. current price
2. nearest meaningful support
3. next support
4. realistic downside before expiration

Avoid buying PUTs directly into major support unless there is strong evidence of a break.

## 7. Expected-Move Sanity Check
Ask:

> **How far must the underlying fall for this option to produce the intended return?**

Consider strike, current price, DTE, IV, expected move, and support.

This is a sanity check, not a complicated pricing model.

## 8. IV / Liquidity
General guide:
- normal IV: preferred
- 60–90% IV: caution
- 90%+: strong caution

High IV is not automatic NO-GO; ask whether the bearish thesis justifies the premium.

Spread >8% of ask → liquidity caution.

## 9. DTE / Theta
Keep the strategy in the **7–45 DTE** window.
- 7–14 DTE: faster, more timing-sensitive
- 15–45 DTE: more room for thesis development

Simple question:

> **Does the option have enough time for the expected bearish move?**

Do not turn theta into a complicated scoring system.

## 10. Thesis + Invalidation
Every PUT must have:

**Thesis:** Why should the stock fall?

**Invalidation:** What would prove the bearish thesis wrong?

Examples of invalidation:
- reclaiming VWAP and broken support
- higher high after failed breakdown
- strong sector reversal
- bearish flow contradicted by price

The option percentage stop still applies, but do not ignore a clearly invalidated underlying thesis.

## 11. Entry Pricing
Suggested limit:

`bid + (ask - bid) × 0.35`

Do not chase an unfilled order automatically.

If the stock is near a major recent low or has just experienced a sharp selloff, flag **potentially late bearish entry**.

## 12. Exit Ladder

| Peak gain | Action | New stop |
|---|---|---|
| 0–10% | Hold | Tier floor (-25%/-20%) |
| +10% | Sell 25–33% | Breakeven |
| +25% | Sell another ~25% | +10% |
| +50% | Sell another ~25% | +25% |
| +100%+ | Trim to small runner | Trail ~27.5% below peak, floor +50% |

With 1 contract, no partial sale is possible; ratchet the stop instead.

## 13. Stop Rules
- Standard: **-25%**
- Strong/high-conviction: **-20%**
- Use stop-market, not stop-limit
- Use GTC for overnight protection
- When replacing a stop, confirm cancellation before placing the replacement
- A GTC stop does not guarantee the fill price during a gap

## 14. Account-Level Circuit Breaker
Because this is a dedicated trading bankroll, do not force a 1% risk rule.

Add:
- approximately **20% trading-bankroll loss in one day → stop opening new positions**
- **2 consecutive losses → pause and re-evaluate**

These are behavioral safeguards, not trade signals.

## 15. Cooldown
- Same ticker stopped out within 0–4 hours → **hard NO-GO**
- 4–48 hours → **CAUTION**
- Do not revenge-enter to recover a previous loss.

## 16. Macro / Events
Check earnings, FOMC, CPI, PCE, NFP, major company announcements, and major macro/geopolitical shocks.

Do not accidentally enter a binary event trade.

## 17. Multi-Alert Deduplication
If the same ticker/strike/expiration appears repeatedly, determine whether alerts represent the same print or genuinely separate flow.

Same premium + same volume + same price + same contract = count once.

## 18. Decision Logic

### 🟢 GO
- meaningful bearish flow
- reasonably fresh
- underlying confirms weakness
- sufficient downside room
- acceptable liquidity
- no hard blocker
- 7–45 DTE
- clear thesis and invalidation

Minor cautions can coexist with GO.

### 🟡 CAUTION
Examples:
- somewhat late
- elevated IV
- mixed sector
- approaching support
- choppy market
- one meaningful confirmation missing

Explain the concern; do not automatically reject.

### 🔴 NO-GO
Hard blockers include:
- terrible liquidity
- clearly stale/ambiguous flow
- thesis already invalidated
- unsuitable binary event
- severe chasing with little downside room
- cooldown violation
- missing critical live data
- option structure clearly does not make sense

## 19. Required Live Data
Before a real GO, use:
- option bid/ask
- option volume/OI
- premium
- ask-side %
- DTE/strike
- current stock price and daily move
- VWAP
- RSI if available
- sector/peer performance
- earnings date
- IV
- current buying power

If critical live information is unavailable:

**INSUFFICIENT DATA — NO-GO UNTIL VERIFIED.**

Never guess.

## 20. Quick GO/NO-GO Format

**PUT:** Ticker / Strike / Expiration

**Flow:** PASS/FAIL  
**Freshness:** PASS/CAUTION/FAIL  
**Underlying:** PASS/CAUTION/FAIL  
**Downside room:** PASS/CAUTION/FAIL  
**Liquidity/IV:** PASS/CAUTION/FAIL  
**Events:** PASS/CAUTION/FAIL  
**Cooldown:** CLEAR/FLAGGED

**Biggest risk:** one sentence

**Thesis:** one sentence

**Invalidation:** one sentence

# VERDICT: 🟢 GO / 🟡 CAUTION / 🔴 NO-GO

If GO:
- Suggested size: 1 / 2 / 3 contracts
- Suggested limit: $___
- Initial stop: ___%

## 21. Human Confirmation
Opening a new real-money PUT requires explicit human GO.

One confirmation covers entry + initial stop.

Predetermined trailing/risk-management actions do not require a new confirmation each time.

Manual early exits outside predetermined rules should be confirmed by the human.

No unattended autonomous trading.

## 22. Philosophy
The system should not try to eliminate every losing trade.

It should identify:

> **Good bearish flow + good timing + bearish price confirmation + enough downside room + acceptable option structure.**

Do not require every indicator to agree perfectly.

The hierarchy is:

**Hard blocker → NO-GO**

**Strong setup + minor caution → potentially GO**

**Clean setup → GO**

The AI's job is to improve consistency and discipline, not make trading impossible.
