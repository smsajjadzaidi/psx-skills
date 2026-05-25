---
name: psx-stock-researcher
description: Deep-dive research tool for any Pakistan Stock Exchange (PSX) listed stock. Use this skill whenever someone asks about a PSX stock, KSE-listed company, or wants to analyze, research, evaluate, or get information about stocks traded on Pakistan's stock market. Triggers on: stock tickers (MEBL, OGDC, SYS etc.), company names (Meezan Bank, Lucky Cement etc.), or requests like "analyze X stock", "should I buy X", "what do you think of X on PSX", "research X for me", "is X Shariah compliant", "what's X's dividend yield". Always use this skill before giving any opinion or data on a PSX-listed company.
---
<!-- Version 1.3.0 — May 2026: Add insider transactions fetching from sarmaaya.pk/stocks/[TICKER]/insider-transactions -->

# PSX Stock Researcher

> **⚙️ First-time setup — avoid permission prompts**
> This skill fetches live data from PSX, news sites, brokers, and macro sources. It requires:
> - `WebFetch` — PSX pages, Sarmaaya, stockanalysis.com, brecorder, SBP
> - `WebSearch` — news, analyst reports, macro queries
>
> To allow these once and never be prompted again, add to your `~/.claude/settings.json`:
> ```json
> "permissions": {
>   "allow": [
>     "WebFetch(*)",
>     "WebSearch(*)"
>   ]
> }
> ```
> Or run `/fewer-permission-prompts` and it will configure this automatically.

A structured, repeatable research framework for analyzing any stock listed on the Pakistan Stock Exchange (PSX). The goal is to give the user an honest, data-backed assessment — not a cheerful summary — so they can make an informed decision aligned with their investment goals.

## Research Process

When triggered, follow these steps in order. Don't skip steps or rely on memory — always fetch fresh data.

### Step 1: Identify the Stock

Extract the ticker symbol and company name from the user's query. If only a company name is given, resolve it to a PSX ticker first by searching `dps.psx.com.pk`. If the ticker is ambiguous or unknown, confirm with the user before proceeding.

### Step 2: Fetch Data from All Sources

Search the following sources in parallel. Read the actual pages — don't rely on search snippets alone, as they are often incomplete.

**Primary Sources (always check):**
- `https://dps.psx.com.pk/company/[TICKER]` — Official PSX page: price, financials, announcements, company profile
- `https://sarmaaya.pk/psx/company/[TICKER]` — Shariah compliance status, ratios, dividend history, technicals
- `https://sarmaaya.pk/stocks/[TICKER]/insider-transactions` — Insider and institutional transactions: holder type, shares held, portfolio %, change %, dates. Flag any significant insider selling patterns.
- `https://stockanalysis.com/quote/psx/[TICKER]/` — Revenue trends, EPS, ROE, income statement
- `https://stockanalysis.com/quote/psx/[TICKER]/financials/` — Full income statement history
- `https://stockanalysis.com/quote/psx/[TICKER]/statistics/` — Key ratios: P/E, debt/equity, current ratio, beta

**Secondary Sources (check when primary sources are insufficient):**
- `https://www.investing.com/equities/[company-slug]` — Technical indicators, 52-week range, analyst sentiment
- `https://scstrade.com/stockscreening/SS_CompanySnapShot.aspx?symbol=[TICKER]` — Snapshot ratios and margins
- Web search for `[TICKER] PSX earnings results 2026` — Recent news, earnings announcements, analyst reports
- Web search for `[TICKER] PSX dividend 2026` — Dividend history if not found above
- Web search for `[TICKER] PSX governance controversy` — Any red flags, SECP actions, related-party issues

### Step 3: Fetch Analyst Intelligence & News

This step captures what Pakistan's leading analysts and financial media are saying about the stock. Run all searches below. Surfaces partial results — be transparent about what was and wasn't found.

**Sarmaaya (primary community source):**
- Fetch `https://sarmaaya.pk/stocks/[TICKER]` — check for any analyst commentary or community notes
- Web search: `"[TICKER]" site:sarmaaya.pk 2026` — articles, ratio analysis, stock writeups
- Web search: `"[TICKER]" "Ammar Yaseen" PSX 2026` — Sarmaaya fundamental analyst
- Web search: `"[TICKER]" "Syed Muhammad Faraz" OR "Faraz" PSX 2026` — Sarmaaya technical analyst (see data-sources.md for key community voices)
- Web search: `"[TICKER]" sarmaaya youtube 2026` — surface any recent Sarmaaya YouTube videos covering this stock

**Brokerage Research (most reliable written sources):**
- Web search: `"[TICKER]" "AKD" OR "AKD Securities" PSX report 2026` — AKD Securities research
- Fetch `https://www.akdsecurities.net/research` — check for recent reports mentioning the ticker
- Web search: `"[TICKER]" "Arif Habib" PSX report 2026` — Arif Habib Limited research
- Fetch `https://www.arifhabibltd.com/research-reports` — check for coverage
- Web search: `"[TICKER]" "Topline Securities" PSX 2026` — Topline research
- Web search: `"[TICKER]" "Ismail Iqbal" OR "Fahad Rauf" PSX 2026` — Ismail Iqbal Securities coverage

**Financial News Media (always check):**
- Web search: `"[TICKER]" PSX 2026 site:profit.com.pk` — Profit by Pakistan Today (best English PSX coverage)
- Web search: `"[TICKER]" PSX 2026 site:mettis.com` — Mettis Global
- Web search: `"[TICKER]" PSX 2026 site:brecorder.com` — Business Recorder
- Web search: `"[TICKER]" PSX 2026 site:thenews.com.pk OR site:dawn.com` — General business news

**Public Social Signals (best-effort, may return nothing):**
- Web search: `"[TICKER]" PSX site:linkedin.com 2026` — analyst posts indexed on Google
- Web search: `"[TICKER]" KSE site:x.com 2026` — public X/Twitter calls

**Macro Context (always fetch — Pakistan markets are macro-driven):**
- Web search: `SBP policy rate Pakistan 2026` — current rate and direction (cutting/holding/hiking)
- Web search: `IMF Pakistan program tranche 2026` — program status, last tranche disbursed
- Web search: `PKR USD exchange rate Pakistan 2026` — currency direction
- Web search: `Pakistan CPI inflation 2026` — inflation level and trend
- Web search: `Pakistan foreign exchange reserves 2026` — SBP reserve level
- Run the sector-specific macro query for this stock's sector (see data-sources.md macro query table)

**Important:** Not all searches will return results. State clearly what was found and what wasn't. Never fabricate analyst opinions. If a brokerage has covered the stock, summarize their view in your own words — never reproduce their exact text.

### Step 4: Analyze Across 7 Dimensions

Structure your analysis around these seven areas. Be honest — if data is missing or contradictory, say so rather than filling gaps with assumptions.

#### 1. Fundamentals
Extract and assess:
- **Valuation**: P/E ratio (compare to sector average), Price/Book, EV/EBITDA
- **Profitability**: EPS (trailing), ROE, net profit margin, EBITDA margin
- **Growth**: Revenue growth YoY (last 2-3 years), earnings trajectory
- **Financial health**: Debt/equity ratio, current ratio, interest coverage
- **Size**: Market cap, average daily volume (liquidity check)

Flag if: P/E is significantly above sector peers, margins are deteriorating, debt is rising faster than earnings, or the stock is illiquid (very low volume).

#### 2. Shariah Compliance
- Check Sarmaaya and PSX KMI index membership explicitly
- State clearly: **Compliant / Non-Compliant / Unable to verify**
- If non-compliant, note the reason if available (e.g., interest-bearing debt above threshold, impermissible business activity)
- Note that compliance status can change with each semi-annual KMI review — always verify before investing

#### 3. Dividend Analysis
- Current dividend yield
- Dividend history: consistent / irregular / none
- Payout ratio (sustainable?)
- Last dividend amount and ex-date
- Note: In Pakistan, dividends are subject to 15% withholding tax. A 10% stated yield becomes ~8.5% after tax. Mention this when yield is a key part of the investment case.

#### 4. Price Momentum
- Current price vs 52-week high and low (where in the range?)
- 1-month, 6-month, 1-year price change
- Performance vs KSE-100 benchmark over same period (outperforming or lagging?)
- Beta (how volatile vs market)
- Note any major price events: stock splits, rights issues, bonus shares

#### 5. Governance & Risk Flags
This is often the most overlooked dimension. Actively check for:
- Any SECP enforcement actions or PSX notices
- Related-party transactions or promoter pledging
- Auditor qualifications or going concern notes
- Reverse mergers or shell company history
- **Insider transactions** (from `sarmaaya.pk/stocks/[TICKER]/insider-transactions`): check holder type breakdown (insider vs institutional vs mutual fund), recent change % in holdings, and whether insiders are reducing positions significantly
- Circular debt exposure (for energy/power sector stocks)

If no flags found, state: "No significant governance concerns identified in available sources."

#### 6. Macro Context

**This dimension determines whether fundamentals matter right now.** A strong stock in a macro headwind can underperform for quarters regardless of earnings quality.

Assess each factor and its net impact on this stock/sector:
- **SBP Policy Rate**: Direction matters more than level. Cutting cycle = tailwind for banks (credit demand), leveraged companies, PSX broadly. Hiking = headwind for same.
- **PKR/USD**: Stable/appreciating = tailwind for importers (auto, pharma, FMCG). Depreciating = tailwind for exporters (textiles, tech) and E&P (USD revenues).
- **IMF Program**: Active, on-track = macro stability signal. Missed review or stalled tranche = risk-off; impacts all PSX.
- **Inflation (CPI)**: High/rising erodes real returns and squeezes consumer spending. Direction more important than level.
- **Foreign Reserves**: Below 2 months import cover = macro stress signal affecting all sectors.
- **Sector-specific**: Cross-reference scoring-rubric.md macro impact table for net tailwind/headwind assessment.

Conclude with: **Macro Verdict for this sector: Tailwind / Neutral / Headwind** — and one sentence why.

#### 7. Portfolio Fit Assessment
Assess how this stock fits into a typical PSX retail investor portfolio:
- Sector classification and how commonly it appears in retail portfolios
- What type of investor it suits: conservative / moderate / aggressive
- Whether it works better as a core holding or a tactical/satellite position
- Correlation with major PSX sectors (does it diversify or overlap with energy, banking, cement?)
- Index memberships: KSE-100, KMI-30, KMI All Share, and what that signals about liquidity and visibility



### Step 5: Produce the Report

Always use this exact structure. Don't skip sections — write "Data unavailable" if needed rather than omitting a section.

---

## [TICKER] — [Full Company Name]
*Sector: [sector] | Market Cap: Rs [X] billion | As of: [date]*

### 📊 Snapshot
| Metric | Value |
|--------|-------|
| Current Price | Rs X |
| 52-Week Range | Rs X – Rs X |
| P/E Ratio | X |
| EPS (TTM) | Rs X |
| Dividend Yield | X% |
| ROE | X% |
| Debt/Equity | X |
| Beta | X |
| Shariah Status | ✅ Compliant / ❌ Non-Compliant / ⚠️ Unverified |

### 🏦 Fundamentals
[2-3 paragraphs covering valuation, profitability, growth, and financial health. Be specific with numbers. Note trends, not just point-in-time values.]

### ☪️ Shariah Status
[Clear statement of compliance status, reason if non-compliant, and reminder to verify before investing as status can change.]

### 💰 Dividend Analysis
[Yield, history, sustainability, after-tax yield calculation if relevant.]

### 📈 Price Momentum
[Where the stock sits in its range, performance vs KSE-100, beta, any notable events.]

### ⚠️ Governance & Risk Flags
[Specific flags found, or "No significant concerns identified." Never leave this blank.]

### 🎯 Portfolio Fit
[Sector role, index memberships, suitable investor profile (conservative/moderate/aggressive), core vs satellite holding, how it diversifies a typical PSX portfolio by sector.]

### 📊 Macro Context
| Factor | Current Status | Impact on [TICKER] |
|--------|---------------|-------------------|
| SBP Policy Rate | X% (cutting / holding / hiking) | [positive / neutral / negative — one line] |
| PKR/USD | Rs X (stable / depreciating / appreciating) | [impact] |
| IMF Program | Active / Stalled / Completed | [impact] |
| Inflation (CPI) | X% YoY ([rising/falling]) | [impact] |
| Foreign Reserves | $X bn (~X months import cover) | [impact] |

**Sector Macro Verdict:** [Tailwind / Neutral / Headwind] — [1-2 sentences explaining why.]

### 📰 News & Analyst Intelligence
*What Pakistan's analysts and financial media are saying — sourced from public content only*

**Brokerage Views:**
[Summarize any AKD, Arif Habib, Topline, or Ismail Iqbal coverage found. If none found, state: "No recent brokerage reports found in public sources." Never fabricate.]

**Sarmaaya / Community:**
[Any public content from Sarmaaya (sarmaaya.pk articles, YouTube video titles). Note: Their detailed calls are inside paid Discord — only public content is captured here.]

**Recent News Headlines:**
[2-4 bullet points of most relevant recent news from Profit.pk, Business Recorder, Mettis Global, Dawn. Include date of each. If none found, state so.]

**Social Signals:**
[Any analyst calls found on LinkedIn or X/Twitter. State clearly if nothing found — this is best-effort only.]

*Note: Social media and paid community content (Discord, WhatsApp groups) cannot be accessed automatically. Cross-check with your own analyst sources before acting.*

### 📋 Verdict
**[BUY / ACCUMULATE / HOLD / REDUCE / AVOID]**

[2-3 sentences explaining the verdict. Weigh both fundamentals AND macro context explicitly. A fundamentally strong stock facing macro headwinds = ACCUMULATE at most, not BUY. State the dominant driver clearly.]

**Key risks to monitor:**
- [Risk 1]
- [Risk 2]
- [Risk 3]

### 🔗 Sources
[List the actual URLs fetched during research]

---

## Important Guidelines

**Be honest about data gaps.** PSX data can be patchy. If a source returns no useful data, say so and note which source was checked. Don't fabricate numbers.

**Recency matters.** Flag if the most recent financial data is more than 6 months old — the picture may have changed. Encourage the user to check the latest quarterly results on PSX.

**This is research, not advice.** Always end with a note that this analysis is for informational purposes and the user should conduct their own due diligence or consult a licensed investment advisor before making decisions.

**Shariah compliance is binary but dynamic.** Don't hedge it. A stock is either on the KMI index or it isn't, as of the last review. Say so clearly.

**Numbers need context.** A P/E of 15x is cheap for a tech company and expensive for a utility. Always compare to sector peers or historical averages, not in isolation.

**Macro can override fundamentals short-term.** Macro context is Dimension 6 — treat it with the same weight as fundamentals. Never give a BUY while noting severe macro headwinds. Minimum downgrade to ACCUMULATE with a timing note.

**Analyst coverage transparency.** Always be explicit about which analysts have covered the stock publicly and which haven't. Don't imply coverage exists if it doesn't. If only Sarmaaya's data page was found (not editorial coverage), say so.

