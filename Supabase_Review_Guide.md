# Trading Database — Review Guide

Project: `ffrbccronrocaierbgjr` · Run these directly in the Supabase SQL Editor
(Dashboard → SQL Editor → New Query) to review your own data.

---

## Tables

| Table | Rows | What it holds |
|---|---|---|
| `trades` | 84 | Every logged trade — entry/exit dates, P&L, return %, win/loss, notes, execution score, emotional state, asset type |
| `strategy_rules` | 37 | Every rule across 4 categories (see below) — the actual Go/No-Go criteria |
| `session_notes` | 8 | Qualitative lessons from the Sept 2026 session (process failures, fixes) |
| `agentic_account_snapshots` | 2 | Real Agentic account balance over time (separate from the older $5K-model tracker) |
| `monthly_performance` | 8 | Historical month-by-month record under the older $5K-notional account model |

## Views & Functions (pre-built, do the analysis for you)

| Name | Type | What it does |
|---|---|---|
| `v_by_ticker` | view | Per-ticker stats: trade count, total P&L, win rate, avg win, avg loss |
| `v_by_asset_type` | view | Same breakdown, grouped by option/stock/crypto |
| `v_cooldown_flags` | view | Flags any trade opened within 4 hours (or 1 day) of a prior loss on the same ticker |
| `pretrade_check(ticker)` | function | One call returns: cooldown status + ticker history + the full Macro Go-NoGo checklist for that ticker |

---

## Review Queries

### 1. Most recent trades
```sql
SELECT * FROM trades
ORDER BY date_closed DESC NULLS LAST
LIMIT 10;
```

### 2. Overall performance summary
```sql
SELECT
  count(*) AS n_trades,
  round(100.0 * count(*) FILTER (WHERE win_loss='Win') / count(*), 1) AS win_rate_pct,
  round(avg(realized_pnl) FILTER (WHERE win_loss='Win'), 2) AS avg_win,
  round(avg(realized_pnl) FILTER (WHERE win_loss='Loss'), 2) AS avg_loss,
  round(avg(realized_pnl), 2) AS expectancy_per_trade,
  sum(realized_pnl) AS total_pnl
FROM trades;
```

### 3. Biggest wins / biggest losses
```sql
SELECT ticker, date_closed, realized_pnl, return_pct, notes
FROM trades ORDER BY realized_pnl DESC LIMIT 5;   -- biggest wins

SELECT ticker, date_closed, realized_pnl, return_pct, notes
FROM trades ORDER BY realized_pnl ASC LIMIT 5;    -- biggest losses
```

### 4. Performance by ticker (uses the pre-built view)
```sql
SELECT * FROM v_by_ticker ORDER BY total_pnl ASC;   -- worst tickers first
```

### 5. Performance by asset type (options vs. stock vs. crypto)
```sql
SELECT * FROM v_by_asset_type;
```

### 6. Full strategy rules, organized by category
```sql
SELECT source_sheet, rule_number, title, detail, rationale
FROM strategy_rules
ORDER BY source_sheet, rule_number NULLS LAST;
```
Categories currently in `source_sheet`: `Core Strategy Tiers`, `Macro Go-NoGo Addendum`,
`Strategy v2 - Risk_Reward`, `Sept 2026 Session Rules`.

### 7. Just the Go/No-Go checklist
```sql
SELECT rule_number, title, detail, rationale
FROM strategy_rules
WHERE source_sheet = 'Macro Go-NoGo Addendum' AND rule_number IS NOT NULL
ORDER BY rule_number;
```

### 8. Pre-trade check for a specific ticker (cooldown + history + checklist in one call)
```sql
SELECT * FROM pretrade_check('INTC');   -- swap in any ticker
```

### 9. All same-day/next-day re-entries after a loss (cooldown violations, past or would-be)
```sql
SELECT * FROM v_cooldown_flags;
```

### 10. Execution score vs. outcome — are you judging yourself on process, not luck?
```sql
SELECT ticker, date_closed, win_loss, realized_pnl, execution_score, emotional_state
FROM trades
WHERE execution_score IS NOT NULL
ORDER BY date_closed DESC;
```

### 11. Session lessons (what changed and why)
```sql
SELECT session_date, topic, detail
FROM session_notes
ORDER BY session_date DESC, id;
```

### 12. Real account growth over time
```sql
SELECT * FROM agentic_account_snapshots ORDER BY snapshot_date;
```

### 13. Setup type breakdown — which tiers are actually working
```sql
SELECT setup_type,
  count(*) AS n_trades,
  round(avg(realized_pnl), 2) AS avg_pnl,
  round(100.0 * count(*) FILTER (WHERE win_loss='Win') / count(*), 1) AS win_rate_pct
FROM trades
WHERE setup_type IS NOT NULL
GROUP BY setup_type
ORDER BY avg_pnl DESC;
```

### 14. Everything about one specific trade
```sql
SELECT * FROM trades WHERE ticker = 'AAPL' ORDER BY date_closed DESC;
```

---

## Notes on using this yourself

- All tables have RLS enabled — you're only ever seeing your own data
- `trades.notes` holds the full reasoning behind every win and loss, in plain language
- If a query above returns nothing for a new table you add later, it just means that table/column doesn't have data yet — not an error
- Any of these queries can be combined with `WHERE date_closed >= '2026-09-01'` (or similar) to scope to a specific time window
