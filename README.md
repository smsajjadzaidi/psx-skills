# PSX Skills for Claude Code

Two Claude Code skills for Pakistan Stock Exchange (PSX/KSE) investors — stock research and portfolio analysis, powered by live data.

## Skills

### `psx-stock-researcher`
Deep-dive on any PSX-listed stock. Fetches live data from PSX, Sarmaaya, stockanalysis.com, brecorder, and macro sources. Covers fundamentals, Shariah compliance, dividends, price momentum, governance flags, news, and macro context. Produces a structured report with a BUY / ACCUMULATE / HOLD / REDUCE / AVOID verdict.

**Triggers on:** tickers (`MEBL`, `OGDC`, `SYS`), company names (`Meezan Bank`, `Lucky Cement`), or natural questions (`"Should I buy OGDC?"`, `"Is SYS Shariah compliant?"`)

### `psx-portfolio-analyser`
Analyzes your PSX portfolio against your investment goals. Saves your investor profile once (goals, Shariah preference, risk tolerance, time horizon) and reuses it every session. Accepts free-form portfolio input, fetches every holding from PSX, and produces a sector breakdown, goal alignment check, macro sensitivity assessment, and gap analysis.

**Triggers on:** `"Analyze my portfolio"`, `"How is my portfolio doing?"`, `"What sectors am I missing?"`

---

## Installation

### CLI (Claude Code terminal)

**Step 1 — Add this repo as a marketplace:**
```bash
/plugin marketplace add smsajjadzaidi/psx-skills
```

**Step 2 — Install the plugin:**
```bash
/plugin install psx-skills@psx-skills
```

That's it. Both skills are available immediately.

### Desktop app

1. Open **Customize → Add plugin** (the plugins/marketplace panel).
2. Paste the repo URL: `https://github.com/smsajjadzaidi/psx-skills`
3. Click **Sync**, then install **psx-skills**.

Restart the session after installing.

---

## Updating

Skills ship with the plugin, so new versions arrive when the plugin updates.

**Recommended — turn on auto-update once, then it's hands-off.** Claude Code auto-updates plugins at startup, but for third-party marketplaces this is OFF by default. Enable it per user via `/plugin` → Marketplaces → `psx-skills` → enable auto-update, or org-wide via managed `settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "psx-skills": {
      "source": { "source": "github", "repo": "smsajjadzaidi/psx-skills" },
      "autoUpdate": true
    }
  }
}
```

**Manual update** (if you'd rather not auto-update):
```bash
/plugin marketplace update psx-skills
/reload-plugins
```

---

## First-Time Setup (skip permission prompts)

The skills fetch live data from PSX and news sources. Add these permissions to `~/.claude/settings.json` once to avoid being prompted on every run:

```json
{
  "permissions": {
    "allow": [
      "WebFetch(*)",
      "WebSearch(*)",
      "Read(~/.claude/memory/*)",
      "Write(~/.claude/memory/*)"
    ]
  }
}
```

Or run `/fewer-permission-prompts` after install and it configures this automatically.

---

## Usage Examples

**Stock research:**
```
Analyze MEBL
Should I buy OGDC?
Is SYS Shariah compliant?
What's FFC's dividend yield?
Research Lucky Cement for me
```

**Portfolio analysis:**
```
Analyze my PSX portfolio
How is my portfolio doing?
What sectors am I missing?
Review my holdings: MEBL, OGDC, SYS, FFC
```

Portfolio input can be tickers, tickers + shares, percentages, or plain text — the skill parses whatever you give it.

---

## Manual Install (no plugin system)

**Option A — Download `.skill` files (easiest):**

1. Download from this repo:
   - [`psx-stock-researcher.skill`](https://github.com/smsajjadzaidi/psx-skills/raw/main/psx-stock-researcher.skill)
   - [`psx-portfolio-analyser.skill`](https://github.com/smsajjadzaidi/psx-skills/raw/main/psx-portfolio-analyser.skill)
2. Move both files to `~/.claude/skills/`
3. Restart Claude Code

**Option B — Clone and copy:**

```bash
git clone https://github.com/smsajjadzaidi/psx-skills.git
cp -r psx-skills/skills/psx-stock-researcher ~/.claude/skills/
cp -r psx-skills/skills/psx-portfolio-analyser ~/.claude/skills/
```

---

## License

MIT — see [LICENSE](LICENSE)
