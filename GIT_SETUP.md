# Git setup — Agentic_Trading

Reference for anyone (human or Claude, any chat, any machine) working with the
git side of this project. Read this before committing or pushing.

## Layout

```
Agentic_Trading/                 parent repo  -> github.com/SubodhKarki/Agentic_Trading   (private)
├── *.md                         Go/No-Go frameworks, Supabase guide, this file
├── .gitmodules                  submodule definitions
├── autonomous_Stock/            SUBMODULE    -> github.com/SubodhKarki/autonomous_stock
├── UW_agentic_claude/           SUBMODULE    -> github.com/SubodhKarki/UW_agentic_claude
└── live-trailing-stop           symlink to UW_agentic_claude/trading (gitignored; absolute Mac path)
```

- All three repos use branch `main` and **SSH** remotes (`git@github.com:...`).
- Commit identity: `Sabina Godar <retrocumulative87@gmail.com>`.
- The parent repo does NOT contain the submodules' files. It stores a pointer
  (a commit SHA) to each one. On GitHub they show up as links (`name @ sha`).

## Everyday workflow

Work happens inside a submodule. Commit and push there first, then bump the parent.

```bash
# 1. commit + push the repo you changed
cd ~/Agentic_Trading/UW_agentic_claude      # or autonomous_Stock
git add -A && git commit -m "..." && git push origin main

# 2. point the parent at the new commit(s)
cd ~/Agentic_Trading
git add -A && git commit -m "bump submodules" && git push
```

If you skip step 2, the parent keeps pointing at the old commit. Nothing breaks,
but a fresh clone gets the older version.

Changes to the top-level `.md` files only need a commit in the parent.

Check everything at once:

```bash
cd ~/Agentic_Trading
git status                        # parent; "modified: X (new commits)" = needs a bump
git submodule foreach git status -sb
```

## New machine / fresh clone

```bash
git clone --recurse-submodules git@github.com:SubodhKarki/Agentic_Trading.git
cd Agentic_Trading
git submodule foreach git checkout main     # submodules clone in detached HEAD; put them on main
```

If you already cloned without `--recurse-submodules`: `git submodule update --init`.

Then per machine (none of these are in git):

- Needs an SSH key added to the GitHub account (`ssh -T git@github.com` to test).
- Set the identity: `git config --global user.name "Sabina Godar"` and
  `git config --global user.email retrocumulative87@gmail.com`.
- **UW_agentic_claude:** install the secret-scanning pre-commit hook:
  `cp ops/pre-commit-secret-check.sh .git/hooks/pre-commit && chmod +x .git/hooks/pre-commit`
- Recreate the local-only files that .gitignore keeps out:
  - `.env` files (API keys, Discord webhooks/tokens; `.env.example` shows the keys)
  - `autonomous_Stock/config/local.yaml` (real account number)
  - `autonomous_Stock/data/state.db` (sleeve ledger; losing it loses ownership records)
  - `~/.tokens/robinhood.pickle`: run `automation/robinhood_login.py` once
- Optional: recreate the symlink:
  `ln -s "$PWD/UW_agentic_claude/trading" live-trailing-stop`

## Secrets rules

- Never commit `.env*` (except `.env.example`), account snapshots, tokens or webhooks.
- UW_agentic_claude `.gitignore` matches the `.env*` **prefix** on purpose (a
  mis-saved `.envesYES` once leaked a Discord token). Its pre-commit hook scans
  staged **content** for tokens and webhook URLs. A "ignored null byte" warning
  from it on the `.xlsx` journal is harmless.
- Before committing new files, skim them for keys. Scripts should read secrets from
  `.env`/env vars (see `scripts/s3_discord_push.py`).

## Notes for Claude (Cowork sessions)

The Cowork device shell (`device_bash`) runs in a sandboxed VM with the folder
mounted. It has real limits here:

1. **It can't push.** The SSH remotes are unreachable from the VM ("Connection
   closed by UNKNOWN port 65535"). Commit in the VM, then give the user the exact
   `git push` command to paste into their own Terminal.
2. **Deleting is blocked by default, which breaks git.** Even `git status` can
   leave a 0-byte `.git/index.lock` it can't remove, and every later git command
   then fails with "Another git process seems to be running". Call
   `device_request_delete_permission` on `/Users/sabinagodar/Agentic_Trading`
   **before** running any git command. If a stale lock already exists (0 bytes,
   no git process running), remove it with `rm -f <repo>/.git/index.lock`.
3. **The VM has no global git identity.** It's set per repo (`git config user.*`). A
   brand-new repo needs it set before committing.
4. **Computer use can't type into Terminal** (click-only tier), so it can't run
   the push either.
5. Add the session attribution trailer to commit messages as instructed by the system.

Suggested order in a session: request delete permission → `git status -sb` in
all three repos → review diffs and scan new files for secrets → ask the user
what to include if unclear → commit submodules → bump and commit parent → give
the user one chained push command:

```bash
cd ~/Agentic_Trading/autonomous_Stock && git push origin main && \
cd ../UW_agentic_claude && git push origin main && \
cd .. && git push origin main
```
