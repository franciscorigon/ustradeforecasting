# 6 RESULTS

        This section presents the outcomes of the evaluation phase applied to the 24 target series of bilateral U.S. trade flows with Canada, Mexico and Brazil over the out-of-sample period from January 2022 to December 2024. Three forecasting algorithms (ARIMA, Random Forest and LightGBM) were compared against a Naïve baseline using error metrics, statistical significance tests and explainability tools.

## 6.1 Descriptive statistics and data overview

        The final dataset contained 180 monthly observations from January 2010 to December 2024. The lower bound of 2010 ensured enough post-Global Financial Crisis observations for training, while the upper bound matches the most recent trade flow data. The 24 target series resulted from three countries (Canada, Mexico and Brazil), two directions (exports and imports) and four sectors (commodities, manufactured goods, high-technology and total), producing the 3 × 2 × 4 evaluation design.

        The 73 input features were country-pair agnostic. They included nominal exchange rates (USD/CAD, USD/MXN, USD/BRL), Real Effective Exchange Rate (REER) indices, macroeconomic variables (Federal Funds Rate, WTI crude oil price, industrial production, CPI, GDP per capita), lagged target values at 1, 3, 6 and 12 months, moving averages at 3, 6 and 12 months, percentage variations and binary crisis indicators for the Global Financial Crisis (2008–2009) and COVID-19 (2020–2021).

        The training period covered January 2010 to December 2021, corresponding to 132 monthly observations for the ML models. For ARIMA, the effective training window had 143 observations, as the walk-forward procedure progressively incorporates each realized month of the test period. The out-of-sample test set comprised 36 monthly observations from January 2022 to December 2024. Detailed exploratory analysis is provided in Appendix B.

## 6.2 Naïve baseline and information asymmetry

        Prior to the model comparison, a Naïve baseline was established as the one-step-ahead forecast equal to the previous observed value (log y_t = log y_{t-1}). Across the 24 series, the Naïve baseline achieved a mean MAPE of 0.32%, lower than all three forecasting models.

        This result reflects an informational asymmetry. At each step of the test period, the Naïve predictor accesses the realized value of the previous observation. The RF and LightGBM models, by contrast, were configured as direct 36-step-ahead forecasters: after training on data through December 2021, they generated all 36 predictions at once, without access to intermediate realized values. ARIMA, although reestimated through walk-forward validation, generates one-step-ahead forecasts, a structurally simpler task than the 36-month direct forecast horizon.

        The Naïve baseline remains a useful reference but is not directly comparable with the three models. In operational and policy contexts, the 36-month direct forecast produced by the ML models has greater practical value for trade strategy and macroeconomic planning than a rolling single-step projection that requires current-period data. The primary comparison is therefore conducted among the three forecasting algorithms, with the Naïve baseline serving as an informational upper-bound reference.

## 6.3 Model performance comparison

        Table 1 reports the mean MAPE for each model across the 24 target series. Random Forest achieved the lowest overall error (MAPE = 0.94%), followed by LightGBM (1.03%) and ARIMA (1.41%). At the series level, defined by lowest MAPE among the three algorithms, RF won in 9 of 24 series, ARIMA in 8 and LightGBM in 7.

**Table 1 — Mean MAPE by model across all 24 target series**

| Model          | Mean MAPE (%) | Series wins (of 24) |
|----------------|:-------------:|:-------------------:|
| Random Forest  | 0.9381        | 9                   |
| LightGBM       | 1.0276        | 7                   |
| ARIMA          | 1.4143        | 8                   |
| Naïve baseline | 0.3200        | —                   |

Source: the author (2026).

        Country-level disaggregation in Table 2 reveals that the ML advantage was consistent across the three trading partners, though with varying magnitudes. For Canada, both RF (0.85%) and LightGBM (0.84%) substantially outperformed ARIMA (1.48%). For Mexico, RF (0.93%) maintained a clear margin over ARIMA (1.20%), while LightGBM (1.22%) performed comparably. For Brazil, RF (1.03%) and LightGBM (1.02%) surpassed ARIMA (1.56%), which registered the highest country-level error.

**Table 2 — Mean MAPE by country and model (%)**

| Country | ARIMA  | Random Forest | LightGBM |
|---------|:------:|:-------------:|:--------:|
| Canada  | 1.4781 | **0.8549**    | 0.8362   |
| Mexico  | 1.2042 | **0.9312**    | 1.2221   |
| Brazil  | 1.5606 | **1.0282**    | 1.0247   |

Source: the author (2026).

        Sectoral disaggregation in Table 3 shows that the performance differential between ARIMA and ML was most pronounced for high-technology products (gap of 0.91 pp for RF) and manufactured goods (0.46 pp), while commodities had the smallest divergence. This pattern matches theoretical expectations on the volatility and non-linear dynamics of high-value manufacturing and technology trade.

**Table 3 — Mean MAPE by sector and model (%)**

| Sector             | ARIMA  | Random Forest | LightGBM |
|--------------------|:------:|:-------------:|:--------:|
| Commodities        | 1.0768 | **0.9848**    | 1.1187   |
| Manufactured goods | 1.3989 | **0.9437**    | 1.0559   |
| High-technology    | 1.8006 | **0.8904**    | 0.9404   |
| Total (aggregate)  | 1.3811 | **0.9334**    | 0.9956   |

Source: the author (2026).

        Figure 1 shows the same comparison visually, with the mean MAPE per algorithm by country and overall.

![MAPE comparison across models and countries](../results/figures/mape_comparison.png)

**Figure 1 — Mean MAPE by algorithm and country**

Source: the author (2026).

        Figure 2 complements the aggregate metrics with concrete forecast examples, in a 3 × 3 grid showing the best, median and worst forecast cases for each algorithm. The best case corresponds to the series with the lowest MAPE for that algorithm, the median to the middle of the ranking, and the worst to the highest MAPE. The grid makes visible the variability of model behavior across the 24 series.

![Best, median and worst forecast cases for each algorithm in the test window](../results/figures/evaluation/best_median_worst_forecasts.png)

**Figure 2 — Best, median and worst forecast cases per algorithm (test window: January 2022 to December 2024)**

Source: the author (2026).

## 6.4 Statistical significance tests

        **Friedman test.** A non-parametric Friedman test assessed whether statistically significant differences in predictive accuracy existed across the three models, treating each of the 24 series as a block. The test yielded a p-value of 0.417, so the null hypothesis of no difference among models could not be rejected. This reflects in part the limited number of series (n = 24), which constrains the test's statistical power; the result does not mean model equivalence.

        **Diebold-Mariano test.** The Diebold-Mariano test with the Harvey et al. (1997) small-sample correction was applied to each of the 24 series for the three model pairs (Table 4). RF was superior to ARIMA in 75% of series (18 of 24); LightGBM outperformed ARIMA in 83% (20 of 24); and RF outperformed LightGBM in 83% (20 of 24). These results establish a consistent hierarchy (RF > LightGBM > ARIMA), with the ML advantage statistically supported in the majority of the series.

**Table 4 — Pairwise Diebold-Mariano test results (Harvey et al., 1997 correction)**

| Comparison              | Series where first model is superior |
|-------------------------|:------------------------------------:|
| RF vs. ARIMA            | 75% (18 of 24)                       |
| LightGBM vs. ARIMA      | 83% (20 of 24)                       |
| RF vs. LightGBM         | 83% (20 of 24)                       |

Source: the author (2026).

        **Ljung-Box test.** The Ljung-Box test was applied to the in-sample residuals of the 24 ARIMA models. Of these, 22 passed at the 5% significance level, indicating no significant residual autocorrelation and adequate model specification. Two series showed significant autocorrelation: Canadian exports of commodities (p = 0.0006) and Canadian total imports (p = 0.0146). These cases suggest potential ARIMA misspecification, which may partially explain the model's weaker performance on these series. The ACF plots of representative ARIMA residuals are presented in Figure 6 (Appendix).

## 6.5 ARIMA versus ML performance gap

        The overall performance gap between ARIMA and the ML models was approximately 0.54 percentage points in favor of ML. The gap was not uniform: ARIMA had a lower MAPE than both ML models in 8 of 24 series, showing that the linear approach retains advantages under specific conditions. ARIMA's best cases show lower volatility and more predictable structure, notably Mexican total exports (MAPE = 0.40%), Canadian exports of commodities (0.63%) and Brazilian high-technology series.

        For the two series with Ljung-Box violations (Canadian exports of commodities and Canadian total imports), ARIMA registered errors markedly higher than both ML models, suggesting residual misspecification as a contributing factor. Excluding these two series, the average gap between ARIMA and RF narrows to 0.47 percentage points, indicating that a properly specified ARIMA remains a competitive baseline for well-behaved time series. The distribution of the gap across sectors is presented in Figure 7 (Appendix).

## 6.6 Feature importance and SHAP analysis

        Feature importance and SHAP (SHapley Additive exPlanations) analyses were conducted for RF and LightGBM to identify the variables driving trade flow predictions. Figure 3 reports the top-15 feature importance scores across the 24 models per algorithm, highlighting exchange rate and REER variables. The SHAP summary plots for both models are presented side by side in Figure 8 (Appendix), and the exchange rate dependence pattern in Figure 9 (Appendix).

![Top-15 feature importance for RF and LightGBM, with FX/REER highlighted](../results/figures/evaluation/feature_importance_top15.png)

**Figure 3 — Top-15 feature importance (FX and REER variables highlighted)**

Source: the author (2026).

        For Random Forest, the WTI crude oil price index emerged as the most influential feature, reflecting the central role of energy markets in trade volumes, particularly in the commodity-intensive Canada–U.S. and Brazil–U.S. corridors. Exchange rate variables, notably the USD/CAD percentage change (FX_USD_CAD_pct), ranked among the top predictors, alongside lagged target values and six-month moving averages. The prominence of moving averages suggests that RF captures medium-term trend persistence as a key driver (left panel of Figure 8, Appendix).

        For LightGBM, the USD/CAD percentage change (FX_USD_CAD_pct) ranked third in global feature importance, reaffirming the centrality of the exchange rate signal (right panel of Figure 8, Appendix).

        Although the top-ranked features differed between the two models, a consistent pattern emerged: RF assigned greater weight to lagged levels and moving averages (which capture accumulated trend information), while LightGBM placed higher weight on percentage change features (capturing short-term directional movements). Figure 4 illustrates this complementarity, comparing mean absolute SHAP values side by side with FX and REER features highlighted in red.

![Side-by-side comparison of mean absolute SHAP values for Random Forest and LightGBM, top-15 features each, with FX and REER variables highlighted](../results/figures/evaluation/shap_comparison_rf_lgbm.png)

**Figure 4 — Comparative SHAP feature importance: Random Forest versus LightGBM (top-15 features each, FX/REER highlighted)**

Source: the author (2026).

        The directional response of trade flows to exchange rate variation is examined through SHAP dependence plots. Figure 9 (Appendix) shows the LightGBM SHAP contribution of the bilateral exchange rate for total exports and total imports of each country pair, evidencing the asymmetric currency transmission already discussed in the theoretical framework.

        The presence of exchange rate variables among the top predictors of both models confirms that exchange rate fluctuations exert a measurable and directionally consistent influence on bilateral trade flows, answering the primary research question.

## 6.7 Exchange rate sensitivity by country and sector

        To quantify the sensitivity of each country-sector combination to exchange rate dynamics, a composite FX sensitivity metric was built as the mean absolute SHAP value of FX and REER features across the 24 series, averaged between RF and LightGBM. The resulting heatmap is shown in Figure 5.

![Heatmap of exchange rate sensitivity by country and sector, averaged between RF and LightGBM SHAP values](../results/figures/evaluation/fx_sensitivity_heatmap.png)

**Figure 5 — Exchange rate sensitivity heatmap (mean |SHAP| of FX and REER features, averaged between Random Forest and LightGBM)**

Source: the author (2026).

        Brazil emerged as the most exchange-rate-sensitive trading partner, particularly in commodities and total imports. This matches theoretical expectations for emerging economies: less diversified export structures, higher commodity dependence and the absence of a preferential trade agreement with the United States produce greater exposure to bilateral exchange rate movements (GODA et al., 2024). Canada exhibited the lowest FX sensitivity across all sectors, consistent with the stabilizing effect of deep productive integration under USMCA. Mexico occupied an intermediate position, reflecting partial insulation through USMCA alongside dependence on manufactured exports, which retain some sensitivity to bilateral exchange rate shifts.

        At the sectoral level, commodities had the highest FX sensitivity across the three country pairs, since their prices are set in USD-denominated international markets, exposing traded volumes directly to bilateral exchange rate movements regardless of domestic conditions (OLIVEIRA et al., 2023). High-technology products had the lowest sensitivity, consistent with evidence that differentiated goods follow longer-term pricing contracts, global value chains and relationship-specific investments that partially insulate them from currency fluctuations (BERGIN; CORSETTI, 2020). Manufactured goods occupied an intermediate position, combining standardized products (more exposed to arbitrage) and differentiated components in integrated supply chains.

## 6.8 Synthesis of findings

        Taken together, the results provide a direct empirical answer to the research question. First, exchange rate variables appeared among the most relevant predictors in both machine learning models (Figures 3 and 4), confirming that exchange rate fluctuations have measurable predictive content for bilateral trade flows. Second, Random Forest was the most accurate model (mean MAPE 0.94%), and the Diebold-Mariano test confirmed its superiority over ARIMA in 75% of the series and over LightGBM in 83% (Table 4), establishing the hierarchy RF > LightGBM > ARIMA. Third, exchange rate sensitivity varied systematically across countries and sectors (Figure 5): Brazil was the most sensitive partner and Canada the least, while commodities responded more strongly than high-technology products.
