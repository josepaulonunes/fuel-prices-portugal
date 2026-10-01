# Fuel Prices in Portugal vs Brent Crude

How do Portuguese pump prices for gasoline and diesel respond to oil prices, and do they rise faster than they fall ("rockets and feathers")?
If they do, is it the petrol stations or the refining and wholesale market? How do Portuguese prices compare with Spain and the EU, and can next Monday's price change be predicted?

**[Open the interactive dashboard on Tableau Public](https://public.tableau.com/app/profile/jos.nunes7914/viz/FuelpricesinPortugalvsBrent/Fuelpricesdashboard)**

![Dashboard](figures/tableau_dashboard.png)

## Key findings
- **Diesel rises faster than it falls against crude oil:** in the long run, a 1 cent rise in Brent raises the pre-tax diesel price by about 1.34 cents, while a 1 cent fall lowers it by only 0.80 cents (p = 0.047).
- **But not against the wholesale reference price:** using the ENSE reference price (based on international quotes for refined products), diesel rises and falls are passed on almost equally (0.90 vs 0.85, p = 0.37).
- **So the diesel asymmetry comes from refining and wholesale markets, not from petrol stations.**
- **Gasoline shows no asymmetry** against either cost measure.
- Retail margins (pump price minus reference price) average about 20 cents per litre for gasoline and 17 cents for diesel. The diesel margin was squeezed close to zero during the spring 2022 price spike, and the gasoline margin stayed clearly above the diesel margin in 2025 and 2026.
- On average since 2019, taxes make up 49% of the price of a litre of diesel.
- **Portugal vs Spain:** fuel in Portugal costs on average 17.6 cents per litre more than in Spain for gasoline and 12.4 cents more for diesel, but the whole gap comes from taxes. Before taxes, Portuguese prices are 2 to 3 cents per litre lower than Spanish prices.
- **Monday forecast:** last week's change in the ENSE reference price predicts Monday's pump price change with an average error of about 1 cent per litre (0.88 for gasoline, 1.06 for diesel) on 2025 and 2026 data not used to estimate the model. This is less than half the error of a "no change" forecast, and the direction (up or down) is right in 86% of weeks for gasoline and 90% for diesel.

## Pump prices vs Brent
![Pump prices vs Brent](figures/pump_prices_vs_brent.png)

## What makes up the price of a litre
![Diesel price decomposition](figures/diesel_price_decomposition.png)

## Retail margins
![Retail margins](figures/retail_margins.png)

## Rockets and feathers
![Rockets and feathers comparison](figures/rockets_feathers_comparison.png)

## Portugal vs Spain vs EU
![Portugal, Spain and EU prices](figures/portugal_spain_eu_prices.png)

## Forecasting Monday's pump price change
![Monday forecast, diesel](figures/monday_forecast_diesel.png)

The model is estimated on 2019 to 2024 and tested on 2025 and 2026:

| Fuel | Test weeks | Average error, model (cents) | Average error, "no change" (cents) | Right direction |
|---|---|---|---|---|
| Gasoline | 91 | 0.88 | 1.96 | 85.7% |
| Diesel | 91 | 1.06 | 3.02 | 90.1% |

The model follows normal weeks closely but underestimates the largest shocks, such as the diesel increase in March 2026.

![Portugal minus Spain, price without taxes](figures/portugal_vs_spain_pretax_gap.png)

## Data sources
- **Brent crude oil price** (USD per barrel): FRED, series DCOILBRENTEU
- **EUR/USD exchange rate**: FRED, series DEXUSEU
- **Portuguese pump prices with and without taxes**: European Commission, Weekly Oil Bulletin
- **Reference prices for gasoline and diesel** (daily, with taxes, without retail costs and margin): ENSE, Entidade Nacional para o Setor Energético

## Method
## Method
1. Brent converted to euros per litre and averaged by week, from January 2019 to September 2026.
2. Pump prices matched to the previous week's costs, since Portuguese prices adjust weekly based on last week's quotes.
3. Price split into oil cost, margin and taxes.
4. Asymmetric distributed lag model, estimated by OLS with HAC (Newey-West) standard errors with 4 lags.
5. The model is estimated twice: against Brent (pump prices without taxes) and against the ENSE reference price (both with taxes).

Notebook 02 also includes a simpler first version of the model, with two weeks of adjustment and no momentum term.

## Econometric specification

Weekly change in the pump price explained by rises and falls in the cost measure, this week and in the previous three weeks, plus last week's price change:

$$\Delta p_t = \alpha + \sum_{k=0}^{3} \beta_k^{+} \Delta c_{t-k}^{+} + \sum_{k=0}^{3} \beta_k^{-} \Delta c_{t-k}^{-} + \rho \, \Delta p_{t-1} + \varepsilon_t$$

where $\Delta c^{+}$ keeps only cost increases and $\Delta c^{-}$ only cost decreases. Long run pass-through:

$$LR^{+} = \frac{\sum_{k} \beta_k^{+}}{1-\rho} \qquad LR^{-} = \frac{\sum_{k} \beta_k^{-}}{1-\rho}$$

Symmetry is tested with an F-test of $H_0: \sum_k \beta_k^{+} = \sum_k \beta_k^{-}$.

## Econometric results

| Cost measure | Fuel | LR rises | LR falls | F statistic | p-value | R² | N |
|---|---|---|---|---|---|---|---|
| Brent | Gasoline | 0.88 | 0.84 | 0.03 | 0.863 | 0.53 | 391 |
| Brent | Diesel | 1.34 | 0.80 | 3.96 | **0.047** | 0.60 | 391 |
| ENSE reference | Gasoline | 0.78 | 0.78 | 0.00 | 0.999 | 0.71 | 391 |
| ENSE reference | Diesel | 0.90 | 0.85 | 0.81 | 0.368 | 0.83 | 391 |

- Against Brent, symmetry is rejected for diesel at the 5% level, but not for gasoline.
- Against the ENSE reference price, symmetry is not rejected for either fuel.
- The ENSE reference price explains a larger share of weekly pump price changes (R² of 0.71 and 0.83 against 0.53 and 0.60), which supports it as the more relevant cost measure.
- The momentum term is positive against Brent (prices keep adjusting the following week) and negative against the ENSE reference price (part of last week's change is corrected).

<details>
<summary><b>Full regression tables</b> (coefficients with HAC standard errors in parentheses)</summary>

Significance: *** p < 0.01, ** p < 0.05, * p < 0.1

**Against Brent** (dependent variable: weekly change in the pump price without taxes)

| Variable | Gasoline | Diesel |
|---|---|---|
| Cost rise, week t | 0.770*** (0.113) | 1.176*** (0.161) |
| Cost rise, week t-1 | 0.001 (0.116) | 0.058 (0.172) |
| Cost rise, week t-2 | -0.142 (0.111) | -0.232** (0.108) |
| Cost rise, week t-3 | 0.113 (0.114) | 0.105 (0.136) |
| Cost fall, week t | 0.665*** (0.091) | 0.815*** (0.110) |
| Cost fall, week t-1 | 0.100 (0.111) | -0.067 (0.115) |
| Cost fall, week t-2 | -0.004 (0.083) | 0.027 (0.102) |
| Cost fall, week t-3 | -0.053 (0.094) | -0.114 (0.101) |
| Price change, week t-1 | 0.159** (0.074) | 0.176** (0.089) |
| Constant | 0.000 (0.001) | -0.003* (0.001) |
| Observations | 391 | 391 |
| R² | 0.53 | 0.60 |

**Against the ENSE reference price** (dependent variable: weekly change in the pump price with taxes)

| Variable | Gasoline | Diesel |
|---|---|---|
| Cost rise, week t | 0.733*** (0.062) | 0.892*** (0.071) |
| Cost rise, week t-1 | 0.185** (0.074) | 0.263*** (0.081) |
| Cost rise, week t-2 | 0.072 (0.066) | 0.102** (0.044) |
| Cost rise, week t-3 | -0.028 (0.037) | -0.098** (0.038) |
| Cost fall, week t | 0.674*** (0.043) | 0.674*** (0.053) |
| Cost fall, week t-1 | 0.144* (0.076) | 0.273*** (0.075) |
| Cost fall, week t-2 | 0.079* (0.043) | -0.004 (0.058) |
| Cost fall, week t-3 | 0.065 (0.050) | 0.153** (0.065) |
| Price change, week t-1 | -0.237*** (0.072) | -0.288*** (0.066) |
| Constant | 0.001 (0.001) | 0.000 (0.001) |
| Observations | 391 | 391 |
| R² | 0.71 | 0.83 |

</details>

Notebook 02 also includes a simpler first version of the model, with two weeks of adjustment and no momentum term.

## Limitations
- Brent does not include refining margins, so results against Brent mix retail behaviour with the refining market. The ENSE test addresses this.
- The ENSE reference price is a benchmark based on international quotes and standard costs, not the actual purchase cost of each company.
- The retail margin includes distribution, station costs and VAT on them, so it is not the same as profit.
- National weekly averages hide differences between stations and brands.
- A few weeks are missing in the Oil Bulletin data, so a small number of price changes cover two or three weeks.
- The forecast uses the latest published ENSE reference prices, which can be revised after publication, so real time accuracy may be slightly lower.

## Project structure
    data/raw/         original data, never edited by hand
    data/processed/   cleaned data and results produced by the notebooks
    notebooks/        analysis, run in order (01 TO 05)
    figures/          charts used in this README

## How to run
    python -m venv .venv
    .venv\Scripts\activate
    pip install -r requirements.txt

Then run the notebooks in order: 01_brent.ipynb, 02_fuel_prices.ipynb, 03_ense_reference.ipynb, 04_portugal_vs_eu.ipynb, 05_monday_forecast.ipynb.

## References
- Bacon, R. W. (1991). Rockets and feathers: the asymmetric speed of adjustment of UK retail gasoline prices to cost changes. *Energy Economics*, 13(3), 211 to 218.
- Borenstein, S., Cameron, A. C. and Gilbert, R. (1997). Do gasoline prices respond asymmetrically to crude oil price changes? *Quarterly Journal of Economics*, 112(1), 305 to 339.
- Meyer, J. and von Cramon-Taubadel, S. (2004). Asymmetric price transmission: a survey. *Journal of Agricultural Economics*, 55(3), 581 to 611.
- Newey, W. K. and West, K. D. (1987). A simple, positive semi-definite, heteroskedasticity and autocorrelation consistent covariance matrix. *Econometrica*, 55(3), 703 to 708.

**Data**
- FRED, Federal Reserve Bank of St. Louis: [DCOILBRENTEU](https://fred.stlouisfed.org/series/DCOILBRENTEU) and [DEXUSEU](https://fred.stlouisfed.org/series/DEXUSEU)
- European Commission, [Weekly Oil Bulletin](https://energy.ec.europa.eu/data-and-analysis/weekly-oil-bulletin_en)
- ENSE, [Preços de referência](https://www.ense-epe.pt/precos-de-referencia/)