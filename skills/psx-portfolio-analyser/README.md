# psx-portfolio-analyser

Portfolio analysis skill for Pakistan Stock Exchange (PSX/KSE) investors. Saves your investor profile once, then analyzes any portfolio against your goals — sector allocation, Shariah compliance, income yield, macro sensitivity, and gaps.

## What It Does

- **First run:** Asks for your goals (growth/income/balanced, Shariah preference, risk tolerance, time horizon) and saves them to `~/.claude/memory/psx-investor-profile.md`
- **Every run:** Recalls your saved profile, accepts free-form portfolio input, fetches every holding from PSX, and produces a full analysis

**Analysis covers:**
- Holdings identification (fetches `dps.psx.com.pk` for every ticker — no guessing)
- Sector breakdown vs KSE-100 benchmark
- Concentration risk
- Goal alignment (yield target, beta, Shariah %)
- Missing sectors by goal type
- Macro sensitivity (SBP rate, PKR, circular debt exposure)
- Correlation and overlap flags

## Installation

**Option 1 — Install as plugin (recommended):**
```bash
/plugin marketplace add smsajjadzaidi/psx-skills
/plugin install psx-skills@smsajjadzaidi/psx-skills
```

**Option 2 — Manual install:**
```bash
cp -r psx-portfolio-analyser ~/.claude/skills/
```

## Permissions Required

Add to `~/.claude/settings.json` to avoid permission prompts:
```json
"permissions": {
  "allow": [
    "WebFetch(*)",
    "WebSearch(*)",
    "Read(~/.claude/memory/*)",
    "Write(~/.claude/memory/*)"
  ]
}
```

## Usage

Trigger naturally — the skill detects portfolio-related requests:

- "Analyze my PSX portfolio"
- "How is my portfolio doing?"
- "Is my portfolio balanced?"
- "What sectors am I missing?"
- "Review my holdings: MEBL, OGDC, SYS, FFC"

**Portfolio input formats all work:**
- Just tickers: `MEBL, OGDC, SYS`
- With shares: `MEBL 300 shares, OGDC 1000 shares`
- With weights: `MEBL 40%, OGDC 30%, SYS 30%`
- Plain text: "I hold Meezan Bank and Lucky Cement"

**Update your profile anytime:**
- "Update my investor profile"

## Source

GitHub: https://github.com/smsajjadzaidi/psx-skills
