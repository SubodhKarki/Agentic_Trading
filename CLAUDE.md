# Agentic_Trading — start here (for Claude / any AI, any machine)

Owner: Subodh Karki (retrocumulative87@gmail.com), DFW, Central Time. Retail options
trader (Robinhood, Unusual Whales flow) plus a small systematic stock sleeve.
Prefers direct, ranked recommendations; Go/No-Go verdicts use 🟢 GO / 🟡 CAUTION / 🔴 NO-GO
on the verdict AND on each checklist item.

## Where we are (updated 2026-10-02)

- **S3 Top 20 is LIVE.** Run #1 done: 20 positions (~$12.50 each) bought 2026-10-01 in ••••8009 by the user's
  order script, verified by Claude. Next: picks after the close **Fri 2026-10-30**, user trades **Mon 2026-11-02**
  2:00–2:45pm CT. Paper ledger filled at the 2026-10-01 close (recorded 10-02). Review on/after 2027-10-01. Full status, monthly cycle, lessons and the user's to-do list:
  `autonomous_Stock/HANDOFF.md` (top section).
- Options side (UW_agentic_claude) unchanged by the S3 work.
- **Machine move DONE for the local daemons (verified 2026-11-05).** All nine launchd jobs run on the
  MacBook Pro out of `/Users/Automation/Claude/`. The **cloud** scheduled tasks are still tied to the
  Air and must be re-linked to the Pro — see "Machines" below.

## Machines (which Mac does what)

| Machine | Claude device name | Role |
|---|---|---|
| **MacBook Air** | `sabinas-macbook-air-local` | Subodh's everyday Mac. Options side (UW_agentic_claude: trail check, heartbeat, status bot). Runs autonomous_Stock only until the move. NOT on 24/7 — may be off for weeks. |
| **MacBook Pro** | ComputerName “SubodhKarki’s MacBook Pro”; Claude device name still to fill in | **autonomous_Stock host (live since the move):** S3 Discord push, momentum_v2, momentum_stocks, stock cycle/boot/listener, the S3 order script (UW venv + Robinhood login only), and the cloud S3 scheduled tasks. On 24/7, plugged in, sleep off, through Oct–Nov 2026. |

- **Move status: launchd jobs DONE (2026-11-05), cloud tasks NOT yet re-linked.** Evidence for the
  local half: `launchctl list` shows all nine labels loaded on the Pro, last exit 0, and
  `com.trading.statusbot` + `com.autonomousstock.listener` hold live PIDs. Still open: the Pro's
  **Claude** device name (not the same string as ComputerName — fill it in from a linked session),
  and re-linking the cloud scheduled tasks, which this machine's task list does not show at all.
  Until that is done they stay tied to the Air and switch themselves off whenever it is off.
  Steps: `autonomous_Stock/HANDOFF.md` → "Moving to another Mac" (helper `scripts/move_mac.sh`).
  NOT verified from here: whether the Air still has its copies of these jobs loaded. If it does,
  both Macs are running them — the duplicate-ledger hazard below. Check it on the Air.
- Which machine am I on? The session's linked computer name (`get_device_info`), or `scutil --get ComputerName` in the
  user's Terminal. (`hostname` inside Claude's device shell just says `claude` — not useful.)
- Never run the autonomous_Stock launchd jobs on both Macs at once (duplicate Discord posts, two ledgers).
- Cloud scheduled tasks that use a Mac are tied to ONE computer. After the move they must be re-linked to the Pro;
  otherwise they stay tied to the Air and switch themselves off whenever the Air is off (see the watchdog row below).

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
4. **Git must run as the `Automation` user** (verified 2026-11-04). From a `subodhkarki` session
   git refuses twice over: *"fatal: detected dubious ownership"*, and
   `.git/modules/autonomous_Stock/index` is `-rw-r--r--` owned by `Automation`, so a commit could
   not write it even past that check. Do NOT add a `safe.directory` exception for another account's
   repo to work around it. Instead hand the user one command:
   `su - Automation -c 'cd ~/Claude/Agentic_Trading/... && git ...'` — it asks for **Automation's**
   password (not `sudo`: root's `~` is `/var/root`, so the paths break). Push works fine from that
   account. Order matters: commit the submodule, bump the parent, then push submodule **then**
   parent, or the parent points at a commit the remote does not have. Prefer explicit paths in
   `git add` — other jobs leave unrelated files modified. Details in `GIT_SETUP.md`.
   (The old version of this rule named `/Users/sabinagodar/Agentic_Trading` and a stale `index.lock`;
   that is the pre-move MacBook Air setup, and that path is outside the access boundary set in
   `autonomous_Stock/AI_ACCESS_POLICY.md` anyway — never touch it.)
5. Computer use (desktop control): Claude may **click and type**. The user granted typing on
   2026-11-04; the earlier "click only, no typing" line was Claude's own note from the MacBook Air
   setup, not a rule the user set. Two caveats that are not permissions: *availability* is separate
   (some sessions have no computer-use tool at all — check before promising it), and for a long or
   locked piece of text (a scheduled-task prompt, a rules doc) prefer the clipboard or a handed-over
   command over typing it out, because a single mistyped character there is hard to spot afterwards.

## Scheduled tasks (cloud, on the user's claude.ai account; they need the Mac on + Claude app open)

| Task | ID | Notes |
|---|---|---|
| S3 Top 20 — monthly picks (paper + live $250 ticket) | trig_01PjHvF9websBbM51BgMvaqo | Last NYSE trading day, 3:15pm CT + retries to ~9:15pm CT. Tied to the MacBook Air until re-linked to the Pro. |
| S3 Top 20 — monthly paper fill | trig_01FvJ84hkrueD8WH6PVyRmYC | Days 2-9, ~6am/~8pm CT |
| Missed-run catch-up watchdog (all scheduled tasks) | trig_015Dt5P6PpfMEWeeKxqN1KbN | Cloud-only, every 2h 7am-9pm CT. A missed run (Mac offline) switches a task OFF (`suspension_reason: device_absent`). Watchdog tries to re-enable + fire; unattended runs are usually blocked from that, so it posts "ACTION NEEDED: <task>" → user says "re-enable and run <task>" in a chat. Leaves user-disabled tasks alone. |
| Robinhood Trade Journal Sync | trig_01W3YeaJr8yV3spbYjpSLMUd | Weekdays 3:30pm CT → Supabase `trades` |
| Supabase keep-alive ping | trig_01REnSWVHo13wMZBridLwaSv | every 3 days |
| ma200 daily fetch + reconcile | trig_01WbmahrEWKz2Hjg2mrnx5U4 | ma200 is paused (`PAUSE_MA200`); candidate to disable |
| Momentum Top 3 — monthly picks | trig_01CzvVot9ykbuWLmNXRYJpZR | DISABLED (cancelled) |

Use the list/update scheduled-task tools to check state; IDs above may go stale. **But they do not
work from an `Automation` session** (verified 2026-11-08): the cloud tasks belong to the Claude app
running under the owner's own login, `/Users/Automation/.claude/scheduled-tasks/` does not exist, so
`list_scheduled_tasks` returns empty and `list_task_runs` says "not found" for a valid `trig_` ID.
An empty list here is NOT evidence a task is gone or unbound. Check it in the desktop app, or in a
conversation in that app. See `autonomous_Stock/MOVE_TO_PRO.md` §6.

## Local daemons (launchd; autonomous_Stock ones move to the MacBook Pro)

- `com.autonomousstock.momentumv2` → `autonomous_Stock/scripts/run_momentum_v2.sh` (~every 3h +
  3:45/4:45pm CT weekdays). Also runs `s3_discord_push.py`, which posts new S3 order tickets to
  Discord #agentic_gent (Claude's sandboxes can't reach Discord).
- UW_agentic_claude jobs (heartbeat, trail check, status bot) — see its `SETUP.md`.

## Common requests

- "S3 Top 20: status" / "run picks now" (on demand: fire the picks scheduled task) / "done" / "12-month review" → `autonomous_Stock/HANDOFF.md`.
- Any scheduled task can be run on demand with the fire-scheduled-task tool; IDs in the table above.
- "commit and push" → `GIT_SETUP.md` (commit in submodules, bump parent, give user one push command).
