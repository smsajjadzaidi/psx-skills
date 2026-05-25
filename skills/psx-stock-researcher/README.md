# psx-stock-researcher

Deep-dive research skill for any Pakistan Stock Exchange (PSX/KSE) listed stock. Fetches live data, runs a 7-dimension analysis, and produces a structured report with a verdict.

## What It Does

Given any PSX ticker or company name, it:
- Fetches live data from PSX, Sarmaaya, stockanalysis.com, SCS Trade
- Searches brecorder, Profit.pk, Mettis, Dawn for recent news
- Searches AKD, Arif Habib, Topline for broker research
- Checks macro context: SBP rate, PKR/USD, IMF program, CPI, sector headwinds
- Checks Shariah compliance (KMI index membership)
- Produces a report: Snapshot → Fundamentals → Shariah → Dividends → Momentum → Governance → Macro → Verdict

**Verdict options:** BUY / ACCUMULATE / HOLD / REDUCE / AVOID

## Installation

**Option 1 — Install as plugin (recommended):**
```bash
/plugin marketplace add smsajjadzaidi/psx-skills
/plugin install psx-skills@smsajjadzaidi/psx-skills
```

**Option 2 — Manual install:**
```bash
# Copy skill to your Claude Code skills directory
cp -r psx-stock-researcher ~/.claude/skills/
```

## Permissions Required

Add to `~/.claude/settings.json` to avoid permission prompts:
```json
"permissions": {
  "allow": [
    "WebFetch(*)",
    "WebSearch(*)"
  ]
}
```

## Usage

Just mention a PSX stock naturally — the skill triggers automatically:

- "Analyze MEBL"
- "Should I buy OGDC?"
- "Is SYS Shariah compliant?"
- "What's FFC's dividend yield?"
- "Research Lucky Cement for me"

## Source

GitHub: https://github.com/smsajjadzaidi/psx-skills
