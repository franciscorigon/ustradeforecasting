# 6 RESULTS

        This section presents the outcomes of the evaluation phase applied to the 24 target series derived from bilateral U.S. trade flows with Canada, Mexico, and Brazil over the out-of-sample period spanning January 2022 to December 2024. Three forecasting algorithms — ARIMA, Random Forest (RF), and LightGBM — were systematically compared against a Naïve baseline using error metrics, statistical significance tests, and explainability tools, following the evaluation framework established in the methodology.

## 6.1 Descriptive statistics and data overview

        The final dataset comprised 180 monthly observations covering January 2010 through December 2024. The lower bound of 2010 was adopted to ensure a sufficient number of post-Global Financial Crisis observations for model training, while the upper bound corresponds to the most recent available trade flow data at the time of this study. The 24 target series were organized along three dimensions: country (Canada, Mexico, and Brazil), direction (exports and imports), and sector (commodities, manufactured goods, high-technology products, and aggregate total), yielding a structured evaluation design of 3 × 2 × 4 series.

        The 73 input features were constructed to be country-pair agnostic, encompassing nominal exchange rates (USD/CAD, USD/MXN, and USD/BRL), Real Effective Exchange Rate (REER) indices, macroeconomic control variables (Federal Funds Rate, WTI crude oil price, industrial production indices, Consumer Price Index, and GDP per capita), lagged values of the target variables at horizons of 1, 3, 6, and 12 months, rolling moving averages at 3-, 6-, and 12-month windows, percentage change transformations, and binary crisis indicators for the Global Financial Crisis (2008–2009) and the COVID-19 pandemic (2020–2021).

        The training period covered January 2010 through December 2021, corresponding to 132 monthly observations for the ML models. For ARIMA, an extended effective training window of 143 observations was used, as the walk-forward procedure progressively incorporates each realized month of the test period. The out-of-sample test set comprised 36 monthly observations from January 2022 to December 2024. Detailed exploratory analysis — including time series plots, correlation matrices, and distributional statistics — is provided in Appendix B.

## 6.2 Naïve baseline and information asymmetry

        Prior to the model comparison, a Naïve baseline was established by defining the one-step-ahead forecast as the log-transformed value of the immediately preceding observation, expressed as log(y_t) = log(y_{t-1}). Across all 24 series, the Naïve baseline achieved a mean MAPE of 0.32%, which was lower than all three forecasting models evaluated in this study.

        This result requires careful interpretation, as it reflects a fundamental informational asymmetry. At each step of the test period, the Naïve predictor accesses the actual realized value of the previous observation. The RF and LightGBM models, by contrast, were configured as direct 36-step-ahead forecasters: after training on data through December 2021, they generated all 36 monthly predictions simultaneously, without access to any intermediate realized values during the test window. ARIMA, while reestimated at each step through walk-forward validation, generates one-step-ahead forecasts at each iteration — a fundamentally different and structurally simpler prediction task than a full 36-month direct forecast horizon.

        This asymmetry does not invalidate the Naïve baseline as a reference, but it limits its comparability against the three models under evaluation. In operational and policy contexts, a 36-month direct forecast — as produced by the ML models — carries substantially greater practical value for trade strategy and macroeconomic planning than a rolling single-step projection requiring current-period data. Accordingly, the primary comparison of predictive performance is conducted among the three forecasting algorithms, with the Naïve baseline serving as an informational upper-bound reference.

## 6.3 Model performance comparison

        Table 1 reports the mean MAPE for each model, aggregated across all 24 target series. Random Forest achieved the lowest overall error (MAPE = 0.94%), followed by LightGBM (1.03%) and ARIMA (1.41%). In terms of series-level wins — defined as the number of series for which a model achieved the lowest MAPE among the three algorithms — RF outperformed both competitors in 9 of 24 series, ARIMA in 8, and LightGBM in 7.

**Table 1 — Mean MAPE by model across all 24 target series**

| Model          | Mean MAPE (%) | Series wins (of 24) |
|----------------|:-------------:|:-------------------:|
| Random Forest  | 0.9381        | 9                   |
| LightGBM       | 1.0276        | 7                   |
| ARIMA          | 1.4143        | 8                   |
| Naïve baseline | 0.3200        | —                   |

Source: the author (2026).

        Country-level disaggregation, presented in Table 2, reveals that the ML advantage was consistent across all three trading partners, though with varying magnitudes. For Canada, both RF (0.85%) and LightGBM (0.84%) substantially outperformed ARIMA (1.48%). For Mexico, RF (0.93%) maintained a clear margin over ARIMA (1.20%), while LightGBM (1.22%) performed comparably to the traditional model. For Brazil, RF (1.03%) and LightGBM (1.02%) both surpassed ARIMA (1.56%) by a meaningful margin, with the latter registering the highest country-level error across all models.

**Table 2 — Mean MAPE by country and model (%)**

| Country | ARIMA  | Random Forest | LightGBM |
|---------|:------:|:-------------:|:--------:|
| Canada  | 1.4781 | **0.8549**    | 0.8362   |
| Mexico  | 1.2042 | **0.9312**    | 1.2221   |
| Brazil  | 1.5606 | **1.0282**    | 1.0247   |

Source: the author (2026).

        Sectoral disaggregation, shown in Table 3, indicates that the performance differential between ARIMA and ML models was most pronounced for high-technology products (gap of 0.91 percentage points for RF) and manufactured goods (gap of 0.46 pp for RF), while commodity sectors exhibited the smallest divergence. This pattern is consistent with theoretical expectations regarding the relative volatility and non-linear dynamics of high-value manufacturing and technology trade flows.

**Table 3 — Mean MAPE by sector and model (%)**

| Sector             | ARIMA  | Random Forest | LightGBM |
|--------------------|:------:|:-------------:|:--------:|
| Commodities        | 1.0768 | **0.9848**    | 1.1187   |
| Manufactured goods | 1.3989 | **0.9437**    | 1.0559   |
| High-technology    | 1.8006 | **0.8904**    | 0.9404   |
| Total (aggregate)  | 1.3811 | **0.9334**    | 0.9956   |

Source: the author (2026).

## 6.4 Statistical significance tests

        **Friedman test.** A non-parametric Friedman test was applied to assess whether statistically significant differences in predictive accuracy existed across the three models simultaneously, treating each of the 24 series as a block. The test yielded a p-value of 0.417, indicating that the null hypothesis of no difference among models could not be rejected at conventional significance levels. This result is attributable in part to the limited number of series (n = 24), which constrains the statistical power of the Friedman test. The failure to detect significance through this test should not be interpreted as evidence of model equivalence; rather, it reflects the test's low discriminatory capacity under the present sample size.

        **Diebold-Mariano test.** To assess pairwise predictive superiority, the Diebold-Mariano test with the small-sample correction proposed by Harvey et al. (1997) was applied to each of the 24 series for the three model pairs. Results are summarized in Table 4. RF demonstrated superior predictive accuracy over ARIMA in 75% of series (18 of 24); LightGBM outperformed ARIMA in 83% of series (20 of 24); and RF outperformed LightGBM in 83% of series (20 of 24). These results establish a consistent performance hierarchy: RF > LightGBM > ARIMA, with the ML advantage over the traditional model being statistically supported in a substantial majority of the series evaluated.

**Table 4 — Pairwise Diebold-Mariano test results (Harvey et al., 1997 correction)**

| Comparison              | Series where first model is superior |
|-------------------------|:------------------------------------:|
| RF vs. ARIMA            | 75% (18 of 24)                       |
| LightGBM vs. ARIMA      | 83% (20 of 24)                       |
| RF vs. LightGBM         | 83% (20 of 24)                       |

Source: the author (2026).

        **Ljung-Box test.** To validate the residual structure of the ARIMA models, the Ljung-Box test was applied to in-sample residuals for each of the 24 series. Of these, 22 passed the test at the 5% significance level, indicating that their residuals exhibited no significant autocorrelation and that the fitted models were adequately specified. Two series registered significant residual autocorrelation: Canadian exports of commodities (p = 0.0006) and Canadian total imports (p = 0.0146). These cases are interpreted as potential ARIMA misspecification and may partially account for the model's comparatively weaker performance on these specific series.

## 6.5 ARIMA versus ML performance gap

        The overall performance gap between ARIMA and the ML models — measured as the absolute difference in mean MAPE — amounted to approximately 0.54 percentage points in favor of ML. This gap, however, was not uniform across all series: ARIMA achieved a lower MAPE than both ML models in 8 of 24 series, demonstrating that the linear autoregressive approach retains comparative advantages under specific conditions. A closer examination of the series in which ARIMA outperformed ML reveals a pattern associated with lower volatility and more predictable structures — notably, Mexican total exports (MAPE = 0.40%), Canadian exports of commodities (0.63%), and Brazilian high-technology series, where shorter lags and near-linear dynamics may favor parsimonious ARIMA specifications.

        Among the two series with Ljung-Box violations — Canadian exports of commodities and Canadian total imports — ARIMA registered errors that were markedly higher than both ML models, suggesting that residual misspecification was a material contributing factor in these cases. When these two problematic series are excluded, the average performance gap between ARIMA and RF narrows to approximately 0.47 percentage points, indicating that a properly specified ARIMA model remains a competitive baseline for well-behaved time series.

## 6.6 Feature importance and SHAP analysis

        Feature importance and SHAP (SHapley Additive exPlanations) analysis were conducted for both RF and LightGBM to identify the variables most influential in driving trade flow predictions. The SHAP summary plots for each model are presented in Figures 1 and 2, respectively, with a side-by-side comparison shown in Figure 3.

        For the Random Forest model, the WTI crude oil price index emerged as the most globally influential feature, reflecting the central role of energy markets in determining trade volumes, particularly for the commodity-intensive Canada–U.S. and Brazil–U.S. corridors. Exchange rate variables — most notably the USD/CAD percentage change (FX_USD_CAD_pct) — ranked among the top predictors, alongside lagged target values and six-month moving averages. The prominence of moving average features in the RF importance profile suggests that the model captures medium-term trend persistence as a key driver of trade dynamics.

        For the LightGBM model, the USD/CAD percentage change (FX_USD_CAD_pct) ranked third in global feature importance, reaffirming the centrality of the exchange rate signal. While the top-ranked features differed between the two models, a consistent pattern emerged: RF assigned comparatively greater weight to lagged levels and moving averages — features that capture accumulated trend information — while LightGBM placed proportionally higher weight on percentage change features, capturing short-term directional movements. Figure 3 illustrates this complementarity in the SHAP profiles of the two models.

        The presence of exchange rate variables among the top predictors of both RF and LightGBM provides empirical confirmation that exchange rate fluctuations exert a measurable and directionally consistent influence on bilateral trade flows, directly addressing the primary research question of this study (SONG; CHEN, 2024).

## 6.7 Exchange rate sensitivity by country and sector

        To quantify the relative sensitivity of each country-sector combination to exchange rate dynamics, a composite FX sensitivity metric was constructed as the mean absolute SHAP value of the exchange rate and REER features across all 24 series, averaged between the RF and LightGBM models. The resulting heatmap is presented in Figure 4.

        Brazil emerged as the most exchange-rate-sensitive trading partner, particularly in the commodities and total imports categories. This result is consistent with theoretical expectations regarding emerging economies: less diversified export structures, higher commodity dependence, and the absence of a comprehensive preferential trade agreement with the United States create greater exposure to bilateral exchange rate movements (GODA et al., 2024). Canada, in contrast, exhibited the lowest FX sensitivity across all sectors, a finding aligned with the stabilizing effect of deep productive integration under the USMCA framework and the structural complementarity of the U.S.–Canada trade relationship. Mexico occupied an intermediate position, reflecting partial insulation through USMCA alongside pronounced dependence on manufactured exports, which retain some — though not complete — sensitivity to bilateral exchange rate shifts.

        At the sectoral level, commodities exhibited the highest FX sensitivity across all three country pairs. Commodity prices are predominantly set in USD-denominated international markets, making their traded volumes directly exposed to bilateral exchange rate movements, regardless of domestic economic conditions (OLIVEIRA et al., 2023). High-technology products registered the lowest FX sensitivity, consistent with evidence that differentiated goods are typically subject to longer-term pricing contracts, global value chain structures, and relationship-specific investments that partially insulate short-run trade volumes from currency fluctuations (BERGIN; CORSETTI, 2020). Manufactured goods occupied an intermediate position, reflecting a combination of standardized products — more exposed to exchange rate arbitrage — and differentiated components traded within integrated supply chains.
