---
name: psx-portfolio-analyser
description: Use when someone wants to analyze their PSX/KSE stock portfolio, check portfolio health, review sector allocation, assess against investment goals, or asks "how is my portfolio doing", "is my portfolio balanced", "what am I missing", "review my holdings". Always use this skill before analyzing any collection of PSX stocks as a portfolio.
---

# PSX Portfolio Analyser

> **⚙️ First-time setup — avoid permission prompts**
> This skill fetches live data from PSX, news sites, and macro sources. It requires:
> - `WebFetch` — reading PSX pages, broker sites, SBP, Sarmaaya
> - `WebSearch` — macro queries, news, analyst research
> - `Read` / `Write` — saving your investor profile to `~/.claude/memory/`
>
> To allow these once and never be prompted again, add to your `~/.claude/settings.json`:
> ```json
> "permissions": {
>   "allow": [
>     "WebFetch(*)",
>     "WebSearch(*)",
>     "Read(~/.claude/memory/*)"
>   ]
> }
> ```
> Or run `/fewer-permission-prompts` and it will configure this automatically.

Analyzes a PSX investor's portfolio against their personal goals. Two-phase skill: first establish (or recall) the investor profile, then analyze the portfolio against it.

**Pakistan context:** PSX portfolios are heavily concentrated in banks and energy by default. A "diversified" PSX portfolio often isn't. Macro sensitivity, Shariah compliance, and circular debt exposure are common blind spots.

---

## Phase 1: Investor Profile

### Check for Saved Profile

Look for a saved investor profile at `~/.claude/memory/psx-investor-profile.md`. If it exists, read it and confirm with the user:

> "I have your saved investor profile: [summarize key points]. Should I use this, or update it?"

If confirmed, skip to Phase 2. If updating or no profile exists, collect the profile now.

### Collect Profile (if needed)

Ask the user these questions — all at once, not one at a time:

---
**To analyze your portfolio properly, I need to understand your goals. Please answer:**

1. **Investment goal** — what's the primary objective?
   - Income (dividends, steady cash flow)
   - Growth (capital appreciation)
   - Balanced (mix of both)
   - Capital preservation (protect value, low risk)

2. **Shariah compliance** — must all holdings be KMI-compliant? (yes / no / preferred but not required)

3. **Risk tolerance** — how do you think about risk?
   - Conservative (stability over returns, hate drawdowns)
   - Moderate (accept some volatility for better returns)
   - Aggressive (comfortable with high volatility for high upside)

4. **Time horizon** — when might you need this money?
   - Short-term (< 2 years)
   - Medium-term (2–5 years)
   - Long-term (5+ years)

5. **Income need** — do you rely on dividend income, or is it a bonus?
   - Yes, need regular cash flow
   - No, reinvesting everything

6. **Sectors to avoid** — any sectors you won't invest in for any reason (ethical, regulatory, personal)?

---

Wait for user's answers before proceeding.

### Save the Profile

Once collected, save to `~/.claude/memory/psx-investor-profile.md`:

```markdown
# PSX Investor Profile
*Last updated: [date]*

## Goals & Preferences
- **Primary goal:** [income / growth / balanced / preservation]
- **Shariah compliance:** [required / preferred / not required]
- **Risk tolerance:** [conservative / moderate / aggressive]
- **Time horizon:** [short / medium / long]
- **Income need:** [yes / no]
- **Sectors to avoid:** [list or "none"]

## Derived Targets
- **Target dividend yield:** [based on goal + income need]
- **Max single-stock concentration:** [based on risk tolerance: conservative=15%, moderate=25%, aggressive=35%]
- **Max single-sector concentration:** [conservative=25%, moderate=35%, aggressive=50%]
- **Beta target:** [conservative <0.8, moderate 0.8–1.2, aggressive >1.2 acceptable]
```

Confirm to the user: "Profile saved. I'll use this for all future portfolio analyses."

---

## Phase 2: Portfolio Input

Ask the user to share their holdings in any format they prefer:

> "Share your portfolio — any format works. Examples:
> - 'MEBL, OGDC, SYS, FFC' (just tickers)
> - 'MEBL 500 shares, OGDC 1000 shares'
> - 'MEBL 40%, OGDC 30%, SYS 30%'
> - Or just describe it in plain text"

Parse whatever they provide. Extract:
- Tickers (resolve company names to tickers if needed)
- Approximate weights (if shares/amounts given, estimate relative weights; if just tickers, treat as equal-weight for analysis)
- Note any ambiguities and confirm with user before proceeding

---

## Phase 3: Portfolio Analysis

Analyze across these dimensions. Reference the saved investor profile throughout — every observation should tie back to their stated goals.

### 1. Holdings Identification & Validation

**For every ticker in the portfolio, fetch its PSX page — no exceptions:**
```
https://dps.psx.com.pk/company/[TICKER]
```
Extract: full company name, sector, market cap, index memberships (KSE-100, KMI-30, KMI All Share).

Do NOT skip this step or rely on memory for sector classification. PSX pages load fast and are the authoritative source. Unknown tickers, obscure small-caps, REITs, ETFs — fetch all of them.

After fetching, build the holdings table:
- Full company name
- Sector (from PSX page)
- KMI/Shariah status (from index membership shown on PSX page)
- Current P&L from user's data

Flag any holdings that conflict with stated preferences (e.g., Shariah-prohibited sector like tobacco/alcohol/conventional banking if compliance is required or preferred, or any sector the user said to avoid).

### 2. Sector Allocation
Build a sector breakdown:

| Sector | Holdings | Weight (est.) | PSX Benchmark % | vs Benchmark |
|--------|----------|--------------|-----------------|--------------|
| Banks | ... | X% | ~35% | over/under |
| Oil & Gas | ... | X% | ~20% | |
| Fertilizer | ... | X% | ~8% | |
| Cement | ... | X% | ~7% | |
| Technology | ... | X% | ~5% | |
| [etc.] | | | | |

PSX KSE-100 approximate sector weights (2025-2026):
- Banks (conventional + Islamic): ~35%
- Oil & Gas E&P: ~20%
- Fertilizer: ~8%
- Cement: ~7%
- Power/IPPs: ~6%
- Technology: ~5%
- Auto: ~4%
- Pharma: ~3%
- FMCG/Consumer: ~3%
- Other: ~9%

Flag: sectors >1.5× benchmark weight (overweight) and sectors with zero exposure that are relevant to their goals.

### 3. Concentration Risk
- Largest single holding weight
- Top 3 holdings combined weight
- Flag if any single stock > their max concentration threshold (from profile)
- Flag if top 3 > 60% of portfolio

### 4. Goal Alignment Check

| Goal Dimension | Target (from profile) | Actual | Gap |
|---------------|----------------------|--------|-----|
| Est. portfolio yield | X% | X% | +/- |
| Avg beta | [target] | X | +/- |
| Shariah compliant % | 100% / N/A | X% | — |
| Dividend payers % | High if income-focused | X% | — |

### 5. Missing Sectors / Gaps

Based on their goals, identify what's missing:

- **Income-focused:** Are there high-yield sectors missing? (Banks, fertilizer, E&P all pay strong dividends)
- **Growth-focused:** Any tech, consumer, or emerging sector exposure?
- **Balanced:** Is there a mix of cyclical and defensive?
- **Capital preservation:** Any defensive holdings? (Pharma, FMCG tend to be lower beta)
- **Shariah-required:** Any non-compliant holdings that need replacing?

List specific missing sectors and suggest 1-2 representative tickers for each gap (no deep analysis — just flag for further research).

### 6. Macro Sensitivity Assessment
Given current Pakistan macro environment, assess portfolio-level exposure:

- **SBP rate sensitivity:** What % of portfolio is in rate-sensitive sectors (banks, leveraged cos)?
- **PKR sensitivity:** Net importer vs exporter exposure
- **Circular debt exposure:** Any IPP/E&P holdings with circular debt risk?
- **IMF-sensitive names:** Holdings that would be most affected by macro instability

State: is this portfolio positioned well for the current macro environment?

### 7. Correlation & Overlap
- Are multiple holdings moving together? (e.g., OGDC + PPL + MARI = triple E&P exposure)
- Flag if >2 stocks in same sub-sector
- Suggest which to consolidate if redundant

---

## Phase 4: Report

### 📊 Portfolio Summary
*[X] holdings | Est. yield: X% | Avg beta: X | [X]% Shariah compliant | As of: [date]*

### 🗂️ Sector Breakdown
[Table from analysis]

**Overweight:** [sectors]
**Underweight:** [sectors]
**Missing:** [sectors relevant to stated goals]

### 🎯 Goal Alignment
[Table from analysis — green/amber/red each dimension]

### ⚠️ Risk Flags
- [Concentration issues]
- [Goal conflicts]
- [Macro exposures]
- [Shariah conflicts if applicable]

### 🔍 Gaps & Recommendations
[What's missing by goal type, with 1-2 suggested tickers per gap — flagged for further research with psx-stock-researcher, not a buy recommendation]

### 📰 Macro Fit
[Is this portfolio well-positioned for current SBP rate cycle, PKR trend, IMF situation?]

### 📋 Overall Assessment
**[Well-balanced / Concentrated / Needs rebalancing / Misaligned with goals]**

[3-4 sentences: what the portfolio does well, what the biggest gap is, one concrete action to consider.]

---

## Important Guidelines

**Never make buy/sell recommendations for specific stocks.** Flag gaps and suggest candidates for further research — always direct to psx-stock-researcher for individual stock deep-dives.

**Equal-weight assumption.** If user doesn't provide weights, state clearly that analysis assumes equal weighting and results may differ from actual portfolio.

**Profile is a starting point.** Goals change. Remind user they can update their profile anytime by saying "update my investor profile."

**This is analysis, not advice.** End every report with: *This analysis is for informational purposes only. Consult a licensed investment advisor before making portfolio decisions.*
