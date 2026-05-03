# Egypt Food Crisis Analysis
### Currency Devaluation, Market Structure & Food Security Outcomes (2010–2024)

> **C-DE211: Data Analysis — Project 3**  
> Faculty of Computing & Information Sciences, Egypt University of Informatics (EUI) · Spring 2026

---

## Overview

This project investigates how Egypt's two major currency devaluations — November 2016 and the 2022 series — transmitted into retail food prices and ultimately into food security deterioration for 105 million people. Using 16,234 monthly WFP price records, WFP market metadata, and FAO annual food security indicators, it tests three formal hypotheses and builds a predictive regression model.

**The short answer:** Egypt's food crisis is fundamentally a currency problem. When the pound falls, the cost of every imported calorie rises immediately — and the data shows this hits the entire food basket simultaneously, not just staples.

---

## Key Results

| Hypothesis / Model | Test | Result |
|---|---|---|
| H1 — 2016 devaluation raised food prices | Mann-Whitney U | **p = 0.000416 ✓ Supported** |
| H1 — 2022 devaluation raised food prices | Mann-Whitney U | **p = 0.008212 ✓ Supported** |
| H2 — Cairo prices exceed national average | Mann-Whitney U | **All 12 staples ✓ Supported** |
| H3 — Staples inflate faster than non-staples | One-Way ANOVA | **p = 0.3094 ✗ Not Supported** |
| Price prediction model | Linear Regression | **R² = 0.9962 ✓ Excellent fit** |

**Key numbers to know:**
- Undernourishment rate: 4.9% (2010) → **9.4% (2024)** — near doubling
- Severe food insecurity: ~7% (2019) → **13.4% (2024)**
- Cereal import dependency peaked at **51%** (2019–2020)
- Sunflower oil: 5 EGP (2010) → 23 EGP (post-2016) → **41 EGP (post-2022)**
- Sugar peaked at **~46 EGP** (2024)
- Wheat flour held at ~7 EGP until 2022, then rose to **~13 EGP** (subsidy effect)
- Staple mean monthly inflation: **2.41%** · Non-staple: **1.77%** (statistically indistinguishable)
- Most commodity Spearman correlations: **r > 0.90** (one dominant latent factor: the exchange rate)

---

## Project Structure

```
├── Egypt_Food_Crisis_Analysis_Fixed_3.ipynb   # Main analysis notebook
├── Egypt_Food_Crisis_Report_Fixed.docx        # Full written report
├── Egypt_Food_Crisis_Presentation_Fixed.pptx  # Slide deck (14 slides)
│
├── figures/
│   ├── fao_indicators_summary.png             # FAO 4-panel indicators (2002–2024)
│   ├── undernourishment_trend.png             # PoU rate with devaluation markers
│   ├── timeseries_static_improved.png         # Commodity prices 2010–2024
│   ├── timeseries_interactive.html            # Interactive version of above
│   ├── yearly_boxplots.png                    # Annual price distributions
│   ├── price_distributions.png               # Per-commodity histograms (10 items)
│   ├── h1_devaluation_impact.png              # H1: before/after box plots
│   ├── h2_market_comparison.png              # H2: Cairo vs national violin plots
│   ├── h3_inflation_boxenplot.png             # H3: staple vs non-staple ANOVA
│   ├── inflation_distribution.png             # MoM inflation histogram
│   ├── correlation_heatmap_improved.png       # Spearman correlation matrix (30 commodities)
│   └── regression_analysis.png               # Actual vs predicted + residuals
```

---

## Data Sources

| Dataset | Source | Coverage |
|---|---|---|
| WFP Food Price Data (`wfp_food_prices_egy`) | WFP VAM / Humanitarian Data Exchange | Monthly retail prices, 30+ commodities, Jan 2010 – mid 2024, 16,234 obs. |
| WFP Market Metadata (`wfp_markets_egy`) | WFP / Humanitarian Data Exchange | Governorate, market type, coordinates per monitored market |
| FAO Food Security Indicators | FAOSTAT | Annual series 2002–2024: PoU, PMSFI, PSFI, CIDR |

**Commodities covered:** wheat flour (subsidized + free-market), rice, lentils, fava beans, pasta, sugar, oils (cotton/maize/sunflower/mixed), eggs, cheese, ghee, milk, beef, chicken, fish, garlic, onions, tomatoes, potatoes, cucumbers.

---

## Methodology

### Preprocessing
- Standardized commodity names across years (WFP uses inconsistent capitalization)
- Computed **month-over-month (MoM) inflation** per commodity: `(price_t / price_{t-1} − 1) × 100`
- Classified commodities into **Staple** (wheat, rice, oils, pulses, pasta, sugar) and **Non-Staple** (meat, dairy, eggs, vegetables, fish)
- Assigned observations to three periods using exact event dates:
  - Pre-2016 / Post-2016–Pre-2022 / Post-2022
  - H1 tests use a **6-month before/after window** around each devaluation
- Forward-filled missing prices where gap ≤ 3 months (per WFP guidance)

### Hypothesis Tests

| Hypothesis | Test | Why |
|---|---|---|
| H1: Devaluation impact on prices | Mann-Whitney U | Shapiro-Wilk confirmed non-normality (p<0.001); rank-based test makes no distributional assumptions |
| H2: Cairo vs national prices | Mann-Whitney U | Same distributional rationale; violin plots show full empirical density |
| H3: Staple vs non-staple inflation | One-Way ANOVA | Comparing group *means* of a derived variable that approaches normality by CLT |

### Correlation
Spearman rank correlations across all 30+ commodity pairs (full 2010–2024 series). Pearson rejected due to non-normality and non-linearity of food prices.

### Regression Model
- **Features:** `price_lag_1`, `price_lag_2`, `price_lag_3`, `month`
- **Target:** commodity price
- **Split:** 80/20 train/test, random seed 42
- **Result:** R² = 0.9962, residuals unbiased; slight heteroscedasticity above 80 EGP

---

## Analysis Pipeline (Notebook Stages)

| Stage | Content |
|---|---|
| 1 | Data loading, cleaning, name standardization |
| 2 | FAO indicators — trends and devaluation event marking |
| 3 | WFP price time-series EDA |
| 4 | Annual box plots and per-commodity distributions |
| 5 | Hypothesis testing (H1, H2, H3) |
| 6 | Correlation analysis (Spearman heatmap) |
| 7 | Linear regression — training, evaluation, residual plots |

---

## How to Run

```bash
# 1. Install dependencies
pip install pandas numpy matplotlib seaborn scipy scikit-learn plotly

# 2. Place the raw data files in the project root:
#    - wfp_food_prices_egy.csv
#    - wfp_markets_egy.csv
#    - fao_indicators_summary_egy.csv

# 3. Open and run the notebook
jupyter notebook Egypt_Food_Crisis_Analysis_Fixed_3.ipynb
```

All figures are saved automatically to the `figures/` directory. The interactive time-series chart (`timeseries_interactive.html`) can be opened directly in any browser.

---

## Policy Conclusions

Because H3 was not supported — staple and non-staple inflation are statistically indistinguishable — the inflation pressure is systemic, not sectoral. This rules out commodity-specific fixes as the primary solution.

1. **FX stabilization** — the most upstream intervention; strategic reserves earmarked for food import financing compress exchange rate overshoot
2. **Import diversification** — reduce wheat dependency on Russia/Ukraine (>80% of imports in 2021); amplified the 2022 shock dramatically
3. **Targeted cash transfers** — expand the Egypt Food Subsidy Card and index transfer amounts to actual food basket inflation monthly; broader coverage than commodity subsidies
4. **Early warning system** — monitor 5–6 sentinel commodities; with r>0.90 across the basket, a small set reliably proxies the entire market

---

## Deliverables

| File | Description |
|---|---|
| `Egypt_Food_Crisis_Analysis_Fixed_3.ipynb` | Full reproducible analysis |
| `Egypt_Food_Crisis_Report_Fixed.docx` | Written report with all figures and methodology |
| `Egypt_Food_Crisis_Presentation_Fixed.pptx` | 14-slide presentation deck |

---

## References

- World Food Programme (2024). VAM Food Security Analysis — Egypt. [HDX](https://data.humdata.org/dataset/wfp-food-prices-for-egypt)
- FAO (2024). Suite of Food Security Indicators — Egypt. [FAOSTAT](https://www.fao.org/faostat/en/)
- Central Bank of Egypt (2016, 2022). Monetary Policy Committee Decisions.
- World Bank (2023). Egypt Economic Monitor: From Crisis to Economic Transformation.
- FAO, IFAD, UNICEF, WFP & WHO (2024). The State of Food Security and Nutrition in the World 2024.

---


