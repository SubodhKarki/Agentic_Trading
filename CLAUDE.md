# Agentic_Trading — start here (for Claude / any AI, any machine)

Owner: Sabina Godar (retrocumulative87@gmail.com), DFW, Central Time. Retail options
trader (Robinhood, Unusual Whales flow) plus a small systematic stock sleeve.
Prefers direct, ranked recommendations; Go/No-Go verdicts use 🟢 GO / 🟡 CAUTION / 🔴 NO-GO
on the verdict AND on each checklist item.

## What's in here

| Path | What | Read first |
|---|---|---|
| `autonomous_Stock/` (submodule) | Systematic stock strategies. **Active: S3 Top 20 momentum** (paper $10k + live $250). | `autonomous_Stock/HANDOFF.md` (top section), then `momentum_s3_top20_forward_test.md` |
| `UW_agentic_claude/` (submodule) | Options side: Unusual Whales alerts → playbook scoring → Discord; trailing-stop/ladder engine; trade journal (xlsx + Supabase SQL). | `UW_agentic_claude/CLAUDE.md`, `README.md`, `SETUP.md` |
| `*GoNoGo*.md`, `PUT_Options_Flow_*.md` | Options Go/No-Go frameworks (0-7 DTE, 7-45 DTE, puts) | as needed |
| `Supabase_Review_Guide.md` | Trade-journal database notes | as needed |
| `GIT_SETUP.md` | Repo layout, submodule workflow, new-machine setup, sandbox git quirks | **before any git command** |

## Accounts (never print full numbers — mask as ••••1234)

- Robinhood **Agentic** ••••9209 — the one agent-tradable account (options trading, journal sync).
  NOT used for S3.
- Robinhood individual (default) ••••8009 — **the S3 live sleeve account** since 2026-09-30 ($250
  deposited, S3-only money). Read-only to Claude. `S3_ACCOUNT_NUMBER` in `autonomous_Stock/.env`.
- Fidelity Roth IRA (long-term) and a taxable account — not connected; not used for S3.
- S3 stays in a **taxable** account by the user's choice. Tax notes: churn is mostly short-term
  gains; **wash sales apply across all accounts incl. the IRA and options on the same ticker**
  (e.g. S3 sells AMD at a loss, then AMD calls bought within 30 days → loss disallowed).

## Hard rules for Claude

1. **Claude never places, previews, stages, cancels or modifies stock orders for S3**, even with the
   user's approval (session safety rule — not a Robinhood limit). The user places S3 orders by running
   `autonomous_Stock/scripts/s3_place_orders.py --execute` in their own Terminal (one "yes") or by hand.
   Claude never runs `--execute` and never logs in to Robinhood with the user's credentials.
2. Locked rule documents (`*_preregistration.md`, `*_forward_test.md`) are never changed after results
   are seen — bug fixes only, each logged in that file's change log with a reason.
3. Secrets: `.env*`, `config/local.yaml`, `~/.tokens/robinhood.pickle`, webhooks, account numbers —
   never commit, never print. UW repo has a content-scanning pre-commit hook.
4. Git from the Cowork shell: request delete permission for `/Users/sabinagodar/Agentic_Trading`
   BEFORE any git command (stale `index.lock` otherwise); pushing is always the user's Terminal
   (SSH remotes unreachable from the sandbox). Details in `GIT_SETUP.md`.
5. Computer use can only click in Terminal (no typing) — hand the user commands to paste instead.

## Scheduled tasks (cloud, on the user's claude.ai account; they need the Mac on + Claude app open)

| Task | ID | Notes |
|---|---|---|
| S3 Top 20 — monthly picks (paper + live $250 ticket) | trig_01PjHvF9websBbM51BgMvaqo | Last NYSE trading day, 3:15pm CT + retries to ~9:15pm CT. Tied to this Mac. |
| S3 Top 20 — monthly paper fill | trig_01FvJ84hkrueD8WH6PVyRmYC | Days 2-9, ~6am/~8pm CT |
| Robinhood Trade Journal Sync | trig_01W3YeaJr8yV3spbYjpSLMUd | Weekdays 3:30pm CT → Supabase `trades` |
| Supabase keep-alive ping | trig_01REnSWVHo13wMZBridLwaSv | every 3 days |
| ma200 daily fetch + reconcile | trig_01WbmahrEWKz2Hjg2mrnx5U4 | ma200 is paused (`PAUSE_MA200`) |
| Momentum Top 3 — monthly picks | trig_01CzvVot9ykbuWLmNXRYJpZR | DISABLED (cancelled) |

Use the list/update scheduled-task tools to check state; IDs above may go stale.

## Local daemons (launchd on the Mac)

- `com.autonomousstock.momentumv2` → `autonomous_Stock/scripts/run_momentum_v2.sh` (~every 3h +
  3:45/4:45pm CT weekdays). Also runs `s3_discord_push.py`, which posts new S3 order tickets to
  Discord #agentic_gent (Claude's sandboxes can't reach Discord).
- UW_agentic_claude jobs (heartbeat, trail check, status bot) — see its `SETUP.md`.

## Common requests

- "S3 Top 20: status" / "run picks now" (on demand: fire the picks scheduled task) / "done" / "12-month review" → `autonomous_Stock/HANDOFF.md`.
- Any scheduled task can be run on demand with the fire-scheduled-task tool; IDs in the table above.
- "commit and push" → `GIT_SETUP.md` (commit in submodules, bump parent, give user one push command).
