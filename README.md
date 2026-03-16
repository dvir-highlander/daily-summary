# daily-summary

A CLI tool that summarizes your daily git commits across multiple repositories. Optionally uses AI (Claude Code) to generate human-readable summaries from your actual code changes.

## Install

```bash
# Clone and install
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

## Setup

```bash
daily-summary --setup
```

The wizard will ask for:
- **Git author email(s)** — comma-separated for teams (defaults to `git config user.email`)
- **Directories to scan** — parent folders containing git repos (e.g., `~/Projects`)
- **Output directory** — where to save markdown summaries (defaults to `~/daily-summaries`)

Config is saved to `~/.daily-summary.conf`.

## Usage

```bash
# Generate summaries
daily-summary              # yesterday (raw commit list)
daily-summary today        # today (raw commit list)
daily-summary 2025-03-12   # specific date

# AI-reviewed summaries (requires Claude Code)
daily-summary --ai         # yesterday — grouped, summarized, with commit refs
daily-summary --ai today   # today

# Browse and manage
daily-summary --list       # interactive picker for saved summaries
daily-summary --week       # this week overview (Mon-Sun)
daily-summary --week last  # last week overview
daily-summary --open       # open summaries folder in Finder/file manager

# Re-generate
daily-summary --refresh today      # force re-scan (overwrites cached file)
daily-summary --ai 2025-03-12      # regenerate with AI for a past date
```

## Modes

### Raw mode (default)

Lists commit messages grouped by repo. Instant, no dependencies beyond git.

### AI mode (`--ai`)

Sends commits and file-level diffs to Claude Code for analysis. Returns:

- **TLDR** — one-line summary of the day's focus
- **What I worked on** — themes grouped by purpose, with commit hashes inline
- **Key changes** — important technical details

```
$ daily-summary --ai today

# Daily Summary — Monday, March 16 2026

**TLDR:** Extended map marker system with pilot location markers and UTM shape filtering.

## What I worked on
- **Drone marker rendering** — Built SVG-based markers with state detection (`a19546d8`, `cdb33e83`)
- **UTM geo-layer expansion** — Added org-level exclusion filtering (`481e679d`)

## Key changes
- New drone marker service with Figma-accurate SVG icon generation
- State-level not-reporting detection for offline drones
- Geo-layer manager supports org-level exclusion filtering
```

### Week view (`--week`)

Shows a Mon-Sun overview with TLDR for each day. Pick a day to view the full summary.

```
$ daily-summary --week

Week of 2025-03-10:

  1) 2025-03-10 (Mon)  — Refactored auth middleware and added session validation
  2) 2025-03-11 (Tue)  — Implemented drone command service phases 2-3
  3) 2025-03-12 (Wed)  — Added mission browse screen and filter UI
  4) 2025-03-13 (Thu)  — no commits
  5) 2025-03-14 (Fri)  — Fixed mapping boundary rendering bugs
  6) 2025-03-15 (Sat)  — no commits
  7) 2025-03-16 (Sun)  — no commits

Pick a day to view (or Enter to cancel):
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

- `git`
- `bash`
- **Optional:** `claude` CLI for `--ai` mode — [Claude Code](https://claude.ai/code)
- **Optional:** `glow` for rendered markdown — `brew install glow`
- Works on macOS and Linux

## License

MIT
