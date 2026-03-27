# Analysis of Competing Hypotheses: Impact of Mobile Gaming on Gaming Marketplace Growth

> **Analysis ID**: 2026-03-26-mobile-gaming-marketplace-growth-impact | **Date**: 2026-03-26 | **Phase**: Diagnostic
> **Evidence base**: 62 items | [Full registry](../evidence-registry.md)
> **Iteration**: 3 | Prior version: [working/ach-matrix.v2.md](ach-matrix.v2.md)
> **Trigger**: User-requested iteration addressing revenue-as-proxy operationalization, compound interaction modeling, and structural fragility direct testing. This is iteration 3. New evidence E56-E62. Triggered by: User-requested iteration addressing revenue-as-proxy operationalization, compound interaction modeling, and structural fragility direct testing.

---

## Problem Statement

What best explains mobile gaming's observed relationship with the size, composition, demographics, and competitive dynamics of the global gaming marketplace -- and what is the most accurate characterization of its current trajectory?

---

## Hypotheses

| ID | Hypothesis | Source / Basis |
|----|-----------|----------------|
| H1 | Mobile gaming complementarily expanded the total gaming market by bringing in new demographics and geographies, and its current slowdown is a normal maturation phase within an overall healthy, larger market | Academic evidence [E36, E37], historical pattern [E39], market data [E02, E06] |
| H2 | Mobile gaming's apparent market expansion is largely an artifact of revenue metrics; its true impact is redistributive and extractive, having degraded game quality and developer sustainability while concentrating revenue in exploitative monetization of a tiny payer base | Monetization critique [E11, E12, E13], contraction signals [E18, E19, E20], payer concentration [E29], engagement data [E56, E57] |
| H3 | Mobile gaming expanded the market historically, but platform convergence and cloud gaming are now dissolving mobile as a distinct segment | BCG convergence [E38, E40], cloud projections [E05], cross-platform [E43] |
| H4 | Mobile gaming's stagnation in mature markets masks continued strong expansion in emerging markets, and the global picture is bifurcated | Emerging market data [E30, E32, E33], regional divergence [E09, E31, E61] |
| H5 | Null hypothesis: Mobile gaming's growth was primarily a function of smartphone hardware ubiquity rather than any distinctive feature of mobile gaming itself | Hardware ubiquity [E27, E33]; F2P-as-coercion [E13] |

---

## Evidence Inventory

### Revenue Evidence (v1/v2 base)

| ID | Evidence | Source | Reliability | Citation |
|----|----------|--------|-------------|----------|
| E02 | Global games market $188.8B; mobile 55% but slowing | Newzoo | High | [E02] |
| E06 | Console CAGR > mobile CAGR | Newzoo | High | [E06] |
| E12 | <0.5% fund 2/3 of revenue | Mother Jones | High | [E12] |
| E14 | Industry recovering from slump | BCG | High | [E14] |
| E16 | Mobile +1%, PC/console +13% | GamesIndustry.biz | High | [E16] |
| E18 | Sony shut mobile studio | GamesIndustry.biz | High | [E18] |
| E19 | Epic 1,000+ layoffs | GamesIndustry.biz | High | [E19] |
| E20 | UK mobile employment -12.9% | GamesIndustry.biz | High | [E20] |
| E30 | Emerging markets driving growth | Global Games Forum | Medium | [E30] |
| E32 | MENA 18% YoY growth | Global Games Forum | Medium | [E32] |
| E36 | Academic: mobile complements traditional gaming | Entertainment Computing | High | [E36] |
| E37 | Multihoming strengthens console sales | JAMS | High | [E37] |
| E38 | Platform convergence dissolving boundaries | BCG | High | [E38] |
| E39 | 50-year platform expansion pattern | WEF | High | [E39] |
| E43 | Multi-platform 570% more playtime | Naavik | Medium | [E43] |

### Engagement/Welfare Evidence (v3 non-revenue additions)

| ID | Evidence | Source | Reliability | Citation |
|----|----------|--------|-------------|----------|
| E45 | D30 retention only 2.6-5% | Business of Apps | High | [E45] |
| E46 | Player guilt, regret, feeling tricked | Gibson et al. | High | [E46] |
| E47 | Loot boxes mediate gambling disorder | JMIR | High | [E47] |
| E56 | D1 retention declining YoY (28-29% to 26-28%); D7 declining; 75% below 3% D28 | GameAnalytics | High | [E56] |
| E57 | Top 50 capture 80% of growth; rest declined; "optimization and extraction, not growth" | DoF/Sensor Tower | High | [E57] |
| E58 | ATT broke performance marketing feedback loop permanently; disruptions compound | Mobile Dev Memo | High | [E58] |
| E59 | Non-game apps overtook games in spending ($85B vs $81.8B) | TechCrunch/Sensor Tower | High | [E59] |
| E60 | 45K jobs lost; 26% EU devs laid off; 30+ studios shut; salaries fell 50% | Wikipedia (sourced) | High | [E60] |
| E62 | 9 premium cleared 10M downloads vs 4 F2P breakouts | DoF/Sensor Tower | High | [E62] |

**Negative evidence:**

| ID | Absence | Expected Under | Significance |
|----|---------|----------------|--------------|
| NE1 | No console/PC revenue decline proportional to mobile's rise | H2 | Traditional should have shrunk if redistributive |
| NE3 | No cloud gaming displacing mobile | H3 | Cloud <1% of market |
| NE5 (NEW) | No retention stabilizing or recovering | H1 | "Normal maturation" predicts plateau, not decline |

---

## Diagnosticity Matrix

### Revenue-weighted assessment

| Evidence | H1 | H2 | H3 | H4 | H5 | Diagnostic Value |
|----------|:--:|:--:|:--:|:--:|:--:|:--:|
| E02: Mobile 55% but slowing | C | N | C | C | C | ZERO |
| E06: Console CAGR > mobile | C | C | C | C | N | LOW |
| E12: <0.5% fund 2/3 revenue | N | C | N | N | N | HIGH |
| E14: Industry recovering | C | I | N | C | N | HIGH |
| E16: Mobile +1%, PC/console +13% | C | C | C | C | N | LOW |
| E36: Academic complementarity | C | I | N | N | I | HIGH |
| E37: Multihoming strengthens console | C | I | N | N | I | HIGH |
| E39: 50-year expansion pattern | C | I | N | N | N | HIGH |
| NE1: No proportional decline | C | I | N | N | N | HIGH |

### Engagement/welfare-weighted assessment (v3)

| Evidence | H1 | H2 | H3 | H4 | H5 | Diagnostic Value |
|----------|:--:|:--:|:--:|:--:|:--:|:--:|
| E45: D30 retention 2.6-5% | I | C | N | N | N | HIGH |
| E46: Player guilt and regret | N | C | N | N | N | HIGH |
| E47: Loot box gambling mediation | N | C | N | N | N | HIGH |
| E56: Retention declining YoY | I | C | N | C | N | HIGH |
| E57: Top 50 = 80% of growth | I | C | N | N | N | HIGH |
| E58: ATT broke UA permanently | I | C | N | N | N | HIGH |
| E59: Non-game apps overtook games | I | C | C | N | N | HIGH |
| E60: 45K jobs, 26% EU devs laid off | I | C | N | I | N | HIGH |
| E62: Premium outperforming F2P | I | C | C | N | C | HIGH |
| NE5: No retention stabilization | I | C | N | N | N | HIGH |

### Legend

| Rating | Meaning |
|--------|---------|
| **C** | Consistent -- evidence supports this hypothesis |
| **I** | Inconsistent -- evidence contradicts this hypothesis |
| **N** | Neutral -- evidence neither supports nor contradicts |

---

## Inconsistency Tally

### Revenue-only (v2 approach)

| Hypothesis | Inconsistent Items | Score | Rank |
|------------|-------------------|-------|------|
| H1 | E35 | 1 | 1 (MOST PLAUSIBLE) |
| H4 | E18, E20 | 2 | 2 |
| H5 | E36, E37 | 2 | 2 (tied) |
| H3 | NE3, E05 | 2 | 2 (tied) |
| H2 | E14, E36, E37, E39, NE1 | 5 | 5 |

### Engagement-only (v3 addition)

| Hypothesis | Inconsistent Items | Score | Rank |
|------------|-------------------|-------|------|
| H2 | (none) | 0 | 1 |
| H3 | (none) | 0 | 1 (tied) |
| H5 | (none) | 0 | 1 (tied) |
| H4 | E60 | 1 | 2 |
| H1 | E45, E56, E57, E58, E59, E60, E62, NE5 | 8 | 5 |

### Combined (equal weighting)

| Hypothesis | Revenue I-score | Engagement I-score | Combined | Rank |
|------------|----------------|-------------------|----------|------|
| H4 | 2 | 1 | 3 | 1 (MOST PLAUSIBLE) |
| H5 | 2 | 0 | 2 | 2 |
| H3 | 2 | 0 | 2 | 2 (tied) |
| H2 | 5 | 0 | 5 | 3 |
| H1 | 1 | 8 | 9 | 5 (LEAST PLAUSIBLE) |

**Critical v3 finding**: When engagement evidence is weighted equally with revenue evidence, **H1 drops from most plausible (rank 1) to least plausible (rank 5)**. This is the direct consequence of operationalizing the revenue-as-proxy diagnosis. The revenue-only picture systematically favored H1 by filtering out evidence domains where H1 performs worst.

The most analytically honest interpretation is that no single hypothesis captures the full picture. The evidence points to a **composite**: mobile gaming historically expanded the market (H1 correct about past) through a structurally fragile model (H2 correct about current dynamics) that is now bifurcating by geography (H4) while the platform category dissolves (H3).

---

## Sensitivity Analysis

| Critical Evidence | If Removed / Reinterpreted | Impact on Conclusion |
|-------------------|----------------------------|----------------------|
| E56 (Retention declining) | If decline is mix shift or measurement artifact | H1 regains plausibility; but 11,600 games across 1.48B MAU makes artifact unlikely |
| E57 (Top 50 concentration) | If normal for mature entertainment (cf. music, film) | H1's "maturation" framing strengthens considerably; strongest counter-argument |
| E58 (ATT permanent) | If ad networks fully adapted through contextual targeting | Compound system model weakens; ATT becomes historical not ongoing |
| E59 (Apps overtook games) | If AI spending is a bubble that reverts | Mobile gaming's loss of position temporary; structural concern reduced |
| E36, E37 (Complementarity) | If outdated or inapplicable post-2023 | H1 loses strongest foundation; H2 gains |

---

## Preliminary Findings

- **Most plausible under equal weighting**: H4 (Geographic bifurcation) -- replaces H1 from v1/v2
- **Most accurate characterization**: Composite explanation across multiple hypotheses
- **Confidence**: **Low** (downgraded from Moderate in v2)
- **Rationale**: H1 was most plausible under revenue-only assessment. Under equal weighting of engagement evidence, H1 accumulates the most inconsistencies. The revenue-only picture systematically favored H1 by excluding evidence domains where it performs worst. Confidence is Low because no single hypothesis captures the situation, the evidence contains internal contradictions (revenue stable, engagement declining), and forward trajectory depends on uncertain compound dynamics.
- **Key discriminating evidence**: E56 (retention declining YoY) and E57 (top-50 concentration, rest declining) are the most powerful new discriminators.

---

## Future Indicators

| Indicator | Would Confirm | Would Refute | Observable By |
|-----------|---------------|--------------|---------------|
| D1/D7 retention stabilizes or recovers | H1 | H2 | Q4 2026 |
| Revenue growth outside top 50 resumes | H1 | H2 | Q2 2027 |
| Non-game apps maintain spending lead | H2, H3 | H1 | Q4 2026 |
| Emerging market mobile >15% for 2 years | H4 | H2 | 2027 |
| Premium mobile game >$500M revenue | Refutes F2P lock-in | H2 monopoly | 2027-2028 |

---

## Sources & Citations

| Ref | Source | Retrieved | Reliability | Method |
|-----|--------|-----------|-------------|--------|
| [E02] | [Newzoo: $188.8B](https://newzoo.com/resources/blog/global-games-market-to-hit-189-billion-in-2025) | 2026-03-26 | High | OSINT |
| [E06] | [Newzoo: CAGR](https://newzoo.com/resources/blog/global-games-market-to-hit-189-billion-in-2025) | 2026-03-26 | High | OSINT |
| [E12] | [Mother Jones: Whales](https://www.motherjones.com/media/2023/04/asphalt-video-games-microtransactions-loot-boxes-in-game-purchases-capitalism/) | 2026-03-26 | High | OSINT |
| [E14] | [BCG: Recovery](https://www.bcg.com/publications/2025/video-gaming-report-2026-next-era-of-growth) | 2026-03-26 | High | OSINT |
| [E16] | [GamesIndustry.biz: Mobile +1%](https://www.gamesindustry.biz/mobile-revenue-remained-flat-across-2025-but-pc-gaming-sees-another-record-year-sensor-tower-state-of-gaming-2026) | 2026-03-26 | High | OSINT |
| [E36] | [Entertainment Computing: Complementarity](https://www.sciencedirect.com/science/article/pii/S1875952121000422) | 2026-03-26 | High | OSINT |
| [E37] | [JAMS: Multihoming](https://link.springer.com/article/10.1007/s11747-022-00893-4) | 2026-03-26 | High | OSINT |
| [E39] | [WEF: 50-year history](https://www.weforum.org/stories/2020/11/gaming-games-consels-xbox-play-station-fun/) | 2026-03-26 | High | OSINT |
| [E45] | [Business of Apps: Retention](https://www.businessofapps.com/data/mobile-game-retention-rates/) | 2026-03-26 | High | OSINT |
| [E46] | [Gibson et al.: Player experiences](https://www.sciencedirect.com/science/article/pii/S0747563223001176) | 2026-03-26 | High | OSINT |
| [E47] | [JMIR: Loot boxes](https://games.jmir.org/2024/1/e57304/) | 2026-03-26 | High | OSINT |
| [E56] | [GameAnalytics: Benchmarks](https://investgame.net/wp-content/uploads/2025/02/2025-GameAnalytics-Mobile-Gaming-Benchmarks.pdf) | 2026-03-26 | High | OSINT |
| [E57] | [DoF/Sensor Tower: Seven Things](https://www.deconstructoroffun.com/blog/seven-things-the-2025-state-of-gaming-report-actually-tells-us) | 2026-03-26 | High | OSINT |
| [E58] | [Mobile Dev Memo: ATT Recession](https://mobiledevmemo.com/the-att-recession/) | 2026-03-26 | High | OSINT |
| [E59] | [TechCrunch: Apps overtook games](https://techcrunch.com/2026/01/21/consumers-spent-more-on-mobile-apps-than-games-in-2025-driven-by-ai-app-adoption/) | 2026-03-26 | High | OSINT |
| [E60] | [Wikipedia: Layoffs](https://en.wikipedia.org/wiki/2022%E2%80%932026_video_game_industry_layoffs) | 2026-03-26 | High | OSINT |
| [E62] | [DoF/Sensor Tower: Premium vs F2P](https://www.deconstructoroffun.com/blog/seven-things-the-2025-state-of-gaming-report-actually-tells-us) | 2026-03-26 | High | OSINT |
| [assumptions.md] | [KAC v3](assumptions.md) | 2026-03-26 | High | ANALYSIS |

**Citation Methods:**
- **OSINT**: Retrieved via Firecrawl MCP or WebSearch/WebFetch fallback
- **ANALYSIS**: Analytical judgment produced via named technique protocol

> Every claim must be cited. Uncited claims are not permitted.

---

*Generated by Structured Analysis Skill | ACH Protocol | Iteration 3 | 2026-03-26*
