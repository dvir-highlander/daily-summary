# daily-summary

A CLI tool that summarizes your daily git commits across multiple repositories. Optionally uses AI ([Claude Code](https://claude.ai/code)) to generate human-readable summaries from your actual code changes.

Works with local commits — no need to push first.

<!-- Terminal demo GIF: record with `vhs` (brew install vhs) using demo.tape -->
<!-- ![demo](assets/demo.gif) -->

## Install

```bash
git clone https://github.com/dvir-highlander/daily-summary.git
cd daily-summary
chmod +x daily-summary
cp daily-summary ~/bin/

# Make sure ~/bin is in your PATH
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc

# Run the setup wizard
daily-summary --setup
```

## Commands

| Command | Description |
|---------|-------------|
| `daily-summary` | Yesterday's commits (raw) |
| `daily-summary today` | Today's commits (raw) |
| `daily-summary 2025-03-12` | Specific date (cached if exists) |
| `daily-summary --ai` | Yesterday's commits — AI-reviewed |
| `daily-summary --ai today` | Today's commits — AI-reviewed |
| `daily-summary --refresh` | Force re-scan yesterday (overwrite cache) |
| `daily-summary --refresh today` | Force re-scan today |
| `daily-summary --week` | This week overview (Mon-Sun) |
| `daily-summary --week last` | Last week overview |
| `daily-summary --list` | Browse saved summaries (interactive) |
| `daily-summary --open` | Open summaries folder |
| `daily-summary --setup` | Configure on a new machine |
| `daily-summary --help` | Show help |
| `daily-summary --version` | Show version |

## Examples

### `daily-summary today` — Raw mode

Quick commit list, no AI, instant.

```
$ daily-summary today

# Daily Summary — Wednesday, March 12 2025

## my-saas-app (2 commits)

- f4a21c03 Add Stripe webhook handler for subscription renewals
- 8b3e0917 Fix race condition in user session middleware

## shared-libs (1 commits)

- d19c44af Update retry logic in HTTP client wrapper

---
**Total: 3 commits**

Saved to: ~/daily-summaries/2025-03-12.md
```

### `daily-summary --ai` — AI-reviewed

Groups work by theme, explains what was done and why, includes commit refs.

```
$ daily-summary --ai
Analyzing 5 commits with AI...

# Daily Summary — Tuesday, March 11 2025

**TLDR:** Shipped Stripe subscription renewal handling and
fixed session reliability issues across the auth layer.

## What I worked on
- **Payment integration** — Added webhook handler for Stripe
  subscription renewals with idempotency checks and
  invoice event logging. (`f4a21c03`, `c7e512ba`)
- **Auth stability** — Fixed race condition in session middleware
  that caused intermittent 401s under load. (`8b3e0917`)
- **Shared HTTP client** — Updated retry logic with exponential
  backoff and configurable timeout. (`d19c44af`, `a02b8e61`)

## Key changes
- New Stripe webhook endpoint with signature verification
- Session middleware now uses atomic read-write for token refresh
- HTTP client retry defaults changed from 3 to 5 with 2s base delay

---
*AI-reviewed summary of 5 commits*

Saved to: ~/daily-summaries/2025-03-11.md
```

### `daily-summary --week` — Weekly overview

Shows the week at a glance with TLDR per day. Pick a day to drill in.

```
$ daily-summary --week

Week of 2025-03-10:

  1) 2025-03-10 (Mon)  — Set up CI pipeline and added integration test suite
  2) 2025-03-11 (Tue)  — Shipped Stripe subscription renewal handling
  3) 2025-03-12 (Wed)  — Added user notification preferences API
  4) 2025-03-13 (Thu)  — no commits
  5) 2025-03-14 (Fri)  — Fixed email template rendering on mobile
  6) 2025-03-15 (Sat)  — no commits
  7) 2025-03-16 (Sun)  — no commits

Pick a day to view (or Enter to cancel):
```

### `daily-summary --list` — Browse saved summaries

Interactive picker to view any past summary.

```
$ daily-summary --list

Saved summaries:

  1) 2025-03-12  — Daily Summary — Wednesday, March 12 2025
  2) 2025-03-11  — Daily Summary — Tuesday, March 11 2025
  3) 2025-03-10  — Daily Summary — Monday, March 10 2025

Pick a number (or Enter to cancel):
```

### `daily-summary --setup` — First-time setup

```
$ daily-summary --setup

=== daily-summary setup ===

Git author email(s) — comma-separated for multiple people.
Email(s) [you@company.com]:

Enter parent directories containing git repos (one per line, empty line to finish):
Example: ~/Projects
> ~/Projects
  Added: ~/Projects
> ~/Work
  Added: ~/Work
>

Output directory [~/daily-summaries]:

Config saved to ~/.daily-summary.conf
Run 'daily-summary' to test it.
```

## Config

Located at `~/.daily-summary.conf`:

```bash
# Git author email(s) to filter commits
# For multiple authors: AUTHOR="alice@co.com,bob@co.com"
AUTHOR="you@company.com"

# Where to save markdown summaries
OUTPUT_DIR="~/daily-summaries"

# Render markdown in terminal using glow (optional)
USE_GLOW=false

# Parent directories to scan for git repos
SCAN_DIRS=(
  "~/Projects"
  "~/Work"
)
```

| Option | Default | Description |
|--------|---------|-------------|
| `AUTHOR` | — | Git author email(s), comma-separated for teams |
| `OUTPUT_DIR` | `~/daily-summaries` | Where markdown files are saved |
| `USE_GLOW` | `false` | Render markdown with [glow](https://github.com/charmbracelet/glow) |
| `SCAN_DIRS` | — | Parent directories to scan for git repos |

## Noise filtering

These commit patterns are automatically excluded:
- `WIP`, `wip`, `fixup`
- `update plan`, `update tracker`
- Stash entries (`WIP on`, `index on`)

## Requirements

- `git` + `bash`
- **Optional:** [`claude`](https://claude.ai/code) CLI for `--ai` mode
- **Optional:** [`glow`](https://github.com/charmbracelet/glow) for rendered markdown — `brew install glow`
- Works on macOS and Linux

## License

MIT
